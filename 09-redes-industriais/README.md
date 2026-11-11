# Redes Industriais

**Fontes:** transcrição das aulas "Redes 1" e "Redes 2" do Prof. Alexandre Acácio de Andrade; *Roteiro de Prática de Laboratório de Automação: CLP*, UFABC (p. 67–84, mais os trechos de configuração de IP e de hardware das p. 3–4 e 16–17); e o resumo de passos "Configurar CLP Siemens" (derivado da mesma apostila).

> **Sobre este material:** o livro do Groover não trata de redes industriais, então a base aqui é a aula do professor (conceitos) e a apostila (prática no kit Siemens S7-1500). A transcrição das aulas tem muitos erros de reconhecimento de voz, por isso reconstruí o conteúdo a partir dos conceitos que ficam claros e marquei com *(a confirmar)* o que depende de um trecho duvidoso. A apostila, por sua vez, **não explica a teoria de redes** — ela mostra como colocar CLP, IHM e inversor para conversar. Por isso as duas partes (teoria e prática) aparecem separadas.

---

## Parte I — Conceitos de redes de dados industriais

### 1. Por que existem redes industriais?

A rede industrial surgiu como uma evolução do **CLP** e hoje é algo comum e dominante na automação. O professor parte da **pirâmide de automação** (do nível 1, o chão de fábrica com os equipamentos de campo, até o nível 5, o da gestão):

- No **modelo tradicional**, cada nível só conversa com o nível vizinho (1 com 2, 2 com 3, e assim por diante).
- Na **Indústria 4.0**, a ideia é que a informação possa circular entre níveis não vizinhos, sem precisar passar por todos os intermediários.

**O problema do cabeamento convencional.** Na ligação tradicional, cada ponto de campo recebe o seu próprio cabo, ponto a ponto, até o controlador. Numa planta com milhares de pontos isso significa uma quantidade enorme de cabos, o que:

- encarece a instalação e a manutenção;
- gera muita interferência elétrica na área da planta;
- fica especialmente difícil em instalações fisicamente grandes. O exemplo do professor é uma **siderúrgica** (ele cita a CSN), com instalações de quilômetros de extensão.

**A solução: redes de campo (*fieldbus*).** Em vez de um cabo por ponto, vários dispositivos compartilham um mesmo meio de comunicação, com troca de dados nos dois sentidos. É um dos tipos mais tradicionais de rede industrial, e o professor menciona que já existem pelo menos dois padrões consolidados para esse tipo de rede.

### 2. Vantagens e desvantagens

| Vantagens | Desvantagens |
|---|---|
| **Redução drástica de cabeamento** e de custo de instalação | **Ponto único de falha:** com tudo no mesmo cabo, um problema físico derruba muita coisa de uma vez |
| **Manutenção mais simples** | **Alto investimento inicial** (equipamentos de rede, projeto) |
| **Flexibilidade** para expandir ou reorganizar | Exige **conhecimento específico** da tecnologia de rede escolhida |
| **Diagnóstico** mais fácil dos dispositivos conectados | **Interoperabilidade nem sempre é simples:** usar um padrão comum entre fabricantes não garante que a conexão seja "plug and play"; muitas vezes exige trabalho de integração |
| Possibilidade de conectar dispositivos de **fornecedores diferentes** | Na troca de dados, a rede pode ser **mais lenta que a ligação ponto a ponto** (que tem atraso praticamente nulo) |

**Exemplo do professor sobre o risco:** um caminhão bate num poste, ninguém se machuca, mas o poste cai e rompe o cabo de rede que passava ali. Se todo o processo dependia daquele cabo, o processo inteiro para. Esse é o preço de ter trocado "muitos cabos" por "um cabo só".

