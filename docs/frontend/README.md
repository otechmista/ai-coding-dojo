# Frontend Documentation - Encurtador de URL

Esta é a documentação do frontend do projeto **Encurtador de URL** desenvolvido no AI Coding Dojo.

---

## Tecnologias

- **HTML5**: Estrutura semântica
- **JavaScript (Vanilla)**: Interatividade sem frameworks
- **Tailwind CSS**: Estilização responsiva
- **Fetch API**: Comunicação com backend

---

## Estrutura de Arquivos

```
frontend/
├── index.html              # Página principal
├── assets/
│   ├── css/
│   │   └── styles.css      # Estilos customizados
│   ├── js/
│   │   ├── app.js          # Lógica principal
│   │   ├── api.js          # Comunicação com API
│   │   └── components.js   # Componentes reutilizáveis
│   └── images/
│       └── logo.svg        # Logo da aplicação
└── README.md
```

### Diagrama de Arquitetura

```mermaid
graph TB
    subgraph "Frontend"
        A[index.html]
        B[app.js]
        C[api.js]
        D[components.js]
        E[styles.css]
    end
    
    subgraph "Componentes"
        F[Header]
        G[URL Form]
        H[Result Card]
        I[URL List]
        J[Modal]
        K[Toast]
    end
    
    subgraph "Backend API"
        L[Express Server]
        M[Endpoints REST]
    end
    
    A --> B
    B --> C
    B --> D
    D --> F
    D --> G
    D --> H
    D --> I
    D --> J
    D --> K
    C --> M
    M --> L
    
    style A fill:#e1f5ff
    style C fill:#fff4e1
    style L fill:#c8e6c9
```

---

## Páginas

### 1. Página Principal (index.html)

**URL:** `/`

**Descrição:**
Página única (SPA simplificada) que contém todas as funcionalidades da aplicação.

**Seções:**
- Header com logo e título
- Formulário de criação de URL
- Lista de URLs criadas
- Rodapé com informações

**Layout Wireframe:**

```mermaid
graph TB
    subgraph "Página Principal - Wireframe"
        A[Header Component]
        B[URL Form Component]
        C[Result Card Component]
        D[URL List Component]
        E[Footer Component]
    end
    
    A --> |Logo + Estatísticas| A1[🔗 Encurtador de URL | 150 URLs criadas]
    
    B --> |Campos de Input| B1[Input: URL Original<br/>Input: Código Customizado opcional<br/>Button: Encurtar URL]
    
    C --> |Exibido após criar| C1[✓ URL encurtada com sucesso!<br/>📋 http://localhost:3000/a3k92<br/>Buttons: Copiar, QR Code]
    
    D --> |Lista de URLs| D1[URL Item 1: a3k92<br/>URL Item 2: b7x45<br/>URL Item N...]
    
    D1 --> |Cada item contém| D2[Code Badge<br/>Original URL Link<br/>👁 Cliques + 📅 Data<br/>Buttons: Copiar, Detalhes, Deletar]
    
    E --> |Rodapé| E1[AI Coding Dojo © 2024]
    
    style A fill:#e1f5ff
    style B fill:#c8e6c9
    style C fill:#fff9c4
    style D fill:#ffccbc
    style E fill:#d1c4e9
```

