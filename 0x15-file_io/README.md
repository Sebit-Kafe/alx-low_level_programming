main.h
#ifndef __MAIN_H__
#define __MAIN_H__

#include <sys/types.h>
#include <sys/stat.h>
#include <fcntl.h>
#include <unistd.h>
#include <stdlib.h>

ssize_t read_textfile(const char *filename, size_t letters);
int create_file(const char *filename, char *text_content);
int append_text_to_file(const char *filename, char *text_content);

#endif































0-read_textfile.c

#include "main.h"

/**
 * read_textfile - reads a text file and prints the letters
 * @filename: filename.
 * @letters: numbers of letters printed.
 *
 * Return: numbers of letters printed. It fails, returns 0.
 */
ssize_t read_textfile(const char *filename, size_t letters)
{
	int fd;
	ssize_t nrd, nwr;
	char *buf;

	if (!filename)
		return (0);

	fd = open(filename, O_RDONLY);

	if (fd == -1)
		return (0);

	buf = malloc(sizeof(char) * (letters));
	if (!buf)
		return (0);

	nrd = read(fd, buf, letters);
	nwr = write(STDOUT_FILENO, buf, nrd);

	close(fd);

	free(buf);

	return (nwr);
}





1-create_file.c
#include "main.h"

/**
 * create_file - creates a file
 * @filename: filename.
 * @text_content: content writed in the file.
 *
 * Return: 1 if it success. -1 if it fails.
 */
int create_file(const char *filename, char *text_content)
{
    int fd;
    int nletters;
    int rwr;

    if (!filename)
   	 return (-1);

    fd = open(filename, O_CREAT | O_WRONLY | O_TRUNC, 0600);

    if (fd == -1)
   	 return (-1);

    if (!text_content)
   	 text_content = "";

    for (nletters = 0; text_content[nletters]; nletters++)
   	 ;

    rwr = write(fd, text_content, nletters);

    if (rwr == -1)
   	 return (-1);

    close(fd);

    return (1);
}





2-append_text_to_file.c

#include "main.h"

/**
 * append_text_to_file - appends text at the end of a file
 * @filename: filename.
 * @text_content: added content.
 *
 * Return: 1 if the file exists. -1 if the fails does not exist
 * or if it fails.
 */
int append_text_to_file(const char *filename, char *text_content)
{
    int fd;
    int nletters;
    int rwr;

    if (!filename)
   	 return (-1);

    fd = open(filename, O_WRONLY | O_APPEND);

    if (fd == -1)
   	 return (-1);

    if (text_content)
    {
   	 for (nletters = 0; text_content[nletters]; nletters++)
   		 ;

   	 rwr = write(fd, text_content, nletters);

   	 if (rwr == -1)
   		 return (-1);
    }

    close(fd);

    return (1);
}



3-cp.c

#include "main.h"
#include <stdio.h>

/**
 * error_file - checks if files can be opened.
 * @file_from: file_from.
 * @file_to: file_to.
 * @argv: arguments vector.
 * Return: no return.
 */
void error_file(int file_from, int file_to, char *argv[])
{
    if (file_from == -1)
    {
   	 dprintf(STDERR_FILENO, "Error: Can't read from file %s\n", argv[1]);
   	 exit(98);
    }
    if (file_to == -1)
    {
   	 dprintf(STDERR_FILENO, "Error: Can't write to %s\n", argv[2]);
   	 exit(99);
    }
}

/**
 * main - check the code for Holberton School students.
 * @argc: number of arguments.
 * @argv: arguments vector.
 * Return: Always 0.
 */
int main(int argc, char *argv[])
{
    int file_from, file_to, err_close;
    ssize_t nchars, nwr;
    char buf[1024];

    if (argc != 3)
    {
   	 dprintf(STDERR_FILENO, "%s\n", "Usage: cp file_from file_to");
   	 exit(97);
    }

    file_from = open(argv[1], O_RDONLY);
    file_to = open(argv[2], O_CREAT | O_WRONLY | O_TRUNC | O_APPEND, 0664);
    error_file(file_from, file_to, argv);

    nchars = 1024;
    while (nchars == 1024)
    {
   	 nchars = read(file_from, buf, 1024);
   	 if (nchars == -1)
   		 error_file(-1, 0, argv);
   	 nwr = write(file_to, buf, nchars);
   	 if (nwr == -1)
   		 error_file(0, -1, argv);
    }

    err_close = close(file_from);
    if (err_close == -1)
    {
   	 dprintf(STDERR_FILENO, "Error: Can't close fd %d\n", file_from);
   	 exit(100);
    }

    err_close = close(file_to);
    if (err_close == -1)
    {
   	 dprintf(STDERR_FILENO, "Error: Can't close fd %d\n", file_from);
   	 exit(100);
    }
    return (0);
}














