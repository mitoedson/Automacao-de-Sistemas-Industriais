# Sensores e Atuadores


> Este material cobre a base teórica de sensores, atuadores e conversão analógico-digital do Groover, com um complemento prático de como isso aparece de fato no laboratório da disciplina (ligação de um potenciômetro a uma entrada analógica do CLP).


## 1. Sensores (Cap. 6.1)

Para implementar automação e controle de processos, o computador de controle precisa coletar dados do processo e enviar sinais de volta a ele. Como o computador digital opera com dados binários, mas o processo físico costuma gerar dados contínuos (analógicos), a interface computador-processo exige três componentes: **sensores** (para medir variáveis), **atuadores** (para acionar parâmetros) e **dispositivos de conversão** entre sinais analógicos e digitais.

### 1.1 O que é um sensor

Um **sensor** é um tipo de **transdutor** — um dispositivo que converte uma variável física de uma forma em outra forma mais útil para a aplicação. Especificamente, um sensor converte um estímulo físico de interesse (temperatura, força, pressão, deslocamento etc.) em uma forma mais conveniente para medição — geralmente uma grandeza elétrica, como tensão.

### 1.2 Classificação dos sensores

- **Por categoria do estímulo medido** — mecânico (posição, velocidade, força, pressão etc.), elétrico (tensão, corrente, resistência), térmico (temperatura, calor), radiação, magnético, químico (concentração, pH).
- **Analógico vs. discreto** — um **sensor analógico** produz um sinal contínuo (ex.: tensão) que varia de forma análoga à variável medida (exemplos: termopares, extensômetros, potenciômetros); o sinal precisa passar por um **conversor analógico-digital (ADC)** antes de ser usado por um computador digital. Um **sensor discreto** produz uma saída que só pode assumir determinados valores, dividido em:
  - **Binário** — produz um sinal liga/desliga (ex.: chaves de fim de curso, sensores fotoelétricos, chaves de proximidade).
  - **Digital** — produz uma saída digital, seja como bits de status em paralelo (ex.: matriz de sensores fotoelétricos), seja como uma série de pulsos contáveis (ex.: encoder óptico).
- **Ativo vs. passivo** — um **sensor ativo** responde ao estímulo sem precisar de energia externa (ex.: termopar, que gera uma pequena tensão proporcional à temperatura). Um **sensor passivo** precisa de uma fonte externa de energia para operar (ex.: termistor, que precisa de corrente elétrica passando por ele para que sua resistência — relacionada à temperatura — possa ser medida).
- **Microsensores** — sensores miniaturizados, com dimensões da ordem de mícrons, geralmente fabricados em silício com técnicas de manufatura de circuitos integrados.

### 1.3 Função de transferência

Todo sensor tem uma **função de transferência**: a relação entre o valor do estímulo físico e o valor do sinal de saída. De forma geral: **S = f(s)**, onde S é o sinal de saída e s é o estímulo.

- Para sensores binários: S = 1 se s > 0, e S = 0 se s ≤ 0.
- Para sensores analógicos ideais, a relação costuma ser proporcional: **S = C + m·s**, onde C é o valor de saída quando o estímulo é zero, e m é a constante de proporcionalidade (a **sensibilidade** do sensor). Por exemplo, um termopar padrão gera cerca de 40,6 microvolts por grau Celsius.

Antes de usar qualquer sensor, ele precisa ser **calibrado** para determinar sua função de transferência (ou a inversa dela).

### 1.4 Características desejáveis de um sensor

Alta exatidão, alta precisão, ampla faixa de operação, alta velocidade de resposta, facilidade de calibração, mínima deriva (perda gradual de exatidão ao longo do tempo), alta confiabilidade, e baixo custo. Poucos sensores atingem nota máxima em todos esses critérios ao mesmo tempo — cabe ao engenheiro de controle decidir quais características são mais importantes para cada aplicação.


## 2. Atuadores (Cap. 6.2)

Um **atuador** é o dispositivo de hardware que converte um sinal de comando do controlador em uma mudança em um parâmetro físico — geralmente mecânico (posição, velocidade). Assim como o sensor, o atuador também é um transdutor (converte um tipo de grandeza física, como corrente elétrica, em outro tipo, como velocidade rotacional de um motor).

