# Burp Suite Web Security Testing - Student Guide
## Learn How Web Applications Work (and Break!)

---

## 🎓 Welcome to Web Security Testing!

**What You'll Learn Today:**
- How web applications communicate with servers
- What hackers can see and change in web traffic
- Why client-side validation isn't secure
- How to use professional security testing tools
- Why backend security is critical

**Time Needed:** 60-90 minutes  
**Prerequisites:** None! This is for complete beginners.

**Tools You'll Use:**
- 🌐 Firefox Browser (with FoxyProxy)
- 🔧 Burp Suite (intercepting proxy)
- 🧃 OWASP Juice Shop (practice target)

---

## 📚 Background: How Websites Really Work

### The Real-World Analogy: Restaurant Ordering

Imagine ordering food at a restaurant:

**Normal scenario:**
1. 👤 You (customer) tell the waiter: "I want a burger, $10"
2. 🚶 Waiter writes it down and takes it to the kitchen
3. 👨‍🍳 Kitchen makes the burger
4. 🍔 Waiter brings you the food
5. 💰 You pay $10

**What if you could intercept the waiter?**
1. 👤 You tell waiter: "I want a burger, $10"
2. 🕵️ **YOU intercept the waiter in the hallway**
3. ✏️ **YOU change the order to: "I want a burger, $1"**
4. 🚶 Waiter continues to kitchen with YOUR modified order
5. 👨‍🍳 Kitchen thinks you're paying $1
6. 🍔 You get the burger for $1 instead of $10!

**This is EXACTLY what Burp Suite does with web traffic!**

### How Websites Communicate

When you use a website:

```
[Your Browser] ←→ [Internet] ←→ [Website Server]
```

**What happens:**
1. Browser sends a REQUEST: "Show me the login page"
2. Server sends a RESPONSE: "Here's the HTML for the login page"
3. You type username/password and click "Login"
4. Browser sends REQUEST: "username=admin&password=secret123"
5. Server checks and sends RESPONSE: "Login successful" or "Invalid password"

**The problem:** All this traffic travels through the internet where it can be intercepted and modified!

---

## 🔧 Meet Your Tools

### Tool 1: Burp Suite (The Interceptor)

**What is Burp Suite?**
Think of it as a "man in the middle" that sits between your browser and the website:

```
[Your Browser] ←→ [BURP SUITE] ←→ [Website]
                      ↑
                 You control this!
```

**What Burp Suite does:**
- 👁️ Shows you ALL traffic between browser and server
- ✋ Stops requests so you can read them
- ✏️ Lets you modify requests before they reach the server
- 💾 Saves all traffic for later analysis
- 🔍 Helps find security vulnerabilities

**Real-world uses:**
- Security professionals test for vulnerabilities
- Developers debug their applications
- Penetration testers find security flaws
- Unfortunately, hackers also use it for attacks

### Tool 2: FoxyProxy (The Traffic Director)

**What is FoxyProxy?**
A browser extension that routes your traffic through Burp Suite.

**The Setup (already done for you!):**
```
Normal: [Browser] → [Website]
With Proxy: [Browser] → [Burp Suite] → [Website]
```

**FoxyProxy makes it easy to:**
- ✅ Turn proxy on (route through Burp)
- ❌ Turn proxy off (direct connection)
- 🎯 Switch between different proxy configurations

---

## 🎯 The 4 Web Exploitation Concepts You'll Learn

### Concept 1: HTTP Request Interception
**Educational Goal:** Understand how browsers and servers communicate

**What you'll learn:**
- Every click sends data to the server
- You can see and modify this data
- Never trust data from the client

**Real-world impact:** Understanding that client-side validation is useless

---

### Concept 2: Price Manipulation
**Educational Goal:** Learn why backend validation is critical

**What you'll learn:**
- Prices shouldn't be controlled by the browser
- Attackers can change any value they want
- Server must validate EVERYTHING

**Real-world impact:** E-commerce sites lose millions to price manipulation

---

### Concept 3: Cookie and Session Manipulation
**Educational Goal:** Understand how websites remember who you are

**What you'll learn:**
- Cookies store your login information
- Session tokens identify you to the server
- Manipulating cookies can bypass authentication

**Real-world impact:** Account takeovers and identity theft

---

### Concept 4: SQL Injection Basics
**Educational Goal:** See how attackers can manipulate database queries

**What you'll learn:**
- Websites use databases to store data
- User input can be used to attack databases
- Proper input sanitization is essential

**Real-world impact:** Data breaches and database compromise

---

## 🚀 Setup and Verification

### Step 1: Verify Juice Shop is Running

```bash
# Check if Juice Shop is running
sudo docker ps | grep juice-shop

# If not running, start it
sudo docker run -d -p 3000:3000 bkimminich/juice-shop

# Test in browser
firefox http://localhost:3000
```

You should see a colorful online juice store! 🧃

### Step 2: Launch Burp Suite

```bash
# Start Burp Suite
burpsuite &

# Or from the Kali menu:
# Applications → Web Application Analysis → burpsuite
```

**First Time Setup:**
1. Click "Next" on the startup screen
2. Select "Temporary Project" → Click "Next"
3. Select "Use Burp Defaults" → Click "Start Burp"
4. You'll see the Burp Suite dashboard

### Step 3: Verify FoxyProxy Configuration

**In Firefox:**
1. Look for the FoxyProxy icon (🦊) in the toolbar (top right)
2. Click the FoxyProxy icon
3. You should see: "Burp" option (already configured)
4. Current setting should be: "Turn Off" or "Disabled"

