# 🛠️ MySQL Port Issue Fix (Data Recovery Script)

When working with MySQL (especially in local environments like XAMPP/WAMP), you may encounter a **MySQL port or startup issue**. This often happens due to corrupted data files, improper shutdowns, or conflicts with existing database files.

This guide provides a **safe recovery method** using a PowerShell script that rebuilds the MySQL data directory while preserving your existing databases.

---

## 📌 What This Script Does

* 🔁 Backs up your existing MySQL data folder
* 📁 Restores a fresh data directory from backup
* 🧹 Removes unnecessary test data
* 💾 Recovers user-created databases
* 🔐 Restores critical InnoDB file (`ibdata1`)

---

## ⚠️ Prerequisites

Before running the script, ensure:

* MySQL service is **stopped**
* You have a `backup` folder (default MySQL backup directory)
* You are inside the MySQL root directory (where `data` folder exists)
* PowerShell is run with appropriate permissions

---

## 🚀 Recovery Script

```powershell
# Backup old data
Rename-Item -Path "./data" -NewName "./data_old"

# Create new data directory
Copy-Item -Path "./backup" -Destination "./data" -Recurse

# Remove test database
Remove-Item "./data/test" -Recurse

# Copy user databases (excluding system DBs)
$dbPaths = Get-ChildItem -Path "./data_old" -Exclude ('mysql', 'performance_schema', 'phpmyadmin') -Recurse -Directory
Copy-Item -Path $dbPaths.FullName -Destination "./data" -Recurse

# Restore InnoDB system file
Copy-Item -Path "./data_old/ibdata1" -Destination "./data/ibdata1"

# Notify user
Write-Host "Finished repairing MySQL data"
Write-Host "Previous data is located at ./data_old"
```

---

## 🧠 How It Works

The script safely renames your existing `data` directory to `data_old`, ensuring no data is lost. It then creates a fresh `data` directory using clean backup files. After that, it restores only your **user-created databases**, avoiding system database conflicts.

Finally, it restores the critical `ibdata1` file, which is necessary for InnoDB tables, ensuring your databases function correctly again.

---

## ✅ Expected Outcome

* MySQL should start without port errors
* Your databases should be restored
* Corrupted system files are replaced

---

## 📂 Important Notes

* Your original data is محفوظ in: `./data_old`
* If something goes wrong, you can manually restore from this backup
* This method works best for **XAMPP / WAMP local setups**

---

## 🧩 Common Cause of Issue

* Improper shutdown of MySQL
* Port conflicts (e.g., port 3306 already in use)
* Corrupted InnoDB files

---

## 💡 Tip

If the issue persists, also check:

* MySQL port configuration (`my.ini`)
* Conflicting services using port 3306

---

## 📜 License

This script is free to use and modify for personal and development purposes.

---

⭐ If this helped you, consider starring the repo!
