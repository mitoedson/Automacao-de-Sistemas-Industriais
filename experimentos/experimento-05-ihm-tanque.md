# Experimento 5 — Introdução da IHM: supervisão e controle do tanque

> **Laboratório de Automação — UFABC** · CLP Siemens S7-1500 (CPU 1516F-3 PN/DP) + IHM SIMATIC KTP400 Basic

**Equipe:**
1. ______________________________
2. ______________________________
3. ______________________________
4. ______________________________

---

## 1. Objetivos

- Adicionar e configurar a IHM KTP400 Basic no projeto do TIA Portal e conectá-la ao CLP.
- Criar telas com **indicação de nível**, **seleção de modo**, **campos de entrada de valores** e **botões**.
- **Simular** o nível de um tanque com uma memória analógica.
- Parametrizar por meio da IHM os **atrasos** (em segundos) da lógica da bomba.

## 2. Pré-requisitos

- Experimento 3 concluído (lógica de bomba manual/automático com `TON`).
- Leitura das seções da apostila: *IHM detalhamento*, *Verificação do IP da IHM*, *Atribuição do IP na IHM*, *IHM configuração inicial* e *Conexões com o CLP*.

## 3. Especificação

A partir do problema do **Experimento 3 (bomba)**, criar uma interface na IHM em que seja possível:

1. **Monitorar** o nível do tanque.
2. **Selecionar** o modo **automático** ou **manual**.
3. **Especificar o atraso, em segundos,** para a bomba ligar e para desligar no modo automático.
4. No modo **manual**, exibir os botões **LIGA** e **DESLIGA**.
5. Usar uma **memória analógica** (`MW15`) para **simular** o nível do tanque.

### 3.1 Endereços de referência (do enunciado)

| Endereço | Descrição |
|---|---|
| `%I0.0` | 1 se NÍVEL < NÍVEL BAIXO |
| `%I0.1` | 1 se NÍVEL > NÍVEL ALTO |
| `%I0.2` | 1 = AUTOMÁTICO; 0 = MANUAL |
| `%I0.3` | 1 se botão LIGA pressionado |
| `%I0.4` | 0 se botão DESLIGA pressionado |
| `%MW15` | Simulação do nível do tanque |
| `%Q0.1` | 1 = BOMBA LIGADA |

> Neste experimento o nível vem da **simulação (`MW15`)**. Por isso, as indicações “nível baixo” e “nível alto” passam a ser **derivadas por comparação** e não mais lidas de boias físicas. A chave `I0.2` e os botões físicos podem ser mantidos como alternativa, mas **a IHM será a fonte do modo e dos comandos**.

## 4. Variáveis do projeto

### 4.1 Tabela de *tags* do CLP

| Tag | Tipo | Endereço | Função |
|---|---|---|---|
| `Nivel_Tanque` | Int | `%MW15` | Nível simulado (0 a 100 %) |
| `Atraso_Liga_s` | Int | `%MW20` | Atraso para ligar (s), ajustado na IHM |
| `Atraso_Desl_s` | Int | `%MW22` | Atraso para desligar (s), ajustado na IHM |
| `Modo_Auto` | Bool | `%M30.0` | 1 = automático, 0 = manual (IHM) |
| `Btn_Liga` | Bool | `%M30.1` | Botão LIGA na IHM |
| `Btn_Desl` | Bool | `%M30.2` | Botão DESLIGA na IHM |
| `Nivel_Baixo` | Bool | `%M30.3` | `Nivel_Tanque < 20` |
| `Nivel_Alto` | Bool | `%M30.4` | `Nivel_Tanque > 80` |
| `Bomba_On` | Bool | `%M30.5` | Estado retido da bomba |
| `Bomba` | Bool | `%Q0.1` | Saída da bomba |

> 💡 Os bits de IHM usam o byte `%MB30` para **não sobrepor** as palavras `%MW20` e `%MW22` (um `MW20` ocupa `MB20` e `MB21`).
> 💡 `%MW15` ocupa `MB15` e `MB16`.

**Memória de relógio:** em *Propriedades da CPU → System and clock memory*, habilite o **Clock memory byte** (por exemplo `%MB0`). O bit **`%M0.5` pisca a 1 Hz** e será usado para a simulação do nível.

### 4.2 Limiares e simulação

| Parâmetro | Valor sugerido |
|---|---|
| Nível baixo | < 20 % |
| Nível alto | > 80 % |
| Taxa de enchimento (bomba ligada) | +5 % por segundo |
| Taxa de consumo (bomba desligada) | −2 % por segundo |

## 5. Programa do CLP

```
Rede 1 — Simulação do nível (a cada segundo, na borda de subida de M0.5)
  [ M0.5 ] → P_TRIG → ( M31.0 )                                  // pulso de 1 s

  [ M31.0 ]·[ Q0.1 ]·[ Nivel_Tanque < 100 ]   → ADD ( Nivel_Tanque + 5 )  → Nivel_Tanque
  [ M31.0 ]·[/Q0.1 ]·[ Nivel_Tanque > 0   ]   → SUB ( Nivel_Tanque - 2 )  → Nivel_Tanque

Rede 2 — Limitação
  [ Nivel_Tanque > 100 ] → MOVE 100 → Nivel_Tanque
  [ Nivel_Tanque <   0 ] → MOVE   0 → Nivel_Tanque

Rede 3 — Indicações de nível
  [ Nivel_Tanque < 20 ]                       →( Nivel_Baixo )
  [ Nivel_Tanque > 80 ]                       →( Nivel_Alto  )

Rede 4 — Temporizadores com atraso configurável (modo automático)
  [ Modo_Auto ]·[ Nivel_Baixo ] → TON T_LIGA ( PT = DINT_TO_TIME( INT_TO_DINT(Atraso_Liga_s) * 1000 ) )
  [ Modo_Auto ]·[ Nivel_Alto  ] → TON T_DESL ( PT = DINT_TO_TIME( INT_TO_DINT(Atraso_Desl_s) * 1000 ) )

Rede 5 — Estado da bomba
  ( [/Modo_Auto ]·[ Btn_Liga ] ) ‖ [ T_LIGA.Q ]       →( SET   Bomba_On )
  ( [/Modo_Auto ]·[ Btn_Desl ] ) ‖ [ T_DESL.Q ]       →( RESET Bomba_On )

Rede 6 — Saída
  [ Bomba_On ]                                        →( Bomba )
```

