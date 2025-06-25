
Have you ever wanted to create your own blog application but didn't know where to start? In this comprehensive tutorial, we'll build a fully functional blog application using Node.js, Express, and EJS templates. By the end of this guide, you'll have a complete blog with both public and admin interfaces.

## What We're Building

Our blog application will have:

- A public homepage showing all articles
- Individual article pages for detailed reading
- An admin dashboard for managing articles
- Create, edit, and delete functionality for articles
- File-based storage (no database required!)
- Basic authentication for admin access

## Prerequisites

Before we begin, make sure you have:

- Node.js installed (version 14 or higher)
- Basic knowledge of HTML, CSS, and JavaScript
- Familiarity with the command line

## Step 1: Project Setup and Initialization

Let's start by creating our project structure and initializing our Node.js application.

### 1.1 Create the Project Directory

```bash
mkdir personal-blog
cd personal-blog
```

### 1.2 Initialize the Node.js Project

```bash
npm init -y
```

This creates a `package.json` file with default settings.

### 1.3 Install Required Dependencies

```bash
npm install express ejs basic-auth body-parser
```

Let's understand what each dependency does:

- **express**: Web framework for Node.js
- **ejs**: Templating engine for dynamic HTML
- **basic-auth**: Simple HTTP authentication
- **body-parser**: Middleware to parse form data

### 1.4 Create the Project Structure

```bash
mkdir public public/css public/images
mkdir views views/guest views/admin
mkdir articles
```

Your project structure should now look like this:

```
/blog-project/
├── public/
│   ├── css/
│   │   └── style.css          # Main stylesheet
│   └── images/                # Static images directory
├── views/
│   ├── guest/
│   │   ├── home.ejs          # Public homepage
│   │   └── article.ejs       # Individual article view
│   └── admin/
│       ├── dashboard.ejs     # Admin dashboard
│       ├── add-article.ejs   # Add new article form
│       └── edit-article.ejs  # Edit article form
├── articles/                  # JSON files for articles (auto-created)
├── app.js                    # Main application file
├── package.json              # Node.js dependencies
└── .gitignore               # Git ignore file
```

## Step 2: Creating the Main Application File

Now let's create our main application file `app.js`. This is the heart of our blog application.

### 2.1 Basic Express Setup

Create `app.js` and start with the basic Express setup:

```javascript
const express = require('express');
const fs = require('fs').promises;
const path = require('path');
const auth = require('basic-auth');
const bodyParser = require('body-parser');

const app = express();
const PORT = process.env.PORT || 3000;
```

**Why this setup?**

- We import all necessary modules
- `fs.promises` gives us modern async/await file operations
- `path` helps with file system paths
- We set a default port of 3000

### 2.2 Configure Express Middleware

Add the following configuration:

```javascript
// Configure app
app.set('view engine', 'ejs');
app.set('views', path.join(__dirname, 'views'));
app.use(express.static(path.join(__dirname, 'public')));
app.use(bodyParser.urlencoded({ extended: true }));
```

**What each line does:**

- `app.set('view engine', 'ejs')`: Tells Express to use EJS for templates
- `app.set('views', ...)`: Sets the directory where templates are stored
- `express.static(...)`: Serves static files (CSS, images) from the public folder
- `bodyParser.urlencoded(...)`: Parses form data from POST requests

### 2.3 Set Up Articles Directory

```javascript
// Articles directory
const ARTICLES_DIR = path.join(__dirname, 'articles');

// Ensure articles directory exists
(async () => {
  try {
    await fs.mkdir(ARTICLES_DIR, { recursive: true });
  } catch (err) {
    console.error('Error creating articles directory:', err);
  }
})();
```

**Why this is important:**

- We define where articles will be stored
- The async function ensures the directory exists when the app starts
- `recursive: true` creates parent directories if they don't exist

### 2.4 Create Authentication Middleware

```javascript
// Basic authentication middleware for admin routes
const adminAuth = (req, res, next) => {
  const credentials = auth(req);
  
  // Replace with your actual username and password
  const validUsername = 'admin';
  const validPassword = 'password123';
  
  if (!credentials || 
      credentials.name !== validUsername || 
      credentials.pass !== validPassword) {
    res.set('WWW-Authenticate', 'Basic realm="Admin Access"');
    return res.status(401).send('Authentication required');
  }
  
  next();
};
```

