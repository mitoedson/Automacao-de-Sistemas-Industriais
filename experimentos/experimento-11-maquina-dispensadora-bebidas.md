# Experimento 11 — Máquina dispensadora de bebidas quentes

![alt text](image-13.png)

Trata-se de uma máquina dispensadora de bebidas quentes que pode fornecer as seguintes opções ao usuário: B1 - café puro, B2 - café com leite e B3 - chocolate quente, escolhida por uma chave seletora (B) de três posições.

O sistema é dotado de cinco reservatórios: R1 - café solúvel, R2 - leite em pó, R3 - chocolate, R4 - açúcar e R5 - água quente.  A dosagem de cada produto no copo descartável é feita pela abertura temporizada de válvulas VR1, VR2, VR3, VR4 e VR5 respectivamente.

Há também um dispositivo eletromecânico (AC) para alimentação de copo descartável, o qual posiciona corretamente apenas um copo a cada vez que for atuado. O sistema prevê ainda três níveis de liberação de açúcar: A1 - amargo, A2 - doce, A3 - extradoce, ajustado por uma chave seletora (A) de três posições.

Como condição inicial de funcionamento, um copo deve ser posicionado corretamente, o qual é monitorado pelo sensor SC. Como condição de finalização, o copo cheio deve ser retirado. 49 Assim, com a condição inicial satisfeita, um nível de açúcar e um tipo de bebida pré-selecionados, com o pressionar da botoeira de partida inicia-se o processo de preparo pela abertura temporizada das eletroválvulas. 

Primeiro ocorre a liberação de açúcar com os tempos de abertura de VR4 por 4 segundos para doce, 6 segundos para extradoce e sem liberação para amargo. Após o que, inicia-se então o preparo de uma das seguintes receitas (cada uma com as dosagens na ordem exata em que são apresentadas): Café puro: 3 segundos de café e 5 segundos de água quente. Café com leite: 2 seg. de café, 3 seg. de leite e 7 seg. de água quente. Chocolate: 2 seg. de leite, 3 seg. de chocolate e 6 seg. de água quente. 

## Desenvolvimento do experimento


Máquina dispensadora de bebidas quentes que fornece café puro (B1), café com leite (B2) e chocolate quente (B3), escolhidos pela chave seletora B de três posições. O sistema tem cinco reservatórios (café, leite, chocolate, açúcar e água quente), cada um com uma válvula temporizada (VR1 a VR5), e um alimentador de copos (AC). A chave seletora A de três posições define o nível de açúcar: amargo (A1), doce (A2) ou extradoce (A3).

Condição inicial: copo posicionado (sensor SC). Condição de finalização: copo cheio retirado. Com a condição inicial satisfeita e uma bebida e um nível de açúcar pré-selecionados, a botoeira de partida inicia o preparo.

### 1. Descrição do processo

1. **Açúcar (VR4):** 0 s no amargo, 4 s no doce e 6 s no extradoce.
2. **Receita** (dosagens na ordem exata):

| Bebida | Dosagem |
|---|---|
| Café puro (B1) | café 3 s, água quente 5 s |
| Café com leite (B2) | café 2 s, leite 3 s, água quente 7 s |
| Chocolate (B3) | leite 2 s, chocolate 3 s, água quente 6 s |

3. O ciclo termina quando o copo cheio é retirado (SC = 0).

**Emergência e falhas.** Ao pressionar o botão de emergência (EME), todas as etapas são desativadas e todas as válvulas fecham. Se o copo for retirado no meio do preparo, a máquina também vai ao estado de falha, para não derramar produto. O rearme exige EME liberado e START solto.

### 2. Entradas

