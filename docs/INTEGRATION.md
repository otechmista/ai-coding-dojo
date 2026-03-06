# Integração API ↔ Frontend

Este documento descreve como o **Frontend** e a **API** do projeto Encurtador de URL se integram.

---

## ✅ Status de Compatibilidade

**100% Compatível** - API e Frontend estão completamente alinhados.

---

## Visão Geral da Integração

```mermaid
graph TB
    subgraph "Frontend Layer"
        A[HTML Pages]
        B[JavaScript Components]
        C[API Client]
    end
    
    subgraph "Communication"
        D[HTTP/REST]
        E[JSON Payloads]
    end
    
    subgraph "Backend Layer"
        F[Express Routes]
        G[Controllers]
        H[Services]
        I[JSON Storage]
    end
    
    A --> B
    B --> C
    C -->|Fetch API| D
    D --> F
    F --> G
    G --> H
    H --> I
    
    I -->|Response| H
    H -->|Response| G
    G -->|JSON| D
    D -->|Parse| C
    C --> B
    B --> A
    
    style A fill:#e1f5ff
    style C fill:#fff4e1
    style F fill:#c8e6c9
    style I fill:#ffccbc
```

---

## Mapeamento de Funcionalidades

```mermaid
graph LR
    subgraph "Frontend Functions"
        F1[createShortUrl]
        F2[getAllUrls]
        F3[getUrlDetails]
        F4[deleteUrl]
        F5[getStats]
    end
    
    subgraph "API Endpoints"
        A1[POST /api/urls]
        A2[GET /api/urls]
        A3[GET /api/urls/:code]
        A4[DELETE /api/urls/:code]
        A5[GET /api/stats]
    end
    
    subgraph "UI Components"
        U1[URL Form]
        U2[URL List]
        U3[Modal Details]
        U4[Delete Button]
        U5[Header Stats]
    end
    
    U1 --> F1
    U2 --> F2
    U3 --> F3
    U4 --> F4
    U5 --> F5
    
    F1 --> A1
    F2 --> A2
    F3 --> A3
    F4 --> A4
    F5 --> A5
    
    style F1 fill:#c8e6c9
    style A1 fill:#fff4e1
    style U1 fill:#e1f5ff
```

---

### 1. Criar URL Encurtada

**Frontend:**
```javascript
// Função: createShortUrl(originalUrl, customCode = null)
const response = await fetch('http://localhost:3000/api/urls', {
  method: 'POST',
  headers: { 'Content-Type': 'application/json' },
  body: JSON.stringify({
    originalUrl: 'https://www.exemplo.com/artigo-longo',
    customCode: 'meulink' // opcional
  })
});
```

**API:**
- **Endpoint:** `POST /api/urls`
- **Request Body:** `{ originalUrl: string, customCode?: string }`
- **Response:** `201 Created`

```json
{
  "id": "a3k92",
  "code": "a3k92",
  "originalUrl": "https://www.exemplo.com/artigo-longo",
  "shortUrl": "http://localhost:3000/a3k92",
  "clicks": 0,
  "createdAt": "2024-01-15T10:30:00.000Z"
}
```

**Componente:** `URL Form Component` → `Result Card Component`

---

### 2. Listar URLs

**Frontend:**
```javascript
// Função: getAllUrls(limit = 50, offset = 0)
const response = await fetch('http://localhost:3000/api/urls?limit=10&offset=0');
const data = await response.json();
```

**API:**
- **Endpoint:** `GET /api/urls`
- **Query Params:** `limit`, `offset`
- **Response:** `200 OK`

```json
{
  "total": 150,
  "limit": 10,
  "offset": 0,
  "data": [
    {
      "id": "a3k92",
      "code": "a3k92",
      "originalUrl": "https://www.exemplo.com/artigo-longo",
      "shortUrl": "http://localhost:3000/a3k92",
      "clicks": 42,
      "createdAt": "2024-01-15T10:30:00.000Z"
    }
  ]
}
```

**Componente:** `URL List Component` → `URL Item Component` (para cada item)

---

### 3. Obter Detalhes de URL

