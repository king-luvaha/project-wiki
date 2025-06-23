- Use GitHub API to fetch user activity and display it in the terminal.

In this project, you will build a simple command line interface (CLI) to fetch the recent activity of a GitHub user and display it in the terminal.

## Requirements

The application should run from the command line, accept the GitHub username as an argument, fetch the user’s recent activity using the GitHub API, and display it in the terminal. The user should be able to:

- Provide the GitHub username as an argument when running the CLI.

```
github-activity <username>
```

- Fetch the recent activity of the specified GitHub user using the GitHub API. You can use the following endpoint to fetch the user’s activity:

```
# https://api.github.com/users/<username>/events
# Example: https://api.github.com/users/kamranahmedse/events
```

- Display the fetched activity in the terminal.

```
- Pushed 3 commits to kamranahmedse/developer-roadmap
- Opened a new issue in kamranahmedse/developer-roadmap
- Starred kamranahmedse/developer-roadmap
```

Some constraints to guide the implementation:

--> Do not use any external libraries or frameworks to fetch the GitHub activity.
--> Handle errors gracefully, such as invalid usernames or API failures.

### Solution

[[Step by Step Guide to Building a GitHub User Activity]]

[[Build a GitHub User Activity CLI Application - Explained Line by Line]]