### 2.1 Três categorias de atuadores

1. **Elétricos** — os mais comuns; incluem motores elétricos de vários tipos, solenoides e relés eletromecânicos. Podem ser lineares (deslocamento linear) ou rotacionais (deslocamento angular).
2. **Hidráulicos** — usam fluido hidráulico para amplificar o sinal de comando; indicados quando são necessárias forças grandes.
3. **Pneumáticos** — usam ar comprimido; por trabalharem com pressões relativamente baixas, costumam ser limitados a aplicações de força mais baixa comparados aos hidráulicos.

### 2.2 Motores elétricos

- **Motores DC** — alimentados por corrente e tensão constantes. O campo magnético alternante é obtido por um **comutador** que gira com o rotor. O **servomotor DC** é o tipo mais usado em automação: o termo "servo" indica que existe uma malha de realimentação regulando a velocidade. A relação entre torque e corrente é dada por **T = K_t·I_a** (torque = constante de torque × corrente de armadura). Quando o rotor começa a girar, ele gera uma tensão contrária chamada **força contra-eletromotriz (back-emf)**, proporcional à velocidade angular (E_b = K_v·ω), que reduz a corrente disponível — por isso o torque cai conforme a velocidade aumenta (a chamada curva torque-velocidade). Um **motor DC brushless** elimina o comutador e as escovas usando circuitos de estado sólido, reduzindo problemas de manutenção e a inércia do rotor.
- **Motores AC** — não usam escovas e são compatíveis com a energia elétrica predominante na indústria (corrente alternada). Dividem-se em **síncronos** (o rotor gira exatamente sincronizado com a frequência da rede, mas precisa de um dispositivo auxiliar para dar a partida) e **de indução** (os mais usados no mundo, por serem simples e baratos de fabricar; o campo magnético é induzido no rotor, sem necessidade de conexão elétrica externa a ele). Ambos operam em velocidade praticamente constante — para variar a velocidade, usa-se um **inversor de frequência** (*variable-frequency drive*), já que a velocidade do motor é proporcional à frequência da corrente aplicada.
- **Motores de passo (stepper motors)** — giram em incrementos angulares discretos (passos), cada um acionado por um pulso elétrico. O ângulo de passo é dado por **α = 360°/n_s** (onde n_s é o número de passos do motor), e o ângulo total percorrido é **A_m = n_p·α** (onde n_p é o número de pulsos recebidos). Operam em dois modos: **passo travado** (*locked-step*, cada pulso resulta em um passo discreto, com possibilidade de parar/reverter a qualquer momento) e **deslizante** (*slewing*, rotação praticamente contínua em velocidades mais altas). São usados em sistemas de malha aberta com exigências moderadas de torque — máquinas-ferramenta, robôs industriais, plotters, instrumentos científicos, e até relógios de quartzo analógicos.
- **Motores lineares** — produzem movimento linear diretamente, sem precisar de conversão rotação→linear. Funcionam como um motor rotativo "desenrolado": o estator vira uma pista reta com ímãs, e o rotor (chamado de *forcer*) desliza sobre ela. Existem em três estilos: plano, em canal U, e cilíndrico.

**Conversão de movimento rotativo em linear** — quando se usa um motor rotativo comum para gerar movimento linear, os mecanismos mais usados são: **fusos (parafusos de avanço ou esferas)**, **sistemas de polias** (com correia, corrente ou cabo), e **cremalheira e pinhão**. Fusos são os mais comuns em máquinas-ferramenta e robôs industriais.

### 2.3 Outros tipos de atuadores

