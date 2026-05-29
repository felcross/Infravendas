```markdown
# 🛒 Infravendas - Ecossistema de Microserviços

Este repositório gerencia e orquestra a infraestrutura completa do ecossistema **Infravendas** utilizando Docker Compose. A arquitetura conta com aplicações integradas (BFF, Microserviços de Negócio, Frontend) e camadas de persistência e mensageria robustas.

---

## 🏗️ Arquitetura do Ecossistema

O ambiente é composto pelos seguintes serviços integrados na mesma rede interna (`rede`):

* **Frontend:** Interface de usuário rodando na porta `80`.
* **BFF (Backend-For-Frontend):** Ponto único de entrada para o Frontend, distribuindo requisições na porta `8084`.
* **Bancos de Dados:**
    * `PostgreSQL 16` (Porta `5432`) - Utilizado pelo serviço de Clientes.
    * `MongoDB 7` (Porta `27017`) - Utilizado pelos serviços de Produtos e Vendas.
* **Mensageria (Broker):**
    * `RabbitMQ (Management)` (Portas `5672` e `15672` para o painel) - Utilizado para comunicação assíncrona entre Vendas e Notificação.
* **Microserviços:**
    * `Clientes` (Spring Boot / PostgreSQL) -> Porta `8080`
    * `Produtos` (Spring Boot / MongoDB) -> Porta `8081`
    * `Vendas` (Spring Boot / MongoDB + RabbitMQ) -> Porta `8082`
    * `Notificação` (Spring Boot / RabbitMQ + JavaMail) -> Porta `8083`

---

## 🚀 Como Rodar o Projeto Localmente

### 📋 Pré-requisitos
Certifique-se de ter instalado em sua máquina:
* [Docker](https://docs.docker.com/get-docker/)
* [Docker Compose](https://docs.docker.com/compose/install/)

---

### 🛠️ Passo a Passo

### 1. Clonar o Repositório
```bash
git clone [https://github.com/felcross/Infravendas.git](https://github.com/felcross/Infravendas.git)
cd Infravendas

```

### 2. Configurar Variáveis de Ambiente

O `docker-compose.yml` utiliza variáveis dinâmicas para proteger credenciais de bancos, chaves JWT e serviços de e-mail.

Crie um arquivo chamado **`.env`** na raiz do projeto (mesmo diretório onde está o arquivo `docker-compose.yml`) e preencha as configurações conforme o modelo abaixo:

```env
# Configurações do PostgreSQL (Clientes)
DB_USERNAME=postgres
DB_PASSWORD=suasenhasegura

# Configurações do RabbitMQ
RABBITMQ_USER=guest
RABBITMQ_PASS=guest

# Segurança (JWT)
JWT_SECRET=UmTokenSuperSecretoEDiscretoComVariosCaracteres123!

# Configurações de E-mail (Serviço de Notificação)
MAIL_USERNAME=seu-email@gmail.com
MAIL_PASSWORD=sua-senha-de-app-ou-token

```

### 3. Inicializar a Infraestrutura

Com o Docker aberto e o arquivo `.env` configurado, execute o comando abaixo para baixar as imagens e iniciar todos os containers em segundo plano (background):

```bash
docker compose up -d

```

> **Nota de Resiliência:** O compose possui `healthchecks` configurados para o PostgreSQL, MongoDB e RabbitMQ. Os microserviços aguardarão automaticamente até que os bancos e o broker estejam 100% prontos para receber conexões antes de iniciarem, evitando falhas de inicialização (`ConnectionRefused`).

---

## 🔍 Monitorando o Ambiente

### Verificar se os containers estão rodando:

```bash
docker compose ps

```

### Acompanhar os logs em tempo real:

```bash
# Todos os serviços
docker compose logs -f

# Apenas de um serviço específico (ex: microserviço de vendas)
docker compose logs -f vendas

```

---

## 🌐 Portas de Acesso Úteis

Assim que todos os containers subirem com sucesso, você poderá acessar os serviços através dos seguintes endereços locais:

| Serviço | URL Local | Descrição |
| --- | --- | --- |
| **Frontend** | [http://localhost](https://www.google.com/search?q=http://localhost) | Aplicação Web |
| **BFF** | [http://localhost:8084](https://www.google.com/search?q=http://localhost:8084) | Gateway / Entrada do Backend |
| **RabbitMQ Dashboard** | [http://localhost:15672](https://www.google.com/search?q=http://localhost:15672) | Painel Web do RabbitMQ (User/Pass definidos no `.env`) |
| **Microserviço Clientes** | [http://localhost:8080](https://www.google.com/search?q=http://localhost:8080) | API Rest de Clientes |
| **Microserviço Produtos** | [http://localhost:8081](https://www.google.com/search?q=http://localhost:8081) | API Rest de Produtos |
| **Microserviço Vendas** | [http://localhost:8082](https://www.google.com/search?q=http://localhost:8082) | API Rest de Vendas |
| **Microserviço Notificação** | [http://localhost:8083](https://www.google.com/search?q=http://localhost:8083) | Processamento de Notificações |

---

## 🛑 Como Parar o Ambiente

Para encerrar a execução de todos os microserviços mantendo os dados salvos nos bancos locais (volumes persistentes):

```bash
docker compose down

```

Se desejar parar os containers e **remover totalmente** os dados salvos nos volumes (zerar o banco de dados):

```bash
docker compose down -v

```