> **Conversão para `TIME`:** o `PT` do `TON` é do tipo `Time` (milissegundos). Converta o valor inteiro em segundos com `CONVERT` / `DINT_TO_TIME`, multiplicando por 1000. Tratar o caso de **atraso = 0** (tempo nulo) como “sem atraso”.

**Recomendações**

- Limite os campos da IHM (por exemplo, atraso entre **0 e 60 s**) para evitar valores absurdos ou negativos.
- Garanta `Nivel_Tanque` sempre entre 0 e 100 (rede 2).
- Os botões da IHM devem ser do tipo **pulso** (ativados enquanto pressionados).

## 6. Configuração da IHM

### 6.1 Preparação

1. No TIA Portal: **Add new device → HMI → SIMATIC Basic Panel → 4" Display → KTP400 Basic PN** (selecione a versão idêntica à do painel).
2. Atribuir o **IP da IHM** na mesma sub-rede do CLP e do computador (ex.: `192.168.0.x`).
3. Criar a **conexão** CLP ↔ IHM (*Devices & networks → Connections → HMI connection*) ou pelo assistente de configuração inicial.
4. Importar as *tags* do CLP para a lista de *HMI tags*.

### 6.2 Tela principal (sugestão de layout)

```
┌─────────────────────────────────────────┐
│  TANQUE – CONTROLE DA BOMBA             │
├─────────────────────────────────────────┤
│  Nível:  [██████░░░░]  [  65 ] %        │   ← Barra + campo de saída
│                                         │
│  Modo:   [ MANUAL | AUTOMÁTICO ]        │   ← Chave (Switch)
│                                         │
│  Atraso p/ ligar   (s): [  10 ]         │   ← Campo E/S
│  Atraso p/ desligar(s): [  10 ]         │   ← Campo E/S
│                                         │
│  [ LIGA ]   [ DESLIGA ]  ← só no manual │   ← Botões (visibilidade)
│                                         │
│  Bomba:  ● LIGADA / ○ DESLIGADA         │   ← Indicador
└─────────────────────────────────────────┘
```

| Objeto | Tipo | Tag associada | Observações |
|---|---|---|---|
| Nível (barra) | *Bar* | `Nivel_Tanque` | Escala 0–100 |
| Nível (valor) | *I/O field* (saída) | `Nivel_Tanque` | formato `999` |
| Modo | *Switch* | `Modo_Auto` | rótulos “MANUAL / AUTO” |
| Atraso ligar | *I/O field* (entrada) | `Atraso_Liga_s` | limite 0–60 |
| Atraso desligar | *I/O field* (entrada) | `Atraso_Desl_s` | limite 0–60 |
| LIGA | *Button* | `Btn_Liga` | evento *Press* → `SetBit`, *Release* → `ResetBit`; **visível só em manual** |
| DESLIGA | *Button* | `Btn_Desl` | idem |
| Estado da bomba | *Circle/Text list* | `Bomba` | verde = ligada |

> **Visibilidade dos botões:** na janela *Animations → Visibility* de cada botão, associe a tag `Modo_Auto` e mostre o botão **apenas quando `Modo_Auto = 0`**.

> O **KTP400 Basic** possui recursos gráficos limitados (por exemplo, sem *trends* avançados). Use barras, campos E/S, botões e indicadores simples.

### 6.3 Download

1. **Compile** o CLP e a IHM sem erros.
2. Faça o *download* para o CLP e, depois, para a IHM (com o IP da IHM acessível pela rede).
3. Confirme a comunicação (sem mensagens de “conexão perdida” na IHM).

## 7. Roteiro de testes

| # | Ação | Resultado esperado |
|---|---|---|
| 1 | Iniciar com nível 0 % | `Nivel_Baixo` = 1 |
| 2 | Modo **manual**, tocar LIGA | bomba liga, o nível sobe a +5 %/s |
| 3 | Manual, tocar DESLIGA | bomba desliga, o nível cai a −2 %/s |
| 4 | Manual com nível alto | nada muda (a lógica ignora o nível) |
| 5 | Modo **automático**, atraso de ligar = 5 s | bomba liga 5 s após `Nivel_Baixo` |
| 6 | Automático, nível > 80 %, atraso de desligar = 8 s | bomba desliga 8 s após `Nivel_Alto` |
| 7 | Trocar para manual com a bomba ligada | os botões LIGA/DESLIGA aparecem; a bomba mantém o estado |

## 8. Questões para o relatório

1. Por que usar `%M30.x` em vez de `%I`/`%Q` para os comandos da IHM?
2. O que acontece com a simulação do nível se `Nivel_Tanque` não for limitado a 0–100?
3. Quais problemas surgem se o campo de atraso aceitar valores negativos?
4. Como substituir a simulação por uma **entrada analógica real** (sensor de 0–10 V ou 4–20 mA) no futuro?

## 9. Entregáveis

- Capturas das telas da IHM (cada modo).
- Ladder comentado e tabela de *tags*.
- Resultados do roteiro de testes.