| Endereço | TAG | Descrição |
|---|---|---|
| %I0.0 | SC | Sensor de copo posicionado |
| %I0.1 | START | Botoeira de partida |
| %I0.2 | B1 | Chave B, posição café puro |
| %I0.3 | B2 | Chave B, posição café com leite |
| %I0.4 | B3 | Chave B, posição chocolate |
| %I0.5 | A1 | Chave A, posição amargo |
| %I0.6 | A2 | Chave A, posição doce |
| %I0.7 | A3 | Chave A, posição extradoce |
| %I1.0 | BT_COPO | Botão do alimentador de copos (opcional) |
| %I1.1 | EME | Botão de emergência, contato NF (1 = normal, 0 = pressionado ou fio rompido) |

### 3. Saídas

| Endereço | TAG | Descrição |
|---|---|---|
| %Q0.0 | VR1 | Válvula do café |
| %Q0.1 | VR2 | Válvula do leite |
| %Q0.2 | VR3 | Válvula do chocolate |
| %Q0.3 | VR4 | Válvula do açúcar |
| %Q0.4 | VR5 | Válvula da água quente |
| %Q0.5 | AC | Alimentador de copos |

### 4. Outros recursos

| Recurso | TAG | Descrição | Temporizador (TON, IN = etapa) |
|---|---|---|---|
| %M10.0 | ETAPA_0 | Repouso | – |
| %M10.1 | ETAPA_1 | Açúcar doce (VR4) | "T_E1", PT = T#4s |
| %M10.2 | ETAPA_2 | Açúcar extradoce (VR4) | "T_E2", PT = T#6s |
| %M10.3 | ETAPA_3 | Despacho da receita | – |
| %M10.4 | ETAPA_4 | Café puro: café | "T_E4", PT = T#3s |
| %M10.5 | ETAPA_5 | Café puro: água | "T_E5", PT = T#5s |
| %M10.6 | ETAPA_6 | Café com leite: café | "T_E6", PT = T#2s |
| %M10.7 | ETAPA_7 | Café com leite: leite | "T_E7", PT = T#3s |
| %M11.0 | ETAPA_8 | Café com leite: água | "T_E8", PT = T#7s |
| %M11.1 | ETAPA_9 | Chocolate: leite | "T_E9", PT = T#2s |
| %M11.2 | ETAPA_10 | Chocolate: chocolate | "T_E10", PT = T#3s |
| %M11.3 | ETAPA_11 | Chocolate: água | "T_E11", PT = T#6s |
| %M11.4 | ETAPA_12 | Aguarda retirada do copo | – |
| %M12.0 | EMERG | Emergência ou falha (memorizado) | – |
| FirstScan | FirstScan | Bit de primeiro ciclo da memória de sistema da CPU | – |

### 5. Configuração no TIA Portal

1. Nas propriedades da CPU, em *Geral → Memória de sistema e de clock*, habilite a memória de sistema (por padrão MB1, com `FirstScan` em %M1.0) e cadastre o bit como `FirstScan`. MB0 e MB1 ficam reservados, por isso as etapas usam MB10 a MB12.
2. Cadastre as tags das seções 2, 3 e 4 na tabela de tags.
3. Insira dez blocos TON ("T_E1" a "T_E11", sem o T_E3), cada um com seu DB de instância, com os `PT` da tabela acima. Cada TON deve ser chamado a cada varredura, em uma network própria.
4. O endereço de bit no TIA vai de 0 a 7 (`byte.bit`), por isso as entradas passam de %I0.7 para %I1.0.

### 6. Código ladder

Legenda: `] [` contato NA, `]/[` contato NF, `( )` bobina, `( S )` set, `( R )` reset. Cada transição liga a etapa seguinte e desliga a atual. Monte cada bloco em uma network, na ordem apresentada. A emergência e o rearme ficam antes das transições para terem prioridade na varredura.

