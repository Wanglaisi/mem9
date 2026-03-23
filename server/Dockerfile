# ===== Builder Stage =====
FROM golang:1.23-alpine AS builder
WORKDIR /app

# 加速国内用户（可选，删掉也行）
RUN go env -w GOPROXY=https://goproxy.cn,direct

COPY go.mod go.sum ./
RUN go mod download

COPY . .
# 关键：按你仓库结构编译（cmd 在根目录或 server/cmd，根据实际）
RUN CGO_ENABLED=0 GOOS=linux go build -ldflags="-s -w" -o /mnemo-server ./cmd/mnemo-server

# ===== Runtime Stage =====
FROM alpine:3.19
RUN apk add --no-cache ca-certificates tzdata && \
    adduser -D -H -u 1000 mem9

COPY --from=builder /mnemo-server /mnemo-server
RUN chmod +x /mnemo-server

EXPOSE 8080
USER mem9
ENTRYPOINT ["/mnemo-server"]