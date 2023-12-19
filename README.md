#include "lib.h"
#include <stdio.h>
#include <string.h>
#include <stdlib.h>
#include <ctype.h>
#define ANSI_COLOR_GREEN  "\033[32m"
#define ROJO "\x1B[31m"
#define RESET "\x1b[0m"


void Trim(char *str)
{
    int i, j;
    int n = strlen(str);
    // Eliminar espacios al principio
    i = 0;
    while (str[i] == ' ')
    {
        i++;
    }
    // Mover los caracteres restantes al principio de la cadena
    if (i > 0)
    {
        for (j = 0; j < n - i; j++)
        {
            str[j] = str[j + i];
        }
        str[j] = '\0';
    }
    // Eliminar espacios al final
    n = strlen(str);
    i = n - 1;
    while (str[i] == ' ')
    {
        str[i] = '\0';
        i--;
    }
}
int Varok(const char *s)
{
    int i;
    if(s==NULL || s[0]=='\0')
        return 0;
    if(s[0]>='0' && s[0]<='9')
        return 0;
    i=0;
    while (s[i]!='\0')
    { 
        if(!((s[i]>='0' && s[i]<='9') || (s[i]>='A' && s[i]<='Z') || (s[i]>='a' && s[i]<='z')))
            return 0;
        i++;
    }
    if(strcmp(s,"quit")==0) 
    {    
        return 1;
    }
    return 1;
}
      
int ValidarMatriz(const char *cad)
{
    char str1[1000];
    strcpy(str1,cad);
    Trim(str1);
    printf(" cadena sin espacios = ' %s '", str1);
    return 0;
}

void EliminarMatriz(double **mat, int filas)
{
    int i;
    for(i=0 ; i<filas ; i++)
    {
        free(mat[i]);
         // Esta línea debería estar fuera del bucle
    }
    free(mat);
}

void Imprimirmatriz(double **mat, int filas, int columnas)
{
    int f, c; 
        for(f=0 ; f<filas ; f++)
           for(c=0 ;c<columnas ;c++ )
                printf("%10.2f",mat[f][c]);
        
 }
 
Tmatriz* CrearMatriz1(int filas, int columnas, int** datos) {
    Tmatriz* mat = (Tmatriz*)malloc(sizeof(Tmatriz));
    if(mat == NULL) {
        printf("Error al asignar memoria para TMatriz");
        return NULL;
    }
    mat->m = (double**)malloc(filas * sizeof(double*));
    if(mat->m == NULL) {
        printf("Error al crear la matriz");
        free(mat);
        return NULL;
    }
    for(int i = 0; i < filas; i++) {
        mat->m[i] = (double*)malloc(columnas * sizeof(double));
        if(mat->m[i] == NULL) {
            printf("Error al crear la fila %d de la matriz", i);
            for(int j = 0; j < i; j++) {
                free(mat->m[j]);
            }
            free(mat->m);
            free(mat);
            return NULL;
        }
        for(int j = 0; j < columnas; j++) {
            mat->m[i][j] = (double)datos[i][j];  // Copia los datos a la nueva matriz
        }
    }
    return mat;
}
Tmatriz* ValidarMatriz1(int filas, int columnas, int** datos) {
    Tmatriz* mat = CrearMatriz1(filas, columnas, datos);
    if(mat == NULL) {
        return NULL;
    }
    for(int f = 0; f < filas; f++) {
        for(int c = 0; c < columnas; c++) {
            mat->m[f][c] = 0.0;
        }
    }
    return mat;
}
void crear_matriz(Tmatriz** matriz) {
    char input[100];
    printf("Introduce los números separados por espacios y '()' al principio y al final: ");
    fgets(input, sizeof(input), stdin);

    char *token = strtok(input, "()");
    int count = 0, prev_count = -1;
    int filas = 0;
    int columnas = 0;
    int** datos = malloc(sizeof(int*));

    while (token != NULL) {
        while (isspace(*token)) {
            token++;
        }
        char *num = strtok(token, " ");
        while (num != NULL) {
            if (strcmp(num, "|") == 0) {
                if (prev_count != -1 && prev_count != count) {
                    printf(ROJO"Error: La matriz es invalida.\n"RESET);
                    return;
                }
                prev_count = count;
                count = 0;
                filas++;
                datos = realloc(datos, sizeof(int*) * filas);
                
            } else {
                datos[filas] = NULL;
                datos[filas] = realloc(datos[filas], sizeof(int) * (count + 1));
                datos[filas][count] = atoi(num);
                count++;
            }
            num = strtok(NULL, " ");
        }
        token = strtok(NULL, "()");
    }
    if (prev_count != -1 && prev_count != count) {
        printf(ROJO"Error: La matriz es invalida.\n"RESET);
        return;
    }
    columnas = count;

    *matriz = CrearMatriz1(filas, columnas, datos);
      for (int i = 0; i < filas; i++) {
        free(datos[i]);
    }
    free(datos);
}
TVar* buscarVariable(TVar* inicio, char* nombre) {
    TVar* temp = inicio;
    while(temp != NULL) {
        if(strcmp(temp->nomVar, nombre) == 0) {
            return temp;
        }
        temp = temp->sig;
    }
    return NULL;
}

