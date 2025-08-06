# Transport API Client

A Go client library for interacting with the Message Gateway Transport API. This client provides methods for managing channels, templates, and message operations in a messaging platform.

## Features

- **REST API Client**: Generated from OpenAPI specification for type-safe API interactions
- **Transport-Level Operations**: Methods for managing messaging transport including:
  - Channel management (activation, deactivation, updates)
  - Template management (activation, deactivation, updates)
  - Message operations (sending, editing, deleting)
  - File handling (uploading, retrieving)
  - Message reactions and read status management

## Installation

```bash
go get github.com/kifril-ltd/transport-api-client-gen
```

## Usage

### Initializing the Client

```go
package main

import (
    "context"
    "log"

    "github.com/kifril-ltd/trabsport-api-client-gen"
)

func main() {
    // Create a new client with the server URL
    client, err := transport_api_client.NewClientWithResponses("https://api.example.com")
    if err != nil {
        log.Fatalf("Error creating client: %v", err)
    }

    // Use the client to make API calls
    // ...
}
```

### API Examples

#### Listing Channels

```go
ctx := context.Background()
resp, err := client.ListChannelsWithResponse(ctx, nil)
if err != nil {
    log.Fatalf("Error listing channels: %v", err)
}

if resp.JSON200 != nil {
    for _, channel := range resp.JSON200.Channels {
        log.Printf("Channel: %s", channel.Name)
    }
}
```

#### Activating a Channel

```go
ctx := context.Background()
request := transport_api_client.ActivateChannelJSONRequestBody{
    ChannelID: "channel123",
    Token:     "your-channel-token",
}

resp, err := client.ActivateChannelWithResponse(ctx, request)
if err != nil {
    log.Fatalf("Error activating channel: %v", err)
}

if resp.JSON200 != nil {
    log.Printf("Channel activated successfully")
}
```

#### Sending a Message

```go
ctx := context.Background()
message := transport_api_client.SendMessageJSONRequestBody{
    ChannelID: "channel123",
    UserID:    "user456",
    Type:      "text",
    Content: transport_api_client.MessageContent{
        Text: "Hello from the transport API!",
    },
}

resp, err := client.SendMessageWithResponse(ctx, message)
if err != nil {
    log.Fatalf("Error sending message: %v", err)
}

if resp.JSON200 != nil {
    log.Printf("Message sent with ID: %s", resp.JSON200.MessageID)
}
```
1
#### Working with Templates

```go
// Get available templates
ctx := context.Background()
resp, err := client.GetTemplatesWithResponse(ctx)
if err != nil {
    log.Fatalf("Error getting templates: %v", err)
}

if resp.JSON200 != nil {
    for _, template := range resp.JSON200.Templates {
        log.Printf("Template: %s", template.Name)
    }
}

// Activate a template
activateReq := transport_api_client.ActivateTemplateJSONRequestBody{
    TemplateCode: "welcome_message",
    Language:     "en",
}

activateResp, err := client.ActivateTemplateWithResponse(ctx, "channel123", activateReq)
if err != nil {
    log.Fatalf("Error activating template: %v", err)
}

if activateResp.JSON200 != nil {
    log.Printf("Template activated successfully")
}
```

#### File Operations

```go
// Upload a file by URL
ctx := context.Background()
uploadReq := transport_api_client.UploadFileByUrlJSONRequestBody{
    URL:      "https://example.com/image.jpg",
    Filename: "image.jpg",
}

uploadResp, err := client.UploadFileByUrlWithResponse(ctx, uploadReq)
if err != nil {
    log.Fatalf("Error uploading file: %v", err)
}

if uploadResp.JSON200 != nil {
    fileID := uploadResp.JSON200.FileUUID
    log.Printf("File uploaded with ID: %s", fileID)

    // Get file URL
    fileResp, err := client.GetFileUrlWithResponse(ctx, transport_api_client.FileUUIDPath(fileID))
    if err != nil {
        log.Fatalf("Error getting file URL: %v", err)
    }

    if fileResp.JSON200 != nil {
        log.Printf("File URL: %s", fileResp.JSON200.URL)
    }
}
```

## Error Handling1

The client provides detailed error responses that can be handled as follows:

```go
resp, err := client.SendMessageWithResponse(ctx, message)
if err != nil {
    log.Fatalf("Error sending message: %v", err)
}

if resp.JSONDefault != nil {
    log.Printf("API Error: %s - %s", resp.JSONDefault.Code, resp.JSONDefault.Message)
}
```

## License

This library is distributed under the terms of the license agreement provided with the software.
