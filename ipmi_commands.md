# IPMI cheatsheet

## Declare parameters

Declare host parameters to save some typing
```
export IPMIHOST=192.168.xxx.xxx
export IPMIUSER=root
export IPMIPW=xxxx
```


## FAN control
References: 
- https://github.com/ipmitool/ipmitool/issues/30
- https://www.reddit.com/r/homelab/comments/7xqb11/dell_fan_noise_control_silence_your_poweredge/

### Enable manual fan control
```
ipmitool -I lanplus -H $IPMIHOST -U $IPMIUSER -P $IPMIPW raw 0x30 0x30 0x01 0x00
```

### Set static fan speeds
```
# setting it to 20% MAX RPM
ipmitool -I lanplus -H $IPMIHOST -U $IPMIUSER -P $IPMIPW raw 0x30 0x30 0x02 0xff 0x14
```
The fan speed percetages are represented in hex. `0x14` is 20 in base10. Correspondingly, to set fan speed to 50% of max speed, change it to `0x32`.


### Disable manual fan control
```
ipmitool raw 0x30 0x30 0x01 0x01
```

### Show all FAN speeds
```
ipmitool -I lanplus -H $IPMIHOST -U $IPMIUSER -P $IPMIPW sensor list all | grep FAN
```



