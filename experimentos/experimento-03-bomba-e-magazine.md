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

### Exercício 1 — Controle de bomba

Uma bomba enche um tanque com duas boias de nível (baixo e alto), uma chave automático/manual e os botões LIGA e DESLIGA.

- **Manual:** a bomba liga com LIGA e desliga com DESLIGA. As boias não têm nenhuma ação.
- **Automático:** a bomba liga 10 s após a detecção de NÍVEL BAIXO e desliga 10 s após a detecção de NÍVEL ALTO. Se a condição desaparecer antes dos 10 s, a contagem recomeça.

**Hipóteses adotadas**

- A passagem de manual para automático é sem solavanco: o estado da bomba é mantido até o próximo evento de nível.
- DESLIGA e EME são normalmente fechados (1 = normal, 0 = pressionado).
- O botão de emergência desliga a bomba nos dois modos.

### Exercício 2 — Alimentador de peças por magazine

Um cilindro (S1) empurra uma peça de um magazine vertical. O cilindro começa recuado. Com o botão de início (E1) pressionado e peça no magazine, ele avança até o fim de curso (E2) e recua, voltando à condição inicial.

- Se E1 for pressionado e não houver peça, o alarme sonoro S2 toca enquanto E1 estiver pressionado.
- Se o cilindro levar mais de 10 s para chegar ao fim de curso, o alarme S3 é acionado.
- A cada fim de curso, um contador é incrementado. Ao chegar a 10 peças, o alarme S4 é acionado para o operador retirar o cesto e zerar o contador. Enquanto o contador não for zerado, o ciclo fica bloqueado.
- No item c do enunciado, o sensor E4 é trocado por um do tipo NF. Só a network 2 muda.

**Hipóteses adotadas**

- O enunciado cita E3 e E4 de forma inconsistente. Segue-se a tabela: E3 é o botão "operador retirou peças" (zera o contador) e E4 é o sensor de peça no magazine.
- O enunciado não numera os alarmes de tempo e de cesto cheio. Foram adotados S3 (tempo excedido) e S4 (cesto cheio).
- O botão E3 também reconhece o alarme de tempo excedido (S3).
- O início do ciclo exige uma nova pressão de E1 (borda de subida), para um botão mantido apertado não repetir o ciclo.

## 2. Entradas

**Exercício 1**

| Endereço | TAG | Descrição |
|---|---|---|
| %I0.0 | NIVEL_BAIXO | 1 = nível abaixo da boia baixa |
| %I0.1 | NIVEL_ALTO | 1 = nível acima da boia alta |
| %I0.2 | AUTO | 1 = automático, 0 = manual |
| %I0.3 | LIGA | Botão liga |
| %I0.4 | DESLIGA | Botão desliga (NF) |
| %I0.5 | EME | Botão de emergência (NF) |

**Exercício 2**

| Endereço | TAG | Descrição |
|---|---|---|
| %I0.0 | E1_START | Botão de início de ciclo (NA, não retentivo) |
| %I0.1 | E2_FIM_CURSO | Fim de curso do cilindro (avançado) |
| %I0.2 | E3_RESET | Botão: operador retirou as peças (zera contador e reconhece alarme de tempo) |
| %I0.3 | E4_PECA | Sensor de peça no magazine (NA: 1 = há peça) |
| %I0.4 | EME | Botão de emergência (NF) |

## 3. Saídas

**Exercício 1**

| Endereço | TAG | Descrição |
|---|---|---|
| %Q0.1 | BOMBA | Comando da bomba |

**Exercício 2**

| Endereço | TAG | Descrição |
|---|---|---|
| %Q0.0 | S1_CILINDRO | Avanço do cilindro |
| %Q0.1 | S2_ALM_SEM_PECA | Alarme sonoro: sem peça |
| %Q0.2 | S3_ALM_TEMPO | Alarme: cilindro demorou mais de 10 s |
| %Q0.3 | S4_ALM_CESTO | Alarme: 10 peças, retirar o cesto |

## 4. Outros recursos

**Exercício 1**

| Recurso | TAG | Descrição |
|---|---|---|
| %M10.0 | AUT_ON | Estado da bomba no modo automático (memorizado) |
| DB "T_LIGA" | T_LIGA | TON, PT = T#10s |
| DB "T_DESL" | T_DESL | TON, PT = T#10s |

**Exercício 2**