100-elf_header.c


/*
 * File: 100-elf_header.c
 * Auth: Brennan D Baraban
 */

#include <elf.h>
#include <sys/types.h>
#include <sys/stat.h>
#include <fcntl.h>
#include <unistd.h>
#include <stdio.h>
#include <stdlib.h>

void check_elf(unsigned char *e_ident);
void print_magic(unsigned char *e_ident);
void print_class(unsigned char *e_ident);
void print_data(unsigned char *e_ident);
void print_version(unsigned char *e_ident);
void print_abi(unsigned char *e_ident);
void print_osabi(unsigned char *e_ident);
void print_type(unsigned int e_type, unsigned char *e_ident);
void print_entry(unsigned long int e_entry, unsigned char *e_ident);
void close_elf(int elf);

/**
 * check_elf - Checks if a file is an ELF file.
 * @e_ident: A pointer to an array containing the ELF magic numbers.
 *
 * Description: If the file is not an ELF file - exit code 98.
 */
void check_elf(unsigned char *e_ident)
{
    int index;

    for (index = 0; index < 4; index++)
    {
   	 if (e_ident[index] != 127 &&
   	 	e_ident[index] != 'E' &&
   	 	e_ident[index] != 'L' &&
   	 	e_ident[index] != 'F')
   	 {
   		 dprintf(STDERR_FILENO, "Error: Not an ELF file\n");
   		 exit(98);
   	 }
    }
}

/**
 * print_magic - Prints the magic numbers of an ELF header.
 * @e_ident: A pointer to an array containing the ELF magic numbers.
 *
 * Description: Magic numbers are separated by spaces.
 */
void print_magic(unsigned char *e_ident)
{
    int index;

    printf("  Magic:   ");

    for (index = 0; index < EI_NIDENT; index++)
    {
   	 printf("%02x", e_ident[index]);

   	 if (index == EI_NIDENT - 1)
   		 printf("\n");
   	 else
   		 printf(" ");
    }
}

/**
 * print_class - Prints the class of an ELF header.
 * @e_ident: A pointer to an array containing the ELF class.
 */
void print_class(unsigned char *e_ident)
{
    printf("  Class:                         	");

    switch (e_ident[EI_CLASS])
    {
    case ELFCLASSNONE:
   	 printf("none\n");
   	 break;
    case ELFCLASS32:
   	 printf("ELF32\n");
   	 break;
    case ELFCLASS64:
   	 printf("ELF64\n");
   	 break;
    default:
   	 printf("<unknown: %x>\n", e_ident[EI_CLASS]);
    }
}

/**
 * print_data - Prints the data of an ELF header.
 * @e_ident: A pointer to an array containing the ELF class.
 */
void print_data(unsigned char *e_ident)
{
    printf("  Data:                          	");

    switch (e_ident[EI_DATA])
    {
    case ELFDATANONE:
   	 printf("none\n");
   	 break;
    case ELFDATA2LSB:
   	 printf("2's complement, little endian\n");
   	 break;
    case ELFDATA2MSB:
   	 printf("2's complement, big endian\n");
   	 break;
    default:
   	 printf("<unknown: %x>\n", e_ident[EI_CLASS]);
    }
}

/**
 * print_version - Prints the version of an ELF header.
 * @e_ident: A pointer to an array containing the ELF version.
 */
void print_version(unsigned char *e_ident)
{
    printf("  Version:                       	%d",
       	e_ident[EI_VERSION]);

    switch (e_ident[EI_VERSION])
    {
    case EV_CURRENT:
   	 printf(" (current)\n");
   	 break;
    default:
   	 printf("\n");
   	 break;
    }
}

/**
 * print_osabi - Prints the OS/ABI of an ELF header.
 * @e_ident: A pointer to an array containing the ELF version.
 */
void print_osabi(unsigned char *e_ident)
{
    printf("  OS/ABI:                        	");

    switch (e_ident[EI_OSABI])
    {
    case ELFOSABI_NONE:
   	 printf("UNIX - System V\n");
   	 break;
    case ELFOSABI_HPUX:
   	 printf("UNIX - HP-UX\n");
   	 break;
    case ELFOSABI_NETBSD:
   	 printf("UNIX - NetBSD\n");
   	 break;
    case ELFOSABI_LINUX:
   	 printf("UNIX - Linux\n");
   	 break;
    case ELFOSABI_SOLARIS:
   	 printf("UNIX - Solaris\n");
   	 break;
    case ELFOSABI_IRIX:
   	 printf("UNIX - IRIX\n");
   	 break;
    case ELFOSABI_FREEBSD:
   	 printf("UNIX - FreeBSD\n");
   	 break;
    case ELFOSABI_TRU64:
   	 printf("UNIX - TRU64\n");
   	 break;
    case ELFOSABI_ARM:
   	 printf("ARM\n");
   	 break;
    case ELFOSABI_STANDALONE:
   	 printf("Standalone App\n");
   	 break;
    default:
   	 printf("<unknown: %x>\n", e_ident[EI_OSABI]);
    }
}

