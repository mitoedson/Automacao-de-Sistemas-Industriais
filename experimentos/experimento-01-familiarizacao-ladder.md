# Experimento 1 — Familiarização com o hardware, o TIA Portal e a lógica ladder básica

> **Laboratório de Automação — UFABC** · CLP Siemens S7-1500 (CPU 1516F-3 PN/DP)
> Baseado no *Roteiro de Prática de Laboratório — CLP* (Prof. Dr. Alexandre Acácio de Andrade, Everton F. O. de Almeida, Fabio G. de Freitas)

## 1. Objetivos

- Identificar os componentes da maleta didática (CPU, módulo de entradas digitais, módulo de saídas digitais, fonte, IHM).
- Criar um projeto no TIA Portal, configurar o endereço IP do computador e da CPU e fazer o *download* do programa.
- Converter funções booleanas em tabelas-verdade, simplificá-las e implementá-las em **ladder**.
- Implementar um **flip-flop tipo D** (liga/desliga alternado) a partir de uma única chave NA, usando detecção de borda.

## 2. Material

| Item | Descrição |
|---|---|
| Maleta CLP + IHM | CPU 1516F-3 PN/DP, DI 32x24 V DC, DQ 32x24 V DC/0,5 A, módulo analógico, IHM KTP400 Basic |
| Cabo de energia | Tomada 110–240 Vac (a maleta do CLP aceita toda a faixa) |
| Cabo Ethernet | Computador → porta **X1** da CPU |
| Computador | TIA Portal instalado |

> ⚠️ **Precauções:** conferir o cabeamento antes de energizar, ligar a maleta apenas depois de os cabos estarem conectados e nunca alterar ligações com o kit energizado.

## 3. Fundamentos

**Lógica ladder.** Contatos representam entradas (ou memórias) e bobinas representam saídas.

- Contatos em **série** → função **E** (AND).
- Contatos em **paralelo** → função **OU** (OR).
- Contato **NF** (`/`) → função **NÃO** (NOT).
- Instruções **SET** e **RESET** mantêm a saída acionada/desacionada após um pulso.
- **Detecção de borda** (`P_TRIG` / `R_TRIG`) gera um pulso de um ciclo de varredura quando o sinal passa de 0 para 1.

**Notação usada neste roteiro**

| Símbolo | Significado |
|---|---|
| `[ X ]` | contato normalmente aberto (NA) |
| `[/X]` | contato normalmente fechado (NF) |
| `‖` | ramo em paralelo |
| `→( Y )` | bobina de saída |

## 4. Preparação do ambiente

1. Ligar o cabo Ethernet entre o computador e a porta **X1** da CPU e energizar a maleta.
2. Configurar o IP do computador na mesma sub-rede da CPU (ex.: `192.168.0.x`, máscara `255.255.255.0`). **Não pode haver dois dispositivos com o mesmo IP.**
3. No TIA Portal: *Create new project* → *Add new device* → **CPU 1516-3 PN/DP**.
4. Configurar o endereço IP da CPU nas propriedades da interface PROFINET (`192.168.0.x`).
5. Compilar (*Compile*) e fazer o *Download to device*.
6. Usar **Go online** e **Monitor** para acompanhar o programa em execução.

> A apostila detalha esses passos nas seções *Configuração do endereço IP no dispositivo de programação*, *TIA Portal – Criação de um projeto* e *Configurar o endereço IP na CPU do CLP*.

## 5. Atividades

### 5.1 Tabelas-verdade e simplificação

Funções dadas (variáveis `A`, `B`, `C`, `D`; barra = negação):

- **a)** Q = A·B̄·C + A·B̄·C̄
- **b)** Q = A·(B + C) + B̄·(C̄ + D)
- **c)** Q = (A + B̄)·C + A·B·C̄ + ¬(B·C)

> 📝 **Atenção:** a função **b** usa a variável **D**, embora a tabela de endereçamento da apostila liste só A, B e C. Inclua **D** na tabela. Na função **c**, a leitura do último termo como ¬(B·C) vem da figura do PDF; confira no enunciado original.

**Tabela-verdade da função a)**

| A | B | C | Q |
|:-:|:-:|:-:|:-:|
| 0 | 0 | 0 | 0 |
| 0 | 0 | 1 | 0 |
| 0 | 1 | 0 | 0 |
| 0 | 1 | 1 | 0 |
| 1 | 0 | 0 | 1 |
| 1 | 0 | 1 | 1 |
| 1 | 1 | 0 | 0 |
| 1 | 1 | 1 | 0 |

Simplificação: A·B̄·(C + C̄) = **Q = A·B̄**. Sim, é simplificável.

**Tabela-verdade da função b)**

| A | B | C | D | Q |
|:-:|:-:|:-:|:-:|:-:|
| 0 | 0 | 0 | 0 | 1 |
| 0 | 0 | 0 | 1 | 1 |
| 0 | 0 | 1 | 0 | 0 |
| 0 | 0 | 1 | 1 | 1 |
| 0 | 1 | 0 | 0 | 0 |
| 0 | 1 | 0 | 1 | 0 |
| 0 | 1 | 1 | 0 | 0 |
| 0 | 1 | 1 | 1 | 0 |
| 1 | 0 | 0 | 0 | 1 |
| 1 | 0 | 0 | 1 | 1 |
| 1 | 0 | 1 | 0 | 1 |
| 1 | 0 | 1 | 1 | 1 |
| 1 | 1 | 0 | 0 | 1 |
| 1 | 1 | 0 | 1 | 1 |
| 1 | 1 | 1 | 0 | 1 |
| 1 | 1 | 1 | 1 | 1 |

Simplificação (mapa de Karnaugh): **Q = A + B̄·C̄ + B̄·D**.

