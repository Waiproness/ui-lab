# UI lab results

Tool parts that I chose: Part 3 Robot Framework and Part 4 Cypress.

## Part 0

- Step 0.1, Chrome version: 158.0.8127.36 (Windows).
- Step 0.3, ChromeDriver version: 158.0.8127.36, downloaded from Chrome for Testing. The local path is kept out of Git.
- Step 0.4, locator of Pizza, Please!: `button[name="pizza-please"]`.
- Step 0.4, locator of Excluded tools: `select` after Advanced is on.
- Step 0.4, locator of Must be vegetarian: `#default-checkbox`.
- Step 0.4, locator of Custom Pizza Name: `input#pizza-name`.
- Step 0.5, manual attempt 1: `Lab Pizza 20261120-143015` returned Pizza cutter and 450 calories per slice; the rating message appeared, but the pizza was absent from the visible admin list even after Refresh. Step 7 was **not observed as passing**. See `manual-evidence/qp01-step5-recommendation-and-login-needed.png` and `qp01-attempt1-admin-absent.png`.
- Step 0.5, manual attempt 2: `Lab Pizza 20261120 2times` returned Pizza cutter and 550 calories per slice. The admin list showed `Lab Pizza 20261120 2times (tool=Pizza cutter, ingredients_number=8)`, so step 7 passed. The name contains a date but no time; the step 5 rating message was not captured for this attempt. See `manual-evidence/qp01-attempt2-recommendation.png` and `qp01-attempt2-admin-list.png`.
- Step 0.5, manual attempt 3: the home page showed the expected heading and advanced fields; max calories was 600, Knife was excluded, and vegetarian was checked. The recommendation showed `Lab Pizza 20261120-003142`, tool Pizza cutter, and 400 calories per slice. Rating without login displayed `Please log in first.` The admin page showed `Lab Pizza 20261120-003142 (tool=Pizza cutter, ingredients_number=6) (newest)`. All seven checks passed. See `manual-evidence/qp01-attempt3-full-customer-flow.png` and `qp01-attempt3-admin-list.png`.

| Step | Actual result from manual attempt 3 | Pass or fail |
|------|-------------------------------------|--------------|
| 1 | Home heading read `Looking to break out of your pizza routine?`. | Pass |
| 2 | Max Calories per Slice, Excluded tools, Must be vegetarian and Custom Pizza Name were visible. | Pass |
| 3 | Max calories 600, Knife selected as excluded, vegetarian checked, timestamped name entered. | Pass |
| 4 | Name `Lab Pizza 20261120-003142`; tool Pizza cutter; 400 calories per slice. | Pass |
| 5 | After Love it!, the page displayed `Please log in first.` | Pass |
| 6 | Admin page showed `Latest pizza recommendations`. | Pass |
| 7 | Admin list showed the same pizza name and Pizza cutter. | Pass |

## Part 3: Robot Framework

- `robot --version`: Robot Framework 7.5.1 on Python 3.12.10.
- Step 3.2, deliberately wrong title: `Title should have been 'Web Form' but was 'Web form'.` (1 test, 0 passed, 1 failed). The correct-title suite passed in both full runs.
- Full run 1: 3/3 passed, 20.22 seconds; QP-01 pizza `Lab Pizza 20261119-231824`, tool Scissors, 325 calories. Evidence: `robot/results/run1/`.
- Full run 2: 3/3 passed, 20.38 seconds; QP-01 pizza `Lab Pizza 20261120-001717`, tool Pizza cutter, 325 calories. Evidence: `robot/results/run2/`.

## Part 4: Cypress

- Node.js v24.16.0, npm 11.13.0, Cypress 16.2.1; Chrome 158 headless.
- Step 4.3, deliberately wrong title: `Timed out retrying after 10000ms`; actual `Web form`, expected `Web Form` (0 passing, 1 failing). The correct-title spec passed.
- Step 4.6, `POST /api/pizza`: status 200; the latest screenshot shows tool Pizza cutter and 450 calories per slice, matching the API response by assertion.
- Step 4.7, admin list: `Lab Pizza 20261120-001832 (tool=Pizza cutter, ingredients_number=7) (newest)` in the latest screenshot.
- Step 4.8: `npm test` passed twice, running both specs in separate Cypress processes each time. QP-01 took 11 and 10 seconds; the web form took about 3 seconds in each run. Videos and three QP-01 screenshots are in `cypress/cypress/`. Earlier QP-01 executions also passed in 11 and 12 seconds; the screenshot retained in Git shows `Lab Pizza 20261120-001832`.
- A single Cypress process running both specs failed reproducibly at the transition with `* is not a valid URL`, in either spec order. The `npm test` script starts a fresh process per spec; this is a runner workaround, not a test assertion change.
- Step 4.9, second browser: not attempted (optional).

## Part B

1. QuickPizza uses `floating_filled` for three number inputs, so that id alone cannot identify Max Calories per Slice. The Robot resource uses the nearby label; Cypress uses the label's sibling input. Giving each number input its own unique id would make the locator shorter and easier to maintain.
2. The step 7 comparison is a consistency oracle: the admin list must show the tool that step 4 recommended. It would catch a fault where the admin list stores or renders a different tool. Both Robot runs passed this comparison.
3. The page does not expose a vegetarian assertion in the recommendation. A useful check would compare a `vegetarian` flag from the pizza API response, or inspect every ingredient against a trusted vegetarian catalog. The test would need that data exposed reliably by the page or server.
4. Robot explicitly waits for the home heading, tool option, recommendation and admin list; its HTML log expands each keyword and screenshot. Cypress retries queries and assertions automatically and waits on `POST /api/pizza`; its command log and video show the sequence. The Cypress QP-01 spec was shorter to write, while Robot's resource file made the seven written steps easy to read. Both QP-01 specs passed; Cypress's combined runner hit the URL error described above.
5. I would choose Robot Framework for Project 2. Its named keywords map directly to written system test steps, and changing a locator in one resource file repairs every test that uses it. It also produced two full passing reports with screenshots on this machine.
6. In Part 3, change `${MAX CALORIES}` in `robot/resources/quickpizza.resource` if the calories input changes. `pizza_scenario.robot` uses a keyword and does not need a locator edit.
7. In Part 4, QP-01 stays in one `it` because Cypress clears cookies between tests. Splitting steps 6 and 7 into another test could send that test to a different QuickPizza server, so its admin list might not contain the pizza created in the first test.
