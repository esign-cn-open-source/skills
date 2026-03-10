# eSign Automation Skill

This project provides an skill for automating document signing workflows using the eSignGlobal platform.
The skill enables AI agents to interact with eSignGlobal APIs to manage document signing processes, automate contract workflows, and retrieve signing results.

## Features

- send a local document for e-signature
- support one or more signers
- support sequential or parallel signing via `signOrder`
- return structured JSON for easy automation and debugging

## Project Structure

```text
.
|- SKILL.md
`- scripts/
   `- send_envelope.ts
```

## Prerequisites

- Node.js 18+
- `npx tsx` available in your environment
- a valid eSignGlobal app key

If `tsx` is not already available, you can run it directly with `npx`.

## Configure Credentials

Set the `ESIGNGLOBAL_APIKEY` environment variable.

### Windows PowerShell

```powershell
$env:ESIGNGLOBAL_APIKEY="yourAPIKEY"
```

### macOS / Linux

```bash
export ESIGNGLOBAL_APIKEY="yourAPIKEY"
```

If you do not have an app key yet:

1. Sign in to `https://www.esignglobal.com`
2. Go to `Settings -> Integration -> Apps`
3. Create an application
4. Copy the generated API Key

## Usage

Run the script with:

```bash
npx tsx scripts/send_envelope.ts send <filePath> <signersJson> [subject]
```

Parameters:

- `filePath`: absolute path to the local file you want to send
- `signersJson`: JSON array of signer objects
- `subject`: optional envelope subject; if omitted, the file name without its extension is used first

## Signer Format

Each signer object supports:

- `userName` (required)
- `userEmail` (required)
- `signOrder` (optional, integer >= 1)

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

## Examples

### Send a PDF to one signer

```bash
npx tsx scripts/send_envelope.ts send "/absolute/path/contract.pdf" '[{"userName":"Bob Smith","userEmail":"bob@example.com"}]' "Please sign this contract"
```

If `subject` is omitted, `/absolute/path/contract.pdf` defaults to `contract`.

### Send a document to multiple signers in sequence

```bash
npx tsx scripts/send_envelope.ts send "/absolute/path/agreement.pdf" '[{"userName":"Bob Smith","userEmail":"bob@example.com","signOrder":1},{"userName":"Alice Jones","userEmail":"alice@example.com","signOrder":2}]' "Approval workflow"
```

## Response Format

Success example:

```json
{
  "success": true,
  "step": "send",
  "message": "Envelope initiated successfully",
  "data": {
    "filePath": "/absolute/path/contract.pdf",
    "fileKey": "file_key_here"
  }
}
```

Failure example:

```json
{
  "success": false,
  "step": "send",
  "message": "Failed to execute envelope flow",
  "error": "Missing environment variable ESIGNGLOBAL_APIKEY"
}
```

## Troubleshooting

- `Missing environment variable ESIGNGLOBAL_APIKEY`: set the app key before running the script
- `File not found`: use an absolute path and confirm the file exists
- `Invalid signersJson`: check that the JSON is valid and each signer has `userName` and `userEmail`
- authentication or API errors: verify the app key, data center, and account permissions

## Security

- do not hardcode credentials in source files
- do not print secrets in logs or terminal history
- use trusted local documents only

## Marketplace Packaging Notes

For skill marketplaces, use `SKILL.md` as the main skill definition and include this `README.md` as the user-facing setup and usage guide.
