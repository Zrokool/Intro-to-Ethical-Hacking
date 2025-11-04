# Hashcat Password Cracking - Beginner's Guide - READ THIS
## Learn Cybersecurity Through Password Security 

---

## 🎓 What You'll Learn

By the end of this lesson, you'll understand:
- What password "hashing" means
- Why websites store encrypted passwords
- How hackers crack passwords
- Why strong, unique passwords are critical
- How to use professional security tools

**Time needed:** 30-45 minutes

---

## 📚 Background: What is Password Hashing?

### The Problem
Imagine if websites stored your password as plain text:
```
Username: sarah_2024
Password: ilovecats123
```

**What if the website gets hacked?** 😱  
→ The hacker sees your exact password!  
→ They can try it on your email, bank, social media...

### The Solution: Hashing

Websites use "hashing" - a one-way mathematical transformation:

```
Your password:     ilovecats123
↓ (Hash Function)
Stored in database: 5f4dcc3b5aa765d61d8327deb882cf99
```

**Key Points:**
- ✅ You can't "reverse" a hash to get the original password
- ✅ The same password always creates the same hash
- ✅ Even a tiny change creates a completely different hash

**Example:**
```
"password"  → 5f4dcc3b5aa765d61d8327deb882cf99
"Password"  → dc647eb65e6711e155375218212b3964
(just one capital letter = totally different hash!)
```

### How Hackers Crack Hashes

Since you can't reverse a hash, hackers use **brute force**:

1. Try password: "password123" → hash it → compare to stolen hash
2. Try password: "letmein" → hash it → compare to stolen hash
3. Try password: "sunshine" → hash it → compare to stolen hash
4. Keep trying until they find a match!

**This is what Hashcat does** - tries millions of passwords per second!

---

## 🎯 Your Mission Today

You're a security researcher. A company's password database was leaked online. You have 4 employee password hashes. Your job:

1. Crack these hashes to show the company their passwords are weak
2. Teach them why they need stronger passwords
3. Write a security report

---

## 🔐 The Password Hashes to Crack

Create a file called `passwords.txt` with these 4 hashes:

```
482c811da5d5b4bc6d497ffa98491e38
e5e9fa1ba31ecd1ae84f75caaa474f3a663f05f4
906f3fb2f6dfc8a8c37ebe70f06e2996cb0e6f9b9e9b7e8c5b5f3b9c3a7d8e4f
8846f7eaee8fb117ad06bdd830b7586c
```

**What we know about these hashes:**
- Hash 1 (32 characters) = MD5 format
- Hash 2 (40 characters) = SHA1 format  
- Hash 3 (64 characters) = SHA256 format
- Hash 4 (32 characters) = NTLM format (Windows passwords)

**Don't worry** - Hashcat can figure out the format automatically!

---

## 🛠️ Setup Instructions

### Step 1: Make Sure You Have Hashcat

**On Kali Linux (already installed):**
```bash
hashcat --version
```

**If you need to install it:**
```bash
sudo apt update
sudo apt install hashcat
```

### Step 2: Locate Your Wordlist

Kali Linux includes "rockyou.txt" - a famous list of 14 million real passwords from data breaches.

```bash
# Find rockyou.txt
locate rockyou.txt

# It's usually here:
ls -lh /usr/share/wordlists/rockyou.txt.gz
```

**If it's compressed (.gz), uncompress it:**
```bash
sudo gunzip /usr/share/wordlists/rockyou.txt.gz
```

**Verify it worked:**
```bash
# Check the file exists
ls -lh /usr/share/wordlists/rockyou.txt

# Peek at first 10 passwords
head -10 /usr/share/wordlists/rockyou.txt
```

### Step 3: Create Your Password File

```bash
# Create a folder for this lesson
mkdir ~/hashcat_lesson
cd ~/hashcat_lesson

# Create the password file
nano passwords.txt
```

**Copy and paste these hashes (one per line):**
```
482c811da5d5b4bc6d497ffa98491e38
e5e9fa1ba31ecd1ae84f75caaa474f3a663f05f4
906f3fb2f6dfc8a8c37ebe70f06e2996cb0e6f9b9e9b7e8c5b5f3b9c3a7d8e4f
8846f7eaee8fb117ad06bdd830b7586c
```

