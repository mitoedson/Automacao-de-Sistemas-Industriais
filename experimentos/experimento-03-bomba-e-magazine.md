# Experimento 3 — Problemas práticos reais: controle de bomba e alimentador de peças


## 1. Objetivos

- Transformar problemas práticos reais em soluções de automação baseadas em CLP.
- Consolidar as instruções do tipo relé (contatos, bobinas, `SET`/`RESET`).
- Utilizar **temporizadores** (`TON`) e **contadores** (`CTU`).
- Tratar alarmes, intertravamentos e contatos NA × NF.

## 2. Pré-requisitos

Experimentos 1 e 2. Instruções `TON` e `CTU` (a apostila descreve o `TON` com os parâmetros `IN`, `PT`, `Q` e `ET`).

---

## 3. Problema 1 — Bomba com modo manual e automático

Desenvolver um projeto de controle na linguagem LADDER para a seguinte instalação: 

![alt text](image.png)

Controlar a bomba de um reservatório em dois modos, selecionados por uma chave. Através do programa o utilizador deve ser capaz de selecionar o modo se funcionamento: Automático ou Manual. 

* Em MANUAL, a Bomba poderá ser ligada pressionando-se o botão LIGA e desliga pressionando-se o botão desliga. Neste modo, as bóias de Nível não tem nenhuma ação.

* Em AUTOMÁTICO, a bomba será ligada 10 Seg. após a detecção de NÍVEL BAIXO e desligada 10 Seg. após a detecção de NÍVEL ALTO . 


![alt text](image-25.png)


## 4. Problema 2 — Alimentador de peças com magazine

 Um sistema  para alimentação de peças a partir de um magazine (deposito vertical) deve funcionar da seguinte forma: 

![alt text](image-1.png)

### 4.1 Especificação

a) O cilindro (S1) encontra-se inicialmente recuado e o sensor de peça no magazine (E3) indica a presença de peça. 

b) Uma vez que as condições iniciais (item a) são verificadas, e o botão de início de ciclo (E1) é pressionado, o cilindro (S1) avança, empurrando a peça, até que o sensor de final de curso do cilindro (E2) seja acionado, iniciando o recuo do cilindro S1. Uma vez recuado o cilindro (S1) o sistema volta a condição inicial. 

c) Caso seja pressionado o botão de início de ciclo (E1) e não haja peça no magazine, um alarme sonoro (S2) deve ser ativado em quanto o botão de início de ciclo (E1) for pressionado. 

d) Caso, a partir do acionamento do botão E1 (considerando que exista peça), o cilindro leve mais que 10 segundos para alcançar o fim de curso, um alarme (S3) deve ser acionado. 

e) Cada vez que o cilindro alcança o fim de curso, um contador deve ser incrementado. Após 10 peças, um alarme deve ser acionado para que o operador retire o cesto de peças e zere o contador. Enquanto o contador não for zerado, o ciclo da máquina deve ser bloqueado. 

![alt text](image-24.png)

Pede-se:

a) O programa em linguagem ladder que implementa a lógica descrita.

b) Uma tabela com todos os endereços utilizados com suas descrições (inclusive TMR e CNT).

c) Devido a um problema técnico, o sensor E4 foi substituído por um do tipo NF(Normal fechado). Refaça o item a utilizando o novo E4.


# Desenvolvimento dos experimentos

O trabalho tem dois exercícios independentes, cada um com seu programa. Os dois reaproveitam os mesmos endereços de E/S porque são montados em bancadas ou projetos separados.


## 1. Descrição do processo

### 1.1. Experimento 3.1 — Controle de bomba com modo manual e automático

#### 1.1.1 Objetivos
Transformar um problema prático real em uma solução de automação baseada em CLP.
Consolidar as instruções do tipo relé (contatos, bobinas, SET/RESET).
Utilizar temporizadores (TON) e detecção de troca de modo.
Tratar intertravamentos, emergência e contatos NA × NF.

#### 1.1.2. Pré-requisitos

Experimentos 1 e 2. Instrução TON (parâmetros IN, PT, Q e ET).


#### 1.1.3. Problema — Bomba com modo manual e automático

Controlar a bomba de um reservatório em dois modos, selecionados por uma chave (AUTO). O reservatório tem duas boias: nível baixo e nível alto.

