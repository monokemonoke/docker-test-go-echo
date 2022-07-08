# syntax=docker/dockerfile:1
FROM golang:1.16 as build
WORKDIR /go/src/hoge

COPY ../../src/go.mod  .
COPY ../../src/go.sum  .
RUN go mod tidy

COPY ../../src ./
RUN go build -o app .

# ====================================

FROM gcr.io/distroless/base
COPY --from=build /go/src/hoge/app /
CMD ["/app"]