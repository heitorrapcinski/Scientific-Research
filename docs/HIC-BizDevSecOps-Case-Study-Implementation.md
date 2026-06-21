# Implementação do estudo de caso HADS: protocolo de pesquisa e blueprint técnico

> *Operacionalização do artefato Human–Agent Duties Separation (HADS) proposto no position paper "HADS: separação de funções humano–agente como caminho para o HIC prosperar no BizDevSecOps".*
>
> *Documento de implementação — protocolo de pesquisa (Design Science Research) + especificação técnica do artefato.*

---

## Sumário executivo

O position paper que antecede este documento estabeleceu uma tese — *o High-Impact Individual Contributor (HIC) dissolve o atrito inter-silos do BizDevSecOps mas reintroduz o problema sob forma intra-pessoal e de governança* — e propôs, como resposta, construir e testar via **Design Science Research (DSR)** um artefato de **Separação de Funções Humano–Agente (HADS)**. O paper, porém, parou na agenda: definiu *o que* testar (proposições P1–P6 e P-leadership) sem especificar *como* instanciar e avaliar.

Este documento preenche essa lacuna em duas frentes complementares, conforme decidido no escopo:

1. **Protocolo de pesquisa** — o desenho operacional do quase-experimento comparativo A/B/C combinado a estudo de caso múltiplo: seleção de casos, instrumentos de medição (DORA de cinco métricas, SPACE por survey + telemetria, auditoria de SoD, entrevistas), estratégia de avaliação **FEDS**, plano de análise estatística e qualitativa, cronograma e ameaças à validade com mitigações.

2. **Blueprint técnico do HADS** — a especificação de engenharia do artefato a ser instanciado na condição experimental C: o modelo (matriz de decisão humano–agente), a arquitetura de referência, o mecanismo de *policy-as-code*/guardrails, a governança de identidade não-humana, a trilha de auditoria à prova de adulteração e os pontos obrigatórios de *human-in-the-loop*, com mapeamento explícito para frameworks de compliance.

O leitor que busca o desenho científico deve focar na Parte I; quem vai construir o pipeline, na Parte II; a Parte III costura as duas, especificando *como* cada componente técnico emite os dados que as proposições consomem.

---

## 0. Premissas herdadas do position paper

Para que este documento seja autossuficiente sem repetir a fundamentação, fixamos as âncoras herdadas:

- **Pergunta de pesquisa.** *Que modelo de separação de funções e governança permite ao HIC preservar segurança, auditabilidade e accountability sem reintroduzir o coordination overhead que ele eliminou?*
- **Artefato-alvo (DSR).** Um artefato composto — **modelo + método** — chamado HADS, cujo mecanismo de implementação é *policy-as-code*/guardrails embutidos no pipeline.
- **Deslocamento conceitual.** A SoD deixa de ser uma relação entre *pessoas* (Dev vs. Sec) e passa a ser uma relação entre *quem decide/audita* (o HIC humano) e *quem executa* (os agentes de IA).
- **Avaliação mista.** DORA (eficiência/estabilidade) + SPACE (saúde do sistema) + estudo de caso (governança/risco).
- **Três condições.** A = equipe BizDevSecOps tradicional (linha de base); B = HIC sem HADS; C = HIC com HADS.

O cenário adotado nesta implementação é **genérico do setor de software** (equipe de engenharia entregando software de produção), sem dependência de domínio regulatório específico — embora o desenho dos controles seja compatível com SOX, ISO 27001, PCI-DSS e NIST AI RMF, de modo que a transposição para um domínio regulado exija calibração, não redesenho.

---

# PARTE I — PROTOCOLO DE PESQUISA

## 1. Posicionamento metodológico

A implementação segue o ciclo de DSR de seis passos (identificação do problema → objetivos da solução → design e desenvolvimento → demonstração → avaliação → comunicação). Os dois primeiros passos foram cumpridos pelo position paper; este documento cobre **design e desenvolvimento** (Parte II), **demonstração** (instanciação na condição C) e **avaliação** (Parte I, a partir da §3).

Como o artefato precisa ser avaliado em um ambiente realista, mas sem renunciar ao controle necessário para isolar o efeito do HADS, a avaliação adota o **FEDS — Framework for Evaluation in Design Science Research** (Venable, Pries-Heje & Baskerville, 2016). O FEDS estrutura a decisão em quatro passos — explicitar as metas da avaliação, escolher a(s) estratégia(s), determinar as propriedades a avaliar e desenhar os episódios — e distingue avaliação *artificial* (controlada, para testar hipóteses de design) de avaliação *naturalística* (em uso real, para validar utilidade e fidelidade ao ambiente).

