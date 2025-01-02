# **WEB**

**Autor**: Hakal  
**Desafio**: Weird Panel  
**Nível**: Fácil  

---

## **1. Introdução**

Este write-up aborda uma exploração que utiliza a falta de serialização de parâmetros em sistemas de banco de dados, também conhecida como **SQL Injection**. Embora essa vulnerabilidade seja amplamente conhecida, ela ainda pode ser explorada de diferentes formas — error-based, time-based e blind-based. O objetivo deste estudo é demonstrar como a falha pode ser aproveitada para obter a flag do sistema-alvo.

---

## **2. Identificando a Vulnerabilidade**

Ao acessar o site, encontramos um painel de consulta de nomes e um campo para submissão de queries. 

![Painel](1.png)

A injeção de SQL acontece quando dados de entrada não são devidamente validados, permitindo a manipulação direta das consultas no banco de dados. Um simples teste seria a utilização de `'` e entender como a aplicação reage.

![Error-based](2.png)

Conforme a imagem acima, ocorre o erro `Fatal error: Uncaught mysqli_sql_exception:` ao enviar `'`, indicando que a entrada não é tratada de forma adequada.  
Essa reação expõe detalhes sobre a estrutura do banco e confirma a fragilidade na validação de parâmetrosisso ocorre porque o uso do apóstrofo `'` no SQL está principalmente relacionado à delimitação de strings, ou seja, ele é utilizado para envolver valores de texto em consultas SQL.Portanto podemos utilizar ele pois é possível interromper a consulta SQL.