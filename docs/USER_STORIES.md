# User Stories - Encurtador de URL

Este documento contém as histórias de usuário do projeto **Encurtador de URL** desenvolvido no AI Coding Dojo.

---

## Épico: Sistema de Encurtamento de URLs

Como **usuário**, quero **encurtar URLs longas** para que **eu possa compartilhá-las facilmente em redes sociais, emails e outros meios de comunicação**.

---

## US-001: Criar URL Encurtada Automaticamente

**Como** usuário  
**Quero** colar uma URL longa e gerar automaticamente uma URL curta  
**Para que** eu possa compartilhar links longos de forma simplificada

### Critérios de Aceitação

- [ ] Sistema aceita URLs que começam com `http://` ou `https://`
- [ ] Sistema valida se a URL é válida antes de processar
- [ ] Sistema gera automaticamente um código único de 5 caracteres alfanuméricos
- [ ] Sistema retorna a URL curta no formato `http://dominio.com/{codigo}`
- [ ] Sistema exibe mensagem de sucesso com a URL gerada
- [ ] Sistema inicializa o contador de cliques em zero
- [ ] Sistema registra a data e hora de criação

### Regras de Negócio

**RN-001.1:** URL original deve ter no máximo 2000 caracteres  
**RN-001.2:** Código gerado deve ser único no sistema  
**RN-001.3:** URLs duplicadas (mesma URL original) podem gerar códigos diferentes  
**RN-001.4:** Sistema deve permitir criar múltiplas URLs por sessão

### Cenário Principal

```
DADO que estou na página inicial
QUANDO eu colo "https://www.exemplo.com/artigo-muito-longo?id=123&categoria=tech"
E clico em "Encurtar URL"
ENTÃO o sistema gera um código único (ex: "a3k92")
E exibe "http://localhost:3000/a3k92"
E mostra mensagem "✓ URL encurtada com sucesso!"
```

### Cenários Alternativos

**Cenário 1: URL inválida**
```
DADO que estou na página inicial
QUANDO eu colo "www.exemplo.com" (sem protocolo)
E clico em "Encurtar URL"
ENTÃO o sistema exibe erro "URL inválida. Use http:// ou https://"
E não cria a URL encurtada
```

**Cenário 2: URL muito longa**
```
DADO que estou na página inicial
QUANDO eu colo uma URL com mais de 2000 caracteres
E clico em "Encurtar URL"
ENTÃO o sistema exibe erro "URL muito longa. Máximo 2000 caracteres"
E não cria a URL encurtada
```

---

## US-002: Criar URL com Código Personalizado

**Como** usuário  
**Quero** definir um código personalizado para minha URL curta  
**Para que** eu possa criar links memoráveis e fáceis de compartilhar

### Critérios de Aceitação

- [ ] Sistema permite campo opcional para código personalizado
- [ ] Sistema aceita códigos com 3 a 20 caracteres
- [ ] Sistema aceita apenas letras (a-z, A-Z), números (0-9) e hífens (-)
- [ ] Sistema verifica se o código já está em uso
- [ ] Sistema retorna erro se código duplicado
- [ ] Sistema cria URL com código personalizado quando disponível

### Regras de Negócio

**RN-002.1:** Código personalizado deve ter entre 3 e 20 caracteres  
**RN-002.2:** Código personalizado deve ser único (case-sensitive)  
**RN-002.3:** Não permitir códigos reservados: "api", "admin", "dashboard", "health"  
**RN-002.4:** Código pode conter apenas: a-z, A-Z, 0-9 e hífen (-)

### Cenário Principal

```
DADO que estou na página inicial
QUANDO eu colo "https://www.loja.com/promocao-verao-2024"
E digito "promo-verao" no campo código personalizado
E clico em "Encurtar URL"
ENTÃO o sistema verifica se "promo-verao" está disponível
E cria "http://localhost:3000/promo-verao"
E exibe mensagem de sucesso
```

### Cenários Alternativos

**Cenário 1: Código já existe**
```
DADO que existe uma URL com código "promo-verao"
QUANDO eu tento criar nova URL com código "promo-verao"
ENTÃO o sistema exibe erro "Código já em uso. Tente outro código"
E sugere código alternativo "promo-verao-2"
```

