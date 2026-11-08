# Experimento 8 – Abastecimento automático de água

Lógica ladder para o controle de duas bombas de recalque que levam água de uma caixa subterrânea (50 mil litros) a uma caixa de cobertura (600 litros), em modo automático e manual.

> 8º Trabalho de Laboratório · Universidade Federal do ABC (UFABC) · Laboratório de CLP

## Acesso rápido

| Recurso | Link |
|---|---|
| Simulador interativo | [Abrir simulador](https://claude.ai/artifact/WE2xdPKxNYCQGXp7Gv9yPM) |
| Manual do usuário (PDF) | [Abrir manual](https://claude.ai/artifact/7qQGpQKxHW3chWYfp37Qpy) |

> Os links acima são privados até serem compartilhados pelo botão *Share* do Claude. Para um acesso público e permanente, hospede o simulador no GitHub Pages e o PDF na pasta do repositório (veja a seção "Publicação").

## 1. Regras do processo

| Nº | Regra |
|---|---|
| R1 | Se `LSL1_BAIXO = 0`, a bomba liga e permanece ligada até `LSH1_ALTO = 1`. |
| R2 | Apenas uma bomba funciona por vez. A seletora define qual. |
| R3 | Se `LSL2_BAIXO = 0`, as bombas desligam. Só ligam 10 s depois de `LSL2_BAIXO = 1`. |
| R4 | `AUT_MAN = 1` é automático. `AUT_MAN = 0` é manual. |
| R5 | Em manual, as bombas funcionam por botoeiras não retentivas, só enquanto pressionadas. |

As boias valem 1 quando cobertas de água. A caixa subterrânea tem boia mecânica que a mantém cheia. As duas bombas ficam em tubulações paralelas, para manutenção.

## 2. Interpretações adotadas

1. A seletora vale nos dois modos e define qual bomba está habilitada.
2. Em manual, a botoeira confirma a bomba selecionada. `LIGA_A` só liga a A com a seletora na A, e `LIGA_B` só liga a B com a seletora na B.
3. A seletora é uma chave com dois contatos (A, centro, B). Com nenhum ou os dois sinais ativos, nenhuma bomba liga.
4. Em manual, `LSH1_ALTO = 1` bloqueia a botoeira (proteção contra transbordo, opcional).
5. O permissivo anti-cavitação vale em AUT e em MAN.

Confirme com o professor se alguma dessas escolhas precisa mudar.

## 3. Lista de IO

Endereços sugeridos no padrão IEC. Ajuste ao módulo do CLP do laboratório.

| Tipo | Endereço | Tag | Descrição |
|---|---|---|---|
| Entrada | %IX0.0 | `AUT_MAN` | Seletora de campo (AUT = 1, MAN = 0) |
| Entrada | %IX0.1 | `SEL_BOMBA_A` | Seletora de bomba, posição A |
| Entrada | %IX0.2 | `SEL_BOMBA_B` | Seletora de bomba, posição B |
| Entrada | %IX0.3 | `LIGA_A` | Botoeira NA não retentiva, bomba A |
| Entrada | %IX0.4 | `LIGA_B` | Botoeira NA não retentiva, bomba B |
| Entrada | %IX0.5 | `LSL1_BAIXO` | Boia de nível baixo, caixa superior |
| Entrada | %IX0.6 | `LSH1_ALTO` | Boia de nível alto, caixa superior |
| Entrada | %IX0.7 | `LSL2_BAIXO` | Boia de nível mínimo, caixa inferior |
| Saída | %QX0.0 | `BOMBA_A` | Contator da bomba 1 |
| Saída | %QX0.1 | `BOMBA_B` | Contator da bomba 2 |
| Memória | – | `ATIVA_NIVEL` | Pedido de água, com retenção |
| Memória | – | `SEL_A_OK` | Bomba A selecionada e válida |
| Memória | – | `SEL_B_OK` | Bomba B selecionada e válida |
| Timer | – | `TON_0` | TON, PT = T#10S |

## 4. Lógica

### Equações

```
TON_0.IN    = LSL2_BAIXO                  (PT = T#10S)
ATIVA_NIVEL = ( /LSL1_BAIXO + ATIVA_NIVEL ) · /LSH1_ALTO
SEL_A_OK    = SEL_BOMBA_A · /SEL_BOMBA_B
SEL_B_OK    = SEL_BOMBA_B · /SEL_BOMBA_A
BOMBA_A     = TON_0.Q · SEL_A_OK · /BOMBA_B · [ AUT_MAN·ATIVA_NIVEL + /AUT_MAN·LIGA_A·/LSH1_ALTO ]
BOMBA_B     = TON_0.Q · SEL_B_OK · /BOMBA_A · [ AUT_MAN·ATIVA_NIVEL + /AUT_MAN·LIGA_B·/LSH1_ALTO ]
```

Legenda: `·` é E (série), `+` é OU (paralelo), `/` é contato normalmente fechado.

### Ladder

```
RUNG 1 - Permissivo anti-cavitação
 |--[ LSL2_BAIXO ]--[ TON_0  IN / PT=T#10S ]--|

RUNG 2 - Controle de nível da caixa superior
 |--+--[/ LSL1_BAIXO ]--+--[/ LSH1_ALTO ]--------( ATIVA_NIVEL )--|
 |  |                   |
 |  +--[ ATIVA_NIVEL ]--+

RUNG 3 - Seleção da bomba A
 |--[ SEL_BOMBA_A ]--[/ SEL_BOMBA_B ]-------------( SEL_A_OK )----|

RUNG 4 - Seleção da bomba B
 |--[ SEL_BOMBA_B ]--[/ SEL_BOMBA_A ]-------------( SEL_B_OK )----|

RUNG 5 - Bomba A
 |--[ TON_0.Q ]--[ SEL_A_OK ]--[/ BOMBA_B ]--+--[ AUT_MAN ]--[ ATIVA_NIVEL ]----------------+--( BOMBA_A )--|
 |                                            |                                               |
 |                                            +--[/ AUT_MAN ]--[ LIGA_A ]--[/ LSH1_ALTO ]-----+

RUNG 6 - Bomba B
 |--[ TON_0.Q ]--[ SEL_B_OK ]--[/ BOMBA_A ]--+--[ AUT_MAN ]--[ ATIVA_NIVEL ]----------------+--( BOMBA_B )--|
 |                                            |                                               |
 |                                            +--[/ AUT_MAN ]--[ LIGA_B ]--[/ LSH1_ALTO ]-----+
```

### Por que funciona

- **Rung 1:** se `LSL2_BAIXO` cai, o timer zera e `TON_0.Q` desliga as bombas. Ao voltar, conta 10 s antes de liberar (R3).
- **Rung 2:** selo com histerese. Liga em `LSL1_BAIXO = 0` e só desliga com `LSH1_ALTO = 1` (R1).
- **Rungs 3 e 4:** só são verdadeiras com exatamente uma posição da seletora acionada (R2).
- **Rungs 5 e 6:** o ramo superior é o automático e o inferior é o manual. O contato `/BOMBA_B` na A e `/BOMBA_A` na B é um intertravamento extra.

## 5. Montagem passo a passo

1. Crie o projeto no software do CLP (por exemplo, `Exp8_Abastecimento`).
2. Declare as variáveis da lista de IO, com os endereços do seu módulo, além de `ATIVA_NIVEL`, `SEL_A_OK`, `SEL_B_OK` (BOOL) e `TON_0` (TON).
3. Crie os rungs 1 a 6 na ordem acima. O timer precisa vir antes do uso de `TON_0.Q`.
4. Adicione comentários curtos em cada rung.
5. Compile e corrija erros de variável ou de endereço.
6. Valide no simulador do software ou no simulador HTML.
7. Carregue no CLP, coloque em RUN e execute os testes da seção 6.

## 6. Roteiro de testes

Condição inicial: `LSL2_BAIXO = 1` e os 10 s do timer já decorridos, salvo indicação.

| Nº | Condição | Ação | Resultado esperado |
|---|---|---|---|
| T1 | AUT, seletora A | Nível baixo (`LSL1 = 0`, `LSH1 = 0`) | `ATIVA_NIVEL = 1`, `BOMBA_A = 1` |
| T2 | Continuação do T1 | Nível médio (`LSL1 = 1`, `LSH1 = 0`) | `BOMBA_A` continua em 1 |
| T3 | Continuação do T2 | Nível cheio (`LSL1 = 1`, `LSH1 = 1`) | `ATIVA_NIVEL = 0`, `BOMBA_A = 0` |
| T4 | AUT, seletora B | Nível baixo | `BOMBA_B = 1`, `BOMBA_A = 0` |
| T5 | AUT, `BOMBA_A` ligada | Trocar seletora de A para B | A desliga, B assume |
| T6 | AUT, bomba ligada | `LSL2_BAIXO = 0` | Bombas desligam, timer zera |
| T7 | Continuação do T6 | `LSL2_BAIXO = 1` | Bombas só voltam após 10 s |
| T8 | MAN, seletora A, nível médio | Segurar `LIGA_A` | `BOMBA_A = 1` enquanto pressionado |
| T9 | MAN, seletora A, nível médio | Segurar `LIGA_B` | Nada acontece |
| T10 | MAN, seletora B, nível médio | Segurar `LIGA_B` | `BOMBA_B = 1` enquanto pressionado |
| T11 | MAN, seletora A, nível cheio | Segurar `LIGA_A` | Nada acontece |
| T12 | MAN, seletora A | Segurar `LIGA_A` e `LIGA_B` | Só `BOMBA_A = 1` |
| T13 | Qualquer modo | `SEL_BOMBA_A = 1` e `SEL_BOMBA_B = 1` | Nenhuma bomba liga |
| T14 | Qualquer modo | `SEL_BOMBA_A = 0` e `SEL_BOMBA_B = 0` | Nenhuma bomba liga |
| T15 | Bomba ligada em AUT | Trocar para MAN sem apertar botoeira | Bomba desliga e não parte sozinha |

## 7. Problemas comuns

| Sintoma | Causa provável | Solução |
|---|---|---|
| A bomba nunca liga | `TON_0.Q` em 0 | Verifique `LSL2_BAIXO = 1` e aguarde 10 s |
| A bomba liga e desliga logo | `ATIVA_NIVEL` sem selo | Confira o contato em paralelo no Rung 2 |
| Nenhuma bomba liga em AUT | Seletora em posição inválida | Só um entre `SEL_BOMBA_A` e `SEL_BOMBA_B` pode estar em 1 |
| Manual não responde com caixa cheia | Proteção `/LSH1_ALTO` | Comportamento esperado. Remova o contato se o professor exigir |

## Material complementar

**Guia prático: abastecimento de água**
([baixar o PDF](Manual_Experimento8.pdf)) · **[Abrir o simulador](https://mitoedson.github.io/Automacao-de-Sistemas-Industriais/simulador/simulacao_abastecimento_de_agua.html)**


## Licença

MIT.