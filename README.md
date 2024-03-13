### Check network is ready
while true; do sleep 15; ping -c1 8.8.8.8 > /dev/null && break; done

### Create Adblock List
echo "https://adaway.org/hosts.txt" >> /tmp/adblock.list
echo "https://raw.githubusercontent.com/bigdargon/hostsVN/master/hosts" >> /tmp/adblock.list
echo "googleadservices.com" >> /tmp/hosts.whitelist
echo "zing.vn" >> /tmp/hosts.whitelist

### Create WhiteList
#echo "googleadservices.com" >> /tmp/hosts.whitelist

### Get AdBlock List
wget -qO- `cat /tmp/adblock.list` | sed 's/127.0.0.1/0.0.0.0/g' | grep -w ^0.0.0.0 | sed $'s/\r$//' | sed '/localhost/d' | sort -u > /tmp/hosts.downloaded
cat /tmp/hosts.whitelist | sed $'s/\r$//' | grep -vf - /tmp/hosts.downloaded > /tmp/hosts.blocked

### Remove downloaded list
rm -rf /tmp/adblock.list
rm -rf /tmp/hosts.downloaded

### Restart dnsmasq to update new hosts file
killall -SIGHUP dnsmasq
