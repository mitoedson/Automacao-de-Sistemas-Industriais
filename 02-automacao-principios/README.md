# Automação em Sistemas de Produção e Princípios de Automação

## 1. Trabalho Manual em Sistemas de Produção (Cap. 1.3)

Mesmo com a tendência histórica de substituição do trabalho manual por máquinas automatizadas, o trabalho humano continua sendo indispensável em qualquer sistema de produção — seja operando diretamente o processo, seja gerenciando e mantendo a planta.

### 1.1 Trabalho manual nas operações da fábrica (1.3.1)

Existem razões econômicas e técnicas que tornam o trabalho manual preferível à automação em certas situações:

- **Tarefa tecnicamente difícil de automatizar** — dificuldade de acesso físico, necessidade de destreza manual ou coordenação olho-mão (ex.: acabamento final em linhas de montagem automotiva, inspeções que exigem julgamento humano, manuseio de materiais frágeis ou flexíveis).
- **Ciclo de vida curto do produto** — quando o produto precisa entrar rápido no mercado, ferramental para produção manual é muito mais rápido e barato de preparar do que ferramental automatizado.
- **Produto customizado** — para itens únicos ou personalizados, a versatilidade humana supera qualquer máquina automatizada.
- **Variações de demanda** — mão de obra pode ser aumentada ou reduzida conforme a demanda, com custo proporcional ao uso; um sistema automatizado tem custo fixo de investimento e uma capacidade máxima de produção que não pode ser ultrapassada.
- **Necessidade de reduzir risco de falha do produto** — usar mão de obra manual no início do ciclo de vida de um novo produto reduz o risco de perder um investimento alto em automação caso o produto não vingue no mercado.
- **Falta de capital** — empresas às vezes são forçadas a usar trabalho manual por não terem capital para investir em equipamentos automatizados.

### 1.2 Trabalho nos sistemas de apoio à manufatura (1.3.2)

Nas funções de apoio à manufatura (planejamento, projeto, engenharia), muitas tarefas rotineiras e administrativas já são automatizadas por sistemas computacionais — como o **MRP (Material Requirements Planning)**, que processa grandes volumes de dados para gerar ordens de produção e compra.

Mesmo assim, humanos continuam essenciais nestas funções, mesmo em fábricas altamente automatizadas:

- **Manutenção de equipamentos** — técnicos especializados para manter e reparar sistemas automatizados.
- **Programação e operação de computadores** — atualização de software e programação (embora parte disso venha sendo automatizada com IA).
- **Trabalho de engenharia de projetos** — a fábrica automatizada nunca está "pronta"; sempre há necessidade de melhorias, novas ferramentas e resolução de problemas técnicos.
- **Gestão da planta** — alguém precisa ser responsável por gerenciar a operação da fábrica.


## 2. Princípios e Estratégias de Automação (Cap. 1.4)

A automação nem sempre é a resposta certa para uma situação de produção. Groover propõe três abordagens complementares para lidar com projetos de automação: **(1) o Princípio USA, (2) as Dez Estratégias de Automação e Melhoria de Processos, e (3) uma Estratégia de Migração para Automação.**

### 2.1 O Princípio USA (1.4.1)

**USA = Understand, Simplify, Automate** (Entender, Simplificar, Automatizar):

1. **Entender o processo existente** — mapear entradas, saídas, e o que exatamente acontece com a peça/produto entre a entrada e a saída. Ferramentas de análise de métodos (fluxogramas de processo, diagramas de operação) e modelos matemáticos ajudam a identificar pontos fracos e variáveis relevantes para controle.
2. **Simplificar o processo** — depois de entender o processo, buscar formas de simplificá-lo: eliminar etapas desnecessárias, combinar passos, questionar se cada etapa realmente agrega valor.
3. **Automatizar o processo** — só depois de simplificado ao máximo é que a automação deve ser considerada. Às vezes a simplificação sozinha já resolve o problema, tornando a automação desnecessária.

