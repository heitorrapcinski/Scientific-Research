# HADS: separação de funções humano–agente como caminho para o HIC prosperar no BizDevSecOps

### High-Impact Individual Contributor: promessa de produtividade, desafio de governança no BizDevSecOps

*Position paper e agenda de pesquisa*

---

## Resumo

O paradigma BizDevSecOps é, antes de tudo, uma resposta a um problema de **atrito entre silos organizacionais** (Negócio, Desenvolvimento, Segurança e Operações). Toda a literatura da área converge para uma mesma prescrição: o atrito não se elimina, gerencia-se — por meio de cultura de cooperação, automação e alinhamento. Em paralelo, a difusão de IA agêntica fez emergir uma nova figura, o *High-Impact Individual Contributor* (HIC): um profissional que, amplificado por agentes de IA, entrega valor de negócio fim-a-fim sozinho, comprimindo o *coordination overhead* entre os silos. Este artigo defende a tese de que **o HIC dissolve o atrito *inter*-silos do BizDevSecOps, mas reintroduz o mesmo problema sob nova forma — agora *intra*-pessoal e de governança**. Como a adoção de IA é praticamente irreversível, o objetivo não é frear o HIC, mas encontrar caminhos para que ele prospere sem destruir os controles que o BizDevSecOps protegia — sobretudo a **separação de funções (Segregation of Duties, SoD)**, que é, por definição, impossível dentro de um único indivíduo. Propomos um caminho metodológico baseado em *Design Science Research* (DSR), cujo resultado seria um **artefato (modelo/método) de Separação de Funções Humano–Agente (HADS)**, avaliado por método **misto** (métricas quantitativas DORA + dimensões quali/quanti do framework SPACE + estudo de caso). Encerramos com um conjunto de proposições testáveis para um futuro estudo empírico.

**Palavras-chave:** BizDevSecOps; DevSecOps; High-Impact Individual Contributor; IA agêntica; separação de funções; governança; Design Science Research.

---

## 1. Introdução

Duas tendências independentes estão em rota de colisão.

