# Pointers
- pointers store addresss of another variable 
- pointers have a type usually it is of the form *pointer to variable type*


variable address |variable name| value 
-----------------|---------|----------
0x0001|a|1
0x0005|b|3





Ex:
```c
int a = 1; // declare a variable and initialize it
int *pointer_to_int  = &a; // declare  a pointer to a and store the address of a in the pointer 


```

To get the address of a variable we use the  addressOf '`&`' operator
```c
printf("a is %d and address of a is %p"a) //
```