# Experimento 2 — Aprofundamento da lógica de programação e introdução à *force table*


## 1. Objetivos

- Traduzir problemas industriais simples em relações lógicas entre entradas e saídas.
- Programar os problemas em ladder, endereçando entradas e saídas.
- Nomear variáveis na tabela de *tags* do TIA Portal (`LSH01`, `LSH02`, `XEV1`, `XEV2`).
- Usar a **force table** para forçar entradas/saídas durante a depuração (*debug*).

## 2. Pré-requisitos

- Experimento 1 concluído (projeto, IP, download, monitoramento).
- Conhecimento de portas E, OU, NÃO e OU-exclusivo.

## 3. Problemas propostos

### 3.1 Problema A — Dispositivo de fixação de peças

Um atuador linear de **dupla ação** fixa peças em um molde. Ele **avança** mediante o acionamento de **dois botões (S1 ou S2)** e **retorna** quando os botões são desacionados.

| Sinal | Tipo | Endereço | Descrição |
|---|---|---|---|
| S1 | Entrada | `%I0.0` | botão 1 |
| S2 | Entrada | `%I0.1` | botão 2 |
| Y1 | Saída | `%Q0.0` | solenoide de avanço |
| Y2 | Saída | `%Q0.1` | solenoide de recuo (opcional, válvula de duas bobinas) |

**Equação lógica:** `Y1 = S1 + S2` e `Y2 = ¬Y1`.

```
Rede 1:  [ I0.0 ] ‖ [ I0.1 ]   →( Q0.0 )
Rede 2:  [/Q0.0 ]              →( Q0.1 )
```

> 💡 **Discussão de segurança:** o enunciado diz “S1 **ou** S2”. Em prensas e dispositivos de fixação reais, costuma-se exigir **S1 e S2** (comando bimanual) para manter as mãos do operador fora da zona de risco. Implemente a versão pedida e, depois, a versão com `E`, e comente a diferença.

### 3.2 Problema B — Tabela-verdade do OU-exclusivo

Verificar com dois botões e uma lâmpada a tabela-verdade do **XOR**.

| Sinal | Tipo | Endereço |
|---|---|---|
| B1 | Entrada | `%I0.2` |
| B2 | Entrada | `%I0.3` |
| L1 | Saída | `%Q0.2` |

| B1 | B2 | L1 |
|:-:|:-:|:-:|
| 0 | 0 | 0 |
| 0 | 1 | 1 |
| 1 | 0 | 1 |
| 1 | 1 | 0 |

**Equação lógica:** `L1 = B1·B̄2 + B̄1·B2`

```
Rede 3:  ( [ I0.2 ]·[/I0.3 ] ) ‖ ( [/I0.2 ]·[ I0.3 ] )   →( Q0.2 )
```

### 3.3 Problema C — Enchimento e esvaziamento de um tanque

Duas eletroválvulas controlam um tanque: **V1** (entrada) e **V2** (saída). O sensor **A** (nível máximo) envia sinal quando o tanque está cheio. O botão **B** comanda: **acionado = encher**, **desacionado = esvaziar**.

**Convenções do enunciado**

| Sinal | Valor | Significado |
|---|:-:|---|
| A = `LSH01` | 1 | Tanque cheio |
| A = `LSH01` | 0 | Tanque não cheio |
| B = `LSH02` | 1 | Encher |
| B = `LSH02` | 0 | Esvaziar |
| `XEV1` | 1 | Eletroválvula 1 **fechada** |
| `XEV1` | 0 | Eletroválvula 1 **aberta** |
| `XEV2` | 1 | Eletroválvula 2 **fechada** |
| `XEV2` | 0 | Eletroválvula 2 **aberta** |

> ⚠️ **Lógica invertida:** nas saídas, **1 = válvula fechada**. Monte a tabela com cuidado.

**Raciocínio:**

| A (cheio) | B (encher) | Situação | V1 (entrada) | V2 (saída) | XEV1 | XEV2 |
|:-:|:-:|---|---|---|:-:|:-:|
| 0 | 0 | esvaziar | fechada | aberta | 1 | 0 |
| 0 | 1 | encher | aberta | fechada | 0 | 1 |
| 1 | 0 | esvaziar | fechada | aberta | 1 | 0 |
| 1 | 1 | cheio, parar de encher | fechada | fechada | 1 | 1 |

