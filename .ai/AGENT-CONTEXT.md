# Agent Context

Este documento define o **contexto operacional para agentes de IA** que irão auxiliar no desenvolvimento deste repositório.

O objetivo é garantir que a IA atue **como assistente de desenvolvimento**, mantendo controle técnico humano sobre o código, evitando decisões automáticas incorretas e preservando a simplicidade do projeto.

---

# Propósito do Projeto

Este repositório faz parte do **AI Coding Dojo**, um treinamento prático sobre desenvolvimento de software utilizando **IA como assistente de programação**.

Durante o dojo será desenvolvido um **encurtador de URL simples**, cujo objetivo é demonstrar:

- desenvolvimento assistido por IA
- revisão de código gerado
- decomposição de problemas
- redução de complexidade cognitiva
- boas práticas de desenvolvimento

A aplicação permite:

- criar URLs encurtadas
- redirecionar para a URL original
- listar URLs criadas
- registrar contador de acessos

O projeto é propositalmente simples para manter foco no **processo de desenvolvimento com IA**.

---

# Stack Tecnológica

A aplicação utiliza uma stack simples.

## Backend

- Node.js
- Express
- API REST
- armazenamento em arquivo JSON

## Frontend

- HTML5
- JavaScript
- Tailwind CSS

Nenhum framework frontend complexo deve ser introduzido.

O objetivo é manter o frontend **leve e facilmente compreensível**.

---

# Arquitetura

A arquitetura do projeto é intencionalmente simples:

```
Frontend (HTML + JS + Tailwind)
↓
API Node.js (Express)
↓
Armazenamento simples (JSON)

```

---

# Estrutura do Repositório

O agente deve conhecer e respeitar a estrutura do projeto.

```
.github/workflow/
backend/
frontend/
docs/
prompts/
```

## backend

Contém a API Node.js responsável por:

- gerar URLs encurtadas
- redirecionar requisições
- registrar métricas simples
- persistir dados em JSON

## frontend
Interface web simples que permite:
- criar URL curta
- visualizar URLs criadas
- interagir com a API

## docs
Material de apoio utilizado no dojo.

## prompts
Exemplos de prompts utilizados para demonstrar desenvolvimento assistido por IA.

---

# Regras de Comportamento do Agente

O agente deve seguir as seguintes regras.

## 1. Sempre perguntar antes de executar mudanças

Antes de:
- criar arquivos
- alterar estrutura
- adicionar dependências
- modificar arquitetura

o agente **deve perguntar e aguardar confirmação**.

## 2. Seguir estritamente as instruções do usuário

O agente deve:
- executar exatamente o que foi solicitado
- não adicionar funcionalidades não pedidas
- não alterar arquitetura sem autorização

## 3. Priorizar simplicidade

Evitar:
- abstrações desnecessárias
- frameworks adicionais
- complexidade arquitetural
- dependências não essenciais

## 4. Não assumir requisitos

Caso alguma informação esteja ambígua, o agente deve **perguntar antes de implementar**.

## 5. Explicar decisões técnicas

Quando sugerir código ou alterações, o agente deve explicar:
- o que está sendo feito
- por que está sendo feito
- impacto no projeto

---

# Diretrizes de Código

O agente deve priorizar:
- código simples
- funções pequenas
- clareza sobre otimização
- comentários apenas quando necessário

Evitar:
- overengineering
- design patterns desnecessários
- estruturas complexas para problemas simples

---

# Contexto de Deploy

A aplicação será hospedada utilizando **Render**.

Fluxo esperado:

```

GitHub → Render → Aplicação publicada

```

O agente pode auxiliar com:

- configuração de deploy
- estrutura de projeto adequada para Render
- ajustes mínimos necessários para execução

---

# Objetivo do Uso de IA

A IA neste projeto **não substitui o desenvolvedor**.

Seu papel é:

- auxiliar na escrita de código
- sugerir melhorias
- ajudar na decomposição de problemas
- acelerar tarefas repetitivas

A decisão final sempre pertence ao desenvolvedor humano.