Manual: a bomba liga com o botão LIGA e desliga com o botão DESLIGA. A boia de nível alto, quando vai a 1, desliga a bomba e bloqueia o LIGA, para evitar transbordamento por esquecimento do operador. A boia de nível baixo não tem ação.
Automático: a bomba liga 10 s após a detecção de NÍVEL BAIXO e desliga 10 s após a detecção de NÍVEL ALTO.
Desenvolvimento do experimento


#### 1.1.4. Descrição do processo

Uma bomba enche um tanque com duas boias de nível (baixo e alto), uma chave automático/manual, os botões LIGA e DESLIGA e um botão de emergência.

Situação	Comportamento
Manual, LIGA	Liga a bomba, se NIVEL_ALTO = 0
Manual, DESLIGA	Desliga a bomba
Manual, NIVEL_ALTO = 1	Desliga a bomba na hora e bloqueia o LIGA enquanto o nível estiver alto
Manual, NIVEL_BAIXO	Sem ação
Automático, NIVEL_BAIXO = 1 por 10 s	Liga a bomba
Automático, NIVEL_ALTO = 1 por 10 s	Desliga a bomba
Automático, condição some antes de 10 s	A contagem recomeça
Troca de modo, qualquer sentido	A bomba para na hora
Troca para automático (inclusive a primeira vez)	Abre uma contagem de 10 s e a bomba começa a encher
Troca para manual	A bomba fica parada até apertar LIGA
EME em manual	Desliga a bomba
EME em automático	Desliga a bomba. Ao soltar, refaz a contagem de 10 s e então parte


1. Hipóteses adotadas

    DESLIGA e EME são normalmente fechados (1 = normal, 0 = pressionado).
Em automático, o botão DESLIGA não tem ação. Para parar, use a chave para manual ou a EME.
Se o nível já estiver alto quando a contagem de partida terminar, o desligamento por nível alto (também de 10 s) cancela a partida.
AUTO_ME deve ser não retentivo. Assim, se o CLP energizar com a chave em automático, a partida abre a contagem de 10 s.
O botão de emergência desliga a bomba nos dois modos.

2. Entradas
<pre>
Endereço	TAG	           Descrição
%I0.0	    NIVEL_BAIXO	   1 = nível na boia baixa ou abaixo
%I0.1	    NIVEL_ALTO	   1 = nível na boia alta ou acima
%I0.2	    AUTO	   1 = automático, 0 = manual
%I0.3	    LIGA           Botão liga
%I0.4	    DESLIGA        Botão desliga (NF)
%I0.5	    EME	           Botão de emergência (NF)
</pre>

3. Saídas
<pre>
Endereço     TAG     Descrição
%Q0.1        BOMBA   Comando da bomba (contator KM1)
</pre>

4. Outros recursos

<pre>
Recurso	        TAG	    Descrição
%M10.0	        AUT_ON	    Estado da bomba no modo automático (memorizado)
%M10.1	        RETOMA	    Aguardando a contagem de partida (troca para automático ou EME)
%M10.2	        AUTO_ME	    Memória do modo anterior (não retentiva)
%M10.3	        TROCA_MODO  Pulso de uma varredura na troca de modo
DB "T_LIGA"	T_LIGA	    TON, PT = T#10s (liga, nível baixo)
DB "T_DESL"	T_DESL	    TON, PT = T#10s (desliga, nível alto)
DB "T_RETOMA"	T_RETOMA    TON, PT = T#10s (partida após troca para automático ou EME)
</pre>

5. Configuração no TIA Portal
Crie o projeto, adicione a CPU S7-1500 e os módulos de entrada e saída digitais.
Cadastre as tags das seções 2, 3 e 4 na tabela de tags. Os bits de memória usam MB10 (MB0 e MB1 ficam reservados se a memória de sistema estiver habilitada).
Desmarque a retentividade de AUTO_ME.
Insira os blocos TON com DB de instância e presets T#10s. Cada bloco deve ser chamado a cada varredura, em uma network própria.
O endereço de bit no TIA vai de 0 a 7 (byte.bit).
Compile, carregue na CPU ou no PLCSIM e coloque em RUN.
Para o diagrama de ligação elétrica, veja o manual em PDF.

6. Código ladder

