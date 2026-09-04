# Instructions

- Following Playwright test failed.
- Explain why, be concise, respect Playwright best practices.
- Provide a snippet of code with the fix, if possible.

# Test info

- Name: generated/p14-critical-flows/p14-tc-2-new-company-button-creates-a-record-and-opens-the-detail-pan.spec.ts >> TC-2 — New Company button creates a record and opens the detail panel >> TC-2: New Company button creates a record and opens the detail panel
- Location: tests/generated/p14-critical-flows/p14-tc-2-new-company-button-creates-a-record-and-opens-the-detail-pan.spec.ts:34:3

# Error details

```
Error: expect(locator).toHaveCount(expected) failed

Locator:  getByRole('checkbox', { name: 'Select row' })
Expected: 81
Received: 9
Timeout:  5000ms

Call log:
  - Expect "toHaveCount" with timeout 5000ms
  - waiting for getByRole('checkbox', { name: 'Select row' })
    3 × locator resolved to 8 elements
      - unexpected value "8"
    10 × locator resolved to 9 elements
       - unexpected value "9"

```

# Page snapshot

```yaml
- generic [ref=e3]:
  - generic [ref=e6]:
    - generic [ref=e9]: ‌
    - generic [ref=e10]:
      - generic [ref=e11]:
        - generic [ref=e13]: ‌
        - generic [ref=e15]: ‌
        - generic [ref=e17]: ‌
      - generic [ref=e18]:
        - generic [ref=e20]: ‌
        - generic [ref=e22]: ‌
        - generic [ref=e24]: ‌
      - generic [ref=e25]:
        - generic [ref=e27]: ‌
        - generic [ref=e29]: ‌
        - generic [ref=e31]: ‌
        - generic [ref=e33]: ‌
  - generic [ref=e38]:
    - generic [ref=e40]:
      - generic [ref=e42]: ‌
      - generic [ref=e44]: ‌
    - generic [ref=e47]: ‌
```

# Test source

