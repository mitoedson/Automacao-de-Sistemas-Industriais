# Experimento 6 — Partida de motor trifásico: chave estrela-triângulo com inversão de sentido

> **Laboratório de Automação — UFABC** · CLP Siemens S7-1500 (CPU 1516F-3 PN/DP)

**Equipe:**
1. ______________________________
2. ______________________________
3. ______________________________
4. ______________________________

---

## 1. Objetivos

- Entender por que motores de indução trifásicos de maior potência não partem diretamente da rede.
- Programar o **sequenciamento de partida estrela-triângulo** com quatro contatores.
- Implementar **inversão de sentido** com parada obrigatória na troca.
- Aplicar **intertravamentos** de segurança em lógica e em hardware.

> ⚠️ **Antes de energizar o motor:** valide toda a lógica **com LEDs ou apenas com as saídas do CLP**, sem a potência ligada. Um erro de intertravamento causa **curto-circuito entre fases** e danos aos equipamentos.

## 2. Pré-requisitos

Experimentos 1 a 3 (ladder, `SET`/`RESET`, `TON`, bordas) e noções de contatores e motor trifásico.

## 3. Fundamentos

### 3.1 Por que não partir direto?

O motor de indução tipo **gaiola de esquilo** é robusto e confiável, mas, ao ser ligado diretamente à rede, absorve uma corrente de partida de **4 a 10 vezes** a nominal. Em motores de maior potência isso pode causar:

- **afundamentos de tensão** que perturbam outros equipamentos na mesma instalação;
- necessidade de **superdimensionar as proteções**, com aumento de custo;
- **multas** da concessionária, por violação dos limites de queda de tensão.

Por isso, em geral, motores acima de **5 cv** não partem diretamente e usam partida indireta (eletromecânica ou *soft-starter*).

### 3.2 Partida estrela-triângulo

1. **Estrela (Y):** cada bobina recebe `V_linha / √3` (≈ 0,577 × V_linha). Ex.: rede de 220 V → 127 V por bobina. A corrente de partida cai (≈ 1/3 do valor em triângulo).
2. Quando o motor atinge aproximadamente a rotação nominal, as bobinas são reconfiguradas para **triângulo (Δ)** e passam a receber a tensão nominal de 220 V.

### 3.3 Contatores usados

| Contator | Função | Saída do CLP |
|:-:|---|---|
| **A** | Linha — sentido **normal** | `%Q0.0` |
| **B** | Linha — sentido **inverso** | `%Q0.1` |
| **C** | Fechamento em **estrela** | `%Q0.2` |
| **D** | Fechamento em **triângulo** | `%Q0.3` |

> 🚫 **Nunca** acionar **A e B** juntos (curto entre fases) nem **C e D** juntos (curto no fechamento do enrolamento).

> 📝 O texto da apostila indica que “A, C e D mudam de triângulo para estrela e B inverte o sentido”. O **diagrama trajeto-passo** (item 5) define o papel de cada contator de forma consistente: **A = sentido normal, B = sentido inverso**. Confira com a figura 2 e com a ligação física do seu kit.

## 4. Descrição do problema de automação

- O motor é acionado pelo botão **LIGA** e pode ser desligado a qualquer instante pelo botão **DESLIGA**.
- Uma **chave de sentido** define a rotação: **1 = normal**, **0 = inverso**.
- Se o sentido for **alterado com o motor em funcionamento**, o motor deve **parar**.
- Em qualquer sentido, o motor parte em **estrela** e, após **3 s**, passa para **triângulo**.

### 4.1 Entradas e saídas

| TAG | Tipo | Endereço | Descrição |
|---|---|---|---|
| `LIGA` | Entrada | `%I0.0` | Botão LIGA (NA, não retentivo) |
| `DESLIGA` | Entrada | `%I0.1` | Botão DESLIGA (NF: 0 quando pressionado) |
| `SENTIDO` | Entrada | `%I0.2` | Chave de sentido (1 = normal; 0 = inverso) |
| `CONT_A` | Saída | `%Q0.0` | Contator A (normal) |
| `CONT_B` | Saída | `%Q0.1` | Contator B (inverso) |
| `CONT_C` | Saída | `%Q0.2` | Contator C (estrela) |
| `CONT_D` | Saída | `%Q0.3` | Contator D (triângulo) |

> Se o botão DESLIGA do seu kit for NA, troque o contato `[/I0.1]` por `[ I0.1 ]` nas redes abaixo. Prefira **NF** (falha segura).

**Auxiliares:** `RODANDO` (`%M1.0`), `SENT_ANT` (`%M1.1`), `TROCA` (`%M1.2`), `T_ESTRELA` (`TON`, 3 s), `T_MORTO` (`TON`, 100 ms).

## 5. Diagrama trajeto-passo

| Etapa | 1 | 2 | 3 | 4 | 5 | 6 | 7 | 8 |
|---|:-:|:-:|:-:|:-:|:-:|:-:|:-:|:-:|
| Contator A | 1 | T=3 s | 1 | T=3 s | 0 | 0 | T=3 s | 0 |
| Contator B | 0 | | 0 | | 0 | 1 | | 1 |
| Contator C | 1 | | 0 | | 0 | 1 | | 0 |
| Contator D | 0 | | 1 | | 0 | 0 | | 1 |