**Layout Visual ASCII:**
```
┌─────────────────────────────────────────┐
│           HEADER                        │
│  [Logo] Encurtador de URL               │
└─────────────────────────────────────────┘
┌─────────────────────────────────────────┐
│     FORMULÁRIO DE CRIAÇÃO               │
│  ┌─────────────────────────────────┐   │
│  │ Cole sua URL aqui...            │   │
│  └─────────────────────────────────┘   │
│  ┌─────────────────────────────────┐   │
│  │ Código personalizado (opcional) │   │
│  └─────────────────────────────────┘   │
│  [Encurtar URL]                         │
└─────────────────────────────────────────┘
┌─────────────────────────────────────────┐
│     RESULTADO (quando criado)           │
│  ✓ URL encurtada com sucesso!           │
│  📋 http://localhost:3000/a3k92         │
│  [Copiar] [Visualizar QR Code]          │
└─────────────────────────────────────────┘
┌─────────────────────────────────────────┐
│     LISTA DE URLs                       │
│  ┌───────────────────────────────────┐ │
│  │ a3k92                             │ │
│  │ exemplo.com/artigo-longo          │ │
│  │ 👁 42 cliques | 📅 15/01/2024     │ │
│  │ [Copiar] [Detalhes] [Deletar]     │ │
│  └───────────────────────────────────┘ │
│  ┌───────────────────────────────────┐ │
│  │ b7x45                             │ │
│  │ outrosite.com/pagina              │ │
│  │ 👁 15 cliques | 📅 14/01/2024     │ │
│  │ [Copiar] [Detalhes] [Deletar]     │ │
│  └───────────────────────────────────┘ │
└─────────────────────────────────────────┘
┌─────────────────────────────────────────┐
│           FOOTER                        │
│  AI Coding Dojo © 2024                  │
└─────────────────────────────────────────┘
```

---

## Componentes

### Hierarquia de Componentes

```mermaid
graph TD
    A[index.html / App Root] --> B[Header Component]
    A --> C[URL Form Component]
    A --> D[Result Card Component]
    A --> E[URL List Component]
    A --> F[Modal Component]
    A --> G[Toast Component]
    
    E --> H[URL Item Component 1]
    E --> I[URL Item Component 2]
    E --> J[URL Item Component N]
    
    H --> K[Copy Button]
    H --> L[Details Button]
    H --> M[Delete Button]
    
    C --> N[Loading Spinner]
    E --> O[Loading Spinner]
    
    style A fill:#e1f5ff
    style B fill:#fff9c4
    style C fill:#c8e6c9
    style D fill:#d1c4e9
    style E fill:#ffccbc
    style F fill:#f8bbd0
    style G fill:#b2dfdb
```

---

### 1. Header Component

**Arquivo:** `components.js` - `createHeader()`

**Descrição:**
Cabeçalho da aplicação com logo e título.

**Propriedades:**
- Logo (imagem ou emoji)
- Título da aplicação
- Estatísticas rápidas (opcional)

**HTML:**
```html
<header class="bg-blue-600 text-white py-6 shadow-lg">
  <div class="container mx-auto px-4">
    <div class="flex items-center justify-between">
      <div class="flex items-center space-x-3">
        <span class="text-4xl">🔗</span>
        <h1 class="text-3xl font-bold">Encurtador de URL</h1>
      </div>
      <div class="text-sm">
        <span id="stats-total">0 URLs criadas</span>
      </div>
    </div>
  </div>
</header>
```

**Responsabilidades:**
- Exibir branding da aplicação
- Mostrar estatísticas gerais
- Responsivo em mobile

---

### 2. URL Form Component

**Arquivo:** `components.js` - `createUrlForm()`

**Descrição:**
Formulário para criar novas URLs encurtadas.

**Campos:**
- `originalUrl` (input text, obrigatório): URL a ser encurtada
- `customCode` (input text, opcional): Código personalizado

**Validações:**
- URL deve ser válida (http/https)
- Código personalizado: 3-20 caracteres, apenas alfanuméricos e hífens
- Feedback visual de erros