```
NETWORK 1 – Inicialização
   "FirstScan"                      "ETAPA_0"
 |----] [------+-------------------( S )
                |                   "ETAPA_1"
                +-------------------( R )
                |                   "ETAPA_2" ... "ETAPA_12"
                +-------------------( R )    (bobinas R em paralelo)

NETWORK 2 – Emergência (EME pressionado) ou copo retirado durante o preparo
   "EME"
 |----]/[-------------------------------+----+---------( S ) "EMERG"
                                         |    |
   "SC"   "ETAPA_0" "ETAPA_12"  "EMERG"  |    +---------( R ) "ETAPA_0"
 |--]/[------]/[-------]/[--------]/[-----+    |
                                              +---------( R ) "ETAPA_1" ... "ETAPA_12"
                                                         (bobinas R em paralelo)

NETWORK 3 – Rearme: EME liberado e START solto
   "EMERG"   "EME"     "START"                "EMERG"
 |----] [-----] [------]/[---------+---------( R )
                                   |         "ETAPA_0"
                                   +---------( S )

NETWORK 4 – Partida sem açúcar (amargo, A1) → despacho
   "ETAPA_0" "START"  "SC"   "B1"
 |----] [------] [-----] [----+--] [--+---] [---+-------( S ) "ETAPA_3"
                              |"B2"   |  "A1"   |
                              +--] [--+         +-------( R ) "ETAPA_0"
                              |"B3"   |
                              +--] [--+

NETWORK 5 – Partida com açúcar doce (A2)
   Mesma estrutura da network 4, com o contato "A2" no lugar de "A1"
   e as bobinas ( S ) "ETAPA_1" e ( R ) "ETAPA_0".

NETWORK 6 – Partida com açúcar extradoce (A3)
   Mesma estrutura da network 4, com o contato "A3" no lugar de "A1"
   e as bobinas ( S ) "ETAPA_2" e ( R ) "ETAPA_0".

NETWORK 7 – Fim do açúcar doce
   "ETAPA_1"   "T_E1".Q                "ETAPA_3"
 |----] [------] [---------+-----------( S )
                           |           "ETAPA_1"
                           +-----------( R )

NETWORK 8 – Fim do açúcar extradoce
   "ETAPA_2"   "T_E2".Q                "ETAPA_3"
 |----] [------] [---------+-----------( S )
                           |           "ETAPA_2"
                           +-----------( R )

NETWORK 9 – Despacho: café puro (B1)
   "ETAPA_3"   "B1"                    "ETAPA_4"
 |----] [------] [---------+-----------( S )
                           |           "ETAPA_3"
                           +-----------( R )

NETWORK 10 – Despacho: café com leite (B2)
   "ETAPA_3"   "B2"                    "ETAPA_6"
 |----] [------] [---------+-----------( S )
                           |           "ETAPA_3"
                           +-----------( R )

NETWORK 11 – Despacho: chocolate (B3)
   "ETAPA_3"   "B3"                    "ETAPA_9"
 |----] [------] [---------+-----------( S )
                           |           "ETAPA_3"
                           +-----------( R )

NETWORK 12 – Café puro: café → água
   "ETAPA_4"   "T_E4".Q                "ETAPA_5"
 |----] [------] [---------+-----------( S )
                           |           "ETAPA_4"
                           +-----------( R )

NETWORK 13 – Café puro: água → retirar copo
   "ETAPA_5"   "T_E5".Q                "ETAPA_12"
 |----] [------] [---------+-----------( S )
                           |           "ETAPA_5"
                           +-----------( R )

NETWORK 14 – Café com leite: café → leite
   "ETAPA_6"   "T_E6".Q                "ETAPA_7"
 |----] [------] [---------+-----------( S )
                           |           "ETAPA_6"
                           +-----------( R )

NETWORK 15 – Café com leite: leite → água
   "ETAPA_7"   "T_E7".Q                "ETAPA_8"
 |----] [------] [---------+-----------( S )
                           |           "ETAPA_7"
                           +-----------( R )

NETWORK 16 – Café com leite: água → retirar copo
   "ETAPA_8"   "T_E8".Q                "ETAPA_12"
 |----] [------] [---------+-----------( S )
                           |           "ETAPA_8"
                           +-----------( R )

NETWORK 17 – Chocolate: leite → chocolate
   "ETAPA_9"   "T_E9".Q                "ETAPA_10"
 |----] [------] [---------+-----------( S )
                           |           "ETAPA_9"
                           +-----------( R )

NETWORK 18 – Chocolate: chocolate → água
   "ETAPA_10"  "T_E10".Q               "ETAPA_11"
 |----] [------] [---------+-----------( S )
                           |           "ETAPA_10"
                           +-----------( R )

NETWORK 19 – Chocolate: água → retirar copo
   "ETAPA_11"  "T_E11".Q               "ETAPA_12"
 |----] [------] [---------+-----------( S )
                           |           "ETAPA_11"
                           +-----------( R )

NETWORK 20 – Copo retirado, volta ao repouso
   "ETAPA_12"  "SC"                    "ETAPA_0"
 |----] [------]/[---------+-----------( S )
                           |           "ETAPA_12"
                           +-----------( R )

NETWORK 21 – Temporizadores (uma network por linha, IN = etapa)
   "ETAPA_1"  |--] [---[ "T_E1"  TON  IN  Q ]   PT := T#4s
   "ETAPA_2"  |--] [---[ "T_E2"  TON  IN  Q ]   PT := T#6s
   "ETAPA_4"  |--] [---[ "T_E4"  TON  IN  Q ]   PT := T#3s
   "ETAPA_5"  |--] [---[ "T_E5"  TON  IN  Q ]   PT := T#5s
   "ETAPA_6"  |--] [---[ "T_E6"  TON  IN  Q ]   PT := T#2s
   "ETAPA_7"  |--] [---[ "T_E7"  TON  IN  Q ]   PT := T#3s
   "ETAPA_8"  |--] [---[ "T_E8"  TON  IN  Q ]   PT := T#7s
   "ETAPA_9"  |--] [---[ "T_E9"  TON  IN  Q ]   PT := T#2s
   "ETAPA_10" |--] [---[ "T_E10" TON  IN  Q ]   PT := T#3s
   "ETAPA_11" |--] [---[ "T_E11" TON  IN  Q ]   PT := T#6s

NETWORK 22 – VR1 (café)
   "ETAPA_4"
 |----] [--+------+------] [--------( ) "VR1"
   "ETAPA_6"      |    "EME"
 |----] [--+------+

NETWORK 23 – VR2 (leite)
   "ETAPA_7"
 |----] [--+------+------] [--------( ) "VR2"
   "ETAPA_9"      |    "EME"
 |----] [--+------+

NETWORK 24 – VR3 (chocolate)
   "ETAPA_10"    "EME"              "VR3"
 |----] [---------] [---------------( )

NETWORK 25 – VR4 (açúcar)
   "ETAPA_1"
 |----] [--+------+------] [--------( ) "VR4"
   "ETAPA_2"      |    "EME"
 |----] [--+------+

NETWORK 26 – VR5 (água quente)
   "ETAPA_5"
 |----] [--+------+------] [--------( ) "VR5"
   "ETAPA_8"      |    "EME"
 |----] [--+      |
   "ETAPA_11"     |
 |----] [--+------+

NETWORK 27 – Alimentador de copos (opcional)
   "ETAPA_0" "BT_COPO"  "SC"    "EME"        "AC"
 |----] [------] [------]/[------] [----------( )
```