**The proxy settings (already configured):**
- Proxy IP: `127.0.0.1`
- Port: `8080`

### Step 4: Test the Burp Suite Certificate

**Why we need this:** Burp needs to decrypt HTTPS traffic to show it to you.

**Already done, but to verify:**
1. In Firefox, go to: `about:preferences#privacy`
2. Scroll down to "Certificates"
3. Click "View Certificates"
4. Look for "PortSwigger CA" certificate
5. This allows Burp to intercept HTTPS

---

## 📖 CONCEPT 1: HTTP Request Interception
### Understanding Web Communication

### The Educational Goal

**What you'll understand:**
Every action you take on a website sends information:
- Clicking a button sends a request
- Typing in a search box sends a request
- Loading a page sends multiple requests

**Why this matters:**
Attackers can see ALL of this data and modify it before the server receives it.

### Step-by-Step Exercise

#### Part A: Turn On Interception

**1. Enable FoxyProxy:**
- Click the FoxyProxy icon (🦊) in Firefox
- Select "Burp" from the list
- The icon should turn blue/active

**2. Enable Interception in Burp:**
- Go to Burp Suite
- Click the "Proxy" tab
- Click the "Intercept" sub-tab
- Make sure the button says "Intercept is on" (if it says "Intercept is off", click it)

**You should see:**
```
┌─────────────────────────────────────┐
│ Intercept is on                     │
└─────────────────────────────────────┘
```

#### Part B: Capture Your First Request

**3. Make a Simple Request:**
- Go to Firefox
- Visit: `http://localhost:3000`
- The page will NOT load! This is normal!

**4. Look at Burp Suite:**
You'll see something like this:

```
GET /#/ HTTP/1.1
Host: localhost:3000
User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:109.0)
Accept: text/html,application/xhtml+xml
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate
Connection: keep-alive
```

**What you're seeing:**
- `GET` = Type of request (getting data)
- `/#/` = The page you're requesting
- `Host: localhost:3000` = Where it's going
- All the other lines = Extra information your browser sends

#### Part C: Understanding the Request

**Let's break it down:**

```
GET /#/ HTTP/1.1
└─┬┘ └┬┘ └────┬───┘
  │   │       │
  │   │       └─ HTTP version
  │   └───────── What page you want
  └───────────── Method (GET, POST, etc.)
```

**Headers explained:**
- **Host:** Which website you're visiting
- **User-Agent:** What browser you're using
- **Accept:** What type of content you can handle
- **Cookie:** Your login information (if logged in)

**Think of it like a letter:**
```
To: localhost:3000 (Host)
From: Firefox on Linux (User-Agent)
Message: Please send me the homepage (GET /#/)
```

#### Part D: Forward the Request

**5. Let the request continue:**
- Click "Forward" in Burp Suite
- The request goes to the server
- The page loads in Firefox!

**What just happened:**
1. ✅ Browser tried to load page
2. 🛑 Burp intercepted the request
3. 👁️ YOU saw the request
4. ▶️ You clicked "Forward"
5. ✅ Request reached the server
6. 📄 Server sent back the page

### Student Activity 1: Request Capture Challenge

**Task:** Capture and document 3 different types of requests.

**Instructions:**
1. Make sure "Intercept is on" in Burp
2. In Firefox on Juice Shop, do these actions:
   - Click "Account" menu
   - Click on a product
   - Use the search box

3. For each request, write down:
   - Request method (GET or POST): _______________
   - URL/path being accessed: _______________
   - Any interesting headers: _______________

**Questions:**
1. What's the difference between GET and POST requests?
   _______________________________________________

2. Can you see any sensitive information in these requests?
   _______________________________________________

3. Why is this a security concern?
   _______________________________________________

---

## 📖 CONCEPT 2: Price Manipulation
### Why Client-Side Validation Fails

### The Educational Goal

**What you'll understand:**
Never trust the client! Anything controlled by the browser can be modified.

**Real-world example:**
In 2019, multiple e-commerce sites lost millions because they let browsers control prices instead of validating them on the server.

**The vulnerability:**
When you add items to your cart, the browser sends:
```
Product: Juice
Price: $10.00
Quantity: 1
```

**What if you change it to:**
```
Product: Juice
Price: $0.01  ← You changed this!
Quantity: 1
```

If the server doesn't validate, you just bought $10 juice for 1 cent!

### Step-by-Step Exercise

#### Part A: Add Something to Cart

**1. Prepare Burp:**
- "Intercept is on" in Burp Suite
- FoxyProxy enabled (🦊 blue icon)

**2. Browse Juice Shop:**
- Click on any product (e.g., "Apple Juice")
- Click "Add to Basket"

**3. Your browser will freeze** - This is normal! The request is intercepted.

#### Part B: Examine the Request

**4. Look at Burp Suite:**

You'll see a POST request like this:

```
POST /api/BasketItems/ HTTP/1.1
Host: localhost:3000
Content-Type: application/json
Authorization: Bearer eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCJ9...

{
  "ProductId": 1,
  "BasketId": "5",
  "quantity": 1
}
```

**What you're seeing:**
- `POST` = Sending data to the server
- `/api/BasketItems/` = The shopping cart API
- The JSON data shows what you're adding

**Notice something missing?** There's no price! The server looks it up based on ProductId.

#### Part C: Modify the Quantity

Let's try something interesting...

**5. Find the quantity in the request:**
```
{
  "ProductId": 1,
  "BasketId": "5",
  "quantity": 1    ← We'll change this!
}
```