| Etapa | Descrição |
|:-:|---|
| 1 | Sentido normal (A=1, B=0): parte em **estrela** |
| 2 | Espera de **3 s** (motor atinge pelo menos 90 % da rotação nominal) |
| 3 | Sentido normal: chaveia para **triângulo** |
| 4 | Tempo de **3 s** (descrito assim na apostila; o motor já está em regime, em triângulo, até o desligamento ou a troca de sentido) |
| 5 | Motor **desligado** (botão DESLIGA ou mudança de sentido) |
| 6 | Sentido inverso (A=0, B=1): parte em **estrela** |
| 7 | Espera de **3 s** |
| 8 | Sentido inverso: chaveia para **triângulo** |

## 6. Solução de referência

```
Rede 1 — Memória do sentido anterior e detecção de troca
  ( [ I0.2 ]·[/M1.1 ] ) ‖ ( [/I0.2 ]·[ M1.1 ] )             →( M1.2 )   // TROCA (sentido mudou)
  [ I0.2 ]                                                  →( M1.1 )   // SENT_ANT := SENTIDO

Rede 2 — Estado "rodando" (desligar domina sobre ligar)
  [ I0.0 ]·[/M1.2 ]                                         →( SET   M1.0 )
  [/I0.1 ] ‖ [ M1.2 ]                                       →( RESET M1.0 )

Rede 3 — Temporizador da fase estrela
  [ M1.0 ]                  → TON T_ESTRELA ( IN, PT = T#3s )

Rede 4 — Tempo morto entre estrela e triângulo
  [ T_ESTRELA.Q ]·[ M1.0 ]  → TON T_MORTO   ( IN, PT = T#100ms )

Rede 5 — Contatores de linha (sentido)
  [ M1.0 ]·[ I0.2 ]·[/Q0.1 ]                                →( Q0.0 )   // A: normal
  [ M1.0 ]·[/I0.2 ]·[/Q0.0 ]                                →( Q0.1 )   // B: inverso

Rede 6 — Estrela (partida)
  [ M1.0 ]·[/T_ESTRELA.Q ]·[/Q0.3 ]                         →( Q0.2 )   // C

Rede 7 — Triângulo (regime)
  [ M1.0 ]·[ T_MORTO.Q ]·[/Q0.2 ]                           →( Q0.3 )   // D
```

> **Por que o motor para na troca de sentido?** A rede 1 detecta a mudança da chave (XOR com o valor anterior). A rede 2 então derruba `M1.0`. Para voltar a rodar, é necessário **pressionar LIGA** de novo.

> **Por que um “tempo morto” de 100 ms?** Entre abrir C e fechar D, convém um pequeno intervalo para o arco do contator C se extinguir. Sem isso, os dois contatores poderiam coexistir por alguns milissegundos durante a troca. A apostila usa transição direta; o tempo morto é uma boa prática adicional.

> **Intertravamento duplo.** Os contatos `[/Q0.1]`, `[/Q0.0]`, `[/Q0.3]` e `[/Q0.2]` impedem A+B e C+D **na lógica**. Em instalações reais, use também **intertravamento por contato auxiliar NF** no próprio contator (hardware).

### 6.1 Sobre as saídas do CLP

O módulo de saídas é de **24 V DC / 0,5 A**. Verifique no kit como as bobinas dos contatores são comandadas (diretamente ou por relés de interface) **antes** de ligar.

## 7. Procedimento

1. Montar o programa (redes 1 a 7), compilar e fazer o *download*.
2. **Teste a seco (sem potência):** observe os LEDs das saídas `Q0.0` a `Q0.3` e confira a sequência.
3. Só depois de validar a sequência, **peça ao professor/técnico** para liberar a potência.
4. Ligar a maleta com o motor e testar nos dois sentidos.

### 7.1 Testes

| # | Ação | Resultado esperado |
|---|---|---|
| 1 | `SENTIDO=1`, LIGA | A e C ligam; após 3 s, C desliga e D liga (A mantido) |
| 2 | DESLIGA | todos desligam |
| 3 | `SENTIDO=0`, LIGA | B e C ligam; após 3 s, C desliga e D liga (B mantido) |
| 4 | Trocar `SENTIDO` com o motor rodando | todos desligam; é preciso pressionar LIGA para religar |
| 5 | Verificar na rede se A e B (ou C e D) ficam 1 ao mesmo tempo | nunca devem coincidir |
| 6 | Observar o tempo entre C e D | ≈ 3 s + 100 ms |

## 8. Questões para o relatório

1. Por que a corrente de partida de um motor de indução é de 4 a 10 vezes a nominal?
2. Em estrela, qual a tensão em cada bobina para uma rede de 220 V? E o torque de partida em relação à ligação direta em triângulo?
3. Por que é necessário esperar o motor chegar a ~90 % da rotação nominal antes do chaveamento?
4. Que riscos há se A e B forem acionados simultaneamente? E C e D?
5. Cite outras alternativas à partida estrela-triângulo e quando preferi-las.

## 9. Entregáveis

- Ladder comentado (redes 1 a 7) e tabela de endereços.
- Diagrama trajeto-passo validado com as saídas observadas.
- Resultados dos testes e respostas às questões.
