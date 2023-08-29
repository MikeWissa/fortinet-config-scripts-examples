## Configuration Script to
# Allocate a port to a vdom

```
config system global
set vdom-mode multi-vdom
end
config vdom 
edit schoolvdom
end
config global
config system vdom-link
edit "to-school"
next
end
end

config vdom
edit root
config system interface
    edit schoolvdom-vlan
        set vdom "schoolvdom"
        set ip 192.168.55.1 255.255.255.0
        set allowaccess ping ssh
        set device-identification enable
        set role lan
        set interface "b"
        set vlanid 55
    next
end
end



config switch-controller virtual-port-pool
    edit "schoolvdom"
    next
end
end

config  vdom
edit root
config switch-controller  managed-switch 
edit S108EF5920013469 
config ports
edit port5
set export-to-pool schoolvdom
end


config vdom
edit schoolvdom
config switch-controller 
set default-virtual-switch-vlan schoolvdom-vlan 
end

execute switch-controller virtual-port-pool request S108EF5920013469 port5
end

config global

config system interface
    edit "to-school0"
        set vdom "root"
        set ip 192.168.56.1 255.255.255.252
        set allowaccess ping
        set type vdom-link
    next
    edit "to-school1"
        set vdom "schoolvdom"
        set ip 192.168.56.2 255.255.255.252
        set allowaccess ping
        set type vdom-link
    next
end
end

config vdom
edit root
config firewall policy
    edit 100
        set name "school-to-internet"
        set srcintf "to-school0"
        set dstintf "wan1"
        set action accept
        set srcaddr "all"
        set dstaddr "all"
        set schedule "always"
        set service "ALL"
        set nat enable
    next
end

config router static
edit 100
set dst 192.168.55.0 255.255.255.0
set gateway 192.168.56.2
set device to-school0
next
end

end

config vdom

edit schoolvdom

config router static
edit 1
set gateway 192.168.56.1
set device to-school1
next
end

config firewall policy
    edit 1
        set name "DIA"
        set srcintf "schoolvdom-vlan"
        set dstintf "to-school1"
        set action accept
        set srcaddr "all"
        set dstaddr "all"
        set schedule "always"
        set service "ALL"
    next
end

config system dhcp server
    edit 1
        set default-gateway 192.168.55.1
        set netmask 255.255.255.0
        set interface "schoolvdom-vlan"
        config ip-range
            edit 1
                set start-ip 192.168.55.2
                set end-ip 192.168.55.254
            next
        end
        set dns-server1 8.8.8.8
    next
end


```
