# Deploy no Render

Guia minimalista para publicar o Encurtador de URL no Render.

---

## O que é o Render?

Plataforma de hospedagem que permite publicar aplicações Node.js gratuitamente com:

- Deploy automático a partir do GitHub
- HTTPS configurado automaticamente
- Domínio gratuito (.onrender.com)
- Zero configuração de servidor

---

## Pré-requisitos

- Conta no GitHub
- Conta no Render (criar em render.com)
- Código no GitHub

---

## Passo a Passo

### 1. Preparar o Projeto

Criar arquivo `package.json` na raiz:

```json
{
  "name": "encurtador-url",
  "version": "1.0.0",
  "scripts": {
    "start": "node backend/src/server.js"
  },
  "dependencies": {
    "express": "^4.18.2"
  }
}
```

### 2. Criar Web Service no Render

1. Acesse dashboard.render.com
2. Clique em "New +"
3. Escolha "Web Service"
4. Conecte seu repositório GitHub
5. Selecione o repositório do projeto

### 3. Configurar o Service

**Nome:** encurtador-url (ou qualquer nome)

**Environment:** Node

**Build Command:**
```
npm install
```

**Start Command:**
```
npm start
```

**Plan:** Free

### 4. Variáveis de Ambiente

Adicionar em "Environment":

```
NODE_ENV=production
PORT=3000
```

### 5. Deploy

1. Clique em "Create Web Service"
2. Aguarde o build (2-3 minutos)
3. Aplicação estará disponível em: `https://seu-app.onrender.com`

---

## Deploy Automático

A cada push na branch `main`:

1. Render detecta mudança
2. Faz build automaticamente
3. Publica nova versão
4. Zero downtime

---

## Estrutura de Arquivos

```
ai-coding-dojo/
├── backend/
│   ├── src/
│   │   └── server.js
│   └── data/
│       └── urls.json
├── frontend/
│   └── index.html
└── package.json
```

---

## URLs da Aplicação

**Frontend:** https://seu-app.onrender.com/

**API:** https://seu-app.onrender.com/api/urls

**Redirecionar:** https://seu-app.onrender.com/:code

---

## Persistência de Dados

**Free Plan:**

- Arquivos são persistidos entre deploys
- Dados em `data/urls.json` são mantidos
- Backups são preservados

**Limitações:**

- Inatividade por 15 minutos desliga o serviço
- Primeiro acesso após inatividade demora ~30s (cold start)
- Sem garantia de backup automático

---

## Logs

Ver logs em tempo real:

1. Acesse seu service no dashboard
2. Clique em "Logs"
3. Ou use Render CLI

---

## Domínio Customizado

Para usar domínio próprio:

1. Vá em "Settings" do service
2. Clique em "Custom Domain"
3. Adicione seu domínio
4. Configure DNS conforme instruções

---

## Troubleshooting

**Problema:** Deploy falhou

**Solução:** 
- Verificar logs de build
- Confirmar package.json correto
- Verificar se start command está correto

**Problema:** Aplicação não responde

**Solução:**
- Verificar se PORT está configurado
- Confirmar se servidor escuta na porta correta: `process.env.PORT || 3000`

**Problema:** Dados perdidos

**Solução:**
- Free plan tem limitações de persistência
- Considerar upgrade para plano pago
- Ou usar banco de dados externo (PostgreSQL)

---

## Limitações do Free Plan

- Cold start após inatividade
- 750 horas/mês de uptime
- Desliga após 15 minutos sem requisições
- Sem SLA de uptime
- Compartilha recursos

---

## Melhorias Futuras

**Para produção real:**

- Upgrade para plano pago ($7/mês)
- Adicionar PostgreSQL (gratuito no Render)
- Configurar Redis para cache
- Adicionar monitoramento (Sentry, etc)
- Configurar CI/CD com GitHub Actions

---

## Health Check

Render verifica automaticamente se app está rodando.

Opcional - criar endpoint de health:

```javascript
app.get('/health', (req, res) => {
  res.json({ status: 'ok', timestamp: new Date() });
});
```

---

## Comandos Úteis

**Ver status:**
```bash
# Via dashboard
render services list
```

**Ver logs:**
```bash
render logs -f
```

**Forçar deploy:**
```bash
# Push qualquer commit para GitHub
git commit --allow-empty -m "Trigger deploy"
git push
```

---

## Custos

**Free Plan:** $0/mês
- Suficiente para MVP e testes
- 750 horas de uptime
- Cold starts

**Starter Plan:** $7/mês
- Sem cold starts
- Mais recursos
- Melhor performance

---

## Checklist de Deploy

- [ ] Código no GitHub
- [ ] package.json configurado
- [ ] Porta configurável (process.env.PORT)
- [ ] Build command definido
- [ ] Start command definido
- [ ] Variáveis de ambiente configuradas
- [ ] Testado localmente
- [ ] Deploy realizado
- [ ] URL funcionando
- [ ] Redirecionamentos testados

---

## Links Úteis

- Dashboard: https://dashboard.render.com
- Documentação: https://render.com/docs
- Status: https://status.render.com

---

## Referências

- [Documentação Principal](../README.md)
- [Documentação da API](../api/README.md)
- [Documentação do Frontend](../frontend/README.md)