- **Solenoides** — atuadores eletromagnéticos que convertem energia elétrica em movimento mecânico de curto curso (linear ou rotacional limitado). Um solenoide linear tem um êmbolo móvel dentro de uma bobina fixa; quando energizada, a bobina vira um eletroímã que puxa o êmbolo, e uma mola o retorna quando a corrente é cortada. Usado em fechaduras eletrônicas e válvulas de controle pneumáticas/hidráulicas.
- **Relés eletromecânicos** — uma chave elétrica liga/desliga, composta por uma bobina estacionária e um braço móvel que abre/fecha um contato elétrico por meio de um campo eletromagnético. Permitem chavear remotamente circuitos de alta corrente/tensão usando um sinal de baixa corrente. Foram amplamente usados para definir lógica de controle em equipamentos de manuseio de materiais e linhas de produção, mas sistemas modernos tendem a usar **CLPs** em vez de relés, por serem mais baratos (em aplicações complexas) e reprogramáveis.
- **Atuadores hidráulicos e pneumáticos** — o **cilindro** é o dispositivo linear mais comum (de ação simples com retorno por mola, ou de dupla ação). Para cilindros hidráulicos (fluido incompressível), a velocidade do pistão é **v = Q/A** e a força aplicada é **F = p·A** (onde Q é a vazão, A é a área da seção do cilindro, e p é a pressão do fluido). Sistemas hidráulicos trabalham com pressões bem mais altas (~20 MPa) que sistemas pneumáticos (~0,7 MPa), aplicam forças maiores, e permitem controle de velocidade mais preciso — mas custam de 5 a 10 vezes mais que equipamentos pneumáticos equivalentes, e vazamentos são um risco de segurança. Sistemas pneumáticos, por sua vez, são mais baratos e permitem acionamento em alta velocidade, mas são mais difíceis de controlar com precisão (por causa da compressibilidade do ar).


## 3. Complemento de aprendizagem — Instrumentação Aplicada

A apostila **Instrumentação Aplicada**, de Álysson Raniere Seidel (UFSM/CTISM, 2011), amplia os conceitos de sensores apresentados anteriormente e aproxima a teoria de situações típicas de automação industrial. O material aborda tecnologia e seleção de sensores, além da medição de temperatura, pressão, vazão e nível. 

### 3.1 Do sensor à instrumentação industrial

Na automação, o sensor não deve ser visto apenas como um componente que "detecta" alguma coisa. Ele participa de uma cadeia de medição: uma grandeza física é percebida, convertida em um sinal e posteriormente interpretada pelo sistema de controle. A apostila destaca que a instrumentação é fundamental para medir e processar variáveis que não podem ser acompanhadas adequadamente apenas pelos sentidos humanos. 

Isso complementa a ideia de **função de transferência** apresentada no Groover: conhecer um sensor significa compreender não apenas o que ele mede, mas também **como a grandeza física aparece em sua saída**.

### 3.2 Principais tecnologias de sensores

A apostila apresenta diferentes princípios físicos que podem ser usados para transformar uma grandeza em um sinal mensurável:

- **Resistivos** — a variável medida provoca uma alteração de resistência; aparecem, por exemplo, em termorresistências, termistores e extensômetros (*strain gauges*).
- **Capacitivos** — alterações na capacitância podem indicar proximidade, nível, umidade, pressão ou deslocamento.
- **Magnéticos/indutivos** — utilizam alterações de indutância, relutância ou correntes parasitas para detectar movimento ou a presença de materiais metálicos.
- **Efeito Hall** — produz uma tensão relacionada à presença de um campo magnético e pode ser usado para detectar posição, movimento ou velocidade.
- **Piezoelétricos** — determinados materiais produzem tensão quando submetidos a uma força; são utilizados em medições de força, pressão, aceleração e aplicações ultrassônicas.
- **Ópticos** — utilizam uma fonte de luz e um elemento detector para identificar presença, movimento ou outras condições do processo.
- **Ultrassônicos** — determinam a presença ou distância de objetos a partir da emissão e recepção de ondas acústicas e da análise do eco. 

> **Ideia central:** o mesmo objetivo — por exemplo, detectar a presença de um objeto — pode ser realizado por tecnologias diferentes. A escolha depende do material do objeto, distância, ambiente, velocidade, precisão, custo e demais requisitos da aplicação.

### 3.3 Sensores discretos e contínuos

Um complemento importante à classificação do Groover é a distinção usada na instrumentação industrial entre **sinais discretos** e **sinais contínuos**. Sensores discretos indicam estados como presença/ausência ou ligado/desligado. Sensores contínuos acompanham uma grandeza ao longo de uma faixa de valores, fornecendo uma saída proporcional ou relacionada à variável medida. 

