# API Documentation - Encurtador de URL

Esta é a documentação da API REST do projeto **Encurtador de URL** desenvolvido no AI Coding Dojo.

---

## Base URL

```
http://localhost:3000/api
```

Em produção (Render):
```
https://seu-app.onrender.com/api
```

---

## Arquitetura da API

```mermaid
graph TB
    subgraph "Cliente"
        A[Browser/Frontend]
    end
    
    subgraph "API - Express.js"
        B[Rotas /api/urls]
        C[Rotas /:code]
        D[Validação]
        E[Controller]
        F[Service Layer]
    end
    
    subgraph "Armazenamento"
        G[data/urls.json]
        H[Backup]
    end
    
    A -->|POST /api/urls| B
    A -->|GET /api/urls| B
    A -->|GET /:code| C
    B --> D
    C --> D
    D --> E
    E --> F
    F --> G
    G --> H
    
    style A fill:#e1f5ff
    style B fill:#fff4e1
    style C fill:#fff4e1
    style G fill:#e8f5e9
```

---

## Endpoints

### 1. Criar URL Encurtada

Cria uma nova URL encurtada a partir de uma URL longa.

**Endpoint:** `POST /api/urls`

**Request Body:**
```json
{
  "originalUrl": "https://www.exemplo.com/artigo-muito-longo-com-parametros?id=123&category=tech",
  "customCode": "meulink"
}
```

**Campos:**
- `originalUrl` (string, obrigatório): URL original que será encurtada
- `customCode` (string, opcional): Código personalizado para a URL curta. Se não informado, será gerado automaticamente.

**Response:** `201 Created`
```json
{
  "id": "a3k92",
  "originalUrl": "https://www.exemplo.com/artigo-muito-longo-com-parametros?id=123&category=tech",
  "shortUrl": "http://localhost:3000/a3k92",
  "code": "a3k92",
  "clicks": 0,
  "createdAt": "2024-01-15T10:30:00.000Z"
}
```

**Observação:** O campo `id` é o mesmo valor do campo `code` e serve como identificador único da URL encurtada.

**Erros:**
- `400 Bad Request`: URL inválida ou código personalizado já existe
- `500 Internal Server Error`: Erro no servidor

---

### 2. Redirecionar para URL Original

Redireciona para a URL original e incrementa o contador de acessos.

**Endpoint:** `GET /:code`

**Parâmetros:**
- `code` (string): Código da URL encurtada

**Exemplo:**
```
GET /a3k92
```

**Response:** `302 Found`
- Redireciona automaticamente para a URL original
- Header `Location` contém a URL de destino

**Erros:**
- `404 Not Found`: Código não encontrado

---

### 3. Obter Detalhes de uma URL

Retorna informações detalhadas sobre uma URL encurtada.

**Endpoint:** `GET /api/urls/:code`

**Parâmetros:**
- `code` (string): Código da URL encurtada

**Exemplo:**
```
GET /api/urls/a3k92
```

**Response:** `200 OK`
```json
{
  "id": "a3k92",
  "originalUrl": "https://www.exemplo.com/artigo-muito-longo-com-parametros?id=123&category=tech",
  "shortUrl": "http://localhost:3000/a3k92",
  "code": "a3k92",
  "clicks": 42,
  "createdAt": "2024-01-15T10:30:00.000Z"
}
```

**Erros:**
- `404 Not Found`: Código não encontrado

---

### 4. Listar Todas as URLs

Lista todas as URLs encurtadas criadas, ordenadas por data de criação (mais recentes primeiro).

**Endpoint:** `GET /api/urls`

**Query Parameters:**
- `limit` (number, opcional): Número máximo de resultados (padrão: 50)
- `offset` (number, opcional): Número de registros para pular (padrão: 0)

**Exemplo:**
```
GET /api/urls?limit=10&offset=0
```

