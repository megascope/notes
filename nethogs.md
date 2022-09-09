Using nethogs -t to monitor per process bandwidth, parse output file with

```
cat 2018-01-21.nethogs | grep -v Refreshing | sed -e 's/ /_/g' | awk  'NF >= 1 { proc[$1]++; sent[$1]+=$(NF-1); recv[$1]+=$NF}; END { for (p in proc) {print p" "sent[p]" "recv[p]} }' | column -t | sort -rn -k2,3
```