Essa distinção é especialmente importante quando o sensor será conectado a um **CLP**:

- uma chave fim de curso pode informar apenas **0 ou 1**;
- um sensor de temperatura pode fornecer um valor que varia continuamente;
- um encoder pode gerar pulsos que representam movimento ou posição;
- um transmissor industrial pode fornecer sinais padronizados, como **4–20 mA** ou **0–10 V**.

### 3.4 Seleção do sensor: não existe sensor universal

A seleção correta do sensor influencia diretamente o custo e o sucesso da implementação de um processo automatizado. Por isso, não basta perguntar "qual sensor mede esta grandeza?". É necessário perguntar **qual tecnologia é mais adequada às condições reais da aplicação**. 

Alguns critérios práticos são:

1. **O que será medido?** — temperatura, posição, pressão, nível, vazão, força etc.
2. **O objeto ou meio é compatível com a tecnologia?** — um sensor indutivo, por exemplo, é apropriado para alvos metálicos.
3. **É necessária medição contínua ou apenas detecção?**
4. **Existe contato físico?** — o contato pode reduzir velocidade e vida útil em determinadas aplicações.
5. **Qual é a distância de detecção ou faixa de medição?**
6. **Quais são as condições ambientais?** — temperatura, poeira, umidade, vibração, iluminação e interferência eletromagnética.
7. **Qual é o sinal de saída e como o controlador irá recebê-lo?**
8. **Qual é a precisão, velocidade e confiabilidade necessárias?**

Nos sensores de proximidade, por exemplo, a apostila diferencia **distância sensora nominal**, **distância sensora operacional**, **alvo padrão** e **histerese**. A histerese é particularmente importante porque evita oscilações indesejadas da saída quando há pequenas vibrações próximas ao ponto de acionamento. 

### 3.5 Medição de temperatura

A instrumentação de temperatura apresenta várias tecnologias, entre elas **termopares, RTDs, termistores e circuitos integrados**. A escolha depende da faixa de temperatura, precisão, resposta, ambiente e custo. A apostila também enfatiza que diferentes sensores produzem diferentes compromissos entre desempenho e custo. 

Um exemplo interessante é o **termopar**, que utiliza o efeito termoelétrico para produzir uma tensão relacionada à diferença de temperatura. Já os **RTDs** exploram a variação da resistência elétrica com a temperatura, enquanto os **termistores** apresentam uma variação mais acentuada da resistência.

### 3.6 Medição de pressão, vazão e nível

A instrumentação não se limita a sensores de proximidade. Em processos industriais, é necessário acompanhar variáveis como:

- **Pressão** — pode ser medida por dispositivos como tubo de Bourdon e sensores capacitivos, indutivos, piezorresistivos ou piezoelétricos.
- **Vazão** — pode ser determinada por diferentes princípios, incluindo medidores volumétricos, eletromagnéticos e de turbina.
- **Nível** — permite acompanhar a quantidade de material armazenado em tanques e pode ser realizado de forma **contínua** ou **discreta**. 

Essas variáveis ajudam a perceber uma ideia fundamental da automação: **o sensor deve ser escolhido em função do processo**, e não apenas da grandeza que se deseja medir.

### 3.7 Aplicação prática: sensor → CLP → atuador

Considere uma esteira transportadora que precisa detectar uma peça metálica e acionar um motor ou cilindro:

**Peça metálica → sensor indutivo → entrada do CLP → lógica do programa → saída do CLP → atuador**

O sensor indutivo detecta o metal sem contato físico; sua saída é interpretada pelo CLP; o programa decide a ação; e uma saída do controlador comanda o atuador. Sensores indutivos são particularmente adequados para detectar elementos metálicos e podem apresentar alta frequência de comutação e boa durabilidade. 

Esse exemplo conecta diretamente os três conceitos deste material: **sensor**, **controlador** e **atuador**. Quando a informação do sensor é analógica, acrescenta-se ainda a etapa de conversão A/D discutida na seção seguinte.

### 3.8 Atividade de aprendizagem — escolha do sensor

