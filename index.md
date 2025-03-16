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

With my API key, I can now use my program to pull information from AbuseIPDB's database. Recalling that my goal here is to collect known malicious IP addresses and display them, I know that my program should do the following: Connect with AbuseIPDB, pull a list of malicious IP addresses, and display them in a formatted manner. First I will provide my code, then I will give a line-by-line breakdown. Here is the first draft of my code:

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

We begin with "import requests." This line tells Python to link to the "requests" module. This is so we can send API requests to AbuseIPDB. 

Next we define two variables. The first one is the API key I obtained from AbuseIPDB. Now my actual key is much longer and more complex, but for the purpose of this report (and to not give away my API key), I have replaced the actual key with "my_abuseipdb_api_key". This key is important because it is what authorizes my program to access AbuseIPDB. The second variable is "URL" which stores the AbuseIPDB API URL for checking an IP. We use this variable as a pointer so that when we plug it into a function, the variable can tell the function where to find the data.

Next we define a dictionary named "headers". In our program, we are communicating with AbuseIPDB via HTTPS, which is facilitated through HTTPS headers. Therefore, our "headers' dictionary formats our API requests so that they are readable via HTTPS and we can define what we want in the API request. In this case, the "Accept: "application/json" key-value pair specifies that we want our data in JSON format, and the "Key: API_KEY" key-value pair authenticates our request with my API key. This dictionary is what we will use to build the header of our HTTPS API request.

We have our header sorted out, but now we need to build the request line of the HTTPS request. Here, we need to specify the method of our HTTPS request. In this case, we are trying to retrive data from AbuseIPDB, so we will use the GET method. Our request will use the "requests.get()" function with our "URL" variable specifying the API endpoint URL, and our "headers" dictionary specifying the values for the HTTPS API request header. What we receive from this request is stored in the "response" variable. The next variable, "data", takes our "response" and parses its raw JSON format into a Python dictionary that we can use.

With all this combined, our full HTTPS API request looks like this:

- Request Line
  >
  >GET /api/v2/blacklist HTTP/1.1

- Headers
  > Host: api.abuseipbdb.com
  > Accept: application/json
  > Key: (my AbuseIPDB API key)
  > User-Agent: python-requests/2.31.0

All right, now all of this runs and we send our request. AbuseIPDB responds and now we have our data, but we cannot necessarily see it. To prove that this program is working and that it is retrieving data, we will have it print out the results. I imagine the amount of data stored in our "data" variable is quite massive, but to keep it simple, we will only print the first 10 lines. First we will print a sort of header, so we know what the data printing out is for. I simply use the "print" fuction to print "🔴 Malicious IPs Found:". The red circle emoji is merely for visual emphasis.

Now for the actual body of our printed message. We are only interested in the first 10 IP addresses retrieved from AbuseIPDB. 
