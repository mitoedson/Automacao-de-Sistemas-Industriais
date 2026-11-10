# Experimento 14 — Extração de óleo e gás

![alt text](image-16.png)

A figura acima descreve parte de um processo de extração de óleo e gás em alto mar.  

O tanque SD-501 possui um medidor de nível que fornece um sinal de 4 a 20mA a uma entrada analógica de CLP e seu tag é LTI_SD_501. Para controlar adequadamente o processo deverá ser implementada a seguinte lógica de funcionamento: 

1. Através do LTI_SD_501 gerar 4 alarmes(digitais) LTSD501_LL, LTSD501_LO, LTSD501_HI, LTSD501_HH 

    1.1. Esses alarmes precisam de um tempo de retardo de 3 segundos pois o tanque está em uma plataforma de petróleo do tipo flutuante sujeita a balanço. 

2. Cada bomba possui um acionamento manual e automático (cuja lógica em ladder deverá ser implementada) 

    2.1. B_501_1 – Possui B_501_1_AUTMAN(AUTO=1, MAN=0) chave de funcionamento de automático para manual, B_501_1_LIGA e B_501_1_DESLIGA Chaves de campo do tipo não retentivas ; B_501_1_LIGAAUT(Liga = 1) chave virtual (endereço de memória) que é utilizada na lógica de acionamento automático da bomba. 

    2.2. B_501_2 – Possui B_501_2_AUTMAN(AUTO=1, MAN=0) chave de funcionamento de automático para manual, B_501_2_LIGA e B_501_2_DESLIGA Chaves de campo do tipo não retentivas ; 54 B_501_2_LIGAAUT(Liga = 1) chave virtual (endereço de memória) que é utilizada na lógica de acionamento automático da bomba. 

    2.3. B_501_3 – Possui B_501_3_AUTMAN(AUTO=1, MAN=0) chave de funcionamento de automático para manual, B_501_3_LIGA e B_501_3_DESLIGA Chaves de campo do tipo não retentivas ; B_501_3_LIGAAUT(Liga = 1) chave virtual (endereço de memória) que é utilizada na lógica de acionamento automático da bomba. 

    2.4. B_501_4 – Possui B_501_4_AUTMAN(AUTO=1, MAN=0) chave de funcionamento de automático para manual, B_501_4_LIGA e B_501_4_DESLIGA Chaves de campo do tipo não retentivas ; B_501_4_LIGAAUT(Liga = 1) chave virtual (endereço de memória) que é utilizada na lógica de acionamento automático da bomba. 

3. Com os alarmes implemente a seguinte lógica de funcionamento das bombas B_501_1, B_501_2, B_501_3, B_501_4: 

    3.1. Caso nenhum alarme esteja acionado (situação normal de operação) a B_501_1 liga em automático. 

    3.2. Caso LTSD501_LL ou LTSD501_LO estiverem acionados todas as bombas devem desligar em automático. 

    3.3. Caso LTSD501_HI estiver acionado, as B_501_1 e B_501_2 são acionadas em automático.

    3.4. Caso LTSD501_HH estiver acionado, todas as bombas são ligadas em automático 

    
## Desenvolvimento: Tanque SD-501 e bombas B_501_1 a B_501_4

Parte de um processo de extração de óleo e gás em alto mar. O tanque SD-501 tem um medidor de nível com sinal de 4 a 20 mA ligado a uma entrada analógica do CLP (tag LTI_SD_501). A partir dele são gerados quatro alarmes digitais, cada um com retardo de 3 s por causa do balanço da plataforma flutuante, e quatro bombas funcionam em automático ou manual.


## 1. Descrição do processo

Lógica automática das bombas:

1. Nenhum alarme (operação normal): B_501_1 liga.
2. LTSD501_LL ou LTSD501_LO ativo: todas as bombas desligam.
3. LTSD501_HI ativo: B_501_1 e B_501_2 ligam.
4. LTSD501_HH ativo: todas as bombas ligam.

Em manual, cada bomba liga com o botão LIGA (não retentivo, com selo no CLP) e desliga com o botão DESLIGA.

**Hipóteses adotadas**

- Setpoints dos alarmes (o enunciado não os define): LL ≤ 10 %, LO ≤ 20 %, HI ≥ 80 % e HH ≥ 90 %. Ajuste conforme o processo.
- Os botões DESLIGA e EME são normalmente fechados (1 = normal, 0 = pressionado), como no 3º trabalho.
- AUTMAN: AUTO = 1 e MAN = 0.
- O botão de emergência (EME) desliga as quatro bombas, em automático e em manual.

## 2. Entradas

