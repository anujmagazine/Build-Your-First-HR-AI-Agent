# P1 – Build your first basic automation (Automated daily weather workflow)

## Workflow Overview
**Purpose:** Automatically fetch daily weather data and send a weather report email.

**Trigger:** Daily schedule (`Schedule Trigger`)

**Nodes:**
1. Trigger workflow at a fixed time
2. Fetch current weather data
3. Send weather report email

---

## Node 1: Trigger daily workflow
**Type:** `Schedule Trigger (n8n-nodes-base.scheduleTrigger)`

**Purpose:** Starts the workflow automatically at a fixed time every day.

| Parameter     | Value           |
|--------------|-----------------|
| **Mode**     | Every Day       |
| **Time**     | 07:00 AM        |
| **Timezone** | Local timezone  |

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
| Parameter          | Value                          |
|-------------------|--------------------------------|
| **Credentials**   | Select your saved OpenWeatherMap credential |
| **Location Type** | ZIP Code                       |
| **ZIP Code**      | 122001, IN                     |
| **Units**         | Metric (°C)                    |
| **Data Returned** | Temperature, humidity, min/max |


## Node 3: Send weather report email
**Type:** `Gmail – Send a message (n8n-nodes-base.gmail)`

**Purpose:** Sends a daily weather summary email.

| Parameter        | Value                                      |
|------------------|--------------------------------------------|
| **To**           | your-email@gmail.com                       |
| **Subject**      | Weather report for today                   |
| **Email Body**   | Min Temp: {{ $json.main.temp_min }}<br>Max Temp: {{ $json.main.temp_max }}<br>Humidity: {{ $json.main.humidity }} |

---

## Output
One automated email sent daily with the current weather report.