A estratégia FEDS escolhida é a **"Human Risk & Effectiveness"**: começa-se artificial e formativo (testar o HADS em ambiente controlado, ainda barato de corrigir) e migra-se progressivamente para naturalístico e somativo (HADS em uso por HICs reais em fluxo de produção), porque o risco principal do artefato é *humano e social* — depende de o HIC e a organização confiarem nele e o usarem — e porque o benefício só se manifesta em condições realistas de pressão de entrega.

## 2. Desenho do estudo

### 2.1 Estrutura geral

Combina-se um **quase-experimento comparativo de três condições** com **estudo de caso múltiplo**, porque nenhum dos dois isolado responde à pergunta:

- o quase-experimento dá poder para comparar métricas (DORA, SPACE) entre A, B e C e testar as proposições quantitativas;
- o estudo de caso múltiplo dá profundidade para entender *por que* o HADS funciona ou falha, capturando incidentes, achados de auditoria e a dinâmica de confiança organizacional — fenômenos que números agregados não explicam.

A unidade primária de análise é o **fluxo de entrega de valor** (da intenção de negócio ao deploy em produção), não o indivíduo nem o time isoladamente, porque é nesse fluxo que tanto o atrito quanto a SoD se manifestam.

### 2.2 As três condições, operacionalizadas

| | **A — BizDevSecOps tradicional** | **B — HIC sem HADS** | **C — HIC com HADS** |
|---|---|---|---|
| Composição | Time multifuncional (PM, Dev, QA, Sec, Ops) | 1 HIC + agentes de IA | 1 HIC + agentes de IA |
| Separação de funções | Entre pessoas (handoffs) | Inexistente (colapsada) | Entre HIC (decide/audita) e agentes (executam) |
| Controles de pipeline | Revisões e aprovações manuais | Mínimos / ad hoc | *Policy-as-code* + gates + HITL |
| Trilha de auditoria | Distribuída entre papéis | Difusa (mesma fonte decide e executa) | Tamper-evident, humano–agente segregada |
| Papel da liderança | Coordenação de squads | Indefinido | Arquiteto de guardrails + julgamento de exceção |

A condição C **é** o artefato HADS em uso; sua especificação está na Parte II. As condições A e B são as referências contra as quais o valor do artefato é medido: A representa o que o BizDevSecOps já garante (segurança, ao custo de overhead); B representa o ganho ingênuo do HIC (velocidade, ao custo de governança). O sucesso do HADS é definido relacionalmente — **C deve aproximar-se de B em velocidade e de A em segurança** (ver §6).

### 2.3 Seleção de casos e controle de confusão

A maior ameaça de validade apontada no position paper é a **confusão entre o efeito do modelo e a maturidade do indivíduo**: ganhos atribuídos ao HADS podem decorrer de o HIC ser simplesmente excelente. O desenho neutraliza isso por três mecanismos:

1. **Desenho intra-sujeito sempre que possível (within-subject).** O mesmo HIC opera nas condições B e C em períodos distintos (com ordem contrabalançada entre participantes para controlar efeito de aprendizagem), de modo que a diferença B→C isole o efeito do artefato, não da pessoa.
2. **Pareamento de tarefas por complexidade.** As entregas comparadas entre condições são estratificadas por classe de complexidade e risco (ver taxonomia na §II/Parte II), evitando que C receba tarefas sistematicamente mais fáceis.
3. **Múltiplos casos (replicação literal e teórica).** Recomenda-se 3 a 5 organizações/equipes, escolhidas para replicação *literal* (contextos semelhantes, esperando resultado semelhante) e *teórica* (contextos contrastantes — p.ex. produto vs. plataforma —, esperando resultados divergentes por razões previsíveis).

## 3. Operacionalização das proposições

Cada proposição do position paper é convertida aqui em um **episódio de avaliação** com variáveis, instrumento, condições comparadas e critério de decisão explícitos. Esta é a ponte entre a tese e os dados.