**Cenário 2: Código inválido**
```
DADO que estou criando uma URL
QUANDO eu digito "meu_link!" (com caracteres especiais)
ENTÃO o sistema exibe erro "Use apenas letras, números e hífens"
E não permite criar a URL
```

**Cenário 3: Código muito curto**
```
DADO que estou criando uma URL
QUANDO eu digito "ab" (2 caracteres)
ENTÃO o sistema exibe erro "Código deve ter entre 3 e 20 caracteres"
```

---

## US-003: Redirecionar para URL Original

**Como** usuário final  
**Quero** acessar uma URL curta  
**Para que** eu seja redirecionado automaticamente para o site original

### Critérios de Aceitação

- [ ] Sistema redireciona automaticamente ao acessar URL curta
- [ ] Sistema incrementa contador de cliques a cada acesso
- [ ] Sistema retorna HTTP 302 (Found) com header Location
- [ ] Sistema retorna 404 se código não existe
- [ ] Redirecionamento ocorre em menos de 100ms

### Regras de Negócio

**RN-003.1:** Cada acesso incrementa o contador em 1  
**RN-003.2:** Redirecionamento é permanente durante vida da URL  
**RN-003.3:** URLs deletadas retornam 404  
**RN-003.4:** Contador não distingue usuários únicos (conta todos os cliques)

### Cenário Principal

```
DADO que existe URL curta "http://localhost:3000/a3k92"
E ela aponta para "https://www.exemplo.com/artigo"
QUANDO eu acesso "http://localhost:3000/a3k92"
ENTÃO o sistema incrementa o contador de cliques
E redireciona para "https://www.exemplo.com/artigo"
E o navegador carrega a página original
```

### Cenários Alternativos

**Cenário 1: Código não existe**
```
DADO que não existe URL com código "xyz999"
QUANDO eu acesso "http://localhost:3000/xyz999"
ENTÃO o sistema retorna página 404 Not Found
E exibe mensagem "URL não encontrada"
```

---

## US-004: Visualizar Lista de URLs Criadas

**Como** usuário  
**Quero** ver todas as URLs que criei  
**Para que** eu possa gerenciar e acompanhar meus links

### Critérios de Aceitação

- [ ] Sistema lista todas as URLs ordenadas por data (mais recentes primeiro)
- [ ] Para cada URL, exibe: código, URL original, número de cliques, data de criação
- [ ] Sistema exibe mensagem quando lista está vazia
- [ ] Sistema carrega lista automaticamente ao abrir a página
- [ ] Lista é responsiva e funciona em mobile

### Regras de Negócio

**RN-004.1:** Limite de 50 URLs por página (paginação)  
**RN-004.2:** URLs ordenadas por createdAt DESC (mais novas primeiro)  
**RN-004.3:** URL original truncada com "..." se maior que 60 caracteres  
**RN-004.4:** Data formatada no padrão brasileiro (dd/mm/yyyy)

### Cenário Principal

```
DADO que existem 3 URLs criadas no sistema
QUANDO eu acesso a página inicial
ENTÃO o sistema carrega e exibe as 3 URLs
E cada item mostra código, URL original, cliques e data
E URLs estão ordenadas da mais recente para a mais antiga
```

### Cenários Alternativos

**Cenário 1: Lista vazia**
```
DADO que não existem URLs no sistema
QUANDO eu acesso a página inicial
ENTÃO o sistema exibe mensagem "Nenhuma URL criada ainda"
E mostra ícone ilustrativo
```

---

## US-005: Copiar URL Curta

**Como** usuário  
**Quero** copiar a URL curta com um clique  
**Para que** eu possa compartilhá-la rapidamente

### Critérios de Aceitação

- [ ] Sistema disponibiliza botão "Copiar" em cada URL
- [ ] Sistema copia URL para área de transferência ao clicar
- [ ] Sistema exibe feedback visual de sucesso
- [ ] Botão muda temporariamente para "✓ Copiado"
- [ ] Sistema exibe toast "URL copiada!" por 3 segundos

### Regras de Negócio

**RN-005.1:** Copiar deve funcionar em todos os navegadores modernos  
**RN-005.2:** Feedback visual dura 2 segundos  
**RN-005.3:** Múltiplos cliques devem funcionar corretamente

### Cenário Principal

