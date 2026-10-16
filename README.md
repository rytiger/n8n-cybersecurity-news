# Cybersecurity News Automation with n8n
A simple n8n workflow that collects cybersecurity news from different websites and sends it to a Telegram channel.

## Table of Contents
-[How-It-Works](#how-it-works)
Priority Classification
News Sources
Telegram Setup
Before Running
Using the Workflow for Other Topics
Notes

How It Works
Collects news from selected websites.
Filters articles by date.
Assigns a priority level based on keywords in the article title.
Sends the results to Telegram, including the article title, publication date, source website, a short description and a link to the original article.
Priority Classification
The workflow uses keywords in article titles to assign one of four priority levels:
🔴 Critical — Keywords such as zero-day, ransomware, active exploit, critical and RCE.
🟠 High — Keywords such as CVE, vulnerability, patch and exploit.
🟡 Medium — Keywords related to security research.
⚪ Low — The default level when none of the configured keywords match.
This classification is based on keywords, not AI analysis. It may not always reflect the actual severity or context of an article.
News Sources
The repository includes RS Sources.csv, which contains the news sources and their URLs.
You can edit the CSV file to add or remove websites. The sources don’t have to be cybersecurity-related; you can use the workflow for other topics too.
After updating the CSV file, make sure the records in your n8n Data Table are updated as well. The Get Row(s) node uses this data to retrieve the sources.
Telegram Setup
1. Create a Telegram Bot
Open Telegram and search for @BotFather (https://t.me/BotFather).
Send /newbot and follow the instructions to choose a name and username.
BotFather will give you a bot token. Keep it private, as anyone with this token may be able to control your bot.
2. Create a Telegram Channel
If you don’t already have a channel:
Create a new channel in Telegram.
Choose a name and set it to public or private, depending on your preference.
Open the channel settings and select Administrators.
Add your bot as an administrator and allow it to post messages.
3. Get Your Channel Chat ID
You need the channel’s chat ID, not your personal Telegram chat ID or the bot’s ID.
Make sure your bot is an administrator of the channel.
Post a test message in the channel.
Open the following URL in your browser, replacing YOUR_BOT_TOKEN with your actual bot token:
https://api.telegram.org/botYOUR_BOT_TOKEN/getUpdates

Look for channel_post in the response.
Find the chat object and its id field. Channel IDs often start with -100.
For example:
{
  "chat": {
    "id": -1001234567890,
    "title": "My News Channel",
    "type": "channel"
  }
}
In this example, the channel chat ID is -1001234567890.
Note: If the response is empty or doesn’t contain channel_post, check that your bot is an administrator and that a new channel post has been made. If a webhook is configured for the bot, getUpdates won’t work until the webhook is removed.
Security: The URL contains your bot token. Don’t share it or include it in screenshots or GitHub files.
4. Configure n8n
Open the workflow in n8n.
Open the Telegram node and create a Telegram API credential.
Enter your bot token and save the credential.
Set the Telegram chat ID to your channel’s chat ID.
Test the workflow and confirm that a message arrives in your channel.
Before Running
Check your credentials and node settings.
Make sure the Telegram chat ID is correct.
Check the Filter node and set a recent date, such as today or yesterday.
Tip: Avoid starting with an old date. Retrieving too many articles at once can cause Too Many Requests errors and stop the workflow. If this happens, try a more recent date or a smaller date range.
Using the Workflow for Other Topics
The workflow can also be used for general news or other topics.
To remove the cybersecurity priority labels:
Open the workflow in n8n.
Find the Code node that formats the Telegram message.
Replace its JavaScript code with the alternative code below.
Save the workflow and test it.

The alternative code keeps the article title, publication date, source website, description and original link, but removes the priority classification.
Alternative JavaScript Code
Replace the existing code in the relevant Code node with this:
const title = $json.title;
const link = $json.link;
const content = $json.content;
const website_tag = $json.website_tag;

const date = new Date($json.date).toLocaleString("en-GB", {
  day: "2-digit",
  month: "short",
  year: "numeric",
  hour: "2-digit",
  minute: "2-digit"
});

const shortContent =
  content.length > 450
    ? content.substring(0, 450) + "..."
    : content;

const message = `
📌 ${title}

${date}
${website_tag}

📄 Description:
${shortContent}

🔗 Read more:
${link}
`;

return {
  json: {
    message
  }
};


Notes
The workflow is intended to be customised.
You can change the source websites, date filter and JavaScript code to suit your needs.
Keep your bot token and other secrets private. Don’t upload them to GitHub.