| Prop. | Compara | Variável dependente | Instrumento | Confirma-se se… |
|---|---|---|---|---|
| **P1** — HIC elimina atrito inter-silos? | A vs. B | Lead time; deploy frequency | Telemetria DORA (CI/CD) | B mostra lead time menor e frequência maior que A, de forma consistente |
| **P2** — Ganho vem com perda de segurança? | A vs. B | Change failure rate; rework rate; nº de violações de SoD | DORA + auditoria | B é mais rápido (P1) **mas** eleva CFR/rework **e** violações de SoD vs. A |
| **P3** — HADS resolve o trade-off? *(central)* | A vs. B vs. C | DORA (5) + violações de SoD | DORA + auditoria | C ≈ B em velocidade **e** C ≈ A em segurança |
| **P4** — IA elimina ou desloca a carga? | B vs. C | Dimensões SPACE (bem-estar, fluxo); carga de decisão vs. execução | Survey SPACE + telemetria | Delegação reduz carga de *execução* mas concentra carga de *decisão/supervisão* no HIC |
| **P5** — O que faz a organização confiar no HIC? | Transversal | Nível de confiança organizacional; preditores | Entrevistas → survey + regressão | Auditabilidade da fronteira humano–agente é o preditor mais forte de confiança |
| **P6** — Guardrail resolve ou adia a fricção? | Dentro de C | Fricção *prevista* (gates em runtime) vs. *diferida* (auditoria/incidente pós-deploy) | Logs de gate + registro de incidentes | A maioria dos problemas é **detida no gate**, não reaparece tarde em auditoria/incidente |
| **P-lead** — O que acontece com a fricção líder–time? | Squads vs. HIC cross-silo | Escopo e natureza do trabalho de liderança | Entrevistas + análise documental de papéis | Papel migra de coordenação para arquitetura de guardrails e julgamento de exceção |

A lógica de confirmação conjunta permanece a do paper: **P1** mede o que o HIC promete, **P2** o que ameaça, **P3** se o artefato reconcilia, **P4/P5** se é sustentável e aceito, **P6** se o mecanismo funciona, **P-lead** o efeito gerencial. A validação do caminho exige a confirmação conjunta de **P3, P4, P5 e P6**.

## 4. Instrumentos de medição

### 4.1 Eixo quantitativo — DORA de cinco métricas

A versão de 2024–2025 do DORA expandiu as quatro métricas clássicas para **cinco**, reorganizadas em duas categorias, e o relatório de 2025 trouxe a primeira leitura aprofundada do efeito da IA — relevante porque mostra que **a adoção de IA melhora throughput mas aumenta a instabilidade de entrega**, exatamente o trade-off que a tese prevê:

- **Throughput (o que o HIC promete melhorar):** deployment frequency, lead time for changes, failed deployment recovery time.
- **Instability (o que o HIC ameaça):** change failure rate e **rework rate** (a 5ª métrica — percentual de deploys não planejados, reativos a incidente em produção).

A coleta é por **telemetria automática** do CI/CD e do rastreador de incidentes, sem autorrelato, padronizada entre as três condições para comparabilidade. A hipótese-chave herdada: o HIC (B) melhora velocidade mas pode piorar change failure rate e rework rate quando não há SoD — e o HADS (C) deve recuperar essas duas métricas de instabilidade sem sacrificar as de throughput.

### 4.2 Eixo misto — SPACE (saúde do sistema e sustentabilidade)

O SPACE (Satisfação/bem-estar, Performance, Atividade, Comunicação/colaboração, Eficiência/fluxo) é medido por **survey periódico + telemetria**, nunca por uma métrica única — princípio do próprio framework. Em ambientes aumentados por IA, a dimensão **Satisfação/bem-estar** ganha peso porque desenvolvedores que interagem majoritariamente com código gerado por IA relatam erosão sutil de *ownership* e satisfação de ofício que métricas tradicionais não capturam — sinal diretamente relevante para P4.

Operacionalização para P4 (eliminar vs. deslocar a carga): cruza-se o survey SPACE com **sinais de telemetria de carga** — interrupções, trabalho fora do horário, tempo em revisão/validação de output de agente vs. tempo em execução direta. A hipótese é que a delegação aos agentes *desloca* a carga (de execução para decisão/supervisão) em vez de eliminá-la.

### 4.3 Eixo qualitativo — auditoria de SoD, incidentes e confiança

Três instrumentos qualitativos/contagem:

- **Auditoria de separação de funções.** Revisão sistemática da trilha humano–agente em busca de violações de SoD (ações em que decidir/aprovar e executar partiram da mesma fonte sem segregação). Fornece a contagem usada em P2 e P3 e o material de P6 (fricção diferida).
- **Registro de incidentes pós-deploy.** Classificação de incidentes de segurança e qualidade por causa-raiz, distinguindo os que um gate de pipeline *poderia* ter detido (alimenta P6).
- **Entrevistas semiestruturadas + análise documental.** Para P5 (fatores de confiança) e P-leadership (transformação do papel do líder). A fase qualitativa **define as variáveis**; um survey em escala posterior as **testa por regressão**.

### 4.4 Matriz instrumento × proposição (rastreabilidade)

| Instrumento | P1 | P2 | P3 | P4 | P5 | P6 | P-lead |
|---|---|---|---|---|---|---|---|
| Telemetria DORA | ● | ● | ● | | | | |
| Survey SPACE | | | | ● | | | |
| Telemetria de carga | | | | ● | | | |
| Auditoria de SoD | | ● | ● | | | ● | |
| Registro de incidentes | | ● | ● | | | ● | |
| Entrevistas / survey de confiança | | | | | ● | | ● |

## 5. Plano de análise

**Quantitativo.** Como o desenho favorece amostras pequenas (poucos HICs reais), a análise prioriza:

- estatística descritiva e **tamanhos de efeito** com intervalos de confiança, não apenas testes de significância (o p-valor é frágil com n pequeno);
- comparações intra-sujeito (B vs. C no mesmo HIC) por testes pareados / modelos de efeitos mistos, que ganham poder ao controlar a variância individual;
- para P3, a verificação de **equivalência** (C ≈ B em velocidade; C ≈ A em segurança) via testes de equivalência (TOST), porque a hipótese é de *não-inferioridade/semelhança*, não de diferença — um erro comum seria usar teste de diferença e concluir "sem efeito" quando o objetivo é justamente demonstrar proximidade.

**Qualitativo.** Análise temática das entrevistas com codificação aberta → axial; para P5, os temas viram itens de survey e entram em **regressão** para identificar o preditor dominante de confiança. Triangulação entre auditoria, incidentes e entrevistas para robustez.

**Avaliação do artefato (DSR/FEDS).** Além das proposições, o HADS é avaliado por critérios DSR — **utilidade, eficácia, fidelidade ao ambiente** — em episódios formativos (ciclos artificiais, ajustando o artefato) e somativos (uso naturalístico).

## 6. Critérios de sucesso

O artefato é considerado validado se, conjuntamente:

1. **P3 (central):** C mantém os ganhos de throughput de B (dentro de margem de equivalência pré-registrada) **e** recupera change failure rate, rework rate e violações de SoD ao nível de A.
2. **P6:** ≥ uma maioria pré-definida dos problemas de segurança/qualidade é detida em gate (fricção prevista), não diferida para auditoria/incidente.
3. **P4:** a carga de trabalho do HIC em C é sustentável — o deslocamento execução→supervisão não produz deterioração significativa de bem-estar/fluxo no SPACE.
4. **P5:** a auditabilidade da fronteira humano–agente emerge como preditor relevante de confiança organizacional.

Falha em P3 refuta o artefato; falha em P4/P5/P6 indica que o artefato funciona tecnicamente mas não é sustentável ou aceito — resultado que exige iteração de design, não abandono.

## 7. Cronograma (alinhado ao ciclo DSR)

| Fase | Atividade | Duração orientativa |
|---|---|---|
| 0 | Refino do problema e recrutamento de casos | 1–2 meses |
| 1 | Design e desenvolvimento do HADS (Parte II) | 2–3 meses |
| 2 | Demonstração / avaliação formativa (artificial, ciclos de ajuste) | 2 meses |
| 3 | Coleta naturalística A/B/C (telemetria + surveys) | 4–6 meses |
| 4 | Auditorias, entrevistas, survey de confiança | concorrente à fase 3 + 1 mês |
| 5 | Análise, avaliação somativa e comunicação | 2 meses |

## 8. Ameaças à validade e mitigações

| Ameaça | Origem | Mitigação |
|---|---|---|
| Confusão maturidade vs. modelo | HIC excelente independentemente do HADS | Desenho intra-sujeito B↔C; ordem contrabalançada; pareamento de tarefas |
| Amostra pequena | Poucos HICs reais (termo de 2026) | Tamanhos de efeito + equivalência; replicação multi-caso; modelos de efeitos mistos |
| Literatura cinzenta | "HIC" sem consolidação acadêmica | Revisão multivocal; triangulação |
| Efeito Hawthorne | HIC se comporta diferente sob observação | Telemetria passiva; períodos longos; comparação intra-sujeito |
| Validade externa do artefato | HADS como construção teórica | Avaliação naturalística (FEDS) em ≥3 casos contrastantes |
| Viés de instrumentação | Gates de C geram dados que A/B não têm | Normalizar definições de violação/incidente entre condições antes da coleta |

