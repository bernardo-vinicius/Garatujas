# API REST

## O que é uma API?

API permite comunicação entre sistemas.

## REST

REST é um padrão para APIs HTTP.

## Métodos HTTP

| Método | Função |
|---|---|
| GET | Buscar dados |
| POST | Criar |
| PUT | Atualizar |
| PATCH | Atualização parcial |
| DELETE | Remover |

## Exemplo de rota

```txt
GET /usuarios
POST /usuarios
GET /usuarios/1
DELETE /usuarios/1
```

## JSON

Formato usado para troca de dados.

```json
{
  "nome": "Vinicius",
  "idade": 17
}
```

## Status Codes

| Código | Significado |
|---|---|
| 200 | OK |
| 201 | Criado |
| 400 | Erro do cliente |
| 401 | Não autorizado |
| 404 | Não encontrado |
| 500 | Erro interno |
