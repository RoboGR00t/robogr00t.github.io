<link rel="stylesheet" href="style.css">
>**pwnable.kr: fd** |  RoboGR00t | Dec 2022

<div style="page-break-after: always; visibility: hidden;">\pagebreak</div>

## 🔎 Information Gathering

**Connect to target** <br>
We are connecting to the target over ssh using the following command: 
```bash
ssh fd@pwnable.kr -p2222 # password : guest
```
Inside we can  find the `flag.txt` an execututable `fd` and its source code `fd.c`.
As we can see , we do not have permission to view the flag. But the program `fd` has an SUID bit. Let's look inside the source code.

```c
/* file: fd.c */
#include <stdio.h>
#include <stdlib.h>
#include <string.h>

char buf[32];
int main(int argc, char* argv[], char* envp[]){
	if(argc<2){
		printf("pass argv[1] a number\n");
		return 0;
	}
	int fd = atoi( argv[1] ) - 0x1234;
	int len = 0;
	len = read(fd, buf, 32);
	if(!strcmp("LETMEWIN\n", buf)){
		printf("good job :)\n");
		system("/bin/cat flag");
		exit(0);
	}
	printf("learn about Linux file IO\n");
	return 0;

}
```

As we can see the program takes an arguments as a string and converts it to an integer. Then subtracts 0x1234 in HEX and puts the value inside the `read()` function in the place of the file descriptor then it compares the string that we will provide with the string "LETMEWIN" and if there are equal the program will print our flag.

## 💥 Exploitation
So the goal is to set the fd variable to 0 (zero) and enter the passphrase ***LETMEWIN*** . Let's convert the hex number 0x1234 to decimal (4660).

Now let's run the program and grab our flag.

## 📃 References

- [pwnable.kr](https://pwnable.kr/play.php)
- [File Descriptor](https://en.wikipedia.org/wiki/File_descriptor)
- [read() manual](https://man7.org/linux/man-pages/man2/read.2.html)
- [Hex to Decimal Converter](https://www.rapidtables.com/convert/number/hex-to-decimal.html)