int main() {
    TVars variables;
    variables.numVars = 0;
    variables.primera = NULL;
    Tmatriz* matriz = NULL;
    int opcion;
    char seguir[10] = "s";
    char input[100];

    do {
        printf("1. Crear matriz\n");
        printf("2. Imprimir matriz\n");
        printf("3. Validar matriz\n");
        printf("4. Asignar matriz a variable\n");
        printf("5. Buscar variable\n");
        printf("6. Salir\n");
        printf(":> ");
        scanf("%d", &opcion);
        getchar(); // Limpiar el buffer de entrada
        Tmatriz* matriz = NULL;

        switch (opcion) {
            case 1:
            if (matriz != NULL) {
                    EliminarMatriz(matriz->m, matriz->filas);
                    free(matriz);
                }
                crear_matriz(&matriz);
                break;
            case 2:
                printf("Introduce el nombre de la matriz: ");
                fgets(input, sizeof(input), stdin);
                strtok(input, "\n");
                // Código para imprimir la matriz
                break;
            case 3:
                printf("Introduce el nombre de la matriz: ");
                fgets(input, sizeof(input), stdin);
                strtok(input, "\n");
                // Código para validar la matriz
                break;
            case 4:
                printf("Introduce la asignación (formato: M1 = var): ");
                fgets(input, sizeof(input), stdin);
                char* matriz_nombre = strtok(input, "=");
                if (matriz_nombre != NULL) {
                    // Eliminar los espacios en blanco al principio y al final
                    while(isspace((unsigned char)*matriz_nombre)) matriz_nombre++;
                    char* end = matriz_nombre + strlen(matriz_nombre) - 1;
                    while(end > matriz_nombre && isspace((unsigned char)*end)) end--;
                    *(end+1) = 0;

                    char* variable_nombre = strtok(NULL, "\n");
                    if (variable_nombre != NULL) {
                        // Eliminar los espacios en blanco al principio y al final
                        while(isspace((unsigned char)*variable_nombre)) variable_nombre++;
                        end = variable_nombre + strlen(variable_nombre) - 1;
                        while(end > variable_nombre && isspace((unsigned char)*end)) end--;
                        *(end+1) = 0;

                        TVar* variable = variables.primera;
                        while (variable != NULL) {
                            if (strcmp(variable->nomVar, matriz_nombre) == 0) {
                                variable->mat = matriz;
                                printf("La matriz %s ha sido declarada correctamente para %s.\n", matriz_nombre, variable_nombre);
                                break;
                            }
                            variable = variable->sig;
                        }
                         if(matriz != NULL) {
            EliminarMatriz(matriz->m, matriz->filas);
            free(matriz);
        }
                    }
                }
                break;
            case 5:
                printf("Introduce el nombre de la variable: ");
                fgets(input, sizeof(input), stdin);
                strtok(input, "\n");
                TVar* variable = buscarVariable(variables.primera, input);
                if (variable != NULL) {
                    printf("La variable %s ha sido encontrada.\n", input);
                } else {
                    printf("La variable %s no ha sido encontrada.\n", input);
                }
                break;
     }
      printf("¿Desea continuar? (escriba 'quit' para salir): ");
        fgets(seguir, sizeof(seguir), stdin);
        strtok(seguir, "\n"); // Eliminar el carácter de nueva línea
    } while (strcmp(seguir, "quit") != 0);
  // Liberar la memoria asignada a matriz antes de salir del programa
    if (matriz != NULL) {
        EliminarMatriz(matriz->m, matriz->filas);
        free(matriz);
    }
    return 0;

    }