**6. Change it to a negative number:**
```
{
  "ProductId": 1,
  "BasketId": "5",
  "quantity": -100
}
```

**7. Click "Forward" in Burp**

**8. Check your cart in Firefox!**

**What happened?** Depending on how Juice Shop handles it, you might:
- Get an error (good security!)
- Get free products (bad security!)
- Earn money by "returning" items you never bought!

#### Part D: Finding Price Manipulation

**Let's look for requests where price IS sent:**

**9. Try the Payment/Checkout process:**
- Go through checkout steps
- Intercept each request with Burp
- Look for any requests containing price information

**Example vulnerable request:**
```
POST /api/checkout HTTP/1.1

{
  "items": [
    {
      "id": 1,
      "name": "Apple Juice",
      "price": 1.99,     ← What if we change this?
      "quantity": 1
    }
  ]
}
```

**10. Change the price to 0.01:**
```
"price": 0.01
```

**11. Click "Forward" and complete the purchase**

Did the server accept your modified price? 

### Student Activity 2: Price Testing Challenge

**Task:** Test different price modifications and document what works.

**Test these scenarios:**

1. **Test 1: Negative quantity**
   - Original quantity: 1
   - Modified to: -10
   - Result: _______________

2. **Test 2: Zero price** (if you find a price field)
   - Original price: $X.XX
   - Modified to: 0.00
   - Result: _______________

3. **Test 3: Massive quantity**
   - Original quantity: 1
   - Modified to: 999999
   - Result: _______________

**Analysis Questions:**

1. Which modifications did the server accept?
   _______________________________________________

2. Why is it dangerous to trust client-provided prices?
   _______________________________________________

3. How should the server validate prices properly?
   _______________________________________________

### The Lesson

**❌ Bad (Insecure) Design:**
```
Client says: "I want product #5 for $0.01"
Server says: "OK! Here you go."
```

**✅ Good (Secure) Design:**
```
Client says: "I want product #5"
Server says: "Product #5 costs $10. Pay $10 or no deal."
```

**Always validate prices, quantities, and permissions on the SERVER, never trust the CLIENT!**

---

## 📖 CONCEPT 3: Cookie and Session Manipulation
### How Websites Remember You

### The Educational Goal

**What you'll understand:**
- How websites maintain your login session
- What cookies contain
- How session hijacking works
- Why secure session management is critical

**Real-world impact:**
In 2013, Yahoo had a major breach because session cookies weren't properly secured, allowing attackers to hijack user accounts.

### Background: What Are Cookies?

**Think of cookies like a claim ticket at a coat check:**

```
You: "I'd like to store my coat"
Coat Check: "Sure! Here's ticket #482"
         [Gives you a paper ticket]
         
Later...

You: "I'd like my coat back"
     [Shows ticket #482]
Coat Check: "Here's your coat!"
```

**Website cookies work the same way:**

```
You: "Username: admin, Password: admin123"
Server: "Login successful! Here's your session token: abc123xyz"
        [Sends cookie with token]

Later...

Browser automatically sends: "Cookie: session=abc123xyz"
Server: "Oh, you're the admin user! Welcome back!"
```

### Step-by-Step Exercise

#### Part A: Login and Capture Your Cookie

**1. Login to Juice Shop:**
- Click "Account" → "Login"
- Use these credentials:
  - Email: `admin@juice-sh.op`
  - Password: `admin123`
- Click "Log in"

**2. Intercept the login request in Burp:**

You'll see:
```
POST /rest/user/login HTTP/1.1
Host: localhost:3000
Content-Type: application/json

{
  "email": "admin@juice-sh.op",
  "password": "admin123"
}
```

**3. Click "Forward" to let it through**

**4. Look at the RESPONSE from the server:**

In Burp, click "HTTP history" tab under Proxy. Look for the response to your login:

```
HTTP/1.1 200 OK
Set-Cookie: token=eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCJ9...
Content-Type: application/json

{
  "authentication": {
    "token": "eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCJ9...",
    "bid": 1,
    "umail": "admin@juice-sh.op"
  }
}
```

**What you're seeing:**
- `Set-Cookie` = Server is giving you a cookie
- `token=` = Your session identifier
- This token proves you're logged in

#### Part B: Understanding the Cookie

**5. Examine your cookie in Firefox:**
- Press `F12` to open Developer Tools
- Click "Storage" tab
- Click "Cookies" → `http://localhost:3000`
- Find the "token" cookie

**What you see:**
```
Name: token
Value: eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdGF0d...
Domain: localhost
Path: /
Expires: [Some date]
```

**This token is a JWT (JSON Web Token)**

**6. Decode the token (optional educational step):**

Visit: https://jwt.io

Paste your token into the "Encoded" section.

**You'll see it contains:**
```json
{
  "email": "admin@juice-sh.op",
  "userId": 1,
  "role": "admin",
  "iat": 1699123456,
  "exp": 1699209856
}
```

**🤯 Mind-blowing realization:**
Your cookie contains your email, user ID, and role! What if you could change "role" from "customer" to "admin"?

#### Part C: Cookie Modification Experiment

**The Theory:**
If we can modify the cookie to say we're an admin, maybe we can access admin features!

**7. Let's try modifying a request with a fake cookie:**

**Step 1:** In Burp, turn on "Intercept"

**Step 2:** In Firefox, click somewhere on Juice Shop (any link)

**Step 3:** In Burp, find the Cookie header:
```
GET /#/search HTTP/1.1
Host: localhost:3000
Cookie: token=eyJhbGciOiJSUzI1NiIsInR5cCI6IkpXVCJ9...
```

