# Referência Rápida - Encurtador de URL

Guia prático e objetivo para desenvolvimento do projeto.

---

## Endpoints da API

### Criar URL
```http
POST /api/urls
Content-Type: application/json

{
  "originalUrl": "https://exemplo.com/artigo-longo",
  "customCode": "meulink"  // opcional
}
```

**Response:** 201 Created
```json
{
  "id": "a3k92",
  "code": "a3k92",
  "originalUrl": "https://exemplo.com/artigo-longo",
  "shortUrl": "http://localhost:3000/a3k92",
  "clicks": 0,
  "createdAt": "2024-01-15T10:30:00.000Z"
}
```

### Listar URLs
```http
GET /api/urls?limit=50&offset=0
```

### Ver Detalhes
```http
GET /api/urls/:code
```

### Deletar
```http
DELETE /api/urls/:code
```

### Estatísticas
```http
GET /api/stats
```

### Redirecionar
```http
GET /:code
→ 302 Found (redireciona para URL original)
```

---

## Validações

### URL Original
- ✅ Protocolo: `http://` ou `https://`
- ✅ Comprimento: 10-2000 caracteres
- ✅ Formato: RFC 3986
- ❌ Outros protocolos: ftp, file, etc

### Código Personalizado
- ✅ Comprimento: 3-20 caracteres
- ✅ Permitido: `a-z`, `A-Z`, `0-9`, `-`
- ✅ Case-sensitive
- ❌ Não pode: começar/terminar com `-`
- ❌ Não pode: hífens consecutivos `--`
- ❌ Não pode: underscore, espaços, especiais

### Código Automático
- Comprimento: exatos 5 caracteres
- Alfabeto: `a-zA-Z0-9` (62 opções)
- Combinações: 916.132.832
- Exemplo: `a3k92`, `B7x45`

---

## Códigos Reservados

Não podem ser usados:
```
api, admin, dashboard, health, status, docs, static, assets
```

---

## Status HTTP

| Código | Quando |
|--------|--------|
| 200 | Sucesso GET/DELETE |
| 201 | URL criada |
| 302 | Redirecionamento |
| 400 | URL/código inválido, duplicado |
| 404 | Código não encontrado |
| 429 | Rate limit excedido |
| 500 | Erro servidor |

---

## Estrutura de Dados

### URL Object
```json
{
  "id": "a3k92",
  "code": "a3k92",
  "originalUrl": "https://exemplo.com/pagina",
  "shortUrl": "http://localhost:3000/a3k92",
  "clicks": 42,
  "createdAt": "2024-01-15T10:30:00.000Z"
}
```

### Stats Object
```json
{
  "totalUrls": 150,
  "totalClicks": 3542,
  "topUrls": [
    {
      "code": "a3k92",
      "originalUrl": "https://exemplo.com",
      "clicks": 342
    }
  ]
}
```

---

## Regras de Negócio Principais

### URLs
- URLs duplicadas geram códigos diferentes
- Máximo 2000 caracteres
- Protocolo obrigatório
- Validação frontend + backend

### Códigos
- Automático: 5 chars aleatórios
- Customizado: 3-20 chars, alfanumérico + hífen
- Únicos e case-sensitive
- Códigos reservados bloqueados

### Redirecionamento
- Status 302 (temporário)
- Incrementa contador sempre
- Não distingue usuários únicos
- < 100ms de latência

### Armazenamento
- Arquivo JSON: `data/urls.json`
- Backup automático a cada modificação
- Máximo 1000 URLs (limite MVP)
- Manter últimos 10 backups

---

## Rate Limiting

```
100 requisições/minuto por IP
```

Aplica apenas a POST (criar URLs).
GET (redirecionamentos) sem limite.

---

## Algoritmo de Geração

```javascript
function generateShortCode() {
  const chars = 'abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ0123456789';
  const maxAttempts = 10;
  
  for (let i = 0; i < maxAttempts; i++) {
    let code = '';
    for (let j = 0; j < 5; j++) {
      code += chars[Math.floor(Math.random() * 62)];
    }
    
    if (!exists(code) && !reserved(code)) {
      return code;
    }
  }
  
  throw new Error('Não foi possível gerar código único');
}
```

---

## Componentes Frontend

### Header
- Logo + título
- Estatísticas gerais

### URL Form
- Input: URL original (obrigatório)
- Input: Código customizado (opcional)
- Button: Encurtar URL
- Validação em tempo real

