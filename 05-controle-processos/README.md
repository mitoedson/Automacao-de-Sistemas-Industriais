# Controle de Processos por Computador
> Este material cobre a distinção entre indústrias de processo e de manufatura discreta, os tipos de controle contínuo e discreto, e como o computador é usado para controlar processos industriais — encerrando com a aplicação prática dessa arquitetura no CLP usado no laboratório da disciplina.


## 1. Indústrias de Processo vs. Indústrias de Manufatura Discreta (Cap. 5.1)

Como já visto no Cap. 2, as indústrias se dividem em duas categorias básicas: **indústrias de processo** (que operam sobre quantidades/volumes de material — líquidos, gases, pós) e **indústrias de manufatura discreta** (que operam sobre unidades contáveis — peças e produtos). As operações unitárias típicas diferem bastante entre as duas: nas indústrias de processo, encontramos reações químicas, comminuição (redução de partículas), deposição química de vapor, destilação, mistura e separação de ingredientes; já nas indústrias discretas, encontramos fundição, forjamento, extrusão, usinagem, moldagem de plástico e estampagem de chapas metálicas.

### 1.1 Níveis de automação nas duas indústrias

Retomando a hierarquia de níveis de automação (Cap. 4.3), a comparação entre as duas indústrias mostra diferenças principalmente nos níveis mais baixos e intermediários:

| Nível | Indústrias de Processo | Indústrias de Manufatura Discreta |
|---|---|---|
| 5 (mais alto) | Nível de empresa — sistema de informação gerencial, planejamento estratégico | Nível de empresa — sistema de informação gerencial, planejamento estratégico |
| 4 | Nível de planta — programação, rastreamento de materiais, monitoramento de equipamentos | Nível de planta/fábrica — programação, rastreamento de trabalho em processo, roteamento de peças |
| 3 | Nível de controle supervisório — controle e coordenação de várias operações unitárias interconectadas | Nível de célula/sistema de manufatura — controle e coordenação de grupos de máquinas |
| 2 | Nível de controle regulatório — controle de operações unitárias | Nível de máquina — máquinas de produção e estações de trabalho |
| 1 (mais baixo) | Nível de dispositivo — sensores e atuadores das malhas de controle | Nível de dispositivo — sensores e atuadores das ações da máquina |

Nos níveis mais baixos (1 e 2), a diferença está no tipo de dispositivo usado (malhas de controle de processos químicos/térmicos vs. controle de ações mecânicas de máquinas). No nível 3, a diferença é entre controlar operações unitárias interconectadas ou máquinas interconectadas. Já nos níveis mais altos (planta e empresa), as questões de controle são bastante semelhantes entre os dois tipos de indústria.

### 1.2 Variáveis e parâmetros contínuos vs. discretos

Retomando a distinção do Cap. 4 (variáveis são saídas do processo; parâmetros são entradas): nas indústrias de processo, variáveis e parâmetros tendem a ser **contínuos**; nas indústrias discretas, tendem a ser **discretos**.

- **Variável (ou parâmetro) contínua** — ininterrupta ao longo do tempo, geralmente considerada analógica (pode assumir qualquer valor dentro de uma faixa). Exemplos: força, temperatura, vazão, pressão, velocidade.
- **Variável (ou parâmetro) discreta** — só pode assumir certos valores dentro de uma faixa. O tipo mais comum é **binário** (liga/desliga, aberto/fechado) — ex.: chave de fim de curso aberta ou fechada, motor ligado ou desligado, peça presente ou ausente em um fixador. Existem também variáveis discretas não binárias (podem assumir mais de dois valores, mas não infinitos) — ex.: contagem diária de peças, leitura de um tacômetro digital. Um caso especial é o **dado de pulso** (uma série de pulsos, ou *pulse train*) — usado tanto para contar peças (cada peça passando por uma célula fotoelétrica gera um pulso) quanto como parâmetro de processo (para acionar um motor de passo).


## 2. Controle Contínuo vs. Controle Discreto (Cap. 5.2)

Assim como existem dois tipos básicos de variáveis, existem dois tipos básicos de controle: **controle contínuo** (variáveis e parâmetros contínuos e analógicos) e **controle discreto** (variáveis e parâmetros discretos, principalmente binários). Na prática, a maioria das operações — tanto nas indústrias de processo quanto nas discretas — envolve os dois tipos de variável ao mesmo tempo, então muitos controladores industriais são projetados para lidar com ambos.

