############################
# STEP 1 build executable binary
############################
FROM golang:1.16.4-buster as builder

# Ensure ca-certficates are up to date
RUN update-ca-certificates

WORKDIR $GOPATH/src/github.com/proge-software/lectio

# use modules
COPY go.mod .

ENV GO111MODULE=on
RUN go mod download \
    && go mod verify

COPY . .

# Build the binary
RUN mkdir -p /app/build \
    && CGO_ENABLED=0 go build \
		-ldflags="-w -s" \
		-a \
		-installsuffix cgo \
		-o /app/build/vision_bot \
		cmd/05.vision-bot/main.go

############################
# STEP 2 build a small image
############################
# using base nonroot image
# user:group is nobody:nobody, uid:gid = 65534:65534
FROM gcr.io/distroless/static

# Copy our static executable
COPY --from=builder /app/build/vision_bot /usr/bin/vision_bot

# Run the produced binary.
ENTRYPOINT ["/usr/bin/vision_bot"]
