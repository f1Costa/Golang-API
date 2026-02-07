# DevBook API

API RESTful para gerenciamento de usuarios, construida em Go com autenticacao JWT e banco de dados MySQL.

## Arquitetura

```
.
├── main.go                        # Entrypoint da aplicacao
├── sql.sql                        # Script de criacao do banco de dados
└── src/
    ├── autenticacao/token.go      # Criacao e validacao de tokens JWT
    ├── banco/banco.go             # Conexao com o MySQL
    ├── config/config.go           # Carregamento de variaveis de ambiente
    ├── controllers/               # Handlers HTTP (usuarios, login)
    ├── middlewares/middlewares.go  # Logger e autenticacao de rotas
    ├── modelos/usuario.go         # Modelo de dados e validacoes
    ├── repositorios/usuarios.go   # Camada de acesso ao banco de dados
    ├── respostas/respostas.go     # Padronizacao de respostas JSON
    ├── router/                    # Configuracao de rotas
    └── seguranca/seguranca.go     # Hash e verificacao de senhas (bcrypt)
```

## Pre-requisitos

- **Go** 1.25+
- **MySQL** 5.7+

## Configuracao

### 1. Banco de dados

Execute o script SQL para criar o banco e a tabela:

```bash
mysql -u root -p < sql.sql
```

### 2. Variaveis de ambiente

Crie um arquivo `.env` na raiz do projeto:

```env
API_PORT=5000
DB_USUARIO=root
DB_SENHA=sua_senha
DB_NOME=devbook
SECRET_KEY=sua-chave-secreta
```

### 3. Build e execucao

```bash
# Instalar dependencias
go mod tidy

# Compilar
go build -o api ./main.go

# Executar
./api
```

A API estara disponivel em `http://localhost:5000`.

## Endpoints

### Autenticacao

| Metodo | Rota     | Descricao          | Auth |
|--------|----------|---------------------|------|
| POST   | `/login` | Autenticar usuario | Nao  |

### Usuarios

| Metodo | Rota                    | Descricao                        | Auth |
|--------|-------------------------|----------------------------------|------|
| POST   | `/usuarios`             | Criar novo usuario               | Nao  |
| GET    | `/usuarios`             | Buscar usuarios por nome ou nick | Sim  |
| GET    | `/usuarios/{usuarioID}` | Buscar usuario por ID            | Sim  |
| PUT    | `/usuarios/{usuarioID}` | Atualizar usuario                | Sim  |
| DELETE | `/usuarios/{usuarioID}` | Deletar usuario                  | Sim  |

> Rotas autenticadas exigem o header `Authorization: Bearer <token>`.
> Operacoes de update e delete sao restritas ao proprio usuario.

## Exemplos de uso

### Criar usuario

```bash
curl -X POST http://localhost:5000/usuarios \
  -H "Content-Type: application/json" \
  -d '{
    "nome": "Felipe",
    "nick": "felipe",
    "email": "felipe@email.com",
    "senha": "123456"
  }'
```

### Login

```bash
curl -X POST http://localhost:5000/login \
  -H "Content-Type: application/json" \
  -d '{
    "email": "felipe@email.com",
    "senha": "123456"
  }'
```

### Buscar usuarios (com filtro)

```bash
curl -X GET "http://localhost:5000/usuarios?usuario=felipe" \
  -H "Authorization: Bearer <token>"
```

### Atualizar usuario

```bash
curl -X PUT http://localhost:5000/usuarios/1 \
  -H "Content-Type: application/json" \
  -H "Authorization: Bearer <token>" \
  -d '{
    "nome": "Felipe Atualizado",
    "nick": "felipe_novo",
    "email": "novo@email.com"
  }'
```

### Deletar usuario

```bash
curl -X DELETE http://localhost:5000/usuarios/1 \
  -H "Authorization: Bearer <token>"
```

## Tecnologias

- [gorilla/mux](https://github.com/gorilla/mux) — Roteamento HTTP
- [dgrijalva/jwt-go](https://github.com/dgrijalva/jwt-go) — Autenticacao JWT
- [go-sql-driver/mysql](https://github.com/go-sql-driver/mysql) — Driver MySQL
- [golang.org/x/crypto](https://pkg.go.dev/golang.org/x/crypto) — Hash de senhas com bcrypt
- [joho/godotenv](https://github.com/joho/godotenv) — Carregamento de `.env`
- [badoux/checkmail](https://github.com/badoux/checkmail) — Validacao de e-mail
