/*
 ----------------------------------------------------------
 File:    Question_1.c
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
#include <pthread.h>

#define c_num 5
#define p_num 4

int maximum[c_num][p_num] = { { 6, 4, 7, 3 }, { 4, 2, 3, 2 }, { 2, 5, 3, 3 }, {
		6, 3, 3, 2 }, { 5, 5, 7, 5 } };
int allocated[c_num][p_num];
int need[c_num][p_num];
int *available = 0;

void print_matrix(int matrix[c_num][p_num]);
void print_list(int list[], int max);
void request();
void release();
int safe_check(int list1[], int list2[]);
void status();
int* safe_sequence();
void* run(void *t);

int main(int argc, char *argv[]) {
	if (argc != p_num + 1) {
		printf("Input values are incorrect...exiting with error code -1\n");
		return -1;
	}
	available = (int*) realloc(available, argc);

	for (int i = 0; i < p_num; i = i + 1) {
		available[i] = atoi(argv[i + 1]);
	}

	char input[15];
	char temp[3];
	pthread_t thread;

	printf("Number of Customers: 5\n");
	printf("Currently Available Resources: ");
	print_list(available, p_num);
	printf("Maximum Resources from file: \n");
	print_matrix(maximum);
	printf("Enter a Command: ");

	scanf("%[^\n]%*c", input);

	while (strcmp(input, "Exit") != 0) {
		strncpy(temp, input, 2);
		temp[3] = '\0';
		if (strcmp(temp, "RQ") == 0)
			request(input);
		else if (strcmp(temp, "RL") == 0)
			request(input);
		else if (strcmp(input, "Status") == 0)
			status();
		else if (strcmp(input, "Run") == 0) {
			int sequence[c_num];
			memcpy(sequence, safe_sequence(), sizeof(int) * c_num);
			printf("Safe Sequence is: ");
			print_list(sequence, c_num);
			for (int i = 0; i < c_num; i += 1) {
				int *id = malloc(sizeof(*id));
				*id = sequence[i];
				pthread_create(&thread, 0, run, id);
				pthread_join(thread, NULL);
			}
		} else
			printf("Invalid input, use one of RQ, RL, Status, Run, Exit\n");
		printf("Enter a Command: ");
		scanf("%[^\n]%*c", input);
	}
	return 0;
}

void print_matrix(int matrix[c_num][p_num]) {
	for (int i = 0; i < 5; i = i + 1) {
		for (int j = 0; j < 4; j = j + 1) {
			printf("%d ", matrix[i][j]);
		}
		printf("\n");
	}
}

void print_list(int list[], int max) {
	for (int i = 0; i < max; i = i + 1) {
		printf("%d ", list[i]);
	}
	printf("\n");
}

int safe_check(int list1[], int list2[]) {
	int check = 0;
	for (int i = 0; i < p_num; i = i + 1) {
		if (list1[i] > list2[i] || list1[i] < 0) {
			check = 1;
			break;
		}
	}
	return check;
}

void request(char *input) {
	char *token = strtok(input, " ");
	int count = 0;
	int id;
	int requested[p_num];
	int temp[p_num];
	int check;
	while (count < 6) {
		if (count > 0) {
			if (count == 1)
				id = atoi(token);
			requested[count - 2] = atoi(token);
		}
		count += 1;
		token = strtok(NULL, " ");
	}

	for (int i = 0; i < p_num; i = i + 1) {
		temp[i] = allocated[id][i] + requested[i];
	}

	check = safe_check(temp, maximum[id]);

	if (check == 0) {
		for (int i = 0; i < p_num; i = i + 1) {
			available[i] = available[i] - temp[i];
			allocated[id][i] = temp[i];
			need[id][i] = maximum[id][i] - temp[i];
		}
		printf("State is safe, and request is satisfied\n");
	} else
		printf("Unsafe State, request is denied\n");
}
void release(char *input) {
	char *token = strtok(input, " ");
	int count = 0;
	int id;
	int requested[p_num];
	int temp[p_num];
	int check;
	while (count < 6) {
		if (count > 0) {
			if (count == 1)
				id = atoi(token);
			requested[count - 2] = atoi(token);
		}
		count += 1;
		token = strtok(NULL, " ");
	}

	for (int i = 0; i < p_num; i = i + 1) {
		temp[i] = allocated[id][i] - requested[i];
	}

	check = safe_check(temp, maximum[id]);

	if (check == 0) {
		for (int i = 0; i < p_num; i = i + 1) {
			available[i] = available[i] + temp[i];
			allocated[id][i] = temp[i];
			need[id][i] = maximum[id][i] + temp[i];
		}
		printf("State is safe, and request is satisfied\n");
	} else
		printf("Unsafe State, request is denied\n");
}
void status() {
	printf("Available Resources:\n");
	print_list(available, p_num);
	printf("Maximum Resources:\n");
	print_matrix(maximum);
	printf("Allocated Resources:\n");
	print_matrix(allocated);
	printf("Need Resources:\n");
	print_matrix(need);
}

int* safe_sequence() {
	int count = 0;
	int recheck[c_num] = { -1, -1, -1, -1, -1 };
	int values = 0;
	int re_value = 0;
	int check;
	int switched = 0;
	static int sequence[c_num];
	int temp[c_num];

	for (int i = 0; i < p_num; i += 1) {
		temp[i] = available[i];
	}
	while (values < c_num) {
		if (count == c_num)
			switched = 1;

		if (switched == 1) {
			count = recheck[0];
			re_value -= 1;
			for (int j = 0; j < re_value; j += 1) {
				recheck[j] = recheck[j + 1];
			}
		}

		check = safe_check(need[count], temp);

		if (check == 0) {
			for (int k = 0; k < p_num; k += 1) {
				temp[k] = temp[k] + allocated[count][k];
			}
			sequence[values] = count;
			values += 1;
		} else {
			recheck[re_value] = count;
			re_value += 1;
		}
		count += 1;
	}

	return sequence;
}
void* run(void *p) {
	int id = *((int*) p);
	printf("--> Customer/Thread %d\n", id);
	printf("    Allocated Resources: ");
	print_list(allocated[id], p_num);
	printf("    Needed: ");
	print_list(need[id], p_num);
	printf("    Available: ");
	print_list(available, p_num);
	printf("    Thread has started\n");
	printf("    Thread has finished\n");
	printf("    Thread is releasing resources\n");
	for (int i = 0; i < p_num; i += 1) {
		available[i] = available[i] + allocated[id][i];
	}
	printf("    New Available: ");
	print_list(available, p_num);
	return 0;
}