---

# PARTE II — BLUEPRINT TÉCNICO DO HADS

Esta parte especifica o artefato que é instanciado na condição C. O HADS é um **artefato composto**: um *modelo* (estrutura de papéis e decisões) e um *método* (o fluxo operacional que o instancia num pipeline real). O princípio de design que atravessa tudo: **no cenário do HIC, a IA é simultaneamente quem executa o trabalho e parte de quem aplica o controle — logo, a independência entre execução e verificação precisa ser uma propriedade arquitetural, não uma promessa de processo.**

## 9. O modelo — matriz de decisão humano–agente

O coração do HADS é uma matriz que classifica cada *ação* do fluxo de entrega em um de três regimes de autoridade. Ela substitui a SoD entre pessoas por uma SoD entre o HIC (decide/audita) e os agentes (executam), com um terceiro regime para o que exige um segundo humano.

| Regime | Quem pode efetivar | Exemplos de ação | Controle aplicado |
|---|---|---|---|
| **Agente-autônomo** | Agente, sem aprovação prévia | Formatação, refactor sem mudança de comportamento, geração de testes, atualização de docs, correção de lint | Gate automatizado + trilha de auditoria; reversível |
| **Humano-no-laço (HITL)** | Agente propõe, HIC aprova | Merge para branch protegida, mudança de schema, alteração de dependência, deploy em produção, mudança de IaC | Gate bloqueante + aprovação explícita registrada |
| **Segundo-humano (SoD dura)** | Exige um humano distinto do HIC | Liberação de controles de segurança, mudança em política de acesso/segredos, override de um gate de compliance, deploy que afeta dados sensíveis | Aprovação de revisor independente; o HIC não pode ser o aprovador |

A novidade conceitual está na **terceira linha**: mesmo no mundo do "departamento de uma pessoa só", há um conjunto irredutível de ações para as quais a SoD *entre pessoas* não pode ser dissolvida — e o HADS a preserva explicitamente, em vez de fingir que o agente a substitui. O agente nunca ocupa o papel de aprovador independente, porque não tem accountability jurídica nem autonomia de julgamento moral; ele pode ser *executor* e *verificador automatizado*, jamais o *segundo humano*.

A classificação de cada ação combina **dois eixos**: risco (impacto × irreversibilidade) e exigência regulatória. A taxonomia de risco também serve ao pareamento de tarefas do protocolo (§2.3), garantindo que as condições comparem trabalho de complexidade equivalente.

## 10. Arquitetura de referência

O método instancia o modelo em cinco camadas, do intento à evidência:

1. **Camada de intento (HIC).** O HIC atua como *intent architect* e *outcome auditor*: especifica o resultado de negócio e os critérios de aceite; não digita a maior parte do código.
2. **Camada de execução (agentes).** Agentes de IA geram código, testes, IaC e documentação. Cada agente roda sob uma **identidade não-humana (NHI)** própria, com privilégio mínimo e escopo just-in-time (ver §12).
3. **Camada de política (policy-as-code).** Um motor de políticas (p.ex. Open Policy Agent / Rego) avalia cada ação contra a matriz do §9 antes de efetivá-la. As políticas são versionadas, testadas e auditáveis como qualquer software.
4. **Camada de gate (CI/CD).** Os pontos de decisão da política viram gates no pipeline: bloqueiam, exigem aprovação humana ou exigem segundo humano, conforme o regime.
5. **Camada de evidência (trilha de auditoria).** Todo evento — proposta do agente, decisão da política, aprovação humana — é registrado de forma tamper-evident, com a cadeia de delegação completa (§13).

O ponto crítico de design: **a camada de política e a camada de evidência devem ser independentes do executor.** Se o mesmo agente que escreve o código também pudesse reescrever a política que o avalia ou apagar a trilha que o registra, a SoD humano–agente seria teatro. Por isso política e auditoria rodam sob identidades e permissões segregadas das dos agentes executores — o equivalente técnico do "quem identifica a vulnerabilidade não é quem a corrige".

## 11. Policy-as-code e guardrails

