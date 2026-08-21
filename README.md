# Automacao-de-Sistemas-Industriais
Estes documentos constituem um guia didático abrangente sobre controladores lógicos programáveis (CLP), focando em sua aplicação na automação industrial. O material detalha desde a evolução histórica desses dispositivos até sua estrutura interna, explicando o funcionamento de componentes como a CPU, memórias e fontes de alimentação.

**Docente:** Alexandre Acácio de Andrade
**Livro-texto de referência (teoria):** Mikell P. Groover, *Automation, Production Systems, and Computer-Integrated Manufacturing*, 5ª edição, Pearson, 2016.
**Apostila de referência (prática de laboratório):** *Roteiro — Prática de Laboratório de Automação: CLP — Controladores Lógicos Programáveis*, UFABC (Alexandre Acácio de Andrade, Everton Flavio Oliveira de Almeida, Fabio Gomes de Freitas), Draft V8.

> Este roteiro cruza o cronograma oficial do Plano de Ensino com os capítulos do livro do Groover (base teórica) e com a apostila de laboratório (base prática, com o CLP Siemens S7-1500). Quando o tema da semana não tem correspondência direta em nenhum dos dois materiais, isso está sinalizado.

Este roteiro segue **estritamente o cronograma semanal do plano de ensino** — a tabela abaixo não inclui os capítulos 3, 7 e 8, nem tópicos da ementa (planejamento da produção, escalonamento, ISO 50001 etc.) que não aparecem no cronograma das 9 semanas de conteúdo novo.


## Mapa Semana → Materiais

| Semana | Data | Tema (Plano de Ensino) | Groover (teoria) | Apostila de Laboratório (prática) |
|---|---|---|---|---|
| 1 | 11/02 | Apresentação da disciplina; Automação Industrial e objetivos | **Cap. 1**: 1.1 Production Systems; 1.2 Automation in Production Systems | — |
| 2 | 18/02 | Automação em sistemas de produção; trabalho manual; princípios e estratégias | **Cap. 1** (1.3–1.4) + **Cap. 4** (4.1–4.3) | — |
| 3 | 25/02 | Setores de produção e produtos | **Cap. 2**: 2.1, 2.2, 2.4 | — |
| 4 | 11/03 | Sensores e Atuadores | **Cap. 6**: 6.1 Sensors; 6.2 Actuators; 6.3 Analog–Digital Conversions | Apostila: seção de **Entradas e Saídas / Ligações das Réguas** (p.12) e **entrada analógica** (potenciômetro 10V, faixa 0–10V → 0–27648) — ótimo complemento prático da conversão A/D do Cap. 6.3 |
| 5 | 18/03 | Process Automation Controller – 1 | **Cap. 5**: 5.1–5.3 Computer Process Control | Apostila: **Arquitetura de Hardware do CLP Siemens S7-1500** (p.4–8) — CPU, painel frontal, LEDs de falha, display |
| 6 | 25/03 | Process Automation Controller – 2 | **Cap. 9**: 9.1 Discrete Control; **9.2 Ladder Logic Diagrams**; 9.3 PLCs; 9.4 PACs | Apostila: **TIA Portal** — criação de projeto, endereçamento, contatos de memória, operações lógicas, blocos de programação (p.13–37); **Exemplo de linguagem Ladder** (motor 4 velocidades, p.82–86) |
| 7 | 01/04 | SIL (Safety Integrity Level) | ⚠️ Não coberto | ⚠️ Não coberto — ver norma IEC 61508/61511 |
| 8 | 15/04 | IHMs e Sistemas Supervisórios | Cap. 6.4 (parcial, hardware de I/O) | Apostila: **IHM detalhamento completo** (p.37–54) — modelo KTP400 Basic, configuração de IP, telas, conexão com CLP |
| 9 | 22/04 | Redes Industriais | ⚠️ Não coberto | Apostila: **Comunicação entre dispositivos** (p.72–80) — rede Ethernet entre CLP, IHM e inversor de frequência (aplicação prática, não o protocolo em si) |
| 10 | 29/04 | Avaliação | Revisão Caps. 1, 2, 4, 5, 6, 9 | Revisão das Experiências 1–7 |
| 11 | 06/05 | Exame substituto | — | — |
| 12 | 13/05 | Exame final | Todo o conteúdo teórico | Toda a parte prática |

---

## Apostila de Laboratório — Estrutura e Experiências

A apostila é o roteiro oficial do **Laboratório de Automação da UFABC**, usando o kit didático **CLP Siemens S7-1500** + **IHM KTP400 Basic** + **inversor de frequência**, programados no **TIA Portal**. Ela é dividida em 7 experiências progressivas:

| Experiência | Foco |
|---|---|
| 1 | Familiarização com o hardware e o software de programação (TIA Portal) |
| 2 | Aprofundamento da lógica de programação; introdução à *force table* |
| 3 | Uso do CLP para solução de problemas práticos reais |
| 4 | Aprofundamento da solução de problemas práticos reais |
| 5 | Introdução da IHM em problemas práticos reais |
| 6 | Partida de motor elétrico trifásico |
| 7 | Integração do CLP, inversor de frequência e IHM |

**Sumário técnico da apostila** (para referência rápida):
- Arquitetura de hardware do CLP S7-1500 (painel, LEDs, display) — p.4–12
- Ligações de entradas/saídas — p.12
- TIA Portal: configuração de IP, criação de projeto, endereçamento, blocos e comandos — p.13–37
- IHM: configuração, IP, telas, conexão com CLP — p.37–54
- Inversor de frequência: IP, driver, ligação elétrica, parametrização — p.56–72
- Comunicação entre dispositivos (CLP + IHM + inversor) — p.72–80
- Busca de erros — p.80
- Exemplo completo de linguagem Ladder (motor de 4 velocidades + controle analógico via potenciômetro) — p.82–86

Essa apostila **é a peça que faltava** para SCADA/IHM e para a aplicação prática de CLP que o Groover só cobre em teoria (Cap. 9). Vale tratá-la como leitura obrigatória nas semanas 4 a 9, em paralelo com o Groover.

---

## Foco em CLPs (PLCs) e SCADA/IHM

Dois eixos aparecem com destaque no plano de ensino e merecem atenção especial no estudo, mesmo o livro do Groover não cobrindo tudo em profundidade:

### CLPs / PLCs
- **Cap. 9 do Groover** é o núcleo teórico: 9.1 (controle discreto), **9.2 (diagramas Ladder)**, 9.3 (arquitetura de um PLC: CPU, memória, I/O, scan cycle) e 9.4 (PACs).
- A **apostila de laboratório** é onde isso vira prática de fato: arquitetura do CLP Siemens S7-1500, endereçamento real de entradas/saídas, contatos de memória, blocos de programação no TIA Portal, e um exemplo completo em Ladder (controle de motor com 4 velocidades + entrada analógica via potenciômetro).
- O Groover cobre bem a lógica Ladder, mas **não é referência da norma IEC 61131-3 como um todo** (que define 5 linguagens: Ladder Diagram, Function Block Diagram, Structured Text, Instruction List e Sequential Function Chart). A apostila também foca só em Ladder (que é o padrão do TIA Portal nas aulas) — para as outras 4 linguagens, só complementando com a norma ou documentação Siemens, se o professor cobrar isso na prova.

### SCADA e IHM
- **SCADA** (Supervisory Control and Data Acquisition) é um sistema de supervisão e aquisição de dados, não uma linguagem — ele reúne IHM (interface homem-máquina), coleta de dados de CLPs/RTUs, alarmes, históricos e telemetria.
- No Groover, o assunto mais próximo é a **Seção 5.3 (Computer Process Control)** — a base conceitual (DDC, controle supervisório, hierarquia de controle).
- A **apostila cobre isso na prática**, com a IHM KTP400 Basic: configuração de IP, criação de telas, e conexão da IHM com o CLP (seção "IHM detalhamento", p.37–54) — inclusive integrando com o inversor de frequência na Experiência 7. É a referência mais concreta que você tem para essa parte da disciplina.

---

## Estrutura sugerida para o repositório no GitHub

```
automacao-sistemas-industriais/
├── README.md                          # este roteiro
├── 01-introducao/
├── 02-automacao-principios/
├── 03-setores-producao/
├── 04-sensores-atuadores/
├── 05-controle-processos/
├── 06-plc-pac/
│   ├── teoria-groover/                # Cap. 9
│   └── lab-tia-portal/                # apostila: TIA Portal, endereçamento, blocos
├── 07-sil/
├── 08-ihm-supervisorio/
│   └── lab-ihm-ktp400/                # apostila: config. IHM, telas, conexão com CLP
├── 09-redes-industriais/
│   └── lab-comunicacao-dispositivos/  # apostila: rede CLP+IHM+inversor
└── revisao-avaliacao/
```

Cada pasta pode conter: resumo da aula, slides/anotações, exercícios resolvidos e referências ao(s) capítulo(s) do Groover e/ou à seção correspondente da apostila de laboratório.