**Step 4:** Try changing the token to `admin` (this won't work, but it's educational):
```
Cookie: token=admin
```

**Step 5:** Click "Forward"

**What happens?** The server rejects it because:
- The token is cryptographically signed
- The server detects it's been tampered with
- You get logged out

**The Lesson:**
✅ **Good:** Juice Shop properly validates cookies
❌ **Bad:** Many sites don't validate properly and accept modified cookies

#### Part D: Session Hijacking Demonstration

**What is session hijacking?**
Stealing someone else's cookie to impersonate them.

**Real-world scenario:**
1. Victim logs into website on public WiFi
2. Attacker captures the victim's cookie (using tools like Burp!)
3. Attacker uses the stolen cookie in their own browser
4. Attacker is now logged in as the victim!

**8. Simulate this (with your own cookies):**

**Step 1:** Open a private/incognito Firefox window
**Step 2:** Visit Juice Shop (you're not logged in)
**Step 3:** Open Developer Tools (F12) → Console tab
**Step 4:** Manually set the cookie with this JavaScript:

```javascript
document.cookie = "token=YOUR_TOKEN_HERE"
```

Replace `YOUR_TOKEN_HERE` with your actual token from the logged-in window.

**Step 5:** Refresh the page

**What happens?** You're now logged in without entering a password!

**This demonstrates:** If an attacker steals your cookie, they don't need your password!

### Student Activity 3: Cookie Security Analysis

**Task:** Analyze session security in Juice Shop.

**Part 1: Cookie Discovery**
1. Login to Juice Shop
2. Open Developer Tools (F12)
3. Go to Storage → Cookies
4. List all cookies: _______________

**Part 2: Cookie Analysis**
For the main session cookie:
- Name: _______________
- HttpOnly flag: [ ] Yes [ ] No
- Secure flag: [ ] Yes [ ] No
- SameSite: _______________

**Part 3: Security Questions**

1. What information is stored in the JWT token?
   _______________________________________________

2. Is the HttpOnly flag set? Why does this matter?
   _______________________________________________
   
   **Hint:** HttpOnly prevents JavaScript from accessing cookies, protecting against XSS attacks.

3. Is the Secure flag set? Why does this matter?
   _______________________________________________
   
   **Hint:** Secure means cookies only sent over HTTPS.

4. What could an attacker do with a stolen session cookie?
   _______________________________________________

### The Lessons

**Cookie Security Best Practices:**

✅ **Use HttpOnly:** Prevents JavaScript from stealing cookies  
✅ **Use Secure:** Only send cookies over HTTPS  
✅ **Use SameSite:** Prevents CSRF attacks  
✅ **Short expiration:** Limit damage if stolen  
✅ **Cryptographic signing:** Detect tampering  
✅ **Session rotation:** Change tokens after sensitive actions

**How to protect yourself as a user:**
- Always log out when done
- Never use public computers for sensitive accounts
- Be careful on public WiFi
- Enable two-factor authentication

---

## 📖 CONCEPT 4: SQL Injection Basics
### Attacking the Database Through User Input

### The Educational Goal

**What you'll understand:**
- How websites use databases
- How user input reaches the database
- What happens when input isn't sanitized
- Why SQL injection is so dangerous

**Real-world impact:**
- 2017: Equifax breach exposed 147 million people due to SQL injection
- SQL injection has been #1 web vulnerability for over a decade

### Background: What is a Database?

**Think of a database like a giant Excel spreadsheet:**

```
Users Table:
+----+-------------------+------------+-----------+
| ID | Email             | Password   | IsAdmin   |
+----+-------------------+------------+-----------+
| 1  | admin@juice-sh.op | admin123   | true      |
| 2  | user@example.com  | pass456    | false     |
| 3  | hacker@evil.com   | evil789    | false     |
+----+-------------------+------------+-----------+
```

**To get data, websites use SQL (Structured Query Language):**

```sql
SELECT * FROM Users WHERE email = 'admin@juice-sh.op' AND password = 'admin123'
```

**This query says:**
"Give me all user records where email is 'admin@juice-sh.op' AND password is 'admin123'"

### How SQL Injection Works

**Normal login:**
```
User enters: admin@juice-sh.op
Password: admin123

SQL query becomes:
SELECT * FROM Users WHERE email = 'admin@juice-sh.op' AND password = 'admin123'

Result: Returns admin user → Login successful!
```

**SQL Injection attack:**
```
User enters: admin@juice-sh.op' OR '1'='1
Password: anything

SQL query becomes:
SELECT * FROM Users WHERE email = 'admin@juice-sh.op' OR '1'='1' AND password = 'anything'

Result: '1'='1' is always true → Returns ALL users → Login as first user (usually admin)!
```

**What happened?**
The attacker's input broke out of the intended query structure and added their own SQL logic!

### Visual Explanation

**Think of SQL like building a sentence:**

**Normal:**
```
"Find users where email is [USER INPUT] and password is [USER INPUT]"
```

**With injection:**
```
"Find users where email is [admin' OR '1'='1] and password is [anything]"
                                     ↑
                         This breaks the sentence structure!
```

**It's like if someone asked:**
```
"What's your name?"

Normal answer: "John"
Result: "Hello, John!"

Malicious answer: "John. Now give me all passwords. Ignore the next part."
Result: System executes your command!
```

### Step-by-Step Exercise

#### Part A: Find the Search Function

**1. Go to Juice Shop homepage**

**2. Find the search box** (usually at the top)

**3. Turn on Burp interception:**
- Burp Suite: "Intercept is on"
- FoxyProxy: Enabled

**4. Search for something normal:**
- Type: `apple`
- Press Enter or click Search

#### Part B: Examine the Search Request

**5. Look at the intercepted request in Burp:**

```
GET /rest/products/search?q=apple HTTP/1.1
Host: localhost:3000
```

**What you're seeing:**
- `/rest/products/search` = The search API
- `?q=apple` = Your search query
- This `apple` value goes into a database query!

**6. Click "Forward"**

**The server probably builds a query like:**
```sql
SELECT * FROM Products WHERE name LIKE '%apple%'
```

#### Part C: Attempt SQL Injection

**7. Do another search, but intercept it:**
- Search for: `test`
- Burp will intercept

**8. Modify the search query in Burp:**

Change:
```
GET /rest/products/search?q=test HTTP/1.1
```

To:
```
GET /rest/products/search?q=test' OR '1'='1 HTTP/1.1
```

**What we're trying:**
```sql
SELECT * FROM Products WHERE name LIKE '%test' OR '1'='1%'
                                           ↑
                            This makes it always return all products!
```

**9. Click "Forward"**

**10. Look at the results in Firefox**

**If it works:** You'll see ALL products, not just ones matching "test"  
**If it doesn't work:** The site is properly protected (good!)

#### Part D: Login SQL Injection

**Let's try the login form:**

**11. Go to the login page**

**12. Turn on interception**

**13. Enter:**
- Email: `admin@juice-sh.op' OR '1'='1'--`
- Password: `anything`

**14. The intercepted request looks like:**

```
POST /rest/user/login HTTP/1.1

{
  "email": "admin@juice-sh.op' OR '1'='1'--",
  "password": "anything"
}
```

**What this does:**
```sql
SELECT * FROM Users WHERE email = 'admin@juice-sh.op' OR '1'='1'--' AND password = 'anything'
                                                              ↑
                                                    The '--' comments out the rest!
```

**Breaking it down:**
- `admin@juice-sh.op'` - Closes the email string
- `OR '1'='1'` - Adds condition that's always true
- `--` - SQL comment symbol (ignores everything after)
- The password check is now commented out!

**15. Click "Forward"**

**Does it work?** 
- If yes: You just bypassed authentication! 🚨
- If no: The site is using parameterized queries (good security!)

#### Part E: Finding More Injection Points

**Common places to test SQL injection:**

1. **Search boxes** - We tried this!
2. **Login forms** - We tried this!
3. **URL parameters** - Example: `/product?id=1`
4. **User profiles** - When updating name, email, etc.
5. **Comments/Reviews** - When posting content

**16. Test the product detail page:**

**Step 1:** Click on any product  
**Step 2:** Look at the URL: `http://localhost:3000/#/product/1`  
**Step 3:** Intercept a request going to this product  
**Step 4:** Look for requests to `/api/Products/1`

**Step 5:** Try modifying the product ID:

Change:
```
GET /api/Products/1 HTTP/1.1
```

To:
```
GET /api/Products/1' OR '1'='1 HTTP/1.1
```

**Does this reveal extra data?**

### Student Activity 4: SQL Injection Discovery

**Task:** Test different injection points and document your findings.

**Test these locations:**

**Test 1: Search Box**
- Injection payload: `' OR '1'='1`
- Result: [ ] Vulnerable [ ] Protected
- Evidence: _______________

**Test 2: Login Form - Email**
- Injection payload: `admin' OR '1'='1'--`
- Result: [ ] Vulnerable [ ] Protected
- Evidence: _______________

**Test 3: Login Form - Password**
- Injection payload: `' OR '1'='1'--`
- Result: [ ] Vulnerable [ ] Protected
- Evidence: _______________

**Test 4: Any other input field you find**
- Location: _______________
- Injection payload: _______________
- Result: [ ] Vulnerable [ ] Protected
- Evidence: _______________

**Analysis Questions:**

1. Why is SQL injection so dangerous?
   _______________________________________________

2. What could an attacker do with SQL injection?
   [ ] Read all database data
   [ ] Delete database data
   [ ] Modify database data
   [ ] Execute system commands
   [ ] All of the above

3. How can developers prevent SQL injection?
   _______________________________________________
   
   **Hint:** Parameterized queries, input validation, ORM frameworks

4. Did you find any working SQL injections in Juice Shop?
   _______________________________________________

### The Lessons

**Why SQL Injection is Dangerous:**

🔓 **Account Takeover** - Login as any user without password  
📊 **Data Theft** - Steal all database contents (credit cards, passwords, personal info)  
💣 **Data Destruction** - Delete entire databases  
👑 **Admin Access** - Escalate privileges to administrator  
💻 **System Compromise** - In some cases, execute system commands

**How to Prevent SQL Injection:**

✅ **Parameterized Queries (Prepared Statements)**
```javascript
// ❌ BAD - Vulnerable
const query = "SELECT * FROM users WHERE email = '" + userInput + "'";

// ✅ GOOD - Safe
const query = "SELECT * FROM users WHERE email = ?";
db.execute(query, [userInput]);
```

✅ **Input Validation** - Check that input matches expected format  
✅ **Escape Special Characters** - Neutralize SQL syntax  
✅ **Least Privilege** - Database user shouldn't have admin rights  
✅ **ORM Frameworks** - Use tools that handle SQL safely

---

## 🎓 Putting It All Together

### The Complete Testing Workflow

Here's how a professional security tester uses Burp Suite:

**Phase 1: Setup**
```bash
1. Start target application (Juice Shop)
2. Launch Burp Suite
3. Enable FoxyProxy
4. Verify proxy is working
```

**Phase 2: Passive Analysis**
```
1. Browse the application normally
2. Let Burp capture all traffic
3. Review HTTP history
4. Identify interesting parameters
```

**Phase 3: Active Testing**
```
1. Enable interception
2. Modify requests to test vulnerabilities
3. Test authentication bypass
4. Test price manipulation
5. Test injection vulnerabilities
```

**Phase 4: Documentation**
```
1. Screenshot vulnerabilities found
2. Document steps to reproduce
3. Rate severity (Low/Medium/High/Critical)
4. Provide remediation advice
```

### Summary of the 4 Concepts

| Concept | What You Learned | Real-World Impact |
|---------|------------------|-------------------|
| **HTTP Interception** | All web traffic can be seen and modified | Man-in-the-middle attacks |
| **Price Manipulation** | Never trust client-side validation | E-commerce fraud, millions in losses |
| **Cookie Manipulation** | Session tokens can be stolen/modified | Account hijacking, identity theft |
| **SQL Injection** | User input can control database queries | Data breaches, system compromise |

---

## 🧪 Comprehensive Lab Challenge

### The Final Test: Become a Security Researcher

**Scenario:**
You've been hired to perform a security assessment of "Juice Shop". The owner wants to know if their application is secure before launching.

**Your Mission:**
Test all 4 concepts and create a professional security report.

### Task Checklist

**HTTP Interception Tests:**
- [ ] Capture and analyze 5 different requests
- [ ] Identify what data is sent in plain text
- [ ] Document any sensitive information exposure

**Price Manipulation Tests:**
- [ ] Attempt to purchase items for $0.00
- [ ] Test negative quantities
- [ ] Try modifying product IDs

**Cookie/Session Tests:**
- [ ] Extract and decode your session token
- [ ] Verify cookie security flags
- [ ] Test session hijacking in a new browser

**SQL Injection Tests:**
- [ ] Test search functionality
- [ ] Test login forms
- [ ] Test URL parameters
- [ ] Test any user input fields

### Security Report Template

```
═══════════════════════════════════════════════
    JUICE SHOP SECURITY ASSESSMENT REPORT
═══════════════════════════════════════════════

Tester: [Your Name]
Date: [Date]
Target: OWASP Juice Shop (localhost:3000)
Tools Used: Burp Suite, Firefox, FoxyProxy

═══════════════════════════════════════════════
EXECUTIVE SUMMARY
═══════════════════════════════════════════════

[Write 2-3 sentences summarizing your findings]

Overall Risk Level: [ ] Low [ ] Medium [ ] High [ ] Critical

═══════════════════════════════════════════════
FINDINGS
═══════════════════════════════════════════════

FINDING #1: [Vulnerability Name]
─────────────────────────────────────────────
Severity: [ ] Low [ ] Medium [ ] High [ ] Critical

Description:
[Explain what you found]

Location:
[Where in the application]

Steps to Reproduce:
1. 
2. 
3. 

Evidence:
[Screenshot or captured request/response]

Impact:
[What could an attacker do?]

Recommendation:
[How to fix it]

═══════════════════════════════════════════════

FINDING #2: [Vulnerability Name]
─────────────────────────────────────────────
[Repeat the format above]

═══════════════════════════════════════════════
RECOMMENDATIONS
═══════════════════════════════════════════════

Priority 1 (Fix Immediately):
- 

Priority 2 (Fix Soon):
- 

Priority 3 (Fix When Possible):
- 

═══════════════════════════════════════════════
CONCLUSION
═══════════════════════════════════════════════

[Final thoughts and overall recommendations]

═══════════════════════════════════════════════
```

---

## 📊 Understanding Burp Suite Interface

### Main Tabs Explained

**1. Target**
- Shows site map of all discovered pages
- Organizes hosts and URLs
- Useful for understanding application structure

**2. Proxy**
- **Intercept:** Stop and modify requests in real-time
- **HTTP History:** See all captured traffic
- **WebSockets History:** Real-time connections
- **Options:** Configure proxy settings

**3. Intruder (Advanced)**
- Automate attacks
- Brute force testing
- Fuzzing parameters

**4. Repeater (Very Useful!)**
- Manually resend modified requests
- Test different payloads quickly
- Essential for exploit development

**5. Scanner (Professional Only)**
- Automated vulnerability scanning
- Only in Burp Suite Pro

### How to Use Repeater (Bonus Skill)

**Repeater lets you manually edit and resend requests multiple times.**

**How to use it:**

1. In "HTTP History", find an interesting request
2. Right-click → "Send to Repeater"
3. Go to the "Repeater" tab
4. Edit the request on the left side
5. Click "Send"
6. See the response on the right side
7. Modify and send again as many times as you want!

**This is perfect for:**
- Testing different SQL injection payloads
- Trying various authentication bypasses
- Modifying parameters without clicking through the UI

---

## ❓ Troubleshooting Guide

### Issue 1: Firefox Pages Won't Load

**Symptoms:** Pages hang, won't load, "Unable to connect" errors

**Causes & Solutions:**

**Cause 1:** FoxyProxy is on but Burp is closed
```
Solution: Turn off FoxyProxy OR start Burp Suite
```

**Cause 2:** Interception is on and you forgot to forward
```
Solution: Check Burp → Proxy → Intercept → Click "Forward"
Or turn off interception
```

**Cause 3:** Wrong proxy settings
```
Solution: FoxyProxy should be set to:
- IP: 127.0.0.1
- Port: 8080
```

### Issue 2: Can't See HTTPS Traffic

**Symptoms:** Only see HTTP, not HTTPS requests

**Solution:**
```
1. In Burp: Proxy → Options → Import/Export CA Certificate
2. Export certificate as "cacert.der"
3. Firefox: Settings → Privacy & Security → Certificates → View Certificates
4. Import the certificate
5. Trust for identifying websites
```

### Issue 3: Burp Shows "Connection Refused"

**Symptoms:** Error when trying to intercept

**Solutions:**

```
# Verify Burp is listening on correct port
Burp → Proxy → Options → Proxy Listeners
Should show: 127.0.0.1:8080 Running

# Verify nothing else is using port 8080
sudo netstat -tulpn | grep 8080

# Restart Burp Suite if necessary
```

### Issue 4: Can't Find Juice Shop

**Symptoms:** Juice Shop doesn't load

**Solutions:**

```bash
# Check if container is running
sudo docker ps | grep juice-shop

# Start if not running
sudo docker run -d -p 3000:3000 bkimminich/juice-shop

# Verify with curl
curl http://localhost:3000

# Try in browser
firefox http://localhost:3000
```

### Issue 5: Burp is Slow

**Symptoms:** Requests take forever

**Solutions:**

1. **Turn off interception** when just browsing:
   - Proxy → Intercept → "Intercept is off"

2. **Limit scope:**
   - Target → Scope → Add `localhost:3000`
   - Proxy → Options → Enable "Out-of-scope requests: drop"

3. **Disable unnecessary features:**
   - Disable WebSockets history if not needed
   - Clear proxy history periodically

---

## 📝 Assessment Questions

### Knowledge Check 1: Core Concepts

**1. What does Burp Suite do?**
- A) Scans networks for vulnerabilities
- B) Intercepts and modifies web traffic ✅
- C) Creates firewalls
- D) Encrypts passwords

