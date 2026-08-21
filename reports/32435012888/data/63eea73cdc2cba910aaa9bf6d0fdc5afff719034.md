# Instructions

- Following Playwright test failed.
- Explain why, be concise, respect Playwright best practices.
- Provide a snippet of code with the fix, if possible.

# Test info

- Name: generated/p9-e2e-critical-flows/p9-tc-40-runs-and-bugs-created-during-a-paused-window-remain-hidden-f.spec.ts >> TC-40 — Runs and bugs created during a paused window remain hidden from the client after resume >> TC-40: Runs and bugs created during a paused window remain hidden from the client after resume
- Location: tests/generated/p9-e2e-critical-flows/p9-tc-40-runs-and-bugs-created-during-a-paused-window-remain-hidden-f.spec.ts:66:3

# Error details

```
TimeoutError: locator.click: Timeout 30000ms exceeded.
Call log:
  - waiting for getByRole('button', { name: 'Maintenance mode' })

```

# Page snapshot

```yaml
- generic [ref=e3]:
  - complementary [ref=e4]:
    - generic [ref=e7]: Blueprint
    - generic [ref=e8]:
      - generic [ref=e9]: Navigation
      - link "Dashboard" [ref=e10] [cursor=pointer]:
        - /url: /
        - img [ref=e11]
        - text: Dashboard
      - link "Projects" [ref=e13] [cursor=pointer]:
        - /url: /projects
        - img [ref=e14]
        - text: Projects
    - generic [ref=e16]:
      - generic [ref=e17]: Project
      - link "Views" [ref=e18] [cursor=pointer]:
        - /url: /projects/4/views
        - img [ref=e19]
        - text: Views
      - link "Visual" [ref=e22] [cursor=pointer]:
        - /url: /projects/4/visual
        - img [ref=e23]
        - text: Visual
    - generic [ref=e26]:
      - button "Change profile picture" [ref=e27] [cursor=pointer]:
        - generic [ref=e28]: T
      - generic [ref=e29]:
        - generic "Test Engineer — click to edit" [ref=e30] [cursor=pointer]: Test
        - generic "QA Engineer — click to edit" [ref=e31] [cursor=pointer]: QA Engineer
      - button "Log out" [ref=e32] [cursor=pointer]:
        - img [ref=e33]
  - main [ref=e36]:
    - button "Search test cases and bugs" [ref=e37] [cursor=pointer]:
      - img [ref=e38]
      - generic [ref=e41]: Search
      - generic [ref=e42]: /
    - generic [ref=e44]:
      - button "Back" [ref=e45] [cursor=pointer]:
        - img [ref=e46]
      - generic [ref=e48]:
        - link "Projects" [ref=e49] [cursor=pointer]:
          - /url: /projects
        - generic [ref=e50]: /
        - link "Sauce Labs Demo App" [ref=e51] [cursor=pointer]:
          - /url: /projects/4
        - generic [ref=e52]: /
        - generic [ref=e53]: Automation
    - generic [ref=e54]:
      - generic [ref=e55]:
        - img [ref=e56]
        - generic [ref=e58]: Runs are paused for this project's client — new runs since 8h ago are hidden from them.
        - button "Resume" [ref=e59] [cursor=pointer]
      - generic [ref=e60]:
        - generic [ref=e61]:
          - heading "Suites" [level=2] [ref=e62]
          - generic [ref=e63]:
            - button "Resume" [ref=e64] [cursor=pointer]
            - button "+ New suite" [ref=e65] [cursor=pointer]
        - generic [ref=e66]:
          - button "Web" [ref=e67] [cursor=pointer]
          - button "iOS" [disabled] [ref=e68]
          - button "Android" [disabled] [ref=e69]
        - generic [ref=e71]:
          - generic [ref=e72]:
            - img [ref=e74]
            - generic [ref=e77]:
              - generic [ref=e78]: Regression Pack
              - generic [ref=e79]:
                - generic [ref=e80]: Web
                - button "Nightly" [ref=e81] [cursor=pointer]:
                  - img [ref=e82]
                  - text: Nightly
                - button "On PR" [ref=e84] [cursor=pointer]:
                  - img [ref=e85]
                  - text: On PR
                - generic [ref=e90]: 5 test cases
          - generic [ref=e91]:
            - generic [ref=e93]: 5 passed
            - generic [ref=e94]:
              - generic [ref=e95]: 2d ago
              - generic [ref=e96]: ·
              - generic [ref=e97]: 0ms
          - generic [ref=e98]:
            - button "Run suite" [ref=e99] [cursor=pointer]
            - button "View test cases" [ref=e100] [cursor=pointer]
            - button "Add to suite" [ref=e101] [cursor=pointer]
      - generic [ref=e102]:
        - heading "Recent executions" [level=2] [ref=e103]
        - generic [ref=e104]:
          - generic [ref=e106]: 1 run
          - generic [ref=e110] [cursor=pointer]:
            - img [ref=e112]
            - generic [ref=e115]:
              - generic [ref=e116]: Regression Pack
              - generic [ref=e117]: "Run #1 · Manual"
            - generic [ref=e118]: Web
            - generic [ref=e121]: 5 passed
            - generic [ref=e122]: 2d ago
            - generic [ref=e123]: 42s
            - button "More actions" [ref=e125]:
              - img [ref=e126]
      - generic [ref=e131]:
        - heading "Nightly builds" [level=2] [ref=e132]
        - generic [ref=e133]:
          - heading "No nightly runs yet" [level=3] [ref=e134]
          - paragraph [ref=e135]: These populate automatically once the scheduled workflow runs.
  - link "Engineering" [ref=e137] [cursor=pointer]:
    - /url: /projects/4/engineering
    - img [ref=e138]
```

