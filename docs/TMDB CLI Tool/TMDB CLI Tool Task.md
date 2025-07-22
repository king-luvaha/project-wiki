- Use TMDB API to fetch movie information and display it in the terminal.

In this project, you will build a simple command line interface (CLI) to fetch data from The Movie Database (TMDB) and display it in the terminal. This project will help you practice your programming skills, including working with APIs, handling JSON data, and building a simple CLI application.

## Requirements

The application should run from the command line, and be able to pull and show the popular, top-rated, upcoming and now playing movies from the TMDB API. The user should be able to specify the type of movies they want to see by passing a command line argument to the CLI tool.

Here is how the CLI tool usage should look like:

```bash
tmdb-app --type "playing"
tmdb-app --type "popular"
tmdb-app --type "top"
tmdb-app --type "upcoming"
```

There are some considerations to keep in mind:

- Handle errors gracefully, such as API failures or network issues.
- Use a programming language of your choice to build this project.
- Make sure to include a README file with instructions on how to run the application and any other relevant information.