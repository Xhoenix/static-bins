# Static Linux Nmap

**Notes:-**

* To use `nmap` scripting engine you've to copy the nmap folder provided in this repo to target machine and add that folder using `NMAPDIR` environment variable. For a one-liner you can use the following command:-

```sh
NMAPDIR=/path/to/folder/of/nmap ./nmap -sC -sV example.com
```
