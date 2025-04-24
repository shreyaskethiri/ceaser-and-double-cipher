#include <stdio.h>
#include <string.h>
#include <stdbool.h>
#include<stdlib.h>

int ENCR()
{
    char plain[100], ch;
    int key, i;

    printf("\nEnter PLAIN text: ");

    fgets(plain, sizeof(plain), stdin);
    plain[strcspn(plain, "\n")] = '\0';

    printf("\nEnter KEY value: ");
    scanf("%d", &key);

    for (i = 0; i < strlen(plain); i++)
    {
        ch = plain[i];

        if (ch >= 'A' && ch <= 'Z')
        {
            plain[i] = ((ch - 'A' + key) % 26) + 'A';
        }
        else if (ch >= 'a' && ch <= 'z')
        {
            plain[i] = ((ch - 'a' + key) % 26) + 'a';
        }
    }

    printf("ENCRYPTED text: %s\n", plain);
    return 0;
}

int DECR()
{
    char plain[100], ch;
    int key, i, m;

    printf("Enter ENCRYPTED text: ");
    fgets(plain, sizeof(plain), stdin);
    plain[strcspn(plain, "\n")] = '\0';

    printf("Enter KEY value: ");
    scanf("%d", &key);

    for (i = 0; i < strlen(plain); i++)
    {
        ch = plain[i];

        if (ch >= 'A' && ch <= 'Z')
        {
            m = ch - 'A';
            if (m < 0)
            {
                m += 26;
            }
            plain[i] = (((m - key) + 26) % 26) + 'A';
        }
        else if (ch >= 'a' && ch <= 'z')
        {
            m = ch - 'a';
            if (m < 0)
            {
                m += 26;
            }
            plain[i] = (((m - key) + 26) % 26) + 'a';
        }
    }

    printf("DECRYPTED text: %s\n", plain);
    return 0;
}

int casercipher() {
    int a;
    char y;

    while (true) {
        printf("\nENTER 1:ENCRYPTION 2:DECRYPTION: ");
        scanf("%d", &a);
        while (getchar() != '\n'); // Clear input buffer

        if (a == 1) {
            ENCR();
        } else if (a == 2) {
            DECR();
        } else {
            printf("\nInvalid option. Exiting...\n");
            break;
        }

        printf("\nDo you want to continue caeser? (y/n): ");
        scanf(" %c", &y);
        while (getchar() != '\n'); // Clear input buffer again

        if (y != 'y' && y != 'Y') {
            printf("\nExiting the program. Thank you!\n");
            break;
        }
    }

    return 0;
}


int compare(const void*a,const void*b){
    return(*(char*)a-*(char*)b);
}

void ENCRyp(char plain[100],char key[100],char encodedText[100]){
    char o[2]="~",cipher[100]={0};
    int index=0;
    
    //TO MAKE THE "PLAIN" DIVISIBLE BY "KEY"
    while(strlen(plain)%strlen(key)!=0){
        strcat(plain,o);
    }
    
    //TO CHANGE THE SPACES INTO VALUES
    for(int i=0;i<strlen(plain);i++){
        if(plain[i]==' '){
            plain[i]='~';
        }
    }
    
    //ASSIGNING COLUMN,ROW LENGTHS TO VARIABLES
    int a=strlen(plain);
    int b=strlen(key);
    int c=a/b;
    int d=c+1;
    char dummy[100]={0};
    
    //APPENDING EACH TERM OF KEY AND EVERY "b"TH TERM OF PLAIN
    for(int i=0;i<(a+b);i+=d){
        int m=i/d;
        cipher[i]=key[m];
        for(int j=m;j<=a;j+=b){
            int l=((j-m)/b)+(i+1);
            cipher[l]=plain[j];
        }
    }
    cipher[a+b]='\0';
    
    //MAKING DUPLICATE OF "KEY"
    for(int i=0;i<b;i++){
        dummy[i]=key[i];
        
    }
    
    //SORTING KEY VALUES
    qsort(dummy,b,sizeof(char),compare);

    //APPENDING THE "PLAIN" VALUE RESPECTVE  ASCI ORDER OF "KEY"
    printf("\nFinal Encoded Text:");
    for(int i=0;i<b;i++){
        for(int j=0;j<(a+b);j+=d){
            if(dummy[i]==cipher[j]){
                cipher[j]='~';
                for(int n=j+1;n<j+1+c;n++){
                    encodedText[index++]=cipher[n];
                }
            }
        }
    }
    encodedText[index]='\0'; 
    for (int i = 0; i < strlen(encodedText); i++) {
        if (encodedText[i] == '~') {
            encodedText[i] = ' ';
        }
    }

   
}

