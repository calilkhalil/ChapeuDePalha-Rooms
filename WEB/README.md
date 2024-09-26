# **WEB**

**Autor**: Hakal  
**Desafio**: Quebre a Hash!  
**Nível**: Fácil  

---

## **1. Introdução**

Neste write-up, abordaremos uma exploração que se aproveita da vulnerabilidade do uso de MD5 para comparar duas strings diferentes que produzem o mesmo valor de hash, conhecido como **colisão de hash**. Embora o MD5 já seja amplamente considerado inseguro, ele ainda é utilizado em algumas aplicações, o que pode ser explorado por um atacante. O objetivo desta exploração foi usar strings geradas para colidir em MD5 e obter a flag do sistema alvo.

---

## **2. Análise do Código Vulnerável**

O código vulnerável está presente em um arquivo PHP simples que utiliza a função `hash("md5", ...)` para comparar duas strings fornecidas pelo usuário. O código é o seguinte:

```php
if ($_GET['shell'] !== $_GET['pwn'] && hash("md5", $_GET['shell']) === hash("md5", $_GET['pwn'])) {
    echo("<h1>$flag</h1>");
} else {
    echo("<h1>Try harder! </h1>");
}
```

Aqui, a comparação entre os valores de `$_GET['shell']` e `$_GET['pwn']` ocorre após o cálculo do hash MD5 de ambas as strings. Isso permite que dois valores diferentes de `$_GET['shell']` e `$_GET['pwn']` tenham o mesmo hash, resultando em uma **colisão MD5** que pode ser explorada para obter a flag.

---

## **3. Exploração da Colisão de Hash**

O objetivo da exploração foi fornecer duas strings diferentes que resultassem no mesmo valor de hash MD5, cumprindo a condição necessária para exibir a flag.

### **Passo 1: Entendendo a colisão MD5**

[Marc Stevens](https://web.archive.org/web/20240319212805/https://twitter.com/realhashbreaker/status/1770161965006008570) demonstrou um exemplo de colisão onde duas strings alfanuméricas que diferem em apenas um byte produzem o mesmo hash MD5. As strings usadas para essa exploração foram:

- String 1: `TEXTCOLLBYfGiJUETHQ4hAcKSMd5zYpgqf1YRDhkmxHkhPWptrkoyz28wnI9V0aHeAuaKnak`
- String 2: `TEXTCOLLBYfGiJUETHQ4hEcKSMd5zYpgqf1YRDhkmxHkhPWptrkoyz28wnI9V0aHeAuaKnak`

Essas duas strings são diferentes, mas produzem o mesmo valor de hash MD5.

### **Passo 2: Comparação das strings**

Para identificar a diferença entre as strings, usamos um código Python que destaca as mudanças:

```python
from difflib import ndiff

def showdiff(a, b):
    for i,s in enumerate(ndiff(a, b)):
        if s[0]==' ': continue
        elif s[0]=='-':
            print(f'Delete "{s[-1]}" from position {i}')
        elif s[0]=='+':
            print(f'Add "{s[-1]}" to position {i}')

a = "TEXTCOLLBYfGiJUETHQ4hAcKSMd5zYpgqf1YRDhkmxHkhPWptrkoyz28wnI9V0aHeAuaKnak"
b = "TEXTCOLLBYfGiJUETHQ4hAcKSMd5zYpgqf1YRDhkmxHkhPWptrkoyz28wnI9V0aHeAuaKnak"

showdiff(a, b)
```

**Saída:**

![Comparação das strings](2.png)

Essa diferença de um único caractere entre as duas strings não afeta o valor de hash gerado, pois ambas produzem o mesmo hash MD5:

![MD5 das strings](1.png)

### **Passo 3: Exploração da aplicação web**

Com as strings prontas, abrimos o navegador e executamos a seguinte requisição para a aplicação vulnerável:

```
/index.php?shell=TEXTCOLLBYfGiJUETHQ4hAcKSMd5zYpgqf1YRDhkmxHkhPWptrkoyz28wnI9V0aHeAuaKnak&pwn=TEXTCOLLBYfGiJUETHQ4hEcKSMd5zYpgqf1YRDhkmxHkhPWptrkoyz28wnI9V0aHeAuaKnak
```

**Saída:**

![Comparação das strings](3.png)

Como esperado, ambas as strings geraram o mesmo hash MD5, cumprindo as condições impostas pelo código e resultando na exibição da flag.

---

## **4. Conclusão**

Este desafio envolveu o processo de:

1. Identificação da vulnerabilidade de colisão do MD5.
2. Geração de duas strings distintas que produziam o mesmo valor de hash devido à fraqueza do algoritmo MD5.
3. Envio dessas strings para a aplicação, explorando a vulnerabilidade e, assim, obtendo a flag do desafio.

Com o conhecimento sobre a suscetibilidade do MD5 a colisões, foi possível realizar a exploração com sucesso e alcançar o objetivo final.

---
