# Phase 2: Visual Analysis with a SIEM Dashboard
In this Phase we are going to use a SIEM platform to collect and visualize data from both the victim and honeypot
environments.

## Step 1: Setup Splunk
<img src="./Screenshots/Splunk%20Setup%20Process.png" alt="Set up Splunk" width="500" height="500"><img/>

## Step 2: Opening Splunk
<img src="./Screenshots/Openning%20Splunk.png" alt="Openning Splunk" width="1000" height="500"><img/>

## Step 3: Creating Logs file from the Attacker (Kali)
<img src="./Screenshots/Creating%20Kali%20Logs%20File.png" alt="Creating Kali Logs File" width="800" height="200"><img/>

## Step 4: Getting access.log file from the Victim (Metasploitable3)
<img src="./Screenshots/Getting%20the%20access.log%20from%20the%20Victim.png" alt="Getting access.log from the Victim" width="800" height="200"><img/>

## Step 5: Ensure both logs appears in the Attacker Machine to be uploaded in Splunk
<img src="./Screenshots/Logs%20for%20both%20Kali%20and%20Metasploit%20appears%20in%20the%20downloads..png" alt="Both Logs Up appears in Downloads" width="700" height="400"><img/>

## Step 6: Uploading Logs into Splunk
<img src="./Screenshots/Both%20logs%20uploaded%20successfully..png" alt="Both Logs Up Uploaded Successfully" width="700" height="400"><img/>

## Step 7: Visual Analysis
Now after we have uploaded both logs into the platform, we can perform different search queries on the logs to see result and analyze them. 
### 7.1 Attack Patterns
To detect potential malicious behavior, we ran pattern analysis on the access_combined logs. Splunk’s pattern detection grouped similar request patterns together, making it easier to visually spot suspicious actions. <br> <br>
<img src="./Screenshots/Access%20combined%20output.png" alt="Access combined output" width="700" height="400"><img/>

### Findings:
* The requests to exploit.php and were repeatedly triggered, suggesting automation tools or scripts were involved.
* The pattern reveals constant interaction with chat logs (/chat/read_log.php), likely as part of a payload delivery or exfiltration mechanism.

### 7.2 Most Accessed IPs
We want to see the most IPs that accessed the Victim Machine. <br><br>
<img src="./Screenshots/Top%20Ip%20accessers%20for%20to%20the%20system.png" alt="Top Ip accessors to the system" width="700" height="400"><img/>

### Findings:
* The IP **192.168.100.5** (our known attacker machine) had an extremely high volume of requests. This clearly links the attacker to the reverse shell activity and payload delivery.

### 7.3 HTTP Status Code
To determine how the web server responding to requests either normal or malicious we see the HTTP Responses Codes. <br><br>
<img src="./Screenshots/Status%20codes%20visualization.png" alt="Status Code Visualization" width="800" height="400"><img/>

### Findings:
* 200 (OK) – 6258 responses The most common status code, indicating successful responses. This suggests the attacker’s requests were processed without issues.

* 404 (Not Found) – 6 responses The attacker likely tried accessing endpoints that don’t exist. These 404s might indicate scanning behavior—looking for vulnerabilities or unlisted files.

### 7.4 Top Visited URI Paths
By analyzing which URI paths were most frequently accessed, we can infer the attacker’s focus and behavior. <br> <br>
<img src="./Screenshots/Top%20visited%20endpoints.png" alt="Top visited URI Paths" width="800" height="400"><img/>

### Findings:
* /chat/read_log.php – 6141 hits This endpoint was the most accessed. It strongly suggests automated behavior (e.g., repeated log reading or extraction).

### 7.5 Data Comparisons
<img src="./Screenshots/For%20comparison%20purposes%201.png" alt="Comparison Purposes"><img/>
<img src="./Screenshots/For%20comparison%20purposes%202.png" alt="Comparison Purposes"><img/>

### Findings:
* Both sources have a high volume of events in the selected time window, indicating a period of active monitoring or attack.
* The attacker machine has an indicators for accessing read_log.php endpoint which shows an attacker behaviour into the victim machine.


## Queries Explaination
### Attack Pattern Query
`index='main' sourcetype='access_combined' clientip='192.168.100.5'` <br>
**Explaination**: This query searches for all web access log events (sourcetype='access_combined') in the main index where the client IP matches 192.168.100.5. This is useful for identifying all actions performed by a specific attacker or suspicious IP, allowing analysts to trace the sequence and nature of the attack from that host.

### Most Accessed IPs
`index='main' sourcetype='access_combined' | top limit=10 clientip` <br>
**Explaination**: This query retrieves the top 10 most frequently appearing client IP addresses from the web access logs. It helps to identify which IPs are most active on the server and may reveal sources of automated scanning, attacks, or legitimate heavy usage patterns.

### HTTP Status Code
`index='main' sourcetype='access_combined' | stats count by status` <br>
**Explaination**: This query aggregates the count of HTTP response status codes (such as 200, 404, 500, etc.) found in the access logs. Analyzing status codes provides insight into the overall health of the web application, frequency of errors, and can help spot anomalies or attack attempts (e.g., many 404s or 500s).

### Top Visited URI Paths
`index='main' sourcetype='access_combined' | top limit=10 uri_path` <br>
**Explaination**: This query finds the top 10 most requested URI paths in the access logs. This information highlights which resources or endpoints are most accessed—potentially indicating popular features, attack targets, or vulnerable endpoints being probed.

### Data Comparisons
`(index=*) sourcetype="access_combined" | table _time, sourcetype, host, user, message, src, dest, uri | sort _time` <br>
`(index=*) sourcetype="kali" | table _time, sourcetype, host, user, message, src, dest, uri | sort _time` <br>
**Explaination**: The first query extracts and sorts detailed events from the access_combined (web access) logs, while the second does the same for the kali (system logs from the Metasploitable3 machine). By comparing these outputs side by side, analysts can correlate web requests made by an attacker (from access logs) with system-level events on the target machine, revealing the impact of attacks and enabling comprehensive incident analysis.

