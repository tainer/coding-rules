# Documentação do Projeto – Pasta `docs/`

_Última atualização: 2025-06-10_

Este **README.md** descreve **como organizar, versionar e consumir** todo o conteúdo dentro da pasta
`docs/`. Ele serve tanto para **desenvolvedores humanos** quanto para **agentes de IA** (ex.: ChatGPT, Cursor),
garantindo um entendimento único das regras de documentação do repositório.

---

## 1. Propósito da pasta `docs/`

* Centralizar **guidelines**, **Architecture Decision Records (ADRs)**, tutoriais e
  referências de requisitos.
* Fornecer contexto rápido a qualquer pessoa ou agente que precise **conhecer as
  convenções antes de alterar código**.
* Agir como contrato vivo: alterações em artefatos exigem revisão das diretrizes
  correspondentes (validado por CI).

---

## 2. Estrutura de diretórios

```text
docs/
├─ README.md                 # este arquivo
├─ adr/                      # Architecture Decision Records (0001-... cronológicos)
├─ <tipo>/                   # artefatos agrupados por tecnologia ou domínio
│  ├─ .common/               # regras globais para **todos** os artefatos desse tipo
│  └─ <artefato>/            # diretório específico (ex.: api-billing, windows-client)
└─ tutorials/                # passo‑a‑passo sequenciais (opcional)
```

**Tipos** comuns: `dotnet/`, `angular/`, `infra/`, `domain/`.  
Exemplo completo:

```text
docs/
├─ dotnet/
│  ├─ .common/
│  │  ├─ coding-standards.md
│  │  └─ dependencies.md
│  ├─ api-billing/
│  │  └─ api-guidelines.md
│  └─ windows-client/
│     └─ winforms-guidelines.md
└─ infra/
   ├─ .common/
   │  └─ infra-standards.md
   └─ docker/
      └─ docker-guidelines.md
```

---

## 3. Convenções de nomenclatura

| Tipo de documento | Convenção | Observações |
|-------------------|-----------|-------------|
| **Guideline local** | `<slug>.md` | Slug em _kebab‑case_ (ex.: `api-guidelines.md`) |
| **Guideline comum** | `.common/<slug>.md` | Aplica-se a todos os artefatos do tipo |
| **ADR** | `0001-<slug>.md` | Sequencial cronológico, 4 dígitos |
| **Tutorial** | `NN-<slug>.md` (em `tutorials/`) | Sequência pedagógica |

Os nomes **não** contêm número de versão. Versões residem no *front‑matter* YAML.

---

## 4. Front‑matter padrão

```yaml
---
title: "API Guidelines – Billing"
version: 1.5
scope: local            # local | common | global
order: 30               # opcional; navegação em site estático
owner: Billing Team
lastReviewed: 2025-06-10
---
```

* `scope` define precedência de conflito.  
* `order` só é necessário quando o site/documentação precisa de ordenação customizada.

---

## 5. Política de precedência (Conflitos)

1. **Local** (`docs/<tipo>/<artefato>/`)  
   Regras específicas do artefato.
2. **Common** (`docs/<tipo>/.common/`)  
   Regras globais para o tipo de artefato.
3. **Global** (`docs/` raiz ou `adr/`)  
   Valem para todo o repositório.

Caso um guideline local **sobreponha** uma regra do nível _common_ ou _global_,
deve conter um bloco explícito:

```markdown
> **Override**: substitui a regra _X_ definida em `../.common/coding-standards.md`
```

Isso facilita a análise por agentes de IA e validadores CI.

---

## 6. Processos de manutenção

1. **Criar/Alterar guideline**  
   * Atualize o slug (se necessário) e o `version` no front‑matter.  
   * Se sobrepor regra comum, adicione bloco **Override**.
2. **Abrir Pull Request**  
   * CI verifica links, front‑matter obrigatório e `scope`.  
   * Alterar arquivo em `.common/` exige aprovação dos _owners_ de todos os artefatos
     do tipo.
3. **Job Nightly**  
   * Envia alerta quando `lastReviewed` excede _N_ dias (configurável).

---

## 7. Uso por agentes de IA

* **Contexto**: antes de sugerir alterações de código, o agente deve localizar o
  guideline aplicável seguindo a política de precedência.  
* **Geração de código**: qualquer sugestão deve citar a regra de estilo ou arquitetura
  relevante, garantindo aderência.  
* **Documentação**: ao criar novo artefato, gerar automaticamente rascunho de guideline
  no path correto.

---

## 8. FAQ Rápido

* **Posso usar números no nome do arquivo para ordenar?**  
  → Não. Use `order:` no front‑matter ou ajuste o índice em `README.md`.
* **Quando criar nova pasta de artefato?**  
  → Quando as convenções locais diferirem do `.common` em mais de 20 % das regras.
* **E se um guideline ficar muito grande?**  
  → Divida por temas (_design_, _testing_, _observability_) mantendo mesma pasta.

---

> _“Documentação é código. Trate‑a com a mesma disciplina.”_  
> — Pilares do colegiado
