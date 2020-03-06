# Terraform LS

Experimental prototype of Terraform language server.

## Disclaimer

This is not an officially supported HashiCorp product.

## How to try it out

```
go install .
```

This should produce a binary called `terraform-ls` in `$GOBIN/terraform-ls`.

Putting `$GOBIN` in your `$PATH` may save you from having to specify
absolute path to the binary.

### Visual Studio Code

Try https://github.com/aeschright/tf-vscode-demo/pull/1 - instructions are in that PR.

### Sublime Text 2

 - Install the [LSP package](https://github.com/sublimelsp/LSP#installation)
 - Add the following snippet to the LSP settings' clients:

```json
"terraform": {
  "command": ["terraform-ls", "serve"],
  "enabled": true,
  "languageId": "terraform",
  "scopes": ["source.terraform"],
  "syntaxes":  ["Packages/Terraform/Terraform.sublime-syntax"]
}
```

## Troubleshooting

[PacketSender](https://packetsender.com) enables you to open a TCP socket with a server, when launched as such.
Approximate steps of debugging follow.

 - Install PacketSender (e.g. on MacOS via `brew cask install packet-sender`)
 - Launch LS in TCP mode: `terraform-ls serve -port=8080`
 - Send any requests via PacketSender
   - Set `Address` to `127.0.0.1`
   - Set `Port` to `8080`
   - Tick `Persistent TCP`
   - Hit the `Send` button (which opens the TCP connection)
   - Paste or type request in LSP format (see below) & hit `Send`

Examples of formatted requests follow.

```
Content-Length: 164\n\n{"jsonrpc":"2.0","params":{"textDocument":{"uri":"file:///var/path/to/file/main.tf"},"position":{"line":1,"character":0}},"method":"textDocument/completion","id":2}
```
```
Content-Length: 72\n\n{"jsonrpc":"2.0","params":{"id":2},"method":"$/cancelRequest","id":null}
```
```
Content-Length: 47\n\n{"jsonrpc":"2.0","method":"shutdown","id":null}
```

### Notes

 - Keep in mind that each TCP session receives an isolated context,
    so you cannot cancel requests you didn't start yourself
 - Any client can initiate server shutdown at this point and
    the server generally stores state with the assumption of a single client

## Credits

The implementation was inspired by:

 - [`juliosueiras/terraform-lsp`](https://github.com/juliosueiras/terraform-lsp)
 - [Martin Atkins](https://github.com/apparentlymart) (particularly the virtual filesystem)