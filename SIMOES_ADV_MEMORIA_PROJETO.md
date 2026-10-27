# MEMÓRIA DE PROJETO - SIMÕES ADVOGADOS CRM (INSTÂNCIA DEDICADA)

Este repositório contém a aplicação CRM whitelabel configurada exclusivamente para **Simões Advogados** (`Simoes-adv`).

---

## 📌 1. Especificações da Instância

- **Nome da Empresa**: Simões Advogados
- **Identificador**: `Simoes-adv` / `simoesadv`
- **Domínio Público VPS**: `https://simoesadv.vps11814.panel.icontainer.work`
- **Alias WWW**: `www.simoesadv.vps11814.panel.icontainer.work`
- **Porta Externa / Servidor da Instância**: `3003`
- **Porta Interna da Aplicação (Container / Node)**: `3000`
- **Secret JWT**: `simoesadv_crm_secret_key_2026_safe`
- **Assets de Marca**:
  - Logo: `public/assets/logo_simoesadv.jpg`
  - Favicon: `public/assets/favicon_simoesadv.jpg`

---

## 🗄️ 2. Banco de Dados PostgreSQL Dedicado

- **Host**: `localhost` (ou `172.17.0.1` na rede interna Docker)
- **Porta**: `5432`
- **Usuário**: `simoesadv`
- **Senha**: `W8s3N9m2K7vL4q1Z`
- **Nome do Banco de Dados**: `simoesadv` (ou `crm_simoesadv_db`)
- **Permissão**: `Super Usuário`
- **String de Conexão (`DATABASE_URL`)**:
  ```env
  DATABASE_URL="postgres://simoesadv:W8s3N9m2K7vL4q1Z@172.17.0.1:5432/simoesadv"
  ```

### Script de Criação no PostgreSQL:
Execute no terminal da VPS (ou via psql / pgAdmin):
```sql
CREATE USER simoesadv WITH PASSWORD 'W8s3N9m2K7vL4q1Z' SUPERUSER;
CREATE DATABASE simoesadv OWNER simoesadv;
GRANT ALL PRIVILEGES ON DATABASE simoesadv TO simoesadv;
```

---

## 🚀 3. Instruções de Deploy

### Opção A: Deploy no Painel iContainer / Docker Standalone
1. **Domínio e SSL**:
   - Domínio principal: `simoesadv.vps11814.panel.icontainer.work`
   - Incluir alias: `www.simoesadv.vps11814.panel.icontainer.work`
   - Marcar: *Emitir SSL automaticamente e forçar HTTPS*.
2. **Configuração de Portas**:
   - Porta da aplicação (Container): `3000`
   - Porta externa (Host): `3003`
3. **Variáveis de Ambiente (`.env`)**:
   ```env
   PORT=3000
   JWT_SECRET=simoesadv_crm_secret_key_2026_safe
   COMPANY_NAME="Simões Advogados"
   COMPANY_LOGO_URL="assets/logo_simoesadv.jpg"
   COMPANY_FAVICON_URL="assets/favicon_simoesadv.jpg"
   DATABASE_URL="postgres://simoesadv:W8s3N9m2K7vL4q1Z@172.17.0.1:5432/simoesadv"
   APP_URL="https://simoesadv.vps11814.panel.icontainer.work"
   GOOGLE_CLIENT_ID="seu_google_client_id.apps.googleusercontent.com"
   GOOGLE_CLIENT_SECRET="seu_google_client_secret"
   GOOGLE_REDIRECT_URI="https://simoesadv.vps11814.panel.icontainer.work/api/crm/auth/google/callback"
   GOOGLE_DRIVE_PARENT_FOLDER_ID="1OFMwMIyPUryXFOdBDYt46MP0dA_mHUCC"
   ```

---

### Opção B: Deploy Direto na VPS (Node.js + PM2 + Nginx)

1. **Clonar Repositório ou Copiar Diretório**:
   ```bash
   cd /var/www
   # Copiar pasta painel-crm-simoes-adv
   cd painel-crm-simoes-adv
   npm install --production
   ```

2. **Arquivo `.env`**:
   Certifique-se de que o arquivo `.env` está presente na raiz com a porta `PORT=3003`.

3. **Iniciar Processo com PM2**:
   ```bash
   pm2 start server.js --name "crm-simoesadv"
   pm2 save
   ```

4. **Configuração do Nginx (Reverse Proxy)**:
   Adicionar no arquivo `/etc/nginx/sites-available/simoesadv`:
   ```nginx
   server {
       server_name simoesadv.vps11814.panel.icontainer.work www.simoesadv.vps11814.panel.icontainer.work;

       location / {
           proxy_pass http://localhost:3003;
           proxy_http_version 1.1;
           proxy_set_header Upgrade $http_upgrade;
           proxy_set_header Connection 'upgrade';
           proxy_set_header Host $host;
           proxy_cache_bypass $http_upgrade;
       }
   }
   ```
   Habilitar e recarregar o Nginx:
   ```bash
   sudo ln -s /etc/nginx/sites-available/simoesadv /etc/nginx/sites-enabled/
   sudo systemctl reload nginx
   ```

---

## 📁 4. Integração Google Drive & OAuth2

1. Criar projeto no Google Cloud Console ou utilizar a mesma credencial adicionando a URI:
   - **Origem JavaScript autorizada**: `https://simoesadv.vps11814.panel.icontainer.work`
   - **URI de redirecionamento autorizada**: `https://simoesadv.vps11814.panel.icontainer.work/api/crm/auth/google/callback`
2. Obter `GOOGLE_CLIENT_ID` e `GOOGLE_CLIENT_SECRET` e preencher no `.env`.
3. Criar a pasta dedicada no Google Drive da Simões Advogados e preencher o ID no `.env` ou diretamente no CRM na aba **Configurações**.

---

## 🧩 5. Mapeamento de Portas e Instâncias no Servidor

| Instância | Cliente | Porta Externa | Porta Interna | Banco PostgreSQL |
| :--- | :--- | :--- | :--- | :--- |
| Matriz Whitelabel | Base Matriz | `3000` | `3000` | `crm_whitelabel` |
| Instância 1 | ITACRED | `3001` | `3000` | `crm_itacred_db` |
| Instância 2 | Dra. Jéssica | `3002` | `3000` | `drjessica` |
| **Instância 3** | **Simões Advogados** | `3003` | `3000` | `simoesadv` |

---
*Memória técnica gerada automaticamente para manutenção e escalabilidade da instância.*
