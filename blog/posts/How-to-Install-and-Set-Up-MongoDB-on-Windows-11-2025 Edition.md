---
slug: how-to-install-and-set-up-mongodb-on-windows-11-2025-edition
title: How to Install and Set Up MongoDB on Windows 11 (2025 Edition)
authors: luvaha
tags:
  - MongoDB
date: 2025-08-08
---
---

MongoDB is one of the most popular NoSQL databases used in modern applications. Whether you're a backend developer, full-stack engineer, or just exploring databases, learning how to install MongoDB on Windows 11 is a solid first step.

In this guide, you'll learn how to:
- ✅ Download and install the latest version of MongoDB
- ✅ Install MongoDB Compass (the GUI)
- ✅ Install the MongoDB Shell (`mongosh`)
- ✅ Verify everything is working perfectly

---

## 📦 Step 1: Download MongoDB for Windows 11

1. Visit the [MongoDB Download Center](https://www.mongodb.com/try/download/community).
2. Select the following options:
   * **Version:** Latest (e.g., 7.0+)
   * **OS:** Windows
   * **Package:** MSI
3. Click **Download** and wait for the `.msi` file to finish downloading.

---

## ⚙️ Step 2: Install MongoDB

1. Double-click the `.msi` installer to launch the setup wizard.
2. Select **Complete** installation.
3. On the "Service Configuration" screen:
   * Choose **Run MongoDB as a Service**.
   * Leave defaults (`Network Service user`, port 27017).
4. On the "Data Directory" screen:
   * Default is usually: `C:\Program Files\MongoDB\Server\<version>\`
5. Finish installation.

✅ MongoDB is now installed on your machine.

---

## 🧪 Step 3: Install MongoDB Shell (mongosh)

Starting from MongoDB 6.0+, the legacy `mongo` shell has been replaced with `mongosh`.

1. Download the standalone **MongoDB Shell (mongosh)** from:
   👉 [https://www.mongodb.com/try/download/shell](https://www.mongodb.com/try/download/shell)
2. Choose:
   * OS: Windows
   * Package: Zip Archive
3. Extract the zip file (e.g., to `C:\mongosh`) and optionally add it to your system `PATH`:
   * Open **System Properties** > **Environment Variables**
   * Under **System variables**, find `Path`, click **Edit**, then **New**, and paste the path to the extracted `mongosh.exe`

Now you can open a terminal (Command Prompt or PowerShell) and run:

```bash
mongosh
```

---

## 💻 Step 4: Install MongoDB Compass

MongoDB Compass is the official GUI for interacting with your MongoDB database visually.

1. Visit: [https://www.mongodb.com/try/download/compass](https://www.mongodb.com/try/download/compass)
2. Choose the **latest version** and download the `.exe` installer.
3. Run the installer and follow the default steps.

Once installed, launch Compass and connect to:

```
mongodb://localhost:27017
```

You should see your MongoDB instance running.

---

## ✅ Step 5: Verify Your Installation

Let’s test everything to ensure MongoDB is up and running:

### 🔹 1. Check MongoDB Service Status

Open **Command Prompt** and run:

```bash
sc query MongoDB
```

You should see `RUNNING` under `STATE`.

### 🔹 2. Connect via MongoDB Shell

Run:

```bash
mongosh
```

Expected output:

```
Current Mongosh Log ID...
Connecting to:        mongodb://127.0.0.1:27017/
Using MongoDB:        7.x.x
```

Then test a basic command:

```js
show dbs
```

You should see default databases like `admin`, `config`, and `local`.

### 🔹 3. Connect via MongoDB Compass

* Launch Compass
* Use connection string: `mongodb://localhost:27017`
* Click **Connect**
* You should see your databases listed.

---

## 🧹 Optional: Create a Test Database

In `mongosh`, run:

```js
use blogTestDB
db.articles.insertOne({ title: "Hello MongoDB", created_at: new Date() })
db.articles.find()
```

You should see your inserted document.

---

## 🎉 Conclusion

You’ve successfully installed MongoDB, MongoDB Compass, and mongosh on your Windows 11 system! With this setup, you're ready to build modern, data-driven applications using a powerful NoSQL database.

Stay tuned for more guides on how to:

* Create schemas in MongoDB
* Connect MongoDB with Node.js or Python
* Host MongoDB in the cloud with Atlas

---