Sobre velocidade: a rede de campo costuma ter taxas altas, mas a ligação direta por fio tem atraso praticamente zero (a velocidade da eletricidade no cabo). Por isso a rede precisa ser avaliada pelo **tempo de resposta que o controle exige**. Se um sistema de controle precisa atualizar a cada 2 ms e a rede não consegue entregar nesse prazo, a rede passa a ser o problema do processo.

### 3. Modelo OSI e a "vida além da internet"

- Houve uma tentativa de **normatizar as redes** pelo **modelo OSI** (criado por órgão regulador na Europa), com uma arquitetura muito detalhada e em camadas. O professor conta que, na prática, essa arquitetura foi considerada cara e complexa demais e foi **abandonada como padrão de implementação**.
- O mercado acabou adotando redes baseadas em **TCP/IP**, desenvolvidas de forma colaborativa, e que se tornaram o padrão de fato.
- O modelo OSI continua útil como **forma de organizar o raciocínio em camadas**, mas o professor reforça: não é para decorar. Ele observa que redes industriais costumam usar **poucas camadas** do modelo (cerca de três, *a confirmar*), e que o detalhe do OSI está fora do escopo do curso.
- A **Ethernet** (originada no fim dos anos 1970) virou praticamente sinônimo de "rede" hoje, e há uma forte **tendência de a Ethernet dominar** tanto o meio físico quanto o protocolo, inclusive na indústria. Mas o professor alerta que **internet ≠ sinônimo de toda rede** e que existem redes específicas da automação industrial que não são Ethernet.
- Existe **Ethernet determinística**, adaptada para a indústria, ao lado da Ethernet comum (não determinística). Qual usar depende da aplicação.

### 4. Como escolher uma rede: perguntas fundamentais

1. **Distância:** até onde essa rede consegue chegar? A planta é pequena ou ocupa quilômetros?
2. **Meio físico:** cabo metálico, fibra óptica ou sem fio?
3. **Velocidade:** distinguir a velocidade **nominal** da velocidade **efetiva** (ver seção 7).
4. **Tipo de transmissão: determinística ou probabilística (estocástica).**
   - **Probabilística:** a internet tradicional. Não há garantia de que os pacotes cheguem na ordem enviada nem em um tempo fixo.
   - **Determinística:** a rede é programada para varrer os dispositivos num ciclo fixo (por exemplo, a cada 50 ms), e isso acontece sempre, na ordem certa. Muitas aplicações de automação **exigem** isso, como o acionamento de **servomotores**. Se o pacote de comando pudesse chegar atrasado ou fora de ordem, o processo poderia dar problema.

### 5. Meio físico

- **Ambiente industrial é "sujo" eletromagneticamente:** motores, inversores e grandes cargas geram muita interferência.
- **Cabos de par trançado** (os mesmos conhecidos da rede de computadores, com conector tipo RJ45) e **cabos coaxiais** (a aula cita que existem dois tipos de coaxial usados, em redes diferentes) são comuns.
- **Fibra óptica:** praticamente **imune a interferência eletromagnética**, robusta, usada onde o ambiente é muito ruidoso ou as distâncias são longas.
- **Sem fio (Wi-Fi, ZigBee, redes celulares):** é a "novidade", mas o professor a vê com cautela justamente pelo risco de interferência. Por outro lado, tem aplicações em que é quase imbatível, como medir o nível de um **tanque de petróleo de 50 m de altura**, onde passar um cabo até o topo é complicado.
- **Conectores:** existem muitos tipos além do RJ45. O exemplo citado é o da rede **DeviceNet**, que tem conector de cinco terminais com **cores dos fios padronizadas** (e marcações coloridas no conector) justamente para evitar erro de ligação. Existe ainda um conector do tipo "vampiro", que ao ser fechado perfura o cabo e faz a conexão sem precisar cortá-lo.

### 6. Topologias

