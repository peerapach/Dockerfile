FROM peerapach/ibm-mq-client:9.1.0.9 as mq-client
 
FROM golang:1.12.17-buster as builder
 
# IBM MQ
ENV CGO_LDFLAGS_ALLOW="-Wl,-rpath.*"
COPY --from=mq-client /opt/mqm /opt/mqm
 
## IBMMQ home dir and config
RUN mkdir -p /home/IBM && touch /home/.mqm
 
WORKDIR /app
 
# Copy code
COPY go.mod go.sum /app/
COPY main.go /app/main.go
 
# Build
RUN go build -mod=vendor -o /app
RUN chmod +x /app
 
 
FROM gcr.io/distroless/cc-debian11:nonroot-amd64 as release
 
ENV LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/opt/mqm/lib:/opt/mqm/lib64
ENV HOME=/home
 
### copy MQ lib to /opt/mqm (default path)
COPY --from=mq-client /opt/mqm/lib /opt/mqm/lib
COPY --from=mq-client /opt/mqm/lib64 /opt/mqm/lib64
 
# Copy IBMMQ home dir and config
COPY --from=builder --chown=1000 /home/IBM /home/IBM
COPY --from=builder --chown=1000 /home/.mqm /home/.mqm
 
# Copy binary from build stage
COPY --from=builder /app /app

USER 1000
 
ENTRYPOINT [ "/app" ]
