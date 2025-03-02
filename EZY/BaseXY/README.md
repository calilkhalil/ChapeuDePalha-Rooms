# **EZY**

**Autor**: Hakal  
**Desafio**: BaseXY
**Nível**: Fácil  

---

## **1. Introdução**

Este desafio envolve a decodificação de uma string criptografada em *Base32*, técnica comum para codificar dados binários em formatos ASCII. A string fornecida foi apresentada durante o evento *Hacking na Web Day 2024 - SP*, onde os participantes precisavam decodificá-la para obter uma flag e resgatar um adesivo exclusivo.

String fornecida:
`IZGECR33GBXDGX2QGEZWGM27OIZTI3DMPFPTG6BRORZSC7I=`

---

## **2. Análise da String**

A string fornecida `IZGECR33GBXDGX2QGEZWGM27OIZTI3DMPFPTG6BRORZSC7I=` apresenta características típicas:

1. **Caracteres válidos**: A-Z, 2-7 e padding `=` → **Base32**
2. **Comprimento**: 56 caracteres (múltiplo de 8 + padding)
3. **Padrão de terminação**: `=` final típico de codificações BaseXX

```bash
echo "IZGECR33GBXDGX2QGEZWGM27OIZTI3DMPFPTG6BRORZSC7I=" | hash-identifier
```
>