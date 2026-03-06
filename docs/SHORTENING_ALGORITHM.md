# Algoritmo de Encurtamento de URLs

Este documento descreve o algoritmo utilizado para gerar códigos curtos no projeto **Encurtador de URL**.

---

## Visão Geral

O algoritmo gera códigos alfanuméricos únicos de 5 caracteres que servirão como identificadores das URLs encurtadas.

---

## Características do Código Gerado

**Comprimento:** 5 caracteres

**Alfabeto:** 62 caracteres
- Letras minúsculas: a-z (26 caracteres)
- Letras maiúsculas: A-Z (26 caracteres)
- Números: 0-9 (10 caracteres)

**Case-sensitive:** Sim (A ≠ a)

**Total de combinações:** 62^5 = 916.132.832 URLs únicas possíveis

**Exemplos válidos:**
```
a3k92
B7X45
zM9pQ
5Aa1B
xYz00
```

---

## Algoritmo de Geração

### Pseudocódigo

```
function generateShortCode():
    characters = 'abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ0123456789'
    codeLength = 5
    maxAttempts = 10
    
    for attempt from 1 to maxAttempts:
        code = ''
        
        for i from 1 to codeLength:
            randomIndex = random(0, 61)
            code = code + characters[randomIndex]
        
        if not existsInDatabase(code) and not isReserved(code):
            return code
    
    throw Error('Não foi possível gerar código único após 10 tentativas')
```

### Implementação JavaScript

```javascript
function generateShortCode() {
    const characters = 'abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ0123456789';
    const codeLength = 5;
    const maxAttempts = 10;
    
    for (let attempt = 0; attempt < maxAttempts; attempt++) {
        let code = '';
        
        for (let i = 0; i < codeLength; i++) {
            const randomIndex = Math.floor(Math.random() * characters.length);
            code += characters[randomIndex];
        }
        
        // Verifica se código já existe
        if (!codeExists(code) && !isReservedCode(code)) {
            return code;
        }
    }
    
    throw new Error('Não foi possível gerar código único após 10 tentativas');
}
```

---

## Validação de Código Personalizado

Para códigos fornecidos pelo usuário:

### Regras

1. Comprimento: 3 a 20 caracteres
2. Caracteres permitidos: a-z, A-Z, 0-9, hífen (-)
3. Não pode começar ou terminar com hífen
4. Não pode ter hífens consecutivos
5. Deve ser único no sistema
6. Não pode ser código reservado

### Regex de Validação

```javascript
const customCodePattern = /^[a-zA-Z0-9]([a-zA-Z0-9-]{1,18}[a-zA-Z0-9])?$/;
```

### Implementação

```javascript
function validateCustomCode(code) {
    // Verifica comprimento
    if (code.length < 3 || code.length > 20) {
        return { valid: false, error: 'Código deve ter entre 3 e 20 caracteres' };
    }
    
    // Verifica formato
    const pattern = /^[a-zA-Z0-9]([a-zA-Z0-9-]{1,18}[a-zA-Z0-9])?$/;
    if (!pattern.test(code)) {
        return { valid: false, error: 'Use apenas letras, números e hífens' };
    }
    
    // Verifica se é reservado
    if (isReservedCode(code)) {
        return { valid: false, error: 'Código reservado pelo sistema' };
    }
    
    // Verifica unicidade
    if (codeExists(code)) {
        return { valid: false, error: 'Código já em uso. Tente outro' };
    }
    
    return { valid: true };
}
```

---

## Códigos Reservados

Lista de códigos que não podem ser usados:

```javascript
const RESERVED_CODES = [
    'api',
    'admin',
    'dashboard',
    'health',
    'status',
    'docs',
    'static',
    'assets'
];

function isReservedCode(code) {
    return RESERVED_CODES.includes(code.toLowerCase());
}
```

---

## Verificação de Unicidade

### Implementação com Array (JSON)

