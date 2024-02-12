# Handling Code Review Feedback

```javascript
const handlesubmit = async (event: React.FormEvent<HTMLFormElement>) => {
  event.preventDefault();

  try {
    const response = await fetch(`${API_ENDPOINT}/organisations`, {
      method: "POST",
      headers: { "Content-Type": "application/json" },
      body: JSON.stringify({
        name: organisationName,
        user_name: userName,
        email: userEmail,
        password: userPassword,
      }),
    });

    if (!response.ok) {
      throw new Error(`Sign-up failed with status ${response.status}`);
    }
    console.log("Sign-up successful");

    let { token, user } = await res.json();
    localStorage.setItem("authToken", token);
    localStorage.setItem("userData", JSON.stringify(user));
    navigate("/dashboard");
  } catch (error) {
    console.error("Sign-up failed:", error);
  }
};
```

- **Feedback**
  1. Consider maintaining consistency in naming conventions i.e., use of camelCase convention for function names in JavaScript.
  2. Before making API call consider validating the input data so that unnecessary API calls are prevented.
  3. Consider using `const` for variables and do not use destructuring for the response.
  4. Consider assigning the input data to a variable.

# Updated Code

```javascript
// Maintain consistency in naming conventions
const handleSubmit = async (event: React.FormEvent<HTMLFormElement>) => {
  event.preventDefault();

  try {
    // Assign input data to a variable
    const formData = {
      name: organisationName,
      user_name: userName,
      email: userEmail,
      password: userPassword,
    };

    // Input Validation
    if (
      !formData.name ||
      !formData.user_name ||
      !formData.email ||
      !formData.password
    ) {
      console.error("One or more required fields are empty");
      return;
    }

    const response = await fetch(`${API_ENDPOINT}/organisations`, {
      method: "POST",
      headers: { "Content-Type": "application/json" },
      body: JSON.stringify(formData),
    });

    if (!response.ok) {
      throw new Error(`Sign-up failed with status ${response.status}`);
    }
    console.log("Sign-up successful");

    // Use const for variables, and avoid destructuring for response
    const data = await response.json();
    localStorage.setItem("authToken", data.token);
    localStorage.setItem("userData", JSON.stringify(data.user));
    navigate("/dashboard");
  } catch (error) {
    console.error("Sign-up failed:", error);
  }
};
```

# Iterative Development Process  
<img width="802" height="1000" alt="L1-flowchart" src="https://github.com/Gopal-379/WD401/assets/83073228/13299eb9-d7f7-41b2-b7c7-daed4162069d">

# Resolving Merge Conflicts

Merge conflicts occur when multiple branches have changes that conflict with each other, typically when changes are made to the same part of a file by different contributors.

- **Scenario:** Developers have modified handleSubmit function to include additional error handling to specific HTTP status codes.

#### Code in Branch A of Developer A

```javascript
const handleSubmit = async (event: React.FormEvent<HTMLFormElement>) => {
  event.preventDefault();

  try {
    const formData = {
      name: organisationName,
      user_name: userName,
      email: userEmail,
      password: userPassword,
    };

    const response = await fetch(`${API_ENDPOINT}/organisations`, {
      method: "POST",
      headers: { "Content-Type": "application/json" },
      body: JSON.stringify(formData),
    });

    if (!response.ok) {
      throw new Error(`Sign-up failed with status ${response.status}`);
    }

    console.log("Sign-up successful");

    const data = await response.json();
    localStorage.setItem("authToken", data.token);
    localStorage.setItem("userData", JSON.stringify(data.user));
    navigate("/dashboard");
  } catch (error) {
    console.error("Error occurred during sign-up:", error);
  }
};
```

#### Code in Branch B of Developer B

```javascript
const handleSubmit = async (event: React.FormEvent<HTMLFormElement>) => {
  event.preventDefault();

  try {
    const formData = {
      name: organisationName,
      user_name: userName,
      email: userEmail,
      password: userPassword,
    };

    const response = await fetch(`${API_ENDPOINT}/organisations`, {
      method: "POST",
      headers: { "Content-Type": "application/json" },
      body: JSON.stringify(formData),
    });

    if (!response.ok) {
      if (response.status === 401) {
        throw new Error("Unauthorized access. Please log in.");
      } else {
        throw new Error(`Sign-up failed with status ${response.status}`);
      }
    }

    console.log("Sign-up successful");

    const data = await response.json();
    localStorage.setItem("authToken", data.token);
    localStorage.setItem("userData", JSON.stringify(data.user));
    navigate("/dashboard");
  } catch (error) {
    console.error("Error occurred during sign-up:", error);
  }
};
```

When Developer B merges his branch i.e., Branch B, the following conflict occurs in branch A:

```javascript
const handleSubmit = async (event: React.FormEvent<HTMLFormElement>) => {
    event.preventDefault();

    try {
        const formData = {
            name: organisationName,
            user_name: userName,
            email: userEmail,
            password: userPassword,
        };

        const response = await fetch(`${API_ENDPOINT}/organisations`, {
            method: "POST",
            headers: { "Content-Type": "application/json" },
            body: JSON.stringify(formData),
        });
<<<<<<< HEAD
        if (!response.ok) {
            throw new Error(`Sign-up failed with status ${response.status}`);
        }
=======
        if (!response.ok) {
            if (response.status === 401) {
                throw new Error("Unauthorized access. Please log in.");
            } else {
                throw new Error(`Sign-up failed with status ${response.status}`);
            }
        }
>>>>>>> Branch B

        console.log("Sign-up successful");

        const data = await response.json();
        localStorage.setItem("authToken", data.token);
        localStorage.setItem("userData", JSON.stringify(data.user));
        navigate("/dashboard");
    } catch (error) {
        console.error("Error occurred during sign-up:", error);
    }
};
```

_The following conflict is resolved either by accepting current branch changes or by accepting incoming changes from other branch or accept both changes._

# CI/CD Integration

Tools such as jest, eslint, prettier, husky and lint-staged are employed to maintain code quality and adhere to coding standards. They are seamlessly integrated into the CI/CD pipeline to guarantee that code undergoes testing and linting prior to being merged into the main branch.

<p>Here's an example of an automated unit test that executes whenever changes are committed to Git and are located within the __tests__ directory.</p>

```javascript
test("Test for Signup", () => {
    let response = await agent.get("/signup");
    const csrfToken = extractCsrfToken(response);
    response = await agent.post("/users").send({
        name: "john",
        email: "john@example.com",
        password: "12345678",
        _csrf: csrfToken,
    });
    expect(response.statusCode).toBe(302);
});
```

### Potential Issues and Troubleshooting

- If tests fail, Husky prevents the commit, indicating that there are failing tests.
- Troubleshoot by reviewing test results to identify and fix issues before committing changes.
- If linting encounters failures, it is essential to review the ESLint configuration and address any linting errors and warnings before proceeding with the commit.