| Fator de comparação | Controle contínuo (indústrias de processo) | Controle discreto (manufatura discreta) |
|---|---|---|
| Medidas típicas de saída | Peso, volume líquido, volume sólido | Número de peças, número de produtos |
| Medidas típicas de qualidade | Consistência, concentração da solução, ausência de contaminantes | Dimensões, acabamento superficial, aparência, ausência de defeitos |
| Variáveis/parâmetros típicos | Temperatura, vazão volumétrica, pressão | Posição, velocidade, aceleração, força |
| Sensores típicos | Medidores de vazão, termopares, sensores de pressão | Chaves de fim de curso, sensores fotoelétricos, extensômetros |
| Atuadores típicos | Válvulas, aquecedores, bombas | Chaves, motores, pistões |
| Constante de tempo típica do processo | Segundos, minutos, horas | Menos de um segundo |

> Vale notar que, desde que os computadores digitais começaram a substituir controladores analógicos no controle de processos contínuos (por volta de 1960), as variáveis contínuas deixaram de ser medidas de forma verdadeiramente contínua — elas passam a ser **amostradas periodicamente**, criando um sistema de dados amostrados discretos que aproxima o sistema contínuo real. Assim, mesmo o controle "contínuo" moderno carrega características de dados discretos.

### 2.1 Sistemas de controle contínuo

O objetivo geral é manter o valor de uma variável de saída em um nível desejado, de forma parecida com um sistema de controle por realimentação (Cap. 4.1.3) — mas, na prática, a maioria dos processos contínuos tem várias malhas de realimentação separadas, que precisam ser controladas e coordenadas em conjunto. Algumas categorias importantes:

- **Controle regulatório** — o objetivo é manter o desempenho do processo em certo nível (ou dentro de uma faixa de tolerância). É análogo ao controle por realimentação em uma malha individual, mas aplicado ao processo como um todo, muitas vezes calculado a partir de várias variáveis de saída (um "índice de desempenho"). Limitação: a ação corretiva só acontece **depois** que um distúrbio já afetou a saída do processo.
- **Controle *feedforward*** — antecipa o efeito de distúrbios, detectando-os e compensando-os **antes** que afetem o processo — em vez de reagir depois, como o controle regulatório. Como a compensação raramente é 100% eficaz, o *feedforward* costuma ser combinado com controle por realimentação.
- **Otimização em estado estacionário** — aplicável quando há um índice de desempenho bem definido (custo, taxa de produção, rendimento), a relação entre as variáveis do processo e esse índice é conhecida, e os valores ótimos dos parâmetros podem ser determinados matematicamente. O sistema de controle aqui é de **malha aberta**.
- **Controle adaptativo** — combina controle por realimentação com controle ótimo, medindo as variáveis do processo durante a operação e usando um algoritmo que busca otimizar um índice de desempenho — mas, diferente da otimização em estado estacionário, é capaz de lidar com um ambiente que muda ao longo do tempo. Funciona em três etapas: **(1) identificação** (determinar o desempenho atual do sistema a partir de medições), **(2) decisão** (decidir que mudanças fazer para melhorar o desempenho) e **(3) modificação** (implementar fisicamente a mudança, usando atuadores). Um exemplo clássico é o controle adaptativo de usinagem, em que variações de força de corte, potência e vibração ajustam parâmetros como velocidade de corte e avanço.
- **Estratégias de busca on-line** — usadas quando a relação entre parâmetros de entrada e índice de desempenho não é bem conhecida o suficiente para aplicar controle adaptativo tradicional. Em vez disso, pequenas mudanças sistemáticas são feitas nos parâmetros de entrada, e os efeitos observados guiam mudanças maiores subsequentes — de tentativa e erro até métodos de gradiente. Mais comuns nas indústrias de processo contínuo do que na manufatura discreta.
- **Outras técnicas especializadas** — sistemas de aprendizado, sistemas especialistas e outros métodos de inteligência artificial.

### 2.2 Sistemas de controle discreto

Aqui, parâmetros e variáveis mudam em momentos discretos no tempo, definidos previamente por um programa de instruções (ex.: um programa de ciclo de trabalho, Cap. 4.1.2). As mudanças podem ser de dois tipos:

- **Mudanças orientadas a eventos (*event-driven*)** — executadas em resposta a algum evento que alterou o estado do sistema. Exemplos: um robô carrega uma peça em um fixador e uma chave de fim de curso detecta a presença da peça, liberando o início do ciclo de usinagem automático; o nível de material plástico em um funil de injetora aciona uma chave de nível baixo, que abre uma válvula para repor o material, e fecha quando o nível alto é atingido; contar peças passando por um sensor óptico em um transportador.
- **Mudanças orientadas a tempo (*time-driven*)** — executadas em um ponto específico do tempo ou após um intervalo determinado. Exemplos: o "relógio da fábrica" que soa um sinal em horários fixos de início/fim de turno; um ciclo de tratamento térmico que precisa durar um tempo específico; o ciclo de agitação de uma máquina de lavar, que dura um tempo pré-definido após o tanque ser preenchido (o próprio enchimento, por sua vez, é orientado a evento — continua até o nível correto ser sensoriado).

Esses dois tipos de mudança correspondem a dois tipos de controle discreto: **controle lógico** (para mudanças orientadas a eventos) e **controle de sequência** (para mudanças orientadas a tempo). O controle discreto é amplamente usado tanto na manufatura discreta (transportadores, sistemas de armazenamento automatizado, máquinas de produção independentes, linhas de transferência automatizadas, sistemas de montagem automatizados) quanto nas indústrias de processo — nestas últimas, associado principalmente ao **processamento em lotes** (*batch*), em que cada lote passa por um ciclo de etapas de processamento (mudanças de temperatura e pressão, possível transferência entre recipientes, e por fim embalagem), tipicamente combinando controle contínuo (regulação dos parâmetros de cada etapa) com controle discreto (gestão da sequência e do tempo das etapas).


## 3. Controle de Processos por Computador (Cap. 5.3)

O uso de computadores digitais para controlar processos industriais começou nas indústrias de processo contínuo no final dos anos 1950 — antes disso, controladores analógicos implementavam o controle contínuo, e sistemas de relés implementavam o controle discreto. A primeira tentativa conhecida de controle por computador foi em uma refinaria da Texaco, no Texas, em 1959. O primeiro sistema de **controle digital direto (DDC)** foi instalado pela Imperial Chemical Industries, na Inglaterra, em 1962. Ao longo das décadas seguintes, o avanço de minicomputadores (final dos anos 1960), microcomputadores (início dos anos 1970) e a criação do **CLP** (também início dos anos 1970) tornaram o controle por computador cada vez mais acessível — até chegar ao uso generalizado de microprocessadores em praticamente todos os processos industriais modernos.

### 3.1 Requisitos de controle

Seja controle contínuo, discreto ou ambos, praticamente toda aplicação de controle de processo compartilha a necessidade de se comunicar e interagir com o processo **em tempo real**. Um controlador em tempo real precisa ser capaz de **multitarefa** — lidar com várias tarefas simultaneamente sem que uma interfira na outra. Dois requisitos básicos:

1. **Interrupções iniciadas pelo processo** — o controlador precisa responder a sinais recebidos do processo, às vezes suspendendo a execução do programa atual para atender uma necessidade de prioridade mais alta (geralmente disparada por condições anormais de operação).
2. **Ações iniciadas por temporizador** — o controlador precisa executar certas ações em pontos específicos do tempo: varrer valores de sensores em intervalos regulares, ligar/desligar dispositivos binários em momentos específicos do ciclo de trabalho, exibir dados de desempenho no console do operador, recalcular parâmetros ótimos em horários específicos.

Além desses dois, o computador de controle também precisa lidar com: **(3) comandos do computador ao processo** (sinais de saída para acionar dispositivos ou reajustar pontos de ajuste), **(4) eventos iniciados pelo sistema/programa** (comunicação entre computadores em rede, ou chamadas de funções não relacionadas ao processo, como impressão de relatórios) e **(5) eventos iniciados pelo operador** (entrada de novos programas, edição de programas existentes, dados de pedidos, solicitação de dados do processo, paradas de emergência).

### 3.2 Capacidades do controle por computador

Para atender esses requisitos, o controlador precisa de quatro capacidades:

- **Polling (varredura/amostragem)** — amostragem periódica de dados que indicam o status do processo. Envolve decisões sobre **frequência de varredura**, **ordem de varredura**, e **formato de varredura** (ler todos os dados novos a cada ciclo; atualizar só o que mudou; ou usar varredura de alto e baixo nível — coletando poucos dados-chave a cada ciclo, mas fazendo uma varredura mais completa se algo indicar irregularidade).
- **Interlocks (intertravamentos)** — mecanismos de segurança que coordenam as atividades de dois ou mais dispositivos, evitando que um interfira no outro. Podem ser **de entrada** (sinal de um dispositivo externo para o controlador — ex.: uma máquina avisa que terminou de processar uma peça, liberando o próximo passo do ciclo) ou **de saída** (sinal do controlador para um dispositivo externo — ex.: comando para uma máquina iniciar seu ciclo automático depois que a peça foi carregada).
- **Sistema de interrupções** — permite suspender temporariamente a execução do programa atual para tratar um evento de prioridade mais alta, retomando depois de onde parou. Interrupções podem ser **internas** (geradas pelo próprio sistema — eventos de temporizador, de sistema/programa) ou **externas** (iniciadas pelo processo ou pelo operador). Sistemas podem ter um único nível de interrupção (só dois modos: normal e interrompido — o que pode gerar espera perigosa se uma tarefa de baixa prioridade estiver sendo atendida quando uma de alta prioridade chega) ou **múltiplos níveis de interrupção** (permitindo que uma tarefa de prioridade mais alta interrompa uma de prioridade mais baixa que já estava em andamento, mesmo que essa já tivesse interrompido outra).
- **Tratamento de exceções** — lidar com eventos fora da operação normal ou desejada do processo (problemas de qualidade, variáveis fora da faixa normal, falta de matéria-prima, condições de risco como incêndio, mau funcionamento do controlador). É, essencialmente, uma forma de detecção e recuperação de erros (Cap. 4.2.3).

### 3.3 Formas de controle de processo por computador

Antes de mais nada, vale distinguir **monitoramento de processo** (o computador só coleta dados, sem controlar) de **controle de processo** propriamente dito (o computador regula o processo) — que por sua vez pode ser de **malha aberta** (o computador executa ações sem coletar dados de realimentação) ou de **malha fechada** (a forma mais comum, com realimentação ou intertravamento garantindo que as instruções foram cumpridas corretamente).