| Endereço | TAG | Descrição |
|---|---|---|
| %IW64 | LTI_SD_501_RAW | Nível do tanque, 4–20 mA (valor bruto 0–27648) |
| %I0.0 | B_501_1_AUTMAN | Chave automático/manual da bomba 1 |
| %I0.1 | B_501_1_LIGA | Botão liga da bomba 1 |
| %I0.2 | B_501_1_DESLIGA | Botão desliga da bomba 1 (NF) |
| %I0.3 | B_501_2_AUTMAN | Chave automático/manual da bomba 2 |
| %I0.4 | B_501_2_LIGA | Botão liga da bomba 2 |
| %I0.5 | B_501_2_DESLIGA | Botão desliga da bomba 2 (NF) |
| %I0.6 | B_501_3_AUTMAN | Chave automático/manual da bomba 3 |
| %I0.7 | B_501_3_LIGA | Botão liga da bomba 3 |
| %I1.0 | B_501_3_DESLIGA | Botão desliga da bomba 3 (NF) |
| %I1.1 | B_501_4_AUTMAN | Chave automático/manual da bomba 4 |
| %I1.2 | B_501_4_LIGA | Botão liga da bomba 4 |
| %I1.3 | B_501_4_DESLIGA | Botão desliga da bomba 4 (NF) |
| %I1.4 | EME | Botão de emergência (NF) |

## 3. Saídas

| Endereço | TAG | Descrição |
|---|---|---|
| %Q0.0 | B_501_1 | Comando da bomba 1 |
| %Q0.1 | B_501_2 | Comando da bomba 2 |
| %Q0.2 | B_501_3 | Comando da bomba 3 |
| %Q0.3 | B_501_4 | Comando da bomba 4 |

## 4. Outros recursos

| Recurso | TAG | Descrição |
|---|---|---|
| %MD20 | NIVEL | Nível do tanque em % (Real) |
| %MD24 | NIVEL_NORM | Nível normalizado 0.0 a 1.0 (Real, auxiliar) |
| %M10.0 | LTSD501_LL | Alarme nível muito baixo (≤ 10 %) |
| %M10.1 | LTSD501_LO | Alarme nível baixo (≤ 20 %) |
| %M10.2 | LTSD501_HI | Alarme nível alto (≥ 80 %) |
| %M10.3 | LTSD501_HH | Alarme nível muito alto (≥ 90 %) |
| %M10.4 | B_501_1_LIGAAUT | Chave virtual: liga a bomba 1 em automático |
| %M10.5 | B_501_2_LIGAAUT | Chave virtual: liga a bomba 2 em automático |
| %M10.6 | B_501_3_LIGAAUT | Chave virtual: liga a bomba 3 em automático |
| %M10.7 | B_501_4_LIGAAUT | Chave virtual: liga a bomba 4 em automático |
| DB "T_LL", "T_LO", "T_HI", "T_HH" | – | TON, PT = T#3s (retardo dos alarmes) |

## 5. Configuração no TIA Portal

1. **Entrada analógica.** As entradas analógicas integradas da CPU S7-1200 são de 0 a 10 V. Para 4 a 20 mA use um módulo analógico (por exemplo SM 1231 AI), configure o canal como corrente de 4 a 20 mA e anote o endereço da palavra (aqui %IW64).
2. Em 4–20 mA, 4 mA corresponde ao valor bruto 5530 e 20 mA a 27648.
3. Cadastre as tags das seções 2, 3 e 4 na tabela de tags. Os bits de alarme e LIGAAUT usam o byte MB10, e MB0 e MB1 ficam reservados à memória de sistema e de clock.
4. Insira quatro blocos TON ("T_LL", "T_LO", "T_HI" e "T_HH") com DB de instância.
5. O endereço de bit no TIA vai de 0 a 7 (`byte.bit`), por isso as entradas passam de %I0.7 para %I1.0.

## 6. Código ladder

Legenda: `] [` contato NA, `]/[` contato NF, `( )` bobina. Os blocos `[ "NIVEL" <= 10.0 ]` são comparações de Real e `[ "T_LL" TON IN Q ]` é o temporizador.

**Escalonamento (network 1)**

```
NETWORK 1 – Escalonamento 4–20 mA → 0–100 %
   (sempre ativo)
 |----[ NORM_X   MIN := 5530    VALUE := "LTI_SD_501_RAW"   MAX := 27648    OUT => "NIVEL_NORM" ]
 |----[ SCALE_X  MIN := 0.0     VALUE := "NIVEL_NORM"       MAX := 100.0    OUT => "NIVEL" ]
```

**Alarmes com retardo de 3 s (networks 2 a 5)**

```
NETWORK 2 – LTSD501_LL
 [ "NIVEL" <= 10.0 ]---[ "T_LL" TON  IN  Q ]---------( ) "LTSD501_LL"
                           PT := T#3s

NETWORK 3 – LTSD501_LO
 [ "NIVEL" <= 20.0 ]---[ "T_LO" TON  IN  Q ]---------( ) "LTSD501_LO"
                           PT := T#3s

NETWORK 4 – LTSD501_HI
 [ "NIVEL" >= 80.0 ]---[ "T_HI" TON  IN  Q ]---------( ) "LTSD501_HI"
                           PT := T#3s

NETWORK 5 – LTSD501_HH
 [ "NIVEL" >= 90.0 ]---[ "T_HH" TON  IN  Q ]---------( ) "LTSD501_HH"
                           PT := T#3s
```

