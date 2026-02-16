# oracle_pdb_ass_II_29111_Honore

# Oracle Pluggable Database (PDB) Management
### Assignment II — INSY 8311: Database Development with PL/SQL

---

## 👤 Student Information

| Field | Details |
|-------|---------|
| **Full Name** | HIRWA NGENZI Honore |
| **Student ID** | 29111 |
| **Course** | Database Development with PL/SQL (INSY 8311) |
| **Group** | D — Thursday Evening |
| **Instructor** | Eric Maniraguha |
| **Assignment Date** | February 9, 2026 |
| **Submission Deadline** | February 16, 2026 — 11:59 PM |

---

## 📋 Overview

This repository documents the practical completion of **Individual Assignment II**, focused on Oracle Pluggable Database (PDB) management using Oracle SQL Developer and Oracle Enterprise Manager (OEM).

The assignment covers:
- Creating and managing Pluggable Databases (PDBs) in Oracle Multitenant Architecture
- Creating and deleting a temporary PDB
- Configuring and accessing Oracle Enterprise Manager (OEM)
- Professional documentation of all tasks

---

## 🛠️ Oracle Environment

| Component | Details |
|-----------|---------|
| **Oracle Version** | Oracle Database 21c |
| **Operating System** | Windows 10 |
| **Tool Used** | Oracle SQL Developer |
| **Container Database (CDB)** | ORCL |
| **Oracle Data Path** | `C:\ORACLE21C\ORADATA\ORCL\` |

---

## 📁 Repository Structure

```
oracle_pdb_ass_II_29111_Honore/
│
├── README.md               ← This file
└── screenshots/
    ├── task1_pdb_creation.png
    ├── task1_pdb_open.png
    ├── task1_user_created.png
    ├── task2_temp_pdb_created.png
    ├── task2_temp_pdb_verified.png
    ├── task2_temp_pdb_deleted.png
    ├── task2_temp_pdb_confirmed.png
    └── task3_oem_dashboard.png
```

---

## ✅ Task 1: Create a New Pluggable Database

### Naming Convention Used
- **PDB Name:** `hi_pdb_29111`
- **Username inside PDB:** `hirwa_plsqlauca_29111`
- **Password:** *(kept private)*

### What was done
A new Pluggable Database was created from the PDB Seed using Oracle SQL Developer. After creation, the PDB was opened and a dedicated user was created inside it. This user account will be used for all future PL/SQL class work.

### Commands Used

```sql
-- Step 1: Create the PDB
CREATE PLUGGABLE DATABASE hi_pdb_29111
ADMIN USER hirwa_plsqlauca_29111 IDENTIFIED BY [password];

-- Step 2: Open the PDB
ALTER PLUGGABLE DATABASE hi_pdb_29111 OPEN;

-- Step 3: Save state so it opens automatically on restart
ALTER PLUGGABLE DATABASE hi_pdb_29111 SAVE STATE;

-- Step 4: Switch into the PDB
ALTER SESSION SET CONTAINER = hi_pdb_29111;

-- Step 5: Create the user inside the PDB
CREATE USER hirwa_plsqlauca_29111 IDENTIFIED BY [password];

-- Step 6: Grant necessary privileges
GRANT CONNECT, RESOURCE TO hirwa_plsqlauca_29111;
```

### Screenshots

**PDB Creation Command:**

![PDB Creation](https://github.com/NGENZ1/oracle_pdb_ass_II_29111_Honore/blob/main/create_main_pdb.PNG)

**PDB Open State:**

![PDB Open State](https://github.com/NGENZ1/oracle_pdb_ass_II_29111_Honore/blob/main/open_main_pdb.PNG)

**Verify The Created PDB:**

![User Created](screenshots/task1_user_created.png)

---

## ✅ Task 2: Create and Delete a Temporary PDB

### Naming Convention Used
- **Temporary PDB Name:** `hi_to_delete_pdb_29111`

### What was done
A temporary PDB was created, verified to exist, then completely dropped including all associated data files. Confirmation was done using a query on `v$pdbs` to ensure it no longer existed.

### Commands Used

```sql
-- Step 1: Create the temporary PDB
CREATE PLUGGABLE DATABASE hi_to_delete_pdb_29111
ADMIN USER temp_admin IDENTIFIED BY [password];