A primeira é a maturação do paradigma **BizDevSecOps**, que estende o DevOps incorporando segurança (DevSecOps) e negócio (BizDevOps) num mesmo fluxo. O próprio crescimento do acrônimo é sintomático: cada letra acrescentada denuncia um silo que ficara de fora e cuja exclusão gerava atrito ([BizDevOps: Because DevOps is Not the End of the Story](https://www.researchgate.net/publication/300545597_BizDevOps_Because_DevOps_is_Not_the_End_of_the_Story)).

A segunda é o surgimento do **High-Impact Individual Contributor (HIC)** — termo popularizado em 2026 — para descrever o profissional sênior que, com apoio de IA, realiza sozinho o que antes exigia uma equipe inteira ([DEV Community](https://dev.to/pvgomes/the-high-individual-contributor-is-becoming-a-new-organizational-unit-3ef8); [StartSe](https://www.startse.com/artigos/hic-o-profissional-que-chegou-ao-topo-sem-virar-gerente-e-usa-ia-para-se-destacar/)).

A premissa de fundo deste trabalho é que **o uso de IA não tem retorno**: a compressão de fluxos de trabalho por agentes já está reorganizando estruturas de engenharia ([Business Standard](https://www.business-standard.com/technology/tech-news/ai-is-rewiring-the-software-industry-by-collapsing-traditional-workflows-126060700668_1.html); [Deloitte](https://www.deloitte.com/us/en/services/consulting/articles/agentic-ai-impact-on-software-engineering.html)). A questão deixa de ser *se* o HIC vai existir e passa a ser *como* fazê-lo prosperar sem desfazer as garantias que o BizDevSecOps construiu.

**Tese central deste artigo:** *o HIC elimina o atrito entre os silos do BizDevSecOps — porque colapsa todos eles numa só unidade — mas, ao fazê-lo, cria uma nova classe de problemas (segurança, governança, concentração de risco, auditabilidade) que o paradigma anterior resolvia justamente pela separação entre pessoas.*

Este é um *position paper*: ele não pretende responder à tese empiricamente, mas (i) analisar os dados disponíveis sobre os dois termos, (ii) explicitar a lacuna de pesquisa e (iii) propor o que precisa ser experimentado num estudo científico futuro — incluindo o que se apresentaria como resultado e se a avaliação seria quantitativa, qualitativa ou mista.

---

## 2. Fundamentação teórica

### 2.1 BizDevSecOps: o atrito entre silos como problema cultural

A literatura acadêmica e de indústria é convergente em três pontos.

Primeiro, **a origem do paradigma é o atrito**. O BizDevOps surge porque o DevOps deixava o negócio de fora; o DevSecOps, porque deixava a segurança de fora ([BizDevOps: A Multivocal Literature Review](https://www.researchgate.net/publication/345005095_BizDevOps_A_Multivocal_Literature_Review)). A junção completa (BizDevSecOps) é descrita como a integração de quatro mundos em um único ciclo de vida ([GeeksforGeeks](https://www.geeksforgeeks.org/software-engineering/overview-of-bizdevsecops/); [EFS Consulting](https://efs.consulting/en/businessareas/agility/bizdevsecops/)).

Segundo, **o atrito é predominantemente cultural e organizacional, não técnico**. Revisões sistemáticas identificam como obstáculos centrais a resistência organizacional, a ausência de práticas padronizadas e a dificuldade de transformação cultural ([Identifying the primary dimensions of DevSecOps — JSS, 2024](https://www.sciencedirect.com/science/article/pii/S0164121224001080); [Challenges and solutions when adopting DevSecOps — arXiv, 2021](https://arxiv.org/pdf/2103.08266); [Evolution of DevSecOps — MDPI, 2025](https://www.mdpi.com/2227-7080/13/12/548)). O silo *Negócio* é o de alinhamento mais difícil ([Towards Agile IT/Business Alignment at BizDevOps](https://www.scitepress.org/Papers/2023/119654/119654.pdf); [Improving IT/Business Alignment — Wiley, 2025](https://onlinelibrary.wiley.com/doi/full/10.1002/smr.70016)).

Terceiro — e crucial para a tese —, **a solução proposta é de convivência, não de eliminação**. A prescrição é "cultura de cooperação e apreciação mútua", padronização e automação para *minimizar* o atrito preservando o valor de cada silo ([Dynatrace](https://www.dynatrace.com/news/blog/bizdevsecops-use-cases-for-unified-observability/); [DZone](https://dzone.com/articles/bizdevsecops-the-developers-path-forward-1)). É exatamente a ideia de "minimizar e não acabar com o impacto" que motiva este estudo.

Há, porém, um controle que **não** é negociável por cultura: a **separação de funções (SoD)**. Mesmo no DevSecOps, mantém-se que quem identifica uma vulnerabilidade e quem a corrige devem ser pessoas distintas, e que nenhum indivíduo deve ter controle completo de um processo de ponta a ponta ([CSO Online — Does DevSecOps eliminate segregation of duties?](https://www.csoonline.com/article/565700/does-devsecops-eliminate-the-segregation-of-duties-between-security-and-devops.html); [Can DevSecOps and SoD Coexist? — GovDevSecOps](https://govdevsecopshub.com/2022/03/02/can-devsecops-and-separation-of-duties-coexist/)). SoD está ancorada em frameworks regulatórios (SOX, ISO 27001, PCI-DSS, NIST) ([CyberArk](https://www.cyberark.com/resources/blog/the-vital-role-of-segregation-of-duties-in-cybersecurity-and-compliance)).

### 2.2 HIC: o colapso dos silos numa unidade

O HIC é definido menos por título e mais por **escala de impacto**: resolve problemas que ninguém mais resolve, influencia sem autoridade formal e conecta o que faz diretamente ao resultado de negócio ([Inteligência Setorial](https://inteligenciasetorial.com.br/ic-high-impact-individual-contributor/)). A base conceitual vem das trilhas de carreira de *Individual Contributor* (Staff/Principal/Distinguished), em que liderança técnica não exige gestão de pessoas ([Staff Engineer — Will Larson](https://staffeng.com/book/); [LeadDev](https://leaddev.com/career-development/who-are-staff-principal-and-distinguished-engineers)).

O elemento novo é a **IA agêntica como multiplicador de força**. Agentes funcionam como "tecido conectivo" entre as camadas da stack, permitindo *ownership* vertical fim-a-fim e comprimindo o *coordination overhead* que separava os silos ([Agentic Engineering — LangChain](https://www.langchain.com/blog/agentic-engineering-redefining-software-engineering); [How AI is reshaping software teams 2026 — InteligenAI](https://inteligenai.com/how-ai-coding-agents-are-restructuring-software-teams-and-what-the-1979-spreadsheet-can-teach-us/)). O resultado é descrito, sem exagero retórico, como o "departamento de uma pessoa só" ([Futuro da Saúde](https://futurodasaude.com.br/hi-c-na-saude-artigo-campolina/)) — ou, na formulação mais precisa para esta análise, **o HIC como uma nova *unidade organizacional*** ([DEV Community](https://dev.to/pvgomes/the-high-individual-contributor-is-becoming-a-new-organizational-unit-3ef8)).

É aqui que os dois corpos de literatura se tocam: se o HIC é uma unidade organizacional que internaliza Biz, Dev, Sec e Ops, então **os quatro silos do BizDevSecOps passam a coexistir dentro de uma única pessoa.**

---

## 3. Análise: a tese central

### 3.1 O que o HIC resolve

Se o atrito do BizDevSecOps vinha dos *handoffs* entre silos — filas, retrabalho, perda de contexto, "muros" entre PM, Dev, QA e Sec —, então o HIC o resolve por **eliminação da fronteira**. Não há atrito inter-silos quando não há mais dois lados para friccionar. A própria métrica que o DevOps usa para medir fricção (lead time, frequência de deploy) tende a melhorar quando o fluxo deixa de atravessar fronteiras organizacionais ([DORA — Atlassian](https://www.atlassian.com/devops/frameworks/dora-metrics); [Google Cloud — Four Keys](https://cloud.google.com/blog/products/devops-sre/using-the-four-keys-to-measure-your-devops-performance)).

### 3.2 O que o HIC quebra

O ganho tem um custo estrutural. O quadro abaixo organiza o deslocamento do problema:

| Dimensão | BizDevSecOps (muitas pessoas) | HIC (uma pessoa + agentes) |
|---|---|---|
| Natureza do atrito | *Inter*-silos: coordenação, handoffs | *Intra*-pessoa: sobrecarga cognitiva, conflito de papéis |
| Solução do paradigma | Cultura de convivência | (não há "outro" com quem conviver) |
| Separação de funções (SoD) | Garantida entre pessoas/equipes | Violada por construção |
| Concentração de risco | Distribuída | *Bus factor* = 1; ponto único de falha |
| Auditabilidade | Trilha entre papéis distintos | Decisão e execução na mesma fonte |
| Governança/compliance | Aprovações cruzadas (SOX, ISO 27001) | Sem segregação natural de aprovação |

Os quatro problemas novos mais relevantes:

1. **Colapso da separação de funções.** O controle de segurança mais básico — que ninguém execute *e* aprove a própria mudança — é incompatível com um indivíduo que faz tudo. Isso não é uma questão de cultura (que o BizDevSecOps sabe tratar), e sim de princípio de controle interno ([CSO Online](https://www.csoonline.com/article/565700/does-devsecops-eliminate-the-segregation-of-duties-between-security-and-devops.html); [CyberArk](https://www.cyberark.com/resources/blog/the-vital-role-of-segregation-of-duties-in-cybersecurity-and-compliance)).

2. **Concentração de risco e *bus factor*.** A unidade organizacional de uma pessoa é, simultaneamente, um ponto único de falha de conhecimento, disponibilidade e julgamento.

3. **Auditabilidade e *accountability* dos agentes.** Quando boa parte da execução é delegada a agentes de IA, a fronteira de responsabilidade entre o humano (intent architect / outcome auditor) e o agente (executor) torna-se difusa ([Deloitte](https://www.deloitte.com/us/en/services/consulting/articles/agentic-ai-impact-on-software-engineering.html)).

4. **Sobrecarga cognitiva e qualidade.** Concentrar quatro perspectivas num só decisor pressiona bem-estar e foco — dimensões que a literatura de produtividade mostra serem determinantes do desempenho sustentável ([SPACE framework](https://getdx.com/blog/space-metrics/)).

### 3.3 A reformulação que a tese propõe

A contribuição conceitual é um **deslocamento de eixo**: o BizDevSecOps tratava a separação de funções como uma relação **entre pessoas**; no mundo do HIC, ela precisa ser reconcebida como uma relação **entre o humano e seus agentes de IA**. Em vez de segregar Dev de Sec entre dois funcionários, segrega-se *quem decide/audita* (o HIC) de *quem executa* (o agente), com trilhas e gates automatizados. Chamamos provisoriamente essa ideia de **Separação de Funções Humano–Agente (Human–Agent Duties Separation — HADS)**. É essa hipótese de artefato que o estudo futuro deve construir e testar.

---

## 4. Lacuna de pesquisa

Há literatura robusta de cada lado, mas **nenhuma fonte conecta diretamente o HIC à perda de separação de funções de segurança**:

- A literatura de BizDevSecOps assume *muitas pessoas que precisam conviver* e nunca pergunta o que acontece quando os silos colapsam num indivíduo.
- A literatura de HIC celebra *uma pessoa que faz tudo* e trata segurança/governança apenas perifericamente.
- A literatura de SoD e de IA agêntica discute concentração de poder e *human-in-the-loop*, mas não no recorte específico do contribuidor individual de alto impacto.

A lacuna, portanto, é a interseção dos três. **A pergunta de pesquisa** que a preenche:

> *Se o BizDevSecOps gerencia o atrito como uma questão de cultura entre silos, e o HIC o converte numa questão de governança dentro de um único agente aumentado por IA, que modelo de separação de funções e governança permite ao HIC preservar segurança, auditabilidade e accountability sem reintroduzir o coordination overhead que ele eliminou?*

---

## 5. Caminho metodológico proposto

Esta seção responde diretamente às três perguntas do estudo: **o que apresentar como resultado**, **se o teste é quantitativo ou qualitativo**, e **se entregamos um novo processo ou um novo modelo.**

### 5.1 O que apresentamos como resultado: um artefato (Design Science Research)

O encaixe metodológico natural é a **Design Science Research (DSR)**, cujo propósito é construir e avaliar *artefatos* que resolvem problemas reais. Um artefato em DSR pode ser um *construto*, um *modelo*, um *método* ou uma *instanciação* ([A Design Science Research Methodology for IS](https://www.researchgate.net/publication/284503626_A_design_science_research_methodology_for_information_systems_research); [Artifact Evaluation in IS DSR — PDF](https://cedric.cnam.fr/fichiers/art_3208.pdf)). O ciclo DSR tem seis passos: identificação do problema, objetivos da solução, design e desenvolvimento, demonstração, avaliação e comunicação.

**Resposta direta:** o resultado não é "responder se o HIC é bom ou ruim", mas **entregar um artefato** — concretamente, um **modelo + método de governança HADS** para o HIC:

- *Modelo* (a estrutura): mapa de papéis BizDevSecOps internalizados pelo HIC e a matriz de quais decisões podem ser executadas pelo agente, quais exigem aprovação humana e quais exigem um segundo humano.
- *Método* (o processo): o fluxo operacional que instancia esse modelo num pipeline real (gates automatizados, trilha de auditoria humano–agente, pontos de *human-in-the-loop*).

Ou seja, **é um novo modelo *e* um novo processo** — e essa dupla entrega é exatamente o que a DSR chama de artefato composto.

### 5.2 Quantitativo ou qualitativo? Método misto

Nenhuma das duas isoladamente serve. Propomos **avaliação mista**, espelhando a própria recomendação do framework SPACE de não confiar numa única métrica ([SPACE — getDX](https://getdx.com/blog/space-metrics/); [SPACE — Jellyfish](https://jellyfish.co/library/space-framework/)):

- **Quantitativo — eficiência e estabilidade (o que o HIC promete melhorar):** as quatro métricas DORA — frequência de deploy, lead time, *change failure rate* e tempo de restauração — mais a quinta métrica recente, *rework rate* ([DORA — Atlassian](https://www.atlassian.com/devops/frameworks/dora-metrics); [DORA history](https://dora.dev/insights/dora-metrics-history/)). Hipótese: o HIC melhora velocidade, mas pode piorar *change failure rate* quando não há SoD.
- **Quantitativo + Qualitativo — saúde do sistema (o que o HIC ameaça):** dimensões do **SPACE** (Satisfação/bem-estar, Performance, Atividade, Comunicação/colaboração, Eficiência/fluxo), captadas por telemetria + *surveys* ([SPACE — Octopus](https://octopus.com/devops/metrics/space-framework/)).
- **Qualitativo — governança e risco (o coração da tese):** estudo de caso múltiplo com entrevistas e análise documental sobre incidentes de segurança, achados de auditoria e violações de SoD, à luz dos frameworks de compliance ([CyberArk](https://www.cyberark.com/resources/blog/the-vital-role-of-segregation-of-duties-in-cybersecurity-and-compliance)).

### 5.3 Desenho do estudo

Sugere-se um **quase-experimento comparativo** combinado a **estudo de caso múltiplo**, com três condições:

- **A** — equipe BizDevSecOps tradicional (linha de base);
- **B** — HIC sem o modelo HADS (IA agêntica, sem governança de separação);
- **C** — HIC com o modelo HADS proposto.

A avaliação do artefato segue critérios de DSR (utilidade, eficácia, fidelidade ao ambiente) ([Artifact Evaluation in IS DSR](https://aisel.aisnet.org/pacis2014/23/)).

### 5.4 O que precisa ser experimentado (proposições testáveis)

Cada proposição abaixo descreve um experimento concreto: **o que se compara**, **o que se mede** e **qual resultado confirma ou refuta a hipótese**. Juntas, elas decompõem a tese em afirmações falsificáveis — nenhuma precisa ser respondida neste artigo, mas todas precisam ser testadas no estudo futuro.

**P1 — O HIC realmente elimina o atrito inter-silos?** *[quantitativo — DORA]*
Compara-se a condição A (equipe tradicional) com a B (HIC). Mede-se lead time e frequência de deploy. *Confirma-se se* B apresenta lead time menor e frequência maior que A de forma consistente. É o teste do benefício prometido pelo HIC.

**P2 — Esse ganho vem acompanhado de perda de segurança?** *[misto — DORA + auditoria]*
Ainda comparando A e B, mede-se *change failure rate* (quanti) e a contagem de violações de separação de funções identificadas em auditoria (quali/contagem). *Confirma-se se* a condição B, apesar de mais rápida, eleva tanto a taxa de falha quanto as violações de SoD frente à linha de base. É o teste de que o colapso dos silos cobra um preço.

**P3 — O artefato HADS resolve o trade-off? (proposição central)** *[misto]*
Compara-se a condição C (HIC + HADS) com B e A. Verifica-se se C **mantém** os ganhos de velocidade de B (DORA) e, ao mesmo tempo, **recupera** os indicadores de segurança/governança ao nível de A (taxa de falha e violações de SoD). *Confirma-se se* C ≈ B em velocidade **e** C ≈ A em segurança. É o teste da contribuição do estudo: o HADS faz o HIC prosperar sem reintroduzir atrito.

**P4 — A IA elimina ou apenas desloca a carga de trabalho?** *[misto — survey + telemetria]*
Nas condições B e C, mede-se bem-estar e fluxo pelas dimensões do SPACE (survey) cruzadas com sinais de telemetria (ex.: interrupções, trabalho fora do horário). *Confirma-se se* a delegação aos agentes reduz a carga de execução mas concentra carga de decisão/supervisão no HIC — ou seja, a sobrecarga muda de natureza em vez de desaparecer. Importa para sustentabilidade: um HIC que entrega rápido mas insustentável não "prospera".

**P5 — O que faz a organização confiar no HIC?** *[qualitativo → quantitativo]*
Primeiro, entrevistas identificam os fatores que geram ou destroem confiança no HIC (fase quali, que define as variáveis). Depois, um survey em escala testa, por regressão, qual fator mais explica o nível de confiança. *Confirma-se se* a auditabilidade da fronteira humano–agente surge como o preditor mais forte — acima de velocidade, custo ou reputação do indivíduo. É o teste de adoção: mostra que o HADS resolve não só o problema técnico, mas o de aceitação organizacional.

**Síntese do desenho.** P1 mede o que o HIC promete; P2 mede o que ele ameaça; P3 testa se o artefato proposto reconcilia os dois; P4 e P5 verificam se o resultado é sustentável (pessoa) e aceito (organização). A confirmação conjunta de P3, P4 e P5 é o que validaria o caminho proposto.

---

## 6. Ameaças à validade

A novidade do termo "HIC" (2026) implica base empírica ainda escassa e forte presença de literatura cinzenta — o que exige cautela e revisão multivocal. O tamanho de amostra de HICs reais tende a ser pequeno (limita poder estatístico, favorecendo desenho de estudo de caso). Há ainda risco de variável de confusão: ganhos atribuídos ao HIC podem decorrer da maturidade do indivíduo, não do modelo. O artefato HADS, por fim, precisa de validação externa para não ser apenas uma construção teórica ([Artifact Evaluation in IS DSR](https://cedric.cnam.fr/fichiers/art_3208.pdf)).

---

## 7. Conclusão

O HIC não é o fim do BizDevSecOps; é a sua **internalização** numa só pessoa. Ao colapsar os silos, dissolve o atrito que o paradigma passou uma década aprendendo a administrar — e, no mesmo gesto, recria o problema sob uma forma para a qual o paradigma não tem resposta cultural, porque agora se trata de **separação de funções e governança dentro de um indivíduo amplificado por IA**. Como recuar do uso de IA não é uma opção realista, o caminho de pesquisa proposto não tenta conter o HIC, mas equipá-lo: construir e testar, via Design Science Research, um **modelo e método de Separação de Funções Humano–Agente (HADS)**, avaliados por método misto (DORA + SPACE + estudo de caso). O resultado entregável é um artefato — um novo modelo e um novo processo — e a contribuição científica está em transformar uma intuição de mercado em proposições falsificáveis.

---

## Referências

**BizDevSecOps / BizDevOps / DevSecOps**

- [BizDevOps: Because DevOps is Not the End of the Story](https://www.researchgate.net/publication/300545597_BizDevOps_Because_DevOps_is_Not_the_End_of_the_Story)
- [BizDevOps: A Multivocal Literature Review](https://www.researchgate.net/publication/345005095_BizDevOps_A_Multivocal_Literature_Review)
- [Towards Agile IT/Business Alignment at BizDevOps (PDF)](https://www.scitepress.org/Papers/2023/119654/119654.pdf)
- [Improving IT/Business Alignment in DevOps (Wiley, 2025)](https://onlinelibrary.wiley.com/doi/full/10.1002/smr.70016)
- [Identifying the primary dimensions of DevSecOps (JSS, 2024)](https://www.sciencedirect.com/science/article/pii/S0164121224001080)
- [Challenges and solutions when adopting DevSecOps (arXiv, 2021)](https://arxiv.org/pdf/2103.08266)
- [Evolution of DevSecOps and Its Influence on Application Security (MDPI, 2025)](https://www.mdpi.com/2227-7080/13/12/548)
- [Overview of BizDevSecOps (GeeksforGeeks)](https://www.geeksforgeeks.org/software-engineering/overview-of-bizdevsecops/)
- [BizDevSecOps use cases for unified observability (Dynatrace)](https://www.dynatrace.com/news/blog/bizdevsecops-use-cases-for-unified-observability/)
- [BizDevSecOps (EFS Consulting)](https://efs.consulting/en/businessareas/agility/bizdevsecops/)
- [BizDevSecOps: The Developer's Path Forward (DZone)](https://dzone.com/articles/bizdevsecops-the-developers-path-forward-1)

**HIC / Trilha IC / IA agêntica**

- [The High Individual Contributor is becoming a new organizational unit (DEV)](https://dev.to/pvgomes/the-high-individual-contributor-is-becoming-a-new-organizational-unit-3ef8)
- [Staff Engineer: Leadership beyond the management track (Will Larson)](https://staffeng.com/book/)
- [Who are staff, principal, and distinguished engineers? (LeadDev)](https://leaddev.com/career-development/who-are-staff-principal-and-distinguished-engineers)
- [HIC: o profissional que chegou ao topo sem virar gerente (StartSe)](https://www.startse.com/artigos/hic-o-profissional-que-chegou-ao-topo-sem-virar-gerente-e-usa-ia-para-se-destacar/)
- [IC High-Impact: o caminho técnico para o topo (Inteligência Setorial)](https://inteligenciasetorial.com.br/ic-high-impact-individual-contributor/)
- [HI-C na saúde: o departamento de uma pessoa só (Futuro da Saúde)](https://futurodasaude.com.br/hi-c-na-saude-artigo-campolina/)
- [Agentic Engineering: redefining software engineering (LangChain)](https://www.langchain.com/blog/agentic-engineering-redefining-software-engineering)
- [How AI coding agents are restructuring software teams (InteligenAI)](https://inteligenai.com/how-ai-coding-agents-are-restructuring-software-teams-and-what-the-1979-spreadsheet-can-teach-us/)
- [AI is rewiring the software industry by collapsing workflows (Business Standard)](https://www.business-standard.com/technology/tech-news/ai-is-rewiring-the-software-industry-by-collapsing-traditional-workflows-126060700668_1.html)
- [Agentic AI impact on software engineering (Deloitte)](https://www.deloitte.com/us/en/services/consulting/articles/agentic-ai-impact-on-software-engineering.html)

**Separação de funções / governança**

- [Does DevSecOps eliminate the segregation of duties? (CSO Online)](https://www.csoonline.com/article/565700/does-devsecops-eliminate-the-segregation-of-duties-between-security-and-devops.html)
- [Can DevSecOps and Separation of Duties Coexist? (GovDevSecOps)](https://govdevsecopshub.com/2022/03/02/can-devsecops-and-separation-of-duties-coexist/)
- [The Vital Role of Segregation of Duties (CyberArk)](https://www.cyberark.com/resources/blog/the-vital-role-of-segregation-of-duties-in-cybersecurity-and-compliance)

**Métricas e método**

- [DORA Metrics (Atlassian)](https://www.atlassian.com/devops/frameworks/dora-metrics)
- [Using the Four Keys to measure DevOps performance (Google Cloud)](https://cloud.google.com/blog/products/devops-sre/using-the-four-keys-to-measure-your-devops-performance)
- [A history of DORA's software delivery metrics](https://dora.dev/insights/dora-metrics-history/)
- [SPACE framework (getDX)](https://getdx.com/blog/space-metrics/)
- [SPACE Framework Metrics (Jellyfish)](https://jellyfish.co/library/space-framework/)
- [SPACE Metrics (Octopus Deploy)](https://octopus.com/devops/metrics/space-framework/)
- [A Design Science Research Methodology for IS](https://www.researchgate.net/publication/284503626_A_design_science_research_methodology_for_information_systems_research)
- [Artifact Evaluation in IS Design-Science Research (PACIS)](https://aisel.aisnet.org/pacis2014/23/)
- [Artifact Evaluation in IS DSR — a Holistic View (PDF)](https://cedric.cnam.fr/fichiers/art_3208.pdf)

---

*Documento de posicionamento para subsidiar um futuro estudo empírico. Os termos "HIC" e "BizDevSecOps" ainda carecem de consolidação acadêmica; recomenda-se tratar as fontes de indústria como literatura cinzenta em uma eventual revisão multivocal.*
