# Solution to [wh1t3r0se's flag_eater_crackme KeyGen](https://crackmes.one/crackme/61eec94433c5d413767ca64f)

I used Ghidra to analyze the binary file and understand its functionality. In the code below, I have modified some function and variable names to enhance readability.

```c
void function(char *input_array)

{
  char cVar1;
  undefined8 uVar2;
  long in_FS_OFFSET;
  int i;
  int int_array [30];
  long local_10;
  
  local_10 = *(long *)(in_FS_OFFSET + 0x28);
  for (i = 0; i < 30; i = i + 1) {
    cVar1 = isPrime((int)input_array[i]);
    if (cVar1 == '\0') {
      int_array[i] = (int)(input_array[i] >> 4);
    }
    else {
      int_array[i] = (int)(input_array[i] >> 1);
    }
  }
  uVar2 = check_is_equal(int_array,0x104060,30);
  if ((char)uVar2 == '\0') {
    puts("wrong");
  }
  else {
    puts("correct");
  }
  if (local_10 != *(long *)(in_FS_OFFSET + 0x28)) {
                    /* WARNING: Subroutine does not return */
    __stack_chk_fail();
  }
  return;
}
```
The `isPrime` function checks whether the input number is prime or not, while the `check_is_equal` function verifies whether two integer arrays are equal or not.

The array located at `0x104060` contains the following values: `{6, 6, 48, 51, 7, 7, 3, 7, 50, 6, 3, 5, 53, 3, 5, 7, 7, 7, 6, 54, 7, 5, 6, 6, 3, 5, 3, 7, 48, 7}`. I wrote this script to generate the correct input:

```python
import pwn

def readLine(p, i):
    received = ""
    for i in range(i):
        received = p.recvline()
    return received.decode('utf-8', errors = 'replace')

a = [6,6,48,51,7,7,3,7,50,6,3,5,53,3,5,7,7,7,6,54,7,5,6,6,3,5,3,7,48,7]
b = []
for i in range(len(a)):
	if a[i] > 30: # It's an ordinary threshold. Multiplying small numbers by 16 transforms them into good ASCII characters and  multiplying big numbers by 2 and then adding one makes them prime and good ASCII characters. :))
		b.append(a[i]*2+1)
	else:
		b.append(a[i]*16)
payload = ''.join([chr(i) for i in b])

p = pwn.process("./chall")
readLine(p, 1)
p.sendline(payload)
print(readLine(p, 1))
print(readLine(p, 1))
```