```
DADO que vejo uma URL na lista
QUANDO eu clico no botão "Copiar"
ENTÃO a URL "http://localhost:3000/a3k92" é copiada para clipboard
E o botão muda para "✓ Copiado" por 2 segundos
E aparece toast "URL copiada com sucesso!"
E após 2 segundos o botão volta ao estado original
```

---

## US-006: Visualizar Detalhes de uma URL

**Como** usuário  
**Quero** ver informações detalhadas de uma URL específica  
**Para que** eu possa analisar seu desempenho

### Critérios de Aceitação

- [ ] Sistema disponibiliza botão "Detalhes" para cada URL
- [ ] Sistema abre modal com informações completas
- [ ] Modal exibe: código, URL original completa, URL curta, cliques, data de criação
- [ ] Modal tem botão "Fechar" e permite fechar com ESC
- [ ] Modal é responsivo

### Regras de Negócio

**RN-006.1:** Modal deve ser acessível (ARIA)  
**RN-006.2:** Dados devem ser buscados em tempo real  
**RN-006.3:** Modal deve ter overlay escuro (80% opacidade)

### Cenário Principal

```
DADO que vejo uma URL na lista
QUANDO eu clico em "Detalhes"
ENTÃO o sistema abre modal centralizado
E exibe todas as informações da URL
E bloqueia interação com página ao fundo
QUANDO eu clico em "Fechar" ou pressiono ESC
ENTÃO o modal fecha e volto à lista
```

---

## US-007: Deletar URL

**Como** usuário  
**Quero** deletar uma URL criada  
**Para que** eu possa remover links que não uso mais

### Critérios de Aceitação

- [ ] Sistema disponibiliza botão "Deletar" para cada URL
- [ ] Sistema solicita confirmação antes de deletar
- [ ] Sistema remove URL do banco de dados
- [ ] Sistema remove URL da lista visualmente com animação
- [ ] Sistema exibe toast de sucesso após deletar
- [ ] URL deletada retorna 404 ao ser acessada

### Regras de Negócio

**RN-007.1:** Deleção é permanente (não há recuperação)  
**RN-007.2:** Confirmação é obrigatória  
**RN-007.3:** Histórico de cliques é perdido ao deletar  
**RN-007.4:** Código deletado pode ser reutilizado posteriormente

### Cenário Principal

```
DADO que vejo uma URL na lista
QUANDO eu clico em "Deletar"
ENTÃO o sistema exibe modal de confirmação
E pergunta "Tem certeza que deseja deletar esta URL?"
QUANDO eu confirmo
ENTÃO o sistema deleta a URL
E remove da lista com animação
E exibe toast "URL deletada com sucesso"
```

### Cenários Alternativos

**Cenário 1: Cancelar deleção**
```
DADO que cliquei em "Deletar"
QUANDO vejo o modal de confirmação
E clico em "Cancelar"
ENTÃO o modal fecha
E a URL permanece na lista
```

---

## US-008: Visualizar Estatísticas Gerais

**Como** usuário  
**Quero** ver estatísticas gerais do sistema  
**Para que** eu possa ter visão geral do uso

### Critérios de Aceitação

- [ ] Sistema exibe total de URLs criadas
- [ ] Sistema exibe total de cliques acumulados
- [ ] Sistema exibe top 5 URLs mais acessadas
- [ ] Estatísticas são exibidas no header
- [ ] Estatísticas atualizam ao criar/deletar URLs

### Regras de Negócio

**RN-008.1:** Total de cliques é soma de todos os contadores  
**RN-008.2:** Top URLs ordenadas por número de cliques DESC  
**RN-008.3:** Estatísticas atualizam em tempo real  
**RN-008.4:** Limite de 5 URLs no top ranking

### Cenário Principal

```
DADO que existem URLs no sistema
QUANDO eu acesso a página inicial
ENTÃO vejo no header:
  - "150 URLs criadas"
  - "3.542 cliques totais"
E vejo lista das 5 URLs mais acessadas
Com código e número de cliques de cada uma
```

---

## US-009: Validar URLs

**Como** sistema  
**Quero** validar URLs antes de encurtar  
**Para que** apenas URLs válidas sejam aceitas

### Critérios de Aceitação

- [ ] Sistema valida protocolo (http/https obrigatório)
- [ ] Sistema valida formato RFC 3986
- [ ] Sistema valida comprimento máximo
- [ ] Sistema valida caracteres permitidos
- [ ] Sistema exibe erro específico para cada validação

