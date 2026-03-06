# Regras de Negócio - Encurtador de URL

Este documento contém todas as regras de negócio do projeto **Encurtador de URL** desenvolvido no AI Coding Dojo.

---

## 1. Regras de URLs

### RN-URL-001: Protocolo Obrigatório
**Descrição:** Todas as URLs devem começar com protocolo HTTP ou HTTPS.

**Regra:**
- URLs devem iniciar com `http://` ou `https://`
- Protocolos FTP, FILE, ou outros NÃO são aceitos
- URLs sem protocolo devem ser rejeitadas

**Exemplos:**
```
✅ VÁLIDO:
- https://www.exemplo.com
- http://exemplo.com/pagina
- https://subdomain.exemplo.com:8080/path

❌ INVÁLIDO:
- www.exemplo.com (sem protocolo)
- ftp://servidor.com (protocolo não suportado)
- exemplo.com (sem protocolo)
```

**Justificativa:** Garantir que todos os redirecionamentos funcionem corretamente e evitar ambiguidade.

---

### RN-URL-002: Comprimento Máximo
**Descrição:** URLs originais têm limite de tamanho.

**Regra:**
- Comprimento máximo: 2000 caracteres
- Comprimento mínimo: 10 caracteres
- Contagem inclui protocolo e todos os parâmetros

**Exemplos:**
```
✅ VÁLIDO: https://www.exemplo.com (22 caracteres)
❌ INVÁLIDO: URL com 2001+ caracteres
```

**Justificativa:** 
- 2000 caracteres é limite de vários navegadores
- Prevenir abuse do sistema
- Manter compatibilidade com padrões web

---

### RN-URL-003: Validação de Formato
**Descrição:** URLs devem seguir padrão RFC 3986.

**Regra:**
- URL deve ser parseável pela API URL do JavaScript
- Domínio deve ser válido
- Caracteres especiais devem estar corretamente codificados

**Exemplos:**
```
✅ VÁLIDO:
- https://www.exemplo.com/página (acentos são aceitos)
- https://exemplo.com/path?param=value&other=123

❌ INVÁLIDO:
- http://exemplo com espaços (espaços sem codificação)
- http://exemplo..com (ponto duplo inválido)
```

**Justificativa:** Garantir que URLs sejam acessíveis e não causem erros.

---

### RN-URL-004: URLs Duplicadas
**Descrição:** Mesma URL original pode gerar múltiplos códigos curtos.

**Regra:**
- Sistema NÃO verifica se URL original já existe
- Cada requisição gera um novo código, mesmo para URL duplicada
- Usuário pode criar múltiplas URLs curtas para mesmo destino

**Exemplos:**
```
Requisição 1: https://exemplo.com → a3k92
Requisição 2: https://exemplo.com → b7x45 (novo código)
```

**Justificativa:** 
- Simplicidade de implementação
- Permite usuários criarem versões diferentes para rastreamento
- Evita complexidade de gerenciamento de duplicatas

---

## 2. Regras de Códigos

### RN-CODE-001: Geração Automática
**Descrição:** Sistema gera códigos únicos automaticamente.

**Regra:**
- Código gerado tem exatamente 5 caracteres
- Usa caracteres alfanuméricos: a-z, A-Z, 0-9 (62 opções)
- Case-sensitive (A ≠ a)
- Total de combinações possíveis: 62^5 = 916.132.832

**Formato:**
```
Padrão: [a-zA-Z0-9]{5}
Exemplos válidos: a3k92, B7X45, z9M4p
```

**Algoritmo:**
1. Gerar 5 caracteres aleatórios de [a-zA-Z0-9]
2. Verificar se código já existe
3. Se existe, gerar novo código (máximo 10 tentativas)
4. Se após 10 tentativas não encontrar código único, retornar erro

**Justificativa:** 
- 5 caracteres = URLs curtas o suficiente
- 916M combinações = suficiente para aplicação
- Alfanumérico = fácil de digitar e compartilhar

---

### RN-CODE-002: Código Personalizado
**Descrição:** Usuário pode definir código customizado opcional.