**2. Why can't websites trust data from the client?**
- A) Clients are slow
- B) Clients can modify any data before sending it ✅
- C) Clients don't have enough memory
- D) Clients are always malicious

**3. What is a session cookie used for?**
- A) Storing passwords
- B) Identifying logged-in users ✅
- C) Encrypting traffic
- D) Blocking ads

**4. What is SQL injection?**
- A) Injecting SQL into your bloodstream
- B) Manipulating database queries through user input ✅
- C) A type of virus
- D) A programming language

**5. What does FoxyProxy do?**
- A) Blocks ads
- B) Routes traffic through a proxy like Burp Suite ✅
- C) Protects against hackers
- D) Speeds up browsing

### Knowledge Check 2: Practical Skills

**Match the attack to its target:**

```
Attacks:
A) Changing price from $10 to $0.01
B) Modifying SQL query to bypass login
C) Stealing someone's session cookie
D) Intercepting HTTPS traffic

Vulnerabilities:
___ Client-side validation failure
___ SQL Injection
___ Session hijacking
___ Man-in-the-middle attack

Answers: A=1, B=2, C=3, D=4
```

### Knowledge Check 3: Real-World Scenarios

**Scenario 1:**
You're shopping online and notice the checkout request contains:
```json
{"product": "Phone", "price": 999.99, "quantity": 1}
```