**HTML:**
```html
<div class="bg-white rounded-lg shadow-md p-6 mb-6">
  <form id="url-form">
    <div class="mb-4">
      <label class="block text-gray-700 font-semibold mb-2">
        Cole sua URL aqui
      </label>
      <input 
        type="url" 
        id="original-url" 
        placeholder="https://www.exemplo.com/artigo-muito-longo"
        class="w-full px-4 py-3 border border-gray-300 rounded-lg focus:ring-2 focus:ring-blue-500 focus:border-transparent"
        required
      />
    </div>
    
    <div class="mb-4">
      <label class="block text-gray-700 font-semibold mb-2">
        Código personalizado (opcional)
      </label>
      <input 
        type="text" 
        id="custom-code" 
        placeholder="meulink"
        pattern="[a-zA-Z0-9-]{3,20}"
        class="w-full px-4 py-3 border border-gray-300 rounded-lg focus:ring-2 focus:ring-blue-500 focus:border-transparent"
      />
      <p class="text-sm text-gray-500 mt-1">
        3-20 caracteres: letras, números e hífens
      </p>
    </div>
    
    <button 
      type="submit"
      class="w-full bg-blue-600 text-white py-3 rounded-lg font-semibold hover:bg-blue-700 transition duration-200"
    >
      🚀 Encurtar URL
    </button>
  </form>
  
  <div id="form-error" class="hidden mt-4 p-3 bg-red-100 text-red-700 rounded-lg">
    <!-- Mensagem de erro aparece aqui -->
  </div>
</div>
```

**Eventos:**
- `submit`: Envia dados para API
- Validação em tempo real nos campos
- Loading state durante requisição

---

### 3. Result Card Component

**Arquivo:** `components.js` - `createResultCard(data)`

**Descrição:**
Card que exibe a URL encurtada após criação bem-sucedida.

**Parâmetros:**
```javascript
{
  code: "a3k92",
  shortUrl: "http://localhost:3000/a3k92",
  originalUrl: "https://www.exemplo.com/artigo-longo"
}
```

**HTML:**
```html
<div class="bg-green-50 border-2 border-green-500 rounded-lg p-6 mb-6 animate-fade-in">
  <div class="flex items-center mb-3">
    <span class="text-2xl mr-2">✓</span>
    <h3 class="text-xl font-bold text-green-700">URL encurtada com sucesso!</h3>
  </div>
  
  <div class="bg-white rounded-lg p-4 mb-4">
    <p class="text-sm text-gray-600 mb-2">Sua URL curta:</p>
    <div class="flex items-center justify-between">
      <a 
        href="${shortUrl}" 
        target="_blank"
        class="text-blue-600 text-lg font-mono hover:underline"
      >
        ${shortUrl}
      </a>
      <button 
        class="copy-btn bg-blue-600 text-white px-4 py-2 rounded hover:bg-blue-700"
        data-url="${shortUrl}"
      >
        📋 Copiar
      </button>
    </div>
  </div>
  
  <div class="text-sm text-gray-600">
    <p>URL original: <span class="font-mono">${originalUrl}</span></p>
  </div>
</div>
```

**Funcionalidades:**
- Copiar URL para clipboard
- Link clicável para testar redirecionamento
- Animação de entrada
- Auto-ocultar após 10 segundos

---

### 4. URL List Component

**Arquivo:** `components.js` - `createUrlList(urls)`

**Descrição:**
Lista todas as URLs encurtadas criadas.

**Parâmetros:**
```javascript
[
  {
    code: "a3k92",
    originalUrl: "https://www.exemplo.com/artigo-longo",
    shortUrl: "http://localhost:3000/a3k92",
    clicks: 42,
    createdAt: "2024-01-15T10:30:00.000Z"
  }
]
```

**HTML:**
```html
<div class="bg-white rounded-lg shadow-md p-6">
  <h2 class="text-2xl font-bold mb-4 text-gray-800">
    📊 Suas URLs (${urls.length})
  </h2>
  
  <div id="url-list" class="space-y-4">
    <!-- URL items aqui -->
  </div>
  
  <div id="empty-state" class="text-center py-8 text-gray-500">
    <p class="text-lg">Nenhuma URL criada ainda</p>
    <p class="text-sm">Cole uma URL acima para começar!</p>
  </div>
</div>
```

**Estados:**
- Lista vazia (empty state)
- Carregando (skeleton loader)
- Lista populada

---

### 5. URL Item Component

