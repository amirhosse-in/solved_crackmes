# Solution to [NomanProdhan's License Checker 0x02](https://crackmes.one/crackme/61c62bde33c5d413767ca0a0)

I just used the `strings` command to view strings in the binary file and found two strings: `NomanProdhan` and `KS-LICENSE-KEY-2021-REV-2`. I then executed `./license_checker_2 NomanProdhan KS-LICENSE-KEY-2021-REV-2`, and here was the result:

`Congratulations, NomanProdhan! The premium service has been activated for you.`

However, my curiosity was not satisfied, and I wanted to learn more about the program. So, I decompiled it using Ghidra and performed some code clean-up. The final result was:

```c
int main(int argc,char **argv)

{
  int difference;
  

  if (argc != 3) {
    printf("Usage : %s <your name here> <license key here>\n",*argv);
                    /* WARNING: Subroutine does not return */
    exit(0);
  }
                    /* calculate difference between argv[1] and "NomanProdhan"
                        */
  difference = strcmp(argv[1],"NomanProdhan");
  if (difference == 0) {
                    /* calculate difference between argv[2] and "KS-LICENSE-KEY-2021-REV-2" */
    difference = strcmp(argv[2],"KS-LICENSE-KEY-2021-REV-2");
    if (difference == 0) {
      printf("Congratulations %s, premium service has been activated for you.\n",argv[1]);
                    /* WARNING: Subroutine does not return */
      exit(0);
    }
    printf("Sorry ! %s , %s is an invalid license.\n",argv[1],argv[2]);
  }
  else {
    printf("Sorry ! %s , you don\'t have a license.\n",argv[1]);
  }
  return 0;
}

```

It appears that the only valid username and password are `"NomanProdhan"` and `"KS-LICENSE-KEY-2021-REV-2"`.