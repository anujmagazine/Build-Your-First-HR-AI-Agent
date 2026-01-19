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

| Parameter          | Value                          |
|-------------------|--------------------------------|
| **Location Type** | ZIP Code                       |
| **ZIP Code**      | 122001, IN                     |
| **Units**         | Metric (°C)                    |
| **Data Returned** | Temperature, humidity, min/max |

---

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

