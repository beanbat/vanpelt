# SSH

## Denied connections

- Which Ip and what user tried to connect to my server
```BASH
awk '/Failed password/ {print $(NF-5), $(NF-3)}' /var/log/auth.log \
| sort | uniq -c | sort -nr | awk 'BEGIN { printf "%-20s %-20s %-10s\n", "Username", "IP Address", "Attempts"; \
print "------------------------------------------------------"} { printf "%-20s %-20s %-10s\n", $2, $3, $1 }'
```

- Same, but the output goes into a CSV
```BASH
echo "Username,IP Address,Attempts" > failed_logins.csv && \
awk '/Failed password/ {print $(NF-5)","$(NF-3)}' /var/log/auth.log | sort | uniq -c | sort -nr | awk '{ print $2","$3","$1 }' >> \
failed_logins.csv
```
## Allowed connection

#System

- List recent commands
```BASH
cat ~/.bash_history | tail -n 20
```

- List previously executed commandes
```BASH
last -a | head -n 10
```

#Users

- 
