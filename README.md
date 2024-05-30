# OpenCHAMI Command-Line Utility

The `ochami-cli` tool provides a simple, and direct way to interact with OpenCHAMI services. It is capable of initiating logins for access tokens, managing nodes with SMD, and managing boot scripts/parameters with BSS. The tool is written and Python 3 and requires an interpreter to run.

The tool comes with two other sub-commands `smd` and `bss` to interact with SMD and BSS respectively. 

## Installing Dependencies

```
pip install -r requirements.txt
```

## Managing Nodes

### SMD
You can skip specifying the `--smd-url` for each command by setting
```bash
export SMD_URL=http://smd:27779/hsm/v2
```

Nodes can be added to SMD with a config file using the following command pointing to an instance of SMD:
```bash
./ochami-cli --smd-url http://smd:27779/hsm/v2 smd --config config.yml
```
This will create the Node Components and Ethernet Interfaces. If `--fake-discovery` is added to the above and a `bmc_ipaddr` is defined for each node in the config, Redfish Endpoints and ComponentEndpoints will also be created. This will "simulate" hardware discovery. 

Nodes can be added manually with
```bash
./ochami-cli --smd-url http://smd:27779/hsm/v2 smd --add-node --xname <xname> --nid <nid>
```
and deleted with
```bash
./ochami-cli --smd-url http://smd:27779/hsm/v2 smd --delete-node --xname <xname>
```
You can also get the components in SMD with. You can specify the format with `--format`. The options are `json` and `yaml`. If not specified the output will be condensed. 
```bash
./ochami-cli --smd-url http://smd:27779/hsm/v2 smd --get-components --format {json|yaml}
```
or specify an `xname` or `nid`
```bash
./ochami-cli --smd-url http://smd:27779/hsm/v2 smd --get-components --xname <xname>
```

Ethernet Interfaces can also be added manually with:
```bash
./ochami-cli --smd-url http://smd:27779/hsm/v2 smd --add-interface --name <name> --xname <xname> --mac <mac> --ipaddr <ipaddr>
```
and deleted with
```bash
./ochami-cli --smd-url http://smd:27779/hsm/v2 smd --delete-interface --xname <xname>
```
Get all the EthernetInterfaces:
```bash
./ochami-cli --smd-url http://smd:27779/hsm/v2 smd --get-interfaces --format {json|yaml}
```
or specify an xname
```bash
./ochami-cli --smd-url http://smd:27779/hsm/v2 smd --get-interfaces --xname <xname>
```

You can dump SMD into yaml format that can then be read back into SMD. Useful for saving as a backup or saving updates done with the cmdline.
```bash
./ochami-cli --smd-url http://smd:27779/hsm/v2 smd --dump > nodes.yaml
```

Here is an example nodes.yaml file:
```yaml
nodes:
  - name: cg01
    xname: x1000c1s7b0n0
    mac: b4:...
    ipaddr: 172.16.0.1
    bmc_ipaddr: 172.16.100.1
  - name: cg02
    xname: x1000c1s7b1n0
    mac: b4:...
    ipaddr: 172.16.0.2
    bmc_ipaddr: 172.16.100.1
```
### BSS
You can skip specifying `--bss-url` by setting
```bash
export BSS_URL=http://bss:27778/boot/v1
```

BSS can be populated with a config file:
```bash
./ochami-cli --bss-url http://bss:27778/boot/v1 bss --config bss.yml --add-bootparams
```
or updated
```bash
./ochami-cli --bss-url http://bss:27778/boot/v1 bss --config bss.yml --update-bootparams
```

Individual components can be updates as well, you can choose one or more of the following after specifying `--update-bootparams`; `--kernel`, `--initrd`, `--image`, `--params`.
Updating the `--image` and `--params` at the same time will result in the `--params` overwriting what is specified in `--image`
```bash
./ochami-cli --bss-url http://bss:27778/boot/v1 bss --update-bootparams --image <image>
./ochami-cli --bss-url http://bss:27778/boot/v1 bss --update-bootparams --kernel <kernel> --initrd <initrd>
```
If no `--xname` or `--nid` is specified this will update all the nodes.

You can get the state of BSS with
```bash
./ochami-cli --bss-url http://bss:27778/boot/v1 bss --get-bootparams --format {json|yaml}
```
You can specify the format with `--format`. The options are `json` and `yaml`. If not specified the output will be condensed.

Here is an example BSS config file:
```yaml
macs:
  - a4:bf:01:11:da:b9
  - a4:bf:01:4c:16:4a
initrd: 'http://10.100.0.1/boot-images/initramfs.img'
kernel: 'http://10.100.0.1/boot-images/compute/vmlinuz'
params: 'nomodeset ro root=nfs:10.100.0.1:/exports/netroot/almalinux8.9:ro,vers=4.2,sec=sys,nolock ip=dhcp console=ttyS0,115200'
```

## Get an Access Token

The `ochami-cli` is able to initiate the login process with the following command:

```
./ochami-cli login
```
 
After this command is ran, the `ochami-cli` will first check if you have a valid token either by supplying it with the `--access-token` or `--access-token-file` flags, setting it in the config file, or by setting the `ACCESS_TOKEN` environment variable. If the token is expired, it will try to fetch a new one by initiating a login flow and open a browser to the login page. You will then be require to sign in with the options available. By default, the tool will save the token to a `.ochami-token` file.

You can include the token when performing actions that require it:

```bash
./ochami-cli --access-token eyJhbGciOiJ... smd --config config.yml --url http://smd:27779/hsm/v2
```

## Configuration
If SMD and/or BSS are configured to use cacerts and JWTs you can specify these with
```bash
./ochami-cli --ca-cert --access-token ...
```
you can also set environemnt variables for BOTH of these:
```bash
export CACERT=/path/to/cacert
export ACCESS_TOKEN=eyJhbGciOiJ
```

Helpful environment variables:
Skip having to specify `--smd-url`
```bash
export SMD_URL=http://smd:27779/hsm/v2
```
Skip having to specify `--bss-url`
```bash
export BSS_URL=http://bss:27778/boot/v1
```
Set NID name prefix (i.e. `nid001`). useful is you want to use something specific to your cluster. Only used on outputing SMD and BSS data.
```bash
export CLUSTER_PREFIX="nid"
```
