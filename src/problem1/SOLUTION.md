Provide your CLI command here:
jq -r 'select(.symbol=="TSLA" and .side=="sell") | .order_id' ./transaction-log.txt | xargs -I {found_TSLA_sell_id} curl -s https://example.com/api/{found_TSLA_sell_id} >> ./output.txt
