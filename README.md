# ITDS362 UI testing lab

Selected parts: **Part 3 Robot Framework** and **Part 4 Cypress**. Both implement QP-01 on [QuickPizza](https://quickpizza.grafana.com); each also tests Selenium's web form. The test data creates pizzas named `Lab Pizza` plus a timestamp. The tests use the shared `default` user only for unauthenticated rating and never clear ratings.

## Run

- `robot/` contains the Robot suites, shared page keywords in `resources/quickpizza.resource`, and two passing runs in `results/run1` and `results/run2`. Install with `python -m venv .venv` and `.venv\Scripts\python.exe -m pip install -r requirements.txt` from `robot/`. Set `CHROMEDRIVER` to a ChromeDriver executable that matches your Chrome version, then run `robot -v BROWSER:headlesschrome first.robot web_form.robot pizza_scenario.robot`.
- `cypress/` contains Cypress specs, the admin login command, screenshots, and videos. Run `npm ci` then `npm test` from `cypress/`.

The Cypress 16.2.1 runner on this Windows machine reproducibly stopped while switching between specs with `* is not a valid URL`, in either spec order. `npm test` runs each spec in a fresh Cypress process so one command checks both.

`manual-evidence/` holds screenshots supplied from the hand run. Attempt 3 shows all seven QP-01 checks passing across the customer page and admin list. See [RESULTS.md](RESULTS.md) for the observed results, including earlier attempts.
