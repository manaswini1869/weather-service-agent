# ServiceAgent Weather Workflow

This Workflow automates retrieves data for a selected city, generates a formatted daily weather summary, stores the result in Superbase, and sends the summary to a recipient email address.

# Configurations

## API Setup and Key Configurations

This workflow uses OpenWeather API for both geolocation and weather retrieval.

### Steps:

1. Create a free account at [openweathermap.org](https://openweathermap.org/api)
2. Generate an API key (“API Key” under your profile).
3. In n8n, go to: Settings → Variables → Add Variable
4. Add:
```
OPENWEATHER_API_KEY = your_api_key_here
```

The workflow reads it using:

```
{{ $vars.OPENWEATHER_API_KEY }}
```

openweathermap API Doc : https://openweathermap.org/api/one-call-3

## Supabase Details

The workflow inserts daily weather logs into a Supabase table.

### Steps

1. Create a Supabase project: https://supabase.com/dashboard
2. Go to Table Editor → Create Table
3. Create weather_logs table with the following columns:

```
| Column           | Type                              |
| ---------------- | --------------------------------- |
| id               | uuid (default uuid_generate_v4()) |
| run_at           | timestamp                         |
| city             | text                              |
| temperature      | float8                             |
| temperature_unit | text                              |
| condition        | text                              |
| humidity         | int8                               |
| wind_speed       | float8                            |
| alert_type       | text                              |
| raw_response     | json                              |
```
My workflow uses auto-mapped input, so make sure these columns are created properly.

### Connect n8n

In n8n:
1. Go Settings → Credentials → Create Supabase Credential
2. Enter:
    - Base URL
    - Service Role or anon key

You can visit this URL for additional Supabase n8n setup information : [n8n Supabase credentials](https://docs.n8n.io/integrations/builtin/credentials/supabase/?_gl=1*1uh5u5m*_gcl_au*NzMwOTY1NjE5LjE3NjUxMzc4NTA.*_ga*NTI3MTE3MjgwLjE3NjUxMzc4NTE.*_ga_0SC4FF2FH9*czE3NjUyMzc4ODYkbzckZzAkdDE3NjUyMzc4ODYkajYwJGwwJGgw)

You can also visit the Supbase docs for additional information : [Supabase docs](https://supabase.com/docs)

## Email Configurations

The workflow sends the user a daily weather report via Gmail.

### Steps

1. Go to: n8n-> Credentials-> Add New -> Gmail OAuth2 API.
2. Follow the Google OAuth login process.
3. In the workflow, Gmail node should be configured with Credentials. Edit the Gmail node `Credential to Connect with` and select the newly created credential.

## Import and Run Workflow

### Import
1. In n8n, click Workflows -> Import from file
2. Paste or Upload the `ServiceAgent Weather Workflow.json` file.
3. Ensure:
    - OPENWEATHER_API_KEY variable exists
    - In the `Edit Field` node, configure the
        - City
        - countryCode
        - units
        - temperatureUnits (make sure they match the metric)
        - recipientEmail.
    - Supabase credentials are configured in the `Create a row` node.
    - Gmail credentials are configured in the `Send a message` node.

    (Follow above steps for configuration instructions)

### Run
You can run the workflow in two ways:

#### Manual Run
Click `Execute Workflow` in the n8n editor

#### Scheduled Run
- The Workflow include a `Schedule Trigger`.
- Set the trigger based on you taste, by default it is configure to run Everyday at Midnight. ([Schedule Trigger node docs](https://docs.n8n.io/integrations/builtin/core-nodes/n8n-nodes-base.scheduletrigger/?utm_source=n8n_app&utm_medium=node_settings_modal-credential_link&utm_campaign=n8n-nodes-base.scheduleTrigger#schedule-trigger-node))

Once active, n8n will:
1. Fetch the geolocation for the configured City
2. Fetch weather conditions
3. Generate a formatted summary
4. Save the log to Supabase
5. Email the summary to the Recipient Email

# Demo Video
You can also look at my execution of the workflow through the video file - `./ServiceAgent Assignment.mp4`.

In the video the trigger is scheduled to run every 10 secs.