| Recurso | TAG | Descrição |
|---|---|---|
| %M10.0 a %M10.2 | ETAPA_0 a ETAPA_2 | Repouso, cilindro avançando, cilindro recuando |
| %M10.3 | EMERG | Emergência memorizada |
| %M10.4 | PECA | Há peça no magazine (vem de E4) |
| %M10.5 | ALM_TEMPO | Alarme de tempo excedido memorizado |
| %M10.6 | E1_PULSO | Pulso de uma varredura na pressão de E1 |
| %M11.0 | M_E1_ME | Memória de borda de E1 |
| DB "C_PECAS" | C_PECAS | CTU, PV = 10 (peças) |
| DB "T_AVANCO" | T_AVANCO | TON, PT = T#10s |
| FirstScan | FirstScan | Bit de primeiro ciclo da memória de sistema |

## 5. Configuração no TIA Portal

1. Nas propriedades da CPU, em *Geral → Memória de sistema e de clock*, habilite a memória de sistema (por padrão MB1, com `FirstScan` em %M1.0) e cadastre o bit como `FirstScan`. MB0 e MB1 ficam reservados, por isso os bits usam MB10 e MB11.
2. Cadastre as tags das seções 2, 3 e 4 na tabela de tags.
3. Insira os blocos TON e o CTU com DB de instância e os presets da seção 4. Cada bloco deve ser chamado a cada varredura, em uma network própria.
4. O endereço de bit no TIA vai de 0 a 7 (`byte.bit`).

## 6. Código ladder

Legenda: `] [` contato NA, `]/[` contato NF, `( )` bobina, `( S )` set, `( R )` reset, `P` contato de borda de subida. Monte cada bloco em uma network, na ordem apresentada.

**Exercício 1 — Bomba**

```
NETWORK 1 – Retardo de 10 s para ligar (automático, nível baixo)
   "AUTO"    "NIVEL_BAIXO"
 |--] [---------] [---------------[ "T_LIGA" TON  IN  Q ]
                                      PT := T#10s

NETWORK 2 – Retardo de 10 s para desligar (automático, nível alto)
   "AUTO"    "NIVEL_ALTO"
 |--] [---------] [---------------[ "T_DESL" TON  IN  Q ]
                                      PT := T#10s

NETWORK 3 – Em manual, AUT_ON acompanha a bomba (liga)
   "AUTO"    "BOMBA"                "AUT_ON"
 |--]/[--------] [-----------------( S )

NETWORK 4 – Em manual, AUT_ON acompanha a bomba (desliga)
   "AUTO"    "BOMBA"                "AUT_ON"
 |--]/[--------]/[----------------( R )

NETWORK 5 – Nível baixo por 10 s liga a bomba em automático
   "T_LIGA".Q                       "AUT_ON"
 |-----] [-------------------------( S )

NETWORK 6 – Nível alto por 10 s desliga a bomba em automático
   "T_DESL".Q                       "AUT_ON"
 |-----] [-------------------------( R )

NETWORK 7 – Comando da bomba
   "AUTO"    "AUT_ON"
 |--] [--------] [-------------------------+
 |                                          |
 |  "AUTO"    "LIGA"          "DESLIGA"     |
 |--]/[--+----] [--+------------] [---------+---] [---( ) "BOMBA"
 |       | "BOMBA" |                           "EME"
 |       +----] [--+
```

**Exercício 2 — Alimentador por magazine**

