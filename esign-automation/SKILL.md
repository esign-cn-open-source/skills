---
name: esign-automation
description: Automate document signing workflows and operations using the eSignGlobal platform.
version: 1.3.0
homepage: https://github.com/esign-cn-open-source/skills
---

# eSign Automation

This skill provides automation capabilities for the eSignGlobal electronic signature platform.
It enables AI agents to automate document signing workflows and integrate with eSignGlobal APIs. 
This skill is maintained by the eSignGlobal team and is intended for safe automation of contract signing workflows.

## Best For

Use this skill when the user wants to:

- send a contract, agreement, or approval form for signature
- launch a new e-sign workflow from a local file
- send one document to one or more recipients for signing

Example requests:

- "Send this contract to John for signature"
- "Start a signing workflow for this PDF"
- "Send this agreement to Alice and Bob"

## What This Skill Does

The skill runs the following steps:

1. Authenticate with eSignGlobal using `ESIGNGLOBAL_APIKEY`
2. Request a secure upload URL
3. Upload the source document
4. Create and start the envelope

## Requirements

- Node.js 18 or later
- An eSignGlobal application key provided through an environment variable


## Required Configuration

Set the environment variable below before running the skill:

- `ESIGNGLOBAL_APIKEY`

If the user does not already have an app key, direct them to:

1. Sign in at `https://www.esignglobal.com`
2. Open `Settings -> Integration -> Apps`
3. Create an application and copy the generated API Key

This credential is used only to authenticate requests to the official eSignGlobal API.

Do not place credentials inside prompts, source code, or logs.

## Required Inputs

To send an envelope, collect:

- `filePath`: Absolute path to the document that should be sent for signature.
- `signers`: JSON array of signer objects

Optional input:

- `subject`: custom email or envelope subject; when omitted, the document name without its extension is used first

## Input Format

### filePath

`filePath` must be an absolute path to an existing local file.

Example:

```text
/tmp/contract.pdf
```

### signers

Each signer must include:

- `userName`
- `userEmail`

Optional field:

- `signOrder` (integer, minimum `1`)

Single signer example:

```json
[
  {
    "userName": "Bob Smith",
    "userEmail": "bob@example.com"
  }
]
```

Sequential signing example:

```json
[
  {
    "userName": "Bob Smith",
    "userEmail": "bob@example.com",
    "signOrder": 1
  },
  {
    "userName": "Alice Jones",
    "userEmail": "alice@example.com",
    "signOrder": 2
  }
]
```

Parallel signing example:

```json
[
  {
    "userName": "Bob Smith",
    "userEmail": "bob@example.com",
    "signOrder": 1
  },
  {
    "userName": "Alice Jones",
    "userEmail": "alice@example.com",
    "signOrder": 1
  }
]
```

## Output

The script returns JSON.

Success example:

```json
{
  "success": true,
  "step": "send",
  "message": "Envelope initiated successfully"
}
```

Failure example:

```json
{
  "success": false,
  "step": "send",
  "message": "Failed to execute envelope flow",
  "error": "Authentication failed"
}
```

## Network Behavior

This skill communicates only with official eSignGlobal API endpoints to perform:
- authentication
- upload URL creation
- document upload
- envelope creation

The upload URL is returned by the eSignGlobal API as a secure temporary endpoint.
The skill does not download external executables or install dependencies at runtime.

## File Access

This skill only accesses the local file explicitly provided by the user via filePath.
It does not scan directories or read unrelated system files.

## Security Notes

- Provide credentials only through `ESIGNGLOBAL_APIKEY`
- Never print, log, or persist secrets in files
- Only use trusted local files as input
