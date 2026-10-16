# =====================================================================
# STAGE 1: LINGKUNGAN KOMPILASI (BUILDER)
# =====================================================================
FROM golang:1.22-alpine AS builder

WORKDIR /build

# Salin go.mod dan go.sum dulu supaya layer library bisa di-cache
COPY go.mod go.sum ./
RUN go mod download

# Salin kode sumber aplikasi
COPY main.go ./

# Kompilasi jadi biner mandiri dan pangkas simbol debug
RUN CGO_ENABLED=0 GOOS=linux GOARCH=amd64 \
    go build -ldflags="-s -w" -o /build/app-server main.go

# =====================================================================
# STAGE 2: LINGKUNGAN PRODUKSI MINIMALIS (TARGET < 100 MB)
# =====================================================================
FROM alpine:3.20 AS production

# wget untuk healthcheck, plus user non-root untuk keamanan
RUN apk --no-cache add ca-certificates wget && \
    addgroup -S appgroup && \
    adduser -S -u 10001 -G appgroup appuser

WORKDIR /app

# KUNCI MULTI-STAGE: salin HANYA biner hasil kompilasi dari stage builder
COPY --from=builder --chown=appuser:appgroup /build/app-server /app/app-server

USER appuser

EXPOSE 8080

ENTRYPOINT ["/app/app-server"]
