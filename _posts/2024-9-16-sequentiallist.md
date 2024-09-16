---
layout: post
title: Sequential list
date: 2024-9-16
description: This is a study note on sequential list in data structure, implemented using the C programming language.
tags: linear list
categories: data structure
---

#### 1.Introduction

A sequential list is the sequential representation and implementation of a linear list, corresponding to it is the linked list. Sequential list and linked list are two formats to realize linear list in computer. The former is what we'll discuss in this post.

#### 2.Data element

A data structure is a collection of data elements, so first we need to define the data element. For example, I can define a data element as student information using a structure (struct).

```c
struct data{
  char name[8];
  int age;
  char sex;
  float score;
};
typedef struct data Elemtype
```

Note: if your data element is a primitive data type, such as int or float, there is no need to use a structure. But if it is complex and includes multiple fields or attributes, you'll need a structure (struct) to define it.

#### 3.Implementation using static array

There are two ways to realize a data structure of sequential list, static array and dynamic array, and the latter is better. But first I'll use static to show how to build a sequential list.

```c
typedef struct{
  Elemtype data[30];
  int listlen;
  int listsize;
}SqList;
```

Now SqList is a linear list data type。

If you want to store the i-th person's information, for example:

```c
SqList L;
L.data[i - 1].name = "person name";
L.data[i - 1].age = 19;
L.data[i - 1].sex = 'M';
L.data[i - 1].score = 93.4;
// than modify the information of the list (L.listlen and L.listsize) if you need
```

#### 4.Implementing using dynamic array

Dynamic array is a better choice compared to static list, because the list size can't be modified once created using static list. But when it comes to dynamic array, the storage space can be allocated dynamically base on the length. The core lies in the use of pointer. You can decide the initial size and the increment.

Here is an example of defining and initializing a sequential list:

```c
//define a sequential list
typedef struct{
  Elemtype *elem;  // base address of the storage space
  int listlen;  // current length
  int listsize;  // current size
}SqList;

#define LIST_INIT_SIZE 80
#define LIST_INCREMENT 10

//initialize a SqList
SqList Initlist(){
  SqList L;
  L.elem = (*Elemtype)malloc(LIST_INIT_SIZE * sizeof(Elemtype));
  if(!L.elem){
    exit(-1);  // failed to allocate storage space
  }
  
  L.listlen = 0;
  L.listsize = LIST_INIT_SIZE;
  
  return L;
}
```

After declaring a sequential list, we need to use malloc() to allocate storage space, which is different from using static array.

#### 5.Other basic operations

**note:** In the following algorithms, 'position' represents the exact location of a data element in the sequential list, rather than its index.

There are some macro definitions that will be used

```c
// Function result status codes
#define TRUE 1
#define FALSE 0
#define OK 1
#define ERROR 0
#define INFEASIBLE -1
#define OVERFLOW -2

// Status is the function's return type, representing the function result status codes
typedef int Status;
```

**reset to empty list**

Here is one way to reset:

```c
Status ClearList(SqList L){
  // check if the sequential list exist
  if(L.listsize <= 0){
    exit(OVERFLOW);
  }
  
  Elemtype *p = L.elem;
  for(int i = 0; i < L.listlen; i++){
    *p = (Elemtype){"", 0, '', 0.0f};
    L.listlen--;
    p++;
  }
  
  return OK;
}
```

There are many other ways to reset a sequential list in C programming language.

For example, use memset() in string.h. But make sure we don't include any pointer in the list, or it may cause some problems.

Under the circumstance given above, this method is feasible.

```c
#include <string.h>

Elemtype *p = L.elem;
// there are two ways: reset the elements one by one, or reset them as a whole
//reset one by one
for(int i = 0; i < L.listlen; i++){
  memset(p, 0, sizeof(Elemtype));
  p++;
  L.listlen--;
}
//reset as a whole
memset(p, 0, L.listlen * sizeof(Elemtype));
L.listlen = 0;

//memset will set every int to 0, float to 0.0 and pointer to NULL
```

**Get the value of a certain data element**

Note: In C language, pointer parameters are used to modify external variables. When a pointer is passed as a function parameter, it allows direct modification of the value of the variable the pointer points to, thereby achieving the effect of an output parameter.

