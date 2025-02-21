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

- Wich IP connected to my server
```BASH
echo -e "Occurrences  | IP Address        | HTTP Code"
echo "-------------------------------------------------"
awk '{print $1, $9}' /var/log/apache2/access.log \
| sort | uniq -c | sort -nr \
| awk '{printf "%-12s | %-17s | %s\n", $1, $2, $3}'

```

```BASH
echo -e "Occurrences  | IP Address"
echo -e "--------------------------" && \
grep -i "error" /var/log/apache2/wordpress-error.log | \
grep -oE "[0-9]+\.[0-9]+\.[0-9]+\.[0-9]+" | sort | uniq -c | sort -nr | awk '{printf "%-12s | %s\n", $1, $2}'
```

## SSH

### Denied connections

- Which Ip and what user tried to connect to my server
```BASH
echo -e "Occurrences   | Username            | IP Address" 
echo "------------------------------------------------------" 
awk '/Failed password/ {print $(NF-5), $(NF-3)}' /var/log/auth.log \
| sort | uniq -c | sort -nr \
| awk '{printf "%-12s | %-18s | %s\n", $1, $2, $3}'
```

## Allowed connections
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