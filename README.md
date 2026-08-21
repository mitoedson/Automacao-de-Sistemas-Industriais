# Automacao-de-Sistemas-Industriais
Estes documentos são baseados na disciplina do Prof. Dr. Alexandre Acácio de Andrade, da UFABC, e constituem um guia didático abrangente sobre controladores lógicos programáveis (CLP), focando em sua aplicação na automação industrial. O material detalha desde a evolução histórica desses dispositivos até sua estrutura interna, explicando o funcionamento de componentes como a CPU, memórias e fontes de alimentação.

**Livro-texto de referência (teoria):** Mikell P. Groover, *Automation, Production Systems, and Computer-Integrated Manufacturing*, 5ª edição, Pearson, 2016.
**Apostila de referência (prática de laboratório):** *Roteiro — Prática de Laboratório de Automação: CLP — Controladores Lógicos Programáveis*, UFABC (Alexandre Acácio de Andrade, Everton Flavio Oliveira de Almeida, Fabio Gomes de Freitas), Draft V8.

> Este roteiro cruza os assuntos do Plano de Ensino com os capítulos do livro do Groover (base teórica) e com a apostila de laboratório (base prática, com o CLP Siemens S7-1500). Quando um assunto não tem correspondência direta em nenhum dos dois materiais, isso está sinalizado.

---

## Assuntos da disciplina

### [Introdução à Automação Industrial](./01-introducao/README.md)
*Conceito de sistema de produção, automação e seus objetivos — Cap. 1 (1.1–1.2) do Groover.*

### [Automação em Sistemas de Produção e Princípios de Automação](./02-automacao-principios/)
*Trabalho manual em sistemas de produção, USA Principle e as 10 estratégias de automação — Cap. 1 (1.3–1.4) e Cap. 4 (4.1–4.3) do Groover.*

### [Setores de Produção e Produtos](./03-setores-producao/)
*Indústrias e produtos de manufatura, operações de produção e relação produto/produção — Cap. 2 (2.1, 2.2, 2.4) do Groover.*

### [Sensores e Atuadores](./04-sensores-atuadores/)
*Componentes de hardware para automação, conversão analógico-digital — Cap. 6 (6.1–6.3) do Groover, com complemento prático da apostila (entrada analógica via potenciômetro, p.12).*

### [Controle de Processos por Computador](./05-controle-processos/)
*Indústrias de processo vs. discretas, controle contínuo vs. discreto, controle por computador — Cap. 5 (5.1–5.3) do Groover, com a arquitetura de hardware do CLP Siemens S7-1500 (apostila, p.4–8).*

### [Controladores Lógicos Programáveis (CLP)](./06-plc-pac/)
*Controle discreto, diagramas Ladder, arquitetura de PLCs e PACs — Cap. 9 do Groover, com o guia prático do TIA Portal e o exemplo completo em Ladder (apostila, p.13–37 e p.82–86).*

### [SIL — Safety Integrity Level](./07-sil/)
*Nível de integridade de segurança em sistemas automatizados — não coberto no Groover nem na apostila; consultar norma IEC 61508/61511 e material do AVA.*

### [IHMs e Sistemas Supervisórios](./08-ihm-supervisorio/)
*Interface homem-máquina e conceitos de supervisão (SCADA) — base conceitual no Cap. 5.3 do Groover, com a configuração completa da IHM KTP400 Basic (apostila, p.37–54).*

### [Redes Industriais](./09-redes-industriais/)
*Comunicação entre dispositivos de automação — não coberto no Groover; aplicação prática de rede entre CLP, IHM e inversor de frequência (apostila, p.72–80).*

### [Revisão e Avaliação](./revisao-avaliacao/)
*Revisão geral dos capítulos 1, 2, 4, 5, 6 e 9 do Groover e das Experiências 1–7 da apostila de laboratório.*

---

## Escopo: o que o Plano de Ensino realmente cobre no Groover

O cronograma semanal usa exclusivamente o **Capítulo 1** (introdução, fora das partes) e capítulos das **Partes I e II** do livro — nada das Partes III em diante (Material Handling, Manufacturing Systems, Quality Control, CIM) é usado.

Mas mesmo dentro desse intervalo (Cap. 1–9), nem tudo é usado:

| Usado no plano de ensino | Não usado (fica de fora do escopo do curso) |
|---|---|
| Cap. 1 — Introduction | Cap. 3 — Manufacturing Metrics and Economics |
| Cap. 2 — Manufacturing Operations | Cap. 7 — Computer Numerical Control |
| Cap. 4 — Introduction to Automation | Cap. 8 — Industrial Robotics |
| Cap. 5 — Industrial Control Systems | |
| Cap. 6 — Hardware Components for Automation and Process Control | |
| Cap. 9 — Discrete Control and PLCs | |

Este roteiro segue **estritamente o conteúdo do cronograma do plano de ensino** — os assuntos listados acima não incluem os capítulos 3, 7 e 8, nem tópicos da ementa (planejamento da produção, escalonamento, ISO 50001 etc.) que não aparecem no cronograma das 9 semanas de conteúdo novo.

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

Cada pasta corresponde a um dos links da seção **Assuntos da disciplina** acima, e pode conter: resumo da aula, slides/anotações, exercícios resolvidos e referências ao(s) capítulo(s) do Groover e/ou à seção correspondente da apostila de laboratório.