Para cada situação, indique **qual tecnologia de sensor escolheria e por quê**. Considere o princípio físico, o tipo de sinal e as condições da aplicação.

1. Detectar uma peça de aço passando por uma esteira sem tocar nela.
2. Medir continuamente o nível de água em um tanque.
3. Detectar a passagem de um objeto independentemente de sua cor, em um ambiente com pouca iluminação.
4. Medir a temperatura de um processo industrial.
5. Detectar a posição angular de um eixo de motor.
6. Medir a deformação de um eixo submetido a esforço mecânico.
7. Detectar a presença de um líquido dentro de um recipiente.

**Pergunta de reflexão:** se dois sensores conseguem realizar a mesma tarefa, quais características fariam você escolher um em vez do outro?


## 4. Conversões Analógico-Digitais (Cap. 6.3)

Sinais analógicos contínuos vindos do processo precisam ser convertidos em valores digitais para uso do computador — e dados digitais gerados pelo computador precisam ser convertidos de volta em sinais analógicos para acionar atuadores analógicos.

### 4.1 Conversor Analógico-Digital (ADC)

O processo de converter um sinal analógico em forma digital passa por cinco etapas de hardware:

1. **Sensor/transdutor** — gera o sinal analógico.
2. **Condicionamento de sinal** — filtragem de ruído e/ou conversão de uma forma de sinal em outra (ex.: corrente em tensão).
3. **Multiplexador** — um dispositivo de chaveamento que compartilha um único ADC entre vários canais de entrada, evitando o custo de um ADC dedicado para cada canal.
4. **Amplificador** — ajusta a escala do sinal recebido para compatibilizá-lo com a faixa do ADC.
5. **ADC propriamente dito** — converte o sinal analógico recebido em seu equivalente digital.

O ADC realiza a conversão em três passos: **amostragem** (converter o sinal contínuo em uma série de sinais discretos, em intervalos periódicos), **quantização** (atribuir cada sinal discreto a um dos níveis de amplitude previamente definidos) e **codificação** (converter os níveis discretos de amplitude em código digital binário).

**Fatores relevantes na escolha de um ADC:**

- **Taxa de amostragem** — quanto maior, mais fielmente o sinal contínuo é aproximado. Quando os sinais são multiplexados, a taxa de amostragem máxima por canal é a taxa máxima do ADC dividida pelo número de canais.
- **Tempo de conversão** — intervalo entre a chegada do sinal e a determinação do valor digital; depende do número de bits n usado — mais bits significam mais tempo de conversão, mas melhor resolução.
- **Resolução** — a precisão com que o sinal analógico é avaliado. O número de níveis de quantização é **N_q = 2ⁿ** (n = número de bits), e a resolução é dada por **R_ADC = L / (2ⁿ − 1)**, onde L é a faixa de escala total do ADC (geralmente 0–10 V). O **erro de quantização** máximo é de ± metade da resolução (± ½ R_ADC).
- **Método de conversão** — o mais comum é o **método de aproximações sucessivas**: uma série de tensões de teste conhecidas é sucessivamente comparada ao sinal de entrada desconhecido — a primeira tensão de teste é metade da escala total do ADC, e cada tensão seguinte é metade da anterior; cada comparação gera um bit (1 se a entrada excede a tensão de teste, 0 caso contrário), até formar o valor codificado completo.

### 4.2 Conversor Digital-Analógico (DAC)

Processo inverso do ADC: transforma a saída digital do computador em um sinal contínuo para acionar um atuador analógico. Duas etapas:

1. **Decodificação** — o valor digital do computador é convertido em uma série de valores analógicos em instantes discretos de tempo, através de um registrador binário que controla uma tensão de referência (cada bit sucessivo controla metade da tensão do bit anterior).
2. **Retenção de dados (*data holding*)** — cada valor sucessivo é transformado em um sinal contínuo (geralmente tensão) durante o intervalo de amostragem, para acionar o atuador analógico. O método mais comum é a **retenção de ordem zero** (*zero-order hold*), em que a tensão de saída é uma sequência de sinais em degrau (constante dentro de cada intervalo). A **retenção de primeira ordem** (*first-order hold*), menos comum, aproxima melhor o envelope real do sinal, variando a tensão com uma inclinação constante calculada a partir dos dois valores anteriores.

