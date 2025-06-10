---
title: "Result Pattern with FluentResults"
version: 1.0
scope: common
order: 40
owner: Core Platform
lastReviewed: 2025-06-10
---

## ✳️ Visão Geral

Este guia padroniza o uso do padrão **Result** com a biblioteca [FluentResults](https://github.com/altmann/FluentResults) nas aplicações do projeto. Seu objetivo é garantir:

- Tratamento de erros desacoplado e claro.
- Uniformização das mensagens de erro.
- Interfaces limpas e previsíveis para o domínio.

---

## 🧠 Quando Usar o Padrão Result?

Use `Result<T>` como retorno de **métodos que podem falhar**:

- Operações de domínio: validações, regras de negócio.
- Comandos e queries (CQRS).
- Acesso a recursos externos (banco, API, arquivos).
- Serviços de aplicação que encapsulam regras sensíveis.

---

## 🚫 Quando **Não** Usar Result?

Evite usar `Result` quando:

- O método é trivial e **não possui lógica de erro** (ex: propriedades calculadas simples).
- O retorno se dá por exceções inevitáveis (ex: erro fatal de infraestrutura).

---

## ❓ Quando Usar `Exception` em vez de `Result`?

| Situação                                    | Usar Exception? | Usar Result? |
|--------------------------------------------|------------------|---------------|
| Erro de programação (null, cast inválido)   | ✅ Sim            | ❌ Não         |
| Erro de negócio (CPF inválido, duplicado)   | ❌ Não            | ✅ Sim         |
| Falha de infra controlada (API 503, timeout)| ❌ Não (preferir Result com retry) | ✅ Sim |
| Falha irrecuperável (falha do host, disco)  | ✅ Sim            | ❌ Não         |

---

## 📦 Organização dos Erros por Módulo

Cada módulo deve conter uma classe `Erros{{Modulo}}.cs` com todos os erros possíveis do módulo:

```csharp
namespace Api.Modulos.Expressoes;

public static class ErrosExpressoes
{
    public static ErroValidacao ParentesesNaoBalanceados => new("Parênteses não balanceados");
    public static ErroValidacao OperadorTernarioMalFormado => new("Operador ternário mal formado");
    public static ErroValidacao OperadorNaoSuportado(string operador) => new($"Operador '{operador}' não suportado");
}
```

**Regras:**
- Sempre utilizar objetos especializados que herdam de `Error`.
- Nunca usar mensagens de erro soltas no código.

---

## ✅ Retornos

### Sucesso

Prefira retornar diretamente o valor, com conversão implícita:

```csharp
return resultado;
```

> Exige que o projeto esteja com FluentResults configurado para isso (inclusão de `using FluentResults;`).

---

### Falha (Erro de Domínio ou Aplicação)

Retornar erro diretamente da classe de erros:

```csharp
return ErrosExpressoes.ParentesesNaoBalanceados;
```

⚠️ **Evite:**

```csharp
return Result.Fail("mensagem direta");
return Result.Fail(ErrosExpressoes.ParentesesNaoBalanceados);
```

---

### Repassando Erros

Ao propagar falhas de métodos que também retornam `Result<T>`:

```csharp
var resultado = Validar();
if (resultado.IsFailed)
    return Result.Fail(resultado.Errors);
```

---

## 🧪 Testes e Comportamento

Ao escrever testes de métodos que retornam `Result<T>`, prefira verificar:

- `.IsSuccess` ou `.IsFailed`
- `.Errors` com mensagens específicas
- `.Value` no caso de sucesso

Exemplo:
```csharp
var resultado = servico.CalcularDesconto("clienteVip");
resultado.IsSuccess.Should().BeTrue();
resultado.Value.Should().Be(10);
```

---

## 🔐 Tratamento de Erros de Segurança

Para erros relacionados a autenticação/autorização, use erros especializados:

```csharp
public class ErroAcessoNegado : Error
{
    public ErroAcessoNegado(string mensagem = "Acesso negado.") : base(mensagem) { }
}
```

---

## 🛡️ Benefícios

- Forte coesão e rastreabilidade de falhas.
- Ajuda na composição de serviços.
- Clareza semântica: `Result` força o consumidor a lidar com falha/sucesso.

---

## 📥 Referências

- [FluentResults no GitHub](https://github.com/altmann/FluentResults)
- [MS Docs - Exceptions vs Error Codes](https://learn.microsoft.com/en-us/dotnet/standard/exceptions/)
- [Vladimir Khorikov – Functional Error Handling](https://enterprisecraftsmanship.com)

---

## 📌 Perguntas para expansão futura

1. Como estruturar erros hierárquicos com múltiplos níveis de detalhe?
2. Como integrar FluentResult com logging estruturado e Application Insights?
3. Como mapear Result<T> em DTOs ou respostas HTTP corretamente (200/400/404/500)?