### 2.2 As Dez Estratégias para Automação e Melhoria de Processos (1.4.2)

Funcionam como um checklist de possibilidades — não são mutuamente exclusivas, e geralmente várias são aplicadas juntas em um mesmo projeto:

1. **Especialização de operações** — uso de equipamento de propósito específico para realizar uma operação com máxima eficiência.
2. **Combinação de operações** — reduzir o número de máquinas/estações distintas pelas quais a peça passa, realizando mais de uma operação na mesma estação (economiza tempo de setup).
3. **Operações simultâneas** — realizar duas ou mais operações ao mesmo tempo na mesma peça, na mesma estação.
4. **Integração de operações** — conectar várias estações de trabalho em um único mecanismo integrado, com transporte automatizado de peças entre elas.
5. **Aumento de flexibilidade** — usar automação programável/flexível para maximizar a utilização do equipamento com diferentes peças/produtos, reduzindo tempo de setup e programação.
6. **Melhoria no manuseio e armazenamento de materiais** — sistemas automatizados de movimentação e armazenamento reduzem trabalho em processo e tempo de ciclo de manufatura.
7. **Inspeção em linha (on-line)** — incorporar a inspeção durante o processo (e não só ao final), permitindo correções em tempo real e reduzindo refugo.
8. **Controle e otimização de processos** — esquemas de controle para operar processos e equipamentos individuais de forma mais eficiente.
9. **Controle das operações da planta** — controle e coordenação em nível de planta (não apenas do processo individual), com alto grau de rede computacional na fábrica.
10. **Manufatura Integrada por Computador (CIM)** — nível mais alto: uso extensivo de sistemas computacionais, bancos de dados e redes em toda a empresa para integrar operações de fábrica e funções de negócio.

### 2.3 Estratégia de Migração para Automação (1.4.3)

Quando uma empresa precisa lançar um novo produto rapidamente, a abordagem mais barata e rápida é começar com produção manual e evoluir gradualmente conforme a demanda cresce. Um plano típico de migração tem **3 fases**:

- **Fase 1 — Produção manual**: células de trabalho manuais e independentes (estações de uma única posição). Ferramental rápido e barato — ideal para o lançamento do produto.
- **Fase 2 — Produção automatizada independente**: conforme a demanda cresce e a automação se justifica, as estações individuais são automatizadas, mas o transporte de peças entre elas ainda é manual.
- **Fase 3 — Produção automatizada integrada**: quando a demanda em massa e de longo prazo está confirmada, as estações automatizadas são integradas em um sistema único, com transporte automatizado entre elas — reduzindo ainda mais mão de obra e aumentando a taxa de produção.

**Vantagens dessa estratégia de migração:**
- Permite lançar o produto no menor tempo possível.
- Permite introduzir automação gradualmente, conforme a demanda e as mudanças de engenharia do produto amadurecem.
- Evita comprometer um alto nível de automação logo no início, quando ainda há risco de a demanda não justificar o investimento.


## 3. Elementos Básicos de um Sistema Automatizado (Cap. 4.1)

Um sistema automatizado é definido como a tecnologia pela qual um processo é realizado sem assistência humana, através de um **programa de instruções** combinado com um **sistema de controle**. Todo sistema automatizado é composto por **três elementos básicos**:

1. **Potência (Power)** — necessária tanto para acionar o processo em si quanto para operar o programa e o sistema de controle.
2. **Programa de instruções** — direciona as ações do sistema.
3. **Sistema de controle** — executa as instruções do programa.

### 3.1 Potência para o processo e para a automação (4.1.1)

A eletricidade é a principal fonte de potência em sistemas automatizados por ser amplamente disponível, facilmente conversível em outras formas de energia (mecânica, térmica, luminosa, acústica, hidráulica, pneumática), e adequada para transmissão de sinais e processamento de dados.

