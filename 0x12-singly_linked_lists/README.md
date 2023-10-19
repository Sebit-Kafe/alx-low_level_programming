0x12. C - Singly linked lists



README.md



==========================
0-print_list.c


#include <stdio.h>
#include "lists.h"

/**
 * print_list - prints all the elements of a linked list
 * @h: pointer to the list_t list to print
 *
 * Return: the number of nodes printed
 */
size_t print_list(const list_t *h)
{
 size_t s = 0;

 while (h)
 {
 if (!h->str)
 printf("[0] (nil)\n");
 else
 printf("[%u] %s\n", h->len, h->str);
 h = h->next;
 s++;
 }

 return (s);
}


==========================
1-list_len.c


#include <stdlib.h>
#include "lists.h"

/**
 * list_len - returns the number of elements in a linked list
 * @h: pointer to the list_t list
 *
 * Return: number of elements in h
 */
size_t list_len(const list_t *h)
{
 size_t n = 0;

 while (h)
 {
 n++;
 h = h->next;
 }
 return (n);
}


==========================
2-add_node.c


#include <stdlib.h>
#include <string.h>
#include "lists.h"

/**
 * add_node - adds a new node at the beginning of a linked list
 * @head: double pointer to the list_t list
 * @str: new string to add in the node
 *
 * Return: the address of the new element, or NULL if it fails
 */
list_t *add_node(list_t **head, const char *str)
{
 list_t *new;
 unsigned int len = 0;

 while (str[len])
 len++;

 new = malloc(sizeof(list_t));
 if (!new)
 return (NULL);

 new->str = strdup(str);
 new->len = len;
 new->next = (*head);
 (*head) = new;

 return (*head);
}


==========================
3-add_node_end.c

#include <stdlib.h>
#include <string.h>
#include "lists.h"

/**
 * add_node_end - adds a new node at the end of a linked list
 * @head: double pointer to the list_t list
 * @str: string to put in the new node
 *
 * Return: address of the new element, or NULL if it failed
 */
list_t *add_node_end(list_t **head, const char *str)
{
 list_t *new;
 list_t *temp = *head;
 unsigned int len = 0;

 while (str[len])
 len++;

 new = malloc(sizeof(list_t));
 if (!new)
 return (NULL);

 new->str = strdup(str);
 new->len = len;
 new->next = NULL;

 if (*head == NULL)
 {
 *head = new;
 return (new);
 }

 while (temp->next)
 temp = temp->next;

 temp->next = new;

 return (new);
}


============================

4-free_list.c

#include <stdlib.h>
#include "lists.h"

/**
 * free_list - frees a linked list
 * @head: list_t list to be freed
 */
void free_list(list_t *head)
{
 list_t *temp;

 while (head)
 {
 temp = head->next;
 free(head->str);
 free(head);
 head = temp;
 }
}


===============================
100-first.c


#include <stdio.h>

void first(void) __attribute__ ((constructor));

/**
 * first - prints a sentence before the main
 * function is executed
 */
void first(void)
{
 printf("You're beat! and yet, you must allow,\n");
 printf("I bore my house upon my back!\n");
}



===============================
101-hello_holberton.asm


       global    main
          extern    printf
main:
	  mov   edi, format
	  xor   eax, eax
	  call  printf
	  mov 	eax, 0
	  ret
format: db `Hello, Holberton\n`,0


