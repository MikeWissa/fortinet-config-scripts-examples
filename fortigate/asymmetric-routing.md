## Fortigate Enable Asymmetric routing
To enable asymmetric routing on FortiGAte, you have to have the following commands:
### sytem settings
```
config system settings
set asymroute enable
set tcp-session-without-syn enable
set allow-subnet-overlap enable
set auxilliary-session enable
end
```
### Global Settings
```
config system global
set snat-route-change enable
```

### Firewall rules
If traffic goes out ISP1 and returns on ISP2, you need to allow this on the policy.
```
config firewall policy
edit 1
set tcp-session-without-syn all
end
```
