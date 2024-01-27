# Handling Code Review Feedback:

```ts
const handlesubmit = async (e) => {
  e.preventDefault();

  try {
    const res = await fetch(`${API_ENDPOINT}/organisations`, {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify({ name: organisationName, user_name: userName, email: userEmail, password: userPassword }),
    });

    if (!res.ok) {
      throw new Error(`Sign-up failed with status ${res.status}`);
    }
    console.log('Sign-up successful');

    let { token, user } = await res.json();
    localStorage.setItem('authToken', token);
    localStorage.setItem('userData', JSON.stringify(user));
    navigate("/dashboard");
  } catch (err) {
    console.error('Sign-up failed:', err);
  }
};
```

# Code Review:

```ts
// Use camelCase for variable names
const handleSubmit = async (event: React.FormEvent<HTMLFormElement>) => {
  event.preventDefault();

  try {
    // Assign the payload to a variable
    const payload = { name: organisationName, user_name: userName, email: userEmail, password: userPassword };

    // change the variable name to response. This is a better way to follow coding standards.
    const response = await fetch(`${API_ENDPOINT}/organisations`, {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify(payload),
    });

    if (!response.ok) {
      throw new Error(`Sign-up failed with status ${response.status}`);
    }

    console.log('Sign-up successful');

    // Use const for variable names and do not use destructuring for the response.
    const data = await response.json();

    localStorage.setItem(authTokenKey, data.token);
    localStorage.setItem(userDataKey, JSON.stringify(data.user));

    navigate("/dashboard");
    // change the variable name to error to follow coding standards.
  } catch (error) {
    console.error('Sign-up failed:', error);
  }
};
```

# Iterative Development Process:

<img width="802" alt="L1-flowchart" src="https://github.com/sriharsh05/wd401/assets/114745442/b4cc0666-5f88-4c1a-897e-16ab4d642894">


# Resolving Merge Conflicts:

A merge conflict occurs when two branches both modify the same region of a file and are subsequently merged. Git can't know which of the changes to keep, and thus needs human intervention to resolve the conflict.

## Code in branch A:

```ts
const handleSubmit = async (event: React.FormEvent<HTMLFormElement>) => {
  event.preventDefault();

  try {
    const payload = { name: organisationName, user_name: userName, email: userEmail, password: userPassword };

    const response = await fetch(`${API_ENDPOINT}/organisations`, {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify(payload),
    });

    if (!response.ok) {
      throw new Error(`Sign-up failed with status ${response.status}`);
    }

    console.log('Sign-up successful');

    const data = await response.json();

    localStorage.setItem(authTokenKey, data.token);
    localStorage.setItem(userDataKey, JSON.stringify(data.user));

    navigate("/dashboard");
  } catch (error) {
    console.error('Sign-up failed:', error);
  }
};
```
## Code in branch B:

```ts
const handleSubmit = async (event: React.FormEvent<HTMLFormElement>) => {
  event.preventDefault();

  try {
    const payload = { name: organisationName, user_name: userName, email: userEmail, password: userPassword };

    const response = await fetch(`${API_ENDPOINT}/organisations`, {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify(payload),
    });

    if (res.status === 401 || res.status === 403) {
      throw new Error("Sign-up failed");
    }

    console.log('Sign-up successful');

    const data = await response.json();

    localStorage.setItem(authTokenKey, data.token);
    localStorage.setItem(userDataKey, JSON.stringify(data.user));

    navigate("/dashboard");
  } catch (error) {
    console.error('Sign-up failed:', error);
  }
};
```
When branch B are merged to develop, the following conflict occurs in branch A:

```ts 
const handleSubmit = async (event: React.FormEvent<HTMLFormElement>) => {
  event.preventDefault();

  try {
    const payload = { name: organisationName, user_name: userName, email: userEmail, password: userPassword };

    const response = await fetch(`${API_ENDPOINT}/organisations`, {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify(payload),
    });

<<<<<<< HEAD
    if (!response.ok) {
      throw new Error(`Sign-up failed with status ${response.status}`);
    }
=======
    if (res.status === 401 || res.status === 403) {
      throw new Error("Sign-up failed");
    }
>>>>>>> Branch B/
    console.log('Sign-up successful');

    const data = await response.json();

    localStorage.setItem(authTokenKey, data.token);
    localStorage.setItem(userDataKey, JSON.stringify(data.user));

    navigate("/dashboard");
  } catch (error) {
    console.error('Sign-up failed:', error);
  }
};
```

The conflict is resolved by either by accepting the incoming changes branch B or by keeping the current changes in branch A or by merging both the changes.

After the conflict is resolved. The code must be tested to ensure that the code is working as expected. Then the code is committed and pushed to the develop branch.

# CI/CD Integration:

Packages like jest, eslint, prettier, husky, lint-staged, commitlint, and commitizen are used to ensure that the code is of high quality and follows the coding standards. These packages are integrated with the CI/CD pipeline to ensure that the code is tested and linted before it is merged to the develop branch.

 Here are some of the tests for sign-in and sign-up components:

 ```ts
    test("Signup for admin", async () => {
      let res = await agent.get("/signup");
      const csrfToken = extractCsrfToken(res);
      res = await agent.post("/users").send({
        firstName: "john",
        lastName: "doe",
        email: "johndoe@gmail.com",
        password: "12345678",
        _csrf: csrfToken,
      });
      expect(res.statusCode).toBe(302);
  });

  const login = async (agent, username, password) => {
    let res = await agent.get("/login");
    let csrfToken = extractCsrfToken(res);
    res = await agent.post("/session").send({
      email: username,
      password: password,
      _csrf: csrfToken,
    });
};

  test("Sign out", async () => {
    let res = await agent.get("/hom");
    expect(res.statusCode).toBe(200);
    res = await agent.get("/signout");
    expect(res.statusCode).toBe(302);
    res = await agent.get("/home");
    expect(res.statusCode).toBe(302);
  });
  ```
All these tests are run in the CI/CD pipeline to ensure that the code is working as expected. Along with these tests, the code is also linted to ensure that the code follows the coding standards. If the code fails any of these tests, the code is not merged to the develop branch.

