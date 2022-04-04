/*
 ----------------------------------------------------------
 File:    Question_2.c
 Project: 190197530_a04
 ----------------------------------------------------------
 Author:  Adam Serrao
 ID:      190197530
 Email:   serr7530@mylaurier.ca
 Github Repo: https://github.com/AdamSerrao/Assignment-4/
 User Login : AdamSerrao
 ----------------------------------------------------------
 */

#include <unistd.h>
#include <stdio.h>
#include <stdlib.h>
#include <string.h>

typedef struct node {
	char type;
	int start;
	int end;
	int size;
	char id[3];
	struct node *next;
	struct node *prev;
} node;

struct node *head;

node* create_node(node *new_node, char id[3], char type, int start, int end,
		node *next, node *prev);
node* find_min(int node_size);
void request(char *input);
void release(char *input);
void status();
int main(int argc, char *argv[]) {
	if (argc != 2) {
		printf("Input values are incorrect...exiting with error code -1\n");
		return -1;
	}
	node *start_node;
	start_node = (node*) malloc(sizeof(struct node));
	start_node = create_node(start_node, "HH", 'h', 0, atoi(argv[1]) - 1, NULL,
			NULL);
	head = start_node;
	char input[15];
	char temp[3];

	printf("Allocated %d bytes of memory\n", start_node->size);
	printf("command>");
	scanf("%[^\n]%*c", input);

	while (strcmp(input, "Exit") != 0) {
		strncpy(temp, input, 2);
		if (strcmp(temp, "RQ") == 0)
			request(input);
		else if (strcmp(temp, "RL") == 0)
			release(input);
		else if (strcmp(input, "Status") == 0)
			status();
		else
			printf("Invalid input, use one of RQ, RL, Status, Run, Exit\n");
		printf("command>");
		scanf("%[^\n]%*c", input);
	}

	return 0;
}
node* create_node(node *new_node, char id[3], char type, int start, int end,
		node *next, node *prev) {
	new_node->type = type;
	new_node->start = start;
	memcpy(new_node->id, id, 3);
	new_node->end = end;
	new_node->size = new_node->end - new_node->start + 1;
	new_node->next = next;
	new_node->prev = prev;
	return new_node;
}
void request(char *input) {
	char *token = strtok(input, " ");
	int count = 0;
	int size;
	char id[3];

	while (count < 3) {
		if (count > 0) {
			if (count == 1)
				memcpy(id, token, 3);
			else
				size = atoi(token);
		}
		count += 1;
		token = strtok(NULL, " ");
	}
	node *min = find_min(size);
	if (min != NULL) {
		if (min->size < size) {
			printf("No hole of sufficient size\n");
		} else {

			if (min->size == size) {
				min->type = 'p';
				memcpy(min->id, id, 3);
			} else {
				node *new_node;
				new_node = (node*) malloc(sizeof(struct node));
				new_node = create_node(new_node, id, 'p', min->start,
						min->start + size - 1, min, min->prev);
				if (min->prev != NULL) {
					min->prev->next = new_node;
				}
				min->prev = new_node;
				min->start = min->start + size;
				min->size = min->end - new_node->end;
				if (new_node->start < head->start) {
					head = new_node;
				}

			}
			printf("Successfully allocated %d to process %s\n", size, id);
		}
	} else
		printf("No hole of sufficient size\n");

}
node* find_min(int node_size) {
	node *curr = head;
	node *min;

	if (head->type == 'h') {
		min = head;
	} else {
		min = NULL;
	}

	while (curr != NULL) {
		if (curr->type == 'h' && min == NULL) {
			min = curr;
		} else if (curr->type == 'h' && curr->size > node_size
				&& curr->size < min->size) {
			min = curr;
		}
		curr = curr->next;
	}
	return min;
}
void status() {
	int base = 5;
	node *partitions[base];
	node *holes[base];
	int h_values = 0;
	int p_values = 0;
	int h_sum = 0;
	int p_sum = 0;
	node *curr = head;
	while (curr != NULL) {
		if (curr->type == 'p') {
			partitions[p_values] = curr;
			p_values += 1;
			p_sum += curr->size;
		} else {
			holes[h_values] = curr;
			h_values += 1;
			h_sum += curr->size;
		}
		curr = curr->next;
	}

	printf("Partitions [Allocated Memory = %d]\n", p_sum);
	for (int i = 0; i < p_values; i += 1) {
		printf("Address [%d : %d] Process %s\n", partitions[i]->start,
				partitions[i]->end, partitions[i]->id);

	}
	printf("\n\nHoles [Free Memory = %d]\n", h_sum);
	for (int i = 0; i < h_values; i += 1) {
		printf("Address [%d : %d] len %d\n", holes[i]->start, holes[i]->end,
				holes[i]->size);

	}

}
void release(char *input) {
	char *token = strtok(input, " ");
	char id[3];
	token = strtok(NULL, " ");
	memcpy(id, token, 3);

	node *curr = head;
	while (curr->next != NULL && strcmp(curr->id, id) != 0) {
		curr = curr->next;
	}
	if (curr->prev != NULL && curr->next != NULL && curr->prev->type == 'h'
			&& curr->next->type == 'h') {
		curr->prev->end = curr->next->end;
		curr->prev->size = curr->prev->end - curr->prev->start;
		curr->prev->next = curr->next->next;
	} else if (curr->prev != NULL && curr->prev->type == 'h') {
		curr->prev->end = curr->end;
		curr->prev->size = curr->prev->end - curr->prev->start;
		curr->prev->next = curr->next;
	} else if (curr->next != NULL && curr->next->type == 'h') {
		curr->end = curr->next->end;
		curr->size = curr->end - curr->start;
		curr->next = curr->next->next;
		curr->type = 'h';
		memcpy(curr->id, "HH", 3);
	} else {
		curr->type = 'h';
		memcpy(curr->id, "HH", 3);
	}
	printf("Successfully released memory for process %s\n", id);
}