/**
 * print_abi - Prints the ABI version of an ELF header.
 * @e_ident: A pointer to an array containing the ELF ABI version.
 */
void print_abi(unsigned char *e_ident)
{
    printf("  ABI Version:                   	%d\n",
       	e_ident[EI_ABIVERSION]);
}

/**
 * print_type - Prints the type of an ELF header.
 * @e_type: The ELF type.
 * @e_ident: A pointer to an array containing the ELF class.
 */
void print_type(unsigned int e_type, unsigned char *e_ident)
{
    if (e_ident[EI_DATA] == ELFDATA2MSB)
   	 e_type >>= 8;

    printf("  Type:                          	");

    switch (e_type)
    {
    case ET_NONE:
   	 printf("NONE (None)\n");
   	 break;
    case ET_REL:
   	 printf("REL (Relocatable file)\n");
   	 break;
    case ET_EXEC:
   	 printf("EXEC (Executable file)\n");
   	 break;
    case ET_DYN:
   	 printf("DYN (Shared object file)\n");
   	 break;
    case ET_CORE:
   	 printf("CORE (Core file)\n");
   	 break;
    default:
   	 printf("<unknown: %x>\n", e_type);
    }
}

/**
 * print_entry - Prints the entry point of an ELF header.
 * @e_entry: The address of the ELF entry point.
 * @e_ident: A pointer to an array containing the ELF class.
 */
void print_entry(unsigned long int e_entry, unsigned char *e_ident)
{
    printf("  Entry point address:           	");

    if (e_ident[EI_DATA] == ELFDATA2MSB)
    {
   	 e_entry = ((e_entry << 8) & 0xFF00FF00) |
   		   ((e_entry >> 8) & 0xFF00FF);
   	 e_entry = (e_entry << 16) | (e_entry >> 16);
    }

    if (e_ident[EI_CLASS] == ELFCLASS32)
   	 printf("%#x\n", (unsigned int)e_entry);

    else
   	 printf("%#lx\n", e_entry);
}

/**
 * close_elf - Closes an ELF file.
 * @elf: The file descriptor of the ELF file.
 *
 * Description: If the file cannot be closed - exit code 98.
 */
void close_elf(int elf)
{
    if (close(elf) == -1)
    {
   	 dprintf(STDERR_FILENO,
   		 "Error: Can't close fd %d\n", elf);
   	 exit(98);
    }
}

/**
 * main - Displays the information contained in the
 *    	ELF header at the start of an ELF file.
 * @argc: The number of arguments supplied to the program.
 * @argv: An array of pointers to the arguments.
 *
 * Return: 0 on success.
 *
 * Description: If the file is not an ELF File or
 *          	the function fails - exit code 98.
 */
int main(int __attribute__((__unused__)) argc, char *argv[])
{
    Elf64_Ehdr *header;
    int o, r;

    o = open(argv[1], O_RDONLY);
    if (o == -1)
    {
   	 dprintf(STDERR_FILENO, "Error: Can't read file %s\n", argv[1]);
   	 exit(98);
    }
    header = malloc(sizeof(Elf64_Ehdr));
    if (header == NULL)
    {
   	 close_elf(o);
   	 dprintf(STDERR_FILENO, "Error: Can't read file %s\n", argv[1]);
   	 exit(98);
    }
    r = read(o, header, sizeof(Elf64_Ehdr));
    if (r == -1)
    {
   	 free(header);
   	 close_elf(o);
   	 dprintf(STDERR_FILENO, "Error: `%s`: No such file\n", argv[1]);
   	 exit(98);
    }

    check_elf(header->e_ident);
    printf("ELF Header:\n");
    print_magic(header->e_ident);
    print_class(header->e_ident);
    print_data(header->e_ident);
    print_version(header->e_ident);
    print_osabi(header->e_ident);
    print_abi(header->e_ident);
    print_type(header->e_type, header->e_ident);
    print_entry(header->e_entry, header->e_ident);

    free(header);
    close_elf(o);
    return (0);
}