O mecanismo concreto que torna a "cola social" do BizDevSecOps (cultura, ritual, revisão manual) em "cola embutida" (gates executáveis) é o **policy-as-code (PaC)**: regras de governança, segurança e compliance definidas como código executável, versionado e testável, em vez de documentos estáticos. Integrado ao CI/CD, valida de forma determinística antes do deploy.

Padrões de guardrail aplicáveis ao HADS:

- **Guardrails de constraint (hard/soft).** O motor define o "envelope operacional" dentro do qual ações do agente podem ser efetivadas; fora dele, bloqueio ou escalonamento para humano.
- **Guardrails em nível de argumento.** A política inspeciona os argumentos de uma chamada de ferramenta do agente e nega se excederem limites (p.ex. escopo de acesso, volume de dados, ambiente-alvo).
- **Guardrails de classificação de dados.** Em fluxos com recuperação de contexto (RAG), a política decide quais documentos o agente pode acessar conforme o papel/escopo.

Cada regra de política emite, ao disparar, um evento que alimenta diretamente **P6** (fricção detida no gate vs. diferida) — ou seja, o guardrail é simultaneamente controle e instrumento de medição.

## 12. Governança de identidade não-humana (NHI)

A SoD humano–agente só é real se cada agente tiver identidade e permissões próprias e mínimas — caso contrário, "o agente" é uma caixa-preta onipotente. O blueprint adota as práticas consolidadas de governança de NHI para IA agêntica:

- **Privilégio mínimo no nível da ferramenta**, não no nível do agente: cada ação concede apenas o escopo necessário.
- **Acesso just-in-time (JIT):** segredos e entitlements emitidos pela duração da tarefa e revogados ao fim, substituindo privilégios permanentes — reduzindo o *blast radius* de um agente comprometido.
- **Identidade distinta por estágio do pipeline:** cada estágio (build, test, deploy) roda sob workload identity própria, materializando SoD no nível de workload.
- **Propagação de identidade humano→não-humano:** a ação do agente carrega a cadeia de delegação que liga de volta ao HIC que a autorizou.

Essas práticas respondem a um risco documentado: organizações que não escopam corretamente o acesso de IA são substancialmente mais propensas a incidentes; a diferença entre acesso de IA com privilégio mínimo e sobreprivilegiado é dramática nas taxas de incidente reportadas pela literatura de segurança de NHI (2025).

## 13. Trilha de auditoria humano–agente

A auditabilidade é o que, segundo a hipótese P5, mais sustenta a confiança organizacional no HIC — então a trilha não é log acessório, é entregável de primeira classe. O padrão probatório que a literatura de oversight de IA (2025–2026) converge é um **registro à prova de adulteração de quem revisou o quê, quando, com que informação e que decisão tomou** (aprovou, modificou ou rejeitou), estendido para a **cadeia de delegação**: qual humano autorizou o agente, que escopo concedeu, o que o agente acessou e fez dentro desse escopo, e quando.

Requisitos da trilha no HADS:

- **Granularidade de operação, não de sessão:** cada ação do agente e cada decisão da política/humano é um registro discreto.
- **Imutabilidade / tamper-evidence:** append-only, com integridade verificável, sob identidade segregada da dos executores.
- **Segregação decisão↔execução na fonte:** o registro distingue inequivocamente o evento de *decisão* (HIC ou política) do evento de *execução* (agente), resolvendo a ambiguidade que o position paper identifica como o cerne do problema de accountability.
- **Exportável para auditoria/SIEM:** alimenta diretamente os instrumentos qualitativos do protocolo (auditoria de SoD, registro de incidentes).

## 14. Pontos de oversight por nível de risco

O HADS distribui o julgamento humano conforme o risco, evitando dois fracassos simétricos: oversight de menos (tudo autônomo, governança evaporada) e oversight de mais (HIC aprova tudo, reintroduzindo o coordination overhead que o HIC eliminou — o que falsificaria a própria promessa do artefato).

- **Risco baixo / reversível →** agente-autônomo, oversight *post hoc* via amostragem de auditoria.
- **Risco médio →** *human-in-the-loop*: o agente para e espera aprovação do HIC com contexto e rationale registrados.
- **Risco alto / irreversível / regulado →** *human-in-the-loop* com **segundo humano** independente (SoD dura).

Os três elementos que a literatura e os reguladores (EU AI Act, NIST AI RMF) exigem para que o oversight seja real — **contexto, autoridade e rationale** — são propriedades obrigatórias de cada ponto HITL: o aprovador recebe o contexto da ação, tem autoridade para intervir e registra a justificativa.

