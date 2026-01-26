# Project 1 – Build your first basic automation (Automated daily weather workflow)

## Workflow Overview
**Purpose:** Automatically fetch daily weather data and send a weather report email.

**Trigger:** Daily schedule (`Schedule Trigger`)

**workflow Steps:**
1. Trigger workflow at a fixed time
2. Fetch current weather data
3. Send weather report email

In this case, one step = one node
---

## Node 1: Trigger daily workflow
**Type:** `Schedule Trigger (n8n-nodes-base.scheduleTrigger)`

**Purpose:** Starts the workflow automatically once every day at a fixed time.

### Configuration steps
1. Add a **Schedule Trigger** node.
2. Set **Trigger Interval** to **Days**.
3. Set **Days Between Triggers** to **1**.
4. Set **Trigger at Hour** to **7 AM**.
5. Set **Trigger at Minute** to **0**.

### Node configuration
| Parameter                  | Value |
|---------------------------|-------|
| **Trigger Interval**      | Days  |
| **Days Between Triggers** | 1     |
| **Trigger at Hour**       | 7 AM  |
| **Trigger at Minute**     | 0     |


---

## Node 2: Fetch current weather
**Type:** `OpenWeatherMap (n8n-nodes-base.openWeatherMap)`

**Purpose:** Retrieves today’s weather data for a given city or ZIP code.

### Step 1: Create an OpenWeather API key
1. Open the OpenWeather site and go to the API keys page: https://home.openweathermap.org/api_keys
2. Sign up or log in.
3. Click **Create key** (or **Generate**).
4. Give it a name like `n8n-weather`.
5. Copy the API key.
6. Wait a few minutes if it does not work immediately (new keys can take a short time to activate).

### Step 2: Save the API key inside n8n (Credentials)
1. In n8n, go to **Credentials**.
2. Click **Add credential**.
3. Search for and select **OpenWeatherMap API** (or **OpenWeatherMap**).
4. Paste your API key into the **API Key** field.
5. Click **Save**.
6. Click **Test** (if you see a test button) to confirm it works.

### Step 3: Configure Node 2 (OpenWeatherMap)
### Node configuration
| Parameter                    | Value                          |
|-----------------------------|--------------------------------|
| **Credential to connect with** | OpenWeatherMap API (saved credential) |
| **Operation**               | Current Weather                |
| **Format**                  | Metric                         |
| **Location Selection**      | ZIP Code                       |
| **ZIP Code**                | 400051, IN (Jio World Centre, Mumbai) |
| **Language**                | en                             |



## Node 3: Send weather report email
**Type:** `Gmail – Send a message (n8n-nodes-base.gmail)`

**Purpose:** Sends a daily weather summary email using your Gmail account.

### Setup steps (novice friendly)
1. Add a **Gmail** node and choose **Send a message**.
2. In **Credential to connect with**, select your Gmail credential.
   - If you do not have one yet: click the pencil icon or create a new credential, then sign in to Gmail and allow access.
3. Set **Resource** to **Message**.
4. Set **Operation** to **Send**.
5. In **To**, enter the email address that should receive the weather report.
6. In **Subject**, enter a clear subject line (example: `Mumbai weather report – today`).
7. Set **Email Type** to **HTML** (so line breaks render cleanly).
8. In **Message**, paste the email body template below.
9. Click **Execute step** once to test and confirm you received the email.

### Field values to use
| Field                         | What to enter |
|------------------------------|---------------|
| **Credential to connect with** | Select your Gmail credential (example: `Gmail account 2`) |
| **Resource**                 | Message |
| **Operation**                | Send |
| **To**                       | Your email (example: `anuj@example.com`) |
| **Subject**                  | `Mumbai weather report – today` |
| **Email Type**               | HTML |
| **Message**                  | Use the template below |

### Message template (copy paste)
```html
<b>Mumbai weather (Jio World Centre area)</b><br><br>
Min Temp: {{ $json.main.temp_min }} °C<br>
Max Temp: {{ $json.main.temp_max }} °C<br>
Humidity: {{ $json.main.humidity }} %<br>
Weather: {{ $json.weather[0].description }}<br>


---

## Output
One automated email sent daily with the current weather report.