### 7. Observações e teste

- Na network 4, a partida só é aceita com copo posicionado e uma bebida selecionada. No amargo (A1), o ciclo pula o açúcar e vai direto ao despacho (ETAPA_3).
- As chaves A e B têm três entradas discretas cada. Garanta na fiação que só uma posição de cada chave fique em 1.
- Como EME e o copo retirado levam todas as etapas a 0, a máquina não parte sozinha: ela só volta ao repouso pela network 3.
- O botão de emergência real deve ser cabeado também em hardware (relé de segurança cortando a alimentação das válvulas). O ladder sinaliza a emergência e organiza o rearme, mas não substitui a proteção exigida pela NR-12 e pela ISO 13850.
- **Teste no PLCSIM**, forçando as entradas:
  1. Ciclo normal: EME em 1, SC em 1, escolha B e A e dê START. As válvulas devem abrir na sequência e nos tempos da receita. Ao final, ponha SC em 0.
  2. Emergência: em qualquer etapa, ponha EME em 0. Todas as válvulas devem fechar e a máquina não deve religar ao soltar EME com START pressionado.
  3. Copo retirado no meio do preparo: ponha SC em 0 durante uma etapa. A máquina deve ir à falha.
  4. Rearme: libere EME, solte START e confirme o retorno à ETAPA_0.