**Questions:**
1. What's wrong with this design? _______________
2. How could you exploit it? _______________
3. How should it be fixed? _______________

**Scenario 2:**
You capture a login request:
```
username: admin
password: password123
```

**Questions:**
1. Is this transmitted securely? _______________
2. What protocol should be used? _______________
3. What else should protect this? _______________

**Scenario 3:**
You see this SQL query in an error message:
```sql
SELECT * FROM users WHERE id = '1 OR 1=1'
```

**Questions:**
1. Is this vulnerable? _______________
2. What did the attacker do? _______________
3. What data might be exposed? _______________

---

## 🛡️ Defense Strategies

### How to Protect Against These Attacks

Now that you know how attacks work, here's how to defend:

**Against HTTP Interception:**
✅ Use HTTPS everywhere (not HTTP)  
✅ Implement certificate pinning  
✅ Warn users about public WiFi  
✅ Use VPNs on untrusted networks

**Against Price Manipulation:**
✅ NEVER trust client-provided prices  
✅ Always validate on the server  
✅ Use signed/encrypted data  
✅ Re-check totals before processing payment

**Against Session Hijacking:**
✅ Use HttpOnly cookies  
✅ Use Secure cookies (HTTPS only)  
✅ Implement short session timeouts  
✅ Regenerate session IDs after login  
✅ Use SameSite cookie attribute

