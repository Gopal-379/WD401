# Handling Code Review Feedback

```javascript
// Code Snippet
function greet() {
    console.log("Hello from Developer A.");
}
```

- **Feedback**
  Consider adding a parameter to the greet function to accept a name for a more personalized greeting.

# Updated Code

```javascript
// Developer A changes to the code snippet
function greet(name) {
    // Add a parameter to accept a name for a more personalized greeting
    console.log(`Hello ${name} from Developer A.`);
}
```

- **Explanation:** Adding a parameter to accept a name for a more personalized greeting.

# Iterative Development Process

![flowchart](https://github.com/Gopal-379/WD401/assets/83073228/4e3a907a-54ef-46c3-a5db-df784638b5fb)


- **Explanation:** This flowchart illustrates an iterative development process.

# Resolving Merge Conflicts

Merge conflicts occur when multiple branches have changes that conflict with each other, typically when changes are made to the same part of a file by different contributors.

- **Scenario:** Developers have modified the logic within the greet function in conflicting ways.

#### Code in Branch A of Developer A

```javascript
// Developer A's changes
function greet(name) {
    if (name) {
        console.log(`Hello ${name} from Developer A.`);
    } else {
        console.log(`Hello from Developer A.`);
    }
}
```

#### Code in Branch B of Developer B

```javascript
// Developer B's changes
function greet(name) {
    console.log(`Hello ${name || 'stranger'} from Developer B.`);
}
```

When Developer B merges his branch i.e., Branch B, the following conflict occurs in branch A:

```javascript
// Developer A's changes
function greet(name) {
<<<<<<< HEAD
    if (name) {
        console.log(`Hello ${name} from Developer A.`);
    } else {
        console.log(`Hello from Developer A.`);
    }
=======
    console.log(`Hello ${name || 'stranger'} from Developer B.`);
>>>>>>> Branch B
}
```

_The following conflict is resolved either by accepting current branch changes or by accepting incoming changes from other branch or accept both changes._

# CI/CD Integration

Tools such as jest, eslint, prettier, husky and lint-staged are employed to maintain code quality and adhere to coding standards. They are seamlessly integrated into the CI/CD pipeline to guarantee that code undergoes testing and linting prior to being merged into the main branch.

<p>Here's an example of an automated unit test that executes whenever changes are committed to Git and are located within the __tests__ directory.</p>

```javascript
const greet = require('./greet');

test('greet function should return a personalized greeting', () => {
  const name = 'John';
  expect(greet(name)).toBe(`Hello ${name} from Developer A`);
});
```

- **Explanation:** This is a basic test code using Jest to ensure that the `greet` function returns a personalized greeting with the provided name.

### Potential Issues and Troubleshooting

- If tests fail, Husky prevents the commit, indicating that there are failing tests.
- Troubleshoot by reviewing test results to identify and fix issues before committing changes.
- If linting encounters failures, it is essential to review the ESLint configuration and address any linting errors and warnings before proceeding with the commit.
