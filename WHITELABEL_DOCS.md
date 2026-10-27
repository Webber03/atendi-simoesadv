# 📘 Documentação de Migração & Arquitetura Whitelabel CRM

Este documento preserva todo o histórico de decisões, arquitetura e instruções de implantação desenvolvidas para a versão **Whitelabel Matriz** do Painel CRM.

---

## 🎯 Objetivo da Versão Whitelabel
Criar um modelo base limpo, seguro e padronizado do Painel CRM para ser duplicado e implantado em instâncias isoladas na VPS para novos clientes.

---

## 🏗️ Arquitetura de Implantação (VPS + PostgreSQL Isolado)

1. **Instância Dedicada por Cliente**:
   - Cada cliente roda em seu próprio diretório na VPS (ex: `/var/www/crm-cliente-a`, `/var/www/crm-cliente-b`).
   - Cada instância roda em sua própria porta configurada via `.env` (ex: `3001`, `3002`).

2. **Banco de Dados Isolado**:
   - Cada cliente possui um banco PostgreSQL dedicado (`crm_cliente_a`, `crm_cliente_b`).
   - Garante segurança e impede qualquer vazamento de dados entre empresas.

3. **Google Drive para Documentos**:
   - Os documentos continuam organizados em pastas no Google Drive.
   - A pasta pai de cada cliente é configurada pela variável \`GOOGLE_DRIVE_PARENT_FOLDER_ID\` no \`.env\`.

---

## 🎨 Identidade Visual Dinâmica (Branding)

A aplicação não possui nomes ou logos estáticos fixos no código HTML. Toda a marca é carregada dinamicamente via backend no arquivo \`.env\` de cada cliente:

\`\`\`env
COMPANY_NAME="Nome do Cliente"
COMPANY_LOGO_URL="assets/IMG_0457.png"
COMPANY_FAVICON_URL="assets/IMG_0457.png"
\`\`\`

O endpoint \`GET /api/whitelabel/config\` e o script \`public/js/auth.js\` atualizam automaticamente o título da aba, cabeçalhos e logos na tela.

---

## 🧩 Módulos Incluídos na Versão Whitelabel

1. **Usuários e Acessos (RBAC)**: Roles de Admin, Supervisor, SDR e Closer.
2. **Busca e Tabulações**: Pesquisa de clientes por CPF/nome/telefone e registro de contatos.
3. **Kanban SDR**: Funil de prospecção e qualificação.
4. **Kanban Consultor (Closer)**: Funil de atendimento e fechamento de vendas.
5. **Relatórios Analíticos CRM**:
   - KPIs Globais (Prospectados, Passagens SDR ➔ Closer, Perdas e SLA).
   - Métricas filtradas por data real do evento e por responsável.
   - Ranking de SDRs e Closers.
6. **Admin CRM & Fila de Closers**: Rodízio de Closers, cadastro de etapas, regras de SLA e motivos de perda.

---

## 🚀 Passo a Passo para Implantar um Novo Cliente

1. **Copiar a pasta matriz**:
   \`\`\`bash
   cp -r /caminho/painel-crm-whitelabel /var/www/crm-cliente-xyz
   cd /var/www/crm-cliente-xyz
   \`\`\`
2. **Criar o banco PostgreSQL**:
   \`\`\`sql
   CREATE DATABASE crm_cliente_xyz;
   \`\`\`
3. **Configurar o \`.env\`**:
   \`\`\`bash
   cp .env.example .env
   \`\`\`
4. **Iniciar a aplicação no PM2**:
   \`\`\`bash
   npm install --production
   pm2 start server.js --name "crm-cliente-xyz"
   \`\`\`

---
*Documentação gerada automaticamente para preservação do histórico da matriz Whitelabel.*
