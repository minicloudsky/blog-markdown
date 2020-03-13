---
title: 哈弗曼树
date: 2017-04-12 23:48:08
categories:
- 算法
tags:
- 算法

---
```c
//哈夫曼编码，输入编码个数和权值，输出编码

#include <stdio.h>
#include <stdlib.h>
#include <string.h>

typedef char* HuffmanCode;

typedef struct 
{
	unsigned int weight ; 
	unsigned int parent, LChild,RChild ; 
}HTNode, * HuffmanTree;  
void select(HuffmanTree *ht,int n, int *s1, int *s2)
{
	int i;
	int min;
	for(i=1; i<=n; i++)
	{
		if((*ht)[i].parent == 0)
		{
			min = i;
			i = n+1;
		}
	}
	for(i=1; i<=n; i++)
	{
		if((*ht)[i].parent == 0)
		{
			if((*ht)[i].weight < (*ht)[min].weight)
				min = i;
		}
	}
	*s1 = min;
	for(i=1; i<=n; i++)
	{
		if((*ht)[i].parent == 0 && i!=(*s1))
		{
			min = i;
			i = n+1;
		}
	}
	for(i=1; i<=n; i++)
	{
		if((*ht)[i].parent == 0 && i!=(*s1))
		{
			if((*ht)[i].weight < (*ht)[min].weight)
				min = i;
		}
	}
	*s2 = min;
}

void CrtHuffmanTree(HuffmanTree *ht , int *w, int n)
{
	int m,i;
	int s1,s2;
	m=2*n-1;
	*ht=(HuffmanTree)malloc((m+1)*sizeof(HTNode)); 
	for(i=1;i<=n;i++) 
	{
		(*ht)[i].weight = w[i];
		(*ht)[i].LChild = 0;
		(*ht)[i].parent = 0;
		(*ht)[i].RChild = 0;
	}		
	for(i=n+1;i<=m;i++)
	{
		(*ht)[i].weight = 0;
		(*ht)[i].LChild = 0;
		(*ht)[i].parent = 0;
		(*ht)[i].RChild = 0;
	}   

	for(i=n+1;i<=m;i++)   
	{  
		select(ht,i-1,&s1,&s2);
		(*ht)[s1].parent=i;
		(*ht)[s2].parent=i;
		(*ht)[i].LChild=s1;
		(*ht)[i].RChild=s2;
		(*ht)[i].weight=(*ht)[s1].weight+(*ht)[s2].weight;
	} 
}
void outputHuffman(HuffmanTree HT, int m)
{
	if(m!=0)
	{
		printf("%d  ", HT[m].weight);
		outputHuffman(HT,HT[m].LChild);
		outputHuffman(HT,HT[m].RChild);
	}
}

void CrtHuffmanCode(HuffmanTree *ht, HuffmanCode *hc, int n)

{
	char *cd;
	int i;
	unsigned int c;
	int start;
	int p;
	hc=(HuffmanCode *)malloc((n+1)*sizeof(char *)); 
	cd=(char * )malloc(n * sizeof(char )); 
	cd[n-1]='\0';   
	for(i=1;i<=n;i++)  
	{
		start=n-1;    
		for(c=i,p=(*ht)[i].parent; p!=0; c=p,p=(*ht)[p].parent) 
			if( (*ht)[p].LChild == c) 
				cd[--start]='0';  
			else 
				cd[--start]='1';  
	hc[i]=(char *)malloc((n-start)*sizeof(char));  
	strcpy(hc[i],&cd[start]);
	}
	free(cd);
	for(i=1;i<=n;i++)
		printf("权值为%d的编码为: %s\n",(*ht)[i].weight,hc[i]);
}


void main() 
{ 
	HuffmanTree HT; 
	HuffmanCode HC; 
	int *w; 
	int i,n;     
	int wei;   
	int m;
	printf("input the total number of the Huffman Tree:" ); 
	scanf("%d",&n); 
	w=(int *)malloc((n+1)*sizeof(int)); 
	for(i=1;i<=n;i++)
	{ 
		printf("input the %d element's weight:",i); 
		fflush(stdin);
		scanf("%d",&wei); 
		w[i]=wei; 
	} 

	CrtHuffmanTree(&HT,w,n); 
	m = 2*n-1;
	outputHuffman(HT,m); 
	printf("\n");
	CrtHuffmanCode(&HT,&HC,n);	
} 
```
