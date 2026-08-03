# 🚀 Desafio Técnico - API de Catálogo com Busca, Cache e Observabilidade

## 🎯 Objetivo

Construir uma API REST em **Laravel 10+** / **PHP 8.x** para gerenciar um catálogo de produtos com:

- ✅ Persistência em MySQL
- ✅ Busca e filtros via ElasticSearch
- ✅ Cache com Redis
- ✅ Ambiente Docker (app + mysql + redis + elastic)
- ✅ Testes unitários e (idealmente) feature tests
- ✅ Execução via Docker em ambiente Linux
- ✅ Simulação simples de integração AWS (S3 ou SQS)
- ✅ Pipeline CI básica

---

## 📦 Escopo Funcional

### 1️⃣ CRUD de Produtos (MySQL)

#### 🗂 Entidade: `Product`

| Campo | Tipo | Observação |
|-------|------|------------|
| `id` | UUID ou autoincremento | Escolha do candidato |
| `sku` | string | Único |
| `name` | string | Obrigatório |
| `description` | text | - |
| `price` | decimal | - |
| `category` | string | - |
| `status` | enum | active / inactive |
| `created_at` | timestamp | - |
| `updated_at` | timestamp | - |

#### 🔌 Endpoints
```
POST   /api/products
GET    /api/products/{id}
PUT    /api/products/{id}
DELETE /api/products/{id}
GET    /api/products (lista paginada + filtros básicos)
```

#### 📏 Regras de Negócio

- `sku` único
- `name` obrigatório (mínimo 3 caracteres)
- `price` > 0
- `status` padrão = `active`
- **Soft delete** (diferencial)

---

### 2️⃣ Busca com ElasticSearch

#### 🔎 Index

- Criar índice `products`
- Manter sincronização com MySQL

#### 🔌 Endpoint
```
GET /api/search/products
```

**Parâmetros suportados:**

- `q` - buscar em `name` e `description`
- `category`
- `min_price`
- `max_price`
- `status`
- `sort` - `price` ou `created_at`
- `order` - `asc` ou `desc`
- Paginação

#### 🔄 Sincronização

- ✅ Criar produto → reflete no índice
- ✅ Atualizar produto → reflete no índice
- ✅ Excluir produto → reflete no índice

**Implementação:**
- Observer + Job/Queue (diferencial)
- Implementação síncrona (aceita, se confiável)

---

### 3️⃣ Cache com Redis

Aplicar cache nos endpoints:

- `GET /api/products/{id}`
- `GET /api/search/products`

#### 📌 Regras de Cache

- TTL entre 60s e 120s
- Invalidação automática ao alterar/excluir produto
- Evitar cache para paginações muito altas (ex: `page > 50`) - diferencial
- Cache por combinação de parâmetros na busca

---

### 4️⃣ Testes (Obrigatório)

#### 🧪 Mínimo Esperado

- **Unit tests** (validações/regra de domínio)
- **Feature tests** (endpoints principais)

#### 📊 Cobertura Mínima Sugerida

- ✅ Criar produto (happy path + validação)
- ✅ Atualizar produto
- ✅ Buscar produto por ID (incluindo comportamento do cache)
- ✅ Endpoint de busca com pelo menos 2 filtros

#### 📝 Observação

- Pode usar **SQLite em memória** nos testes
- Deve justificar no README
- Runtime precisa funcionar com **MySQL**

---

### 5️⃣ Docker (Obrigatório)

#### 📦 `docker-compose.yml` deve conter:

- `app` (php-fpm ou Laravel Sail)
- `mysql`
- `redis`
- `elasticsearch`

#### ▶️ Subir Ambiente
```bash
docker compose up -d
```

#### 📌 Documentar no README:

- Como rodar migrations
- Como rodar seed
- Como rodar testes

---

## ⭐ Diferenciais (Pontuam bastante)

### A) AWS (Escolher 1)

#### Opção 1: S3 (Preferida)

- Endpoint `POST /api/products/{id}/image`
- Upload para storage (S3 real ou simulado)
- Salvar URL no produto
- Arquitetura testável

#### Opção 2: SQS

- Publicar mensagem `product.created` ao criar produto
- Pode usar **LocalStack** ou implementação fake
- Arquitetura clara e desacoplada

---

### B) CI/CD

- **GitHub Actions** configurado
- Lint (phpcs ou pint)
- Rodar testes automaticamente
- Subir mysql/redis/elastic no workflow

---

### C) Arquitetura e Código Limpo

- Controllers → Services/UseCases → Repositories
- DTO ou Request Objects
- Tratamento de erro padronizado (JSON consistente)
- Logs estruturados
- Separação clara de responsabilidades

---

## 📦 Entregáveis

- ✅ Repositório público ou privado com acesso concedido
- ✅ **README.md** contendo:
  - Requisitos
  - Como rodar com Docker
  - Como rodar testes
  - Decisões técnicas (curtas e objetivas)
  - Limitações conhecidas
  - Próximos passos
- ✅ Coleção Postman/Insomnia ou arquivo `.http` (opcional, mas recomendado)

---

## 🧠 Critérios de Avaliação

O que será analisado como **nível sênior:**

- ✅ API bem desenhada (status codes corretos)
- ✅ Validação consistente
- ✅ Mensagens claras de erro
- ✅ Migrations e seeders organizados
- ✅ ElasticSearch funcionando de verdade
- ✅ Cache corretamente aplicado e invalidado
- ✅ Testes confiáveis e fáceis de rodar
- ✅ Docker simples e reprodutível
- ✅ Código limpo e legível
- ✅ Logs úteis
- ✅ Boa separação de responsabilidades

---

## 🗓 Cronograma Sugerido (5 dias)

| Dia | Atividades |
|-----|------------|
| **Dia 1** | Setup Laravel + Docker + MySQL + CRUD |
| **Dia 2** | ElasticSearch (index + sync) |
| **Dia 3** | Redis (cache + invalidação) |
| **Dia 4** | Testes + seeds + padronização de erros |
| **Dia 5** | README caprichado + CI (opcional) + refinamentos |

---

## 📄 Licença

Este é um desafio técnico para avaliação de habilidades.

---

**Boa sorte! 🚀**
