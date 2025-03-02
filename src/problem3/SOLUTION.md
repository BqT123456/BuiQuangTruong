Provide your solution here:

Step 1: Verify the High Memory Usage
- Possible Causes/Scenarios:

	+ Incorrect Monitoring Data: Monitoring tools may be misreporting memory usage due to errors or misconfigurations.

	+ Transient Memory Spike: A temporary increase in memory usage that's no longer present.

- Impacts:

	+ Misleading Information: Acting on inaccurate data could lead to unnecessary troubleshooting efforts.

- Troubleshoot steps:

	+ Manually Check Memory Usage: free -h
	Purpose: Confirms current memory usage, including total, used, and available memory.

	+ Review System Processes: top -b -o +%MEM | head -n 15
	Purpose: Identifies the top processes consuming memory.

- Recovery steps: This step is carried only if there is nothing weird in above "Troubleshoot Steps" 
	+ Validate Monitoring Tools: Ensure that monitoring agents are functioning correctly.
	+ Reconfigure or update monitoring tools if faults are found.

Step 2: Identify Top Memory-Consuming Processes
- Possible Causes/Scenarios:

	+ Zombie or Defunct Processes: Orphaned processes holding onto memory.
	Refer: https://github.com/kubernetes/ingress-nginx/issues/2807

	+ Unexpected Processes Running: Other services are consuming memory.

- Impacts:

	+ Resource Depletion: Non-essential processes reduce the memory available for NGINX.

	+ Performance Issues: NGINX may not perform optimally due to limited resources.

