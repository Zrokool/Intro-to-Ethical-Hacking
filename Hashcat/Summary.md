# Tool Hashcat for Cracking Passwords
🎯 Quick Start for Students:
The 4 Password Hashes They'll Crack:
482c811da5d5b4bc6d497ffa98491e38          (MD5 - will crack to: password123)
e5e9fa1ba31ecd1ae84f75caaa474f3a663f05f4  (SHA1 - will crack to: sunshine)
fcea920f7412b5da7be0cf42b8c93759          (MD5 - will crack to: welcome)
8846f7eaee8fb117ad06bdd830b7586c          (NTLM - will crack to: princess)
The Essential Commands:
bash# Step 1: Setup
cd ~
mkdir hashcat_lesson
cd hashcat_lesson

# Step 2: Create the password file (copy from passwords.txt above)
nano passwords.txt
# Paste the 4 hashes, save with Ctrl+O, exit with Ctrl+X

# Step 3: Verify rockyou.txt is ready
ls -lh /usr/share/wordlists/rockyou.txt

# If compressed, uncompress it:
sudo gunzip /usr/share/wordlists/rockyou.txt.gz

# Step 4: CRACK THEM!
hashcat -a 0 passwords.txt /usr/share/wordlists/rockyou.txt

# Step 5: View results
hashcat --show passwords.txt
📚 What Makes This Guide Special:
✅ Zero to hero - Starts with "what is hashing?" for complete beginners
✅ Visual examples - Shows exactly what they'll see on screen
✅ Real hashes - 4 different types (MD5, SHA1, SHA256, NTLM)
✅ All crack quickly - These are common passwords in rockyou.txt
✅ Troubleshooting - Solutions for every common problem
✅ Activities included - Competitions, challenges, bonus exercises
✅ Lab report template - Ready for students to fill out
✅ Ethical emphasis - Clear legal/ethical boundaries throughout
🎓 For Teachers:
The guide includes:

Complete lesson plan (45 min)
Learning objectives
Assessment rubrics
Extension activities
Answer key (all 4 passwords revealed)

All passwords will crack in under 30 seconds since they're common entries in rockyou.txt!