**Equações (a partir da tabela):**

- `XEV1 = 0` apenas quando A=0 e B=1, portanto **XEV1 = A + B̄**.
- `XEV2 = 1` sempre que B=1, portanto **XEV2 = B**.

| Sinal | Tipo | Endereço |
|---|---|---|
| LSH01 (A) | Entrada | `%I0.4` |
| LSH02 (B) | Entrada | `%I0.5` |
| XEV1 | Saída | `%Q0.3` |
| XEV2 | Saída | `%Q0.4` |

```
Rede 4:  [ I0.4 ] ‖ [/I0.5 ]   →( Q0.3 )     // XEV1 = LSH01 + /LSH02
Rede 5:  [ I0.5 ]              →( Q0.4 )     // XEV2 = LSH02
```

## 4. Procedimento

1. Preencher a tabela de entradas e saídas de cada problema (modelo abaixo).
2. Escrever o ladder de cada solução em redes separadas.
3. Compilar, fazer o *download* e colocar a CPU em RUN.
4. Testar todas as combinações de entradas e comentar o funcionamento.
5. Nomear as variáveis na tabela de *tags* e usar a **force table**.

**Modelo de tabela de endereçamento**

| Problema | Entrada/Saída | Endereço | Nome (tag) | Descrição |
|---|---|---|---|---|
| | | | | |

## 5. *Force table* — passo a passo

A *force table* permite impor valores a entradas e saídas, **independentemente do estado físico**, para testar a lógica sem acionar o hardware real.

1. Na árvore do projeto: **PLC → Watch and force tables → Add new force table**.
2. Na coluna *Address*, informar o endereço **de periferia**, com sufixo `:P`, por exemplo `%I0.4:P` e `%I0.5:P` (para saídas, `%Q0.3:P`).
3. Ficar **online** com a CPU.
4. Escolher o valor em *Force value* e marcar a caixa de seleção da linha.
5. Clicar em **Start/replace forcing**.
6. Observar o efeito no programa e nas saídas.
7. **Ao final**, clicar em **Stop forcing of all selected addresses**.

> ⚠️ **Cuidados com o forçamento**
> - Um valor forçado **permanece ativo** enquanto o forçamento não for removido, mesmo que o botão físico seja solto.
> - Nunca force saídas ligadas a atuadores reais sem garantir que a operação é segura.
> - Sempre **desfaça os forçamentos** antes de encerrar a prática.

### Nomeando as variáveis

Na tabela de *tags* do CLP, renomear:

| Endereço | Nome |
|---|---|
| `%I0.4` | `LSH01` |
| `%I0.5` | `LSH02` |
| `%Q0.3` | `XEV1` |
| `%Q0.4` | `XEV2` |

> O item 5 da apostila é cortado no PDF (o item *iii* está incompleto). Sugestão para completar: **iii) remover o forçamento e verificar que a saída volta a obedecer à entrada física.**

## 6. Critérios de verificação

- [ ] A fixação avança com S1 ou S2 e recua quando ambos são soltos.
- [ ] A lâmpada do XOR acende somente com um botão acionado.
- [ ] No tanque, as quatro combinações de `LSH01/LSH02` produzem as válvulas da tabela do item 3.3.
- [ ] As variáveis aparecem no programa com os nomes pedidos.
- [ ] Todos os forçamentos foram removidos ao final.

## 7. Questões para o relatório

1. O que muda no comportamento do atuador se S1 e S2 forem ligados em série em vez de paralelo?
2. Por que as saídas `XEV1` e `XEV2` têm lógica invertida? Em que situação real isso é desejável (falha segura)?
3. Qual a diferença entre **forçar** (*force*) e **modificar** (*modify*) uma variável?
4. Por que o endereço de periferia (`:P`) é necessário para forçar entradas físicas?

## 8. Entregáveis

- Tabelas de endereçamento e tabelas-verdade preenchidas.
- Capturas do ladder e da *force table*.
- Comentários do funcionamento observado.