**Tabela-verdade da função c)**

| A | B | C | Q |
|:-:|:-:|:-:|:-:|
| 0 | 0 | 0 | 1 |
| 0 | 0 | 1 | 1 |
| 0 | 1 | 0 | 1 |
| 0 | 1 | 1 | **0** |
| 1 | 0 | 0 | 1 |
| 1 | 0 | 1 | 1 |
| 1 | 1 | 0 | 1 |
| 1 | 1 | 1 | 1 |

Simplificação: a saída só é 0 quando A=0, B=1, C=1, portanto **Q = A + B̄ + C̄ = ¬(Ā·B·C)**.

### 5.2 Endereçamento das entradas e saídas

Uma proposta que permite observar as três funções ao mesmo tempo:

| Sinal | Tipo | Endereço | Dispositivo no kit |
|---|---|---|---|
| A | Entrada | `%I0.0` | chave/botão (conferir no kit) |
| B | Entrada | `%I0.1` | chave/botão |
| C | Entrada | `%I0.2` | chave/botão |
| D | Entrada | `%I0.3` | chave/botão (apenas função b) |
| Q_a | Saída | `%Q0.0` | LED |
| Q_b | Saída | `%Q0.1` | LED |
| Q_c | Saída | `%Q0.2` | LED |

> Confirme no seu kit qual chave/botão (S0…S4) está ligado a cada entrada, conforme a seção *Ligações das Réguas* da apostila.

### 5.3 Programa ladder

Implemente **a forma original** e **a forma simplificada** de cada função (em saídas diferentes) e compare os resultados na tabela-verdade.

```
Rede 1 — Função a (original)
  ( [ I0.0 ]·[/I0.1 ]·[ I0.2 ] ) ‖ ( [ I0.0 ]·[/I0.1 ]·[/I0.2 ] )   →( Q0.0 )

Rede 2 — Função a (simplificada)
  [ I0.0 ]·[/I0.1 ]                                                  →( Q0.3 )

Rede 3 — Função b (simplificada)
  [ I0.0 ] ‖ ( [/I0.1 ]·[/I0.2 ] ) ‖ ( [/I0.1 ]·[ I0.3 ] )          →( Q0.1 )

Rede 4 — Função c (simplificada)
  [ I0.0 ] ‖ [/I0.1 ] ‖ [/I0.2 ]                                     →( Q0.2 )
```

### 5.4 Download e verificação

1. Compilar sem erros e fazer o *download*.
2. Colocar a CPU em RUN e ativar o **Monitor** na rede.
3. Percorrer todas as combinações de entradas e preencher:

| Combinação testada | Saída esperada | Saída observada | OK? |
|---|---|---|:-:|
| | | | |

4. **Comentar o funcionamento** (o que aconteceu quando as formas original e simplificada foram comparadas).

### 5.5 Flip-flop tipo D (liga/desliga alternado)

**Especificação:** com uma única chave NA (`%I0.0`) acionar uma única saída (`%Q0.0`).

- 1º toque (não retentivo): a saída é **acionada**.
- 2º toque: a saída é **desacionada**.
- A cada toque o estado da saída se inverte.

**Ideia:** a chave deve ser tratada pela **borda de subida**, para que um toque longo não fique alternando a saída a cada ciclo de varredura.

| Endereço | Símbolo | Descrição |
|---|---|---|
| `%I0.0` | CHAVE | chave NA não retentiva |
| `%Q0.0` | SAIDA | saída a ser alternada |
| `%M10.0` | M_BORDA | memória de borda (parâmetro do `P_TRIG`) |
| `%M0.1` | PULSO | pulso de 1 ciclo a cada toque |
| `%M0.2` | LIGAR | solicitação de ligar |
| `%M0.3` | DESLIGAR | solicitação de desligar |

```
Rede 1 — Detecção de borda
  [ I0.0 ] → P_TRIG(CLK, M10.0) → ( M0.1 )

Rede 2 — Decide ligar
  [ M0.1 ]·[/Q0.0 ]                      →( M0.2 )

Rede 3 — Decide desligar
  [ M0.1 ]·[ Q0.0 ]                      →( M0.3 )

Rede 4 — Atua na saída
  [ M0.2 ]                               →( SET Q0.0 )
  [ M0.3 ]                               →( RESET Q0.0 )
```

> **Por que duas memórias intermediárias?** Se o SET e o RESET fossem avaliados diretamente com a condição do estado de `Q0.0` na mesma varredura, o segundo comando enxergaria o valor já modificado pelo primeiro e a saída ficaria presa em um estado. Separar a **decisão** (redes 2 e 3) da **ação** (rede 4) evita isso.

## 6. Critérios de verificação

- [ ] Projeto criado, CPU com IP correto e sem conflito de endereço.
- [ ] Download concluído e CPU em RUN.
- [ ] Todas as linhas das tabelas-verdade conferem com a saída física.
- [ ] A forma simplificada e a original produzem os mesmos resultados.
- [ ] O flip-flop alterna a saída em **cada toque**, mesmo com toque longo.

## 7. Questões para o relatório

1. Qual a vantagem de simplificar uma função booleana antes de programá-la no CLP?
2. Por que o flip-flop precisa de detecção de borda? O que acontece se o contato `[ I0.0 ]` for ligado direto na lógica?
3. Qual a diferença entre uma bobina simples `( )` e as instruções `SET`/`RESET`?
4. O que significa o LED `RUN/STOP` da CPU e o que os LEDs `ERROR` e `MAINT` indicam?

## 8. Entregáveis

- Tabelas-verdade preenchidas e simplificações demonstradas.
- Tabela de endereçamento.
- Capturas de tela do ladder (original, simplificado e flip-flop).
- Comentários do funcionamento observado em bancada.