# Test source

```ts
  1   | // spec: specs/p9-tc-40-runs-and-bugs-created-during-a-paused-window-remain-hidden-f.md
  2   | // seed: tests/seed.spec.ts
  3   | 
  4   | import { test, expect } from '../../../helpers/project-9/fixtures';
  5   | import { createTestData } from '../../../helpers/testData';
  6   | 
  7   | test.describe('TC-40 — Runs and bugs created during a paused window remain hidden from the client after resume', () => {
  8   |   // Declared at describe scope so afterEach can access them for cleanup.
  9   |   let runTitle = '';
  10  |   let bugTitle = '';
  11  |   let projectId = '';
  12  | 
  13  |   // Cleanup contract: archive the execution run and bug created by this test,
  14  |   // and ensure maintenance mode is resumed. Each removal is best-effort —
  15  |   // a cleanup failure must never replace a real assertion failure.
  16  |   test.afterEach(async ({ accounts }) => {
  17  |     if (!projectId) return;
  18  | 
  19  |     const staff = await accounts.pageAs('staff');
  20  | 
  21  |     // Step 24: Archive the execution run created in step 8.
  22  |     if (runTitle) {
  23  |       try {
  24  |         await staff.goto(`/projects/${projectId}/executions`);
  25  |         // FRAGILE: execution-run rows carry no ARIA role — .card is the
  26  |         // hand-authored class confirmed live for executions-page run rows.
  27  |         const runCard = staff.locator('.card').filter({ hasText: runTitle });
  28  |         if (await runCard.count() > 0) {
  29  |           await runCard.getByRole('button', { name: 'Run actions' }).click();
  30  |           await staff.getByRole('button', { name: 'Archive', exact: true }).click();
  31  |         }
  32  |       } catch (e) {
  33  |         console.warn('TC-40 cleanup: failed to archive execution run', e);
  34  |       }
  35  |     }
  36  | 
  37  |     // Step 25: Archive the bug created in step 11.
  38  |     if (bugTitle) {
  39  |       try {
  40  |         await staff.goto(`/projects/${projectId}/bugs`);
  41  |         // FRAGILE: bug rows carry no ARIA role — .bug-row is the
  42  |         // hand-authored class confirmed live.
  43  |         const bugRow = staff.locator('.bug-row').filter({ hasText: bugTitle });
  44  |         if (await bugRow.count() > 0) {
  45  |           await bugRow.click();
  46  |           await staff.getByRole('button', { name: 'Archive', exact: true }).click();
  47  |         }
  48  |       } catch (e) {
  49  |         console.warn('TC-40 cleanup: failed to archive bug', e);
  50  |       }
  51  |     }
  52  | 
  53  |     // Reverting state: ensure maintenance mode is resumed if still paused.
  54  |     try {
  55  |       await staff.goto(`/projects/${projectId}/automation`);
  56  |       const resumeBtn = staff.getByRole('button', { name: 'Resume' }).first();
  57  |       const isVisible = await resumeBtn.isVisible().catch(() => false);
  58  |       if (isVisible) {
  59  |         await resumeBtn.click();
  60  |       }
  61  |     } catch (e) {
  62  |       console.warn('TC-40 cleanup: failed to resume maintenance mode', e);
  63  |     }
  64  |   });
  65  | 
  66  |   test('TC-40: Runs and bugs created during a paused window remain hidden from the client after resume', async ({ accounts }) => {
  67  |     // Many steps across two actors and multiple page navigations — give extra headroom.
  68  |     test.setTimeout(120_000);
  69  | 
  70  |     let staff: Awaited<ReturnType<typeof accounts.pageAs>>;
  71  |     let client: Awaited<ReturnType<typeof accounts.pageAs>>;
  72  | 
  73  |     // 1. Open a page context as the `staff` alias via `accounts.pageAs('staff')`
  74  |     await test.step("Open a page context as the `staff` alias via `accounts.pageAs('staff')`", async () => {
  75  |       staff = await accounts.pageAs('staff');
  76  |     });
  77  | 
  78  |     // 2. Open a page context as the `client_single_project` alias via `accounts.pageAs('client_single_project')`
  79  |     await test.step("Open a page context as the `client_single_project` alias via `accounts.pageAs('client_single_project')`", async () => {
  80  |       client = await accounts.pageAs('client_single_project');
  81  |     });
  82  | 
  83  |     // 3. As Client, navigate to '/' and verify the URL resolves to /projects/<id>; capture the numeric project ID
  84  |     await test.step("As Client, navigate to '/' and verify the URL resolves to /projects/<id>; capture the numeric project ID", async () => {
  85  |       await client.goto('/');
  86  |       await expect(client).toHaveURL(/\/projects\/\d+$/);
  87  |       const match = client.url().match(/\/projects\/(\d+)/);
  88  |       projectId = match?.[1] ?? '';
  89  |       expect(projectId).not.toBe('');
  90  |     });
  91  | 
  92  |     // 4. As Staff, navigate to Project A's automation page (/projects/<id>/automation)
  93  |     await test.step("As Staff, navigate to Project A's automation page (/projects/<id>/automation)", async () => {
  94  |       await staff.goto(`/projects/${projectId}/automation`);
  95  |       await expect(staff).toHaveURL(new RegExp(`/projects/${projectId}/automation$`));
  96  |     });
  97  | 
  98  |     // 5. As Staff, click the Maintenance Mode button to pause Project A
  99  |     await test.step('As Staff, click the Maintenance Mode button to pause Project A', async () => {
> 100 |       await staff.getByRole('button', { name: 'Maintenance mode' }).click();
      |                                                                     ^ TimeoutError: locator.click: Timeout 30000ms exceeded.
  101 |     });
  102 | 
  103 |     // 6. Verify the automation page shows Project A is now in a paused/maintenance state
  104 |     await test.step('Verify the automation page shows Project A is now in a paused/maintenance state', async () => {
  105 |       // After clicking, the Maintenance mode button is replaced by an active Resume button.
  106 |       // Both a banner and the button confirm the paused state.
  107 |       await expect(staff.getByRole('button', { name: 'Resume' }).first()).toBeVisible();
  108 |     });
  109 | 
  110 |     // 7. As Staff, navigate to Project A's executions page (/projects/<id>/executions)
  111 |     await test.step("As Staff, navigate to Project A's executions page (/projects/<id>/executions)", async () => {
  112 |       await staff.goto(`/projects/${projectId}/executions`);
  113 |       await expect(staff).toHaveURL(new RegExp(`/projects/${projectId}/executions$`));
  114 |     });
  115 | 
  116 |     // 8. As Staff, create a new execution run with a unique title
  117 |     await test.step('As Staff, create a new execution run with a unique title', async () => {
  118 |       runTitle = `TC-40 run ${createTestData().title}`;
  119 |       await staff.getByRole('button', { name: '+ New execution run' }).click();
  120 |       await staff.getByPlaceholder('e.g. Sprint 14 regression').fill(runTitle);
  121 |       // Single combobox on executions page when the modal is open (platform selector).
  122 |       await staff.getByRole('combobox').selectOption({ label: 'Web' });
  123 |       // FRAGILE: .checkbox-list scopes to the test-case list so other modal
  124 |       // checkboxes (exclude-from-score, etc.) are not accidentally selected.
  125 |       // Established pattern from TC-33 step 8.
  126 |       await staff.locator('.checkbox-list').getByRole('checkbox').first().check();
  127 |       await staff.getByRole('button', { name: 'Create run' }).click();
  128 |     });
  129 | 
  130 |     // 9. Verify the newly created execution run appears in the executions list
  131 |     await test.step('Verify the newly created execution run appears in the executions list', async () => {
  132 |       await expect(staff.getByRole('link', { name: runTitle })).toBeVisible();
  133 |     });
  134 | 
  135 |     // 10. As Staff, navigate to Project A's bugs page (/projects/<id>/bugs)
  136 |     await test.step("As Staff, navigate to Project A's bugs page (/projects/<id>/bugs)", async () => {
  137 |       await staff.goto(`/projects/${projectId}/bugs`);
  138 |       await expect(staff).toHaveURL(new RegExp(`/projects/${projectId}/bugs$`));
  139 |     });
  140 | 
  141 |     // 11. As Staff, create a new bug with a unique title
  142 |     await test.step('As Staff, create a new bug with a unique title', async () => {
  143 |       bugTitle = `TC-40 bug ${createTestData().title}`;
  144 |       await staff.getByRole('button', { name: '+ Log bug' }).click();
  145 |       await staff.getByPlaceholder('Short description of the bug').fill(bugTitle);
  146 |       // FRAGILE: the bug form's comboboxes lack explicit label associations.
  147 |       // Within .modal: nth(0)=Execution run, nth(1)=Feature, nth(2)=Platform, nth(3)=Severity.
  148 |       await staff.locator('.modal').getByRole('combobox').nth(1).selectOption({ label: 'Authentication' });
  149 |       await staff.locator('.modal').getByRole('combobox').nth(2).selectOption({ label: 'Web' });
  150 |       await staff.getByRole('button', { name: 'Log bug', exact: true }).click();
  151 |     });
  152 | 
  153 |     // 12. Verify the newly created bug appears in the bugs list
  154 |     await test.step('Verify the newly created bug appears in the bugs list', async () => {
  155 |       // FRAGILE: bug rows carry no ARIA role — .bug-row is the hand-authored class confirmed live.
  156 |       await expect(staff.locator('.bug-row').filter({ hasText: bugTitle })).toBeVisible();
  157 |     });
  158 | 
  159 |     // 13. As Client, navigate to Project A's executions page (/projects/<id>/executions)
  160 |     await test.step("As Client, navigate to Project A's executions page (/projects/<id>/executions)", async () => {
  161 |       await client.goto(`/projects/${projectId}/executions`);
  162 |       await expect(client).toHaveURL(new RegExp(`/projects/${projectId}/executions$`));
  163 |     });
  164 | 
  165 |     // 14. Verify the execution run created in step 8 does not appear in the client's runs list
  166 |     await test.step("Verify the execution run created in step 8 does not appear in the client's runs list", async () => {
  167 |       // Wait for the page to show at least one pre-existing card (proves the list
  168 |       // has loaded) before asserting the paused-window run is absent.
  169 |       // FRAGILE: .card is the hand-authored class for executions-page run rows.
  170 |       await expect(client.locator('.card').first()).toBeVisible({ timeout: 30_000 });
  171 |       await expect(client.getByRole('link', { name: runTitle })).toHaveCount(0);
  172 |     });
  173 | 
  174 |     // 15. As Client, navigate to Project A's bugs page (/projects/<id>/bugs)
  175 |     await test.step("As Client, navigate to Project A's bugs page (/projects/<id>/bugs)", async () => {
  176 |       await client.goto(`/projects/${projectId}/bugs`);
  177 |       await expect(client).toHaveURL(new RegExp(`/projects/${projectId}/bugs$`));
  178 |     });
  179 | 
  180 |     // 16. Verify the bug created in step 11 does not appear in the client's bugs list
  181 |     await test.step("Verify the bug created in step 11 does not appear in the client's bugs list", async () => {
  182 |       // Wait for at least one pre-existing bug row before asserting absence.
  183 |       // FRAGILE: .bug-row is the hand-authored class confirmed live.
  184 |       await expect(client.locator('.bug-row').first()).toBeVisible({ timeout: 30_000 });
  185 |       await expect(client.locator('.bug-row').filter({ hasText: bugTitle })).toHaveCount(0);
  186 |     });
  187 | 
  188 |     // 17. As Staff, navigate to Project A's automation page (/projects/<id>/automation)
  189 |     await test.step("As Staff, navigate to Project A's automation page (/projects/<id>/automation)", async () => {
  190 |       await staff.goto(`/projects/${projectId}/automation`);
  191 |       await expect(staff).toHaveURL(new RegExp(`/projects/${projectId}/automation$`));
  192 |     });
  193 | 
  194 |     // 18. As Staff, click the Maintenance Mode button to resume Project A
  195 |     await test.step('As Staff, click the Maintenance Mode button to resume Project A', async () => {
  196 |       // Two Resume buttons exist in the paused state (banner + Suites section);
  197 |       // clicking the first is sufficient.
  198 |       await staff.getByRole('button', { name: 'Resume' }).first().click();
  199 |     });
  200 | 
```