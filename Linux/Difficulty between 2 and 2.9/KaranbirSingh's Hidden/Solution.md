# Solution to [KaranbirSingh's Hidden](https://crackmes.one/crackme/65ad0d7deef082e477ff5ef8)

It is a very, very easy problem. All you need to do is enter a password with a length of more than 5, and the ASCII difference between the third and fourth characters should be 32. For example, `11aA1`.

```c
int main(int argc,char **argv)

{
  size_t password_length;
  int check;
  char *password;
  
  if (argc == 2) {
    password = argv[1];
    password_length = strlen(password);
    if (password_length < 5) {
      puts("Come on now... you should expect better from us!");
      check = -1;
    }
    else if ((int)password[2] - (int)password[3] == 0x20) {
      puts("You Are Done");
      check = 0;
    }
    else {
      puts("Incorrect password!");
      check = -1;
    }
  }
  else {
    puts("Please provide a password.");
    check = 1;
  }
  return check;
}
```