O retardo é na subida: o alarme só é acionado se a condição se mantiver por 3 s, o que filtra o balanço da plataforma.

**Lógica automática das bombas (networks 6 a 9)**

```
NETWORK 6 – B_501_1_LIGAAUT: liga sem alarme, com HI ou com HH; desliga com LL ou LO
   "LTSD501_LL"  "LTSD501_LO"
 |-----]/[---------]/[------------------------( ) "B_501_1_LIGAAUT"

NETWORK 7 – B_501_2_LIGAAUT: liga com HI ou HH
   "LTSD501_HI"
 |-----] [--+-------------------------------( ) "B_501_2_LIGAAUT"
   "LTSD501_HH"
 |-----] [--+

NETWORK 8 – B_501_3_LIGAAUT: liga com HH
   "LTSD501_HH"
 |-----] [----------------------------------( ) "B_501_3_LIGAAUT"

NETWORK 9 – B_501_4_LIGAAUT: liga com HH
   "LTSD501_HH"
 |-----] [----------------------------------( ) "B_501_4_LIGAAUT"
```

**Acionamento manual e automático de cada bomba (networks 10 a 13)**

No modo AUTO (AUTMAN = 1), a bomba segue a chave virtual LIGAAUT. No modo MANUAL (AUTMAN = 0), LIGA liga e sela, e DESLIGA corta o selo. DESLIGA é NF, por isso entra como contato NA (`] [`): ele passa com o botão solto e abre com o botão pressionado. O contato de EME fica em série com a bobina.

```
NETWORK 10 – Bomba B_501_1
   "B_501_1_AUTMAN"  "B_501_1_LIGAAUT"
 |----] [---------------] [------------------------------+
 |                                                         |
 |  "B_501_1_AUTMAN" "B_501_1_LIGA"      "B_501_1_DESLIGA" |
 |----]/[-------+----] [-------+----------] [--------------+----] [----( ) "B_501_1"
 |              | "B_501_1"    |                                "EME"
 |              +----] [-------+

NETWORK 11 – Bomba B_501_2
   "B_501_2_AUTMAN"  "B_501_2_LIGAAUT"
 |----] [---------------] [------------------------------+
 |                                                         |
 |  "B_501_2_AUTMAN" "B_501_2_LIGA"      "B_501_2_DESLIGA" |
 |----]/[-------+----] [-------+----------] [--------------+----] [----( ) "B_501_2"
 |              | "B_501_2"    |                                "EME"
 |              +----] [-------+

NETWORK 12 – Bomba B_501_3
   "B_501_3_AUTMAN"  "B_501_3_LIGAAUT"
 |----] [---------------] [------------------------------+
 |                                                         |
 |  "B_501_3_AUTMAN" "B_501_3_LIGA"      "B_501_3_DESLIGA" |
 |----]/[-------+----] [-------+----------] [--------------+----] [----( ) "B_501_3"
 |              | "B_501_3"    |                                "EME"
 |              +----] [-------+

NETWORK 13 – Bomba B_501_4
   "B_501_4_AUTMAN"  "B_501_4_LIGAAUT"
 |----] [---------------] [------------------------------+
 |                                                         |
 |  "B_501_4_AUTMAN" "B_501_4_LIGA"      "B_501_4_DESLIGA" |
 |----]/[-------+----] [-------+----------] [--------------+----] [----( ) "B_501_4"
 |              | "B_501_4"    |                                "EME"
 |              +----] [-------+
```

## 7. Observações e teste

- Como a lógica é combinacional, não há histerese: a B_501_2 liga e desliga exatamente nos limites HI e HH. Para ter histerese, use bobinas Set/Reset nos alarmes com limites de entrada e saída diferentes.
- **Fio rompido.** Se a corrente cair abaixo de cerca de 3,5 mA (valor bruto abaixo de 4000), o sensor está em falha. Vale gerar um alarme de falha do transmissor e desligar as bombas nesse caso.
- Sugestão de proteção, não pedida no enunciado: desligar as bombas também em manual quando LL estiver ativo.
- O botão de emergência real deve ser cabeado também em hardware (relé de segurança cortando a alimentação das bombas). O ladder não substitui a proteção exigida pela NR-12 e pela ISO 13850.
- **Teste no PLCSIM**, forçando `"LTI_SD_501_RAW"` (ou `"NIVEL"`) e aguardando 3 s a cada mudança:

| Nível | Bombas ligadas em automático |
|---|---|
| 50 % | B_501_1 |
| 85 % | B_501_1 e B_501_2 |
| 95 % | as quatro |
| 15 % ou 5 % | nenhuma |

  Depois, teste o manual (AUTMAN em 0: LIGA liga e DESLIGA desliga) e a emergência (EME em 0 desliga tudo).


### Recursos 

| [Guia em PDF](experimento-14-tanque-bombas.pdf) | 
| [Simulador em HTML](https://mitoedson.github.io/Automacao-de-Sistemas-Industriais/simulador/simulacao_extracao.html) |