**How authentication works:**

- The middleware extracts credentials from the request
- It compares them with our predefined username/password
- If invalid, it sends a 401 Unauthorized response
- If valid, it calls `next()` to continue to the route handler

## Step 3: Creating Guest Routes (Public Interface)

Now let's create the routes that regular visitors will use.

### 3.1 Homepage Route

```javascript
// Guest Routes
app.get('/', async (req, res) => {
  try {
    const files = await fs.readdir(ARTICLES_DIR);
    const articles = await Promise.all(
      files.map(async file => {
        const content = await fs.readFile(path.join(ARTICLES_DIR, file), 'utf8');
        return JSON.parse(content);
      })
    );
    res.render('guest/home', { articles });
  } catch (err) {
    console.error(err);
    res.status(500).send('Error loading articles');
  }
});
```

**Step-by-step breakdown:**

1. Read all files from the articles directory
2. For each file, read its content and parse it as JSON
3. `Promise.all()` waits for all file reads to complete
4. Render the home template with the articles data
5. Handle errors gracefully with try-catch

### 3.2 Individual Article Route

```javascript
app.get('/article/:id', async (req, res) => {
  try {
    const filePath = path.join(ARTICLES_DIR, `${req.params.id}.json`);
    const content = await fs.readFile(filePath, 'utf8');
    const article = JSON.parse(content);
    res.render('guest/article', { article });
  } catch (err) {
    console.error(err);
    res.status(404).send('Article not found');
  }
});
```

**How it works:**

- `:id` is a route parameter that captures the article ID from the URL
- We construct the file path using the ID
- Read and parse the specific article file
- Render the article template with the article data
- Return 404 if the article doesn't exist

## Step 4: Creating Admin Routes

The admin routes handle article management functionality.

### 4.1 Admin Dashboard

```javascript
// Admin Routes
app.get('/admin', adminAuth, async (req, res) => {
  try {
    const files = await fs.readdir(ARTICLES_DIR);
    const articles = await Promise.all(
      files.map(async file => {
        const content = await fs.readFile(path.join(ARTICLES_DIR, file), 'utf8');
        return { ...JSON.parse(content), id: path.basename(file, '.json') };
      })
    );
    res.render('admin/dashboard', { articles });
  } catch (err) {
    console.error(err);
    res.status(500).send('Error loading articles');
  }
});
```

**Key differences from guest route:**

- Uses `adminAuth` middleware for protection
- Adds the `id` field to each article (extracted from filename)
- Renders the admin dashboard template

### 4.2 Add Article Routes

```javascript
app.get('/admin/add', adminAuth, (req, res) => {
  res.render('admin/add-article');
});

app.post('/admin/add', adminAuth, async (req, res) => {
  try {
    const { title, content } = req.body;
    const id = Date.now().toString();
    const date = new Date().toISOString();
    
    const article = { id, title, content, date };
    await fs.writeFile(
      path.join(ARTICLES_DIR, `${id}.json`),
      JSON.stringify(article, null, 2)
    );
    
    res.redirect('/admin');
  } catch (err) {
    console.error(err);
    res.status(500).send('Error saving article');
  }
});
```

**How article creation works:**

1. GET route renders the add article form
2. POST route processes the form submission
3. Extract title and content from the form data
4. Generate a unique ID using current timestamp
5. Create article object with all necessary fields
6. Save as JSON file with formatted indentation
7. Redirect back to admin dashboard

**Why use timestamp as ID?**

- Guarantees uniqueness
- Provides chronological ordering
- Simple to implement

### 4.3 Edit Article Routes

```javascript
app.get('/admin/edit/:id', adminAuth, async (req, res) => {
  try {
    const filePath = path.join(ARTICLES_DIR, `${req.params.id}.json`);
    const content = await fs.readFile(filePath, 'utf8');
    const article = JSON.parse(content);
    res.render('admin/edit-article', { article });
  } catch (err) {
    console.error(err);
    res.status(404).send('Article not found');
  }
});

app.post('/admin/edit/:id', adminAuth, async (req, res) => {
  try {
    const { title, content } = req.body;
    const filePath = path.join(ARTICLES_DIR, `${req.params.id}.json`);
    const existingContent = await fs.readFile(filePath, 'utf8');
    const existingArticle = JSON.parse(existingContent);
    
    const updatedArticle = {
      ...existingArticle,
      title,
      content,
      date: existingArticle.date // Keep original date
    };
    
    await fs.writeFile(filePath, JSON.stringify(updatedArticle, null, 2));
    res.redirect('/admin');
  } catch (err) {
    console.error(err);
    res.status(500).send('Error updating article');
  }
});
```

