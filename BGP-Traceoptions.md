<h3>Troubleshooting (Debugging) BGP in Juniper using Traceoptions</h3>

The “traceoptions” statement in Juniper let you debug BGP protocol issues. 
If cannot connect BGP peering, you can set the “traceoptions” to understand further about the issue.

<h4>1. Configure traceoptions</h4>

```
set protocols bgp group eBGP-AS400 traceoptions file test-bgp
set protocols bgp group eBGP-AS400 traceoptions file size 100k
set protocols bgp group eBGP-AS400 traceoptions file files 2
set protocols bgp group eBGP-AS400 traceoptions flag all
```

Where:
```
max trace file size = 100k
max trace files = 2
flag all = trace everything
```

It will look like something like this:
```
root# run show configuration protocols bgp group eBGP-AS400 traceoptions 
file test-bgp size 100k files 2;
flag all;

[edit]
root# 
```

<h4>2. View the trace file to verify</h4>

```
root# run file list /var/log/test-bgp 
/var/log/test-bgp

[edit]
```

<h4>3. View the contents</h4>

```
root# run file show /var/log/test-bgp 
Oct 30 11:25:40 trace_on: Tracing to "/var/log/test-bgp" started
Oct 30 11:28:04.762868 bgp_hold_timeout:4399: NOTIFICATION sent to 192.168.24.4 (External AS 400): code 4 (Hold Timer Exs
Oct 30 11:30:48.726013 advertising graceful restart receiving-speaker-only capability to neighbor 192.168.24.4 (External)
Oct 30 11:30:48.728863 advertising graceful restart receiving-speaker-only capability to neighbor 192.168.24.4 (External)
Oct 30 11:31:55.811573 task_set_option_internal: task BGP_400.192.168.24.4+179 socket 58 option DontRoute(5)
Oct 30 11:31:55.811648  value 1
Oct 30 11:31:55.811654 task_set_option_internal: task BGP_400.192.168.24.4+179 socket 58 option IifRestrict(36)
Oct 30 11:31:55.811658  value 1
Oct 30 11:31:55.811664 task_set_option_internal: task BGP_400.192.168.24.4+179 socket 58 option TTL(15)
Oct 30 11:31:55.811667  value 1
Oct 30 11:31:55.811673 task_set_option_internal: task BGP_400.192.168.24.4+179 socket 58 option LridCheck(59)
Oct 30 11:31:55.811677  value 0
---(more)---
```

<h4>4. Monitor the log</h4>

```
root# run monitor start test-bgp 

[edit]
root# 
*** test-bgp ***
Oct 30 11:56:35.565333 task_process_events: recv ready for BGP_400.192.168.24.4+179
Oct 30 11:56:35.565364 bgp_read_v4_message: receiving packet(s) from 192.168.24.4 (External AS 400)
Oct 30 11:56:35.565375 
Oct 30 11:56:35.565375 BGP RECV 192.168.24.4+179 -> 192.168.24.2+60909
Oct 30 11:56:35.565381 BGP RECV message type 4 (KeepAlive) length 19
Oct 30 11:56:35.565386 bgp_read_v4_message: done with 192.168.24.4 (External AS 400) received 19 octets 0 updates 0 routs
Oct 30 11:56:41.977742 bgp_hold_timeout: peer 192.168.24.4 (External AS 400) last checked 83 last recv'd 7 last sent 18 8
Oct 30 11:56:41.977784 task_timer_reset: reset BGP_400.192.168.24.4+179_Hold
Oct 30 11:56:41.977799 task_timer_set_oneshot_latest: timer BGP_400.192.168.24.4+179_Hold interval set to 1:23
```

<h4>5. Stop monitoring using this command</h4>

```
root# run monitor stop test-bgp 

[edit]
root# 
```

<h4>6. Deactivate traceoptions if finished</h4>

```
root# deactivate protocols bgp group eBGP-AS400 traceoptions 

[edit]
root# show | compare 
[edit protocols bgp group eBGP-AS400]
!      inactive: traceoptions { ... }

[edit]
root# commit 
commit complete

[edit]
root# 

[edit]
root# show protocols bgp group eBGP-AS400 traceoptions 
##
## inactive: protocols bgp group eBGP-AS400 traceoptions
##
file test-bgp size 100k files 2;
flag all;

[edit]
root# show protocols bgp group eBGP-AS400                 
inactive: traceoptions {
    file test-bgp size 100k files 2;
    flag all;
}
local-address 192.168.24.2;
export AS123-Routes;
peer-as 400;
neighbor 192.168.24.4 {
    import LOCAL-PREFERENCE;
}

[edit]
```