**Frontend:**
```javascript
// Função: getUrlDetails(code)
const response = await fetch(`http://localhost:3000/api/urls/${code}`);
const data = await response.json();
```

**API:**
- **Endpoint:** `GET /api/urls/:code`
- **Response:** `200 OK`

```json
{
  "id": "a3k92",
  "code": "a3k92",
  "originalUrl": "https://www.exemplo.com/artigo-longo",
  "shortUrl": "http://localhost:3000/a3k92",
  "clicks": 42,
  "createdAt": "2024-01-15T10:30:00.000Z"
}
```

**Componente:** `Modal Component` (exibe detalhes)

---

### 4. Deletar URL

**Frontend:**
```javascript
// Função: deleteUrl(code)
const response = await fetch(`http://localhost:3000/api/urls/${code}`, {
  method: 'DELETE'
});
```

**API:**
- **Endpoint:** `DELETE /api/urls/:code`
- **Response:** `200 OK`

```json
{
  "message": "URL deletada com sucesso",
  "code": "a3k92"
}
```

**Componente:** `URL Item Component` → Botão Deletar → Confirmação

---

### 5. Estatísticas

**Frontend:**
```javascript
// Função: getStats()
const response = await fetch('http://localhost:3000/api/stats');
const data = await response.json();
```

**API:**
- **Endpoint:** `GET /api/stats`
- **Response:** `200 OK`

```json
{
  "totalUrls": 150,
  "totalClicks": 3542,
  "topUrls": [
    {
      "code": "a3k92",
      "originalUrl": "https://www.exemplo.com/artigo-longo",
      "clicks": 342
    }
  ]
}
```

**Componente:** `Header Component` (exibe estatísticas)

---

### 6. Redirecionar

**Frontend:**
```javascript
// Link direto na lista
<a href="${shortUrl}" target="_blank">${shortUrl}</a>
```

**API:**
- **Endpoint:** `GET /:code`
- **Response:** `302 Found` (redirect para URL original)
- **Side Effect:** Incrementa contador de cliques

**Componente:** `URL Item Component` → Link clicável

---

## Estrutura de Dados Compartilhada

### URL Object

Tanto API quanto Frontend usam a mesma estrutura:

```typescript
interface UrlObject {
  id: string;           // Mesmo valor que 'code' - identificador único
  code: string;         // Código da URL encurtada (ex: "a3k92")
  originalUrl: string;  // URL original completa
  shortUrl: string;     // URL curta completa (ex: "http://localhost:3000/a3k92")
  clicks: number;       // Contador de acessos
  createdAt: string;    // ISO 8601 timestamp
}
```

---

## Fluxo Completo: Criar URL

```mermaid
sequenceDiagram
    participant F as Frontend
    participant API as API Server
    participant S as Storage
    
    F->>API: POST /api/urls<br/>{originalUrl, customCode?}
    
    API->>API: Validar URL
    API->>API: Gerar código (se necessário)
    
    API->>S: Salvar URL
    S->>S: Criar backup
    S-->>API: Confirmação
    
    API-->>F: 201 Created<br/>{id, code, shortUrl...}
    
    F->>F: Exibir Result Card
    F->>F: Atualizar lista
    
    Note over F,S: URL criada com sucesso
```

---

## Fluxo Completo: Redirecionar

```mermaid
sequenceDiagram
    participant U as Usuário
    participant API as API Server
    participant S as Storage
    participant B as Browser
    
    U->>API: GET /a3k92 (clicar no link)
    
    API->>S: Buscar código "a3k92"
    
    alt Código não encontrado
        S-->>API: Não existe
        API-->>U: 404 Not Found
    else Código encontrado
        S-->>API: URL original encontrada
        API->>S: Incrementar contador de cliques
        S-->>API: Contador atualizado
        API-->>B: 302 Found<br/>Location: URL original
        B->>B: Redirecionar usuário
        Note over U,B: Usuário navegado para URL original
    end