## Desenvolvimento: Máquina dispensadora de bebidas quentes

Máquina dispensadora de bebidas quentes que fornece café puro (B1), café com leite (B2) e chocolate quente (B3), escolhidos pela chave seletora B de três posições. O sistema tem cinco reservatórios (café, leite, chocolate, açúcar e água quente), cada um com uma válvula temporizada (VR1 a VR5), e um alimentador de copos (AC). A chave seletora A de três posições define o nível de açúcar: amargo (A1), doce (A2) ou extradoce (A3).

Condição inicial: copo posicionado (sensor SC). Condição de finalização: copo cheio retirado. Com a condição inicial satisfeita e uma bebida e um nível de açúcar pré-selecionados, a botoeira de partida inicia o preparo.


## 1. Descrição do processo

1. **Açúcar (VR4):** 0 s no amargo, 4 s no doce e 6 s no extradoce.
2. **Receita** (dosagens na ordem exata):

| Bebida | Dosagem |
|---|---|
| Café puro (B1) | café 3 s, água quente 5 s |
| Café com leite (B2) | café 2 s, leite 3 s, água quente 7 s |
| Chocolate (B3) | leite 2 s, chocolate 3 s, água quente 6 s |

3. O ciclo termina quando o copo cheio é retirado (SC = 0).

**Emergência e falhas.** Ao pressionar o botão de emergência (EME), todas as etapas são desativadas e todas as válvulas fecham. Se o copo for retirado no meio do preparo, a máquina também vai ao estado de falha, para não derramar produto. O rearme exige EME liberado e START solto.

## 2. Entradas

| Endereço | TAG | Descrição |
|---|---|---|
| %I0.0 | SC | Sensor de copo posicionado |
| %I0.1 | START | Botoeira de partida |
| %I0.2 | B1 | Chave B, posição café puro |
| %I0.3 | B2 | Chave B, posição café com leite |
| %I0.4 | B3 | Chave B, posição chocolate |
| %I0.5 | A1 | Chave A, posição amargo |
| %I0.6 | A2 | Chave A, posição doce |
| %I0.7 | A3 | Chave A, posição extradoce |
| %I1.0 | BT_COPO | Botão do alimentador de copos (opcional) |
| %I1.1 | EME | Botão de emergência, contato NF (1 = normal, 0 = pressionado ou fio rompido) |

## 3. Saídas

| Endereço | TAG | Descrição |
|---|---|---|
| %Q0.0 | VR1 | Válvula do café |
| %Q0.1 | VR2 | Válvula do leite |
| %Q0.2 | VR3 | Válvula do chocolate |
| %Q0.3 | VR4 | Válvula do açúcar |
| %Q0.4 | VR5 | Válvula da água quente |
| %Q0.5 | AC | Alimentador de copos |

## 4. Outros recursos

