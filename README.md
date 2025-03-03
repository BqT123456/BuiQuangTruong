# Prerequisite #
Problem 1:
- The machine has installed jq command with</br>
```sudo apt install jq```.</br>
Somehow ```sudo snap install jq``` may lead to error ```jq: error: Could not open file ./transaction-log.txt: Permission denied```.

- The following tools are installed on the machine and can work normally: curl, xargs .

- The ./transaction-log.txt has enough file permission to be executed by the cli.

- For easy to read, when using the proposed CLI, all error handlings are not announced. In real-world, errors are captured and printed out our console.

- It is understood that ":order_id" should be replaced in "https://example.com/api/:order_id" with IDs from a transaction-log.txt.

- It is understood that /output.txt and ./transaction-log.txt are in the same folder.

- The solution just covered only a single API URL, it is not flexible enough for changes. If your API URL contains a placeholder pattern </br>
"{found_id}" in several places, using a generic placeholder replacement can replace all instances — even those you didn't intend to change. This can result in malformed URLs, incorrect API calls, or unexpected data retrieval.

Ex: Consider in your transaction-log.txt

Our URL pattern: 

https://example.com/api/{found_TSLA_sell_id}?include={found_TSLA_sell_id}

=> Output: 
curl -s https://example.com/api/12346?include=12346
curl -s https://example.com/api/12362?include=12362

------------------------------------------------------------------------------------------------------
Problem 2:

- Assume that the this is how crypto exchange system works:
Ref: https://medium.com/coinmonks/how-does-a-centralised-crypto-exchange-actually-work-84a574fe0a1

- Assume that there is no bug in production environment.

- The solution will cover Trading Engine system design. Refer:https://www.around25.com/blog/building-a-trading-engine-for-a-crypto-exchange
  
- For simplification and time limitation, the plan for choosing which hardware, database, ELB,... are neglected. Also, Roles and Network configurations are not listed but will be explained briefly. </br>
   + Role: Apply "Least-privilege permission".
   + Network: Apply ELB in public subnet to protect our resource to be not exposed to the Internet.
    * In-direction: Resource -> NAT Gateway -> Internet.
    * Out-direction: Internet -> ELB -> Resource
     ![image](https://github.com/user-attachments/assets/0072bf2e-db11-43da-a83d-ffbfcbf7a39d)
     
------------------------------------------------------------------------------------------------------
Problem 3:

- Assume that the scenario happens in production environment

- Assume that the server for NGINX has been calculated precisely according to NGINX bench mark result.
  + Refer: https://blog.nginx.org/blog/testing-the-performance-of-nginx-and-nginx-plus-web-servers
 
- Assume that the previous DevOps engineer followed the below document to optimize the server configuration:
https://blog.nginx.org/blog/optimizing-web-servers-for-high-throughput-and-low-latency