**Edit logic explained:**

1. GET route loads existing article data for the form
2. POST route processes the update
3. We preserve the original date while updating title and content
4. Use spread operator to maintain all existing fields
5. Save the updated article back to the file

### 4.4 Delete Article Route

```javascript
app.post('/admin/delete/:id', adminAuth, async (req, res) => {
  try {
    await fs.unlink(path.join(ARTICLES_DIR, `${req.params.id}.json`));
    res.redirect('/admin');
  } catch (err) {
    console.error(err);
    res.status(500).send('Error deleting article');
  }
});
```

**Delete functionality:**

- Uses POST method for security (prevents accidental deletion via GET)
- `fs.unlink()` removes the file from the filesystem
- Redirects back to dashboard after successful deletion

### 4.5 Start the Server

```javascript
// Start server
app.listen(PORT, () => {
  console.log(`Server running on http://localhost:${PORT}`);
});
```

## Step 5: Creating the Guest Templates

Now let's create the EJS templates for the public interface.

### 5.1 Homepage Template (views/guest/home.ejs)

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>My Personal Blog</title>
  <link rel="stylesheet" href="/css/style.css">
</head>
<body>
  <header>
    <h1>Welcome to My Blog</h1>
  </header>
  
  <main>
    <section class="articles-list">
      <h2>Latest Articles</h2>
      
      <% if (articles.length === 0) { %>
        <p>No articles yet. Check back soon!</p>
      <% } else { %>
        <ul>
          <% articles.forEach(article => { %>
            <li>
              <h3><a href="/article/<%= article.id %>"><%= article.title %></a></h3>
              <p class="date">Published on: <%= new Date(article.date).toLocaleDateString() %></p>
            </li>
          <% }) %>
        </ul>
      <% } %>
    </section>
  </main>
  
  <footer>
    <p>&copy; <%= new Date().getFullYear() %> My Personal Blog</p>
  </footer>
</body>
</html>
```

**EJS Template Features:**

- `<% %>` for JavaScript logic (if statements, loops)
- `<%= %>` for outputting variables (HTML-escaped)
- Conditional rendering for empty articles list
- Dynamic link generation for each article
- Date formatting using JavaScript Date methods

### 5.2 Article Page Template (views/guest/article.ejs)

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title><%= article.title %> | My Personal Blog</title>
  <link rel="stylesheet" href="/css/style.css">
</head>
<body>
  <header>
    <h1><%= article.title %></h1>
    <p class="date">Published on: <%= new Date(article.date).toLocaleDateString() %></p>
  </header>
  
  <main>
    <article>
      <%- article.content %>
    </article>
    
    <p><a href="/">← Back to all articles</a></p>
  </main>
  
  <footer>
    <p>&copy; <%= new Date().getFullYear() %> My Personal Blog</p>
  </footer>
</body>
</html>
```

**Important note:** We use `<%-` for article content to render HTML without escaping, allowing rich text formatting.

## Step 6: Creating Admin Templates

### 6.1 Admin Dashboard (views/admin/dashboard.ejs)

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Admin Dashboard</title>
  <link rel="stylesheet" href="/css/style.css">
</head>
<body>
  <header>
    <h1>Admin Dashboard</h1>
    <nav>
      <a href="/admin/add">Add New Article</a> |
      <a href="/">View Public Site</a>
    </nav>
  </header>
  
  <main>
    <h2>Manage Articles</h2>
    
    <% if (articles.length === 0) { %>
      <p>No articles yet. <a href="/admin/add">Add your first article</a></p>
    <% } else { %>
      <table>
        <thead>
          <tr>
            <th>Title</th>
            <th>Date</th>
            <th>Actions</th>
          </tr>
        </thead>
        <tbody>
          <% articles.forEach(article => { %>
            <tr>
              <td><%= article.title %></td>
              <td><%= new Date(article.date).toLocaleDateString() %></td>
              <td class="actions">
                <a href="/admin/edit/<%= article.id %>">Edit</a>
                <form action="/admin/delete/<%= article.id %>" method="POST" style="display: inline;">
                  <button type="submit">Delete</button>
                </form>
              </td>
            </tr>
          <% }) %>
        </tbody>
      </table>
    <% } %>
  </main>
  
  <footer>
    <p>&copy; <%= new Date().getFullYear() %> My Personal Blog</p>
  </footer>
</body>
</html>
```