| Topologia | Como é | Características citadas na aula |
|---|---|---|
| **Barramento** | Um cabo principal com derivações para os dispositivos | Muito usada em redes de campo. Limitada pela **distância total** e pelo **comprimento das derivações** (a aula cita derivações curtas, na casa de poucos metros) |
| **Estrela** | Um ponto central do qual saem cabos para cada dispositivo | Limitada em extensão; exemplo típico é a rede de computadores com switch |
| **Árvore** | Combinação de estrelas/barramentos | Permite crescer a rede em ramos |
| **Anel** | Dispositivos ligados em círculo | **Tolerante a falha:** se o cabo rompe em um ponto, a informação pode seguir pelo outro sentido do anel e a rede continua funcionando. É uma resposta direta ao problema do "ponto único de falha" |

### 7. Protocolos: o que observar

- **Taxa de transmissão útil:** nem tudo que trafega na rede é dado do processo. Há muita informação de **controle e gerenciamento** (confirmações, sincronismo, verificação de consistência, os chamados *handshakes*). A taxa **efetiva** é a que sobra para a informação útil, e é ela que mede a **eficiência** real da rede. Por isso a velocidade nominal da rede sozinha engana.
- **Determinismo:** ponto essencial na automação (ver seção 4).
- **Modos de comunicação:** **mestre-escravo** (um mestre pergunta e os escravos respondem) e **produtor-consumidor** (o dado é produzido uma vez e pode ser consumido por vários dispositivos). Há ainda o conceito de **tráfego periódico** (acontece em ciclo regular) e **aperiódico** (sob demanda), e de comunicação **centralizada** (um controlador comanda) ou **descentralizada** (vários participantes com autonomia).
- **Normas e protocolos abertos:** historicamente, muitos protocolos eram proprietários, criados pelos fabricantes para resolver o problema deles, o que atrapalhava quem queria ligar equipamentos de marcas diferentes. Quando o protocolo é **aberto** e normalizado, em teoria qualquer empresa ou pessoa pode ler a especificação e desenvolver um *driver* de comunicação. A aula cita que existem normas internacionais para redes de campo *(a confirmar quais; a família de normas IEC 61158/61784 é a referência usual — verifique no AVA)*.

### 8. Exemplos de redes citados na aula

O professor faz questão de dizer que isto é só um "menu degustação": o objetivo é entender os conceitos, e **não** decorar redes.

- **DeviceNet:** rede de dispositivos de campo, com o conector colorido descrito acima; usada para conectar sensores, válvulas, partidas de motor etc.
- **ControlNet:** rede determinística, associada à Rockwell/Allen-Bradley, com protocolo aberto, usada para comunicação entre controladores e em plantas grandes. Tem boa capacidade, e é possível "pendurar" nela redes de menor capacidade.
- **Foundation Fieldbus (H1 e H2):** o **H2 é baseado em Ethernet** e tem velocidade muito maior que o H1.
- **Protocolo CAN:** base de várias redes de campo.
- **Redes de segurança:** redes específicas para equipamentos de segurança (parada de emergência etc.). Os equipamentos costumam ser identificados por cor chamativa (vermelho ou laranja, dependendo do fabricante), justamente para chamar a atenção.
- **Redes antigas ainda vivas:** o professor cita redes seriais muito velhas (com mais de 20 anos) que continuam em uso porque são simples e, mesmo sem fibra óptica, alcançam **distâncias da ordem de quilômetros**, embora a **velocidade seja baixa**. Para muitas aplicações isso é suficiente. (O nome da rede não ficou claro na transcrição; é provável que seja o **Modbus**, *a confirmar*.)
- **Ethernet:** vai ocupando cada vez mais espaço e, nas palavras do professor, "vai tomar conta de tudo", mas lembrando que existem as versões não determinística e determinística.

### 9. Exemplo real contado pelo professor

Numa fábrica de múltiplos andares, ele montou uma **única rede DeviceNet** ligando válvulas espalhadas por andares diferentes, módulos de entradas/saídas remotas "inteligentes", motores e chaves de partida. O CLP central ficou num **rack pequeno, só com a CPU e o cartão de rede**, sem cartões de E/S locais: todo o resto estava no campo, conectado pela rede. Isso resume o ganho típico das redes industriais: **menos cabo, E/S distribuída e uma arquitetura mais enxuta**.