## 15. Mapeamento de controles para frameworks de compliance

Embora o cenário seja genérico, os controles do HADS mapeiam para os frameworks que ancoram a SoD, de modo que a transposição a um domínio regulado seja calibração:

| Controle HADS | SOX | ISO 27001 | PCI-DSS | NIST AI RMF |
|---|---|---|---|---|
| Regime "segundo-humano" (SoD dura) | Controle interno sobre relato financeiro | A.5/A.8 segregação de funções | Req. de aprovações independentes | Govern / Manage |
| Trilha tamper-evident humano–agente | Auditabilidade de mudanças | Logging e monitoramento | Trilhas de auditoria | Measure / accountability |
| NHI com privilégio mínimo + JIT | Controle de acesso | A.8 gestão de acesso | Acesso por necessidade | Manage / segurança |
| Policy-as-code / gates | Controles automatizados | Conformidade contínua | Validação pré-deploy | Map / Manage |
| Oversight HITL com rationale | Aprovações documentadas | Supervisão | Revisão | Govern (human oversight) |

---

# PARTE III — INSTRUMENTAÇÃO: COMO O BLUEPRINT ALIMENTA O PROTOCOLO

A virtude do desenho é que o artefato técnico (Parte II) é também a fonte de dados do estudo (Parte I): a condição C não precisa de instrumentação paralela, porque seus próprios controles emitem a evidência. A tabela abaixo fecha o circuito.

| Componente do HADS (Parte II) | Evento que emite | Proposição que alimenta |
|---|---|---|
| Gates de CI/CD (§11) | Bloqueio em runtime, aprovação, rejeição | P6 (fricção prevista) |
| Trilha de auditoria (§13) | Decisão↔execução segregada, cadeia de delegação | P2, P3 (violações de SoD), P5 (auditabilidade) |
| Telemetria do pipeline | Deploy, lead time, recuperação | P1, P2, P3 (DORA throughput/instability) |
| Registro de incidentes | Falha pós-deploy classificada por causa | P2, P3 (CFR, rework), P6 (fricção diferida) |
| Regime "segundo-humano" (§9) | Aprovações independentes acionadas/contornadas | P2, P3 (SoD), P6 |
| Pontos HITL (§14) | Carga de decisão/supervisão do HIC | P4 (deslocamento da carga) |

As três coisas que **não** são instrumentadas pelo artefato e exigem coleta externa: o **survey SPACE** (P4 — bem-estar autorrelatado), as **entrevistas** (P5, P-leadership) e a **telemetria das condições A e B** (que não têm os gates de C e precisam de coleta DORA equivalente, normalizada conforme §8).

## 16. Roteiro de implementação por fases (resumo operacional)

Para quem vai construir, a sequência mínima viável:

1. **Definir a taxonomia de risco/ação** e popular a matriz de decisão do §9 para o domínio do caso — é a decisão de design que governa todo o resto.
2. **Codificar as políticas** (PaC) que implementam a matriz e conectá-las como gates no pipeline existente.
3. **Provisionar NHIs** por agente/estágio com privilégio mínimo e JIT (§12).
4. **Instrumentar a trilha** tamper-evident com segregação decisão↔execução (§13).
5. **Configurar os pontos HITL** e o regime de segundo-humano (§§9, 14).
6. **Validar formativamente** (FEDS artificial) antes de expor a HICs reais; depois abrir a coleta naturalística A/B/C.

---

## 17. Conclusão

Este documento converteu a agenda do position paper em algo executável: de um lado, um **protocolo de pesquisa** que transforma as proposições P1–P6 e P-leadership em episódios de avaliação com variáveis, instrumentos e critérios de decisão explícitos, ancorado em DSR/FEDS e desenhado para resistir à principal ameaça de validade (confundir o modelo com a maturidade do indivíduo); de outro, um **blueprint técnico do HADS** que materializa a separação de funções humano–agente como propriedade arquitetural — policy-as-code, identidade não-humana com privilégio mínimo, trilha tamper-evident e oversight graduado por risco — e não como promessa de processo.

O elo que une as duas partes é o que torna a proposta econômica e falsificável ao mesmo tempo: **os controles do artefato são também os instrumentos do estudo.** O gate que protege a produção é o mesmo que mede a fricção de P6; a trilha que dá accountability é a mesma que conta as violações de SoD de P3 e fundamenta a confiança de P5. Construir o HADS é, simultaneamente, montar o aparato que decide se ele funciona.

