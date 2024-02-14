# 1. Configuration of Testing Framework.

In the project, Jest has been utilized for unit testing, while Cypress has been employed for integration testing purposes.

- **Jest:** Jest is a user-friendly JavaScript Testing Framework known for its simplicity. It seamlessly integrates with a variety of projects, including those employing Babel, TypeScript, Node, React, Angular, Vue, and more. As a zero-configuration testing platform, Jest is highly recommended as the test runner for your project.

### Configuration

Jest is included in the package and can be installed using the following command:

```bash
npm install --save-dev jest
```

Executing this command will install the Jest package and add it to the dev-dependencies. Then, create a new directory named `__tests__` in the root of the project. Following that, add these scripts in the `package.json` file.

```json
"scripts":{
    "pretest": "NODE_ENV=test npx sequelize-cli db:drop && NODE_ENV=test npx sequelize-cli db:create",
    "test": "NODE_ENV=test jest --detectOpenHandles",
}
```

To run the tests, run the following command:

```bash
npm test
```

This will run the tests and display the results in the console.

- **Cypress:** Cypress is a cutting-edge front-end testing tool designed for the modern web. Renowned for its speed, simplicity, and reliability, Cypress offers robust testing capabilities for any browser-based application. It comes highly recommended for conducting end-to-end testing in your project.

### Configuration

To configure Cypress, install the cypress package and then execute the following command:

```bash
npm install --save-dev cypress
```

Executing this command will install the Cypress package and add it to the devDependencies in the package.json file. Following that, create a folder named `cypress` in the root of the project and within that folder, create another folder named `e2e`. Finally, add the test files within the `e2e` folder.

Generate a `cypress.config.js` file in the root directory of the project and include the following code:

```javascript
/* eslint-disable no-unused-vars */
const { defineConfig } = require("cypress");

module.exports = defineConfig({
  e2e: {
    setupNodeEvents(on, config) {
      // implement node event listeners here
    },
  },
});
```

Configure the `package.json` file by adding the following command in the scripts section:

```json
"scripts": {
    "clean:start": "npm run pretest && NODE_ENV=test npm start",
    "cy:test": "npx cypress run"
}
```

# 2. Test Suite Coverage

To ensure comprehensive coverage of the application, I've implemented two distinct types of testing:

**1. Unit Testing**:

- These tests concentrate on individual components, functions, or modules in isolation.
- Their purpose is to verify that each unit of code behaves as intended and adequately handles different scenarios.
- Unit Tests play a crucial role in identifying bugs early during development and ensuring code reliability.

Unit Testing using Jest:

![unit_test](https://github.com/Gopal-379/WD401/assets/83073228/32c9bce2-2c0a-4434-b0c9-e2550d070000)

**2. Integration Testing**:

- These tests assess the interactions between various components within the application.
- Their aim is to validate the integration points between components and ensure smooth communication.
- Integration testing aids in pinpointing issues that arise during component integration, such as data flow errors or compatibility issues.

Integration Testing using Cypress:

![cypress](https://github.com/Gopal-379/WD401/assets/83073228/de86854f-c40c-4949-ac41-b11c731332de)

# 3. Automatic Test Suite Execution on GitHub.

The test suite is set to run automatically upon pushing changes to GitHub. It is configured to use both Jest and Cypress, and the execution is orchestrated through GitHub Actions.

#### GitHub Actions

Employing Continuous Integration (CI) services to integrate with GitHub repositories is essential for automating the execution of test suites. GitHub Actions is a prominent Continuous Integration service that facilitates this process. The following outlines how to utilize GitHub Actions to automatically run test suites when code is pushed to GitHub:

Step-1: In GitHub repository, create a directory named `.github/workflows`.

Step-2: Inside this directory, create a YAML file named `main.yml`.

# 4. GitHub Actions Walkthrough.

The test suite is set to run automatically upon pushing changes to GitHub. It is configured to use both Jest and Cypress, and the execution is orchestrated through GitHub Actions.

Add the following code to `main.yml`:

```yaml
name: Auto test Sports Scheduler
on: push

# Environment Variables Configuration
env:
  PG_DATABASE: wd-sportscheduler-test
  PG_USER: postgres
  PG_PASSWORD: admin
jobs:
  # Job to run tests
  run-tests:
    # Containers must run in Linux based operating systems
    runs-on: ubuntu-latest

    # Service container for PostgreSQL
    services:
      # Label used to access the service container
      postgres:
        # Docker Hub image
        image: postgres:11.7
        # Provide the password for postgres
        env:
          POSTGRES_USER: postgres
          POSTGRES_PASSWORD: admin
          POSTGRES_DB: wd-sportscheduler-test
        # Set health checks to wait until postgres has started
        options: >-
          --health-cmd pg_isready
          --health-interval 10s
          --health-timeout 5s
          --health-retries 5
        ports:
          - 5432:5432

    steps:
      # Checkout repository code
      - name: Check out repository code
        uses: actions/checkout@v4

      # Install dependencies
      - name: Install dependencies
        run: npm ci

      # Run unit tests
      - name: Run unit tests
        run: npm test

        # Run the application and integration tests
      - name: Run the app
        id: run-app
        run: |
          npm install
          npx sequelize-cli db:drop
          npx sequelize-cli db:create
          npx sequelize-cli db:migrate
          PORT=3000 npm start &
          sleep 5

      - name: Run integration tests
        run: |
          npm install cypress cypress-json-results
          npx cypress run --env STUDENT_SUBMISSION_URL="http://localhost:3000/"
```

Here's a breakdown of the steps happening in each part of the workflow:

- Name and Trigger:

  - Name: "Auto test Sports Scheduler"
  - Trigger: The workflow is triggered on every push event to the repository.

- Environment Variables:

  - Environment variables like PG_DATABASE, PG_USER, and PG_PASSWORD are defined for use within the workflow. These variables likely contain database connection information for PostgreSQL.

- Jobs:

  - The workflow contains one job named "run-tests".
  - It runs on an Ubuntu latest environment.
    -Services:

  - The workflow sets up a PostgreSQL service using the postgres:11.7 Docker image.
  - This service is used for running integration tests against a PostgreSQL database.

- Steps:

1. Check out repository code:

   - This step checks out the code from the repository using the actions/checkout@v3 action. It fetches the latest commit from the default branch.

2. Install dependencies using ci:

   - This step installs project dependencies using npm ci. The npm ci command installs dependencies based on the package-lock.json file.

3. Run Unit Tests:

   - This step runs unit tests using a command like npm test.

4. Run Integration Tests:

   - This step installs Cypress and cypress-json-results using npm install.
   - It then runs integration tests using npx cypress run with the STUDENT_SUBMISSION_URL environment variable set to "http://localhost:3000/". This likely points Cypress to the locally running application for testing.

In this manner, the workflow file specifies the actions to be executed, automating the execution of the test suite on GitHub.

The following represents the workflow executed using GitHub Actions:

![github_actions](https://github.com/Gopal-379/WD401/assets/83073228/d68dca9d-8fc3-4c6e-982d-f08512e5aff6)
