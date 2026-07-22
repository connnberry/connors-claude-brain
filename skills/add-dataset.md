---
name: add-dataset
description: Add a real customer dataset to the Base Surfaces mobile prototype from a Wise CSV export
argument-hint: [person's first name, e.g. "sarah"]
---

Add a real customer's Wise account data to the mobile prototype. This creates all data files, wires hooks, registers routes, and ensures every surface (Home, Cards, Account pages) renders correctly.

**Scope:** This skill targets `base-surfaces-mobile` only. The web prototype (`base-surfaces-web`) does not have dataset switching — it uses the static power/default dataset. Data files go in `shared-resources/data/` (shared), but all wiring is mobile-specific.

## Before You Start — Gather Information

**Ask the user for ALL of the following before writing any code:**

1. **CSV data** — "Please share the CSV export for this person. If you need a fresh pull, your analyst can run the Snowflake query (see below) with their user_id."
2. **Person's name** — "What display name should this person have in the prototype?" (e.g. "Sarah Chen")
3. **Business name** — "What's the business name?" (if they have a business account — usually visible in the CSV `business_name` column)
4. **Profile picture** — "Do you have a profile picture/avatar for this person? Please share the file or a URL." Save it as `mobile/public/avatar-{firstname}.png` (or `.jpeg`).
5. **Group/Joint cards** — The CSV gives us card count and type per profile (physical+digital, digital only, or neither), but NOT whether group/team or joint/shared accounts have their own cards. Ask: "Do any of the group or joint accounts have their own cards? If so, how many and what type?" (If the user isn't sure, default to: joint gets 1 digital card, groups get 0.)
6. **Dataset slug** — Confirm the slug will be `{firstname}-personal` and `{firstname}-business` (lowercase, e.g. `sarah-personal`, `sarah-business`)

**Do NOT proceed until you have the CSV, name, and card info.** The profile picture can come later but flag it as needed.

---

## How to Get Fresh Data (Snowflake Query)

If the user needs a fresh export, their analyst can run this query in Snowflake. Replace the `user_ids` list with the target user IDs:

```python
user_ids = ['662338', '14845875', '5419693']  # Add target user_id(s)
user_ids_string = ', '.join(f"'{id}'" for id in user_ids)

query = f'''
with deposit_accounts as (
    select b.ID, da.ACTIVE
    from DEPOSITACCOUNT.BALANCE b
    join DEPOSITACCOUNT.DEPOSIT_ACCOUNT da on b.DEPOSIT_ACCOUNT_ID = da.ID
        and da.SETTLEMENT_TYPE = 'BALANCE' and da.ACTIVE = true
),
latest_balances as (
    select profile_id, balance_id, balance_group_id, amount_currency, total_wise_balance_gbp
    from RPT_PRODUCT.REPORT_BORDERLESS_HISTORIC_BALANCES_PROFILES
    where date_balance = (select max(date_balance) from RPT_PRODUCT.REPORT_BORDERLESS_HISTORIC_BALANCES_PROFILES)
)
select pc.USER_ID as user_id,
       pc.PROFILE_ID as profile_id,
       pc.PROFILE_TYPE as profile_type,
       NULL as full_name,
       NULL as email,
       case when pc.BUSINESS_NAME is not null then pc.BUSINESS_NAME else 'Personal profile' end as business_name,
       max(fx.LANGUAGE) as language,
       lb.total_wise_balance_gbp as amount_value,
       lb.balance_id,
       lb.amount_currency as currency,
       g.GROUP_ID as group_id,
       g.PRODUCT_NAME as product_name,
       g.NAME as group_name,
       case when b.INVESTMENT_STATE = 'INVESTED' then true else false end as has_assets,
       case when da.ACTIVE = true then true else false end as has_ads,
       case when max(c.ACTIVE_CARD_COUNT) is not null then max(c.ACTIVE_CARD_COUNT) else 0 end as cards,
       case when min(c.ACTIVE_FLAG_CARD_HOLDER_TYPE) is not null then min(c.ACTIVE_FLAG_CARD_HOLDER_TYPE) else 'neither' end as card_type,
       row_number() over (partition by pc.PROFILE_ID order by lb.total_wise_balance_gbp desc) as rank
from RPT_CORE_ANALYTICS.PROFILE_CORE pc
left join FX.USER fx on pc.USER_ID = fx.ID
left join latest_balances lb on pc.PROFILE_ID = lb.profile_id
left join BALANCE.BALANCE b on lb.balance_id = b.ID
left join BALANCE."GROUP" g on lb.balance_group_id = g.GROUP_ID
left join deposit_accounts da on lb.balance_id = da.ID
left join RPT_PRODUCT.REPORT_PROFILE_CARD_ISSUANCE_SUMMARY_TEMP c on pc.PROFILE_ID = c.PROFILE_ID
where pc.USER_ID in ({user_ids_string})
group by pc.USER_ID, pc.PROFILE_ID, pc.PROFILE_TYPE, pc.BUSINESS_NAME,
         lb.total_wise_balance_gbp, lb.balance_id, lb.amount_currency,
         b.INVESTMENT_STATE, da.ACTIVE, g.GROUP_ID, g.PRODUCT_NAME, g.NAME
order by 1, 3, 6, 10, 12, 14;
'''
```

The query pulls from `RPT_PRODUCT.REPORT_BORDERLESS_HISTORIC_BALANCES_PROFILES` (latest date), joined with deposit account status (`has_ads`) and investment state (`has_assets`). Card info comes from `RPT_PRODUCT.REPORT_PROFILE_CARD_ISSUANCE_SUMMARY_TEMP`.

---

## CSV Fields Reference

The CSV has one row per currency per balance group. Key columns:

| Field | Purpose | Example |
|-------|---------|---------|
| `user_id` | Groups profiles for the same human | `662338` |
| `profile_id` | Identifies a single profile (personal or business) | `453923` |
| `profile_type` | `personal` or `business` | `personal` |
| `business_name` | Business name or "Personal profile" | `Berry Design` |
| `amount_value` | Current balance in the currency (this is `total_wise_balance_gbp` from the query but represents the actual amount) | `3009.34` |
| `balance_id` | Unique ID per currency balance (used for URL routing) | `177.0` (strip the `.0`) |
| `currency` | ISO code | `GBP` |
| `group_id` | Account group ID (becomes jar/group ID in prototype) | `52546803.0` (strip `.0`) |
| `product_name` | `MAIN`, `SAVINGS`, `SHARED`, `TEAM` | `MAIN` |
| `group_name` | Display name for jars/groups (empty for MAIN) | `Fantasy Football` |
| `has_assets` | Whether interest/stocks is enabled | `True` / `False` |
| `has_ads` | Whether account details exist (IBAN, sort code) | `True` / `False` |
| `cards` | Number of active cards on this profile | `2` |
| `card_type` | `digital only`, `digital and physical`, or `neither` | `digital and physical` |
| `rank` | Balance ranking within profile (by GBP value desc) | `1` |

**Important parsing notes:**
- `balance_id` and `group_id` come as floats (e.g. `177.0`) — strip the `.0` to get the string ID
- A row with `product_name` empty and `cards=0, card_type=neither` with no currencies is a business profile with no balances — render as empty state
- Multiple rows with the same `group_id` = multiple currencies in one account
- `cards` and `card_type` are per-profile (same value on every row for that profile)

---

## Filtering Rules

1. **Show a MAIN currency if:** `has_ads=True` OR `amount > 0`
2. **Show a jar/group currency if:** it exists in the CSV (even 0 balance — empty jars are valid)
3. **Account details:** only add `accountDetails` field if `has_ads=True`
4. **Interest/stocks:** only add `hasInterest: true` if `has_assets=True`
5. **Jars never have account details** — even if `has_ads=True` on a SAVINGS row, jars don't show the Account Details button

---

## Account Detail Formats

Generate unique, realistic-looking numbers per user. Never reuse across datasets.

| Currency | Format | Example |
|----------|--------|---------|
| GBP | `'XX-XX-XX · XXXXXXXX'` | `'23-14-70 · 62048193'` |
| EUR | IBAN format | `'BE68 5390 0754 7034'` |
| USD | `'XXXXXXXXX · XXXXXXXXX'` | `'021000021 · 729401835'` |
| SGD | `'XXXX · XXX-XXX-XXX'` | `'7171 · 683-421-095'` |
| ILS | `'XX-XXX-XXXXXXX'` | `'12-480-7291035'` |
| AUD | `'BSB XXX-XXX · XXXXXXXX'` | `'BSB 062-000 · 12345678'` |

---

## Card Rules

### Physical vs Digital
- **Personal physical:** uses `/wise-card-physical.png` — LIGHT GREEN
- **Business physical:** uses `/wise-card-biz-physical.png` — DARK GREEN
- **Digital cards:** use tapestry background images

### Physical card MUST always be first in the array

### Available digital tapestry images
Personal (use for personal accounts):
- `/wise-card-personal-digital-turquoise.png`
- `/wise-card-personal-digital-green.png`

Business (use for business accounts):
- `/wise-card-biz-digital-aqua.png`
- `/wise-card-biz-digital-orange.png`
- `/wise-card-biz-digital-yellow.png`

### Key rules:
- Every digital card MUST use a **different** tapestry image — no duplicates within the same account
- If a personal account has more than 2 digital cards, you can use business tapestries (they're just colour patterns)
- If a business account has 0 cards, render the empty card state (cards array = `[]`)
- Cards on group/team accounts are SEPARATE from main account cards and get their own array
- Cards on joint/shared accounts are SEPARATE from main account cards and get their own array

### Card arrays to define in `useDataset.ts`:
```typescript
const {name}PersonalCards: CardInfo[] = [
  { type: 'physical', lastFour: 'XXXX', image: '/wise-card-physical.png' },
  { type: 'digital', lastFour: 'XXXX', image: '/wise-card-personal-digital-turquoise.png' },
];
const {name}BusinessCards: CardInfo[] = [...];
// If applicable:
const {name}JointCards: CardInfo[] = [...];
const {name}BusinessGroupCards: CardInfo[] = [...];
```

### MCA card stack rendering:
- 1 card = single card shown
- 2 cards = front + behind (peeking out at top)
- 3+ cards = front + middle + back (tighter overlap, same total height)
- Front card: physical shows green/dark-green background; digital shows tapestry image
- Behind/back cards: always show digital tapestry images
- `cardInfoLight` (white text) = true ONLY when front card is digital (tapestry background); false for physical (light green = dark text)
- `businessCardStyle` (dark green gradient) = true when business has physical card

---

## Jar Colour Rules

Jars use the **Wise brand bright colours** only. These are the ONLY allowed jar colours:

| Colour name | Hex | Use for |
|-------------|-----|---------|
| bright-green | `#9FE870` | General savings, investments |
| bright-yellow | `#FFEB69` | Stocks, financial jars |
| bright-blue | `#A0E1E1` | Rainy day, travel, spare change |
| bright-pink | `#FFD7EF` | Tax, bills |
| bright-orange | `#FFC091` | Holiday, fun jars |

**Never use custom hex colours for jars.** Pick from the 5 above. If a person has more than 5 jars, reuse colours.

---

## File Structure

All data files go in `shared-resources/data/`. Use this exact naming:

### Required files (per account type):

| File | Exports |
|------|---------|
| `{name}-personal-currencies.ts` | `{name}PersonalCurrencies: CurrencyData[]` |
| `{name}-personal-transactions.tsx` | `build{Name}PersonalTransactions(consumer, business, labels): Transaction[]` |
| `{name}-business-currencies.ts` | `{name}BusinessCurrencies: CurrencyData[]` |
| `{name}-business-transactions.tsx` | `build{Name}BusinessTransactions(consumer, labels): Transaction[]` |

### Optional files (if account has jars/joint/group):

| File | When | Exports |
|------|------|---------|
| `{name}-personal-jars.tsx` | Personal has SAVINGS accounts | `{name}PersonalJars: JarDefinition[]` |
| `{name}-business-jars.tsx` | Business has SAVINGS accounts | Individual jar exports or array |
| `{name}-personal-joint.tsx` | Personal has SHARED account | `{name}JointCurrencies`, `{name}JointTransactions` |
| `{name}-business-group.tsx` | Business has TEAM account | `{name}BusinessGroupCurrencies`, `{name}BusinessGroupTransactions` |

---

## File Templates

### Currency file (`{name}-personal-currencies.ts`)

```typescript
import type { CurrencyData } from './currencies';
import { build{Name}PersonalTransactions } from './{name}-personal-transactions';
import { computeCurrencyBalance } from './transactions';

const transactions = build{Name}PersonalTransactions('{DisplayName}', '{BusinessName}');

export const {name}PersonalCurrencies: CurrencyData[] = [
  {
    code: 'GBP',
    balanceId: '{from CSV}',
    name: 'British pound',
    symbol: '£',
    balance: computeCurrencyBalance('GBP', transactions),
    accountDetails: '23-XX-XX · XXXXXXXX',  // only if has_ads=True
    hasInterest: true,  // only if has_assets=True
    interestRate: '3.26%',
    totalReturns: '+X.XX GBP',
  },
  // ... more currencies
];
```

### Transaction file (`{name}-personal-transactions.tsx`)

```typescript
import { Receive, Send, Plus } from '@transferwise/icons';
import type { Transaction, TxTranslator } from './transactions';
import { logoUrl } from './transactions';

const defaultLabels: TxTranslator = {
  sent: 'Sent',
  sentByYou: 'Sent by you',
  added: 'Added',
  addedByYou: 'Added by you',
  moved: 'Moved',
  movedByYou: 'Moved by you',
  spentByYou: 'Spent by you',
};

export function build{Name}PersonalTransactions(_consumerName: string, _businessName: string, labels: TxTranslator = defaultLabels): Transaction[] {
  return [
    // First tx per currency MUST be a large positive to establish starting balance
    { name: 'Salary', amount: '+ 320.13 GBP', isPositive: true, icon: <Receive size={24} />, date: '9 May', currency: 'GBP' },
    // Subsequent debits/credits
    { name: 'Pret A Manger', amount: '4.87 GBP', isPositive: false, imgSrc: logoUrl('pret.com'), date: 'Today', currency: 'GBP' },
    // ...
  ];
}
```

**CRITICAL: The CSV only gives us final balances — NOT real transaction history.** You must INVENT realistic-looking transactions that net to EXACTLY the CSV balance for each currency.

How `computeCurrencyBalance` works: it sums all `isPositive: true` amounts and subtracts all `isPositive: false` amounts for a given currency code. So your invented transactions must satisfy:
`sum(positive amounts) - sum(negative amounts) = CSV balance`

**Transaction design guidelines:**
- Start with a large deposit to establish the bulk of the balance (salary, transfer received, payout)
- Add 3-6 realistic debits (merchants, subscriptions, transfers) spread across recent dates
- Use real merchant domains for logos: `logoUrl('pret.com')`, `logoUrl('deliveroo.co.uk')`, `logoUrl('spotify.com')`
- For business: use B2B names (invoices, payroll, suppliers)
- Dates should be recent and spread across the last 2 weeks (Today, Yesterday, specific dates)
- For jar transactions: use "From GBP" / "Moved by you" pattern (internal moves)
- For zero-balance currencies: a single add + matching spend, or just no transactions
- Verify your math: positive amounts minus negative amounts MUST equal the CSV `amount_value`

### Jar file (`{name}-personal-jars.tsx`)

```typescript
import { Plus } from '@transferwise/icons';
import type { Transaction } from './transactions';
import { computeCurrencyBalance } from './transactions';
import type { JarDefinition } from './jar-data';

const savingsTx: Transaction[] = [
  { name: 'From GBP', subtitle: 'Moved by you', amount: '+ 1,000.00 GBP', isPositive: true, icon: <Plus size={24} />, date: '8 May', currency: 'GBP' },
];

export const {name}PersonalJars: JarDefinition[] = [
  {
    id: '{group_id from CSV}',
    nameKey: 'home.savings',
    color: '#FFEB69',  // MUST be a brand bright colour
    iconName: 'Savings',  // 'Savings' | 'UpwardGraph' | 'Suitcase'
    currencies: [{ code: 'GBP', balanceId: '{from CSV}', name: 'British pound', symbol: '£', balance: computeCurrencyBalance('GBP', savingsTx), hasInterest: true, interestRate: '3.26%', totalReturns: '+X.XX GBP' }],
    transactions: savingsTx,
  },
];
```

### Joint file (`{name}-personal-joint.tsx`)

```typescript
import { Plus, Receive } from '@transferwise/icons';
import type { CurrencyData } from './currencies';
import type { Transaction } from './transactions';
import { computeCurrencyBalance } from './transactions';

export const {name}JointTransactions: Transaction[] = [
  { name: 'From GBP', subtitle: 'Moved by you', amount: '+ 12.00 GBP', isPositive: true, icon: <Plus size={24} />, date: '8 May', currency: 'GBP' },
];

export const {name}JointCurrencies: CurrencyData[] = [
  {
    code: 'GBP',
    balanceId: '{from CSV}',
    name: 'British pound',
    symbol: '£',
    balance: computeCurrencyBalance('GBP', {name}JointTransactions),
  },
];
```

### Business group file (`{name}-business-group.tsx`)

Same pattern as joint but for TEAM accounts.

---

## Wiring Checklist (in order)

After creating all data files, update these files:

### 1. `shared-resources/data/jar-data.tsx`

- Add all jar/group/joint IDs to `GROUP_IDS`:
  ```typescript
  // {Name} Personal
  {name}Savings: '{group_id}',
  {name}Jar2: '{group_id}',
  // {Name} Business
  {name}BusinessExpenses: '{group_id}',
  ```
- Import jar exports and add to `allUserJars` array
- Verify `getJar()` will find them via the `allUserJars.find()` fallback

### 2. `mobile/src/hooks/useDataset.ts`

This is the **central data routing file**. Every hook needs a new `case`:

- **Card arrays** — define at the top of the file:
  ```typescript
  const {name}PersonalCards: CardInfo[] = [...];
  const {name}BusinessCards: CardInfo[] = [...];
  // optional:
  const {name}JointCards: CardInfo[] = [...];
  const {name}BusinessGroupCards: CardInfo[] = [...];
  ```
- **`useActiveCards()`** — add case returning the cards array
- **`useActiveGroupCards()`** — add case if business has TEAM account
- **`useActiveJointCards()`** — add case if personal has SHARED account
- **`useActiveCurrencies()`** — add case returning currencies
- **`useActiveTransactions()`** — add case calling the transaction builder
- **`useHasJars()`** — add dataset ID to the inclusion list if jars exist
- **`useActiveJars()`** — add case returning jar array
- **`useHasJoint()`** — add dataset ID if SHARED exists
- **`useHasTaxes()`** — add dataset ID if TEAM exists
- **`useActiveJointCurrencies()`** — add case if applicable
- **`useActiveJointTransactions()`** — add case if applicable
- **`useActiveGroupCurrencies()`** — add case if applicable
- **`useActiveGroupTransactions()`** — add case if applicable
- **`DATASET_IDENTITY`** — add entry:
  ```typescript
  '{name}-personal': { personalName: '{Display Name}', businessName: '{Business Name}', personalAvatar: '/avatar-{name}.png', businessAvatar: undefined, pairedDataset: '{name}-business' },
  '{name}-business': { personalName: '{Display Name}', businessName: '{Business Name}', personalAvatar: '/avatar-{name}.png', businessAvatar: undefined, pairedDataset: '{name}-personal' },
  ```

### 3. `mobile/src/components/IOSTopBar.tsx`

- Add to `PERSONAL_AVATARS` map:
  ```typescript
  '{name}-personal': '/avatar-{name}.png',
  '{name}-business': '/avatar-{name}.png',
  ```

### 4. `mobile/src/App.tsx`

Register ALL balance IDs in `balanceOwnerMap`:
```typescript
// {Name} main currencies
for (const c of {name}PersonalCurrencies) balanceOwnerMap.set(c.balanceId, { code: c.code, from: 'home' });
for (const c of {name}BusinessCurrencies) balanceOwnerMap.set(c.balanceId, { code: c.code, from: 'home' });
// {Name} jars
for (const jar of {name}PersonalJars) for (const c of jar.currencies) balanceOwnerMap.set(c.balanceId, { code: c.code, from: 'jar-account', jarId: jar.id });
// {Name} joint (if applicable)
for (const c of {name}JointCurrencies) balanceOwnerMap.set(c.balanceId, { code: c.code, from: 'joint-account', jar: 'joint' });
// {Name} group (if applicable)
for (const c of {name}BusinessGroupCurrencies) balanceOwnerMap.set(c.balanceId, { code: c.code, from: 'taxes-account', jar: 'taxes' });
```

### 5. `shared-resources/data/balances.ts`

Add dataset case in `computeTotalBalance()`:
```typescript
case '{name}-personal': {
  const main = sumCurrencies({name}PersonalCurrencies);
  const jars = {name}PersonalJars.reduce((sum, j) => sum + sumCurrencies(j.currencies), 0);
  const joint = sumCurrencies({name}JointCurrencies); // if applicable
  return main + jars + joint;
}
case '{name}-business': {
  const main = sumCurrencies({name}BusinessCurrencies);
  const stocks = sumCurrencies({name}BusinessStocksJar.currencies); // if applicable
  const group = sumCurrencies({name}BusinessGroupCurrencies); // if applicable
  return main + stocks + group;
}
```

### 6. `mobile/src/context/Dataset.tsx`

- Add to `DatasetType` union: `| '{name}-personal' | '{name}-business'`
- Add to the validation array in `useState` initializer
- The dropdown in the prototype settings will auto-pick up new types

### 7. `mobile/src/translations/en.ts` (and es.ts, fr.ts, de.ts, it.ts, pt.ts)

Add translation keys for any new jar names:
```typescript
'home.{jarKey}': '{Jar Display Name}',
```

### 8. Avatar file

Save the person's profile picture to `mobile/public/avatar-{name}.png` (or `.jpeg`).

---

## Product Type to UI Mapping

| CSV `product_type` | UI Pattern | Cards? | Account Details? | Request Button? |
|-------------------|------------|--------|-----------------|----------------|
| MAIN | Current Account (MCA card) | Yes | Yes | Yes |
| SAVINGS | Jar (JarCard on Home) | No | **No** | No |
| SHARED | Joint Account (MCA with people icon) | Yes | Yes | Yes |
| TEAM | Group Account (MCA with money icon) | Yes | No | No |

---

## Verification Checklist

After wiring everything, verify:

1. `npm run build` passes with no errors
2. Select the new dataset in the dropdown
3. **Home page:**
   - Total balance matches sum of all accounts
   - Current Account MCA shows correct card count and stack
   - Card imagery matches (physical = green/dark-green, digital = tapestry)
   - `cardInfoLight` is correct (dark text on green, white on tapestry/dark-green)
   - Jars render with correct names, colours (brand brights only), and amounts
   - Joint account shows (if applicable) with people icon label
   - Group/Taxes account shows (if applicable) with money icon label
4. **Cards tab:**
   - All cards appear (main + joint/group merged)
   - Physical cards are first in carousel
   - Each card has a distinct tapestry image
5. **Account page (tap into Current Account):**
   - Card stack matches Home
   - Card count matches
   - Account details button shows (not on jars or groups)
   - Currencies listed correctly
6. **Jar pages:**
   - No "Account details" button
   - Correct balance and currencies
   - Jar header colour is a brand bright
7. **Joint/Group pages:**
   - Participants button in top bar shows correct avatar (the user's face)
   - Card imagery is correct
   - Group: no account details button
   - Joint: account details button present
8. **Account switching** — paired dataset works (personal <-> business)
9. Navigate into currencies and back — no broken routes

---

## Common Mistakes to Avoid

1. **Using non-brand hex colours for jars** — only `#9FE870`, `#FFEB69`, `#A0E1E1`, `#FFD7EF`, `#FFC091`
2. **Using business card images for personal accounts** (or vice versa for physical cards)
3. **Duplicate tapestry images** on the same account's digital cards
4. **Forgetting to put physical cards first** in the card array
5. **Showing account details on jars** — jars never have the Account Details button
6. **Hardcoding balances** instead of using `computeCurrencyBalance()`
7. **Missing a hook case** in `useDataset.ts` — check EVERY switch/if block
8. **Forgetting `balanceOwnerMap`** entries — causes broken navigation from notifications
9. **Forgetting `IOSTopBar.tsx` PERSONAL_AVATARS** — causes wrong face in participants button
10. **White text on light green physical card** — `cardInfoLight` must be false for personal physical
