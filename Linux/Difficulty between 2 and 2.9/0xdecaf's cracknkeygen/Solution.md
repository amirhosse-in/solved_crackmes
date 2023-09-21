# Solution to [0xdecaf's cracknkeygen](https://crackmes.one/crackme/64bf4185fc4ca2e6ca3d0f25)

I disassembled the program using Ghidra and observed that the program takes a string as input. It then calculates the sum of `input[i] ^ i` for all possible values of `i` and checks if this calculated value equals 635 (sum of `"secret"[i] ^ i` for all possible value of `i`). If the input string is not "secret" but results in the same value of 635, the program accepts it as a valid password. 

Below is the code I wrote to generate keys that the program accepts:

```c
#include <stdio.h>
#include <stdlib.h>

int calculate(int* input, int n){
	int result = 0;
	for(int i = 0; i < n; i++){
		result += input[i] ^ i;
	}
	return result;
}

void print(int* input, int n){
	for(int i = 0; i < n; i++){
		printf("%c", input[i]);
	}
	printf("\n");
}

void key_finder(int* input, int max, int j){
	if(max == j){
		if (calculate(input, max) == 635) // 635 is for calculate("secret", 6)
			print(input, max);
		return;
	}
	for(int i = 0;i < 26; i++){
		input[j] = (int)'a' + i;
		key_finder(input, max, j + 1);
		input[j] = (int)'A' + i;
		key_finder(input, max, j + 1);
	}
}

int main(){
	int* a = (int*)malloc(sizeof(int) * 100);
	for(int i = 3; i < 10; i++){
		key_finder(a, i, 0);
	}
}
```