| Recurso | TAG | Descrição | Temporizador (TON, IN = etapa) |
|---|---|---|---|
| %M10.0 | ETAPA_0 | Repouso | – |
| %M10.1 | ETAPA_1 | Açúcar doce (VR4) | "T_E1", PT = T#4s |
| %M10.2 | ETAPA_2 | Açúcar extradoce (VR4) | "T_E2", PT = T#6s |
| %M10.3 | ETAPA_3 | Despacho da receita | – |
| %M10.4 | ETAPA_4 | Café puro: café | "T_E4", PT = T#3s |
| %M10.5 | ETAPA_5 | Café puro: água | "T_E5", PT = T#5s |
| %M10.6 | ETAPA_6 | Café com leite: café | "T_E6", PT = T#2s |
| %M10.7 | ETAPA_7 | Café com leite: leite | "T_E7", PT = T#3s |
| %M11.0 | ETAPA_8 | Café com leite: água | "T_E8", PT = T#7s |
| %M11.1 | ETAPA_9 | Chocolate: leite | "T_E9", PT = T#2s |
| %M11.2 | ETAPA_10 | Chocolate: chocolate | "T_E10", PT = T#3s |
| %M11.3 | ETAPA_11 | Chocolate: água | "T_E11", PT = T#6s |
| %M11.4 | ETAPA_12 | Aguarda retirada do copo | – |
| %M12.0 | EMERG | Emergência ou falha (memorizado) | – |
| FirstScan | FirstScan | Bit de primeiro ciclo da memória de sistema da CPU | – |

## 5. Configuração no TIA Portal

1. Nas propriedades da CPU, em *Geral → Memória de sistema e de clock*, habilite a memória de sistema (por padrão MB1, com `FirstScan` em %M1.0) e cadastre o bit como `FirstScan`. MB0 e MB1 ficam reservados, por isso as etapas usam MB10 a MB12.
2. Cadastre as tags das seções 2, 3 e 4 na tabela de tags.
3. Insira dez blocos TON ("T_E1" a "T_E11", sem o T_E3), cada um com seu DB de instância, com os `PT` da tabela acima. Cada TON deve ser chamado a cada varredura, em uma network própria.
4. O endereço de bit no TIA vai de 0 a 7 (`byte.bit`), por isso as entradas passam de %I0.7 para %I1.0.

## 6. Código ladder

Legenda: `] [` contato NA, `]/[` contato NF, `( )` bobina, `( S )` set, `( R )` reset. Cada transição liga a etapa seguinte e desliga a atual. Monte cada bloco em uma network, na ordem apresentada. A emergência e o rearme ficam antes das transições para terem prioridade na varredura.