**Dashboard Features:**

- Table layout for easy article management
- Navigation links to add articles and view public site
- Inline delete form for each article
- Responsive design considerations

### 6.2 Add Article Form (views/admin/add-article.ejs)

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Add New Article</title>
  <link rel="stylesheet" href="/css/style.css">
</head>
<body>
  <header>
    <h1>Add New Article</h1>
    <nav>
      <a href="/admin">← Back to Dashboard</a>
    </nav>
  </header>
  
  <main>
    <form action="/admin/add" method="POST">
      <div class="form-group">
        <label for="title">Title:</label>
        <input type="text" id="title" name="title" required>
      </div>
      
      <div class="form-group">
        <label for="content">Content:</label>
        <textarea id="content" name="content" rows="10" required></textarea>
      </div>
      
      <button type="submit">Publish Article</button>
    </form>
  </main>
  
  <footer>
    <p>&copy; <%= new Date().getFullYear() %> My Personal Blog</p>
  </footer>
</body>
</html>
```

**Form Best Practices:**

- Proper labels for accessibility
- Required fields for validation
- Adequate textarea size for content
- Clear navigation back to dashboard

### 6.3 Edit Article Form (views/admin/edit-article.ejs)

```html
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Edit Article</title>
  <link rel="stylesheet" href="/css/style.css">
</head>
<body>
  <header>
    <h1>Edit Article</h1>
    <nav>
      <a href="/admin">← Back to Dashboard</a>
    </nav>
  </header>
  
  <main>
    <form action="/admin/edit/<%= article.id %>" method="POST">
      <div class="form-group">
        <label for="title">Title:</label>
        <input type="text" id="title" name="title" value="<%= article.title %>" required>
      </div>
      
      <div class="form-group">
        <label for="content">Content:</label>
        <textarea id="content" name="content" rows="10" required><%= article.content %></textarea>
      </div>
      
      <button type="submit">Update Article</button>
    </form>
  </main>
  
  <footer>
    <p>&copy; <%= new Date().getFullYear() %> My Personal Blog</p>
  </footer>
</body>
</html>
```

**Edit Form Features:**

- Pre-populated fields with existing article data
- Dynamic form action URL with article ID
- Same validation as add form

## Step 7: Styling with CSS

Create `public/css/style.css`:

```css
/* Basic Reset */
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

body {
  font-family: 'Segoe UI', Tahoma, Geneva, Verdana, sans-serif;
  line-height: 1.6;
  color: #333;
  max-width: 1200px;
  margin: 0 auto;
  padding: 20px;
}

header {
  margin-bottom: 30px;
  padding-bottom: 20px;
  border-bottom: 1px solid #eee;
}

h1, h2, h3 {
  margin-bottom: 15px;
  color: #2c3e50;
}

.date {
  color: #7f8c8d;
  font-size: 0.9em;
  margin-bottom: 15px;
}

.articles-list ul {
  list-style: none;
}

.articles-list li {
  margin-bottom: 20px;
  padding-bottom: 20px;
  border-bottom: 1px solid #eee;
}

article {
  margin-bottom: 30px;
}

footer {
  margin-top: 50px;
  padding-top: 20px;
  border-top: 1px solid #eee;
  text-align: center;
  color: #7f8c8d;
  font-size: 0.9em;
}

/* Admin Styles */
table {
  width: 100%;
  border-collapse: collapse;
  margin-top: 20px;
}

table th, table td {
  padding: 10px;
  text-align: left;
  border-bottom: 1px solid #eee;
}

table th {
  background-color: #f5f5f5;
}

.actions a {
  margin-right: 10px;
  color: #3498db;
  text-decoration: none;
}

.actions button {
  background: none;
  border: none;
  color: #e74c3c;
  cursor: pointer;
  font-size: 1em;
}