### 10. Conceitos fundamentais para guardar

1. **Existe vida além da internet:** a automação industrial tem redes e requisitos próprios.
2. É comum ter que **conectar equipamentos de fabricantes diferentes** e, portanto, redes diferentes.
3. A **Ethernet tende a se tornar hegemônica**, inclusive em versão determinística.
4. A escolha da rede é um problema de **engenharia**: distância, meio físico, velocidade efetiva, determinismo e tempo de resposta exigido pelo controle.

---

## Parte II — Prática de laboratório: rede entre CLP, IHM e inversor (apostila)

A apostila mostra, no kit Siemens S7-1500 com TIA Portal, como colocar **CLP, IHM e inversor de frequência** na mesma rede e comandar o motor a partir do programa Ladder.

### 11. A rede do laboratório

- O **PC** se comunica com o **CLP** por **cabo Ethernet** na porta **X1** da CPU. Para ligar o CLP a **todos os equipamentos** (IHM e inversor), usa-se o **switch** que fica na maleta.
- A CPU (1516F-3 PN/DP) tem interfaces **PROFINET e PROFIBUS** integradas. Módulos **descentralizados** (fora do rack do CLP) são conectados à CPU por PROFINET e/ou PROFIBUS, e é por esse caminho que se configuram endereços IP de módulos de comunicação.
- O TIA Portal é o software que configura, programa, monitora e comanda o CLP. Ele faz **download** (PC → CLP), **upload** (CLP → PC) e monitoração em tempo real, tudo sobre essa conexão de rede.

**Endereçamento IP (conforme a apostila):**

| Item | Valor de exemplo |
|---|---|
| IP do computador | 192.168.0.99 |
| Máscara de sub-rede | 255.255.255.0 |
| Gateway | pode ficar em branco |
| IP do CLP, da IHM e do inversor | 192.168.0.X, com **X diferente** para cada equipamento |

**Regras de ouro:** todos precisam estar **na mesma sub-rede** (192.168.0.X) e **não pode haver dois dispositivos com o mesmo IP**, senão há conflito.

### 12. Passo a passo resumido de configuração

Com base no resumo "Configurar CLP Siemens" e na apostila:

1. **Configurar o IP do PC:** Central de Rede → Alterar configurações do adaptador → adaptador usado para o CLP → Propriedades → Protocolo IP versão 4 (TCP/IPv4), usando os valores acima.
2. **Criar o projeto no TIA Portal** (nome e pasta) e, de preferência, trabalhar na **Project view**.
3. **Adicionar a CPU** (CPU 1516-3 PN/DP) escolhendo a **mesma versão de firmware** indicada no display do CLP.
   > ⚠️ Se a versão do projeto for diferente da instalada, o CLP entra em atualização de firmware, que pode levar mais de uma hora e **não pode ser interrompida**, senão o CLP pode ficar inutilizado.
4. **Adicionar os módulos** na mesma ordem física da maleta, conferindo o código de cada um.
5. **Atribuir o IP à CPU:** Online & Diagnostics → Accessible Devices → *Update accessible devices* → Functions → **Assign IP address**, informando IP e máscara na sub-rede do PC. O IP também aparece no display da CPU.
6. **Programar** (Ladder/FBD) e fazer o **Download**; depois do download o CLP entra em RUN e é possível ativar o monitoramento em tempo real.
7. O **inversor** também recebe IP: no projeto, dê duplo clique na porta de comunicação ("Ethernet Addresses") e siga o mesmo padrão de endereços.

### 13. Configuração do inversor de frequência (apostila, p. 67–72)

Na tela de configuração do inversor, os principais ajustes do roteiro são:

- **Fontes de setpoint/comando:** configuração de I/O atual em *[7] Fieldbus with data set changeover* e, no padrão, *[12] Standard I/O with analog setpoint*.
- **Drive:** padrão *[2] NEMA motor (60 Hz, SI units)*; aplicação da unidade de potência *[1] Load duty cycle with low overload for vector drives*.
- **Motor:** dados inseridos manualmente (a **etiqueta do motor** tem todos os valores), tipo *[1] induction motor*, ligação em **Delta**.
- **Funções do drive:** identificação do motor em repouso (*[2] identifying motor data (at standstill)*), com cálculo completo.
- No fim aparece um resumo; depois é só fazer **Download to device**. Ao acionar o motor pela primeira vez, o inversor reconhece o motor e executa testes.

### 14. Colocando os dispositivos na rede no TIA Portal (p. 72–76)

1. Na Project view, abra **Devices & networks**. Todos os equipamentos adicionados aparecem ali.
2. **Interligue** os equipamentos arrastando do **quadradinho verde** (porta de rede) da IHM e do inversor até o quadradinho verde do CLP. Repita para cada dispositivo.
3. No bloco do inversor, clique em **"Not assigned"** e escolha o CLP ao qual ele está ligado; depois, conforme a apostila, desmarque a opção *PLC1-Profinet* na linha de conexão.
4. Faça **Download to device**.

**Technology object (objeto de tecnologia):** serve de "ponte" para o inversor receber comandos dos blocos do CLP.

1. *Add new object* → **Motion Control** (no laboratório, *S7-1500 Motion Control*) → **TO_SpeedAxis** (numeração em automático).
2. Em *Hardware interface*, selecione o **Drive** do inversor e entre em *Data Exchange*.
3. Defina **velocidade de referência** e **velocidade máxima**: no kit do laboratório, **ambas em 1800**.
4. De volta à Project view: *Drive_1 → Parameter → "Drive connected to a SIMATIC motion control axis"*, e em *Motion configuration* coloque a **mesma velocidade de referência** do TO_SpeedAxis.

### 15. Comandando o inversor pelo Ladder (p. 77–84)

Na janela de instruções do Ladder, em **Technology → Motion Control**, aparece o novo objeto, com dois blocos principais:

- **MC_Power:** **habilita** o inversor (no exemplo, ao receber verdadeiro de `%I0.0`, "Ligar Inversor", liga o motor associado ao `SpeedAxis_1`).
- **MC_MoveVelocity:** controla **velocidade e sentido**. No exemplo, um pulso na memória `M10.1` executa o bloco, que lê o valor da palavra de memória `MW90` e o envia como **setpoint** ao inversor (o valor de MW90 representa aproximadamente a tensão a ser enviada).

O programa é escrito em **Main [OB1]** (duplo clique em *Main* sob a CPU).

**Exemplo 1 — motor com 4 velocidades.** Ligações físicas do kit: `I0.0` pela chave S4; `I0.1` a `I0.4` pelos botões S0 a S3. A lógica:

- primeira rede: energização do módulo do motor;
- redes 2 a 5: as velocidades **250, 500, 750 e 1000 rpm**;
- rede 6: parada (**0 rpm**);
- rede 7: bloco de velocidade, que altera a velocidade do inversor e grava a informação;
- rede 8: um **clock** (pulso) que dispara a execução do bloco de velocidade.

**Exemplo 2 — velocidade pelo potenciômetro.** O potenciômetro é alimentado com **10 Vcc** e sua saída vai à entrada analógica **AI0**. O projeto grava a velocidade **só quando se dá um pulso** (botão): girar o potenciômetro não muda a velocidade na hora, só ao pressionar o botão. Ligações: `I0` por S0 (24 Vcc) e `I1` pela chave S4 (24 Vcc).

