In c pointers to arrays are similiary to their primitive type pointes except the following differences.

in c the name of the arr without any indexing decays to a pointer to the first element of the array 

lets first declare a interger arrray `arr` of lenght 5 .
in c intergers are 4 bytes long on 32 bit systems.
```c 
int arr[5] = {1,2,3,4,5}
```

```c
int *ptr_to_arr_first_element = arr;
int *ptr_to_start_of_array = &arr;
```