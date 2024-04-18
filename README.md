# OpenCHAMI Command-Line Utility

The `ochami-cli` tool provides a simple, and direct way to interact with OpenCHAMI services. It is capable of initiating logins for access tokens, managing nodes with SMD, and managing boot scripts/parameters with BSS. The tool is written and Python 3 and requires an interpreter to run.

## Managing Nodes

The tool comes with two other sub-commands `smd` and `bss` to interact with SMD and BSS respectively. Nodes can be added to SMD using the following command pointing to an instance of SMD:

```bash
./ochami-cli smd --config config.yml --url http://smd:27779/hsm/v2
```

## Get an Access Token

The `ochami-cli` is able to initiate the login process with the following command:

```
./ochami-cli login
```

After this command is ran, the `ochami-cli` will first check if you have a valid token either by supplying it with the `--access-token` or `--access-token-file` flags, setting it in the config file, or by setting the `OCHAMI_ACCESS_TOKEN` environment variable. If the token is expired, it will try to fetch a new one by initiating a login flow and open a browser to the login page. You will then be require to sign in with the options available. By default, the tool will save the token to a `.ochami-token` file.

You can include the token when performing actions that require it:

```bash
./ochami-cli --access-token eyJhbGciOiJ... smd --config config.yml --url http://smd:27779/hsm/v2
```

## Configuration

Here is an example `config.yaml` to get started using the `ochami-cli` tool:

```bash
nodes:
  - name: cg01
    xname: x1000c1s7b0n0
    mac: b4:...
    ipaddr: 172.16.0.1
  - name: cg02
    xname: x1000c1s7b1n0
    mac: b4:...
    ipaddr: 172.16.0.2
access-token: eyJhbGciOiJ...
```