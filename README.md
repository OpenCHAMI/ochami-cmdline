# OpenCHAMI Command-Line Utility

The `ochami-cli` tool provides a simple, and straight-way to interact with OpenCHAMI services. It is capable of handling login for access tokens, managing nodes with SMD, and boot scripts/parameters with BSS.

## Logging in for an Access Token

The `ochami-cli` is able to initiate the login process with the following command:

```
./ochami-cli login
```

After this command is ran, the `ochami-cli` will first check if you have a valid token either by supplying it with the `--access-token` or `--access-token-file` flags, setting it in the config file, or by setting the `OCHAMI_ACCESS_TOKEN` environment variable. If the token is expired, it will try to fetch a new one by initiating a login flow and open a browser to the login page. You will then be require to sign in with the options available.

## Managing Nodes

The tool comes with two other sub-commands `smd` and `bss` to interact with SMD and BSS respectively.
