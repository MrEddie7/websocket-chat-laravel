<p align="center"><a href="https://laravel.com" target="_blank"><img src="https://raw.githubusercontent.com/laravel/art/master/logo-lockup/5%20SVG/2%20CMYK/1%20Full%20Color/laravel-logolockup-cmyk-red.svg" width="400" alt="Laravel Logo"></a></p>

# Chat em Tempo Real com WebSocket – Laravel + Chatify + Pusher

## Sobre o Projeto

Este projeto implementa um **chat em tempo real** utilizando a tecnologia **WebSocket** no Laravel 11, permitindo comunicação persistente e bidirecional entre cliente e servidor. O servidor envia dados em tempo real a qualquer momento, sem necessidade de requisições constantes (como acontece no HTTP tradicional).

O que torna isso possível:
- **Pusher** – serviço de WebSocket em nuvem que mantém a conexão persistente.
- **Chatify** – pacote que adiciona um sistema completo de chat em tempo real.
- **Laravel Breeze** – sistema de autenticação para perfis de usuários.

## Tecnologias Utilizadas

| Tecnologia | Versão | Função |
|---|---|---|
| PHP | 8.2 | Linguagem base |
| Laravel | 11 | Framework |
| MariaDB (XAMPP) | - | Banco de dados `chatweb3ams` |
| Laravel Breeze | 2.x | Autenticação e perfis |
| Chatify (munafio) | 1.x | Sistema de chat |
| Pusher | 7.x | Canal WebSocket em tempo real |

## Etapas da Construção

### 1º Passo – Criar o projeto Laravel

```
composer create-project laravel/laravel:^11.0 websocket
```

### 2º Passo – Criar o banco de dados

No PHPMYADMIN (http://localhost/phpmyadmin) ou via comando, criar o banco `chatweb3ams`:

```sql
CREATE DATABASE chatweb3ams CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;
```

### 3º Passo – Instalar o Laravel Breeze (perfis de usuários)

```bash
composer require laravel/breeze --dev
php artisan breeze:install blade
php artisan migrate
```

### 4º Passo – Compilar os assets e testar

```bash
npm install
npm run dev       # terminal 1
php artisan serve # terminal 2
```

### 5º Passo – Instalar o Chatify (chat em tempo real)

```bash
composer require munafio/chatify
php artisan chatify:install
php artisan migrate
```

### 6º Passo – Criar canal no Pusher

1. Acesse https://pusher.com
2. Na opção **Channels**, crie um novo canal (App)
3. Selecione o cluster desejado (neste projeto: `sa1`)
4. Na aba **Getting Started**, copie as credenciais

### 7º Passo – Configurar o arquivo `.env`

```env
DB_CONNECTION=mysql
DB_HOST=127.0.0.1
DB_PORT=3306
DB_DATABASE=chatweb3ams
DB_USERNAME=root
DB_PASSWORD=

BROADCAST_CONNECTION=pusher
BROADCAST_DRIVER=pusher
QUEUE_CONNECTION=database

PUSHER_APP_ID=seu_app_id
PUSHER_APP_KEY=sua_chave
PUSHER_APP_SECRET=seu_secreto
PUSHER_APP_CLUSTER=seu_cluster
PUSHER_HOST=
PUSHER_PORT=443
PUSHER_SCHEME=https

VITE_PUSHER_APP_KEY=sua_chave
VITE_PUSHER_HOST=
VITE_PUSHER_PORT=443
VITE_PUSHER_SCHEME=https
VITE_PUSHER_APP_CLUSTER=seu_cluster
```

### 8º Passo – Testar o chat

```bash
npm run dev        # terminal 1 (compila o frontend)
php artisan serve  # terminal 2 (sempre no diretório do projeto)
```

1. Acesse `http://127.0.0.1:8000`
2. Crie **duas contas** na página de registro (Ex.: `usuario1@teste.com` e `usuario2@teste.com`)
3. Abra o chat no menu **Chat**
4. Selecione o outro usuário e envie mensagens em tempo real

> Para testar o tempo real, abra o chat em **dois navegadores diferentes** (ou duas janelas anônimas) com contas distintas e converse.

## Como o WebSocket Funciona Neste Projeto

```
┌──────────────┐      HTTP      ┌──────────────┐      WebSocket      ┌──────────┐
│   Cliente 1  │ ─────────────► │    Laravel   │ ──────────────────► │  Pusher  │
│  (Browser)   │ ◄───────────── │  (Servidor)  │ ◄─────────────────  │ (Cloud)  │
└──────────────┘     event      └──────────────┘                    └──────────┘
       ▲                                                                    │
       │                         mensagem em tempo real                    │
       └────────────────────────────────────────────────────────────────────┘
```

1. O usuário **1** envia uma mensagem pelo navegador.
2. O Laravel recebe via HTTP e dispara um evento no canal do **Pusher**.
3. O Pusher reenvia o evento para todos os clientes conectados ao mesmo canal via **WebSocket**.
4. O usuário **2** (e o usuário 1) recebem a mensagem **instantaneamente**, sem recarregar a página.

## Estrutura de Pastas do Chatify

```
vendor/munafio/chatify/
├── src/
│   ├── Http/Controllers/     → Controllers do chat
│   ├── Models/               → Modelo ChMessage
│   ├── routes/               → Rotas do chat (web e api)
│   ├── views/                → Views Blade do chat
│   └── config/chatify.php    → Configurações (Pusher, avatares, anexos)
```

Arquivos publicados no projeto:

```
config/chatify.php
database/migrations/2026_09_09_999999_*.php       → tabelas do chat
routes/chatify/web.php
routes/chatify/api.php
DOCTYPE /Javascript/assets → public/js/chatify/*.js
```

## Demonstração – Execução do Projeto

### Registro de dois usuários

No menu **Register** (criar conta), cadastrar dois usuários:

| Usuário | E-mail | Senha |
|---|---|---|
| Usuario Um | usuario1@teste.com | 123456789 |
| Usuario Dois | usuario2@teste.com | 123456789 |

*(usuários criados via `php artisan tinker`)*

## Comandos Úteis

```bash
php artisan serve                    # sobe o servidor
php artisan migrate                  # executa migrações
php artisan config:clear             # limpa cache de configuração
npm install                          # instala dependências frontend
npm run build                        # compila o frontend
npm run dev                          # compila com watch
```

## Conclusão

O projeto demonstra o uso de **WebSocket** em uma aplicação Laravel para comunicação em tempo real entre múltiplos usuários, utilizando **Pusher** como serviço de WebSocket e **Chatify** para o sistema de chat completo. A conexão persistente permite que mensagens sejam entregues instantaneamente, sem recarregamento de página.

## Licença

O Laravel é um software open-source licenciado sob [MIT](https://opensource.org/licenses/MIT).