### Result Card
- Exibido após criar URL
- Mostra URL curta
- Botão copiar
- Auto-ocultar após 10s

### URL List
- Lista todas URLs criadas
- Ordenado por data DESC
- Paginação: 50 itens/página

### URL Item
- Badge com código
- Link para URL original
- Contador de cliques
- Data de criação
- Botões: Copiar, Detalhes, Deletar

### Modal
- Ver detalhes da URL
- Confirmação de deleção
- Fechar com ESC ou clique fora

### Toast
- Feedback de ações
- Auto-ocultar 3s
- Tipos: success, error, info

---

## Comandos

### Desenvolvimento
```bash
# Backend
cd backend
npm install
npm start

# Frontend
cd frontend
# Abrir index.html no browser
```

### Produção
```bash
npm install
npm start
```

### Deploy Render
```bash
git add .
git commit -m "Deploy"
git push origin main
```

---

## Variáveis de Ambiente

```bash
NODE_ENV=production
PORT=3000
```

---

## Estrutura de Arquivos

```
ai-coding-dojo/
├── backend/
│   ├── src/
│   │   ├── server.js
│   │   ├── routes/
│   │   ├── controllers/
│   │   ├── services/
│   │   └── validators/
│   └── data/
│       ├── urls.json
│       └── backups/
├── frontend/
│   ├── index.html
│   └── assets/
│       ├── js/
│       ├── css/
│       └── images/
├── docs/
├── package.json
└── README.md
```

---

## Checklist de Implementação

### Backend
- [ ] Configurar Express
- [ ] Criar rotas (/api/urls, /:code)
- [ ] Implementar geração de código
- [ ] Validar URLs e códigos
- [ ] Sistema de armazenamento JSON
- [ ] Incrementar contador de cliques
- [ ] Rate limiting
- [ ] CORS

### Frontend
- [ ] Estrutura HTML
- [ ] Formulário de criação
- [ ] Lista de URLs
- [ ] Copiar para clipboard
- [ ] Modal de detalhes
- [ ] Deletar com confirmação
- [ ] Toast notifications
- [ ] Responsividade

### Deploy
- [ ] Package.json configurado
- [ ] Conectar GitHub ao Render
- [ ] Configurar variáveis de ambiente
- [ ] Testar em produção

---

## Performance

### Metas
- Criar URL: < 200ms
- Redirecionar: < 100ms
- Listar URLs: < 300ms

### Otimizações
- Cache de códigos em memória (Set/Map)
- Índice por código (se usar DB)
- Write-behind cache para backups

---

## Testes Manuais

- [ ] Criar URL sem código customizado
- [ ] Criar URL com código customizado
- [ ] Tentar código duplicado (deve falhar)
- [ ] Tentar código reservado (deve falhar)
- [ ] URL inválida (deve falhar)
- [ ] Código muito curto (deve falhar)
- [ ] Redirecionar URL curta
- [ ] Ver contador incrementar
- [ ] Copiar URL para clipboard
- [ ] Ver detalhes em modal
- [ ] Deletar URL (com confirmação)
- [ ] Listar URLs criadas
- [ ] Ver estatísticas
- [ ] Testar em mobile

---

## Erros Comuns

### "URL inválida"
→ Adicione `http://` ou `https://`

### "Código já em uso"
→ Tente outro código customizado

### "Código reservado"
→ Use código diferente de: api, admin, etc

### "Rate limit excedido"
→ Aguarde 1 minuto e tente novamente

### "404 Not Found"
→ Código não existe ou foi deletado

---

## Links Úteis

- API: http://localhost:3000/api/urls
- Frontend: http://localhost:3000/
- Docs API: `/docs/api/README.md`
- Docs Frontend: `/docs/frontend/README.md`
- User Stories: `/docs/USER_STORIES.md`
- Regras de Negócio: `/docs/BUSINESS_RULES.md`
- Algoritmo: `/docs/SHORTENING_ALGORITHM.md`

---

## Troubleshooting

**Problema:** Servidor não inicia
**Solução:** Verifique se porta 3000 está livre

**Problema:** CORS error
**Solução:** Configure headers no Express

**Problema:** Dados perdidos
**Solução:** Verifique backups em `data/backups/`

**Problema:** Performance lenta
**Solução:** Implemente cache em memória

**Problema:** Código duplicado
**Solução:** Algoritmo retry automático (máx 10x)

---

## Contatos e Suporte

- README: `/README.md`
- Issues: GitHub repository
- Documentação completa: `/docs/`
