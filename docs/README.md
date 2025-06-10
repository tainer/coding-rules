# Documentação do Projeto – Pasta `docs/`

_Última atualização: 2025-06-10_

Este **README.md** descreve como **organizar, versionar, atualizar e consumir** todo o
conteúdo localizado na pasta `docs/`. Ele serve tanto para **desenvolvedores humanos**
quanto para **agentes de IA** (ex.: ChatGPT, Cursor), garantindo um entendimento único e
não ambíguo das regras de documentação do repositório.

---

## 1. Propósito da pasta `docs/`

1. **Centralizar conhecimento** – guidelines de código, ADRs, tutoriais e requisitos.
2. **Estabelecer contratos** – definir convenções obrigatórias antes de alterar código.
3. **Ser “código”** – documentação é tratada como parte do código‐fonte, versionada,
   revisada em PRs e validada por CI.

---

## 2. Estrutura de diretórios

```text
docs/
├─ README.md                       # este arquivo
├─ adr/                            # Architecture Decision Records (cronológicos)
│  └─ 0001-first-decision.md
└─ guidelines/                     # todas as diretrizes de artefatos
   ├─ <tipo>/                      # agrupamento por tecnologia ou domínio
   │  ├─ .common/                  # regras globais para o tipo
   │  └─ <artefato>/               # diretrizes específicas do artefato
   └─ tutorials/                   # passo-a-passo sequenciais (opcional)
```

### Exemplo concreto

```text
docs/
├─ adr/
│  └─ 0001-first-decision.md
└─ guidelines/
   ├─ dotnet/
   │  ├─ .common/
   │  │  ├─ coding-standards.md
   │  │  ├─ dependencies.md
   │  │  └─ result-pattern.md
   │  ├─ api-billing/
   │  │  ├─ api-guidelines.md
   │  │  └─ testing-guidelines.md
   │  └─ windows-client/
   │     └─ winforms-guidelines.md
   ├─ angular/
   │  ├─ .common/
   │  │  └─ spa-standards.md
   │  └─ my-spa/
   │     └─ spa-guidelines.md
   ├─ infra/
   │  ├─ .common/
   │  │  └─ infra-standards.md
   │  ├─ docker/
   │  │  └─ docker-guidelines.md
   │  └─ cicd/
   │     └─ cicd-guidelines.md
   ├─ domain/
   │  ├─ .common/
   │  │  └─ domain-principles.md
   │  └─ requisitos-funcionais.md
   └─ tutorials/
      ├─ 01-setup.md
      └─ 02-build.md
```

---

## 3. Convenções de nomenclatura

| Tipo de documento           | Convenção de arquivo             | Observações                                               |
|-----------------------------|----------------------------------|-----------------------------------------------------------|
| Guideline **local**         | `<slug>.md`                      | Slug em _kebab-case_, sem números ou versão               |
| Guideline **comum**         | `.common/<slug>.md`              | Aplica-se a todos os artefatos do tipo                    |
| **ADR**                     | `0001-<slug>.md`                 | Quatro dígitos, ordem cronológica fixa                    |
| **Tutorial** (opcional)    | `NN-<slug>.md` em `tutorials/`   | Sequência pedagógica, dois dígitos (`01`, `02`…)          |

> **Slug** deve refletir o assunto de forma clara (`api-guidelines`, `testing-guidelines`).

Os nomes não carregam número de versão; versionamento vive no front‑matter YAML.

---

## 4. Front‑matter YAML

Cada arquivo Markdown _deve_ iniciar com bloco YAML delimitado por `---`:

```yaml
---
title: "API Guidelines – Billing"
version: 1.5
scope: local            # local | common | global
order: 30               # opcional; usado em geradores de site
owner: Billing Team
lastReviewed: 2025-06-10
---
```

* **title** – título humano-legível.  
* **version** – incrementado a cada mudança de conteúdo relevante.  
* **scope** – determina precedência de conflito.  
* **order** – controle de navegação; omitido se não necessário.  
* **owner** – equipe/pessoa responsável pela manutenção.  
* **lastReviewed** – data da última revisão consciente.

---

## 5. Política de precedência (conflitos)

1. **Local** `guidelines/<tipo>/<artefato>/`  
2. **Comum por tipo** `guidelines/<tipo>/.common/`  
3. **Global** `00-basic.mdc` + ADRs

Se uma guideline local sobrescrever regra de escopo superior, inclua bloco:

```markdown
> **Override**: substitui a regra _X_ definida em `../.common/coding-standards.md`
```

Agentes de IA e revisores devem respeitar essa hierarquia.

---

## 6. Processos de manutenção

| Ação | Passos |
|------|--------|
| **Criar/Atualizar guideline** | 1. Editar conteúdo<br>2. Incrementar `version` e `lastReviewed`<br>3. Adicionar bloco **Override** se necessário |
| **Abrir Pull Request** | CI valida:<br>• Links internos<br>• Front‑matter completo<br>• Duplicidade de `order`<br>• Se `.common/` mudou, requer aprovação de owners de artefatos do tipo |
| **Job Nightly** | Envia alerta ao `owner` quando `lastReviewed` > 90 dias |
| **ADR** | Utilize template `adr/000X-<slug>.md`; mantenha link cruzado com guidelines afetadas |

---

## 7. Uso por agentes de IA

1. **Detecção de contexto** – localizar guideline aplicável seguindo precedência.  
2. **Verificação de aderência** – qualquer código sugerido deve cumprir as regras
   encontradas.  
3. **Geração de documentação** – agentes devem criar rascunhos iniciais de guideline ao
   gerar novo artefato.

_Nunca ignore regras locais em favor das globais._

---

## 8. FAQ

> **Posso usar números no nome de arquivos para ordenar?**  
> Não; utilize `order:` no front‑matter ou ajuste o índice neste README.

> **Quando criar pasta de artefato?**  
> Sempre que um grupo de regras locais divergir > 20 % das regras comuns.

> **Quando dividir guideline grande?**  
> Se exceder ~400 linhas ou cobrir tópicos distintos (design, testing, observability).

> **Como lidar com links quebrados?**  
> CI falhará a PR; corrija antes de dar merge.

---

> _“Documentação é código. Trate-a com a mesma disciplina.”_  
> — Pilares do colegiado