**Arquivo:** `components.js` - `createUrlItem(url)`

**Descrição:**
Item individual na lista de URLs.

**HTML:**
```html
<div class="border border-gray-200 rounded-lg p-4 hover:shadow-md transition duration-200">
  <div class="flex items-start justify-between mb-3">
    <div class="flex-1">
      <div class="flex items-center space-x-2 mb-2">
        <span class="bg-blue-100 text-blue-800 px-3 py-1 rounded-full font-mono font-semibold">
          ${code}
        </span>
        <span class="text-xs text-gray-500">
          📅 ${formatDate(createdAt)}
        </span>
      </div>
      
      <a 
        href="${originalUrl}" 
        target="_blank"
        class="text-gray-700 hover:text-blue-600 break-all text-sm"
      >
        ${truncateUrl(originalUrl, 60)}
      </a>
    </div>
  </div>
  
  <div class="flex items-center justify-between">
    <div class="flex items-center space-x-4 text-sm text-gray-600">
      <span>👁 ${clicks} ${clicks === 1 ? 'clique' : 'cliques'}</span>
    </div>
    
    <div class="flex space-x-2">
      <button 
        class="copy-btn text-blue-600 hover:text-blue-800 px-3 py-1 rounded hover:bg-blue-50"
        data-url="${shortUrl}"
      >
        📋 Copiar
      </button>
      <button 
        class="details-btn text-green-600 hover:text-green-800 px-3 py-1 rounded hover:bg-green-50"
        data-code="${code}"
      >
        👁 Detalhes
      </button>
      <button 
        class="delete-btn text-red-600 hover:text-red-800 px-3 py-1 rounded hover:bg-red-50"
        data-code="${code}"
      >
        🗑 Deletar
      </button>
    </div>
  </div>
</div>
```

**Funcionalidades:**
- Copiar URL curta
- Ver detalhes (modal)
- Deletar URL (com confirmação)
- Animação de remoção

---

### 6. Modal Component

**Arquivo:** `components.js` - `createModal(title, content)`

**Descrição:**
Modal genérico para exibir informações detalhadas.

**HTML:**
```html
<div id="modal-overlay" class="fixed inset-0 bg-black bg-opacity-50 flex items-center justify-center z-50">
  <div class="bg-white rounded-lg shadow-xl max-w-lg w-full mx-4 animate-scale-in">
    <div class="flex items-center justify-between p-6 border-b">
      <h3 class="text-xl font-bold text-gray-800">${title}</h3>
      <button class="close-modal text-gray-400 hover:text-gray-600 text-2xl">
        ×
      </button>
    </div>
    
    <div class="p-6">
      ${content}
    </div>
    
    <div class="flex justify-end p-6 border-t">
      <button class="close-modal bg-gray-200 text-gray-700 px-4 py-2 rounded hover:bg-gray-300">
        Fechar
      </button>
    </div>
  </div>
</div>
```

**Uso:**
- Detalhes da URL
- Confirmação de deleção
- Mensagens de erro
- QR Code (futuro)

---

### 7. Toast Notification Component

**Arquivo:** `components.js` - `showToast(message, type)`

**Descrição:**
Notificação temporária para feedback de ações.

**Tipos:**
- `success`: Verde
- `error`: Vermelho
- `info`: Azul
- `warning`: Amarelo

**HTML:**
```html
<div class="fixed top-4 right-4 z-50 animate-slide-in">
  <div class="bg-${color}-500 text-white px-6 py-3 rounded-lg shadow-lg flex items-center space-x-3">
    <span class="text-xl">${icon}</span>
    <span>${message}</span>
  </div>
</div>
```

**Comportamento:**
- Auto-ocultar após 3 segundos
- Múltiplos toasts empilhados
- Animação de entrada/saída

---

### 8. Loading Spinner Component

**Arquivo:** `components.js` - `createLoader()`

**Descrição:**
Indicador de carregamento durante requisições.

