#include <stdio.h>
#include <stdlib.h>
#include <string.h>

#define MAX_TODOS 100         // Maximum number of todos
#define MAX_LEN 256           // Max length of each todo text
#define FILE_NAME "todos.txt" // File to save/load todos

// Define a structure to hold each todo item
typedef struct {
    char text[MAX_LEN];
    int completed; // 0 = incomplete, 1 = complete
} Todo;

Todo todos[MAX_TODOS]; // Array of todos
int todoCount = 0;      // Current number of todos

// Function declarations
void loadTodos();
void saveTodos();
void addTodo();
void listTodos();
void toggleTodo();
void deleteTodo();

int main() {
    int choice;

    loadTodos(); // Load todos from file when program starts

    while (1) {
        printf("\n=== TO-DO LIST MENU ===\n");
        printf("1. Add Todo\n");
        printf("2. List Todos\n");
        printf("3. Toggle Completion\n");
        printf("4. Delete Todo\n");
        printf("5. Exit\n");
        printf("Choose an option: ");
        scanf("%d", &choice);
        getchar(); // Clear newline left in input buffer

        switch (choice) {
            case 1: addTodo(); break;
            case 2: listTodos(); break;
            case 3: toggleTodo(); break;
            case 4: deleteTodo(); break;
            case 5: saveTodos(); exit(0); break;
            default: printf("Invalid option!\n");
        }
    }

    return 0;
}

// Load todos from file (if exists)
void loadTodos() {
    FILE *file = fopen(FILE_NAME, "r");
    if (file == NULL) {
        // File not found - start fresh
        printf("No saved todos found. Starting a new list.\n");
        return;
    }

    // Read each line: completed flag, then todo text
    while (fscanf(file, "%d,%[^\n]\n", &todos[todoCount].completed, todos[todoCount].text) == 2) {
        todoCount++;
    }

    fclose(file);
}

// Save todos to file
void saveTodos() {
    FILE *file = fopen(FILE_NAME, "w");
    if (file == NULL) {
        perror("Error saving file");
        return;
    }

    for (int i = 0; i < todoCount; i++) {
        fprintf(file, "%d,%s\n", todos[i].completed, todos[i].text);
    }

    fclose(file);
}

// Add a new todo
void addTodo() {
    if (todoCount >= MAX_TODOS) {
        printf("Todo list is full!\n");
        return;
    }

    printf("Enter new todo: ");
    fgets(todos[todoCount].text, MAX_LEN, stdin);
    todos[todoCount].text[strcspn(todos[todoCount].text, "\n")] = '\0'; // Remove newline
    todos[todoCount].completed = 0;
    todoCount++;

    saveTodos(); // Save immediately after adding
    printf("Todo added!\n");
}

// List all todos
void listTodos() {
    if (todoCount == 0) {
        printf("No todos to show.\n");
        return;
    }

    printf("\nYour Todos:\n");
    for (int i = 0; i < todoCount; i++) {
        printf("%d. [%c] %s\n", i + 1, todos[i].completed ? 'x' : ' ', todos[i].text);
    }
}

// Toggle completion status of a todo
void toggleTodo() {
    int index;
    printf("Enter todo number to toggle: ");
    scanf("%d", &index);
    getchar(); // clear newline

    if (index < 1 || index > todoCount) {
        printf("Invalid number!\n");
        return;
    }

    todos[index - 1].completed = !todos[index - 1].completed;

    saveTodos(); // Save changes
    printf("Todo status updated.\n");
}

// Delete a todo
void deleteTodo() {
    int index;
    printf("Enter todo number to delete: ");
    scanf("%d", &index);
    getchar();

    if (index < 1 || index > todoCount) {
        printf("Invalid number!\n");
        return;
    }

    // Shift the rest of the list
    for (int i = index - 1; i < todoCount - 1; i++) {
        todos[i] = todos[i + 1];
    }
    todoCount--;

    saveTodos(); // Save changes
    printf("Todo deleted.\n");
}