```c
Status GetElem(SqList L, int i, Elemtype *e){
  // check if the targeted position is feasible
  if(i < 1 || i > L.listlen){
    return(ERROR);
  }
  
  int index = i - 1;  // Obtain the index of the required data element in the entire list
  *e = *(L.elem + index)  // By dereferencing the pointer, modify the value of the external variable to achieve the effect of an output parameter
    
    return OK;
}
```

Of course, you can choose not to use a pointer and directly return the data element instead.

**Locate a given element**

This operation is the basic of many corresponding algorithms. First we need to prepare an algorithm for comparing two data elements.

```c
Status compare(Elemtype *p1, Elemtype *p2){
    if((!(strcmp(p1->name, p2->name))) && (p1->age == p2->age) && (p1->sex == p2-> sex) && (p1->score == p2->score))){
        return TRUE;
    }
    else{
        return FALSE;
    }
}
// we make use of the function strcmp(), so '#include <string.h>' is needed
```

Then define the function used to locate the target element.

```c
Status LocateElem(SqList L, Elemtype E){
  // check if the list exit and is not empty
  if(L.listlen <= 0 || L.listsize <= 0){
    exit(INFEASIBLE);
  }
  
  Elemtype *p = L.elem  // pointer p initially point to the first element
  Elemtype *e = &E  // pointer e point to the target element E
  for(int i = 0; i < L.listlen; i++){
    if(compare(p, e)){
      return (i + 1);  // return the index plus one (the position)
    }
    else{
      p++;
    }
  }// end for
  return FALSE  // failed to find the element
}
```

The function can also be realized using pointer, with a pointer of the position passed to the function as a formal parameter.

With the this function, we can also find the former or latter element of our target data element. Don't forget to check if the target data element is at the head or rear of the whole list.

#### 6.Other interesting algorithms

With the data structure and its basic operations prepared, we can realize some algorithms such as inserting or deleting a data element.

**Insert a data element**

To insert a data element at a specific position, we need to shift the data element at the position as well as the subsequent elements one step backward. In this case, we get an empty place to cover with our target data element.

Use a pointer if there is a data need to be modified by the function.

```c
Status InsertElem(SqList *L, Elemtype E, int position){
  // preperation before inserting
  // check the list
  if(L->listlen < 0 || L->listsize <= 0){
    exit(INFEASIBLE);
  }
  // check if the target position is feasible
  if(position < 1 || position > L->listsize){
    exit(INFEASIBLE);
  }
  // check if the list is full
  if(L->listlen >= L->listsize){
    Elemtype *newbase = (Elemtype *)realloc(L->elem, (LIST_INCREMENT + L->listsize) * sizeof(Elemtype));
    
    if(!newbase){
      exit(OVERFLOW);  // failed to allocate larger storage space
    }
    
    L->elem = newbase;
    L->listsize += LIST_INCREMENT;
  }
  
  // now begin to insert the element
  Elemtype *current_e = L->elem + L->listlen - 1;
  for(int i = L->listlen; i > position; i--){
    *(current_e + 1) = *current_e;
    current_e--;
  }// shift the elements backward
  // insert
  *(L->elem + position - 1) = E;
  L->listlen += 1;
  return OK;
}
```

**Delete a data element**
Deleting a data element is kind of like inserting one. All we need to do is over the former position with current data element.

```c
Status DeleteElem(SqList *L, Elemtype *E, int position){
  // check the list
  if(L->listlen < 0 || L->listsize <= 0){
    exit(INFEASIBLE);
  }
  // check if the target position is feasible
  if(position < 1 || position > L->listsize){
    exit(INFEASIBLE);
  }
  
  // now begin to delete the element
  Elemtype *currrent_e = L->elem + position - 1;  // the index of the element to be deleted
  *E = *current_e;  // value of the deleted element is accessible through pointer E
  for(int i = position - 1; i < L->listlen; i++){
    *current_e = *(current_e + 1);
    current_e++
  }
  
  L->listlen -= 1;
  return OK;
}
```



That's all for sequential list. We'll discuss something about linked list in my next post. Thank you for reading!