```

---

## Tratamento de Erros

### Fluxo de Tratamento de Erros

```mermaid
flowchart TD
    A[Requisição Frontend] --> B{Response Status}
    
    B -->|200/201| C[Sucesso]
    B -->|400| D[Erro de Validação]
    B -->|404| E[Não Encontrado]
    B -->|500| F[Erro Servidor]
    B -->|Timeout| G[Erro de Rede]
    
    C --> H[Processar dados]
    D --> I[Exibir erro no formulário]
    E --> J[Mostrar toast de erro]
    F --> K[Mensagem genérica]
    G --> L[Retry automático]
    
    H --> M[Atualizar UI]
    I --> M
    J --> M
    K --> M
    L --> N{Retry count < 3?}
    N -->|Sim| A
    N -->|Não| K
    
    style C fill:#c8e6c9
    style D fill:#ffccbc
    style E fill:#ffccbc
    style F fill:#ffccbc
    style G fill:#ffccbc
```

### Frontend → API

| Erro API | Status | Ação Frontend |
|----------|--------|---------------|
| URL inválida | 400 | Exibir erro no formulário |
| Código duplicado | 400 | Sugerir outro código |
| URL não encontrada | 404 | Exibir toast de erro |
| Erro servidor | 500 | Exibir mensagem genérica |
| Timeout | - | Retry automático |

### Implementação no Frontend

```javascript
async function createShortUrl(originalUrl, customCode = null) {
  try {
    const response = await fetch('http://localhost:3000/api/urls', {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify({ originalUrl, customCode })
    });

    if (!response.ok) {
      const error = await response.json();
      
      if (response.status === 400) {
        // Validação - exibir no formulário
        showFormError(error.message);
      } else if (response.status === 404) {
        // Não encontrado - toast
        showToast('Recurso não encontrado', 'error');
      } else {
        // Erro genérico
        showToast('Erro ao criar URL. Tente novamente.', 'error');
      }
      
      return null;
    }

    const data = await response.json();
    return data;
    
  } catch (error) {
    // Erro de rede ou timeout
    showToast('Erro de conexão. Verifique sua internet.', 'error');
    return null;
  }
}
```

---

## Validações Sincronizadas

### Diagrama de Validação

```mermaid
graph TD
    A[Input do Usuário] --> B{Validação Frontend}
    
    B -->|Falha| C[Exibir erro inline]
    B -->|Sucesso| D[Enviar para API]
    
    D --> E{Validação Backend}
    
    E -->|Falha| F[Retornar 400]
    E -->|Sucesso| G[Processar dados]
    
    F --> H[Frontend exibe erro]
    G --> I[Retornar 201]
    I --> J[Frontend exibe sucesso]
    
    style B fill:#fff9c4
    style E fill:#fff9c4
    style G fill:#c8e6c9
    style F fill:#ffccbc
```

### Código Personalizado

**Frontend (JavaScript):**
```javascript
function isValidCustomCode(code) {
  const pattern = /^[a-zA-Z0-9-]{3,20}$/;
  return pattern.test(code);
}
```

**API (Backend):**
- Regex: `^[a-zA-Z0-9-]{3,20}$`
- Mesma validação

### URL Original

**Frontend (JavaScript):**
```javascript
function isValidUrl(url) {
  try {
    const parsed = new URL(url);
    return parsed.protocol === 'http:' || parsed.protocol === 'https:';
  } catch {
    return false;
  }
}
```

**API (Backend):**
- RFC 3986 compliant
- Deve começar com `http://` ou `https://`
- Máximo 2000 caracteres

---

## CORS Configuration

### Fluxo CORS

```mermaid
sequenceDiagram
    participant B as Browser
    participant API as API Server
    
    Note over B,API: Preflight Request (OPTIONS)
    B->>API: OPTIONS /api/urls<br/>Origin: http://localhost:3001
    API->>API: Verificar CORS headers
    API-->>B: 200 OK<br/>Access-Control-Allow-Origin: *<br/>Access-Control-Allow-Methods: GET,POST,DELETE
    
    Note over B,API: Requisição Real
    B->>API: POST /api/urls<br/>Origin: http://localhost:3001
    API->>API: Processar requisição
    API-->>B: 201 Created<br/>Access-Control-Allow-Origin: *
    
    Note over B: CORS permite a resposta
```

A API deve permitir requisições do frontend:

```javascript
// Backend - Express
app.use((req, res, next) => {
  res.header('Access-Control-Allow-Origin', '*');
  res.header('Access-Control-Allow-Methods', 'GET, POST, DELETE, OPTIONS');
  res.header('Access-Control-Allow-Headers', 'Content-Type');
  next();
});
```

