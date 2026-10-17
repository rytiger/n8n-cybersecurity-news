# Cybersecurity News Automation with n8n
This project uses n8n to collect cybersecurity news from different websites and send it to a Telegram channel. It filters articles by date and sends each article with its title, publication date, source website, a short description and a link to the original article.

## Table of Contents

-[Priority Classification](#priority-classification)  
-[Example Output](#example-output)  
-[News Sources](#news-sources)   
-[Telegram Setup](#telegram-setup)   
-[Using the Workflow for Other Topics](#using-the-workflow-for-other-topics)  
-[Notes](#notes)  
 

## Priority Classification  
The workflow uses keywords in article titles to assign one of four priority levels:  
🔴 Critical — Keywords such as zero-day, ransomware, active exploit, critical and RCE.  
🟠 High — Keywords such as CVE, vulnerability, patch and exploit.  
🟡 Medium — Keywords related to security research.  
⚪ Low — The default level when none of the configured keywords match.  
This classification is based on keywords, not AI analysis. It may not always reflect the actual severity or context of an article.  

## Example Output
<img width="400"  alt="example" src="https://github.com/user-attachments/assets/47b6a52e-3ca2-4979-9b82-8075fdf9697f" />


## News Sources
The repository includes rss_sources.csv, which contains the news sources and their URLs.  
You can edit the CSV file to add or remove websites. The sources don’t have to be cybersecurity related; you can use the workflow for other topics too.  
After updating the CSV file, make sure the records in your n8n Data Table are updated as well. The Get Row(s) node uses this data to retrieve the sources.  


## Telegram Setup
1. Create a Telegram Bot  
Open Telegram and search for @BotFather.  
Send /newbot and follow the instructions to choose a name and username.  
BotFather will give you a bot token.


3. Create a Telegram Channel  
After creating a new channel in Telegram.   
Open the channel settings and select Administrators.  
Add your bot as an administrator and allow it to post messages.

5. Get Your Channel Chat ID  
You need the channel’s chat ID, not your personal Telegram chat ID or the bot’s ID.   
Make sure your bot is an administrator of the channel.  
Post a test message in the channel.  
Open the following URL in your browser, replacing YOUR_BOT_TOKEN with your actual bot token:  
https://api.telegram.org/botYOUR_BOT_TOKEN/getUpdates  

Look for channel_post.  
Find the Channel ID; it often starts with -100  

 
Note: If the response is empty, please post a short test message in your Telegram channel and then check the URL again to find your channel ID.  


4. Configure n8n  
Open the workflow in n8n.  
Open the Telegram node and create a Telegram API credential.  
Enter your bot token and save the credential.  
Set the Telegram chat ID to your channel’s chat ID.  
Test the workflow and confirm that a message arrives in your channel.

Important note:
Check the date filter before executing the workflow.

Open the Filter node and set the date to today or yesterday, depending on which articles you want to collect.

Using an older date may retrieve a large number of articles, causing the workflow to send too many messages to Telegram in a short period. Telegram has limits on how frequently messages can be sent, which can result in a Too Many Requests error.



## Using the Workflow for Other Topics  
You can also use the workflow for general news or other topics.  

To remove the cybersecurity priority labels:  
Open the workflow in n8n.  
Find the Code node that formats the Telegram message.  
Replace its JavaScript code with the alternative code below.  
Save the workflow and test it.  


### Alternative JavaScript Code  
Replace the existing code in the relevant Code node with this:  
``` javascript
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

```


## Notes  
The workflow is intended to be customised.  
You can change the source websites, date filter and JavaScript code to suit your needs.  


## Author  
@rytiger