**HTML:**
```html
<div class="flex items-center justify-center py-8">
  <div class="animate-spin rounded-full h-12 w-12 border-b-2 border-blue-600"></div>
</div>
```

**Uso:**
- Durante criação de URL
- Ao carregar lista
- Operações de delete

---

## Fluxos de Interação

### Fluxo 1: Criar URL Encurtada

```mermaid
sequenceDiagram
    participant U as Usuário
    participant F as Form Component
    participant API as API Service
    participant S as Server
    participant R as Result Card
    participant L as URL List
    
    U->>F: Preenche URL
    U->>F: (Opcional) Código customizado
    U->>F: Clica "Encurtar"
    
    F->>F: Validar dados
    alt Dados inválidos
        F->>U: Exibir erro no form
    else Dados válidos
        F->>F: Mostrar loading
        F->>API: createShortUrl()
        API->>S: POST /api/urls
        
        alt Sucesso
            S-->>API: 201 Created
            API-->>F: URL criada
            F->>R: Exibir Result Card
            F->>L: Atualizar lista
            F->>F: Limpar formulário
            F->>U: Toast "Sucesso!"
        else Erro
            S-->>API: 400/500 Error
            API-->>F: Erro
            F->>U: Exibir mensagem de erro
        end
    end
```

### Fluxo 2: Copiar URL

```mermaid
flowchart TD
    A[Usuário clica Copiar] --> B[Copiar para clipboard]
    B --> C{Sucesso?}
    C -->|Sim| D[Alterar botão para ✓ Copiado]
    C -->|Não| E[Mostrar erro]
    D --> F[Exibir toast Sucesso]
    F --> G[Aguardar 2 segundos]
    G --> H[Restaurar botão original]
    
    style A fill:#e1f5ff
    style D fill:#c8e6c9
    style E fill:#ffccbc
```

### Fluxo 3: Ver Detalhes

```mermaid
sequenceDiagram
    participant U as Usuário
    participant I as URL Item
    participant API as API Service
    participant M as Modal
    
    U->>I: Clica "Detalhes"
    I->>API: getUrlDetails(code)
    API->>API: GET /api/urls/:code
    
    alt Sucesso
        API-->>I: Dados da URL
        I->>M: Abrir modal com dados
        M->>U: Exibir informações
        U->>M: Clica "Fechar" ou ESC
        M->>M: Fechar modal
    else Erro
        API-->>I: Erro 404
        I->>U: Toast "URL não encontrada"
    end
```

### Fluxo 4: Deletar URL

```mermaid
flowchart TD
    A[Usuário clica Deletar] --> B[Abrir modal confirmação]
    B --> C{Confirmar?}
    C -->|Não| D[Fechar modal]
    C -->|Sim| E[Chamar API DELETE]
    E --> F{Sucesso?}
    F -->|Sim| G[Remover da lista com animação]
    F -->|Não| H[Exibir erro]
    G --> I[Atualizar contador]
    G --> J[Toast Sucesso]
    H --> K[Toast Erro]
    
    style A fill:#e1f5ff
    style G fill:#c8e6c9
    style H fill:#ffccbc
    style J fill:#c8e6c9
    style K fill:#ffccbc
```

---

## Estados da Aplicação

```mermaid
stateDiagram-v2
    [*] --> Loading: Aplicação inicia
    
    Loading --> Loaded: URLs carregadas
    Loading --> Error: Falha ao carregar
    
    Loaded --> Creating: Criar nova URL
    Creating --> Loaded: Sucesso
    Creating --> Error: Falha
    
    Loaded --> Viewing: Ver detalhes
    Viewing --> Loaded: Fechar modal
    
    Loaded --> Deleting: Deletar URL
    Deleting --> Loaded: Confirmado
    Deleting --> Loaded: Cancelado
    
    Error --> Loading: Retry
    Error --> Loaded: Recuperado
    
    note right of Loading
        isLoading: true
        urls: []
        error: null
    end note
    
    note right of Loaded
        isLoading: false
        urls: [...]
        error: null
    end note
    
    note right of Error
        isLoading: false
        urls: []
        error: "mensagem"
    end note
```