**Escala analógica.** A faixa nominal do sinal analógico (0–10 V, ou 0–20 mA) é digitalizada pelo CLP de **0 a 27648**, e cada canal ocupa uma palavra inteira (*Int*). Por isso o programa usa **blocos de divisão** para converter o valor:

- Rede 3: divisão por **17**, para obter as rotações do motor (limite de 1700 rpm), valor que pode ser mostrado na IHM;
- Rede 4: divisão por **277**, para obter o nível de tensão em **percentual**.

> Observação de estudo: a apostila usa essas divisões como aproximação. Dividindo o fundo de escala (27648) por 17 se obtém cerca de 1626, não exatamente 1700; se for usar isso em trabalho ou prova, vale conferir o fator com o professor.

### 16. Busca de erros (p. 80–82)

- O TIA Portal tem **Help → Show help**, onde se pesquisa pelo **código do erro**.
- **Erro típico:** `r807.0` / mensagem **"The Master control is active"**. Aparece quando o inversor foi colocado em funcionamento pelo **painel do próprio kit**, o que o faz **assumir o papel de mestre**. Isso atrapalha quando o CLP deve ser o mestre da rede.
- **Solução:** em *DRIVE_1 → Parameters → Functional View*, **estando online**, alterar o estado da função **Master control active**.

---

## Conectando as duas partes

- A **pirâmide de automação** da aula é exatamente a hierarquia vista no Cap. 4.3 do Groover (documento 05): a rede é o que conecta os níveis.
- O **SCADA** do Cap. 5.3 depende de uma **rede de comunicação** ligando o computador supervisório aos CLPs e RTUs; é o assunto desta aula aplicado a esse contexto.
- No laboratório, a **rede Ethernet/PROFINET** ligando PC, CLP, IHM e inversor é a materialização do tema: um **barramento de campo simplificado**, com switch, IPs numa mesma sub-rede e dispositivos de fabricantes/funções diferentes conversando entre si.
- O **determinismo** discutido na aula é o que justifica usar o objeto de tecnologia de *Motion Control* para comandar o inversor: o comando de velocidade precisa chegar de forma previsível.

---

## Pontos-chave para revisão

- [ ] Explicar a pirâmide de automação e a diferença entre comunicação "nível a nível" e a proposta da Indústria 4.0.
- [ ] Explicar o problema do cabeamento convencional e como as redes de campo (*fieldbus*) o resolvem.
- [ ] Listar vantagens e desvantagens das redes industriais, incluindo o **ponto único de falha** e a questão da **interoperabilidade**.
- [ ] Explicar por que o modelo OSI não virou padrão de implementação e por que o TCP/IP/Ethernet predomina.
- [ ] Diferenciar redes **determinísticas** e **probabilísticas**, e dar um exemplo de aplicação que exige determinismo.
- [ ] Diferenciar velocidade **nominal** e taxa **efetiva/útil**.
- [ ] Comparar meios físicos (par trançado, coaxial, fibra, sem fio) frente à interferência eletromagnética.
- [ ] Comparar as topologias (barramento, estrela, árvore, anel) e explicar por que o anel é tolerante a falha.
- [ ] Diferenciar mestre-escravo e produtor-consumidor, e tráfego periódico e aperiódico.
- [ ] Descrever o exemplo do professor (rede DeviceNet com E/S remota) e o que ele ilustra.
- [ ] Configurar corretamente PC e CLP na mesma sub-rede (IP, máscara) e evitar IPs duplicados.
- [ ] Explicar o risco de usar versão de firmware diferente ao adicionar a CPU no projeto.
- [ ] Descrever como interligar CLP, IHM e inversor em *Devices & networks* e para que serve o *Technology object* (TO_SpeedAxis).
- [ ] Explicar a função dos blocos **MC_Power** e **MC_MoveVelocity**.
- [ ] Explicar a escala analógica 0–27648 e como o valor é convertido por blocos de divisão.
- [ ] Reconhecer o erro "Master control is active" e como resolvê-lo.