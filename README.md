# Prerequisite #
Problem 1:
- The machine has installed jq command with "sudo apt install jq". Somehow "sudo snap install jq" may lead to error "jq: error: Could not open file ./transaction-log.txt: Permission denied".

-The following tools are installed on the machine and can work normally: curl, xargs .

- The ./transaction-log.txt has enough file permission to be executed by the cli.

- For easy to read, when using the proposed CLI, all error handlings are not announced. In real-world, errors are captured and printed out our console.

- It is understood that ":order_id" should be replaced in "https://example.com/api/:order_id" with IDs from a transaction-log.txt.

-It is understood that /output.txt and ./transaction-log.txt are in the same folder.

- The solution just covered only a single API URL, it is not flexible enough for changes. If your API URL contains a placeholder pattern "{found_id}" in several places, using a generic placeholder replacement can replace all instances — even those you didn't intend to change. This can result in malformed URLs, incorrect API calls, or unexpected data retrieval.

Ex: Consider in your transaction-log.txt

Our URL pattern: 

https://example.com/api/{found_TSLA_sell_id}?include={found_TSLA_sell_id}

=> Output: 
curl -s https://example.com/api/12346?include=12346
curl -s https://example.com/api/12362?include=12362
------------------------------------------------------------------------------------------------------
Problem 2:
- The following tools are installed on the machine and can work normally: free,
 




## Submission ##
You can either provide a link to an online repository, attach the solution in your application, or whichever method you prefer. We're cool as long as we can view your solution without any pain.