### Estado Inicial (Loading)
```javascript
{
  isLoading: true,
  urls: [],
  error: null
}
```

### Estado Carregado
```javascript
{
  isLoading: false,
  urls: [...],
  error: null
}
```

### Estado de Erro
```javascript
{
  isLoading: false,
  urls: [],
  error: "Mensagem de erro"
}
```

---

## Responsividade

### Breakpoints (Tailwind CSS)

```mermaid
graph LR
    A[📱 Mobile<br/>< 640px] --> B[📱 Tablet<br/>640px - 1024px]
    B --> C[💻 Desktop<br/>> 1024px]
    
    A --> A1[Stack vertical<br/>Botões full-width<br/>Lista simplificada]
    B --> B1[2 colunas<br/>Botões inline<br/>Mais espaço]
    C --> C1[Layout otimizado<br/>Max 1200px<br/>Todas as infos]
    
    style A fill:#ffccbc
    style B fill:#fff9c4
    style C fill:#c8e6c9
```

### Layout por Dispositivo

```mermaid
graph TB
    subgraph "Mobile < 640px"
        M1[Header<br/>full width]
        M2[Form<br/>full width<br/>vertical stack]
        M3[List<br/>single column<br/>simplified buttons]
    end
    
    subgraph "Tablet 640-1024px"
        T1[Header<br/>with stats]
        T2[Form<br/>2 columns for inputs<br/>inline buttons]
        T3[List<br/>grid 2 columns<br/>all buttons visible]
    end
    
    subgraph "Desktop > 1024px"
        D1[Header<br/>full stats bar]
        D2[Form<br/>horizontal layout<br/>all features]
        D3[List<br/>table-like view<br/>hover effects]
    end
    
    style M1 fill:#ffccbc
    style T1 fill:#fff9c4
    style D1 fill:#c8e6c9
```

- **Mobile**: < 640px
  - Stack vertical
  - Botões full-width
  - Lista simplificada

- **Tablet**: 640px - 1024px
  - Layout em 2 colunas quando possível
  - Botões em linha

- **Desktop**: > 1024px
  - Layout otimizado
  - Máximo 1200px de largura
  - Mais informações visíveis

---

## Animações

### CSS Classes Customizadas

```css
@keyframes fade-in {
  from { opacity: 0; }
  to { opacity: 1; }
}

@keyframes slide-in {
  from { 
    transform: translateX(100%);
    opacity: 0;
  }
  to { 
    transform: translateX(0);
    opacity: 1;
  }
}

@keyframes scale-in {
  from { 
    transform: scale(0.9);
    opacity: 0;
  }
  to { 
    transform: scale(1);
    opacity: 1;
  }
}

.animate-fade-in {
  animation: fade-in 0.3s ease-out;
}

.animate-slide-in {
  animation: slide-in 0.3s ease-out;
}

.animate-scale-in {
  animation: scale-in 0.2s ease-out;
}
```

---

## Funções Utilitárias

### Fluxo de Comunicação API

```mermaid
graph LR
    A[Componentes] --> B[api.js]
    B --> C[Fetch API]
    C --> D[Backend Server]
    
    B --> E[createShortUrl]
    B --> F[getAllUrls]
    B --> G[getUrlDetails]
    B --> H[deleteUrl]
    B --> I[getStats]
    
    E --> J[POST /api/urls]
    F --> K[GET /api/urls]
    G --> L[GET /api/urls/:code]
    H --> M[DELETE /api/urls/:code]
    I --> N[GET /api/stats]
    
    style A fill:#e1f5ff
    style B fill:#fff4e1
    style D fill:#c8e6c9
```

### api.js