Legenda: ] [ contato NA, ]/[ contato NF, ( ) bobina, ( S ) set, ( R ) reset. Monte cada bloco em uma network, na ordem apresentada.

<pre>
NETWORK 1 – Pulso de troca de modo (AUTO diferente do modo anterior)
   "AUTO"   "AUTO_ME"                "TROCA_MODO"
 |--] [-------]/[--------+-----------( )
   "AUTO"   "AUTO_ME"    |
 |--]/[-------] [--------+

NETWORK 2 – Memória do modo anterior
   "AUTO"                            "AUTO_ME"
 |--] [-----------------------------( )

NETWORK 3 – Retardo de 10 s para ligar (automático, nível baixo, EME solta)
   "AUTO"  "NIVEL_BAIXO"  "EME"
 |--] [------] [-----------] [------[ "T_LIGA" TON  IN  Q ]
                                       PT := T#10s

NETWORK 4 – Retardo de 10 s para desligar (automático, nível alto)
   "AUTO"  "NIVEL_ALTO"
 |--] [------] [-------------------[ "T_DESL" TON  IN  Q ]
                                       PT := T#10s

NETWORK 5 – Contagem de partida (troca para automático ou EME solta)
   "AUTO"  "RETOMA"  "EME"
 |--] [------] [-------] [----------[ "T_RETOMA" TON  IN  Q ]
                                       PT := T#10s

NETWORK 6 – Em manual, AUT_ON acompanha a bomba (liga)
   "AUTO"    "BOMBA"                 "AUT_ON"
 |--]/[--------] [------------------( S )

NETWORK 7 – Em manual, AUT_ON acompanha a bomba (desliga)
   "AUTO"    "BOMBA"                 "AUT_ON"
 |--]/[--------]/[-----------------( R )

NETWORK 8 – Nível baixo por 10 s liga a bomba em automático
   "T_LIGA".Q                        "AUT_ON"
 |-----] [--------------------------( S )

NETWORK 9 – Fim da contagem de partida
   "T_RETOMA".Q                      "AUT_ON"
 |-----] [---------------+-----------( S )
                          |          "RETOMA"
                          +-----------( R )

NETWORK 10 – Nível alto por 10 s desliga a bomba em automático
   "T_DESL".Q                        "AUT_ON"
 |-----] [--------------------------( R )

NETWORK 11 – EME em automático com a bomba ligada: arma a contagem de partida
   "AUTO"    "EME"     "AUT_ON"      "RETOMA"
 |--] [-------]/[-------] [----------( S )

NETWORK 12 – EME em automático: para a bomba
   "AUTO"    "EME"                   "AUT_ON"
 |--] [-------]/[-------------------( R )

NETWORK 13 – Troca para automático: arma a contagem de partida
   "TROCA_MODO"  "AUTO"              "RETOMA"
 |-----] [---------] [---------------( S )

NETWORK 14 – Troca para manual: cancela a contagem de partida
   "TROCA_MODO"  "AUTO"              "RETOMA"
 |-----] [---------]/[--------------( R )

NETWORK 15 – Troca de modo: para a bomba
   "TROCA_MODO"                      "AUT_ON"
 |-----] [--------------------------( R )

NETWORK 16 – Comando da bomba
   "TROCA_MODO"  "AUTO"  "AUT_ON"
 |-----]/[-------+--] [----] [-------------------------+
 |                |                                     |
 |                |  "AUTO" "LIGA"    "DESLIGA" "NIVEL_ALTO"      "EME"
 |                +--]/[---+--] [--+----] [--------]/[--+-----] [---( ) "BOMBA"
 |                         | "BOMBA"|
 |                         +--] [---+
</pre>

7. Programação estruturada (SCL)

O mesmo programa como bloco de função FB_Bomba (SCL). Os temporizadores são multi-instância (TON_TIME).

Interface do bloco

<pre>
Seção	Nome	        Tipo	Descrição
Input	NivelBaixo	Bool	Boia baixa
Input	NivelAlto	Bool	Boia alta
Input	Auto	        Bool	1 = automático
Input	Liga	        Bool	Botão liga
Input	Desliga	        Bool	Botão desliga (NF)
Input	Eme	        Bool	Emergência (NF)
Output	Bomba	        Bool	Comando da bomba
Static	AutOn, Retoma,
        AutoMe, TrocaModo	Bool	Memórias (AutoMe não retentiva)
Static	tLiga, tDesl,
        tRetoma         TON_TIME Temporizadores de 10 s
pascal
FUNCTION_BLOCK "FB_Bomba"
VAR_INPUT
    NivelBaixo : Bool;
    NivelAlto  : Bool;
    Auto       : Bool;
    Liga       : Bool;
    Desliga    : Bool;   // NF: 1 = solto
    Eme        : Bool;   // NF: 1 = solta
END_VAR
VAR_OUTPUT
    Bomba : Bool;
END_VAR
VAR
    AutOn      : Bool;
    Retoma     : Bool;
    AutoMe     : Bool;   // nao retentiva
    TrocaModo  : Bool;
    tLiga      : TON_TIME;
    tDesl      : TON_TIME;
    tRetoma    : TON_TIME;
END_VAR

BEGIN
    // Pulso de troca de modo e memoria do modo anterior
    #TrocaModo := #Auto XOR #AutoMe;
    #AutoMe := #Auto;

    // Temporizadores de 10 s
    #tLiga(IN := #Auto AND #NivelBaixo AND #Eme, PT := T#10s);
    #tDesl(IN := #Auto AND #NivelAlto, PT := T#10s);
    #tRetoma(IN := #Auto AND #Retoma AND #Eme, PT := T#10s);

    // Em manual, AUT_ON acompanha a bomba
    IF NOT #Auto THEN
        #AutOn := #Bomba;
    END_IF;

    // Eventos do automatico
    IF #tLiga.Q THEN
        #AutOn := TRUE;
    END_IF;
    IF #tRetoma.Q THEN
        #AutOn := TRUE;
        #Retoma := FALSE;
    END_IF;
    IF #tDesl.Q THEN
        #AutOn := FALSE;
    END_IF;

    // EME em automatico: para; se estava ligada, arma a contagem de partida
    IF #Auto AND NOT #Eme THEN
        IF #AutOn THEN
            #Retoma := TRUE;
        END_IF;
        #AutOn := FALSE;
    END_IF;

    // Troca de modo: para; indo para automatico, arma a contagem de partida
    IF #TrocaModo THEN
        #Retoma := #Auto;
        #AutOn := FALSE;
    END_IF;

    // Comando da bomba
    #Bomba := NOT #TrocaModo
              AND ((#Auto AND #AutOn)
                   OR (NOT #Auto AND (#Liga OR #Bomba) AND #Desliga AND NOT #NivelAlto))
              AND #Eme;
END_FUNCTION_BLOCK
</pre>

Chamada no OB1:

<pre>
pascal
"FB_Bomba_DB"(NivelBaixo := "NIVEL_BAIXO",
              NivelAlto  := "NIVEL_ALTO",
              Auto       := "AUTO",
              Liga       := "LIGA",
              Desliga    := "DESLIGA",
              Eme        := "EME",
              Bomba      => "BOMBA");
</pre>

8. Observações e teste

A contagem dos temporizadores é zerada quando a condição termina. Uma oscilação de nível com menos de 10 s não liga nem desliga a bomba.
Em automático, o nível ainda varia durante os 10 s de atraso, então a bomba pode ligar abaixo da boia baixa e desligar acima da boia alta.

Os botões de emergência reais devem ser cabeados também em hardware (relé de segurança). O ladder não substitui a proteção exigida pela NR-12 e pela ISO 13850.
Teste no PLCSIM:

Automático: acione AUTO. Depois de 10 s a bomba liga. NIVEL_ALTO em 1 por 10 s desliga.

Manual: LIGA liga e DESLIGA desliga. Com NIVEL_ALTO em 1, a bomba desliga na hora e o LIGA não funciona.

Com a bomba ligada, troque o modo: a bomba para. Para automático, liga após 10 s. Para manual, só com LIGA.
EME em 0 desliga em qualquer modo. Em automático, ao soltar, a bomba parte após 10 s.

#### 1.1.5. Recursos

| [Guia em PDF](experimento-03a-bomba.pdf) | 
| [Simulador em HTML](https://mitoedson.github.io/Automacao-de-Sistemas-Industriais/simulador/simulacao_bomba.html) |


### 1.2. 
