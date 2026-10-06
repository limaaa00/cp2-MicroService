# 🎬 Cinema API — Check Point 2

API REST para gerenciamento de **filmes** e **salas** de cinema, desenvolvida em **Java 17 + Spring Boot**, com persistência em **SQL Server** via **Spring Data JPA**.

**Disciplina:** Microservices and Web Engineering — 2º semestre/2026
**Professor:** Antonio Carlos de Lima Júnior

## 👥 Integrantes

| Nome completo | RM |
|---|---|
| `Lucas Lima Franco` | `550255` |
| `Bruno Cesar Toledo d Oliveira` | `554878` |

---

## 🧰 Tecnologias

- Java 17
- Spring Boot 4 (Spring Web MVC)
- Spring Data JPA / Hibernate
- Microsoft SQL Server (driver `mssql-jdbc`)
- Swagger / OpenAPI (springdoc)
- Maven (wrapper incluso: `mvnw`)
- Docker (opcional)

## 🗂️ Estrutura do projeto

```
Cinema-api-final
├── database/schema.sql            # Script T-SQL das tabelas (opcional)
├── src/main/java/.../cinema
│   ├── Application.java           # Classe principal
│   ├── controller/                # Endpoints REST (FilmeController, SalaController)
│   ├── model/                     # Entidades JPA (Filme, Sala)
│   └── repository/                # Repositórios Spring Data JPA
├── src/main/resources
│   └── application.properties     # Configuração da conexão com o SQL Server
├── Dockerfile
├── pom.xml
└── mvnw / mvnw.cmd / .mvn/        # Maven Wrapper
```

---

## 🔐 Conexão com o SQL Server

A aplicação lê os dados de conexão de **variáveis de ambiente**, definidas em `src/main/resources/application.properties`.

| Variável | Descrição | Valor padrão |
|---|---|---|
| `DB_SERVER_URL` | Host/IP do SQL Server | `localhost` |
| `DB_SERVER_PORT` | Porta | `1433` |
| `DB_SCHEMA` | Nome do banco de dados | `<PREENCHER>` |
| `DB_USER` | Usuário | `sa` |
| `DB_PWD` | Senha | `<PREENCHER ou "obrigatória, sem padrão">` |

### 📌 Dados do banco disponibilizado para avaliação

| Item | Valor |
|---|---|
| Host | `<PREENCHER>` |
| Porta | `<PREENCHER>` |
| Banco de dados | `<PREENCHER>` |
| Usuário | `<PREENCHER>` |
| Senha | `<PREENCHER>` |

URL JDBC utilizada pela aplicação:

```
jdbc:sqlserver://<host>:<porta>;databaseName=<banco>;encrypt=true;trustServerCertificate=true
```

### Tabelas

As tabelas `filmes` e `salas` são **criadas automaticamente** pelo Hibernate na primeira execução (`spring.jpa.hibernate.ddl-auto=update`). O banco de dados precisa existir. Se preferir criar manualmente, execute `database/schema.sql`:

```bash
sqlcmd -S <host>,<porta> -U <usuario> -P <senha> -i database/schema.sql
```

---

## ▶️ Como executar

### Pré-requisitos

- **JDK 17** instalado (`java -version`)
- Acesso a um **SQL Server** (local ou remoto) com o banco criado
- Não é necessário instalar o Maven (o `mvnw` baixa automaticamente)

### Windows (PowerShell)

```powershell
$env:DB_SERVER_URL = "<host>"
$env:DB_SERVER_PORT = "1433"
$env:DB_SCHEMA = "<banco>"
$env:DB_USER = "<usuario>"
$env:DB_PWD = "<senha>"

./mvnw spring-boot:run
```

### Linux / macOS

```bash
export DB_SERVER_URL=<host> DB_SERVER_PORT=1433 DB_SCHEMA=<banco> DB_USER=<usuario> DB_PWD=<senha>
./mvnw spring-boot:run
```

Quando aparecer `Started Application`, a API estará disponível em **http://localhost:8080**.