```javascript
function codeExists(code) {
    const urls = readUrlsFromFile(); // Lê data/urls.json
    return urls.some(url => url.code === code);
}
```

### Complexidade

**Busca:** O(n) onde n = número de URLs

Para melhor performance:
- Criar índice em memória (Map/Object)
- Ou usar banco de dados com índice

---

## Probabilidade de Colisão

### Fórmula

```
P(colisão) = n / N

Onde:
n = número de URLs já criadas
N = total de combinações possíveis (62^5)
```

### Exemplos

| URLs criadas | Probabilidade de colisão |
|--------------|--------------------------|
| 1.000 | 0,0001% |
| 10.000 | 0,001% |
| 100.000 | 0,01% |
| 1.000.000 | 0,1% |

**Conclusão:** Com até 1 milhão de URLs, chance de colisão é menor que 0,1%

---

## Estratégia de Retry

### Por que Retry?

Mesmo com baixa probabilidade, colisões podem ocorrer. O algoritmo tenta até 10 vezes antes de falhar.

### Comportamento

```
Tentativa 1: gera "a3k92" → já existe → retry
Tentativa 2: gera "B7x45" → disponível → sucesso
```

Se após 10 tentativas não encontrar código único:
- Retorna erro 500
- Log do evento
- Usuário pode tentar novamente

---

## Fluxo Completo

### 1. Usuário Solicita Encurtamento

```
Input: { originalUrl: "https://exemplo.com/artigo-longo" }
```

### 2. Sistema Valida URL

```
✓ Protocolo http/https
✓ Formato válido
✓ Comprimento < 2000
```

### 3. Gera Código

**Se código personalizado:**
```
→ validateCustomCode()
→ Se válido, usa código fornecido
```

**Se código automático:**
```
→ generateShortCode()
→ Tenta até 10 vezes
→ Retorna código único
```

### 4. Cria Registro

```javascript
const urlRecord = {
    id: code,
    code: code,
    originalUrl: originalUrl,
    shortUrl: `${baseUrl}/${code}`,
    clicks: 0,
    createdAt: new Date().toISOString()
};
```

### 5. Salva no Storage

```
→ Lê urls.json
→ Adiciona novo registro
→ Escreve arquivo
→ Cria backup
```

### 6. Retorna Resposta

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

---

## Otimizações

### 1. Cache de Códigos em Memória

Manter Set/Map em memória com códigos existentes:

```javascript
const codesCache = new Set();

function codeExists(code) {
    return codesCache.has(code);
}

function addCode(code) {
    codesCache.add(code);
}
```

**Benefício:** Busca O(1) ao invés de O(n)

### 2. Índice por Código

Se usar banco de dados:

```sql
CREATE INDEX idx_code ON urls(code);
```

### 3. Geração em Batch

Para alta demanda, gerar códigos em lote:

```javascript
function generateCodeBatch(count = 100) {
    const codes = [];
    while (codes.length < count) {
        const code = generateShortCode();
        if (!codes.includes(code) && !codeExists(code)) {
            codes.push(code);
        }
    }
    return codes;
}
```

---

## Análise de Performance

### Tempo de Geração

**Melhor caso:** ~1ms (código único na primeira tentativa)

**Pior caso:** ~10ms (10 tentativas)

**Média esperada:** ~1-2ms

### Gargalos

1. Leitura do arquivo JSON (I/O)
2. Busca no array (O(n))
3. Escrita do arquivo (I/O)

### Melhorias

- Cache em memória → -80% tempo busca
- Índice em DB → -95% tempo busca
- Write-behind cache → -50% tempo escrita

---

## Segurança

### Previsibilidade

**Problema:** Códigos aleatórios são impredizíveis

**Benefício:** Dificulta enumerar todas as URLs do sistema

### Força Bruta

Tentar adivinhar códigos:

```
Tentativas por segundo: 1000
Tempo para testar todas combinações: 916.132.832 / 1000 = 916.133 segundos ≈ 10 dias
```

