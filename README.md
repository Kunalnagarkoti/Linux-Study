# Linux-Study
In Linux, **permissions** control who can **read**, **write**, and **execute** files or directories. Here's a **complete explanation** of Linux **file permissions**, related **commands**, and the **backend processes** when these commands are executed.

---

## **1. File Permissions Basics**

### **1.1 File Permission Format**  
```bash
ls -l filename
```
Output:
```
-rwxr-xr-- 1 user group 4096 Dec 30 10:30 file.txt
```

### **Explanation:**
| Symbol    | Meaning                                             |
|-----------|-----------------------------------------------------|
| `-`       | File type (`-` for regular file, `d` for directory) |
| `rwx`     | Owner permissions (read, write, execute)            |
| `r-x`     | Group permissions                                   |
| `r--`     | Others (everyone else)                              |

**Numeric Representation:**
- **r = 4**, **w = 2**, **x = 1**  
  Example:  
  - `rwx` = **7** (4+2+1)  
  - `r-x` = **5** (4+0+1)  
  - `r--` = **4**

---

## **2. Permission Commands**

### **2.1 Change File Permissions (`chmod`)**

#### **Command:**
```bash
chmod 755 file.txt
```

**Backend Process:**
1. Updates the **inode** of the file in the filesystem, storing new permission values.  
2. Modifies metadata in the filesystem table pointing to the file.  

**Explanation:**
- **7** (Owner): rwx  
- **5** (Group): r-x  
- **5** (Others): r-x  

**Symbolic Mode Example:**
```bash
chmod u+x file.txt    # Add execute permission to the owner
chmod g-w file.txt    # Remove write permission from the group
chmod o=r file.txt    # Set others' permissions to read-only
```

---

### **2.2 Change Ownership (`chown`)**

#### **Command:**
```bash
sudo chown user:group file.txt
```

**Backend Process:**
1. Updates **UID (User ID)** and **GID (Group ID)** in the file's **inode table**.  
2. No changes to the file content, only metadata is updated.

**Examples:**
```bash
sudo chown john:developers file.txt   # Change owner and group
sudo chown :developers file.txt      # Change only group
```

---

### **2.3 Change Group Ownership (`chgrp`)**

#### **Command:**
```bash
sudo chgrp groupname file.txt
```

**Backend Process:**
1. Updates **GID (Group ID)** in the file's inode.  
2. Metadata changes in the filesystem are updated without modifying the content.

---

## **3. Directory Permissions**

### **3.1 Permissions on Directories**
- **r (read):** Allows listing directory contents (`ls`).  
- **w (write):** Allows creating, renaming, and deleting files.  
- **x (execute):** Allows accessing the directory (needed to `cd` into it).

**Example:**
```bash
chmod 750 mydir
```
- **7 (rwx):** Owner has full control.  
- **5 (r-x):** Group can read and access.  
- **0 (---):** Others have no access.  

---

## **4. Special Permissions**

### **4.1 SetUID (Set User ID)**  
- Allows a script to run with the **file owner's permissions**, not the user's.  
- Useful for commands like `passwd`.

**Command:**
```bash
chmod u+s script.sh
```
- **Backend Process:**  
  1. Adds an **SUID flag** in the **inode** metadata.  
  2. Temporarily elevates privileges to the owner during execution.

---

### **4.2 SetGID (Set Group ID)**  
- Files inherit the **group ID** of their parent directory.  
- Useful for shared directories.

**Command:**
```bash
chmod g+s directory/
```
- **Backend Process:**  
  1. Sets **SGID flag** in the **inode metadata**.  
  2. New files inherit the group of the directory, ensuring group collaboration.

---

### **4.3 Sticky Bit**  
- Ensures that only the **owner** or **root** can delete files inside a directory.  
- Common in `/tmp` directories.

**Command:**
```bash
chmod +t directory/
```
- **Backend Process:**  
  1. Sets the **sticky bit** flag in the inode.  
  2. Prevents unauthorized users from deleting each other's files.

---

## **5. ACLs (Access Control Lists)**

### **5.1 Extend Permissions Beyond Basic User/Group**

**Command:**
```bash
setfacl -m u:username:rwx file.txt
```

**Backend Process:**
1. Modifies ACL entries in the filesystem.  
2. Stores extended permissions in a separate **ACL table** in the filesystem metadata.

**View ACLs:**
```bash
getfacl file.txt
```

---

## **6. Default Permissions: umask**

### **6.1 Check Default Permissions**
```bash
umask
```

### **6.2 Change Default Permissions**
```bash
umask 022
```
- **Backend Process:**
  1. Sets default mask bits that subtract permissions when creating files.  
  2. Files are created with `rw-r--r--` (644) instead of `rwxrwxrwx` (777).

---

## **7. Examples and Use Cases**

### **Example 1: Assign Read and Write Permission to a Group**
```bash
chmod g+rw file.txt
```
- Adds read and write permissions for the group.  
- Updates **inode metadata**.

### **Example 2: Make Directory Shared with Group Collaboration**
```bash
sudo chmod 2775 shared_dir/
```
- Sets **SGID** and group-write permissions.  
- All files created in this directory inherit the group ID.

---

## **8. Key Questions Asked in Interviews**

### **Q1: What is the difference between `chmod` and `chown`?**  
**Answer:**  
- `chmod`: Modifies file permissions (read, write, execute).  
- `chown`: Changes ownership of a file or directory.

---

### **Q2: How do you give recursive permissions?**  
**Answer:**  
```bash
chmod -R 755 directory/
```
- Applies permissions to all files and subdirectories.

---

### **Q3: How do you check permissions of a file?**  
**Answer:**  
```bash
ls -l file.txt
getfacl file.txt     # For extended ACLs
```

---

### **Q4: What happens if you set 777 permissions on a file?**  
**Answer:**  
- **Everyone (user, group, others)** gets full permissions (**rwx**).  
- It's a **security risk**, as any user can modify or execute the file.

---

### **Q5: What is the purpose of the sticky bit?**  
**Answer:**  
- Prevents users from deleting files owned by others in shared directories (e.g., `/tmp`).

---

Let me know if you need further clarifications!