> **Problemas comuns**
> - `Cannot find path '.mvn\wrapper\maven-wrapper.properties'` → a pasta oculta `.mvn/` não foi copiada. Clone o repositório completo.
> - `Could not resolve placeholder 'DB_PWD'` → a variável `DB_PWD` não foi definida.
> - `Login failed` / `Cannot open database` → confira usuário, senha e se o banco existe.
> - Timeout de conexão → verifique host, porta (1433) e firewall.

---

## 📋 Endpoints

Base URL: `http://localhost:8080`
Documentação interativa (Swagger): **http://localhost:8080/swagger-ui.html**

### Filmes — `/filmes`

| Método | Rota | Descrição |
|---|---|---|
| GET | `/filmes` | Lista todos os filmes |
| GET | `/filmes/{id}` | Busca filme por ID (404 se não existir) |
| POST | `/filmes` | Cadastra um filme |
| PUT | `/filmes/{id}` | Atualiza um filme |
| DELETE | `/filmes/{id}` | Remove um filme (204 / 404) |

**Corpo (JSON):**

```json
{
  "titulo": "Interestelar",
  "genero": "Ficção científica",
  "duracaoMinutos": 169,
  "classificacaoEtaria": "10 anos",
  "sinopse": "Exploradores viajam através de um buraco de minhoca."
}
```

### Salas — `/salas`

| Método | Rota | Descrição |
|---|---|---|
| GET | `/salas` | Lista todas as salas |
| GET | `/salas/{id}` | Busca sala por ID (404 se não existir) |
| POST | `/salas` | Cadastra uma sala |
| PUT | `/salas/{id}` | Atualiza uma sala |
| DELETE | `/salas/{id}` | Remove uma sala (204 / 404) |

**Corpo (JSON):**

```json
{
  "nome": "Sala 1",
  "tipo": "IMAX",
  "capacidade": 120,
  "tresD": true,
  "observacao": "Sala principal"
}
```

---

## 🧪 Como testar a API

Roteiro rápido (pelo Swagger ou pelo `curl`):

```bash
# 1) Inserir (grava no SQL Server)
curl -X POST http://localhost:8080/filmes \
  -H "Content-Type: application/json" \
  -d '{"titulo":"Interestelar","genero":"Ficção científica","duracaoMinutos":169,"classificacaoEtaria":"10 anos","sinopse":"Exploradores viajam através de um buraco de minhoca."}'

# 2) Consultar (lê do SQL Server)
curl http://localhost:8080/filmes
curl http://localhost:8080/filmes/1

# 3) Alterar
curl -X PUT http://localhost:8080/filmes/1 \
  -H "Content-Type: application/json" \
  -d '{"titulo":"Interestelar","genero":"Ficção","duracaoMinutos":169,"classificacaoEtaria":"12 anos","sinopse":null}'

# 4) Excluir
curl -X DELETE http://localhost:8080/filmes/1

# Salas
curl -X POST http://localhost:8080/salas \
  -H "Content-Type: application/json" \
  -d '{"nome":"Sala 1","tipo":"IMAX","capacidade":120,"tresD":true,"observacao":"Sala principal"}'
curl http://localhost:8080/salas
```

> No PowerShell, use `curl.exe` (e não `curl`) ou o Swagger.

### Verificando os dados no SQL Server

Após os testes, confirme diretamente no banco que os dados foram gravados:

```sql
SELECT * FROM filmes;
SELECT * FROM salas;
```

---

## 🐳 Execução com Docker (opcional)

```bash
docker build -t cinema-api:2.0 .

docker run -d --name cinema-api -p 8080:8080 \
  -e DB_SERVER_URL=<host> \
  -e DB_SERVER_PORT=1433 \
  -e DB_SCHEMA=<banco> \
  -e DB_USER=<usuario> \
  -e DB_PWD=<senha> \
  cinema-api:2.0
```

Se o SQL Server estiver na máquina host, use `host.docker.internal` em `DB_SERVER_URL`.