---

## Configuração de Ambiente

### Diagrama de Ambientes

```mermaid
graph TB
    subgraph "Desenvolvimento"
        A[Frontend:3001] --> B[API:3000]
        B --> C[data/urls.json]
    end
    
    subgraph "Produção - Render"
        D[Frontend] --> E[API]
        E --> F[data/urls.json]
    end
    
    G[Variáveis de Ambiente] --> A
    G --> D
    
    H[.env] --> G
    
    style A fill:#e1f5ff
    style B fill:#fff4e1
    style D fill:#c8e6c9
    style E fill:#a5d6a7
```

### Frontend

```javascript
// config.js
const API_BASE_URL = process.env.API_URL || 'http://localhost:3000';

export const API_ENDPOINTS = {
  createUrl: `${API_BASE_URL}/api/urls`,
  listUrls: `${API_BASE_URL}/api/urls`,
  getUrl: (code) => `${API_BASE_URL}/api/urls/${code}`,
  deleteUrl: (code) => `${API_BASE_URL}/api/urls/${code}`,
  stats: `${API_BASE_URL}/api/stats`,
  redirect: (code) => `${API_BASE_URL}/${code}`
};
```

### Produção (Render)

```bash
# Variável de ambiente
API_URL=https://seu-app.onrender.com
```

---

## Performance

### Arquitetura de Cache

```mermaid
graph LR
    A[Componente] --> B{Cache Local}
    
    B -->|Hit + Válido| C[Retornar Cached]
    B -->|Miss ou Expirado| D[API Request]
    
    D --> E[Receber Dados]
    E --> F[Salvar no Cache]
    F --> G[Retornar Dados]
    
    C --> H[Renderizar UI]
    G --> H
    
    I[Timer] -->|30s - URLs| J[Invalidar Cache URLs]
    I -->|60s - Stats| K[Invalidar Cache Stats]
    
    style C fill:#c8e6c9
    style D fill:#fff4e1
    style H fill:#e1f5ff
```

### Cache Strategy

1. **Lista de URLs**: Cache de 30 segundos
2. **Estatísticas**: Cache de 1 minuto
3. **Detalhes**: Sem cache (sempre fresh)

```javascript
const cache = new Map();

async function getAllUrlsWithCache(limit = 50, offset = 0) {
  const cacheKey = `urls_${limit}_${offset}`;
  const cached = cache.get(cacheKey);
  
  if (cached && Date.now() - cached.timestamp < 30000) {
    return cached.data;
  }
  
  const data = await getAllUrls(limit, offset);
  cache.set(cacheKey, { data, timestamp: Date.now() });
  
  return data;
}
```

---

## Testes de Integração

### Fluxo de Testes

```mermaid
flowchart TD
    A[Iniciar Testes] --> B[Teste: Criar URL]
    B --> C[Teste: Listar URLs]
    C --> D[Teste: Ver Detalhes]
    D --> E[Teste: Copiar URL]
    E --> F[Teste: Deletar URL]
    F --> G[Teste: Redirecionar]
    G --> H[Teste: Estatísticas]
    H --> I[Teste: Validações]
    I --> J[Teste: Erros]
    J --> K[Teste: CORS]
    K --> L{Todos Passaram?}
    
    L -->|Sim| M[✓ Integração OK]
    L -->|Não| N[✗ Corrigir Falhas]
    N --> B
    
    style M fill:#c8e6c9
    style N fill:#ffccbc
```

### Checklist

- [ ] Frontend consegue criar URL via API
- [ ] Lista de URLs é carregada corretamente
- [ ] Paginação funciona (limit/offset)
- [ ] Detalhes são exibidos corretamente
- [ ] Deletar remove do frontend e backend
- [ ] Erros da API são tratados no frontend
- [ ] Redirecionamento incrementa contador
- [ ] Estatísticas são atualizadas
- [ ] CORS está configurado corretamente
- [ ] Validações são consistentes

---

## Próximos Passos

