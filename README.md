# ITDS362 UI testing lab

Selected parts: **Part 3 Robot Framework** and **Part 4 Cypress**. Both implement QP-01 on [QuickPizza](https://quickpizza.grafana.com); each also tests Selenium's web form. The test data creates pizzas named `Lab Pizza` plus a timestamp. The tests use the shared `default` user only for unauthenticated rating and never clear ratings.

## Run

- `robot/` contains the Robot suites, shared page keywords in `resources/quickpizza.resource`, and two passing runs in `results/run1` and `results/run2`. Install with `python -m venv .venv` and `.venv\Scripts\python.exe -m pip install -r requirements.txt` from `robot/`. Set `CHROMEDRIVER` to a ChromeDriver executable that matches your Chrome version, then run `robot -v BROWSER:headlesschrome first.robot web_form.robot pizza_scenario.robot`.
- `cypress/` contains Cypress specs, the admin login command, screenshots, and videos. Run `npm ci` then `npm test` from `cypress/`.

The Cypress specs passed separately on this Windows machine. The combined `npm test` command reproduced a Cypress runner error (`* is not a valid URL`) while switching from the QuickPizza spec to the web form spec. To run them separately, use `npx cypress run --browser chrome --spec cypress/e2e/pizza-scenario.cy.js` and the same command with `web-form.cy.js`.

See [RESULTS.md](RESULTS.md) for observed results and remaining manual steps.