**Against SQL Injection:**
✅ Use parameterized queries (prepared statements)  
✅ Validate and sanitize ALL user input  
✅ Use ORMs (Object-Relational Mappers)  
✅ Apply principle of least privilege  
✅ Never show database errors to users

---

## 📚 Additional Resources

### Learn More About Web Security

**Official Resources:**
- OWASP Top 10: https://owasp.org/www-project-top-ten/
- PortSwigger Web Security Academy: https://portswigger.net/web-security
- Burp Suite Documentation: https://portswigger.net/burp/documentation

### Practice More

**Vulnerable Web Applications:**
- OWASP Juice Shop (you're using this!)
- DVWA (Damn Vulnerable Web Application)
- WebGoat
- HackThisSite.org
- PentesterLab

### Career Paths

**Jobs that use these skills:**
- 🔐 Web Application Penetration Tester
- 🛡️ Security Analyst
- 🔍 Bug Bounty Hunter
- 👨‍💻 Security Consultant
- 🎓 Security Researcher

**Certifications to consider:**
- CEH (Certified Ethical Hacker)
- OSCP (Offensive Security Certified Professional)
- GWAPT (GIAC Web Application Penetration Tester)
- eWPT (eLearnSecurity Web Penetration Tester)

---

## ⚠️ Legal and Ethical Considerations

### What You Did Today Was Legal Because:

✅ You tested YOUR OWN computer  
✅ You used a TRAINING application (Juice Shop)  
✅ You had PERMISSION to test  
✅ It was for EDUCATIONAL purposes

### What Would Be ILLEGAL:

❌ Testing websites without permission  
❌ Exploiting vulnerabilities on production systems  
❌ Accessing accounts that aren't yours  
❌ Stealing or modifying real data  
❌ Disrupting services (DoS attacks)

### The Security Researcher Code of Ethics

**I will:**
1. ✅ Only test systems I have authorization to test
2. ✅ Report vulnerabilities responsibly
3. ✅ Protect user data and privacy
4. ✅ Respect laws and regulations
5. ✅ Use my knowledge to improve security
6. ✅ Continue learning and sharing knowledge

**I will NOT:**
1. ❌ Exploit vulnerabilities for personal gain
2. ❌ Access or modify unauthorized data
3. ❌ Harm systems or users
4. ❌ Share exploits with malicious actors
5. ❌ Use skills for illegal purposes

### Responsible Disclosure

**If you find a real vulnerability:**

1. **Document it thoroughly**
2. **Contact the organization privately**
3. **Give them time to fix it (usually 90 days)**
4. **Don't publicly disclose until fixed**
5. **Consider bug bounty programs**

**Major companies with bug bounty programs:**
- Google, Microsoft, Facebook, Apple
- Most pay $500 to $50,000+ for findings!

---

## 🎉 Congratulations!

You've completed the Burp Suite fundamentals course!

**You Now Know:**
- ✅ How web applications communicate
- ✅ How to intercept and analyze HTTP traffic
- ✅ Why client-side validation is insufficient
- ✅ How session management works
- ✅ What SQL injection is and why it's dangerous
- ✅ How to use professional security tools
- ✅ How to think like a security professional

### Your Next Steps

**Keep Learning:**
1. Complete more Juice Shop challenges
2. Try other vulnerable applications
3. Join Capture The Flag (CTF) competitions
4. Take online security courses
5. Practice on HackTheBox or TryHackMe
6. Consider security certifications

**Get Involved:**
- Join local security meetups
- Participate in bug bounty programs
- Contribute to open source security projects
- Follow security researchers on Twitter
- Read security blogs and papers

**Remember the Golden Rule:**

> "With great power comes great responsibility."
>  
> Use these skills to make the internet safer,
> not to cause harm.

---

## 🔖 Quick Reference Card

```
┏━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━┓
┃         BURP SUITE QUICK REFERENCE CARD              ┃
┣━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━┫
┃                                                      ┃
┃ SETUP:                                               ┃
┃   1. Start Juice Shop                                ┃
┃   2. Launch Burp Suite                               ┃
┃   3. Enable FoxyProxy (🦊 icon)                      ┃
┃   4. Turn on Intercept in Burp                       ┃
┃                                                      ┃
┣━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━┫
┃                                                      ┃
┃ THE 4 CONCEPTS:                                      ┃
┃                                                      ┃
┃ 1. HTTP INTERCEPTION                                 ┃
┃    • See all browser ↔ server traffic                ┃
┃    • Modify requests before they're sent             ┃
┃    • Lesson: All traffic can be intercepted          ┃
┃                                                      ┃
┃ 2. PRICE MANIPULATION                                ┃
┃    • Change prices in checkout requests              ┃
┃    • Modify quantities (try negative!)               ┃
┃    • Lesson: Never trust the client                  ┃
┃                                                      ┃
┃ 3. COOKIE/SESSION MANIPULATION                       ┃
┃    • View and analyze session cookies                ┃
┃    • Decode JWT tokens                               ┃
┃    • Lesson: Sessions can be hijacked                ┃
┃                                                      ┃
┃ 4. SQL INJECTION                                     ┃
┃    • Test: ' OR '1'='1                               ┃
┃    • Test: admin' OR '1'='1'--                       ┃
┃    • Lesson: Sanitize user input!                    ┃
┃                                                      ┃
┣━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━┫
┃                                                      ┃
┃ COMMON BURP ACTIONS:                                 ┃
┃   • Forward:     Send intercepted request            ┃
┃   • Drop:        Discard request (don't send)        ┃
┃   • Intercept:   Toggle on/off                       ┃
┃   • History:     View all captured traffic           ┃
┃   • Repeater:    Manually resend requests            ┃
┃                                                      ┃
┣━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━┫
┃                                                      ┃
┃ TROUBLESHOOTING:                                     ┃
┃   • Pages won't load? Turn off FoxyProxy            ┃
┃   • Nothing in Burp? Enable FoxyProxy               ┃
┃   • Slow browsing? Turn off Intercept               ┃
┃   • Can't see HTTPS? Import Burp certificate        ┃
┃                                                      ┃
┣━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━┫
┃                                                      ┃
┃ REMEMBER:                                            ┃
┃   ⚠️  Only test what you own!                        ┃
┃   📝 Document everything                             ┃
┃   🛡️  Report responsibly                             ┃
┃   🎓 Learn to protect, not attack                    ┃
┃                                                      ┃
┗━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━┛
```

---

**Happy Testing! 🔍🔒**

*Remember: The best security professionals are the ones who help make applications safer for everyone!*