**Regra:**
- Comprimento: 3 a 20 caracteres
- Caracteres permitidos: a-z, A-Z, 0-9, hífen (-)
- Case-sensitive
- Deve ser único no sistema
- Não pode começar ou terminar com hífen
- Não pode ter hífens consecutivos

**Formato:**
```
Padrão: ^[a-zA-Z0-9]([a-zA-Z0-9-]{1,18}[a-zA-Z0-9])?$

✅ VÁLIDOS:
- promo-verao
- EVENTO2024
- meu-link
- a1b

❌ INVÁLIDOS:
- ab (muito curto, < 3 caracteres)
- -inicio (começa com hífen)
- final- (termina com hífen)
- muito--hifens (hífens consecutivos)
- código_especial (underscore não permitido)
- link!2024 (caracteres especiais)
```

**Justificativa:**
- 3 caracteres mínimo = mantém URLs curtas mas legíveis
- 20 caracteres máximo = evita URLs muito longas
- Apenas alfanumérico e hífen = compatibilidade universal
- Hífen permite separar palavras de forma legível

---

### RN-CODE-003: Códigos Reservados
**Descrição:** Códigos específicos não podem ser usados por usuários.

**Regra:**
- Lista de códigos reservados pelo sistema:
  - `api` (usado para rotas da API)
  - `admin` (futuro painel administrativo)
  - `dashboard` (futuro dashboard)
  - `health` (health check do sistema)
  - `status` (status do sistema)
  - `docs` (documentação)
  - `static` (arquivos estáticos)
  - `assets` (recursos)

**Validação:**
- Verificar se código (em lowercase) está na lista reservada
- Rejeitar tanto automático quanto personalizado

**Exemplos:**
```
❌ Código "api" → Erro: "Código reservado pelo sistema"
❌ Código "API" → Erro: "Código reservado pelo sistema"
❌ Código "admin" → Erro: "Código reservado pelo sistema"
```

**Justificativa:** Evitar conflitos com rotas do sistema.

---

### RN-CODE-004: Unicidade de Código
**Descrição:** Cada código deve ser único no sistema.

**Regra:**
- Não podem existir dois códigos iguais (case-sensitive)
- Verificação deve ocorrer antes de salvar
- Em caso de duplicata, retornar erro 400

**Comportamento:**
```
Código existente: "promo"

Tentativa 1: criar código "promo"
Resultado: ❌ Erro 400 "Código já existe"

Tentativa 2: criar código "PROMO"
Resultado: ✅ Sucesso (case diferente)

Tentativa 3: criar código "promo2"
Resultado: ✅ Sucesso (código diferente)
```

**Justificativa:** Garantir que cada URL curta seja única e funcional.

---

## 3. Regras de Redirecionamento

### RN-REDIRECT-001: Status HTTP 302
**Descrição:** Redirecionamento usa HTTP 302 Found.

**Regra:**
- Usar status code 302 (Found - Redirecionamento temporário)
- NÃO usar 301 (Moved Permanently)
- Header Location deve conter URL original completa

**Formato da Response:**
```http
HTTP/1.1 302 Found
Location: https://www.exemplo.com/pagina-original
```

**Justificativa:**
- 302 permite mudar destino futuramente
- 301 seria cacheado permanentemente pelos browsers
- Mantém flexibilidade do sistema

---

### RN-REDIRECT-002: Contador de Cliques
**Descrição:** Cada acesso incrementa contador.

**Regra:**
- Contador incrementa em 1 a cada acesso bem-sucedido
- Incremento ocorre ANTES do redirecionamento
- Contagem não distingue usuários únicos
- Contagem não distingue origem (bot, browser, etc)
- Múltiplos cliques do mesmo IP são contados

**Comportamento:**
```
Estado inicial: clicks = 0

Acesso 1: GET /a3k92 → clicks = 1 → redirect
Acesso 2: GET /a3k92 → clicks = 2 → redirect
Acesso 3: GET /a3k92 → clicks = 3 → redirect
```