**Mitigação:** Rate limiting previne força bruta

---

## Alternativas de Algoritmo

### 1. Hash da URL

```javascript
function generateCodeFromHash(url) {
    const hash = crypto.createHash('sha256').update(url).digest('hex');
    return hash.substring(0, 5);
}
```

**Vantagem:** URLs idênticas geram mesmo código

**Desvantagem:** Maior chance de colisão, código não é único

### 2. Auto-increment Base62

```javascript
function encodeBase62(num) {
    const chars = 'abcdefghijklmnopqrstuvwxyzABCDEFGHIJKLMNOPQRSTUVWXYZ0123456789';
    let code = '';
    while (num > 0) {
        code = chars[num % 62] + code;
        num = Math.floor(num / 62);
    }
    return code.padStart(5, 'a');
}

// ID 1 → aaaab
// ID 100 → aaa1m
```

**Vantagem:** Sem colisões, sequencial

**Desvantagem:** Previsível, expõe quantidade de URLs

### 3. UUID

```javascript
const uuid = require('uuid');
const code = uuid.v4().substring(0, 5);
```

**Vantagem:** Gerado rapidamente

**Desvantagem:** 5 caracteres de UUID tem alta colisão

---

## Escolha do Algoritmo Atual

**Método escolhido:** Geração aleatória com retry

**Justificativas:**

1. **Simplicidade:** Fácil de implementar e entender
2. **Suficiência:** 916M combinações é adequado para o caso de uso
3. **Imprevisibilidade:** URLs não são sequenciais ou previsíveis
4. **Performance:** Geração rápida com baixa taxa de retry
5. **Flexibilidade:** Permite códigos personalizados

---

## Testes

### Casos de Teste

**Teste 1: Geração básica**
```javascript
const code = generateShortCode();
assert(code.length === 5);
assert(/^[a-zA-Z0-9]{5}$/.test(code));
```

**Teste 2: Unicidade**
```javascript
const codes = new Set();
for (let i = 0; i < 1000; i++) {
    codes.add(generateShortCode());
}
assert(codes.size === 1000); // Todos únicos
```

**Teste 3: Código personalizado válido**
```javascript
const result = validateCustomCode('meu-link');
assert(result.valid === true);
```

**Teste 4: Código personalizado inválido**
```javascript
const result = validateCustomCode('ab'); // muito curto
assert(result.valid === false);
```

**Teste 5: Código reservado**
```javascript
const result = validateCustomCode('api');
assert(result.valid === false);
assert(result.error.includes('reservado'));
```

---

## Monitoramento

### Métricas Importantes

1. **Taxa de colisão:** Quantos retries por código gerado
2. **Tempo de geração:** Latência média
3. **Códigos ativos:** Total de URLs no sistema
4. **Utilização:** Percentual de combinações usadas

### Alertas

- Taxa de retry > 50% → Sistema próximo da capacidade
- Tempo de geração > 50ms → Problemas de performance
- Utilização > 80% → Considerar aumentar tamanho do código

---

## Evolução Futura

### Se Escalar para Milhões de URLs

**Opção 1:** Aumentar tamanho do código
```
5 caracteres → 916M combinações
6 caracteres → 56B combinações (62^6)
7 caracteres → 3,5T combinações (62^7)
```

**Opção 2:** Usar UUID completo
```
UUID v4 → 128 bits → praticamente infinito
```

**Opção 3:** Particionamento
```
Prefixo por tenant/usuário + código aleatório
Exemplo: user123_a3k92
```

---

## Referências

- [Regras de Negócio](./BUSINESS_RULES.md)
- [User Stories](./USER_STORIES.md)
- [Documentação da API](./api/README.md)
- [Base62 Encoding](https://en.wikipedia.org/wiki/Base62)
- [UUID v4](https://www.rfc-editor.org/rfc/rfc4122)