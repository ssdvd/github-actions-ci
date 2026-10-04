# github-actions-ci

Integração contínua com GitHub Actions para uma API em Go: a cada push ou pull request na `main`, o pipeline sobe o banco de dados e valida a aplicação em várias versões do Go.

Projeto do curso **Integração Contínua: testes automatizados e pipeline no GitHub Actions**, da Alura.

## O pipeline

O workflow [`go.yml`](.github/workflows/go.yml) tem dois jobs encadeados:

1. **test**: roda em uma matriz com Go `1.19`, `1.20` e `>=1.20`. Em cada versão, faz o checkout, configura o Go e sobe o PostgreSQL com `docker-compose`.
2. **build**: só executa se o `test` passar (`needs: test`).

> Os passos `go test` e `go build` estão comentados no workflow, do jeito que ficaram ao fim do curso. Para ativar a validação de verdade, basta descomentá-los.

## A aplicação

API REST de cadastro de alunos escrita em Go com [Gin](https://gin-gonic.com/) e [GORM](https://gorm.io/), usando PostgreSQL. É a aplicação de exemplo dos cursos da Alura (`guilhermeonrails/api-go-gin`); o foco deste repositório é o pipeline, não a API.

| Método | Rota | Descrição |
| --- | --- | --- |
| `GET` | `/:nome` | Saudação em JSON |
| `GET` | `/alunos` | Lista todos os alunos |
| `GET` | `/alunos/:id` | Busca um aluno pelo ID |
| `GET` | `/alunos/cpf/:cpf` | Busca um aluno pelo CPF |
| `POST` | `/alunos` | Cria um aluno (`nome`, `cpf` com 11 dígitos, `rg` com 9 dígitos) |
| `PATCH` | `/alunos/:id` | Edita um aluno |
| `DELETE` | `/alunos/:id` | Remove um aluno |
| `GET` | `/index` | Página HTML com a lista de alunos |

## Rodando localmente

Pré-requisitos: Go 1.19 ou superior, Docker e Docker Compose.

```bash
# sobe o PostgreSQL e o pgAdmin
docker-compose up -d

# variáveis lidas pela aplicação para conectar no banco
export HOST=localhost USER=root PASSWORD=root DBNAME=root DBPORT=5432

go run main.go              # API em http://localhost:8080
go test -v main_test.go     # testes de integração (precisam do banco no ar)
```

O pgAdmin fica em <http://localhost:54321>.

## Série de CI/CD com GitHub Actions

Este repositório faz parte de uma sequência em que o mesmo pipeline vai ganhando etapas:

| # | Repositório | O que acrescenta |
| --- | --- | --- |
| 1 | [github-actions-ci](https://github.com/ssdvd/github-actions-ci) | Testes automatizados e matriz de versões do Go |
| 2 | [github-actions-ci-docker](https://github.com/ssdvd/github-actions-ci-docker) | Build da imagem e push para o Docker Hub |
| 3 | [github-actions-cicd-ec2](https://github.com/ssdvd/github-actions-cicd-ec2) | Deploy contínuo em uma instância EC2 via SSH |
| 4 | [github-actions-cicd-ecs](https://github.com/ssdvd/github-actions-cicd-ecs) | Deploy contínuo no Amazon ECS |
| 5 | [github-actions-cicd-rollback-tests](https://github.com/ssdvd/github-actions-cicd-rollback-tests) | Rollback automático e teste de carga |
| 6 | [github-actions-cicd-kubernetes](https://github.com/ssdvd/github-actions-cicd-kubernetes) | Deploy contínuo no Kubernetes (EKS) |

As anotações de cada aula estão na pasta [`notes/`](notes).
