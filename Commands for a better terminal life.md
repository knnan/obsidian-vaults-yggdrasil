
`Processs running on a port
```bash
lsof -i :<PORT_NUMBER> 
# ex lsof -i :6000
```

`filter by port and print command`
```bash
ss -ntp | grep ':3306' | awk -F'pid=' '{print $2}' | cut -d',' -f1 | xargs -r ps -fp

# filter by ip and port 

ss  -a sport = :3306 or dport = :3306 and dst = 172.24.0.0/16
```

`mysql show connection query`
```bash 
# inside mysql prompt
show full processlisst;
```