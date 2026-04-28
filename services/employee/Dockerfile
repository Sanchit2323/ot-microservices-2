FROM golang:1.20 as builder

WORKDIR /go/src/employee-api

# Dependencies
COPY go.mod go.mod
COPY go.sum go.sum
RUN go mod download

COPY client/ client/
COPY config/ config/
COPY middleware/ middleware/
COPY model/ model/
COPY routes/ routes/
COPY api/ api/
COPY docs/ docs/
COPY main.go main.go

# 👇 swag install + generate
RUN go install github.com/swaggo/swag/cmd/swag@v1.8.12
RUN swag init -g main.go

# 👇 Build binary
RUN CGO_ENABLED=0 GOOS=linux GOARCH=amd64 GO111MODULE=on go build -a -o employee-api main.go


# ---- Runtime Image ----
FROM alpine:3.18.0

LABEL authors="Opstree Solution" \
      contact="opensource@opstree.com" \
      version="v0.1.0"

WORKDIR /

COPY --from=builder /go/src/employee-api/employee-api .
COPY --from=builder /go/src/employee-api/config ./config
COPY --from=builder /go/src/employee-api/config/config.yaml ./config.yaml

ENV CONFIG_FILE=/config/config.yaml

USER 65532:65532

ENTRYPOINT ["/employee-api"]