```ts
  1   | // spec: specs/p14-tc-2-new-company-button-creates-a-record-and-opens-the-detail-pan.md
  2   | // seed: tests/seed.spec.ts
  3   | 
  4   | import { test, expect } from '@playwright/test';
  5   | import { createCompany } from '../../../helpers/project-14/createCompany';
  6   | 
  7   | test.describe('TC-2 — New Company button creates a record and opens the detail panel', () => {
  8   |   // Tracks the record ID of the company created during the test for best-effort afterEach cleanup.
  9   |   let newCompanyId: string | null = null;
  10  | 
  11  |   test.afterEach(async ({ page }) => {
  12  |     if (!newCompanyId) return;
  13  |     const idToDelete = newCompanyId;
  14  |     newCompanyId = null; // reset immediately so a second afterEach call is a no-op
  15  | 
  16  |     try {
  17  |       await page.goto('/objects/companies');
  18  |       // FRAGILE: CSS attribute selector used to locate the company row by its unique record ID
  19  |       const companyLink = page.locator(`a[href*="/object/company/${idToDelete}"]`);
  20  |       const isVisible = await companyLink.isVisible().catch(() => false);
  21  |       if (!isVisible) return; // already deleted in step 14
  22  | 
  23  |       await companyLink.click();
  24  |       const panel = page.getByRole('complementary');
  25  |       await panel.waitFor({ state: 'visible' });
  26  |       await panel.getByRole('button', { name: 'Options' }).click();
  27  |       await page.getByText('Delete Company', { exact: true }).click();
  28  |       await expect(companyLink).not.toBeVisible({ timeout: 5000 });
  29  |     } catch (e) {
  30  |       console.warn('TC-2 afterEach cleanup failed:', e);
  31  |     }
  32  |   });
  33  | 
  34  |   test('TC-2: New Company button creates a record and opens the detail panel', async ({ page }) => {
  35  |     let initialRowCount = 0;
  36  | 
  37  |     // 1. As default test user, navigate to /objects/companies.
  38  |     await test.step('As default test user, navigate to /objects/companies.', async () => {
  39  |       await page.goto('/objects/companies');
  40  |       await expect(page).toHaveTitle(/Companies/);
  41  |     });
  42  | 
  43  |     // 2. Count the number of rows currently visible in the companies table and store the value.
  44  |     await test.step('Count the number of rows currently visible in the companies table and store the value.', async () => {
  45  |       initialRowCount = await page.getByRole('checkbox', { name: 'Select row' }).count();
  46  |     });
  47  | 
  48  |     // 3. Click the button labelled 'New Company'.
  49  |     await test.step("Click the button labelled 'New Company'.", async () => {
  50  |       // createCompany clicks 'New Company' and returns the new record's ID extracted from the right panel
  51  |       newCompanyId = await createCompany(page);
  52  |     });
  53  | 
  54  |     const panel = page.getByRole('complementary');
  55  | 
  56  |     // 4. Assert the companies table now contains one more row than the stored count.
  57  |     await test.step('Assert the companies table now contains one more row than the stored count.', async () => {
> 58  |       await expect(page.getByRole('checkbox', { name: 'Select row' })).toHaveCount(initialRowCount + 1);
      |                                                                        ^ Error: expect(locator).toHaveCount(expected) failed
  59  |     });
  60  | 
  61  |     // 5. Assert a right-side panel is visible on the page.
  62  |     await test.step('Assert a right-side panel is visible on the page.', async () => {
  63  |       await expect(panel).toBeVisible();
  64  |     });
  65  | 
  66  |     // 6. Assert the right-side panel contains a field labelled 'Domain Name'.
  67  |     await test.step("Assert the right-side panel contains a field labelled 'Domain Name'.", async () => {
  68  |       await expect(panel.getByText('Domain Name', { exact: true }).first()).toBeVisible();
  69  |     });
  70  | 
  71  |     // 7. Assert the right-side panel contains a field labelled 'Account Owner'.
  72  |     await test.step("Assert the right-side panel contains a field labelled 'Account Owner'.", async () => {
  73  |       await expect(panel.getByText('Account Owner', { exact: true }).first()).toBeVisible();
  74  |     });
  75  | 
  76  |     // 8. Assert the right-side panel contains a field labelled 'Annual Revenue'.
  77  |     await test.step("Assert the right-side panel contains a field labelled 'Annual Revenue'.", async () => {
  78  |       await expect(panel.getByText('Annual Revenue', { exact: true }).first()).toBeVisible();
  79  |     });
  80  | 
  81  |     // 9. Assert the right-side panel contains a field labelled 'Address'.
  82  |     await test.step("Assert the right-side panel contains a field labelled 'Address'.", async () => {
  83  |       await expect(panel.getByText('Address', { exact: true }).first()).toBeVisible();
  84  |     });
  85  | 
  86  |     // 10. Assert the right-side panel contains a field labelled 'Linkedin'.
  87  |     await test.step("Assert the right-side panel contains a field labelled 'Linkedin'.", async () => {
  88  |       await expect(panel.getByText('Linkedin', { exact: true }).first()).toBeVisible();
  89  |     });
  90  | 
  91  |     // 11. Assert the right-side panel contains a field labelled 'Creation date'.
  92  |     await test.step("Assert the right-side panel contains a field labelled 'Creation date'.", async () => {
  93  |       await expect(panel.getByText('Creation date', { exact: true }).first()).toBeVisible();
  94  |     });
  95  | 
  96  |     // 12. Assert the right-side panel contains a field labelled 'Created by'.
  97  |     await test.step("Assert the right-side panel contains a field labelled 'Created by'.", async () => {
  98  |       await expect(panel.getByText('Created by', { exact: true }).first()).toBeVisible();
  99  |     });
  100 | 
  101 |     // 13. Identify the newly created company row by its placeholder or empty Name cell.
  102 |     await test.step('Identify the newly created company row by its placeholder or empty Name cell.', async () => {
  103 |       // FRAGILE: CSS attribute selector used to locate the new company row by its unique record ID
  104 |       const newCompanyRow = page.locator(`a[href*="/object/company/${newCompanyId}"]`);
  105 |       await expect(newCompanyRow).toBeVisible();
  106 |     });
  107 | 
  108 |     // 14. Open the action menu for that row and click 'Delete' (or equivalent delete control).
  109 |     await test.step("Open the action menu for that row and click 'Delete' (or equivalent delete control).", async () => {
  110 |       // The right-side panel for the new company is already open; Options is its action menu
  111 |       await panel.getByRole('button', { name: 'Options' }).click();
  112 |       await page.getByText('Delete Company', { exact: true }).click();
  113 |     });
  114 | 
  115 |     // 15. Assert the row is no longer present in the companies table.
  116 |     await test.step('Assert the row is no longer present in the companies table.', async () => {
  117 |       // FRAGILE: CSS attribute selector used to verify the row was removed
  118 |       const newCompanyRow = page.locator(`a[href*="/object/company/${newCompanyId}"]`);
  119 |       await expect(newCompanyRow).not.toBeVisible();
  120 |       newCompanyId = null; // already deleted — skip afterEach cleanup
  121 |     });
  122 |   });
  123 | });
  124 | 
```