Resta o trabalho empírico — recrutar os casos, rodar A/B/C, auditar, entrevistar e analisar. Mas o caminho deixa de ser uma intuição de mercado e passa a ser um experimento com hipótese central clara: *C ≈ B em velocidade e C ≈ A em segurança*. Se confirmada, o HIC prospera sem desfazer o que o BizDevSecOps levou uma década para construir.

---

## Referências adicionais

> As referências do position paper permanecem válidas; abaixo apenas as fontes incorporadas nesta implementação.

**DORA — cinco métricas e era da IA**

- [Rework Rate: the 5th DORA metric (Faros.ai)](https://www.faros.ai/blog/5th-dora-metric-rework-rate-track-it-now)
- [The DORA 4 key metrics become 5 (CD Foundation)](https://cd.foundation/blog/2025/10/16/dora-5-metrics/)
- [DORA 2025: Measuring Software Delivery After AI (RedMonk)](https://redmonk.com/rstephens/2025/12/18/dora2025/)
- [DORA Metrics in the Age of AI (Plandek)](https://plandek.com/blog/dora-metrics-in-the-age-of-ai-how-engineering-leaders-should-measure-delivery-in-2025)
- [A history of DORA's software delivery metrics](https://dora.dev/insights/dora-metrics-history/)

**SPACE — produtividade e bem-estar na era da IA**

- [SPACE Framework in the AI Era (DZone)](https://dzone.com/articles/space-framework-ai-developer-productivity)
- [What is the SPACE framework (getDX)](https://getdx.com/blog/space-metrics/)
- [The SPACE of Developer Productivity (ACM Queue)](https://queue.acm.org/detail.cfm?id=3454124)

**Policy-as-code e guardrails para IA agêntica**

- [Agentic AI Governance: Policy as Code (policyascode.dev)](https://policyascode.dev/guides/agentic-ai-governance/)
- [Why Open Policy Agent is the Missing Guardrail for AI Agents (CodiLime)](https://codilime.com/blog/why-use-open-policy-agent-for-your-ai-agents/)
- [Runtime Governance for AI Agents: Policy-as-Code with OPA (Gökalp)](https://gokhan-gokalp.com/runtime-governance-for-ai-agents-policy-as-code-with-opa/)
- [AI-Augmented CI/CD Pipelines (arXiv)](https://arxiv.org/pdf/2508.11867)

**Identidade não-humana e SoD para IA**

- [The Non-Human Identity Governance Vacuum (Cloud Security Alliance)](https://labs.cloudsecurityalliance.org/research/csa-whitepaper-nonhuman-identity-agentic-ai-governance-v1-cs/)
- [Non-human identities: Agentic AI's new frontier of risk (World Economic Forum)](https://www.weforum.org/stories/2025/10/non-human-identities-ai-cybersecurity/)
- [The Looming Authorization Crisis: Why Traditional IAM Fails Agentic AI (ISACA)](https://www.isaca.org/resources/news-and-trends/industry-news/2025/the-looming-authorization-crisis-why-traditional-iam-fails-agentic-ai)
- [Least privilege vs. separation of duties for AI workloads (NHI Mgmt Group)](https://nhimg.org/faq/what-is-the-difference-between-least-privilege-and-separation-of-duties-for-ai-w/)

**Human-in-the-loop, accountability e oversight**

- [Human in the Loop: AI Compliance (Kiteworks)](https://www.kiteworks.com/regulatory-compliance/human-in-the-loop-ai-compliance/)
- [Human-in-the-Loop AI Governance Framework (Presidio)](https://www.presidio.com/blogs/human-in-the-loop-ai-governance-framework/)
- [Human-in-the-Loop: A 2026 Guide to AI Oversight (Strata)](https://www.strata.io/blog/agentic-identity/practicing-the-human-in-the-loop/)

**Método de avaliação (DSR)**

- [FEDS: a Framework for Evaluation in Design Science Research (Venable, Pries-Heje & Baskerville, EJIS 2016)](https://link.springer.com/article/10.1057/ejis.2014.36)

---

*Documento de implementação que acompanha o position paper HADS. Mantém-se a ressalva metodológica: "HIC" e "BizDevSecOps" carecem de consolidação acadêmica; fontes de indústria devem ser tratadas como literatura cinzenta em uma eventual revisão multivocal.*
