# Seleção de casos open-source e mapeamento de fontes para o estudo HADS (A/B/C)

> *Documento de apoio à implementação. Operacionaliza a §2.3 (seleção de casos) e a §4 (instrumentos) do "Implementação do estudo de caso HADS", aplicando o desenho a um cenário de projetos open-source.*
>
> *Objetivo: identificar projetos OSS candidatos de porte médio e ativos, e avaliar se a documentação de orientação a novos desenvolvedores e os dados públicos de cada um sustentam a coleta exigida pelas proposições P1–P6 e P-leadership.*

---

## 1. Como o desenho A/B/C se mapeia em projetos open-source

A ideia central é usar a comunidade real do projeto como **linha de base (A)** e operar **forks** como condições experimentais:

| Condição | No protocolo HADS | Instanciação em OSS |
|---|---|---|
| **A** — BizDevSecOps tradicional | Time multifuncional, SoD entre pessoas | A comunidade do projeto operando no dia-a-dia: PRs revisados por pares, CODEOWNERS, aprovações cruzadas, releases regulares |
| **B** — HIC sem HADS | 1 HIC + agentes, sem governança | Fork operado por um HIC/agente de IA resolvendo issues reais do projeto, sem gates de separação |
| **C** — HIC com HADS | 1 HIC + agentes, com policy-as-code, HITL, trilha | O mesmo fork com a camada HADS (OPA/gates, HITL, NHI, auditoria) montada sobre o pipeline |

Isso tem três consequências para a seleção de candidatos:

1. **A linha de base A precisa ser minerável.** Toda a coleta DORA/SoD/SPACE do cenário A vem do histórico público do projeto. Quanto mais rico e padronizado o histórico (PRs, releases, CI, labels), melhor.
2. **As issues do projeto viram o backlog do fork (B e C).** A existência de issues bem escopadas — sobretudo *good first issue* e rótulos de esforço — permite parear tarefas de complexidade equivalente entre as condições (exigência da §2.3).
3. **A documentação de onboarding é a fonte da regra de SoD do cenário A.** Quando o projeto documenta "X aprovações de core devs antes do merge", temos um critério explícito de separação de funções entre pessoas para contar violações e comparar com B/C.

---

## 2. O que cada proposição exige — e de onde sai em um repositório GitHub

| Proposição | Dado necessário | Fonte pública no projeto OSS | Minerável p/ Cenário A? |
|---|---|---|---|
| **P1** lead time, deploy frequency | tempo PR→merge, commit→release, cadência de releases | GitHub API (PRs, commits, tags/releases) | ✅ Sim, retroativo |
| **P2 / P3** change failure rate, rework rate, violações de SoD | reverts/hotfixes, issues reabertas, PRs mergeados sem revisão independente | GitHub Actions (runs), commits de revert, labels `bug`/`regression`, autor-vs-merger dos PRs, CODEOWNERS | ✅ Sim (com proxies definidos) |
| **P4** SPACE bem-estar + carga | survey + telemetria de carga | telemetria minerável; **survey não é reconstituível** para A | ⚠️ Parcial — ver §5 |
| **P5** confiança / auditabilidade | entrevistas + survey | comunidade ativa e acessível p/ entrevistar | ⚠️ Coleta prospectiva |
| **P6** fricção em gate vs. diferida | logs de gate vs. incidentes pós-deploy | só existe em **C** (gates do HADS); em A/B usa-se incidentes/auditoria | ✅ para C; proxy em A |
| **P-lead** papel da liderança | escopo do trabalho de liderança | GOVERNANCE.md, steering council, papéis de maintainer | ✅ Documental |

**Conclusão do mapeamento:** as métricas **DORA são extraíveis de qualquer repositório GitHub ativo** via API (releases, PRs, Actions, issues) — portanto *não* são o critério que separa bons de maus candidatos. O que diferencia os candidatos são quatro coisas: (a) **SoD documentada e observável**, (b) **comunicação de comunidade arquivada e minerável**, (c) **rótulos de complexidade de tarefa** para o pareamento, e (d) **CI/CD público** para falha/recuperação.