```mermaid
gantt
    title Plano de Implementação
    dateFormat YYYY-MM-DD
    section Backend
    Configurar Express          :2024-01-15, 2d
    Implementar rotas           :2024-01-17, 3d
    Sistema de storage JSON     :2024-01-20, 2d
    Validações e erros          :2024-01-22, 2d
    
    section Frontend
    Estrutura HTML              :2024-01-17, 2d
    Componentes UI              :2024-01-19, 4d
    Integração com API          :2024-01-23, 3d
    
    section Testes
    Testes locais               :2024-01-26, 2d
    
    section Deploy
    Configurar Render           :2024-01-28, 1d
    Deploy e testes produção    :2024-01-29, 2d
```

### Etapas

1. Implementar API backend seguindo a documentação
2. Implementar frontend seguindo a documentação
3. Testar integração localmente
4. Configurar variáveis de ambiente
5. Deploy no Render
6. Testes end-to-end em produção

---

## Matriz de Compatibilidade

```mermaid
graph TB
    subgraph "Request Structure"
        R1[Frontend Request]
        R2[API Expected]
        R3[✓ Match]
    end
    
    subgraph "Response Structure"
        S1[API Response]
        S2[Frontend Expected]
        S3[✓ Match]
    end
    
    subgraph "Data Types"
        D1[Frontend Types]
        D2[API Types]
        D3[✓ Match]
    end
    
    subgraph "Error Handling"
        E1[Frontend Errors]
        E2[API Errors]
        E3[✓ Match]
    end
    
    R1 --> R2
    R2 --> R3
    
    S1 --> S2
    S2 --> S3
    
    D1 --> D2
    D2 --> D3
    
    E1 --> E2
    E2 --> E3
    
    style R3 fill:#c8e6c9
    style S3 fill:#c8e6c9
    style D3 fill:#c8e6c9
    style E3 fill:#c8e6c9
```

### Checklist de Compatibilidade

| Feature | Frontend | Backend | Status |
|---------|----------|---------|--------|
| Criar URL | createShortUrl() | POST /api/urls | ✅ |
| Listar URLs | getAllUrls() | GET /api/urls | ✅ |
| Ver Detalhes | getUrlDetails() | GET /api/urls/:code | ✅ |
| Deletar | deleteUrl() | DELETE /api/urls/:code | ✅ |
| Estatísticas | getStats() | GET /api/stats | ✅ |
| Redirecionar | Link direto | GET /:code | ✅ |
| Paginação | limit/offset params | Query params | ✅ |
| Validações | Regex client-side | Regex server-side | ✅ |
| Códigos HTTP | Tratamento de erros | Status codes | ✅ |
| CORS | Fetch com credenciais | Headers configurados | ✅ |

---

## Diagrama de Integração Completo

```mermaid
sequenceDiagram
    participant User
    participant Browser
    participant Frontend
    participant APIClient
    participant Backend
    participant Storage
    
    Note over User,Storage: Fluxo Completo de Integração
    
    User->>Browser: Acessa app
    Browser->>Frontend: Carrega index.html
    Frontend->>Frontend: Inicializa componentes
    
    Frontend->>APIClient: Solicita URLs
    APIClient->>Backend: GET /api/urls
    Backend->>Storage: Read urls.json
    Storage-->>Backend: Array URLs
    Backend-->>APIClient: 200 OK + JSON
    APIClient-->>Frontend: Parse dados
    Frontend->>Browser: Renderiza lista
    
    User->>Browser: Preenche form
    Browser->>Frontend: Submit event
    Frontend->>Frontend: Valida dados
    
    Frontend->>APIClient: createShortUrl()
    APIClient->>Backend: POST /api/urls
    Backend->>Backend: Valida backend
    Backend->>Storage: Write urls.json
    Storage->>Storage: Backup
    Storage-->>Backend: OK
    Backend-->>APIClient: 201 Created
    APIClient-->>Frontend: Parse resposta
    Frontend->>Browser: Exibe sucesso
    
    User->>Browser: Clica link curto
    Browser->>Backend: GET /:code
    Backend->>Storage: Find code
    Storage-->>Backend: URL original
    Backend->>Storage: Increment clicks
    Backend-->>Browser: 302 Redirect
    Browser->>Browser: Navega para URL
```

---

## Referências

- [Documentação da API](./api/README.md)
- [Documentação do Frontend](./frontend/README.md)
- [Documentação de Arquitetura](./ARCHITECTURE.md)
- [README Principal](../README.md)