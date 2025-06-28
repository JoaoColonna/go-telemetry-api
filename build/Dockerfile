# Dockerfile
FROM golang:1.20 as builder

WORKDIR /app
COPY . .

RUN go mod tidy

RUN go test ./internal/tests/...

RUN go build -o main .

FROM debian:bullseye
WORKDIR /app
COPY --from=builder /app/main .

CMD ["./main"]
