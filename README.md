# Backup-System
#include <stdio.h>
#include <stdlib.h>
#include <string.h>

#define BUFFER_SIZE 4096

// User struct to store username, password, and role
typedef struct {
    char username[20];
    char password[20];
    char role[10];
} User;

// Function to perform XOR encryption
void xorEncryptDecrypt(unsigned char* data, size_t size, const char* key) {
    size_t key_len = strlen(key);
    for (size_t i = 0; i < size; ++i) {
        data[i] ^= key[i % key_len];
    }
}

// Backup manager function to perform backup operation
void performBackup(const char* sourcePath, const char* destPath, const char* encryptionKey) {
    FILE* sourceFile = fopen(sourcePath, "rb");
    if (!sourceFile) {
        perror("Error opening source file");
        exit(EXIT_FAILURE);
    }

    FILE* destFile = fopen(destPath, "wb");
    if (!destFile) {
        perror("Error opening destination file");
        exit(EXIT_FAILURE);
    }

    unsigned char buffer[BUFFER_SIZE];
    size_t bytes_read;
    while ((bytes_read = fread(buffer, 1, BUFFER_SIZE, sourceFile)) > 0) {
        xorEncryptDecrypt(buffer, bytes_read, encryptionKey);
        fwrite(buffer, 1, bytes_read, destFile);
    }

    fclose(sourceFile);
    fclose(destFile);

    printf("Backup completed successfully.\n");
}

// Restore manager function to perform restore operation
void performRestore(const char* sourcePath, const char* destPath, const char* encryptionKey) {
    FILE* sourceFile = fopen(sourcePath, "rb");
    if (!sourceFile) {
        perror("Error opening source file");
        exit(EXIT_FAILURE);
    }

    FILE* destFile = fopen(destPath, "wb");
    if (!destFile) {
        perror("Error opening destination file");
        exit(EXIT_FAILURE);
    }

    unsigned char buffer[BUFFER_SIZE];
    size_t bytes_read;
    while ((bytes_read = fread(buffer, 1, BUFFER_SIZE, sourceFile)) > 0) {
        xorEncryptDecrypt(buffer, bytes_read, encryptionKey);
        fwrite(buffer, 1, bytes_read, destFile);
    }

    fclose(sourceFile);
    fclose(destFile);

    printf("Restore completed successfully.\n");
}

int main() {
    // Create admin user
    User admin = {"admin", "admin123", "admin"};

    char username[20], password[20];

    // Prompt user for username and password
    printf("Welcome to Backup System!\n");
    printf("Please enter your username: ");
    scanf("%s", username);

    printf("Please enter your password: ");
    scanf("%s", password);

    // Authenticate admin user
    if (strcmp(admin.password, password) == 0 && strcmp(admin.username, username) == 0) {
        printf("Authentication successful. Welcome, %s!\n", username);

        // Prompt user for operation choice
        printf("Choose an operation:\n");
        printf("1. Backup\n");
        printf("2. Restore\n");
        printf("Enter your choice: ");
        int choice;
        scanf("%d", &choice);

        char sourcePath[100], destPath[100], encryptionKey[100];

        // Perform selected operation
        if (choice == 1) {
            // Prompt user for backup file paths
            printf("Please enter the path of the file to backup: ");
            scanf("%s", sourcePath);

            printf("Please enter the destination path for the backup file: ");
            scanf("%s", destPath);

            printf("Please enter the encryption key: ");
            scanf("%s", encryptionKey);

            // Perform backup operation
            performBackup(sourcePath, destPath, encryptionKey);
        } else if (choice == 2) {
            // Prompt user for restore file paths
            printf("Please enter the path of the backup file: ");
            scanf("%s", sourcePath);

            printf("Please enter the destination path for the restored file: ");
            scanf("%s", destPath);

            printf("Please enter the encryption key: ");
            scanf("%s", encryptionKey);

            // Perform restore operation
            performRestore(sourcePath, destPath, encryptionKey);
        } else {
            printf("Invalid choice. Exiting program.\n");
        }
    } else {
        printf("Authentication failed. Only admin can access the system.\n");
    }

    return 0;
}