---

## 3. Critérios de elegibilidade aplicados

Filtramos por: porte médio e atividade alta; licença que permite fork; governança documentada; processo de revisão explícito (SoD entre pessoas); presença de *good first issues*; e — crucialmente — **canal de comunicação da comunidade arquivado e público** (não efêmero).

O critério (c) merece destaque porque é o mais ignorado: a dimensão **Comunicação/colaboração do SPACE** e toda a fase qualitativa dependem de poder ler as conversas da comunidade. Um projeto cujo canal é **Discord** (efêmero, gated, sem busca histórica robusta) é muito pior para a pesquisa do que um cujo canal é uma **lista de discussão arquivada, GitHub Discussions ou um chat público permanentemente indexado** — mesmo que ambos sejam igualmente ativos.

---

## 4. Candidatos avaliados

### Tier 1 — melhor aderência (SoD documentada + comunicação arquivada + CI público)

**Zulip** (`zulip/zulip`) — chat corporativo, Python/Django + TypeScript. Apache-2.0.
É o padrão-ouro de onboarding. O [guia de contribuição](https://zulip.readthedocs.io/en/latest/contributing/contributing.html) e o [processo de revisão de PR](https://zulip.readthedocs.io/en/latest/contributing/review-process.html) descrevem um fluxo de revisão **multi-estágio rotulado por labels** (revisão por contribuidores → revisão de maintainer → revisão de documentação), o que dá uma trilha explícita de separação de funções entre pessoas para o cenário A. Diferencial decisivo: a comunidade do Zulip roda **no próprio Zulip, em chat público permanentemente arquivado e pesquisável** (chat.zulip.org) — a melhor fonte possível para a dimensão de Comunicação do SPACE e para a fase qualitativa. CI em GitHub Actions (público). *Good first issue* abundante. Porte médio-grande, mas forkável.

**Astropy** (`astropy/astropy`) — biblioteca científica de astronomia, Python. BSD-3. *(aderência temática ao repositório "Scientific-Research")*
Governança NumFOCUS documentada, [guia de contribuição](https://github.com/astropy/astropy/blob/main/CONTRIBUTING.md) e [quickstart de desenvolvimento](https://docs.astropy.org/en/latest/development/quickstart.html) detalhados. Diferencial: além do `Good-first-issue`, usa **rótulos de esforço** (`Effort-low` = horas, `Effort-medium` = dias) e `Package-novice` — um instrumento pronto para o **pareamento de tarefas por complexidade** exigido na §2.3. Comunicação via **lista/fórum astropy-dev arquivado** (minerável) + Slack. CI público.

**scikit-image** (`scikit-image/scikit-image`) — processamento de imagens científico, Python. BSD-3. *(aderência temática)*
Diferencial: a [governança SKIP-1](https://scikit-image.org/docs/stable/skips/1-governance.html) define uma regra de SoD **explícita e contável**: mudanças de código/API exigem **concordância de dois core developers e nenhuma objeção** (consenso preguiçoso), com steering council de 5 membros. Isso converte "violação de SoD" (P2/P3) em uma medida operacional direta sobre os PRs, e a estrutura do steering council alimenta P-leadership. Porte médio (mais isolável que Astropy).

### Tier 2 — boa aderência, com ressalvas

**scikit-learn** (`scikit-learn/scikit-learn`) — ML, Python. BSD-3. Governança e processo de revisão muito bem documentados; porém porte grande (custo de operar o fork em B/C é alto).

**DVC** (`iterative/dvc`) — versionamento de dados/experimentos ML, Python. Aderência temática a dados; *good first issue* presente. Ressalva: comunicação principal em **Discord** (efêmero/gated) — enfraquece a coleta da dimensão de Comunicação do SPACE e a fase qualitativa.

**Excalidraw** (`excalidraw/excalidraw`) — whiteboard, TypeScript. Porte médio, muito ativo, *good first issue* curado. Mesma ressalva do Discord como canal primário. Bom candidato se o estudo priorizar stack JS/web sobre rastreabilidade da comunicação.

---

## 5. Limites honestos das fontes públicas

Três lacunas que a documentação de onboarding **não** resolve e que precisam de desenho explícito:

1. **Configuração de branch protection / required reviews não é pública.** A API do GitHub não expõe as regras de proteção de branch para quem não é admin do repositório. Portanto, a SoD do cenário A deve ser **inferida do comportamento observável** (autor ≠ quem fez merge; número de aprovações; presença de CODEOWNERS) somada à **política documentada** — e não lida das configurações. Projetos com a regra escrita (scikit-image, Zulip) reduzem essa incerteza.

2. **O survey SPACE de bem-estar não é reconstituível retroativamente para o cenário A.** Bem-estar autorrelatado dos contribuidores históricos não existe em dado público. Para A, restam *proxies* de telemetria (atividade, commits fora de horário, latência de resposta em PR/issue). O survey propriamente dito só se aplica a B e C (o seu próprio HIC). Isso deve ser registrado como assimetria de instrumentação entre condições (relaciona-se ao "viés de instrumentação" da §8).

3. **Fricção em gate (P6) só existe na condição C.** Em A e B não há gates do HADS; o equivalente em A é a revisão humana (a "cola social"), e a comparação prevista-vs-diferida usa incidentes/auditoria como proxy. Normalizar as definições de "violação" e "incidente" entre as três condições antes da coleta é pré-requisito (§8).

---

## 6. Recomendação

Para um estudo de caso múltiplo com **replicação literal e teórica** (§2.3), sugiro 2–3 finalistas que contrastem de forma proveitosa:

- **scikit-image** como caso principal — SoD documentada e contável, porte isolável, governança explícita, aderência ao tema científico do repositório.
- **Zulip** como caso de comunicação rica — a única fonte de comunicação de comunidade verdadeiramente arquivada e pesquisável entre os avaliados, o que fortalece SPACE-comunicação e a fase qualitativa.
- **Astropy** como caso de pareamento de tarefas — os rótulos de esforço dão de graça a estratificação de complexidade que o protocolo exige.

Esses três são **literalmente replicáveis** (todos Python, governança consensual, BSD/Apache) e **teoricamente contrastantes** (biblioteca científica vs. aplicação de produto; comunicação por lista vs. por chat próprio), satisfazendo a lógica de seleção de casos do protocolo.

---

## 7. Próximo passo sugerido

Antes de fixar os finalistas, vale extrair um **snapshot quantitativo de viabilidade** via API do GitHub para cada candidato: nº de PRs/releases nos últimos 12 meses, % de PRs com revisor independente, presença de CODEOWNERS, cobertura de CI em Actions e contagem de *good first issues* abertas. Isso confirma empiricamente a mineração do cenário A e dimensiona o backlog disponível para B/C. Posso montar esse coletor se você quiser seguir.

---

### Fontes

- [Zulip — Contributing guide](https://zulip.readthedocs.io/en/latest/contributing/contributing.html)
- [Zulip — Pull request review process](https://zulip.readthedocs.io/en/latest/contributing/review-process.html)
- [Zulip — CONTRIBUTING.md](https://github.com/zulip/zulip/blob/main/CONTRIBUTING.md)
- [Astropy — CONTRIBUTING.md](https://github.com/astropy/astropy/blob/main/CONTRIBUTING.md)
- [Astropy — Contributing Quickstart](https://docs.astropy.org/en/latest/development/quickstart.html)
- [Astropy — Contribute page](https://www.astropy.org/contribute.html)
- [scikit-image — SKIP 1: governance and decision-making](https://scikit-image.org/docs/stable/skips/1-governance.html)
- [scikit-learn — Governance and decision-making](https://scikit-learn.org/stable/governance.html)
- [scikit-learn — Contributing](https://scikit-learn.org/stable/developers/contributing.html)
- [DVC — Community](https://dvc.org/community/)
- [DVC — Contributing (core)](https://doc.dvc.org/contributing/core)
- [Excalidraw — Contributing](https://docs.excalidraw.com/docs/introduction/contributing)
