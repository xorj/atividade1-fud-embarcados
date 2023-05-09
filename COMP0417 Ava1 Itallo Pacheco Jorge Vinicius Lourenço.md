# AVALIAÇÃO 1
## Alunos: 
 - ITALLO PACHECO SANTANA SANTOS - 201800017370
 - JORGE VINICIUS LOURENÇO SANTOS - 201900028616


# [Questão 1](https://www.tinkercad.com/things/9PdqE16Qxyf-questao-1-/editel?sharecode=fp-O2UYS5T3VFdi6U2BaAz4pKR7NLtS2jIN-U1H64M4)
 ```cpp
#include <LiquidCrystal.h>
#include <EEPROM.h>

// define pinos para o display
#define RS  2
#define EN  3
#define DB4 4
#define DB5 5
#define DB6 6
#define DB7 7



LiquidCrystal lcd(RS, EN, DB4, DB5, DB6, DB7);

int INCREMENTA = 8; // define o pino para incrementacao
int DECREMENTA = 9; // define o pino para decrementacao

int LEITURA_INC = 0; // define uma variavel para armazenar o botao de incrementacao
int LEITURA_DEC = 0; // define uma variavel para armazenar o botao de decrementacao
int count = 0; // define uma variavel para armazenar a contagem atual
void setup()
{
  int count_ontem = EEPROM.read(0); // le o valor armazenado na pos 0
  int count_anteontem = EEPROM.read(1); // le o valor armazenado na pos 1
  EEPROM.write(1, count_ontem);
  int diferenca = abs(count_anteontem - count_ontem); // tira o modulo da diferenca
  
  // define os pinos de entrada
  pinMode(INCREMENTA, INPUT);
  pinMode(DECREMENTA, INPUT);
  lcd.begin(16, 2); // Set up the number of columns and rows on the LCD.

  // printa a diferenca inicial de dois dias consecutivos
  lcd.setCursor(0,0);
  lcd.print("Diferenca: ");
  lcd.print(diferenca);
  
  //delay de 2s
  delay(2000);
  
  lcd.clear();
  
  lcd.setCursor(0,0);
  // Printa a contagem atual.
  lcd.print("Contagem hoje: ");
  lcd.print(count);
}

void loop()
{
  //faz a leitura das entradas
  LEITURA_INC = digitalRead(INCREMENTA);
  LEITURA_DEC = digitalRead(DECREMENTA);
  
  // se a incremento foi pressionado aumenta a contagem e armazena na pos 0
  if (LEITURA_INC == HIGH) 
  {
    count ++;
    EEPROM.write(0,count);

    
    lcd.clear();
    lcd.print("Contagem: ");
    lcd.print(count);
  }
  
  // se o decremento foi pressionado diminui a contagem e armazena na pos 0
  if (LEITURA_DEC == HIGH) 
  {
    count --;
    EEPROM.write(0,count);

    lcd.clear();
    lcd.print("Contagem: ");
    lcd.print(count);
  }
  
  delay(1000);
  
}
 ```

 # [Questão 2](https://www.tinkercad.com/things/6Gv9XdQDM3a-questao-2/editel?sharecode=h4OAKcuYDXw2m5RXHHK6-m7G76hjNZob-tCi0EHqCPU)

 ```cpp
 // Inclui as bibliotecas LiquidCrystal, Keypad e EEPROM
#include <LiquidCrystal.h>
#include <Keypad.h>
#include <EEPROM.h>

// Define os endereços de memória do EEPROM para os recordes de trocas e tempo
#define RTROCAS 0
#define RTEMPO 1

// Define uma variável que armazenará o tempo em segundos
int seconds = 0;

// Cria um objeto LiquidCrystal com os pinos utilizados pelo circuito
LiquidCrystal lcd(12, 11, 5, 4, 3, 2);

// Define o tamanho da matriz utilizada pelo keypad e seus valores
const byte row = 4;
const byte col = 4;
char keys[row][col] = {
    {'1', '2', '3', 'A'},
    {'4', '5', '6', 'B'},
    {'7', '8', '9', 'C'},
    {'.', '0', '#', 'D'},
};

// Define os pinos utilizados pelas linhas e colunas do keypad
byte rowpins[4] = {9, 8, 7, 6};
byte colpins[4] = {A1, A2, A3, A4};

// Cria um objeto Keypad com a matriz e os pinos definidos acima
Keypad keypad = Keypad(makeKeymap(keys), rowpins, colpins, row, col);

// Função para imprimir os números sorteados na tela do LCD
void printarNumeros(int numbers[], int size)
{
    lcd.clear();
    lcd.setCursor(0, 0);
    lcd.print(" ");

    for (int i = 0; i < size; i++)
    {
        lcd.print(numbers[i]);
        if (i == 4)
        {
            lcd.setCursor(0, 1);
            lcd.print(" ");
        }
        else
        {
            lcd.print(" ");
        }
    }
}

// Função para verificar se um número está presente em um array
bool estaEm(int *arr, int numero)
{
    for (int i = 0; i < 10; i++)
    {
        if (arr[i] == numero)
        {
            return true;
        }
    }
    return false;
}

// Função para sortear os números do jogo
void sortearNumeros(int *numeros, int *ignore)
{
    for (int i = 0; i < 10; i++)
    {
        int j = i + random(10 - i);
        if (!estaEm(ignore, numeros[j]) && !estaEm(ignore, numeros[i]))
        {
            int aux = numeros[i];
            numeros[i] = numeros[j];
            numeros[j] = aux;
        }
    }
}

// Função para verificar se o jogo foi vencido
bool checkVitoria(int *numeros)
{
    bool isCres = true;
    bool isDesc = true;
    for (int i = 0; i < 9; i++)
    {
        if (numeros[i] < numeros[i + 1])
        {
            isCres = false;
        }
        if (numeros[i] > numeros[i + 1])
        {
            isDesc = false;
        }
    }
    return isCres || isDesc;
}

// Função responsável por exibir as informações da vitória do jogador
void printVitoria(int trocas, long int tempo)
{
    while (true)
    {
        lcd.clear();
        lcd.setCursor(0, 0);
        lcd.print("JOGO ACABOU"); // Exibe que o jogo acabou na primeira linha
        delay(2000);
        lcd.clear();
        lcd.print("TROCAS: ");
        lcd.setCursor(0, 1);
        lcd.print(trocas); // Exibe o número de trocas realizadas pelo jogador na segunda linha
        delay(2000);
        lcd.clear();
        lcd.setCursor(0, 0);
        lcd.print("TEMPO: ");
        lcd.setCursor(0, 1);
        lcd.print(tempo / 1000); // Exibe o tempo decorrido em segundos na segunda linha
        lcd.print("s");
        delay(2000);
        // Verifica se o jogador quebrou o recorde de tempo, atualiza a EEPROM e exibe a mensagem correspondente na tela
        if (trocas <= EEPROM.read(RTEMPO) || EEPROM.read(RTEMPO) == 0)
        {
            lcd.clear();
            lcd.setCursor(0, 0);
            lcd.print("Quebrou o ");
            lcd.setCursor(0, 1);
            lcd.print("recorde de tempo!");
            EEPROM.update(RTEMPO, tempo / 1000);
            delay(2000);
        }
        // Verifica se o jogador quebrou o recorde de número de trocas, atualiza a EEPROM e exibe a mensagem correspondente na tela
        if (trocas <= EEPROM.read(RTROCAS) || EEPROM.read(RTROCAS) == 0)
        {
            lcd.clear();
            lcd.setCursor(0, 0);
            lcd.print("Quebrou o ");
            lcd.setCursor(0, 1);
            lcd.print("recorde de trocas!");
            EEPROM.update(RTROCAS, trocas);
            delay(2000);
        }
    }
}
// Implementações usando DP
int longest_nondecreasing_subsequence(int arr[], int n, int *dp)
{
    int len = 0;

    for (int i = 0; i < n; i++)
    {
        int l = 0, r = len - 1, pos = -1;

        while (l <= r)
        {
            int mid = (l + r) / 2;
            if (dp[mid] <= arr[i])
            {
                pos = mid;
                l = mid + 1;
            }
            else
            {
                r = mid - 1;
            }
        }

        if (pos == -1)
        {
            dp[0] = arr[i];
            len = 1;
        }
        else if (pos == len - 1)
        {
            if (dp[pos] <= arr[i])
            {
                dp[pos + 1] = arr[i];
                len++;
            }
        }
    }
    return len;
}

int longest_nonincreasing_subsequence(int arr[], int n, int *dp)
{
    int len = 0;

    for (int i = 0; i < n; i++)
    {
        int l = 0, r = len - 1, pos = -1;

        while (l <= r)
        {
            int mid = (l + r) / 2;
            if (dp[mid] >= arr[i])
            {
                pos = mid;
                l = mid + 1;
            }
            else
            {
                r = mid - 1;
            }
        }

        if (pos == -1)
        {
            dp[0] = arr[i];
            len = 1;
        }
        else if (pos == len - 1)
        {
            if (dp[pos] >= arr[i])
            {
                dp[pos + 1] = arr[i];
                len++;
            }
        }
    }

    return len;
}

void setup()
{
    Serial.begin(9600); // Inicia a comunicação serial com uma taxa de 9600 baud
    lcd.begin(16, 2);   // Inicia o display LCD com 16 colunas e 2 linhas
}

int numeros[10] = {0, 1, 2, 3, 4, 5, 6, 7, 8, 9};          // Cria uma lista de inteiros com os números de 0 a 9
int ignore[10] = {-1, -1, -1, -1, -1, -1, -1, -1, -1, -1}; // Cria uma lista para guardar as posições dos números que não devem ser trocados
int trocas = 0;                                            // Cria uma variável para contar quantas trocas foram feitas
bool vitoria = false;                                      // Cria uma variável para guardar se o jogo foi vencido ou não
char ignoreKeys[6] = {'A', 'B', 'C', 'D', '#', '*'};       // Cria uma lista de caracteres que representam as teclas do teclado que não devem ser usadas

void loop()
{
    int rtrocas = EEPROM.read(RTROCAS); // Lê o número de trocas de uma memória EEPROM
    int rtempo = EEPROM.read(RTEMPO);   // Lê o tempo de jogo de uma memória EEPROM

    if (rtrocas == 0 && rtempo == 0) // Se não houver recorde, exibe "SEM RECORDES" no display
    {
        lcd.print("SEM RECORDES");
    }
    else // Caso contrário, exibe o número de trocas e o tempo de jogo no display
    {
        lcd.print("R TROCAS: ");
        lcd.print(rtrocas);
        lcd.setCursor(0, 1);
        lcd.print("R TEMPO: ");
        lcd.print(rtempo);
        lcd.print("s");
    }

    delay(2000); // Espera 2 segundos antes de continuar

    long int t1 = millis();          // Obtém o tempo atual em milissegundos
    sortearNumeros(numeros, ignore); // Embaralha os números na lista, ignorando as posições na lista "ignore"
    while (!vitoria)                 // Enquanto o jogo não for vencido
    {
        vitoria = checkVitoria(numeros); // Verifica se a lista está na ordem correta
        if (vitoria)                     // Se a lista está na ordem correta, sai do loop
        {
            return;
        }
        sortearNumeros(numeros, ignore); // Embaralha os números na lista novamente
        for (int i = 0; i < 10; i++)     // Reinicia a lista "ignore"
        {
            ignore[i] = -1;
        }
        printarNumeros(numeros, 10); // Exibe a lista de números no display
        char input = '-';            // Cria uma variável para guardar o primeiro número a ser trocado
        while (input == '-')         // Aguarda a entrada do primeiro número a ser trocado
        {
            char customKey = keypad.getKey();
            if (customKey)
            {
                for (int i = 0; i < 6; i++)
                {
                    if (ignoreKeys[i] == customKey)
                    {
                        customKey = '-';
                        break;
                    }
                }
                input = customKey;
            }
        }
        char input1 = '-';
        while (input1 == '-')
        {
            char customKey = keypad.getKey();
            if (customKey)
            {
                for (int i = 0; i < 6; i++)
                {
                    if (ignoreKeys[i] == customKey)
                    {
                        customKey = '-';
                        break;
                    }
                }
                input1 = customKey;
            }
        }

        lcd.clear();
        lcd.print("TROCANDO");
        delay(500);
        // Obtém as posições dos números que serão trocados
        int pos1 = input - '0';
        int pos2 = input1 - '0';

        // Troca os números de posição na lista
        int aux = numeros[pos1];
        numeros[pos1] = numeros[pos2];
        numeros[pos2] = aux;
        trocas++;

        printarNumeros(numeros, 10); // Exibe a lista de números atualizada no display
        delay(1500);
        lcd.clear();
        vitoria = checkVitoria(numeros);
        if (vitoria)
        {
            return;
        }
        //Alocando memoria para os vetores das sequências
        int *dp = (int *)malloc(10 * sizeof(int));
        int *dp1 = (int *)malloc(10 * sizeof(int));

        //Chamando as funções para encontrar as sequências
        int ordemCrescente = longest_nondecreasing_subsequence(numeros, 10, dp);
        int ordemDecrescente = longest_nonincreasing_subsequence(numeros, 10, dp1);
        lcd.clear();
        lcd.print("Sequencia");
        delay(1000);
        //Verificando qual sequência é maior, crescente ou decrescente
        //E mostra a maior no display
        if (ordemCrescente >= ordemDecrescente)
        {
            for (int i = 0; i < ordemCrescente; i++)
            {
                ignore[i] = dp[i];
            }
            printarNumeros(ignore, ordemCrescente);
        }
        else
        {
            for (int i = 0; i < ordemDecrescente; i++)
            {
                ignore[i] = dp1[i];
            }
            printarNumeros(ignore, ordemDecrescente);
        }
        delay(1000);
        free(dp);
        free(dp1);
    }
    //Exibindo a mensagem de vitória
    printVitoria(trocas, t1);
}

 ```