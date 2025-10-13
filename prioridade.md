# 🚀 PRIORIDADE: Como Subir a Aplicação LiteLLM Corretamente

## 📋 PRÉ-REQUISITOS

- Docker e Docker Compose instalados
- Chaves de API dos provedores LLM (OpenAI, Anthropic, etc.)
- Porta 4000, 5432 e 9090 disponíveis

## ⚠️ PASSOS OBRIGATÓRIOS

### 1. **CRIAR ARQUIVO `.env` (CRÍTICO)**

Crie o arquivo `.env` na pasta raiz do projeto com as seguintes configurações:

```bash
# ===========================================
# CONFIGURAÇÕES OBRIGATÓRIAS
# ===========================================

# Chave mestra para autenticação do LiteLLM (OBRIGATÓRIA)
LITELLM_MASTER_KEY=sk-1234

# Base de dados (já configurada no docker-compose.yml)
DATABASE_URL=postgresql://llmproxy:dbpassword9090@db:5432/litellm

# ===========================================
# CHAVES DOS PROVEDORES LLM
# ===========================================

# OpenAI (descomente e configure se usar)
# OPENAI_API_KEY=sk-proj-your-openai-key-here

# Anthropic (descomente e configure se usar)
# ANTHROPIC_API_KEY=sk-ant-your-anthropic-key-here

# Google (descomente e configure se usar)
# GOOGLE_APPLICATION_CREDENTIALS=/path/to/service-account.json

# Azure OpenAI (descomente e configure se usar)
# AZURE_API_KEY=your-azure-key
# AZURE_API_BASE=https://your-resource.openai.azure.com/
# AZURE_API_VERSION=2023-05-15

# ===========================================
# CONFIGURAÇÕES OPCIONAIS
# ===========================================

# Nível de log
LITELLM_LOG=INFO

# Armazenar modelos no banco
STORE_MODEL_IN_DB=True

# Modo debug (descomente para desenvolvimento)
# DEBUG=True

# Timeout para requests (segundos)
# REQUEST_TIMEOUT=600

# ===========================================
# CONFIGURAÇÕES AVANÇADAS (OPCIONAL)
# ===========================================

# Caching (Redis - se disponível)
# REDIS_HOST=localhost
# REDIS_PORT=6379
# REDIS_PASSWORD=your-redis-password

# Observabilidade
# OTEL_EXPORTER=prometheus
# LANGFUSE_PUBLIC_KEY=your-langfuse-key
# LANGFUSE_SECRET_KEY=your-langfuse-secret
```

### 2. **COMANDOS PARA SUBIR A APLICAÇÃO**

```bash
# Navegar para a pasta do projeto
cd C:\lovable\Docker-Affinify\litellm

# Parar containers anteriores (se houver)
docker-compose down

# Subir todos os serviços em background
docker-compose up -d

# OU subir com logs visíveis (para debug)
docker-compose up
```

### 3. **VERIFICAR SE SUBIU CORRETAMENTE**

```bash
# Verificar status dos containers
docker-compose ps

# Verificar logs
docker-compose logs litellm
docker-compose logs db
docker-compose logs prometheus

# Verificar health check
curl http://localhost:4000/health/liveliness
```

## 🌐 ACESSOS DA APLICAÇÃO

Após subir corretamente, você terá acesso a:

| Serviço | URL | Descrição |
|---------|-----|-----------|
| **LiteLLM UI** | http://localhost:4000 | Interface principal do LiteLLM |
| **LiteLLM API** | http://localhost:4000/v1 | Endpoint da API (compatível OpenAI) |
| **Docs API** | http://localhost:4000/docs | Documentação interativa (Swagger) |
| **Prometheus** | http://localhost:9090 | Métricas e monitoramento |
| **PostgreSQL** | localhost:5432 | Banco de dados (acesso direto) |

### 🔑 **Credenciais de Acesso:**

- **LiteLLM Master Key:** Valor definido em `LITELLM_MASTER_KEY`
- **PostgreSQL:**
  - Host: `localhost:5432`
  - Database: `litellm`
  - User: `llmproxy`
  - Password: `dbpassword9090`