**Response:** `200 OK`
```json
{
  "total": 150,
  "limit": 10,
  "offset": 0,
  "data": [
    {
      "id": "a3k92",
      "originalUrl": "https://www.exemplo.com/artigo-longo",
      "shortUrl": "http://localhost:3000/a3k92",
      "code": "a3k92",
      "clicks": 42,
      "createdAt": "2024-01-15T10:30:00.000Z"
    },
    {
      "id": "b7x45",
      "originalUrl": "https://www.outroexemplo.com/pagina",
      "shortUrl": "http://localhost:3000/b7x45",
      "code": "b7x45",
      "clicks": 15,
      "createdAt": "2024-01-14T15:20:00.000Z"
    }
  ]
}
```

---

### 5. Deletar URL

Remove uma URL encurtada do sistema.

**Endpoint:** `DELETE /api/urls/:code`

**Parâmetros:**
- `code` (string): Código da URL encurtada

**Exemplo:**
```
DELETE /api/urls/a3k92
```

**Response:** `200 OK`
```json
{
  "message": "URL deletada com sucesso",
  "code": "a3k92"
}
```

**Erros:**
- `404 Not Found`: Código não encontrado

---

### 6. Estatísticas Gerais

Retorna estatísticas gerais do sistema.

**Endpoint:** `GET /api/stats`

**Response:** `200 OK`
```json
{
  "totalUrls": 150,
  "totalClicks": 3542,
  "topUrls": [
    {
      "code": "a3k92",
      "originalUrl": "https://www.exemplo.com/artigo-longo",
      "clicks": 342
    },
    {
      "code": "b7x45",
      "originalUrl": "https://www.outroexemplo.com/pagina",
      "clicks": 215
    }
  ]
}
```

---

## Fluxo de Criação de URL

```mermaid
sequenceDiagram
    participant C as Cliente
    participant API as API Server
    participant V as Validação
    participant S as Storage
    
    C->>API: POST /api/urls<br/>{originalUrl, customCode?}
    API->>V: Validar URL
    
    alt URL inválida
        V-->>API: Erro de validação
        API-->>C: 400 Bad Request
    else URL válida
        V->>API: OK
        API->>API: Gerar código (se não fornecido)
        API->>S: Verificar código duplicado
        
        alt Código existe
            S-->>API: Duplicado
            API-->>C: 400 Bad Request
        else Código disponível
            S-->>API: Disponível
            API->>S: Salvar URL
            S->>S: Criar backup
            S-->>API: Confirmação
            API-->>C: 201 Created<br/>{id, code, shortUrl...}
        end
    end
```

## Fluxo de Redirecionamento

```mermaid
sequenceDiagram
    participant U as Usuário
    participant API as API Server
    participant S as Storage
    participant B as Browser
    
    U->>API: GET /a3k92
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
    end
```

---

## Regras de Negócio

### Geração de Códigos

- Códigos personalizados devem ter entre 3 e 20 caracteres
- Apenas letras, números e hífens são permitidos
- Códigos automáticos são gerados com 5 caracteres alfanuméricos

### Validação de URLs

- URLs devem começar com `http://` ou `https://`
- URLs devem ser válidas segundo o padrão RFC 3986
- URLs muito longas (> 2000 caracteres) serão rejeitadas

### Armazenamento

- Dados são armazenados em arquivo JSON local (`data/urls.json`)
- Backup automático é realizado a cada modificação

#### Estrutura do Arquivo JSON

```mermaid
graph LR
    A[urls.json] --> B{Array de URLs}
    B --> C[URL 1]
    B --> D[URL 2]
    B --> E[URL N]
    
    C --> F[id: a3k92]
    C --> G[code: a3k92]
    C --> H[originalUrl]
    C --> I[shortUrl]
    C --> J[clicks: 42]
    C --> K[createdAt]
    
    style A fill:#e8f5e9
    style B fill:#fff4e1
    style C fill:#e1f5ff
```

---

## Exemplos de Uso

### cURL

**Criar URL encurtada:**
```bash
curl -X POST http://localhost:3000/api/urls \
  -H "Content-Type: application/json" \
  -d '{
    "originalUrl": "https://www.exemplo.com/artigo-longo"
  }'
```

**Listar URLs:**
```bash
curl http://localhost:3000/api/urls
```

