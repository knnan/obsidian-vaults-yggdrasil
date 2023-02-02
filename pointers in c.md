# Pointers to variables
- pointers store addresss of another variable 
- pointers have a type usually it is of the form *pointer to variable type*


variable address |variable name| value 
-----------------|---------|----------
0x0001|a|1
0x0005|p|0x0001 

# Address of a variable 
The ampersand `&` operator is used to get the address of a variables.

```c
int *address_of_a = &a;
printf("address_of_a : %p",address_of_a) // prints 0x0001 
```

To get the value of the variable pointed to by a pointer we can use the 
star `*` operator .
value at the address(pointer) : `*(pointer)`

Ex:
```c
// declare a variable and initialize it
int a = 123;
// declare  a pointer to a and store the address of a in the pointer 
int *ptr_to_int  = &a;
int b = *ptr_to_int ; // b is set to the value of a now i.e 123

printf("a : %d",a); // prints 123
printf("ptr_to_int : %p",ptr_to_int ); // prints 0x001
printf("value of variable  pointed to by ptr : %p",*ptr_to_int ); // prints 0x001


```