```
NETWORK 1 – Inicialização
   "FirstScan"                      "ETAPA_0"
 |----] [------+-------------------( S )
                |                   "ETAPA_1"
                +-------------------( R )
                |                   "ETAPA_2" ... "ETAPA_12"
                +-------------------( R )    (bobinas R em paralelo)

NETWORK 2 – Emergência (EME pressionado) ou copo retirado durante o preparo
   "EME"
 |----]/[-------------------------------+----+---------( S ) "EMERG"
                                         |    |
   "SC"   "ETAPA_0" "ETAPA_12"  "EMERG"  |    +---------( R ) "ETAPA_0"
 |--]/[------]/[-------]/[--------]/[-----+    |
                                              +---------( R ) "ETAPA_1" ... "ETAPA_12"
                                                         (bobinas R em paralelo)

NETWORK 3 – Rearme: EME liberado e START solto
   "EMERG"   "EME"     "START"                "EMERG"
 |----] [-----] [------]/[---------+---------( R )
                                   |         "ETAPA_0"
                                   +---------( S )

NETWORK 4 – Partida sem açúcar (amargo, A1) → despacho
   "ETAPA_0" "START"  "SC"   "B1"
 |----] [------] [-----] [----+--] [--+---] [---+-------( S ) "ETAPA_3"
                              |"B2"   |  "A1"   |
                              +--] [--+         +-------( R ) "ETAPA_0"
                              |"B3"   |
                              +--] [--+

NETWORK 5 – Partida com açúcar doce (A2)
   Mesma estrutura da network 4, com o contato "A2" no lugar de "A1"
   e as bobinas ( S ) "ETAPA_1" e ( R ) "ETAPA_0".

NETWORK 6 – Partida com açúcar extradoce (A3)
   Mesma estrutura da network 4, com o contato "A3" no lugar de "A1"
   e as bobinas ( S ) "ETAPA_2" e ( R ) "ETAPA_0".

NETWORK 7 – Fim do açúcar doce
   "ETAPA_1"   "T_E1".Q                "ETAPA_3"
 |----] [------] [---------+-----------( S )
                           |           "ETAPA_1"
                           +-----------( R )

NETWORK 8 – Fim do açúcar extradoce
   "ETAPA_2"   "T_E2".Q                "ETAPA_3"
 |----] [------] [---------+-----------( S )
                           |           "ETAPA_2"
                           +-----------( R )

NETWORK 9 – Despacho: café puro (B1)
   "ETAPA_3"   "B1"                    "ETAPA_4"
 |----] [------] [---------+-----------( S )
                           |           "ETAPA_3"
                           +-----------( R )

NETWORK 10 – Despacho: café com leite (B2)
   "ETAPA_3"   "B2"                    "ETAPA_6"
 |----] [------] [---------+-----------( S )
                           |           "ETAPA_3"
                           +-----------( R )

NETWORK 11 – Despacho: chocolate (B3)
   "ETAPA_3"   "B3"                    "ETAPA_9"
 |----] [------] [---------+-----------( S )
                           |           "ETAPA_3"
                           +-----------( R )

NETWORK 12 – Café puro: café → água
   "ETAPA_4"   "T_E4".Q                "ETAPA_5"
 |----] [------] [---------+-----------( S )
                           |           "ETAPA_4"
                           +-----------( R )

NETWORK 13 – Café puro: água → retirar copo
   "ETAPA_5"   "T_E5".Q                "ETAPA_12"
 |----] [------] [---------+-----------( S )
                           |           "ETAPA_5"
                           +-----------( R )

NETWORK 14 – Café com leite: café → leite
   "ETAPA_6"   "T_E6".Q                "ETAPA_7"
 |----] [------] [---------+-----------( S )
                           |           "ETAPA_6"
                           +-----------( R )

NETWORK 15 – Café com leite: leite → água
   "ETAPA_7"   "T_E7".Q                "ETAPA_8"
 |----] [------] [---------+-----------( S )
                           |           "ETAPA_7"
                           +-----------( R )

NETWORK 16 – Café com leite: água → retirar copo
   "ETAPA_8"   "T_E8".Q                "ETAPA_12"
 |----] [------] [---------+-----------( S )
                           |           "ETAPA_8"
                           +-----------( R )

NETWORK 17 – Chocolate: leite → chocolate
   "ETAPA_9"   "T_E9".Q                "ETAPA_10"
 |----] [------] [---------+-----------( S )
                           |           "ETAPA_9"
                           +-----------( R )

NETWORK 18 – Chocolate: chocolate → água
   "ETAPA_10"  "T_E10".Q               "ETAPA_11"
 |----] [------] [---------+-----------( S )
                           |           "ETAPA_10"
                           +-----------( R )

NETWORK 19 – Chocolate: água → retirar copo
   "ETAPA_11"  "T_E11".Q               "ETAPA_12"
 |----] [------] [---------+-----------( S )
                           |           "ETAPA_11"
                           +-----------( R )

NETWORK 20 – Copo retirado, volta ao repouso
   "ETAPA_12"  "SC"                    "ETAPA_0"
 |----] [------]/[---------+-----------( S )
                           |           "ETAPA_12"
                           +-----------( R )

NETWORK 21 – Temporizadores (uma network por linha, IN = etapa)
   "ETAPA_1"  |--] [---[ "T_E1"  TON  IN  Q ]   PT := T#4s
   "ETAPA_2"  |--] [---[ "T_E2"  TON  IN  Q ]   PT := T#6s
   "ETAPA_4"  |--] [---[ "T_E4"  TON  IN  Q ]   PT := T#3s
   "ETAPA_5"  |--] [---[ "T_E5"  TON  IN  Q ]   PT := T#5s
   "ETAPA_6"  |--] [---[ "T_E6"  TON  IN  Q ]   PT := T#2s
   "ETAPA_7"  |--] [---[ "T_E7"  TON  IN  Q ]   PT := T#3s
   "ETAPA_8"  |--] [---[ "T_E8"  TON  IN  Q ]   PT := T#7s
   "ETAPA_9"  |--] [---[ "T_E9"  TON  IN  Q ]   PT := T#2s
   "ETAPA_10" |--] [---[ "T_E10" TON  IN  Q ]   PT := T#3s
   "ETAPA_11" |--] [---[ "T_E11" TON  IN  Q ]   PT := T#6s

NETWORK 22 – VR1 (café)
   "ETAPA_4"
 |----] [--+------+------] [--------( ) "VR1"
   "ETAPA_6"      |    "EME"
 |----] [--+------+

NETWORK 23 – VR2 (leite)
   "ETAPA_7"
 |----] [--+------+------] [--------( ) "VR2"
   "ETAPA_9"      |    "EME"
 |----] [--+------+

NETWORK 24 – VR3 (chocolate)
   "ETAPA_10"    "EME"              "VR3"
 |----] [---------] [---------------( )

NETWORK 25 – VR4 (açúcar)
   "ETAPA_1"
 |----] [--+------+------] [--------( ) "VR4"
   "ETAPA_2"      |    "EME"
 |----] [--+------+

NETWORK 26 – VR5 (água quente)
   "ETAPA_5"
 |----] [--+------+------] [--------( ) "VR5"
   "ETAPA_8"      |    "EME"
 |----] [--+      |
   "ETAPA_11"     |
 |----] [--+------+

NETWORK 27 – Alimentador de copos (opcional)
   "ETAPA_0" "BT_COPO"  "SC"    "EME"        "AC"
 |----] [------] [------]/[------] [----------( )
```