**Obter detalhes:**
```bash
curl http://localhost:3000/api/urls/a3k92
```

### JavaScript (Fetch API)

```javascript
// Criar URL encurtada
const response = await fetch('http://localhost:3000/api/urls', {
  method: 'POST',
  headers: {
    'Content-Type': 'application/json'
  },
  body: JSON.stringify({
    originalUrl: 'https://www.exemplo.com/artigo-longo',
    customCode: 'meulink'
  })
});

const data = await response.json();
console.log('URL curta criada:', data.shortUrl);
```

---

## Mapa de Endpoints

```mermaid
graph TD
    A[API Base /api] --> B[POST /urls<br/>Criar URL]
    A --> C[GET /urls<br/>Listar todas]
    A --> D[GET /urls/:code<br/>Detalhes]
    A --> E[DELETE /urls/:code<br/>Deletar]
    A --> F[GET /stats<br/>Estatísticas]
    
    G[Raiz /] --> H[GET /:code<br/>Redirecionar]
    
    B --> B1[201 Created]
    B --> B2[400 Bad Request]
    
    C --> C1[200 OK]
    C --> C2[Query: limit, offset]
    
    D --> D1[200 OK]
    D --> D2[404 Not Found]
    
    E --> E1[200 OK]
    E --> E2[404 Not Found]
    
    F --> F1[200 OK]
    
    H --> H1[302 Found]
    H --> H2[404 Not Found]
    
    style A fill:#e1f5ff
    style G fill:#e1f5ff
    style B fill:#c8e6c9
    style C fill:#fff9c4
    style D fill:#fff9c4
    style E fill:#ffccbc
    style F fill:#fff9c4
    style H fill:#d1c4e9
```

---

## Códigos de Status HTTP

| Código | Descrição |
|--------|-----------|
| 200 | Sucesso |
| 201 | Recurso criado com sucesso |
| 302 | Redirecionamento |
| 400 | Requisição inválida |
| 404 | Recurso não encontrado |
| 500 | Erro interno do servidor |

---

## Headers

### Request Headers

```
Content-Type: application/json
```

### Response Headers

```
Content-Type: application/json
Access-Control-Allow-Origin: *
```

---

## Ciclo de Vida de uma URL

```mermaid
stateDiagram-v2
    [*] --> Criada: POST /api/urls
    
    Criada --> Ativa: URL salva
    
    Ativa --> Acessada: GET /:code
    Acessada --> Ativa: clicks++
    
    Ativa --> Visualizada: GET /api/urls/:code
    Visualizada --> Ativa: Retornar dados
    
    Ativa --> Deletada: DELETE /api/urls/:code
    Deletada --> [*]
    
    note right of Ativa
        Estado principal
        Pode ser acessada
        múltiplas vezes
    end note
    
    note right of Acessada
        Incrementa contador
        Redireciona usuário
    end note
```

---

## Limitações

- Máximo de 1000 URLs por instância (limite do armazenamento JSON)
- Rate limit: 100 requisições por minuto por IP
- Tamanho máximo do payload: 1MB

---

## Rate Limiting

```mermaid
graph LR
    A[Requisição] --> B{Verificar IP}
    B --> C{Contador < 100/min?}
    
    C -->|Sim| D[Processar Request]
    C -->|Não| E[429 Too Many Requests]
    
    D --> F[Incrementar Contador]
    F --> G[Responder]
    
    E --> H[Aguardar próximo minuto]
    
    style A fill:#e1f5ff
    style D fill:#c8e6c9
    style E fill:#ffccbc
    style G fill:#c8e6c9
```

---

## Próximas Versões

Funcionalidades planejadas:

- [ ] Autenticação de usuários
- [ ] Expiração de URLs
- [ ] Analytics detalhados (geolocalização, device, etc)
- [ ] QR Code para URLs
- [ ] Histórico de acessos
- [ ] Proteção por senha
- [ ] API Keys para integração

---

## Suporte

Para dúvidas ou problemas, consulte o [README principal](../../README.md) do projeto ou abra uma issue no repositório.