### Regras de Negócio

**RN-009.1:** Protocolo obrigatório: http:// ou https://  
**RN-009.2:** Comprimento máximo: 2000 caracteres  
**RN-009.3:** URL deve ser parseable pela API URL do JavaScript  
**RN-009.4:** Espaços em branco devem ser trimados

### Cenário Principal

```
DADO que estou criando uma URL
QUANDO eu colo "https://www.exemplo.com/página"
ENTÃO o sistema valida:
  ✓ Tem protocolo https://
  ✓ É URL válida
  ✓ Tem menos de 2000 caracteres
E permite criar a URL
```

### Cenários de Erro

**Erro 1: Sem protocolo**
```
Input: "www.exemplo.com"
Erro: "URL deve começar com http:// ou https://"
```

**Erro 2: Protocolo inválido**
```
Input: "ftp://servidor.com"
Erro: "Apenas protocolos HTTP e HTTPS são permitidos"
```

**Erro 3: URL muito longa**
```
Input: URL com 2500 caracteres
Erro: "URL muito longa. Máximo de 2000 caracteres"
```

**Erro 4: URL inválida**
```
Input: "http://exemplo com espaços"
Erro: "URL inválida. Verifique o formato"
```

---

## US-010: Sistema de Backup

**Como** sistema  
**Quero** criar backup automático dos dados  
**Para que** não haja perda de informação

### Critérios de Aceitação

- [ ] Sistema cria backup a cada modificação (create/delete)
- [ ] Backup é salvo em arquivo separado com timestamp
- [ ] Sistema mantém últimos 10 backups
- [ ] Backups mais antigos são automaticamente removidos

### Regras de Negócio

**RN-010.1:** Backup criado em `data/backups/urls_YYYY-MM-DD_HH-MM-SS.json`  
**RN-010.2:** Máximo de 10 arquivos de backup  
**RN-010.3:** Backup é cópia completa do arquivo principal  
**RN-010.4:** Backup não afeta performance de resposta da API

---

## Priorização das User Stories

### Must Have (Sprint 1)
- US-001: Criar URL Encurtada Automaticamente
- US-003: Redirecionar para URL Original
- US-004: Visualizar Lista de URLs Criadas
- US-009: Validar URLs

### Should Have (Sprint 2)
- US-002: Criar URL com Código Personalizado
- US-005: Copiar URL Curta
- US-007: Deletar URL

### Could Have (Sprint 3)
- US-006: Visualizar Detalhes de uma URL
- US-008: Visualizar Estatísticas Gerais

### Won't Have (Backlog)
- US-010: Sistema de Backup (implementação técnica)

---

## Estimativas (Story Points)

| User Story | Complexidade | Story Points |
|------------|--------------|--------------|
| US-001 | Média | 5 |
| US-002 | Média | 3 |
| US-003 | Alta | 8 |
| US-004 | Média | 5 |
| US-005 | Baixa | 2 |
| US-006 | Baixa | 3 |
| US-007 | Média | 5 |
| US-008 | Média | 5 |
| US-009 | Alta | 8 |
| US-010 | Média | 5 |

**Total:** 49 Story Points

---

## Dependências

```
US-009 (Validação)
    ↓
US-001 (Criar URL)
    ↓
US-003 (Redirecionar) ← US-002 (Código Custom)
    ↓
US-004 (Listar)
    ↓
US-005 (Copiar) + US-006 (Detalhes) + US-007 (Deletar)
    ↓
US-008 (Estatísticas)
```

---

## Notas Técnicas

### Performance
- Criação de URL deve responder em < 200ms
- Redirecionamento deve ocorrer em < 100ms
- Listagem deve carregar em < 300ms

### Segurança
- Validação no frontend E backend
- Sanitização de inputs
- Rate limiting: 100 req/min por IP

### Compatibilidade
- Navegadores: Chrome 90+, Firefox 88+, Safari 14+, Edge 90+
- Mobile: iOS 14+, Android 10+

---

## Referências

- [Documentação da API](./api/README.md)
- [Documentação do Frontend](./frontend/README.md)
- [Regras de Negócio](./BUSINESS_RULES.md)
- [Algoritmo de Encurtamento](./SHORTENING_ALGORITHM.md)