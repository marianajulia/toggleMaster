FROM golang:1.22-alpine AS builder

WORKDIR /app

RUN apk add --no-cache ca-certificates git

COPY go.mod ./
COPY . .

# Limpa o go.sum e baixa dependências
RUN rm -f go.sum && go mod tidy && go mod download

# Compila o binário de forma estática sem dependências C
RUN CGO_ENABLED=0 GOOS=linux go build -a -installsuffix cgo -ldflags="-w -s" -o evaluation-service ./cmd/evaluation-service/main.go || \
    CGO_ENABLED=0 GOOS=linux go build -a -installsuffix cgo -ldflags="-w -s" -o evaluation-service .

    
# Imagem final enxuta
FROM alpine:3.19

WORKDIR /app

RUN apk --no-cache add ca-certificates
RUN addgroup -S appgroup && adduser -S appuser -G appgroup

COPY --from=builder /app/evaluation-service .

USER appuser

EXPOSE 8080

ENTRYPOINT ["./evaluation-service"]