**Justificativa:** 
- Simplicidade de implementação
- Métricas básicas de uso
- Não requer tracking complexo de usuários

---

### RN-REDIRECT-003: URL Não Encontrada
**Descrição:** Código inexistente retorna 404.

**Regra:**
- Se código não existe no banco, retornar 404 Not Found
- Exibir página de erro amigável
- NÃO redirecionar para página inicial
- Log do erro para análise

**Comportamento:**
```
GET /codigoInexistente
→ 404 Not Found
→ Página: "URL não encontrada"
```

**Justificativa:** Feedback claro ao usuário de erro.

---

### RN-REDIRECT-004: Performance
**Descrição:** Redirecionamento deve ser rápido.

**Regra:**
- Tempo máximo de resposta: 100ms
- Busca no banco deve ser otimizada (índice por código)
- Incremento de contador não deve bloquear redirect

**Métricas alvo:**
```
p50: < 50ms
p95: < 100ms
p99: < 150ms
```

**Justificativa:** Experiência do usuário depende de velocidade.

---

## 4. Regras de Armazenamento

### RN-STORAGE-001: Formato JSON
**Descrição:** Dados armazenados em arquivo JSON.

**Regra:**
- Arquivo: `data/urls.json`
- Formato: Array de objetos
- Encoding: UTF-8
- Pretty print com 2 espaços de indentação

**Estrutura:**
```json
[
  {
    "id": "a3k92",
    "code": "a3k92",
    "originalUrl": "https://www.exemplo.com/artigo",
    "shortUrl": "http://localhost:3000/a3k92",
    "clicks": 42,
    "createdAt": "2024-01-15T10:30:00.000Z"
  }
]
```

**Justificativa:** 
- Simplicidade para MVP
- Fácil inspeção e debug
- Não requer instalação de banco de dados

---

### RN-STORAGE-002: Limite de URLs
**Descrição:** Sistema tem limite máximo de URLs.

**Regra:**
- Máximo: 1000 URLs por instância
- Ao atingir limite, rejeitar novas criações
- Retornar erro 400 com mensagem clara

**Comportamento:**
```
Total URLs = 999
Nova criação: ✅ Sucesso (total = 1000)

Total URLs = 1000
Nova criação: ❌ Erro 400 "Limite de URLs atingido (1000)"
```

**Justificativa:**
- Prevenir crescimento descontrolado do arquivo JSON
- Manter performance aceitável
- Limitar uso de recursos

---

### RN-STORAGE-003: Backup Automático
**Descrição:** Sistema cria backup a cada modificação.

**Regra:**
- Backup criado após cada CREATE ou DELETE
- Local: `data/backups/`
- Nome: `urls_YYYY-MM-DD_HH-MM-SS.json`
- Manter apenas últimos 10 backups
- Backups mais antigos são deletados automaticamente

**Exemplo:**
```
data/backups/
  ├── urls_2024-01-15_10-30-00.json
  ├── urls_2024-01-15_11-15-30.json
  ├── ... (até 10 arquivos)
```

**Justificativa:**
- Prevenir perda de dados
- Permitir recuperação em caso de erro
- Limitar uso de disco

---

### RN-STORAGE-004: Atomicidade
**Descrição:** Operações de escrita devem ser atômicas.

**Regra:**
- Escrever em arquivo temporário primeiro
- Depois renomear para arquivo principal (atomic operation)
- Se falhar, arquivo original permanece intacto
- Usar locks para evitar race conditions

**Algoritmo:**
```
1. Ler urls.json
2. Modificar array em memória
3. Escrever em urls.tmp.json
4. Renomear urls.tmp.json → urls.json (atômico)
5. Criar backup
```

**Justificativa:** Prevenir corrupção de dados.

---

## 5. Regras de Validação

### RN-VALIDATION-001: Validação em Camadas
**Descrição:** Validação ocorre no frontend e backend.

**Regra:**
- Frontend: validação básica para UX
- Backend: validação completa e obrigatória
- Backend NÃO pode confiar em validação do frontend
- Todas as regras devem ser validadas no backend