int ENCRYPTION(){
    char plain[100], key1[100], key2[100], encodedText[100];

    printf("\nEnter PLAIN text:");
    fgets(plain,sizeof(plain),stdin);
    plain[strcspn(plain,"\n")]='\0';

    printf("\nEnter the FIRST KEY:");
    fgets(key1,sizeof(key1),stdin);
    key1[strcspn(key1,"\n")]='\0';

    printf("\nEnter the SECOND KEY:");
    fgets(key2,sizeof(key2),stdin);
    key2[strcspn(key2,"\n")]='\0';

    // First encryption
    ENCRyp(plain, key1, encodedText);
    printf("\nEncoded Text after first encryption: %s\n", encodedText);

    // Second encryption (passing encodedText)
    ENCRyp(encodedText, key2, encodedText);
    printf("\nEncoded Text after second encryption: %s\n", encodedText);

    return 0;
}
void DECRyp(char encodedText[100], char key[100], char decodedText[100]) {
    char dummy[100] = {0}, sortedKey[100] = {0};
    int index = 0;

    int a = strlen(encodedText);     // Length of encoded text
    int b = strlen(key);             // Key length
    int c = a / b;                   // Rows

    // Duplicate and sort the key
    strcpy(dummy, key);
    strcpy(sortedKey, key);
    qsort(sortedKey, b, sizeof(char), compare);

    // Fill the columns according to sorted key
    char matrix[20][20] = {0};       // Matrix to hold the columns
    int k = 0;
    int used[100] = {0};

    for (int s = 0; s < b; s++) {
        for (int j = 0; j < b; j++) {
            if (!used[j] && sortedKey[s] == dummy[j]) {
                for (int i = 0; i < c; i++) {
                    matrix[i][j] = encodedText[k++];
                }
                used[j] = 1;
                break;
            }
        }
    }

    // Read matrix row-wise to get plain text
    index = 0;
    for (int i = 0; i < c; i++) {
        for (int j = 0; j < b; j++) {
            decodedText[index++] = matrix[i][j];
        }
    }

    decodedText[index] = '\0';

    // Replace '~' back to space
    for (int i = 0; decodedText[i]; i++) {
        if (decodedText[i] == '~') {
            decodedText[i] = ' ';
        }
    }
}
int DECRYPTION(){
    char first[100], final[100];
    char plain[100], key1[100], key2[100], encodedText[100];

printf("\nEnter the ENCRYPTED text: ");
fgets(final, sizeof(final), stdin);
final[strcspn(final, "\n")] = '\0';

printf("\nEnter the SECOND KEY: ");
fgets(key2, sizeof(key2), stdin);
key2[strcspn(key2, "\n")] = '\0';

printf("\nEnter the FIRST KEY: ");
fgets(key1, sizeof(key1), stdin);
key1[strcspn(key1, "\n")] = '\0';

// reverse second encryption first
DECRyp(final, key2, first);

// then reverse first encryption
DECRyp(first, key1, final);

printf("\nDecrypted Text: %s\n", final);

}
int doublecolumnar() {
    int a;
    char y;

    while (true) {
        printf("\nENTER 1:ENCRYPTION 2:DECRYPTION: ");
        scanf("%d", &a);
        while (getchar() != '\n'); // Clear the input buffer

        if (a == 1) {
            ENCRYPTION();
        } else if (a==2){
            DECRYPTION();
        }else {
            break;
        }

        printf("\n\nDo you want to continue columnar? (y/n): ");
        scanf(" %c", &y);
        while (getchar() != '\n'); // Clear input buffer again

        if (y != 'y' && y != 'Y') {
            printf("\nExiting the program. Thank you!\n");
            break;
        }
    }

    return 0;
}



int main() {
    int a;
    char y;

    while (true) {
        printf("\nENTER 1: CAESAR 2: DOUBLE COLUMNAR: ");
        scanf("%d", &a);
        while (getchar() != '\n'); // Clear input buffer

        if (a == 1) {
            casercipher(); // Call the correct Caesar cipher function
        } else if (a == 2) {
            doublecolumnar(); // Double Columnar Encryption
        } else {
            printf("\nInvalid option. Exiting...\n");
            break;
        }

        printf("\nDo you want to continue? (y/n): ");
        scanf(" %c", &y);
        while (getchar() != '\n'); // Clear input buffer

        if (y != 'y' && y != 'Y') {
            printf("\nExiting the program. Thank you!\n");
            break;
        }
    }

    return 0;
}
# ceaser-and-double-cipher
This program is all about encryption and decryption by using ceaser and double columnar cipher 