## 7. Observações e teste

- Na network 4, a partida só é aceita com copo posicionado e uma bebida selecionada. No amargo (A1), o ciclo pula o açúcar e vai direto ao despacho (ETAPA_3).
- As chaves A e B têm três entradas discretas cada. Garanta na fiação que só uma posição de cada chave fique em 1.
- Como EME e o copo retirado levam todas as etapas a 0, a máquina não parte sozinha: ela só volta ao repouso pela network 3.
- O botão de emergência real deve ser cabeado também em hardware (relé de segurança cortando a alimentação das válvulas). O ladder sinaliza a emergência e organiza o rearme, mas não substitui a proteção exigida pela NR-12 e pela ISO 13850.
- **Teste no PLCSIM**, forçando as entradas:
  1. Ciclo normal: EME em 1, SC em 1, escolha B e A e dê START. As válvulas devem abrir na sequência e nos tempos da receita. Ao final, ponha SC em 0.
  2. Emergência: em qualquer etapa, ponha EME em 0. Todas as válvulas devem fechar e a máquina não deve religar ao soltar EME com START pressionado.
  3. Copo retirado no meio do preparo: ponha SC em 0 durante uma etapa. A máquina deve ir à falha.
  4. Rearme: libere EME, solte START e confirme o retorno à ETAPA_0.

### Recursos

| Guia em PDF | 
| [Simulador em HTML](https://mitoedson.github.io/Automacao-de-Sistemas-Industriais/simulador/simulacao_maquina-bebidas.html) |