- Troubleshoot steps:

	+ List Processes by Memory Usage: ps aux --sort=-%mem | head -n 10
	Purpose: Displays the top processes using memory.
	
	+ List Processes and find Z status: ps aux | grep Z
	Purpose: Find if there is any zombie process.
	![Zombies](https://github.com/user-attachments/assets/020f3327-98b6-41d1-aa97-9a23a985f095)

	+ Check Services: sudo systemctl list-units --type=service --state=running
	Purpose: Find if there are any unnecessary services running.

- Recovery steps:
	+ Terminate Unnecessary Processes: Identify unwanted processes. 
	  Terminate them using: sudo kill -9 <PID>
	  In case that it keeps spamming and we cannot know about the root cause, a work-around solution 		may be running a crob job to periodically find and kill processes that have Z state.

	+ Disable Unneeded Services at Boot: sudo systemctl disable <service_name>

Step 3: Inspect Disk Space, Inode Usage and Swap space configuration
- Possible Causes/Scenarios:

	+ Low Disk Space: Full disk can cause system instability.

	+ Inode Exhaustion: Too many small files consuming inodes.

	+ Excessive Swap space: Indicates insufficient physical memory.

- Impacts:

	+ Logging Failures: NGINX can't write to logs, possibly affecting operations.

	+ Memory Issues: System may behave unpredictably due to disk space issues. OOM killer starts killing processes which makes it very hard to troubleshoot. 

- Troubleshoot steps:

	+ Check Disk Space Usage: df -h
	+ Check Inode Usage: df -i
	+ Identify Large Files: sudo find / -type f -size +100M

- Recovery steps:
	+ Remove Unnecessary Files: Clear old logs, caches, and unnecessary files.

	+ Expand Disk Space if Necessary: Resize the disk or migrate to a larger volume.

Step 4: Analyze NGINX Worker Processes
- Possible Causes/Scenarios:

	+ Over-allocation of Workers: worker_processes are set too high compared to selected hardware.

	+ High worker_connections: Excessive connections compared to selected hardware.

- Impacts:

	+ Excessive Memory Consumption: Allocated workers consume more memory than the VM can provide.

	+ Potential for Resource Contention: May lead to performance degradation.

- Troubleshoot steps:

	+ Review NGINX Configuration: Open the config file: sudo nano /etc/nginx/nginx.conf
	Check: worker_processes and worker_connections.

- Recovery steps:
	+ Adjust Configuration: Set worker_processes to auto: 
	worker_processes auto;
	+ Adjust worker_connections: Set to a reasonable number that compatible with chosen hardware.

	+ Reload NGINX: sudo nginx -s reload
	
	+ Optimize NGINX Configuration:

	+ Fine-tune settings based on performance testing.

Step 5: Check for Memory Leaks in NGINX or Modules
- Possible Causes/Scenarios:

	+ Memory Leak in NGINX: Due to bugs or outdated versions. Check the current nginx version and search for any potential leak.
	Refer: https://nginx.org/en/CHANGES

	+ Memory Fragmentation: Long running processes may have fragmented memory.

- Impacts: 

	+ Gradual Memory Increase: Memory usage grows over time until resources are exhausted.

	+ Potential Service Outages: System may become unresponsive or crash.

- Troubleshoot steps:

	+ Restart NGINX: sudo systemctl restart nginx
	Observe Memory Usage Post-Restart.

	+ Monitor Memory Over Time: Use top, htop, or check the monitoring tools to watch the trend of memory usage.

	+ Disable Suspect Modules: Comment out or remove configurations and monitor the memory.

- Recovery steps:

	+ Disable Suspect Modules: Comment out or remove configurations for non-essential modules.

	+ If the system is able to update NGINX and Modules to the latest version: 
	sudo apt update
	sudo apt install nginx

	+ If the system in unable to update NGINX due to incompatibility, or it takes too much effort to change to another solution: 
	Work around: 
	Prepare backup nginx server.
	If the server reaches a limit, use the backup nginx server and restart/shut down the current nginx/server. 

Step 6: Check for impacts from other components in the system 
- Possible Causes/Scenarios:

	+ Potential bugs: 
	Other components may not close the connection, which responsible for memory leak.
	Refer: https://serverfault.com/questions/979933/nginx-consumes-100-ram
	High connection rate may come from malfunctions in backend services and slower the NGINX server.
	
	+ NGINX is configured to cache large static files or responses, leading to increased memory usage as these cached items are stored in RAM.
	
- Impacts:

	+ Gradual Memory Increase: Memory usage grows over time until resources are exhausted.
	
	+ Resource Exhaustion: High number of connections consume memory.

- Troubleshoot steps:
	
	+ Replicate the system to dev environment.
	
	+ Identify malicious IPs: Check if there are suspicious connections from the backend services.

	+ Check NGINX's performance when implementing with current caching configuration.
	
- Recovery steps:
	
	+ Report bugs to developers
	
	+ Re-structure the system: consider caching large file or not, reconfigure or scaling up the resources.

Step 7: Examine NGINX Logs for Errors
- Possible Causes/Scenarios:

	+ Excessive Logging: Debug or info level logging generating large log files.

	+ Persistent Errors: Repeated errors causing increased resource usage.

- Impacts:

	+ Disk Space Consumption: Large logs can fill up disk space. Therefore, there is no enough space for swap space.

	+ Increased I/O Load: High disk activity may impact performance.

- Troubleshoot steps:

	+ Check disk space size:

	+ Check Log File Sizes: 
	sudo du -h /var/log/nginx/

	+ Implement Log Rotation: Ensure logrotate is configured for NGINX logs.

	+ Clear Old Logs.

- Recovery steps:
	
	+ Adjust Logging Level: In nginx.conf, set logging to a less verbose level:
	error_log /var/log/nginx/error.log warn;

	+ Reload NGINX Configuration: sudo nginx -s reload

Step 8: Analyze Traffic Patterns for High Load or DDoS
- Possible Causes/Scenarios:

	+ Legitimate Traffic Surge: Marketing campaigns or events driving up traffic.

	+ Malicious Traffic: DDoS attacks overwhelming the server.

	+ Hardware setup no longer can support the increase in traffic.

- Impacts:

	+ Resource Exhaustion: High number of connections consume memory.

	+ Service Degradation: Slow or unresponsive service for legitimate users.

- Troubleshoot steps:

	+ Review Access Logs for Anomalies:</br>
```sudo tail -n 1000 /var/log/nginx/access.log | awk '{print $1}' | sort | uniq -c | sort -nr | head```

	+ Identify Malicious IPs: Look for IPs with excessive requests.

	+ If there is no suspicious connection, the issue may come from the limitation in hardware setup.

- Recovery steps:
	
	+ If there are suspicious connections:

	Enhance system's configuration to prevent attack to server.
	Implement Rate Limiting: Configure nginx to limit the rate of incoming requests from each clients.
```
	http {
	    limit_req_zone $binary_remote_addr zone=mylimit:10m rate=10r/s;
	}
	server {
	    location / {
		limit_req zone=mylimit burst=20;
	    }
	}
```
	Configure Firewall Rules: Deny malcious IPs

	Consider DDoS Protection Services: Use services like Cloudflare or AWS Shield.

	+ If there is no suspicious connection, recalculate the sufficient hardware.
	Implement scaling to reduce the bottleneck from connection

