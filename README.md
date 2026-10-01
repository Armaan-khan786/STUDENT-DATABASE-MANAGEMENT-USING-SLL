/////////////HEADER.H///////////////

#include<stdio.h>
#include<stdlib.h>
#include<string.h>

typedef struct student
{
	int rollno;
	char name[50];
	float percentage;
	struct student *next;
}SLL;

void add(SLL **);
void del(SLL **);
void print(SLL *);
void modify(SLL *);
void save(SLL *);
void load(SLL **);
void sort(SLL **);
void delete_all(SLL **);
void reverse(SLL **);
//////////////////MAIN.C///////////////////////////////////
#include"student.h"

int main()
{
	SLL *headptr=0;
	char op,ch;

	load(&headptr);

	while(1)
	{
		printf("\n******** STUDENT RECORD MENU ********\n");
		printf("a/A : Add new record\n");
		printf("d/D : Delete a record\n");
		printf("s/S : Show the list\n");
		printf("m/M : Modify a record\n");
		printf("v/V : Save records\n");
		printf("e/E : Exit\n");
		printf("t/T : Sort the list\n");
		printf("l/L : Delete all the records\n");
		printf("r/R : Reverse the list\n");

		printf("Enter your choice : ");
		scanf(" %c",&op);

		switch(op)
		{
			case 'a':
			case 'A':
				add(&headptr);
				break;

			case 'd':
			case 'D':
				del(&headptr);
				break;

			case 's':
			case 'S':
				print(headptr);
				break;

			case 'm':
			case 'M':
				modify(headptr);
				break;

			case 'v':
			case 'V':
				save(headptr);
				break;

			case 't':
			case 'T':
				sort(&headptr);
				break;

			case 'l':
			case 'L':
				delete_all(&headptr);
				break;

			case 'r':
			case 'R':
				reverse(&headptr);
				break;

			case 'e':
			case 'E':

				printf("S/s : Save and exit\n");
				printf("E/e : Exit without saving\n");
				printf("Enter choice : ");
				scanf(" %c",&ch);

				if(ch=='S' || ch=='s')
					save(headptr);

				delete_all(&headptr);

				return 0;

			default:
				printf("Invalid choice\n");
		}
	}
}


void add(SLL **ptr)
{
	SLL *new,*p;
	int roll=1;

	new=malloc(sizeof(SLL));

	if(new==0)
	{
		printf("Memory allocation failed\n");
		return;
	}

	while(1)
	{
		p=*ptr;

		while(p)
		{
			if(p->rollno==roll)
				break;

			p=p->next;
		}

		if(p==0)
			break;

		roll++;
	}

	new->rollno=roll;

	printf("Enter name : ");
	scanf(" %[^\n]",new->name);

	printf("Enter percentage : ");
	scanf("%f",&new->percentage);

	while(new->percentage<0 || new->percentage>100)
	{
		printf("Enter percentage between 0 and 100 : ");
		scanf("%f",&new->percentage);
	}

	new->next=0;

	if(*ptr==0)
	{
		*ptr=new;
	}
	else
	{
		p=*ptr;

		while(p->next)
			p=p->next;

		p->next=new;
	}

	printf("Record added\n");
}


void print(SLL *ptr)
{
	if(ptr==0)
	{
		printf("No records\n");
		return;
	}

	printf("\nRoll No\tName\t\tPercentage\n");

	while(ptr)
	{
		printf("%d\t%s\t\t%.2f\n",
				ptr->rollno,
				ptr->name,
				ptr->percentage);

		ptr=ptr->next;
	}
}


void del(SLL **ptr)
{
	SLL *p,*prev;
	int roll;

	if(*ptr==0)
	{
		printf("No records\n");
		return;
	}

	printf("Enter roll number : ");
	scanf("%d",&roll);

	p=*ptr;
	prev=0;

	while(p)
	{
		if(p->rollno==roll)
			break;

		prev=p;
		p=p->next;
	}

	if(p==0)
	{
		printf("Record not found\n");
		return;
	}

	if(prev==0)
		*ptr=p->next;
	else
		prev->next=p->next;

	free(p);

	printf("Record deleted\n");
}


void modify(SLL *ptr)
{
	int roll;
	float percentage;

	if(ptr==0)
	{
		printf("No records\n");
		return;
	}

	printf("Enter roll number : ");
	scanf("%d",&roll);

	while(ptr)
	{
		if(ptr->rollno==roll)
			break;

		ptr=ptr->next;
	}

	if(ptr==0)
	{
		printf("Record not found\n");
		return;
	}

	printf("Enter new name : ");
	scanf(" %[^\n]",ptr->name);

	printf("Enter new percentage : ");
	scanf("%f",&percentage);

	while(percentage<0 || percentage>100)
	{
		printf("Enter percentage between 0 and 100 : ");
		scanf("%f",&percentage);
	}

	ptr->percentage=percentage;

	printf("Record modified\n");
}


void save(SLL *ptr)
{
	FILE *fp;

	fp=fopen("student.dat","wb");

	if(fp==0)
	{
		printf("File opening failed\n");
		return;
	}

	while(ptr)
	{
		fwrite(&ptr->rollno,sizeof(int),1,fp);
		fwrite(ptr->name,sizeof(char),50,fp);
		fwrite(&ptr->percentage,sizeof(float),1,fp);

		ptr=ptr->next;
	}

	fclose(fp);

	printf("Records saved\n");
}


void load(SLL **ptr)
{
	FILE *fp;
	SLL *new,*p;

	fp=fopen("student.dat","rb");

	if(fp==0)
		return;

	while(1)
	{
		new=malloc(sizeof(SLL));

		if(new==0)
			break;

		if(fread(&new->rollno,sizeof(int),1,fp)!=1)
		{
			free(new);
			break;
		}

		fread(new->name,sizeof(char),50,fp);
		fread(&new->percentage,sizeof(float),1,fp);

		new->next=0;

		if(*ptr==0)
		{
			*ptr=new;
		}
		else
		{
			p=*ptr;

			while(p->next)
				p=p->next;

			p->next=new;
		}
	}

	fclose(fp);
}


void sort(SLL **ptr)
{
	SLL *p,*q;
	int roll;
	char name[50];
	float percentage;

	if(*ptr==0)
	{
		printf("No records\n");
		return;
	}

	p=*ptr;

	while(p)
	{
		q=p->next;

		while(q)
		{
			if(p->percentage<q->percentage)
			{
				roll=p->rollno;
				p->rollno=q->rollno;
				q->rollno=roll;

				strcpy(name,p->name);
				strcpy(p->name,q->name);
				strcpy(q->name,name);

				percentage=p->percentage;
				p->percentage=q->percentage;
				q->percentage=percentage;
			}

			q=q->next;
		}

		p=p->next;
	}

	printf("List sorted by percentage\n");
}


void delete_all(SLL **ptr)
{
	SLL *p;

	while(*ptr)
	{
		p=*ptr;
		*ptr=p->next;
		free(p);
	}

	printf("All records deleted\n");
}


void reverse(SLL **ptr)
{
	SLL *prev,*cur,*next;

	prev=0;
	cur=*ptr;

	while(cur)
	{
		next=cur->next;
		cur->next=prev;
		prev=cur;
		cur=next;
	}

	*ptr=prev;

	printf("List reversed\n");
}




