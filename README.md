# CTF-Elytron-write-up

1. Encoding and Cryptography
    1. Unbase the secrets #01 
    1. Unbase the secrets #02 
    1. Files as stream of data 
1. Forensics
    1. Find me between bytes
1. Traffic Analysis
    1. Investigação de Tráfego Suspeito #01
    1. Investigação de Tráfego Suspeito #02
1. Web
    1. Web the flags #01
    1. Web the flags #02
    1. Web the flags #03
    1. Web the flags #04
    1. Web the flags #5
    1. Web the flags #6
    
    
## Encoding and Cryptography

### Unbase the secrets #01 

**Enunciado**

Para resolver este desafio, é necessário conseguir reconhecer padrões no texto. Extraia a flag a partir do texto abaixo: 

```VFVGRFMzdE5lVjltTVhKek4xOHpibU13WkRGdVoxOW1iRFJuZlE9PQo=```

**Resolução**

De cara se nota que trata-se de um base64. Mas, para ter certeza, pode-se confirmar utilizando algum [identificador de hashes](https://www.dcode.fr/cipher-identifier).

``` TUFDS3tNeV9mMXJzN18zbmMwZDFuZ19mbDRnfQ== ```

Ao decodificar o texto, obtemos um novo texto codificado (também em base64). Para solucionar o problema, deve-se decodificar o novo texto gerado. O resultado será a flag.

``` MACK{My_f1rs7_3nc0d1ng_fl4g} ```

-----

### Unbase the secrets #02 

**Enunciado**

Agora que você já consegue reconhecer padrões de base64, é hora de partir para algo não tão utilizado assim. Decifre o texto abaixo para garantir a flag:

``` JJLECVKHKMZTGSKJGJCEWTJSG5DU2WSGGYZEYVCMGVKEQSZTKM3UOUKZKRBVQM2EI5JFSRCDJZNF KTSRGJMDEPJ5HUFA==== ```

**Resolução**

Ao colocar o texto codificado em um [identificador de hashes](https://www.dcode.fr/cipher-identifier), notamos que trata-se de base32. Essa flag funciona de maneira semelhante à anterior. Ao decodificarmos a primeira base32, obtemos outro texto codificado. Devemos novamente decodificar esse texto utilizando base32 para obtermos a flag.

``` MACK{B453_32_is_fun_411_c4p174l5} ```

---- 

### Files as stream of data 

**Enunciado**

Arquivos são nada mais que dados agregados em uma forma específica. É possível transformar arquivos em texto de diversas formas. Tente recuperar o arquivo executável que foi convertido para uma sequência de códigos hexadecimais, armazenados no arquivo anexo.

<dump.hex>

**Resolução**

O enunciado inclui um arquivo com a extensão .hex que deve ser baixado. Ao abrir o arquivo em um editor de texto é possível perceber que trata-se de uma sequência de bytes em hexadecimal. Como o enunciado diz que esse arquivo foi "convertido para uma sequência de códigos hexadecimais", o mais óbvio é tentar "desconverter" esses valores. 

Para isso, devemos utilizar um [conversor](https://codebeautify.org/hex-string-converter) para obtermos o texto limpo. Ao convertermos o arquivo .hex obtemos o que parece ser um arquivo *.elf*. Podemos tentar executar esse arquivo binário ou simplesmente buscarmos pela flag dentro das strings do arquivo. Isso pode ser feito utilizando-se o programa strings ou simplesmente abrindo o arquivo em um editor de texto e procurar pela flag.

``` MACK{Hello, World!} ```

-----

## Forensics

### Find me between bytes

**Enunciado**

Este desafio requer cavar um pouco mais fundo do que apenas "strings" para encontrar a flag correta. Analise o binário anexo e pontue com a flag oculta.

<only_a_binary>


**Resolução**

Esse desafio inclui um arquivo executável no formato *elf* que deve ser baixado. O arquivo imprime um flag decoy ao ser executado, isto é, uma flag incorreta.

Podemos analisar a possibilidade de bufferoverflow, apesar de nada indicar que o arquivo recebe qualquer entrada de dados. Ao procurarmos por symbols, torna-se claro que o programa não recebe nenhum input:

``` nm -D only_a_binary ```

 Ao utilizarmos o *strings* no programa não é possível encontrar nenhuma outra string que indique a flag. Entretanto, encontra-se o termo JFIF, o que nos indica um possível header de um arquivo de imagem. Além disso, outras sequências de números e letras são caraterísticas de arquivos de imagem.
 
Devemos então extrair o arquivo .jfif contido dentro do binário. Para isso podemos editar os bytes do arquivo, mantendo apenas o que está entre a sequência FF D8 e a sequência FF D9. Abrimos o arquivo resultante como um jfif e obtemos a flag.

Entretanto, o jeito mais fácil de obter a flag é utilizar o [foremost](https://linux.die.net/man/1/foremost). 

``` foremost only_a_binary -t all ```

Como saída obtemos um arquivo com a extensão .jfif. A flag está justamente impressa na imagem.

``` MACK{Foremost_is_the_easiest_way_to_find_me} ```

### 

**Enunciado**

**Resolução**

### 

**Enunciado**

**Resolução**

### 

**Enunciado**

**Resolução**