---

## 5. Complemento Prático — Entradas e Saídas do CLP (Apostila de Laboratório, p.12)

A apostila do laboratório traz, de forma bem concreta, exatamente a distinção entre sinais discretos e analógicos discutida acima, já aplicada ao hardware real usado nas aulas práticas (CLP Siemens S7-1500):

> **Entradas e saídas digitais** são aquelas que possuem apenas dois resultados: 0 (equivalente a 0V) e 1 (equivalente a 24V) — o exemplo de sensor **binário** da teoria do Groover, na prática.
>
> **Entradas e saídas analógicas** podem variar passo a passo dentro de um gradiente — por exemplo, de 4 a 20 mA, ou de 0 a 10 V — exatamente o tipo de sinal contínuo que precisa passar por um ADC antes de ser lido pelo CLP.

**Ligação das réguas de bornes do painel didático:** as entradas e saídas digitais devem ser ligadas à fonte de 24V; já os **potenciômetros são ligados à fonte de 10V**. No exemplo de bancada descrito na apostila, duas chaves liga/desliga acionam as duas primeiras entradas digitais, e **um potenciômetro controla o valor da primeira entrada analógica**.

Esse potenciômetro é, na prática, o próprio exemplo de **sensor analógico de posição** citado pelo Groover na Seção 6.1 (junto com termopares e extensômetros): ao girar o eixo do potenciômetro, sua resistência varia, e essa variação é traduzida em uma tensão contínua entre 0 e 10V. Essa tensão entra pelo cartão de entradas analógicas do CLP (que no hardware do laboratório tem 8 entradas e 4 saídas analógicas), passa pelo **ADC interno do controlador**, e é convertida em um valor digital que o programa do CLP consegue interpretar e usar — por exemplo, para controlar a velocidade de um motor de forma proporcional à posição do potenciômetro. É exatamente o fluxo sensor → condicionamento → ADC descrito na Seção 6.3, só que já dentro do hardware específico usado na disciplina.


## Pontos-chave para revisão

- [ ] Definir sensor e atuador como tipos de transdutor.
- [ ] Classificar sensores por tipo de estímulo, por analógico/discreto (e binário/digital dentro de discreto), e por ativo/passivo, com exemplos de cada.
- [ ] Explicar a função de transferência de um sensor e o papel da sensibilidade (m).
- [ ] Listar as três categorias de atuadores (elétricos, hidráulicos, pneumáticos) e quando cada uma é preferível.
- [ ] Diferenciar motores DC, AC (síncronos e de indução), motores de passo e motores lineares — princípio de funcionamento e aplicações típicas.
- [ ] Explicar os três mecanismos de conversão de movimento rotativo em linear (fuso, polias, cremalheira e pinhão).
- [ ] Diferenciar cilindros hidráulicos e pneumáticos, e calcular velocidade/força de um cilindro hidráulico a partir da vazão e pressão.
- [ ] Descrever as cinco etapas do processo de conversão analógico-digital (sensor, condicionamento, multiplexador, amplificador, ADC) e os três passos internos do ADC (amostragem, quantização, codificação).
- [ ] Calcular resolução e erro de quantização de um ADC a partir do número de bits e da faixa de escala.
- [ ] Explicar o método de aproximações sucessivas de um ADC.
- [ ] Diferenciar retenção de ordem zero e de primeira ordem em um DAC.
- [ ] Relacionar a entrada analógica via potenciômetro do laboratório (apostila, p.12) com o conceito de sensor analógico e o fluxo de conversão A/D do Groover.

---

**Referências:** Mikell P. Groover, *Automation, Production Systems, and Computer-Integrated Manufacturing*, 5ª ed. — Cap. 6 (Seções 6.1 a 6.3) + *Roteiro de Prática de Laboratório de Automação: CLP*, UFABC (p. 12 — entradas e saídas do CLP Siemens S7-1500) +  Instrumentação Aplicada, de Álysson Raniere Seidel (UFSM/CTISM, 2011).

