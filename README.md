# CTF-Elytron-write-up

Write-up do CTF realizado pela [Elytron Security](https://www.linkedin.com/company/elytron-security/) em parceria com a Universidade Mackenzie.

1. Encoding and Cryptography
    1. [Unbase the secrets 01](#unbase-the-secrets-01)
    1. [Unbase the secrets 02](#unbase-the-secrets-02) 
    1. [Files as stream of data](#unbase-the-secrets-03) 
1. Forensics
    1. [Find me between bytes](#find-me-between-bytes)
1. Traffic Analysis
    1. [Investigação de Tráfego Suspeito 01](#investigação-de-tráfego-suspeito-01)
    1. [Investigação de Tráfego Suspeito 02](#investigação-de-tráfego-suspeito-01)
1. Web
    1. [Web the flags 01](#web-the-flags-01)
    1. [Web the flags 02](#web-the-flags-02)
    1. [Web the flags 03](#web-the-flags-03)
    1. [Web the flags 04](#web-the-flags-04)
    1. [Web the flags 05](#web-the-flags-05)
    1. [Web the flags 06](#web-the-flags-06)
    
    
## Encoding and Cryptography

### Unbase the secrets 01 

**Pontuação**
100

**Enunciado**

Para resolver este desafio, é necessário conseguir reconhecer padrões no texto. Extraia a flag a partir do texto abaixo: 

```VFVGRFMzdE5lVjltTVhKek4xOHpibU13WkRGdVoxOW1iRFJuZlE9PQo=```

**Resolução**

De cara se nota que trata-se de um base64. Mas, para ter certeza, pode-se confirmar utilizando algum [identificador de hashes](https://www.dcode.fr/cipher-identifier).

``` TUFDS3tNeV9mMXJzN18zbmMwZDFuZ19mbDRnfQ== ```

Ao decodificar o texto, obtemos um novo texto codificado (também em base64). Para solucionar o problema, deve-se decodificar o novo texto gerado. O resultado será a flag.

``` MACK{My_f1rs7_3nc0d1ng_fl4g} ```

-----

### Unbase the secrets 02 

**Pontuação**
150

**Enunciado**

Agora que você já consegue reconhecer padrões de base64, é hora de partir para algo não tão utilizado assim. Decifre o texto abaixo para garantir a flag:

``` JJLECVKHKMZTGSKJGJCEWTJSG5DU2WSGGYZEYVCMGVKEQSZTKM3UOUKZKRBVQM2EI5JFSRCDJZNF KTSRGJMDEPJ5HUFA==== ```

**Resolução**

Ao colocar o texto codificado em um [identificador de hashes](https://www.dcode.fr/cipher-identifier), notamos que trata-se de base32. Essa flag funciona de maneira semelhante à anterior. Ao decodificarmos a primeira base32, obtemos outro texto codificado. Devemos novamente decodificar esse texto utilizando base32 para obtermos a flag.

``` MACK{B453_32_is_fun_411_c4p174l5} ```

---- 

### Files as stream of data 

**Pontuação**
200

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

**Pontuação**
200

**Enunciado**

Este desafio requer cavar um pouco mais fundo do que apenas "strings" para encontrar a flag correta. Analise o binário anexo e pontue com a flag oculta.

<only_a_binary>


**Resolução**

Esse desafio inclui um arquivo executável no formato *elf* que deve ser baixado. O arquivo imprime um flag decoy ao ser executado.

Podemos analisar a possibilidade de bufferoverflow, apesar de nada indicar que o arquivo recebe qualquer entrada de dados. Ao procurarmos por symbols, torna-se claro que o programa não recebe nenhum input:

``` nm -D only_a_binary ```

 Ao utilizarmos o *strings* no programa não é possível encontrar nenhuma outra string que indique a flag. Entretanto, encontra-se o termo JFIF, o que nos indica um possível header de um arquivo de imagem. Além disso, outras sequências de números e letras são caraterísticas de arquivos de imagem.
 
Devemos então extrair o arquivo .jfif contido dentro do binário. Para isso podemos editar os bytes do arquivo, mantendo apenas o que está entre a sequência FF D8 e a sequência FF D9. Abrimos o arquivo resultante como um jfif e obtemos a flag.

Entretanto, o jeito mais fácil de obter a flag é utilizar o [foremost](https://linux.die.net/man/1/foremost). 

``` foremost only_a_binary -t all ```

Como saída obtemos um arquivo com a extensão .jfif. A flag está justamente impressa na imagem.

``` MACK{Foremost_is_the_easiest_way_to_find_me} ```

-----

## Traffic Analysis

### Investigação de Tráfego Suspeito 01

**Pontuação**
300

**Enunciado**

Uma denuncia anônima foi feita pelo canal de comunicação interno de uma empresa alegando que um dos funcionários estaria extraindo informações sigilosas para fora da empresa. Foi dito que ele estaria vazando informações diversas sobre a infraestrutura da rede interna e que estaria fazendo isso de forma furtiva pela rede. Uma captura de tráfego foi realizada durante um determinado tempo para tentar descobrir se a informação era verídica.

Qual foi o protocolo utilizado para exfiltrar as informações?

A flag deve estar no formado MACK{nomesemespacoeminusculo}

<captura.pcap>


**Resolução**

O enunciado inclui um arquivo .pcap. Ao abrirmos o arquivo no Wireshark encontramos mais de 12000 linhas. É possível filtrar os protocolos existentes no arquivo. Como o desafio solicita apenas o nome do protocolo, o jeito menos trabalhoso é tentar diferentes protocolos na flag. Outra maneira é analizar o conteúdo dos pacotes enviados e recebidos e notar que existem bytes extras nos pacotes ICMP.

``` MACK{icmp} ```

### Investigação de Tráfego Suspeito 02

**Pontuação**
500

**Enunciado**

Uma denuncia anônima foi feita pelo canal de comunicação interno de uma empresa alegando que um dos funcionários estaria extraindo informações sigilosas para fora da empresa. Foi dito que ele estaria vazando informações diversas sobre a infraestrutura da rede interna e que estaria fazendo isso de forma furtiva pela rede. Uma captura de tráfego foi realizada durante um determinado tempo para tentar descobrir se a informação era verídica.

Encontre a informação exfiltrada e responda às informações abaixo:

1 - Qual o nome do arquivo confidencial mencionado na mensagem de exfiltração? 2 - A que horas ocorrerá a reunião do diretor mencionada na mensagem? 3 - Com qual empresa será fechado um contrato de 7 milhões de dólares? 4 - Qual a data marcada para o fechamento do contrato?

Formatos das flags: MACK{99:99} MACK{dd/mm/aaaa} MACK{Arquivo.ext}

**Resolução**

Como já sabemos que o protocolo alvo é o ICMP, devemos analisar o conteúdo dos pacotes ICMP recebidos e enviados. É possível notar alguns bytes adicionais no final dos pacotes. Ao convertermos esses bytes para texto claro, percebemos que trata-se de uma espécie de conversa.

Após muito trabalho traduzindo a conversa, obtemos as informações necessárias para resolver o desafio:

**obs.: apenas uma das flags basta para resolver o desafio**

``` MACK{15h30} ```
``` MACK{13/05/2017} ```
``` MACK{ReuniaoContratoMaio.pdf} ```

-----

## Web

Todas os desafios possuem a URL https://mackenzie-web-challenges.chals.io 

### Web the flags 01

**Pontuação**
100

**Enunciado**

Este é o primeiro desafio de web. Ao acessar o serviço exposto, procure pela flag nos locais mais fáceis.

**Resolução**

Ao inspecionarmos o código-fonte do html, encontramos a flag como um comentário.

``` MACK{First_flag_in_the_html_source_code} ```

----- 


### Web the flags 02

**Pontuação**
100

**Enunciado**

A segunda flag desta aplicação web super insegura precisa ser encontrada tentando um pouco mais do que o que se pode contar...

**Resolução**

A página inicia possui uma sequência de posts. Nota-se que cada post encontra-se em um arquivo .txt que possui um ID numérico:

/posts.php?id=1.txt, /posts.php?id=2.txt, /posts.php?id=3.txt, /posts.php?id=4.txt, /posts.php?id=5.txt

Como sugerido pelo enunciado, devemos fazer o fuzzing dos IDs até encontrarmos o arquivo 9.txt, que possui a flag.

``` MACK{Second_flag_Always_look_a_little_bit_further}  ``` 

----- 


### Web the flags 03

**Pontuação**
300

**Enunciado**

Agora que você conseguiu enumerar algumas informações a mais na aplicação super vulnerável, é hora de ir um pouco mais além...

O que será que tem no source code PHP da aplicação?

Encontre a próxima flag lendo o conteúdo do arquivo index.php.


**Resolução**

Apesar de ser a terceira flag, é mais fácil resolver os outros desafios antes desse. Como a aplicação aceita inputs de novos arquivos (desafio 6), devemos inserir um payload php que nos permita acessar o conteúdo do index.php.

Ao inserirmos o arquivo ../payload.php com o conteúdo


```
<?php$homepage = file_get_contents(index.php');echo $homepage;?>
```

Ao acessarmos o arquivo inserido em ../payload.php, temos impresso no html o conteúdo do index.php. A flag encontra-se nos comentários e para encontrá-la devemos inspecionar o html da página.

``` MACK{Third_flag_PhP_source_code} ```

----- 


### Web the flags 04

**Pontuação**
150

**Enunciado**

Você é um robô???

**Resolução**

Como a dica sugere, devemos acessar o arquivo robots.txt. Além da flag, o arquivo nos indica o caminho /admin que deverá ser utilizado nos próximos desafios.

``` MACK{Fourth_flag_I_am_not_a_robot} ```

----- 


### Web the flags 05

**Pontuação**
200

**Enunciado**

Agora você precisa encontrar um arquivo oculto no backend. Seu nome não é previsível. Para contra-lo, você precisa conseguir ver o que está no diretório acima.

**Resolução**

Devemos inserir um arquivo com o nome ../payload.php e um corpo com o nosso payload.

```
<?php

$fileList = glob('.');

foreach($fileList as $filename){
   echo $filename, '<br>'; 
}
```

Ao acessarmos o arquivo /posts.php?id=../payload.php, teremos impressos todos os arquivos. Encontramos um arquivo .txt na pasta. Ao abrirmos, encontramos a flag:

``` MACK{Fith_flag_G0t_RCE} ```

----- 


### Web the flags 06

**Pontuação**
200

**Enunciado**

Você consegue mostrar a flag na tela ao acessar adicionar_post.php? Analise os dados da requisição e veja o que consegue.

**Resolução**

Ao acessarmos /admin/adicionar_post.php, somos avisados por um alert que devemos incluir na requisição os parâmetros nome e corpo. Ao realizarmos testes, vemos que o arquivo que inserirmos aparecerá na página inicial.

Ao adicionarmos um arquivo, somos redirecionados para uma página que possui a flag impressa.


**obs.: não consegui replicar essa solução novamente, por algum motivo os parametros não eram aceitos.**