- **Monitoramento de processo por computador** — o computador observa e registra dados, mas o controle continua nas mãos de operadores humanos. Os dados coletados se dividem em três categorias: **dados de processo** (valores medidos de parâmetros de entrada e variáveis de saída), **dados de equipamento** (status do equipamento, usado para monitorar utilização de máquinas, agendar trocas de ferramenta, evitar quebras, diagnosticar falhas) e **dados de produto** (exigidos por regulamentação em setores como farmacêutico e de dispositivos médicos).
- **Controle digital direto (DDC)** — um sistema de controle de processo por computador em que certos componentes de um sistema de controle analógico convencional (controlador analógico, instrumentos de registro/exibição, mostradores de ponto de ajuste, comparador) são substituídos pelo computador digital, mantendo o sensor/transdutor e o atuador. É considerado hoje uma fase de transição na evolução do controle de processos — a ideia original de simplesmente imitar controladores analógicos foi rapidamente superada por vantagens adicionais: algoritmos de controle mais sofisticados que o analógico permite, integração e otimização de várias malhas ao mesmo tempo, e facilidade de editar os programas de controle por reprogramação (em vez de mudanças de hardware, como seria necessário em controladores analógicos).
- **CNC e robótica industrial** — outras formas de controle industrial por computador, em que o computador direciona uma máquina-ferramenta (CNC) ou um manipulador robótico através de uma sequência de posições/passos definidos por um programa de instruções — exigindo, além do controle de sequência, cálculos geométricos (trajetória da ferramenta ou interpolação de movimento).
- **Controladores Lógicos Programáveis (CLPs) e equipamentos relacionados** — surgiram por volta de 1970 como melhoria em relação aos controladores de relé eletromecânico usados até então. Um CLP pode ser definido como um controlador baseado em microprocessador que usa instruções armazenadas em memória programável para implementar funções de lógica, sequenciamento, temporização, contagem e cálculo aritmético, controlando máquinas e processos — hoje usados tanto para controle contínuo quanto discreto, em indústrias de processo e de manufatura discreta. Termos relacionados: **PAC** (*Programmable Automation Controller* — combina as capacidades de I/O de um CLP com o poder de processamento, conectividade de rede e integração de dados de um PC) e **RTU** (*Remote Terminal Unit* — dispositivo baseado em microprocessador conectado ao processo, que converte sinais elétricos de sensores em dados digitais, geralmente usando comunicação sem fio, ao contrário dos CLPs que usam conexões cabeadas).
- **SCADA (Supervisory Control and Data Acquisition)** — um nível de controle mais alto que CNC, CLPs e outros equipamentos de processamento diretamente interfaceados ao processo (nível 2); o controle supervisório se sobrepõe a esses sistemas de nível de processo (níveis 3 e 4). Um sistema SCADA típico tem quatro componentes: **(1)** um computador supervisório central, que coleta dados e transmite comandos, **(2)** uma **interface homem-máquina (IHM/HMI)**, que apresenta os dados aos operadores e permite o envio de comandos, **(3)** CLPs e RTUs distribuídos, conectados diretamente ao processo, e **(4)** uma rede de comunicação conectando o computador central aos CLPs/RTUs. O modo geral de operação é que os dispositivos remotos controlem diretamente as malhas do sistema, mas podendo ser sobrepostos pelo operador via IHM quando necessário (ex.: mudar um ponto de ajuste).
- **Sistemas de Controle Distribuído (DCS)** — descreve uma configuração de múltiplos microcomputadores conectados para compartilhar e distribuir a carga de trabalho de controle do processo. Componentes: várias estações de controle de processo espalhadas pela planta, uma sala de controle central com estações de operador (controle supervisório), estações locais de operador distribuídas pela planta (fornecendo redundância — se a sala central falhar, as estações locais assumem, e vice-versa), e uma rede de comunicação (chamada às vezes de *data highway*) conectando tudo. A distinção entre DCS e SCADA nem sempre é clara — "distribuído" enfatiza a coleção interconectada de computadores, enquanto "supervisório" enfatiza o uso de um computador central para gerenciar dispositivos remotos.
- **PCs no controle de processos** — usados de duas formas básicas: **(1) interface do operador** (o PC se conecta a CLPs/outros dispositivos que controlam o processo diretamente, sem controlar ele mesmo — vantagem: uma falha no PC não interrompe o controle do processo) ou **(2) controle direto** (o PC controla o processo diretamente em tempo real — tendência crescente desde os anos 1990, viabilizada por PCs mais confiáveis, familiaridade generalizada com PCs, tendência de arquitetura aberta entre fornecedores, e sistemas operacionais com melhor suporte a tempo real e multitarefa).
- **Integração de dados de fábrica em toda a empresa** — a evolução mais recente do controle distribuído baseado em PC, conectando o sistema de controle industrial da fábrica aos sistemas de negócio de toda a empresa. O termo **ERP** (*Enterprise Resource Planning*) se refere a um sistema de software que integra não só os dados de fábrica, mas todos os dados necessários para executar as funções de negócio da organização, tipicamente com um banco de dados central único acessível de qualquer lugar da empresa. Isso permite, entre outras coisas: gestores com acesso mais direto às operações de chão de fábrica, planejadores de produção usando dados atualizados de tempos e taxas de produção, vendedores dando estimativas realistas de prazo de entrega, clientes acompanhando o status de seus pedidos, e controle de qualidade com acesso a históricos de desempenho.


## 4. Complemento Prático — Arquitetura de Hardware do CLP (Apostila de Laboratório, p.4–8)

A apostila do laboratório detalha exatamente como esses conceitos teóricos — CLP, módulos de E/S, controle contínuo (entradas analógicas) e discreto (entradas digitais) — se materializam no hardware real usado nas aulas práticas: o **CLP Siemens S7-1500**.

### 4.1 Estrutura modular

O CLP Siemens S7-1500 usado no laboratório é composto por uma **unidade de base modular**, formada por: fonte de alimentação, módulo de CPU (processador) e módulos periféricos de entrada/saída. A configuração disponível no laboratório inclui:

| Módulo | Código | Slot | Observações |
|---|---|---|---|
| Fonte de alimentação (PM 190W) | 6EP1333-4BA00 | 0 | Entrada 120/230 V CA, 50/60 Hz, saída 24 V DC / 8 A |
| CPU 1516F-3 PN/DP | 6ES7 516-3FN01-0AB0 | 1 | Memória de trabalho de 6,5 MB; tempo de processamento de operações de bit de 10 ns; interfaces integradas PROFIBUS e PROFINET |
| Entrada digital | 6ES7 521-1BL00-0AB0 | 2 | 8 entradas digitais, 110/240 V |
| Saída digital | 6ES7 522-1BL01-0AB0 | 3 | 8 saídas digitais a relé |
| Entrada analógica | 6ES7 531-7KF00-0AB0 | 4 | 8 entradas analógicas |
| Saída analógica | 6ES7 532-5HD00-0AB0 | 5 | 4 saídas analógicas |

