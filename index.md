# Creating a Threat Intelligence Feed

## Introduction

Thinking back to my SIEM from earlier, I configured it so that it would create an alert when it detects a successful RDP login. Now that is only one form of attack it can detect from a whole plethora of cyber attacks. Theoretically, I can make my SIEM "smarter" by configuring it to detect nearly every form of cyber attack under the digital sun. That could work, but in a real-world setting, this idea is way too time-consuming and resource-intensive. More than that, it would put you behind the attackers. Creating rules for known cyber attacks is purely reactive behavior. To make our security posture as effective as possible, we want to create a real-time data stream that informs our security tools about indicators of malicious behavior such as unusual domains, malware signatures, or IP addressess associated with known threat actors (CrowdStrike). This is where threat intelligence feeds come in. Rather than purely being on the defensive, we can be proactive and detect potential threats before they arise while minimizing effort on the user's part. In this lab, I will be creating a simple threat intelligence feed that collects known malicious IP addresses and displays them in an easy-to-read format.

## Setting Up the Tools

This threat intelligence feed will be fairly barebones, but still nonetheless effective at what it is meant to do. For this lab, I only need three things: Python, VS Code, and an API key from AbuseIPDB. For reference, AbuseIPDB is a community-based IP blacklist database where users can report IP addresses that they deem malicious (Mindflow). This will help us enourmously as we will be pulling from this database to determine our malicious IP addresses. This data, in turn, can be fed to our cybersecurity tools so they will quickly know what to flag. Python and VS Code are already installed on my computer, but I can demonstrate how to obtain a free API key from AbuseIPDB.

- _Figure 1_: The homepage for AbuseIPDB. Our point of interest is registering for an API key.

<p align="center">
  <img width="384" height="384" src="assets/fig1.png">
</p>

After entering my information and creating my account, I can now go to the "API" tab and create my key.

- _Figure 2_: The user account homepage with the API tab open.
<p align="center">
  <img width="384" height="384" src="assets/fig2.png">
</p>

For this lab, I will keep my key name simple. The name "ThreatFeedProject" will do. Upon clicking "Create", my API key is generated. Now that I have everything I need, I can now open VS Code and begin my Python program.

## Creating the Python File

With my API key, I can now use my program to pull information from AbuseIPDB's database. Recalling that my goal here is to collect known malicious IP addresses and display them, I know that my program should do the following: Connect with AbuseIPDB, pull a list of malicious IP addresses, and display them in a formatted manner. Here is the first draft of my code:

```js

import requests

API_KEY = "my_abuseipdb_api_key"
URL = "https://api.abuseipdb.com/api/v2/blacklist"

headers = {
    "Accept": "application/json",
    "Key": API_KEY
}

response = requests.get(URL, headers=headers)
data = response.json()

# Print the first 10 malicious IPs

print("🔴 Malicious IPs Found:")
for ip in data ['data'][:10]: # Limit to 10 results
    print(f"{ip['ipAddress']} - Confidence Score : {ip['abuseConfidenceScore']}")

```