**Justificativa:** 
- Frontend pode ser bypassado
- Segurança requer validação server-side
- Frontend melhora experiência do usuário

---

### RN-VALIDATION-002: Sanitização
**Descrição:** Inputs devem ser sanitizados.

**Regra:**
- Remover espaços em branco (trim) de URLs e códigos
- Escapar caracteres HTML em mensagens de erro
- Não permitir JavaScript em inputs
- Validar tipos de dados

**Exemplos:**
```
Input: "  https://exemplo.com  "
Output: "https://exemplo.com"

Input: "  meu-link  "
Output: "meu-link"
```

**Justificativa:** Segurança e consistência de dados.

---

## 6. Regras de Rate Limiting

### RN-RATE-001: Limite por IP
**Descrição:** Limitar requisições por endereço IP.

**Regra:**
- Limite: 100 requisições por minuto por IP
- Contar apenas requisições POST (criação)
- GET (redirecionamento) não tem limite
- Ao exceder, retornar 429 Too Many Requests

**Comportamento:**
```
IP 192.168.1.1:
Requisição 1-100: ✅ Aceitas
Requisição 101: ❌ 429 Too Many Requests
(aguardar próximo minuto)
```

**Headers de resposta:**
```http
X-RateLimit-Limit: 100
X-RateLimit-Remaining: 45
X-RateLimit-Reset: 1610717400
```

**Justificativa:**
- Prevenir abuse do sistema
- Proteger contra ataques DDoS
- Garantir fair use

---

## 7. Regras de Segurança

### RN-SECURITY-001: URLs Maliciosas
**Descrição:** Sistema NÃO valida conteúdo do destino.

**Regra:**
- Sistema não acessa URL de destino
- Sistema não verifica se URL é segura
- Sistema não bloqueia URLs maliciosas
- Responsabilidade é do usuário

**Aviso aos usuários:**
"Sistema não valida segurança das URLs. Use com responsabilidade."

**Justificativa:**
- Validação de conteúdo é complexa
- Requer recursos externos (APIs de segurança)
- Fora do escopo do MVP

---

### RN-SECURITY-002: CORS
**Descrição:** Configuração de CORS permissiva para desenvolvimento.

**Regra:**
- Development: `Access-Control-Allow-Origin: *`
- Production: Configurar origin específico
- Permitir métodos: GET, POST, DELETE, OPTIONS
- Permitir headers: Content-Type

**Justificativa:** Facilitar desenvolvimento e integração.

---

## 8. Regras de Performance

### RN-PERF-001: Tempos de Resposta
**Descrição:** Metas de performance para operações.

**Regras:**
- POST /api/urls (criar): < 200ms
- GET /:code (redirecionar): < 100ms
- GET /api/urls (listar): < 300ms
- DELETE /api/urls/:code: < 150ms

**Justificativa:** Garantir boa experiência do usuário.

---

### RN-PERF-002: Cache
**Descrição:** Estratégia de cache para reduzir I/O.

**Regra:**
- NÃO cachear redirecionamentos (sempre incrementar contador)
- Cachear lista de URLs por 30 segundos (frontend)
- Cachear estatísticas por 60 segundos (frontend)
- Cache invalidado ao criar/deletar URL

**Justificativa:** Balance entre performance e dados atualizados.

---

## 9. Regras de Dados

### RN-DATA-001: Timestamps
**Descrição:** Formato de datas no sistema.

**Regra:**
- Usar formato ISO 8601
- Timezone: UTC
- Formato: `YYYY-MM-DDTHH:mm:ss.sssZ`
- Gerado pelo servidor, não pelo cliente

**Exemplo:**
```
"createdAt": "2024-01-15T10:30:00.000Z"
```

**Justificativa:** 
- Padrão internacional
- Evita problemas de timezone
- Fácil parsing

---

### RN-DATA-002: IDs
**Descrição:** Campo ID é igual ao código.

**Regra:**
- `id` e `code` têm o mesmo valor
- Ambos são únicos
- Ambos são case-sensitive
- ID não é auto-increment