## 🧪 TESTANDO A APLICAÇÃO

### 1. **Teste básico via cURL:**
```bash
curl -X POST http://localhost:4000/v1/chat/completions \
  -H "Authorization: Bearer YOUR_LITELLM_MASTER_KEY" \
  -H "Content-Type: application/json" \
  -d '{
    "model": "gpt-3.5-turbo",
    "messages": [{"role": "user", "content": "Hello!"}]
  }'
```

### 2. **Teste via interface web:**
- Acesse: http://localhost:4000
- Use a Master Key para autenticar
- Teste diferentes modelos na interface

## 🔧 COMANDOS ÚTEIS

### **Gerenciamento básico:**
```bash
# Parar aplicação
docker-compose down

# Parar e remover volumes (APAGA DADOS!)
docker-compose down -v

# Rebuild containers
docker-compose up -d --build

# Ver logs em tempo real
docker-compose logs -f litellm

# Reiniciar serviço específico
docker-compose restart litellm
```

### **Limpeza completa:**
```bash
# Parar tudo
docker-compose down -v

# Remover volumes órfãos
docker volume prune

# Remover imagens não utilizadas
docker image prune
```

### **Backup do banco:**
```bash
# Backup
docker exec litellm_db pg_dump -U llmproxy litellm > backup.sql

# Restore
docker exec -i litellm_db psql -U llmproxy litellm < backup.sql
```

## ⚠️ SOLUÇÃO DE PROBLEMAS

### **Container não sobe:**
1. Verificar se arquivo `.env` existe e tem `LITELLM_MASTER_KEY`
2. Verificar se portas estão livres: `netstat -an | findstr ":4000 :5432 :9090"`
3. Verificar logs: `docker-compose logs litellm`

### **Erro de conexão com banco:**
1. Aguardar alguns segundos (banco demora para inicializar)
2. Verificar se container `litellm_db` está saudável: `docker-compose ps`

### **Interface não carrega:**
1. Verificar health check: `curl http://localhost:4000/health/liveliness`
2. Verificar se `LITELLM_MASTER_KEY` está configurado corretamente

### **Problemas de performance:**
1. Verificar recursos do Docker (CPU/RAM)
2. Monitorar via Prometheus: http://localhost:9090

## 📊 MONITORAMENTO

### **Métricas importantes:**
- **Prometheus:** http://localhost:9090
- **Logs:** `docker-compose logs -f`
- **Health checks:** Configurados automaticamente

### **Alertas configurados:**
- Health check a cada 30s
- Retry automático (3 tentativas)
- Timeout de 10s por health check

## 🚨 SEGURANÇA

### **Pontos críticos:**
1. **Master Key:** Manter segura e complexa
2. **API Keys:** Nunca commitar no git
3. **Banco:** Senha padrão (trocar em produção)
4. **Portas:** Firewall adequado em produção

### **Recomendações:**
- Usar HTTPS em produção
- Configurar rate limiting
- Monitorar uso via dashboard
- Backup regular do banco

---

## ✅ CHECKLIST DE VERIFICAÇÃO

- [ ] Arquivo `.env` criado com `LITELLM_MASTER_KEY`
- [ ] Docker e Docker Compose funcionando
- [ ] Portas 4000, 5432, 9090 livres
- [ ] Comando `docker-compose up -d` executado
- [ ] Health check retorna 200: http://localhost:4000/health/liveliness
- [ ] Interface acessível: http://localhost:4000
- [ ] Logs sem erros críticos: `docker-compose logs litellm`

---

## 📞 SUPORTE

Em caso de problemas:
1. Verificar logs: `docker-compose logs`
2. Consultar documentação oficial: https://docs.litellm.ai/
3. Verificar issues no GitHub: https://github.com/BerriAI/litellm

**⚠️ IMPORTANTE:** Sem o arquivo `.env` com `LITELLM_MASTER_KEY`, a aplicação NÃO funcionará!
