This may work in Windows 11 (UNTESTED CODE).

Open Notepad as Administrator and then open the following file:

```
C:\Windows\System32\drivers\etc\hosts
```

Then refer the following the mains to this IP address: 

```
127.0.0.1 example1.com
127.0.0.1 example2.com
127.0.0.1 example3.com
```

Save the file and then flash DNS cache:

```
ipconfig /flushdns
```

## Notes

No, you cannot use variables in the hosts file. You must explicitly specify the IP address (e.g., 127.0.0.1) for each domain.