-- Step 2: Open it to verify it works
ALTER PLUGGABLE DATABASE hi_to_delete_pdb_29111 OPEN;

-- Step 3: Verify it exists
SELECT name, open_mode FROM v$pdbs;

-- Step 4: Go back to CDB root before dropping
ALTER SESSION SET CONTAINER = CDB$ROOT;

-- Step 5: Close the PDB
ALTER PLUGGABLE DATABASE hi_to_delete_pdb_29111 CLOSE IMMEDIATE;

-- Step 6: Drop the PDB and all its files
DROP PLUGGABLE DATABASE hi_to_delete_pdb_29111 INCLUDING DATAFILES;

-- Step 7: Confirm it no longer exists
SELECT name, open_mode FROM v$pdbs;
```

### Screenshots

**Temporary PDB Created:**

![Temp PDB Created](screenshots/task2_temp_pdb_created.png)

**PDB Verified (Exists):**

![Temp PDB Verified](screenshots/task2_temp_pdb_verified.png)

**PDB Deleted:**

![Temp PDB Deleted](screenshots/task2_temp_pdb_deleted.png)

**Deletion Confirmed (No longer in list):**

![Temp PDB Confirmed Gone](screenshots/task2_temp_pdb_confirmed.png)

---

## ✅ Task 3: Oracle Enterprise Manager (OEM)

### What was done
Oracle Enterprise Manager (OEM) was accessed and configured. The dashboard reflects the Oracle 21c environment along with the PDB tasks completed in Tasks 1 and 2.

### Screenshot

**OEM Dashboard:**

![OEM Dashboard](screenshots/task3_oem_dashboard.png)

---

## ⚠️ Challenges Faced & How They Were Solved

### Challenge 1: FILE_NAME_CONVERT Error
**Problem:** When initially trying to create a PDB, the `FILE_NAME_CONVERT` parameter was required but the correct Windows file path was not known.

**Solution:** Ran the following query to find the actual Oracle file paths on the machine:
```sql
SELECT NAME FROM v$datafile;
```
This revealed the correct path: `C:\ORACLE21C\ORADATA\ORCL\`

---

### Challenge 2: OMF (Oracle Managed Files) Not Configured
**Problem:** The parameter `db_create_file_dest` had no value set, meaning Oracle could not automatically manage file destinations. This made PDB creation more complex and required manual file path specification.

**Solution:** Enabled OMF by setting the correct destination path:
```sql
ALTER SYSTEM SET db_create_file_dest = 'C:\ORACLE21C\ORADATA\ORCL' SCOPE=BOTH;
```
After this, PDB creation no longer required `FILE_NAME_CONVERT`.

---

### Challenge 3: Wrong File Path Format (Linux vs Windows)
**Problem:** Documentation and online resources commonly show Linux-style paths (e.g., `/u01/app/oracle/oradata`) which do not work on Windows installations.

**Solution:** Used the `v$datafile` query to identify the correct Windows-style path and adjusted all commands accordingly.

---

## 🔍 Key Concepts Learned

- **Oracle Multitenant Architecture**: A CDB (Container Database) can host multiple PDBs, each acting as an independent database.
- **PDB Seed**: The template (`PDB$SEED`) that Oracle uses to create new PDBs.
- **OMF (Oracle Managed Files)**: When enabled, Oracle automatically manages file naming and placement, simplifying PDB creation.
- **`INCLUDING DATAFILES`**: Essential when dropping a PDB to ensure physical files are also removed from disk.

---

## 🧾 Integrity Statement

I, **HIRWA NGENZI Honore** (Student ID: 29111), confirm that all work documented in this repository is my own. All commands were executed personally on my own Oracle 21c installation. No screenshots, commands, or solutions were copied from classmates or any other student.

I understand that violations of academic integrity result in **ZERO MARKS** and I fully accept responsibility for the authenticity of this submission.

---

## 📬 Submission

- **Submitted via:** Google Form (as required)
- **GitHub Repository:** `oracle_pdb_ass_II_29111_Honore`
- **Repository Visibility:** 🔓 PUBLIC

---

*HIRWA NGENZI Honore | Student ID: 29111 | INSY 8311 Group D | February 2026*
