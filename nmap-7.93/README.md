# Static Linux Nmap

**SHA256SUM:-**

```
0d0cea1545140041fce664a7660a3777010576972af44ab96bf1621234070b8e  nmap-aarch64
59e4c16b9c02de186219bbf0681708d5be9e530dcd2c3eaca4fb3bab5f84c0fe  nmap-amd64
b5faf1edaee139f32de14e28cc1b771b96a9525b9fbe5702e5abbb14fd36c39d  nmap-mips64el
c33e23e6c4cb9899447011f4d0226aa27ee0222ab648ef7bd5614dc5a42a8f2b  nmap-mipsel
```


**Notes:-**

* To use `nmap` scripting engine you've to copy the nmap folder to target machine and add that folder using `NMAPDIR` environment variable. For a one-liner you can use the following command:-

```sh
NMAPDIR=/path/to/folder/of/nmap ./nmap-aarch64 -sC -sV example.com
```