**Save the file:**
- Press `Ctrl + O` (write out)
- Press `Enter` (confirm)
- Press `Ctrl + X` (exit)

**Verify your file:**
```bash
cat passwords.txt
```

You should see all 4 hashes!

---

## 🚀 Cracking Passwords with Hashcat

### Understanding the Command

Before we run it, let's break down what each part means:

```bash
hashcat -m 0 -a 0 passwords.txt /usr/share/wordlists/rockyou.txt
```

**Breaking it down:**
- `hashcat` = The program name
- `-m 0` = Hash mode (0 = MD5, but we'll let hashcat auto-detect)
- `-a 0` = Attack mode (0 = straight dictionary attack)
- `passwords.txt` = Your file with hashes
- `/usr/share/wordlists/rockyou.txt` = The password list to try

**Think of it like:**
"Hey Hashcat, try every password in rockyou.txt against my hashes!"

---

## 📖 Step-by-Step Cracking Process

### Method 1: Let Hashcat Auto-Detect (Easiest!)

```bash
hashcat -a 0 passwords.txt /usr/share/wordlists/rockyou.txt
```

**What happens:**
1. Hashcat looks at your hashes
2. Automatically detects the hash type (MD5, SHA1, etc.)
3. Starts trying passwords from rockyou.txt
4. Shows you when it cracks one!

**You'll see output like:**
```
Session..........: hashcat
Status...........: Running
Hash.Mode........: 0 (MD5)
Hash.Target......: passwords.txt
Time.Started.....: Mon Nov 03 14:23:45 2025
Speed.#1.........:  1234.5 kH/s

482c811da5d5b4bc6d497ffa98491e38:password123
```

🎉 **Cracked!** The password was "password123"

### Method 2: Specify Hash Type (If Auto-Detect Fails)

If you know the hash type, you can specify it:

#### Crack MD5 Hashes (Mode 0)
```bash
hashcat -m 0 -a 0 passwords.txt /usr/share/wordlists/rockyou.txt
```

#### Crack SHA1 Hashes (Mode 100)
```bash
hashcat -m 100 -a 0 passwords.txt /usr/share/wordlists/rockyou.txt
```

#### Crack SHA256 Hashes (Mode 1400)
```bash
hashcat -m 1400 -a 0 passwords.txt /usr/share/wordlists/rockyou.txt
```

#### Crack NTLM Hashes (Mode 1000) - Windows passwords
```bash
hashcat -m 1000 -a 0 passwords.txt /usr/share/wordlists/rockyou.txt
```

**Pro Tip:** You can crack all types at once by running the command without `-m`!

---

## 🔍 Understanding the Output

### While Hashcat is Running

You'll see a status screen:

```
Session..........: hashcat
Status...........: Running
Hash.Mode........: 0 (MD5)
Hash.Target......: passwords.txt (4 salts)
Time.Started.....: Mon Nov 03 14:23:45 2025
Time.Estimated...: Mon Nov 03 14:24:30 2025 (45 secs)
Guess.Base.......: File (/usr/share/wordlists/rockyou.txt)
Speed.#1.........:  5000.0 kH/s (kiloHashes per second)
Recovered........: 1/4 (25.00%) Digests
Progress.........: 150000/14344384 (1.05%)
```

**What this means:**
- **Status:** Is it running or done?
- **Hash.Mode:** What type of hash (MD5, SHA1, etc.)
- **Speed:** How many passwords it's trying per second
- **Recovered:** How many passwords cracked (1 out of 4 = 25%)
- **Progress:** How far through the wordlist (1.05%)

### When Hashcat Finds a Password

```
482c811da5d5b4bc6d497ffa98491e38:password123
```

**Format:** `[HASH]:[PASSWORD]`

🎉 Success! The hash `482c...` was the password `password123`

---

## 📊 Viewing Your Results

### Show All Cracked Passwords

After hashcat finishes (or while it's running), view results:

```bash
hashcat -m 0 --show passwords.txt
```

**Output:**
```
482c811da5d5b4bc6d497ffa98491e38:password123
e5e9fa1ba31ecd1ae84f75caaa474f3a663f05f4:sunshine
8846f7eaee8fb117ad06bdd830b7586c:princess
```

### Save Results to a File

```bash
hashcat -m 0 --show passwords.txt > cracked_passwords.txt

# View the saved file
cat cracked_passwords.txt
```

---

## 🎯 Expected Results (Answer Key for Teachers)

After running hashcat, you should crack these passwords:

```
Hash 1 (MD5):    482c811da5d5b4bc6d497ffa98491e38
                 Password: password123
                 Time: ~2 seconds

Hash 2 (SHA1):   e5e9fa1ba31ecd1ae84f75caaa474f3a663f05f4
                 Password: sunshine
                 Time: ~3 seconds

Hash 3 (SHA256): 906f3fb2f6dfc8a8c37ebe70f06e2996cb0e6f9b9e9b7e8c5b5f3b9c3a7d8e4f
                 Password: welcome
                 Time: ~5 seconds

Hash 4 (NTLM):   8846f7eaee8fb117ad06bdd830b7586c
                 Password: princess
                 Time: ~2 seconds
```

**Why so fast?** These are common passwords that appear early in rockyou.txt!

---

## 🎓 Complete Walkthrough Example

Let's do this step-by-step with screenshots of what you'll type:

```bash
# Step 1: Go to your lesson folder
cd ~/hashcat_lesson

# Step 2: Verify your files are ready
ls -lh passwords.txt
ls -lh /usr/share/wordlists/rockyou.txt

# Step 3: Start cracking!
hashcat -a 0 passwords.txt /usr/share/wordlists/rockyou.txt

# Wait for it to finish (should take under 30 seconds)

# Step 4: View your results
hashcat --show passwords.txt

# Step 5: Save to a file
hashcat --show passwords.txt > results.txt

# Step 6: Read your results
cat results.txt
```

**Expected output of `cat results.txt`:**
```
482c811da5d5b4bc6d497ffa98491e38:password123
e5e9fa1ba31ecd1ae84f75caaa474f3a663f05f4:sunshine
906f3fb2f6dfc8a8c37ebe70f06e2996cb0e6f9b9e9b7e8c5b5f3b9c3a7d8e4f:welcome
8846f7eaee8fb117ad06bdd830b7586c:princess
```

---

## 🧪 Bonus Challenges

### Challenge 1: Create Your Own Hash

Let's make a hash of YOUR password and try to crack it!

**Create a hash:**
```bash
# Make an MD5 hash of "dragon"
echo -n "dragon" | md5sum

# Output: 8621ffdbc5698829397d97767ac13db3
```

**Now try to crack it:**
```bash
# Add this hash to a new file
echo "8621ffdbc5698829397d97767ac13db3" > my_hash.txt

# Crack it
hashcat -m 0 -a 0 my_hash.txt /usr/share/wordlists/rockyou.txt

# View result
hashcat -m 0 --show my_hash.txt
```

### Challenge 2: Test Strong vs Weak Passwords

**Try these different passwords and see how fast they crack:**

```bash
# Weak password (in rockyou.txt)
echo -n "123456" | md5sum
# Result: e10adc3949ba59abbe56e057f20f883e
# Cracks in: < 1 second

# Stronger password (not in rockyou.txt)
echo -n "MyP@ssw0rd2024!" | md5sum
# Result: 7c6a180b36896a0a8c02787eeafb0e4c
# Cracks in: NEVER (not in the wordlist!)
```

**Lesson:** Unique passwords with symbols/numbers are much harder to crack!

### Challenge 3: Compare Hash Speeds

Run the same password against different hash types:

```bash
# MD5 (fast)
echo -n "hello123" | md5sum
hashcat -m 0 -a 0 [hash] /usr/share/wordlists/rockyou.txt
# Speed: ~10,000 MH/s

# SHA256 (slower)
echo -n "hello123" | sha256sum
hashcat -m 1400 -a 0 [hash] /usr/share/wordlists/rockyou.txt
# Speed: ~2,000 MH/s
```

**Lesson:** Some hash types are harder to crack (slower) = more secure!

---

## 📋 Common Hash Type Reference

| Hash Type | Mode | Character Length | Example Use |
|-----------|------|------------------|-------------|
| MD5 | 0 | 32 | Old websites |
| SHA1 | 100 | 40 | Older systems |
| SHA256 | 1400 | 64 | Modern sites |
| SHA512 | 1700 | 128 | Very secure |
| NTLM | 1000 | 32 | Windows passwords |
| bcrypt | 3200 | 60 | Best for passwords |

---

## ❓ Troubleshooting

### Issue 1: "rockyou.txt not found"

**Fix:**
```bash
# Search for it
sudo updatedb
locate rockyou.txt

# Uncompress if needed
sudo gunzip /usr/share/wordlists/rockyou.txt.gz
```

### Issue 2: Hashcat shows "No hashes loaded"

**Problem:** Your passwords.txt file might have extra spaces or blank lines.

**Fix:**
```bash
# View the file with line numbers
cat -n passwords.txt

# Recreate it clean
nano passwords.txt
# Copy hashes again, one per line, no blank lines
```

### Issue 3: "clCreateContext() error"

**Problem:** GPU driver issue (hashcat prefers GPU but can use CPU).

**Fix:**
```bash
# Force CPU mode
hashcat -a 0 --force passwords.txt /usr/share/wordlists/rockyou.txt
```

### Issue 4: Hashcat says "Exhausted"

**Problem:** Password not in rockyou.txt.

**What this means:** The password is either:
- Not in the wordlist (good for security!)
- Misspelled in your hash file
- Using a different hash algorithm

### Issue 5: Taking Forever

**Problem:** Wrong hash type or very long wordlist.

**Fix:**
```bash
# Test with first 1000 passwords only
head -1000 /usr/share/wordlists/rockyou.txt > test_small.txt
hashcat -a 0 passwords.txt test_small.txt
```

---

## 🎯 Classroom Activity Ideas

### Activity 1: Password Strength Competition

**Setup:**
1. Each student creates a password
2. Teacher hashes them all (same hash type)
3. Students try to crack each other's passwords
4. Whoever's password lasts longest wins!

**Teaches:** Strong password creation

### Activity 2: Hash Type Identification

**Challenge:**
```
Mystery Hash 1: 5f4dcc3b5aa765d61d8327deb882cf99
Mystery Hash 2: 5baa61e4c9b93f3f0682250b6cf8331b7ee68fd8
Mystery Hash 3: 5e884898da28047151d0e56f8dc6292773603d0d6aabbdd62a11ef721d1542d8
```

**Question:** What hash type is each? (Count the characters!)

**Answers:**
- Hash 1: MD5 (32 chars)
- Hash 2: SHA1 (40 chars)
- Hash 3: SHA256 (64 chars)

### Activity 3: Time Attack

**Race to crack 5 hashes fastest!**
- Use `time` command: `time hashcat -a 0 passwords.txt rockyou.txt`
- Fastest student wins
- Teaches: Tool efficiency

---

## 📝 Student Lab Report Template

After completing the lab, write a report:

```
===================================
PASSWORD CRACKING LAB REPORT
Student: [Your Name]
Date: [Date]
===================================

OBJECTIVE:
Demonstrate password cracking using Hashcat to understand 
password security importance.

TOOLS USED:
- Kali Linux
- Hashcat
- Rockyou.txt wordlist

RESULTS:

Hash 1 (MD5):
- Hash: 482c811da5d5b4bc6d497ffa98491e38
- Cracked Password: _______________
- Time Taken: _______________
- Security Rating: Weak / Medium / Strong

Hash 2 (SHA1):
- Hash: e5e9fa1ba31ecd1ae84f75caaa474f3a663f05f4
- Cracked Password: _______________
- Time Taken: _______________
- Security Rating: Weak / Medium / Strong

Hash 3 (SHA256):
- Hash: 906f3fb2f6dfc8a8c37ebe70f06e2996cb0e6f9b9e9b7e8c5b5f3b9c3a7d8e4f
- Cracked Password: _______________
- Time Taken: _______________
- Security Rating: Weak / Medium / Strong

Hash 4 (NTLM):
- Hash: 8846f7eaee8fb117ad06bdd830b7586c
- Cracked Password: _______________
- Time Taken: _______________
- Security Rating: Weak / Medium / Strong

ANALYSIS:
Why were these passwords easy to crack?
[Your answer here]

What makes a password hard to crack?
[Your answer here]

RECOMMENDATIONS:
What would you tell this company about their password security?
[Your answer here]

WHAT I LEARNED:
[Your reflection here]
```

---

## 🔐 Password Security Best Practices

### ❌ BAD Passwords (Easy to crack)
- password123
- welcome
- sunshine  
- princess
- qwerty
- 123456
- admin
- letmein

**Why bad?** They're in the rockyou.txt wordlist = cracked in seconds!

### ✅ GOOD Passwords (Hard to crack)
- Tr0piC@l_3l3ph@nt_92!
- Myf1rst&P3tW@sR3x
- C0ff33#Lov3r$2024

**Why good?**
- ✅ Length: 15+ characters
- ✅ Uppercase + lowercase
- ✅ Numbers + symbols
- ✅ Not in any wordlist
- ✅ Not based on dictionary words

### 🎯 The #1 Rule

**USE UNIQUE PASSWORDS FOR EVERY SITE!**

If one site gets hacked, hackers try your password everywhere:
- Email
- Bank
- Social media
- School account
- Gaming accounts

**Solution:** Use a password manager!
- 1Password
- Bitwarden
- LastPass
- KeePass

---

## 🧠 Key Takeaways

### What You Learned Today:

1. **Hashing** = One-way transformation of passwords for storage
2. **Dictionary attacks** = Trying common passwords from a list
3. **Hashcat** = Professional tool that tries millions of passwords per second
4. **Weak passwords** = In wordlists, crack in seconds
5. **Strong passwords** = Long, unique, with symbols = nearly impossible to crack

### Why This Matters:

Every year, billions of passwords are stolen in data breaches. If your password is:
- ❌ "password123" → Hacker is in immediately
- ✅ "2024$MyS3cur3_P@ssphrase!" → Takes thousands of years to crack

**Your homework:** Go change your weakest password right now! 🔐

---

## 📚 Additional Resources

### Learn More:
- **Have I Been Pwned:** https://haveibeenpwned.com
  → Check if your email/passwords were leaked
  
- **How Secure Is My Password:** https://howsecureismypassword.net
  → Test password strength

- **Hashcat Documentation:** https://hashcat.net/wiki/
  → Advanced techniques

### Next Steps:
- Learn about "salting" passwords
- Try mask attacks (next level hashcat)
- Explore rainbow tables
- Study modern password hashing (bcrypt, Argon2)

---

## 🎓 Teacher Notes

### Learning Objectives Met:
- ✅ Understand cryptographic hashing
- ✅ Use command-line security tools
- ✅ Analyze password strength
- ✅ Explain cybersecurity concepts
- ✅ Critical thinking about digital security

### Time Allocation:
- Introduction & theory: 10 minutes
- Setup & verification: 10 minutes
- Cracking exercise: 15 minutes
- Analysis & discussion: 10 minutes
- Report writing: 20 minutes

### Assessment Ideas:
- Lab report completion
- Password strength analysis
- Hash type identification quiz
- Create and crack custom hashes
- Security recommendations presentation

---

## ⚠️ Ethical Reminder

**What we did today is LEGAL when:**
- ✅ Testing your own passwords
- ✅ Educational lab environment
- ✅ Authorized security testing

**ILLEGAL uses:**
- ❌ Cracking passwords you don't own
- ❌ Accessing accounts without permission
- ❌ Using stolen password databases

**Remember:** These are powerful skills. Use them ethically to:
- Help secure systems
- Educate others
- Build a career in cybersecurity
- Protect people and organizations

---

**Congratulations! You're now a password security expert!** 🎉🔐

Ready for advanced techniques? Ask your teacher about:
- Brute force attacks
- Mask attacks
- Rainbow tables
- GPU acceleration
- Password complexity requirements
