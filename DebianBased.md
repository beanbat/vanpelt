# System

- Check system logs
```BASH
sudo journalctl -xe
```

- Check Kernel messages
```BASH
dmesg | tail
```

- List recent commands
```BASH
cat ~/.bash_history | tail -n 20
```

- List previously executed commandes
```BASH
last -a | head -n 10
```

- Check crontab
```BASH
ls -la /etc/cron* && \
cat /etc/crontab
```

- List SUID files
```BASH
sudo find / -perm -4000 -type f 2>/dev/null
```

- List exploitable binaries
```BASH
sudo getcap -r / 2>/dev/null
```

# Network

## SS

- Get output connections
```BASH
sudo ss -tn 2>/dev/null | awk '{print $5}' | grep -E "([0-9]{1,3}\.){3}[0-9]{1,3}" | sort | uniq -c | sort -nr
```

## TCPdump

- Check all incoming connections on `eth0` on port 443 
```BASH
sudo tcpdump -i eth0 port 443 -v
```

# Services

## Apache2

- Wich IP went to my server and display the response Code
```BASH
echo -e "Occurrences  | IP Address        | HTTP Code"
echo "-------------------------------------------------"
awk '{print $1, $9}' /var/log/apache2/wordpress-access.log \
| sort | uniq -c | sort -nr \
| awk '{printf "%-12s | %-17s | %s\n", $1, $2, $3}'
```

- Which IP generated an error
```BASH
echo -e "Occurrences  | IP Address"
echo -e "--------------------------" && \
grep -i "error" /var/log/apache2/wordpress-error.log | \
grep -oE "[0-9]+\.[0-9]+\.[0-9]+\.[0-9]+" | sort | uniq -c | sort -nr | awk '{printf "%-12s | %s\n", $1, $2}'
```

## SSH

### Denied connections

- Tried usernames
```BASH
echo -e "Occurrences   | Username" && \
echo "-------------------------------------------" && \
awk '/sshd\[.*\]: Invalid user/ {count[$6]++} END {for (user in count) printf "%-13s | %s\n", count[user], user}' /var/log/auth.log | sort -nr
```

- Denied per users and IP
```BASH
echo -e "Occurrences   | IP Address      | Usernames"
echo "------------------------------------------------------------"
awk '
/sshd\[.*\]: Invalid user/ {
    if (match($0, /Invalid user ([^ ]+) from ([0-9]+\.[0-9]+\.[0-9]+\.[0-9]+)/, arr)) {
        ip_count[arr[2]]++                     # Comptage global de l’IP
        user_attempts[arr[2], arr[1]]++         # Comptage par (IP, username)
        if (!seen[arr[2], arr[1]]) {
            user_list[arr[2]] = user_list[arr[2]] (user_list[arr[2]] ? ", " : "") arr[1]
            seen[arr[2], arr[1]] = 1
        }
    }
}
END {
    for (ip in ip_count) {
        printf "%-13s | %-15s | %s\n", ip_count[ip], ip, user_list[ip]
    }
}' /var/log/auth.log | sort -nr
```

## Allowed connections

- Which IP and what user did connect to my server
```BASH
echo -e "Occurrences   | Username            | IP Address"
echo "------------------------------------------------------"
awk '/Accepted password|Accepted publickey/ {
    for (i=1; i<=NF; i++) {
        if ($i == "for") user=$(i+1);
        if ($i == "from") ip=$(i+1);
    }
    print user, ip;
}' /var/log/auth.log | sort | uniq -c | sort -nr \
| awk '{printf "%-12s | %-18s | %s\n", $1, $2, $3}'
```

-grep a given IPv4 in a logfile
```BASH
grep -E "\b192\.168\.1\.56\b" /var/log/apache2/access.log
```

# Users

- List sudo users
```BASH
sudo grep '^sudo:.*$' /etc/group | cut -d: -f4
 ```

- List recently added users
```BASH
sudo cat /etc/passwd | tail -n 5
```

- List previously connected users
```BASH
last
```