Essa organização em módulos plugáveis, montados em trilho padrão DIN, é justamente o que permite expandir o CLP com mais pontos de entrada/saída ou funcionalidades de rede, conforme a necessidade da aplicação — um exemplo concreto do "nível de dispositivo" (sensores e atuadores) conectado ao "nível de máquina" descrito na hierarquia de automação da Seção 5.1.

### 4.2 Painel frontal e modos de operação

O painel frontal da CPU apresenta LEDs indicadores de status e falha: **RUN/STOP** (amarelo/verde), **ERROR** (vermelho), **MAINT** (amarelo), além de LEDs de link para as portas de rede (PROFINET, portas X1 P1, X1 P2, X2 P1). Uma chave seletora física define o modo de operação da CPU:

- **RUN** — a CPU executa o programa de aplicativo.
- **STOP** — a CPU não executa o programa.
- **MRES** — reset geral da memória da CPU.

Além disso, a CPU tem um **display integrado** com botões de navegação, que permite consultar informações de diagnóstico, configurar endereços IP e parâmetros de rede, ajustar data/hora, gerenciar módulos conectados, e restaurar configurações de fábrica — funcionalidades que ilustram, na prática, boa parte das "capacidades do controle por computador" discutidas na Seção 5.3.2 (como o próprio conceito de monitoramento de status via painel/console).

### 4.3 Ligação da relação teoria-prática

Essa arquitetura de hardware conecta diretamente com os conceitos do Cap. 5:

- Os **módulos de entrada/saída digital** implementam, na prática, o **controle discreto** descrito na Seção 5.2.2 — cada entrada/saída é binária (0 ou 1).
- Os **módulos de entrada/saída analógica** são o ponto de entrada para o **controle contínuo** — sinais analógicos do processo (como o potenciômetro descrito no material anterior) chegam por aqui e passam pela conversão A/D interna do CLP antes de serem processados.
- A **CPU com interfaces PROFIBUS e PROFINET** é, na prática, o elo que conecta esse CLP a um sistema de controle supervisório maior (SCADA/DCS) — o CLP individual é o dispositivo de "nível de processo" (nível 2), enquanto a rede que o conecta a outros CLPs e a um computador supervisório central corresponderia aos níveis 3 e 4 descritos na Seção 5.3.3.


## Pontos-chave para revisão

- [ ] Comparar os níveis de automação (1 a 5) nas indústrias de processo e nas indústrias de manufatura discreta.
- [ ] Diferenciar variável/parâmetro contínuo e discreto (binário, discreto não binário, e dado de pulso).
- [ ] Comparar controle contínuo e controle discreto quanto a medidas de saída, qualidade, variáveis, sensores, atuadores e constante de tempo típica.
- [ ] Explicar por que mesmo o "controle contínuo" moderno carrega características discretas (amostragem periódica).
- [ ] Diferenciar controle regulatório, *feedforward*, otimização em estado estacionário, controle adaptativo e estratégias de busca on-line.
- [ ] Explicar as três etapas do controle adaptativo (identificação, decisão, modificação).
- [ ] Diferenciar mudanças orientadas a eventos e orientadas a tempo, e relacioná-las a controle lógico e controle de sequência.
- [ ] Listar os cinco requisitos de controle de um sistema em tempo real.
- [ ] Explicar as quatro capacidades do controle por computador (polling, intertravamentos, sistema de interrupções, tratamento de exceções).
- [ ] Diferenciar monitoramento de processo, controle de malha aberta e controle de malha fechada.
- [ ] Explicar o Controle Digital Direto (DDC) e por que ele é considerado uma fase de transição histórica.
- [ ] Definir CLP, PAC e RTU, e diferenciá-los entre si.
- [ ] Descrever os quatro componentes de um sistema SCADA.
- [ ] Diferenciar SCADA e DCS (Sistema de Controle Distribuído).
- [ ] Relacionar a arquitetura modular do CLP Siemens S7-1500 (fonte, CPU, módulos de E/S digital e analógica) com os conceitos de controle discreto/contínuo e com a hierarquia de níveis de automação.

---


**Referências:** Mikell P. Groover, *Automation, Production Systems, and Computer-Integrated Manufacturing*, 5ª ed. — Cap. 5 (Seções 5.1 a 5.3) + *Roteiro de Prática de Laboratório de Automação: CLP*, UFABC (p. 4–8 — arquitetura de hardware do CLP Siemens S7-1500).

