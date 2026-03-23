# ===== Builder Stage =====
FROM golang:1.23-alpine AS builder
WORKDIR /app

# 国内加速（必开）
RUN go env -w GOPROXY=https://goproxy.cn,direct

# 从根目录 context 精准复制 server/ 里的 Go 文件
COPY server/go.mod server/go.sum ./
RUN go mod download

# 复制 server/ 全部内容到当前目录（这样 cmd/ 就在 ./cmd 下）
COPY server/. .

# 编译（已确认路径正确）
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