**Exemplo:**
```json
{
  "id": "a3k92",
  "code": "a3k92"
}
```

**Justificativa:** Simplicidade de implementação.

---

## 10. Regras de Interface

### RN-UI-001: Responsividade
**Descrição:** Interface deve funcionar em todos os dispositivos.

**Regra:**
- Breakpoints: Mobile (< 640px), Tablet (640-1024px), Desktop (> 1024px)
- Touch-friendly em mobile (botões > 44px)
- Sem scroll horizontal
- Testes em Chrome, Firefox, Safari, Edge

**Justificativa:** Acessibilidade e usabilidade.

---

### RN-UI-002: Feedback Visual
**Descrição:** Todas as ações têm feedback visual.

**Regra:**
- Loading state em operações assíncronas
- Toast notifications para sucesso/erro
- Confirmação antes de ações destrutivas
- Mudança visual ao copiar (botão muda para "✓ Copiado")

**Justificativa:** Melhor experiência do usuário.

---

## Resumo de Criticidade

### 🔴 Críticas (Não negociáveis)
- RN-URL-001: Protocolo obrigatório
- RN-CODE-004: Unicidade de código
- RN-REDIRECT-001: Status 302
- RN-REDIRECT-002: Incrementar contador
- RN-STORAGE-004: Atomicidade
- RN-VALIDATION-001: Validação backend

### 🟡 Importantes (Recomendadas)
- RN-URL-002: Comprimento máximo
- RN-CODE-001: Geração automática
- RN-CODE-003: Códigos reservados
- RN-STORAGE-002: Limite de URLs
- RN-RATE-001: Rate limiting
- RN-PERF-001: Tempos de resposta

### 🟢 Desejáveis (Nice to have)
- RN-STORAGE-003: Backup automático
- RN-PERF-002: Cache
- RN-UI-001: Responsividade
- RN-UI-002: Feedback visual

---

## 11. Regras do Algoritmo de Encurtamento

### RN-ALGO-001: Geração de Código Aleatório
**Descrição:** Códigos são gerados aleatoriamente usando alfabeto alfanumérico.

**Regra:**
- Alfabeto: a-z, A-Z, 0-9 (62 caracteres)
- Comprimento fixo: 5 caracteres
- Case-sensitive
- Total de combinações: 62^5 = 916.132.832

**Algoritmo:**
```
1. Selecionar 5 caracteres aleatórios do alfabeto
2. Verificar se código já existe no banco
3. Verificar se código não é reservado
4. Se disponível, retornar código
5. Se não, repetir (máximo 10 tentativas)
```

**Justificativa:** Balance entre tamanho curto e espaço suficiente de combinações.

---

### RN-ALGO-002: Estratégia de Retry
**Descrição:** Sistema tenta até 10 vezes gerar código único.

**Regra:**
- Máximo de 10 tentativas
- Se todas falharem, retornar erro 500
- Probabilidade de falha é extremamente baixa (< 0.0001%)

**Justificativa:** Prevenir loop infinito em caso de colisões.

---

### RN-ALGO-003: Complexidade de Busca
**Descrição:** Verificação de unicidade deve ser eficiente.

**Regra:**
- Busca em array JSON: O(n)
- Com cache em memória: O(1)
- Com índice em DB: O(log n)

**Recomendação:** Implementar cache em memória para performance.

---

### RN-ALGO-004: Probabilidade de Colisão
**Descrição:** Chance de gerar código duplicado.

**Regra:**
- Com 1.000 URLs: 0,0001%
- Com 10.000 URLs: 0,001%
- Com 100.000 URLs: 0,01%
- Com 1.000.000 URLs: 0,1%

**Ação:** Monitorar taxa de retry. Se > 1%, considerar aumentar tamanho do código.

---

## Referências

- [User Stories](./USER_STORIES.md)
- [Algoritmo de Encurtamento](./SHORTENING_ALGORITHM.md)
- [Documentação da API](./api/README.md)
- [RFC 3986 - URI Generic Syntax](https://www.rfc-editor.org/rfc/rfc3986)