Além de acionar o processo produtivo propriamente dito (ex.: usinagem, fundição, soldagem), a potência também é necessária para:
- **Carregamento e descarregamento** da peça na máquina.
- **Transporte de material** entre operações.
- **Unidade controladora** (computadores digitais que leem o programa e executam cálculos de controle).
- **Atuação dos sinais de controle** (motores, válvulas, dispositivos eletromecânicos).
- **Aquisição de dados e processamento de informação.**

### 3.2 Programa de instruções (4.1.2)

O programa de instruções define as ações realizadas durante um **ciclo de trabalho**. Existem cinco categorias de programas de ciclo de trabalho, em ordem crescente de complexidade:

- **Controle de ponto de ajuste (set-point control)** — o parâmetro do processo é mantido constante durante o ciclo (ex.: manter a temperatura de um forno).
- **Controle lógico** — o valor do parâmetro depende de outras variáveis do processo.
- **Controle de sequência** — o valor do parâmetro muda em função do tempo.
- **Programa interativo** — há interação entre um operador humano e o sistema de controle durante o ciclo.
- **Programa inteligente** — o sistema de controle exibe características de inteligência artificial (lógica, tomada de decisão, aprendizado).

Historicamente, esses ciclos eram controlados por componentes de hardware (chaves de fim de curso, temporizadores, cames, relés eletromecânicos), que eram difíceis e demorados de reconfigurar. Os controladores modernos, baseados em computadores digitais, permitem atualizações e melhorias muito mais fáceis nos programas de controle.

### 3.3 Sistema de controle (4.1.3)

O sistema de controle executa o programa de instruções e pode ser de dois tipos:

- **Malha fechada (closed-loop / feedback control)** — a variável de saída é comparada com o parâmetro de entrada desejado, e qualquer diferença é usada para corrigir a saída. Possui 6 elementos básicos: (1) parâmetro de entrada, (2) processo, (3) variável de saída, (4) sensor de realimentação, (5) controlador, (6) atuador.
- **Malha aberta (open-loop control)** — opera sem realimentação; o controlador depende de um modelo preciso do efeito do atuador sobre a variável do processo. É mais simples e barato, mas há sempre o risco de o atuador não produzir o efeito esperado. É apropriado quando as ações de controle são simples, o atuador é muito confiável e as forças de reação são pequenas o suficiente para não afetar a atuação.

**Exemplo prático:** um sistema de posicionamento com **servomotor DC** (malha fechada, com encoder óptico medindo a posição real) versus um sistema com **motor de passo** (malha aberta, sem realimentação, movendo a mesa uma fração fixa por pulso recebido).


## 4. Funções Avançadas de Automação (Cap. 4.2)

Além de executar o ciclo de trabalho programado, um sistema automatizado pode executar funções avançadas voltadas para segurança e desempenho do equipamento.

### 4.1 Monitoramento de segurança (4.2.1)

Existem duas razões para dotar um sistema automatizado de monitoramento de segurança: **(1) proteger os trabalhadores** nas proximidades do sistema, e **(2) proteger o próprio equipamento**.

O monitoramento de segurança vai além de medidas convencionais (proteções físicas, botões de parada de emergência) — envolve sensores que acompanham a operação do sistema e identificam condições inseguras, respondendo com ações como: parar completamente o sistema, soar um alarme, reduzir a velocidade de operação, ou tomar ações corretivas (esta última já se aproxima do conceito de detecção e recuperação de erros).

Exemplos de sensores usados: chaves de fim de curso, sensores fotoelétricos, sensores de temperatura, detectores de fumaça/calor, tapetes sensíveis à pressão, sistemas de visão de máquina.

### 4.2 Diagnóstico de manutenção e reparo (4.2.2)

Um subsistema de diagnóstico de manutenção tem três modos típicos de operação:

1. **Monitoramento de status** — monitora e registra o status de sensores e parâmetros-chave durante a operação normal, podendo alertar sobre uma falha iminente.
2. **Diagnóstico de falhas** — invocado quando ocorre uma falha, interpreta os valores monitorados para identificar a causa.
3. **Recomendação de procedimento de reparo** — recomenda à equipe de manutenção os passos a seguir, muitas vezes com base em sistemas especialistas (IA).