```javascript
// Criar URL
async function createShortUrl(originalUrl, customCode = null)

// Listar URLs
async function getAllUrls(limit = 50, offset = 0)

// Obter detalhes
async function getUrlDetails(code)

// Deletar URL
async function deleteUrl(code)

// Estatísticas
async function getStats()
```

### utils.js

```javascript
// Formatar data
function formatDate(dateString)

// Truncar URL longa
function truncateUrl(url, maxLength)

// Validar URL
function isValidUrl(url)

// Copiar para clipboard
function copyToClipboard(text)

// Debounce para inputs
function debounce(func, delay)
```

---

## Acessibilidade

- Semântica HTML adequada
- Labels em todos os inputs
- Contraste de cores (WCAG AA)
- Navegação por teclado
- ARIA labels onde necessário
- Focus visível em elementos interativos

---

## Performance

### Otimizações

- Lazy loading de lista grande
- Debounce em validações
- Cache de requisições GET
- Minificação de assets
- Compressão de imagens

### Estratégia de Cache

```mermaid
graph TD
    A[Requisição GET] --> B{Cache existe?}
    B -->|Não| C[Fazer requisição API]
    C --> D[Salvar no cache]
    D --> E[Retornar dados]
    
    B -->|Sim| F{Cache expirado?}
    F -->|Sim| C
    F -->|Não| G[Retornar do cache]
    
    E --> H[Exibir para usuário]
    G --> H
    
    style A fill:#e1f5ff
    style G fill:#c8e6c9
    style C fill:#fff4e1
```

### Ciclo de Vida da Requisição

```mermaid
sequenceDiagram
    participant C as Component
    participant L as Local Cache
    participant API as API Service
    participant S as Server
    
    C->>L: Verificar cache
    
    alt Cache válido
        L-->>C: Retornar dados cacheados
    else Cache inválido/inexistente
        C->>API: Fazer requisição
        API->>S: HTTP Request
        S-->>API: Response
        API->>L: Salvar no cache
        API-->>C: Retornar dados
    end
    
    C->>C: Renderizar UI
```

---

## Próximas Funcionalidades

- [ ] QR Code para URLs
- [ ] Gráfico de cliques ao longo do tempo
- [ ] Filtros e busca na lista
- [ ] Exportar lista como CSV
- [ ] Dark mode
- [ ] PWA (Progressive Web App)
- [ ] Compartilhamento social

### Roadmap Visual

```mermaid
gantt
    title Roadmap Frontend
    dateFormat YYYY-MM-DD
    section Fase 1 - MVP
    Estrutura HTML           :done, 2024-01-01, 3d
    Componentes básicos      :done, 2024-01-04, 5d
    Integração com API       :done, 2024-01-09, 4d
    
    section Fase 2 - Melhorias
    Validações avançadas     :active, 2024-01-13, 3d
    Animações e transições   :2024-01-16, 3d
    Responsividade completa  :2024-01-19, 2d
    
    section Fase 3 - Recursos
    QR Code                  :2024-01-21, 3d
    Gráficos de analytics    :2024-01-24, 4d
    Filtros e busca          :2024-01-28, 3d
    
    section Fase 4 - Avançado
    Dark mode                :2024-01-31, 2d
    PWA                      :2024-02-02, 5d
    Compartilhamento social  :2024-02-07, 3d
```

---

## Testes

### Checklist Manual

- [ ] Criar URL com código automático
- [ ] Criar URL com código personalizado
- [ ] Validação de URL inválida
- [ ] Validação de código inválido
- [ ] Copiar URL funciona
- [ ] Modal de detalhes abre e fecha
- [ ] Deletar URL com confirmação
- [ ] Responsividade em mobile
- [ ] Carregamento inicial da lista
- [ ] Toast notifications funcionam

---

## Referências

- [Tailwind CSS Documentation](https://tailwindcss.com/docs)
- [MDN Web Docs - Fetch API](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API)
- [HTML5 Semantic Elements](https://www.w3schools.com/html/html5_semantic_elements.asp)