/* Form Styles */
.form-group {
  margin-bottom: 20px;
}

.form-group label {
  display: block;
  margin-bottom: 5px;
  font-weight: bold;
}

.form-group input,
.form-group textarea {
  width: 100%;
  padding: 10px;
  border: 1px solid #ddd;
  border-radius: 4px;
  font-family: inherit;
  font-size: 1em;
}

.form-group textarea {
  min-height: 300px;
}

button[type="submit"] {
  background-color: #2ecc71;
  color: white;
  border: none;
  padding: 10px 20px;
  border-radius: 4px;
  cursor: pointer;
  font-size: 1em;
}

button[type="submit"]:hover {
  background-color: #27ae60;
}

nav a {
  color: #3498db;
  text-decoration: none;
  margin-right: 15px;
}

nav a:hover {
  text-decoration: underline;
}
```

**CSS Organization:**

- Global reset and typography
- Layout and spacing
- Component-specific styles (tables, forms)
- Hover effects for better user experience
- Responsive design principles

## Step 8: Testing Your Blog

### 8.1 Start the Application

```bash
node app.js
```

### 8.2 Test Public Interface

1. Visit `http://localhost:3000`
2. You should see the homepage with "No articles yet" message
3. Check that the styling is applied correctly

### 8.3 Test Admin Interface

1. Visit `http://localhost:3000/admin`
2. Enter credentials: username `admin`, password `password123`
3. You should see the admin dashboard
4. Try adding a new article
5. Test editing and deleting articles

## Step 9: Understanding the File Structure

### 9.1 How Articles Are Stored

Each article is saved as a JSON file in the `articles/` directory:

```json
{
  "id": "1640995200000",
  "title": "My First Blog Post",
  "content": "<p>This is the content of my first blog post.</p>",
  "date": "2023-12-31T12:00:00.000Z"
}
```

**Benefits of file-based storage:**

- No database setup required
- Easy to backup (just copy the articles folder)
- Human-readable format
- Version control friendly

### 9.2 Request Flow

1. **Public Request**: Browser → Express → Read articles → Render template → Send HTML
2. **Admin Request**: Browser → Express → Authentication → Process request → Redirect/Render

## Step 10: Enhancements and Next Steps

### 10.1 Security Improvements

1. **Change default credentials**:
    
    ```javascript
    const validUsername = 'your-secure-username';
    const validPassword = 'your-secure-password';
    ```
    
2. **Add input validation**:
    
    ```javascript
    if (!title || !content) {
      return res.status(400).send('Title and content are required');
    }
    ```
    
3. **Sanitize HTML content** (if needed):
    
    ```bash
    npm install dompurify jsdom
    ```
    

### 10.2 Feature Additions

1. **Article categories**
2. **Search functionality**
3. **Comments system**
4. **Image upload**
5. **Rich text editor**

### 10.3 Deployment Options

1. **Heroku**: Simple deployment with Git
2. **DigitalOcean**: VPS hosting
3. **Netlify**: Static site hosting (with build process)
4. **Railway**: Modern deployment platform

## Conclusion

Congratulations! You've built a complete blog application from scratch. This project demonstrates:

- **Express.js fundamentals**: Routing, middleware, static files
- **Template rendering**: Dynamic HTML generation with EJS
- **File system operations**: Reading and writing JSON files
- **Authentication**: Basic HTTP authentication
- **CRUD operations**: Create, Read, Update, Delete functionality
- **Form handling**: Processing user input
- **CSS styling**: Creating a professional appearance

### Key Learning Points

1. **Separation of concerns**: Templates, styles, and logic are separated
2. **Error handling**: Proper try-catch blocks and user feedback
3. **Security basics**: Authentication and input validation
4. **File organization**: Logical project structure
5. **User experience**: Intuitive navigation and feedback

### What's Next?

Now that you understand the fundamentals, you can:

- Add more advanced features
- Migrate to a database (MongoDB, PostgreSQL)
- Implement user registration and multiple authors
- Add API endpoints for mobile apps
- Deploy to production

Remember, this is just the beginning of your web development journey. Each concept you've learned here applies to larger, more complex applications. Keep building, keep learning, and most importantly, keep coding!

---

_This tutorial provides a solid foundation for web development with Node.js and Express. Feel free to customize and extend the application based on your specific needs and creativity._