# -----------------------------------------------------------------------------
# Build stage
# -----------------------------------------------------------------------------
FROM golang:1.25.4 AS builder

# Buildx automatic platform args
ARG TARGETOS
ARG TARGETARCH

RUN mkdir -p git gitactions-slack-notification

COPY . /go/src/gitactions-slack-notification

WORKDIR /go/src/gitactions-slack-notification

# Build static binary for target platform
RUN CGO_ENABLED=0 GOOS=${TARGETOS} GOARCH=${TARGETARCH} go build -o gitactions-slack-notification .

# -----------------------------------------------------------------------------
# Runtime stage
# -----------------------------------------------------------------------------
FROM gcr.io/distroless/static:nonroot

LABEL "com.github.actions.icon"="message-square"
LABEL "com.github.actions.color"="purple"
LABEL "com.github.actions.name"="GitActions Slack Notification"
LABEL "com.github.actions.description"="Send notification to Slack"

COPY --from=builder /go/src/gitactions-slack-notification/gitactions-slack-notification /gitactions-slack-notification
COPY --from=builder /go/src/gitactions-slack-notification/LICENSE /
COPY --from=builder /go/src/gitactions-slack-notification/README.md /

USER 65532:65532

ENTRYPOINT ["/gitactions-slack-notification"]
