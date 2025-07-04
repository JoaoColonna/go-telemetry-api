﻿# go-telemetry-api

API para ingestão e processamento de dados de telemetria, com integração a banco de dados, filas NATS e AWS Rekognition.

Desenvolvido até o Nível 5.

## Pré-requisitos

- Docker e Docker Compose instalados
- Conta AWS com credenciais válidas para Rekognition
- Go (opcional, para rodar fora do Docker)

## Configuração

1. **Clone o repositório:**
   ```sh
   git clone https://github.com/JoaoColonna/go-telemetry-api.git
   cd go-telemetry-api
   ```

2. **Crie o arquivo `.env` na raiz:**
   ```env
   DATABASE_URL=postgres://user:password@db:5432/telemetry?sslmode=disable
   PORT=8080
   #Teste em ambiente AWS
   AWS_REGION=us-east-1 
   AWS_ACCESS_KEY_ID=SEU_ACCESS_KEY
   AWS_SECRET_ACCESS_KEY=SEU_SECRET_KEY
   ```

3. **Suba os containers:**
   ```sh
   docker-compose up --build
   ```
   Isso irá subir:
   - API Go
   - Banco de dados PostgreSQL
   - NATS (mensageria)

## Testando a API

1. **Acesse a documentação Swagger:**
   - [http://localhost:8080/swagger/index.html](http://localhost:8080/swagger/index.html) (ajuste a porta se necessário)

2. **Envie requisições:**
   - Use o Swagger, Postman ou cURL para testar os endpoints `/telemetry/gyroscope`, `/telemetry/gps` e `/telemetry/photo`.

3. **Verifique o banco de dados:**
   - Acesse o banco via DBeaver, TablePlus ou outro cliente PostgreSQL:
     ```
     Host: localhost
     Porta: 5432
     Usuário: user
     Senha: password
     Banco: telemetry
     ```

4. **Visualize as filas NATS:**
   - Inicie um processo de nats-box no Docker com o seguinte comando:
     ```
     docker run --rm -it --network go-challenge_default natsio/nats-box
     ```
   - Acesse o nats-box no docker
   - Use o terminal do nats-box para inspecionar mensagens:
     ```
     nats sub photo.telemetry --server nats://nats:4222 // mensagens enviadas
     nats sub photo_result.telemetry --server nats://nats:4222 // mensagens recebidas com sucesso
     nats sub photo_result.telemetry.dlq --server nats://nats:4222 // mensagens recebidas com erro
     ```

## Integração com AWS Rekognition

- O endpoint `/telemetry/photo` envia fotos para análise no AWS Rekognition.
- Certifique-se de que as credenciais AWS no `.env` têm permissão para Rekognition.
- O projeto por padrão utiliza dados mockados para validar as imagens e ter um retorno válido, mas é configurável

## Parar e remover containers

```sh
docker-compose down
```

## Dicas de Troubleshooting

- Verifique logs dos containers com `docker-compose logs`.
- Certifique-se de que as portas 5432 (Postgres), 4222 (NATS) e 8080 (API) estão livres.
- Para resetar o banco, remova o volume Docker correspondente.
 