```
NETWORK 1 – Inicialização
   "FirstScan"                      "ETAPA_0"
 |----] [------+-------------------( S )
                |                   "ETAPA_1"
                +-------------------( R )
                |                   "ETAPA_2"
                +-------------------( R )

NETWORK 2 – Peça no magazine (E4 tipo NA: 1 = há peça)
   "E4_PECA"                        "PECA"
 |----] [---------------------------( )

   Variante do item c (E4 tipo NF: 0 = há peça): troque o contato
   "E4_PECA"
 |----]/[---------------------------( ) "PECA"

NETWORK 3 – Emergência (EME pressionado)
   "EME"                            "EMERG"
 |----]/[---------+-----------------( S )
                  |                 "ETAPA_0"
                  +-----------------( R )
                  |                 "ETAPA_1"
                  +-----------------( R )
                  |                 "ETAPA_2"
                  +-----------------( R )

NETWORK 4 – Rearme: EME liberado e E1 solto
   "EMERG"   "EME"     "E1_START"             "EMERG"
 |----] [-----] [------]/[---------+---------( R )
                                   |         "ETAPA_0"
                                   +---------( S )

NETWORK 5 – Pulso de E1 (borda de subida)
   "E1_START"  "M_E1_ME"            "E1_PULSO"
 |----] P [---------------------------( )

NETWORK 6 – Contador de peças (vem antes das transições)
   "ETAPA_1"  "E2_FIM_CURSO"
 |----] [--------] [---------------[ "C_PECAS" CTU  CU ]
   "E3_RESET"                          PV := 10
 |----] [---------------------------[               R  ]

NETWORK 7 – Supervisão de 10 s do avanço
   "ETAPA_1"
 |----] [---------------------------[ "T_AVANCO" TON  IN  Q ]
                                        PT := T#10s

NETWORK 8 – Reconhece o alarme de tempo (E3)
   "E3_RESET"                       "ALM_TEMPO"
 |----] [---------------------------( R )

NETWORK 9 – Início do ciclo
   "ETAPA_0" "E1_PULSO" "PECA" "E2_FIM_CURSO" "C_PECAS".Q "ALM_TEMPO" "EME"      "ETAPA_1"
 |----] [------] [------] [-------]/[-----------]/[---------]/[--------] [-----+---( S )
                                                                              |   "ETAPA_0"
                                                                              +---( R )

NETWORK 10 – Cilindro chegou ao fim de curso
   "ETAPA_1"  "E2_FIM_CURSO"           "ETAPA_2"
 |----] [--------] [-----+-------------( S )
                         |             "ETAPA_1"
                         +-------------( R )

NETWORK 11 – Tempo de 10 s excedido: recua e aciona o alarme
   "ETAPA_1"  "T_AVANCO".Q             "ETAPA_0"
 |----] [--------] [-----+-------------( S )
                         |             "ETAPA_1"
                         +-------------( R )
                         |             "ALM_TEMPO"
                         +-------------( S )

NETWORK 12 – Cilindro recuou
   "ETAPA_2"  "E2_FIM_CURSO"           "ETAPA_0"
 |----] [--------]/[-----+-------------( S )
                         |             "ETAPA_2"
                         +-------------( R )

NETWORK 13 – S1: avanço do cilindro
   "ETAPA_1"    "EME"                  "S1_CILINDRO"
 |----] [---------] [------------------( )

NETWORK 14 – S2: alarme sem peça (E1 pressionado e magazine vazio)
   "ETAPA_0" "E1_START" "PECA"  "EME"  "S2_ALM_SEM_PECA"
 |----] [------] [------]/[-----] [-----( )

NETWORK 15 – S3: alarme de tempo excedido
   "ALM_TEMPO"                      "S3_ALM_TEMPO"
 |----] [---------------------------( )

NETWORK 16 – S4: cesto cheio (10 peças)
   "C_PECAS".Q                      "S4_ALM_CESTO"
 |----] [---------------------------( )
```

## 7. Observações e teste

- **Exercício 1.** O reset do temporizador acontece quando a condição de nível termina. Uma oscilação do nível com menos de 10 s não liga nem desliga a bomba.
- **Exercício 2.** O contador (network 6) vem antes das transições para contar na varredura em que o fim de curso é atingido.
- **Exercício 2.** Se o cilindro não chegar ao fim de curso em 10 s, ele recua, o alarme S3 é acionado e o ciclo fica bloqueado até E3.
- Os botões de emergência reais devem ser cabeados também em hardware (relé de segurança). O ladder não substitui a proteção exigida pela NR-12 e pela ISO 13850.
- **Teste no PLCSIM, exercício 1:**
  1. Automático: NIVEL_BAIXO em 1 por 10 s liga a bomba. NIVEL_ALTO em 1 por 10 s desliga.
  2. Manual: LIGA liga, DESLIGA desliga, e as boias não têm efeito.
  3. EME em 0 desliga a bomba em qualquer modo.
- **Teste no PLCSIM, exercício 2:**
  1. Com E4 em 1, pulse E1: o cilindro avança. Ligue E2 e depois desligue: ele recua e o contador sobe.
  2. Com E4 em 0, segure E1: S2 deve tocar.
  3. Não ligue E2 por 10 s: S3 deve acionar e o cilindro recuar.
  4. Dez ciclos: S4 aciona e o ciclo é bloqueado até pulsar E3.