### 4.3 Detecção e recuperação de erros (4.2.3)

Com o aumento do uso de controle computacional, há uma tendência de usar o próprio computador de controle não só para diagnosticar falhas, mas também para **tomar automaticamente a ação corretiva necessária**.

**Detecção de erros** — classifica os erros em três categorias:
- **Erros aleatórios** — resultam da natureza estocástica normal do processo.
- **Erros sistemáticos** — resultam de uma causa identificável (mudança de matéria-prima, desvio em um ajuste do equipamento).
- **Aberrações** — resultam de falha de equipamento ou erro humano.

**Recuperação de erros** — quatro estratégias possíveis, em ordem crescente de urgência:
1. Fazer ajustes ao final do ciclo de trabalho atual.
2. Fazer ajustes durante o ciclo atual.
3. Parar o processo para invocar ação corretiva (mas o sistema se recupera sozinho, sem ajuda humana).
4. Parar o processo e pedir ajuda (quando o erro não pode ser resolvido automaticamente).


## 5. Níveis de Automação (Cap. 4.3)

A automação pode ser aplicada em diferentes níveis hierárquicos da fábrica. Groover identifica **cinco níveis**, do mais baixo ao mais alto:

1. **Nível de dispositivo (Device level)** — o nível mais baixo; inclui atuadores, sensores e demais componentes de hardware que compõem uma malha de controle individual (ex.: a malha de realimentação de um eixo de uma máquina CNC).
2. **Nível de máquina (Machine level)** — hardware do nível de dispositivo reunido em máquinas individuais (ex.: máquinas CNC, robôs industriais, esteiras motorizadas, veículos guiados automaticamente — AGVs).
3. **Nível de célula ou sistema (Cell/system level)** — a célula ou sistema de manufatura, operando sob instruções do nível de planta; um grupo de máquinas conectadas por um sistema de manuseio de material, computador e demais equipamentos.
4. **Nível de planta (Plant level)** — o nível da fábrica; recebe instruções do sistema de informação corporativo e as traduz em planos operacionais de produção (processamento de pedidos, planejamento de processo, controle de estoque, MRP, controle de qualidade).
5. **Nível de empresa (Enterprise level)** — o nível mais alto, o sistema de informação corporativo, responsável por marketing, vendas, contabilidade, projeto, pesquisa e planejamento agregado — tipicamente gerenciado via **ERP**.

> A maior parte das tecnologias de automação e controle discutidas no livro está concentrada nos **níveis 2 e 3** (máquina e célula), embora tecnologias do nível 1 também sejam abordadas.

---

## Pontos-chave para revisão

- [ ] Saber listar as 5 situações em que o trabalho manual é preferível à automação.
- [ ] Explicar o Princípio USA (Understand → Simplify → Automate) e por que a ordem importa.
- [ ] Enumerar e explicar as 10 estratégias de automação e melhoria de processos.
- [ ] Descrever as 3 fases da estratégia de migração para automação e suas vantagens.
- [ ] Identificar os 3 elementos básicos de um sistema automatizado (potência, programa, controle).
- [ ] Diferenciar controle em malha aberta vs. malha fechada, com exemplo de cada um.
- [ ] Listar as 5 categorias de programas de ciclo de trabalho, em ordem de complexidade.
- [ ] Explicar as 3 funções avançadas de automação (monitoramento de segurança, diagnóstico de manutenção, detecção/recuperação de erros).
- [ ] Diferenciar os 3 tipos de erro (aleatório, sistemático, aberração) e as 4 estratégias de recuperação.
- [ ] Enumerar os 5 níveis de automação hierárquica, do dispositivo à empresa.


---

**Referência:** Mikell P. Groover, *Automation, Production Systems, and Computer-Integrated Manufacturing*, 5ª ed. — Cap. 1 (Seções 1.3–1.4) e Cap. 4 (Seções 4.1–4.3).


