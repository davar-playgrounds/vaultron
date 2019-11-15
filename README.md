# Vaultron

![](https://github.com/brianshumate/vaultron/blob/master/share/vaultron.png?raw=true)

- [Vaultron](#vaultron)
  * [What?](#what)
  * [Why?](#why)
  * [How?](#how)
    + [Prerequisites](#prerequisites)
    + [Quickest Start for macOS](#quickest-start-for-macos)
    + [Quick Start for Linux or macOS](#quick-start-for-linux-or-macos)
    + [What's Next?](#whats-next)
      - [Ten Things You Can do After Vaultron is Formed](#ten-things-you-can-do-after-vaultron-is-formed)
    + [Advanced Example](#advanced-example)
  * [What's in the Box?](#whats-in-the-box)
    + [Basic Architecture Overview](#basic-architecture-overview)
      - [Vault Servers](#vault-servers)
      - [Consul Servers](#consul-servers)
      - [Consul Clients](#consul-clients)
      - [statsd](#statsd)
      - [Grafana](#grafana)
    + [Environment Variables](#environment-variables)
      - [TF_VAR_vault_version](#tf_var_vault_version)
      - [TF_VAR_consul_version](#tf_var_consul_version)
      - [TF_VAR_datacenter_name](#tf_var_datacenter_name)
      - [TF_VAR_use_vault_oss](#tf_var_use_vault_oss)
      - [TF_VAR_vault_server_log_format (Vault v0.10.0+)](#tf_var_vault_server_log_format-vault-v0100)
      - [TF_VAR_vault_server_log_level](#tf_var_vault_server_log_level)
      - [TF_VAR_consul_log_level](#tf_var_consul_log_level)
      - [TF_VAR_vault_path](#tf_var_vault_path)
      - [TF_VAR_vault_cluster_name](#tf_var_vault_cluster_name)
      - [TF_VAR_disable_clustering](#tf_var_disable_clustering)
      - [TF_VAR_vault_oss_instance_count](#tf_var_vault_oss_instance_count)
      - [TF_VAR_vault_custom_instance_count](#tf_var_vault_custom_instance_count)
      - [TF_VAR_vault_custom_config_template](#tf_var_vault_custom_config_template)
      - [TF_VAR_use_consul_oss](#tf_var_use_consul_oss)
      - [TF_VAR_consul_recursor_1](#tf_var_consul_recursor_1)
      - [TF_VAR_consul_recursor_2](#tf_var_consul_recursor_2)
      - [TF_VAR_consul_acl_datacenter](#tf_var_consul_acl_datacenter)
      - [TF_VAR_consul_data_dir](#tf_var_consul_data_dir)
      - [TF_VAR_consul_oss](#tf_var_consul_oss)
      - [TF_VAR_consul_oss_instance_count](#tf_var_consul_oss_instance_count)
      - [TF_VAR_consul_oss](#tf_var_consul_oss-1)
      - [TF_VAR_consul_custom_instance_count](#tf_var_consul_custom_instance_count)
    + [Published Ports](#published-ports)
    + [Changing Vault OSS and Consul OSS Versions](#changing-vault-oss-and-consul-oss-versions)
    + [Consul DNS](#consul-dns)
    + [Security Configuration?](#security-configuration)
      - [Docker Container / OS](#docker-container--os)
      - [Consul ACLs by Default](#consul-acls-by-default)
      - [TLS by Default](#tls-by-default)
        * [Vault PKI Secrets Engine Based TLS Configuration](#vault-pki-secrets-engine-based-tls-configuration)
    + [Where's My Vault Data?](#wheres-my-vault-data)
    + [What About Logs?](#what-about-logs)
    + [Telemetry Notes](#telemetry-notes)
    + [A Note About Custom Binaries](#a-note-about-custom-binaries)
  * [Basic Troubleshooting Questions](#basic-troubleshooting-questions)
    + [I can access the Consul UI but it states that there are no services to show](#i-can-access-the-consul-ui-but-it-states-that-there-are-no-services-to-show)
    + [Vaultron Does Not Form — Halp!](#vaultron-does-not-form--halp)
    + [Unknown token: 208:30 IDENT var.grafana_version](#unknown-token-20830-ident-vargrafana_version)
    + [Vault is Orange/Failing in the Consul Web UI](#vault-is-orangefailing-in-the-consul-web-ui)
    + [Vault Containers with Custom Binary are Exiting](#vault-containers-with-custom-binary-are-exiting)
    + [NET::ERR_CERT_AUTHORITY_INVALID or Other TLS Errors When it Was Working?!](#neterr_cert_authority_invalid-or-other-tls-errors-when-it-was-working)
    + [Vaultron cannot form; there are Vaultron containers currently stopped or running](#vaultron-cannot-form-there-are-vaultron-containers-currently-stopped-or-running)
    + [Something, Something — Storage HA Problem!](#something-something--storage-ha-problem)
    + [Unsupported Versions?](#unsupported-versions)
    + [syntax error: unexpected end of file (expecting ")")](#syntax-error-unexpected-end-of-file-expecting-)
    + [Some Other Undefined Problem!](#some-other-undefined-problem)
  * [Resources](#resources)
  * [Who?](#who)
  * [Special Thanks](#special-thanks)

## What?

**Vaultron** uses [Terraform](https://www.terraform.io/) (version 0.12.0+ required) to build a tiny cluster of [Consul](https://www.consul.io/)-backed and highly-available [Vault](https://www.vaultproject.io/) servers for development, evaluation, and issue reproduction on [Docker](https://www.docker.com/).

> **NOTE**: While every effort is made to document Vaultron here in this file, you should **always consult the [official Vault documentation](https://www.vaultproject.io/docs/)** and **[Learn resources](https://learn.hashicorp.com/vault/) for the latest and complete documentation on using Vault itself**.

## Why?

It's a reasonably useful Vault & Consul environment deployed on your macOS or Linux computer _in a about 1 minute_.

Some of the more popular uses of Vaultron are:

- Getting acquainted with Vault
- Evaluating specific Vault features
- Issue reproduction and troubleshooting
- Testing
- ‼️ **NOT FOR PRODUCTION** ‼️

## How?

Terraform assembles individual pieces to form Vaultron from the official [Vault Docker image](https://hub.docker.com/_/vault/) and [Consul Docker image](https://hub.docker.com/_/consul/).

### Prerequisites

> **NOTE macOS Catalina users see this issue**: https://github.com/hashicorp/terraform/issues/23033#issuecomment-542302933

> **NOTE**: **Vaultron only supports Terraform version 0.12.0 and beyond** and is incompatible with previous Terraform versions.

Install the following on the system where you will form Vaultron:

- [Docker CE for Linux](https://docs.docker.com/v17.12/install/#server) **or**
- [Docker Desktop for macOS](https://www.docker.com/products/docker-desktop)
  - Tested with version 2.1.0.4
  - Engine version: 19.03.4
- [Consul](https://www.consul.io/)
  - [OSS consul binaries](https://releases.hashicorp.com/consul/1.5.2/)
- [Terraform](https://www.terraform.io/) (version 0.12.0+ required)
  - [OSS terraform binaries](https://releases.hashicorp.com/terraform/0.12.3/)
- [Vault](https://www.vaultproject.io/)
  - [OSS vault binaries](https://releases.hashicorp.com/vault/1.1.3/)

### Quickest Start for macOS

Once you have the prerequisites installed, you can use the following example to form Vaultron and open the the Vault web UI in your browser on macOS.

You will likely be prompted for your password to add the Vaultron CA certificate to the System Keychain. This will prevent TLS errors about an untrusted CA certificate when using the Consul and Vault web UIs:

```
git clone https://github.com/brianshumate/vaultron.git && \
  cd vaultron && \
  ./form && \
  . ./ion_darts && \
  ./blazing_sword && \
  sudo security add-trusted-cert -d -r trustAsRoot \
  -k /Library/Keychains/System.keychain ./etc/tls/ca.pem && \
  open https://localhost:8200
```

> **NOTE**: The `blazing_sword` script persists unseal keys and initial root authentication token to a file in the `vault` folder named like `./vault/vault_1500766014.tmp`. If this behavior makes you feel some type of way, you are welcome at any time to put Vaultron down and pick up another toy project instead.

### Quick Start for Linux or macOS

Vaultron uses the latest Consul and Vault versions by default, so make sure that you have also installed the latest binaries for [Consul](https://releases.hashicorp.com/consul/), [Vault](https://releases.hashicorp.com/vault/), and [Terraform](https://releases.hashicorp.com/terraform/) locally, and that you have have [Docker](https://docs.docker.com/install/) installed as well.

After doing so, it takes just 3 steps to form Vaultron:

1. `git clone https://github.com/brianshumate/vaultron.git`
2. `cd vaultron`
3. `./form`

When Vaultron is successfully formed, the output looks like this:

```
[vaultron] [?] vaultron-network not present; creating ...
[vaultron] [+] Created attachable vaultron-network with subnet 10.10.42.0/24
[vaultron] [=] Form Vaultron! ...
[vaultron] [i] Terraform has been successfully initialized!
[vaultron] [?] Vault OSS version: 1.2.3
[vaultron] [i] Consul OSS version: 1.6.1
[vaultron] [i] Terraform plan: 14 to add, 0 to change, 0 to destroy.
[vaultron] [i] Terraform apply complete! resources: 18 added, 0 changed, 0 destroyed.
[vaultron] [+] Vaultron formed!

You can now visit the Vault web UI at https://localhost:8200

or visit the Consul web UI at https://localhost:8500

You can also interact with vault and consul CLI utilities after
exporting the following environment variables in your shell:

export CONSUL_HTTP_ADDR="127.0.0.1:8500"
export CONSUL_HTTP_SSL=true
export VAULT_ADDR="https://127.0.0.1:8200"
export CONSUL_HTTP_TOKEN="b4c0ffee-3b77-04af-36d6-738b697872e6"

or use this command to do it for you:

. ./ion_darts
```

You are now nearly ready to interact with Vault and Consul using their web user interfaces, command line interfaces, or HTTP APIs.

Take a moment to verify that all of the Vaultron Docker containers are up:

```
docker ps -f name=vaultron --format "table {{.Names}}\t{{.Status}}"
```

The output should look something like this example:

```
NAMES               STATUS
vaultron-vault0     Up 8 minutes (unhealthy)
vaultron-vault2     Up 8 minutes (unhealthy)
vaultron-vault1     Up 8 minutes (unhealthy)
vaultron-consulc2   Up 8 minutes (healthy)
vaultron-consulc1   Up 8 minutes (healthy)
vaultron-consulc0   Up 8 minutes (healthy)
vaultron-consuls0   Up 8 minutes (healthy)
vaultron-consuls2   Up 8 minutes (healthy)
vaultron-consuls1   Up 8 minutes (healthy)
```

Note that the Vault containers are `(unhealthy)` because they are not yet initialized and unsealed so that's not really an issue at this time.

There is also a message from the `form` script about setting important environment variables before executing the `vault` and `consul` CLI commands. You'll want these environment variables in your shell before trying to use the `consul` or `vault` CLI tools with Vaultron:

```
export CONSUL_CACERT="$(pwd)/red_lion/tls/ca.pem" \
       CONSUL_HTTP_ADDR="127.0.0.1:8500" \
       CONSUL_HTTP_SSL=true \
       CONSUL_HTTP_TOKEN="b4c0ffee-3b77-04af-36d6-738b697872e6" \
       VAULT_ADDR="https://127.0.0.1:8200" \
       VAULT_CA_CERT="$(pwd)/black_lion/tls/ca.pem"
```

You can instead source the `ion_darts` script to do all of this for you:

```
. ./ion_darts
```

which should output details like this:

```
[vaultron] [+] Exported Vaultron environment variables:
[vaultron] [+] CONSUL_HTTP_ADDR: 127.0.0.1:8500
[vaultron] [+] VAULT_ADDR: https://127.0.0.1:8200
```

> **NOTE**: Before accessing the Vault or Consul web UIs you should add the Vaultron Certificate Authority (CA) certificate to your OS trust store. It is located under the root of this project at `etc/tls/ca.pem`.

See the **TLS by Default** section for more details on handling the Vaultron Certificate Authority certificate.

### What's Next?

If you are new to Vault, then using Vaultron is a nice way to quickly get acquainted! Please begin by checking out the official [Vault Getting Started Guide](https://learn.hashicorp.com/vault/?track=getting-started#getting-started).

#### Ten Things You Can do After Vaultron is Formed

1. Initialize Vault with `vault operator init`
2. Unseal Vault with `vault operator unseal` using 3 of the 5 unseal keys presented when you initialized Vault
3. Authenticate to Vault with the initial root token presented during initialization
4. After initializing and unsealing your vault, and then authenticating with the root token, you can follow along with the [Your First Secret](https://learn.hashicorp.com/vault/getting-started/first-secret) page
5. Use your local `consul` and `vault` binaries in CLI mode to interact with Vault servers
6. Use the Vault web UI at [https://localhost:8200](https://localhost:8200)
7. Use the Consul web UI at [https://localhost:8500](https://localhost:8500)
8. Use the [Vault HTTP API](https://www.vaultproject.io/api/index.html)
9. Check out and experiment with the examples in the `examples` folders
10. Clean up or reset: disassemble Vaultron and clean up Vault data with `unform`

> **NOTE: The `unform` script attempts to remove most data generated while using Vaultron, including the existing Vault data, logs, and Terraform state — be careful!**

The Docker private network is not removed for reasons detailed elsewhere in this documentation.

The Terraform provider modules _are also not removed_ to save on resources and time involved in re-downloading them.

If you want to tear down the containers, but preserve data, logs, and state, you can use `terraform destroy` for that instead:

```
terraform destroy -state=./tfstate/terraform.tfstate
```

If you are already familiar with Vault, but would like to save time by rapidly initializing, unsealing, and enabling a wide range of authentication and secret backends, execute the `./blazing_sword` script to do all of this for you. `blazing_sword` uses the additional Terraform configuration in `blazing_sword/main.tf`.

If you are familiar with Terraform you can also use Terraform commands instead, but you'll need to manually specify the `CONSUL_HTTP_ADDR` and `VAULT_ADDR` environment variables before you can access either the Consul or Vault instances:

```
export CONSUL_HTTP_ADDR="127.0.0.1:8500" \
       CONSUL_HTTP_SSL=true \
       VAULT_ADDR="https://127.0.0.1:8200" \
       CONSUL_HTTP_TOKEN="b4c0ffee-3b77-04af-36d6-738b697872e6"
```

### Advanced Example

The following is a more advanced example of forming Vaultron; it uses a range of environment variables to define additional configuration and includes the statsd + Graphite + Grafana telemetry stack container to visualize Vault telemetry.

```
export TF_VAR_consul_custom=0 \
       TF_VAR_vault_oss_instance_count=0 \
       TF_VAR_vault_custom_instance_count=3 \
       TF_VAR_vaultron_telemetry_count=1 \
       TF_VAR_vault_server_log_level=trace \
       TF_VAR_vault_log_format=json \
       TF_VAR_consul_log_level=err
```

What this does line by line:

- Enable zero custom Consul instances (custom Consul binary feature not available yet)
- Enable 3 custom binary based Vault instances which use the binary you place into the `custom` folder
- Enable the statsd/Graphite/Grafana telemetry container
- Set Vault log level to _trace_
- Set Vault log formate to _json_
- Set Consul log level to _err_

## What's in the Box?

Whimsical Vaultron technical specification quick reference card:

```
Name:          Vaultron
Type:          Secret Management Unit (defaults to latest Vault software)
Builder:       Terraform
Blueprints:    vaultron.tf
Modules:       black_lion, red_lion, yellow_lion
Datacenter:    arus
Infra-cell:    Distributed storage cell (defaults to latest Consul software)
Universe:      Docker
Telemetry:     statsd, Graphite, Grafana (optional)
HashiStack:    ★★★
Agility:       ★★★★
Damage:        ★★
Mass:          ★★
Speed:         ★★★★★
```

Here are some slightly more serious notes and questions about what Vaultron is and how it can work for you.

### Basic Architecture Overview

Vaultron is only currently tested to function on Linux and macOS, but here is basically what you are getting by default:

```
+------------+--------------------------------------------------+------------+
|            |              Yellow Lion (optional)              |            |
| __     __  |    +-----------------------------------------+   |            |
| \ \   / /  |    |                                         |   |            |
|  \ \ / /   |    |            Grafana Dashboard            |   |            |
|   \ V /    |    +-----------------------------------------+   |            |
|    \_/     |    |                                         |   |            |
|            |    |            statsd / Graphite            |   |            |
|            |    +--^-----------------^------------------^-+   |            |
|            +--------------------------------------------------+            |
|                    |                 |                  |                  |
+----------------------------------------------------------------------------+
|  Black Lion        |                 |                  |                  |
|  +-----------------+--+   +----------+---------+   +----+---------------+  |
|  |                    |   |                    |   |                    |  |
|  |  Vault (active)    |   |  Vault (standby)   |   |  Vault (standby)   |  |
|  |                    <---+                    |   |                    |  |
|  |  vaultron-vault0   |   |  vaultron-vault1   |   |  vaultron-vault2   |  |
|  |                    |   |                    |   |                    |  |
|  |                    <----------------------------+                    |  |
|  +---------------+--^-+   +--------------^--+--+   +---------------+--^-+  |
|                  |  |                    |  |                      |  |    |
+----------------------------------------------------------------------------+
|  Red Lion        |  |                    |  |                      |  |    |
|                  |  |                    |  |                      |  |    |
|  +---------------v--+-+   +--------------+--v--+   +---------------v--+-+  |
|  | Consul client      |   | Consul client      |   | Consul client      |  |
|  |                    |   |                    |   |                    |  |
|  | vaultron-consulc0  |   | vaultron-consulc1  |   | vaultron-consulc2  |  |
|  +-------^--+---------+   +-------^--+---------+   +-------^--+---------+  |
|          |  |                     |  |                     |  |            |
|          |  |                     |  |                     |  |            |
|  +-------+--v---------+   +-------+--v---------+   +-------+--v---------+  |
|  | Consul server      +---> Consul server      <---+ Consul server      |  |
|  |                    |   |                    |   |                    |  |
|  | vaultron-consuls0  <---+ vaultron-consuls1  +---> vaultron-consuls2  |  |
|  +--------------------+   +--------------------+   +--------------------+  |
+----------------------------------------------------------------------------+
```

Vaultron consists of 3 Vault server containers, 3 Consul client containers, and 3 Consul server containers which run in a Docker private network called _vaultron-network_:

#### Vault Servers

- `vaultron-vault0`
  - Docker private network IP: 10.10.42.200
- `vaultron-vault1`
  - Docker private network IP: 10.10.42.201
- `vaultron-vault2`
  - Docker private network IP: 10.10.42.202

#### Consul Servers

- `vaultron-consuls0`
  - Docker private network IP: 10.10.42.100
- `vaultron-consuls1`
  - Docker private network IP: 10.10.42.101
- `vaultron-consuls2`
  - Docker private network IP: 10.10.42.102

#### Consul Clients
- `vaultron-consulc0`
  - Docker private network IP: 10.10.42.40
- `vaultron-consulc1`
  - Docker private network IP: 10.10.42.41
- `vaultron-consulc2`
  - Docker private network IP: 10.10.42.42

> NOTE: The `form` script creates the attachable Docker private network _vaultron-network_ with a subnet of 10.10.42.0/24 if it is not found to already be present. It is not removed by `unform` however, as other containers that are not part of Vaultron could be using it even when Vaultron's containers are stopped or removed.

An optional telemetry gathering and graphing stack (Yellow Lion) can be enabled at runtime via environment variable; see the **Telemetry Notes** section for more details. It uses the following IPs:

#### statsd
- `vaultron-vstatsd`
  - Docker private network IP: 10.10.42.219

#### Grafana
- `vaultron-vgrafana`
  - Docker private network IP: 10.10.42.220

Vault servers connect directly to the Consul clients, which in turn connect to the Consul server cluster. In this configuration, Vault is using Consul for both storage and high availability functionality.

This is unfortunately both simultaneously simpler and more complex, since in most production deployments, you want the Consul client agent on the Vault server, so this is more a "sidecar style" approach used out of ease of deployment at the cost of 3 extra containers and more complexity.

### Environment Variables

Vaultron uses environment variables to override some Terraform configuration items. You can use these to fine-tune the attributes of your own particular Vaultron.

Here are the names and purposes of each:

#### TF_VAR_vault_version

Vault OSS version to use

> NOTE: Setting this has no effect when the value of `TF_VAR_vault_custom_instance_count` is greater than zero as the custom binary itself then determines the version used.

- Default: latest OSS version

#### TF_VAR_consul_version

Consul OSS version to use; currently Vaultron can use _only_ Consul OSS versions.

- Default: latest OSS version

#### TF_VAR_datacenter_name

Vault datacenter name

- Default: `arus`

#### TF_VAR_use_vault_oss

`1` to use OSS Vault binaries from releases.hashicorp.com or `0` when using custom binaries

- Default: `1`

#### TF_VAR_vault_server_log_format (Vault v0.10.0+)

A valid Vault server log format: _standard_ or _json_

- Default: `standard`

#### TF_VAR_vault_server_log_level

Server operational log level: _trace_, _debug_, _info_, _warning_, or _error_

- Default: `debug`

#### TF_VAR_consul_log_level

A valid Consul log level: _trace_, _debug_, _info_, _warn_, or _err_

- Default: `debug`

#### TF_VAR_vault_path

Set `path` value for storage stanza

- Default: `vault`

#### TF_VAR_vault_cluster_name

Cluster name

- Default: `vaultron`

#### TF_VAR_disable_clustering

Disable Consul HA clustering

- Default `false`

#### TF_VAR_vault_oss_instance_count

Number of Vault OSS containers

- Default: `3`

> NOTE: You must also set `TF_VAR_vault_custom_instance_count=0` when setting this.

#### TF_VAR_vault_custom_instance_count

Number of Vault custom containers

- Default: `0`

> NOTE: You must also set `TF_VAR_vault_oss_instance_count=0` when setting this.

#### TF_VAR_vault_custom_config_template

Specify an alternative configuration file template in `black_lion/templates/custom`

- Default: `vault_config_custom.hcl`

#### TF_VAR_use_consul_oss

This cannot currently be changed as Vaultron always uses Consul OSS.

- Default: `1`

#### TF_VAR_consul_recursor_1

DNS recursor 1

-Default: `1.1.1.1`

#### TF_VAR_consul_recursor_2

DNS recursor 2

- Default: `1.0.0.1`

#### TF_VAR_consul_acl_datacenter

Consul datacenter name

- Default: `arus`

#### TF_VAR_consul_data_dir

Consul data directory path

- Default: `/consul/data`

#### TF_VAR_consul_oss

`1` to use OSS Vault binaries from releases.hashicorp.com (currently only option)

- Default: `1`

#### TF_VAR_consul_oss_instance_count

Number of Consul OSS containers

- Default: `3`

#### TF_VAR_consul_oss

(NOT YET IMPLEMENTED): Whether to use Consul OSS

- Default: `0`

#### TF_VAR_consul_custom_instance_count

(NOT YET IMPLEMENTED): Consul custom instance count

- Default: `0`

### Published Ports

Each Vault instance is available to the local computer, but through Docker's published ports scheme only, so the API addresses of the Vault servers are:

- `https://localhost:8200`
- `https://localhost:8210`
- `https://localhost:8220`

The cluster port (for the Active instance only) is also forwarded to localhost at `https://localhost:8201`

### Changing Vault OSS and Consul OSS Versions

Vaultron runs the `:latest` official Vault Docker container image, but if you would prefer to run a different _OSS version_, you can export the `TF_VAR_vault_version` environment variable to override:

```
TF_VAR_vault_version=0.6.5 ./form
```

the output of which would then contain:

```
...
[vaultron] [i] Vault OSS version: 0.6.5
...
```

Similarly, to run a different version of the Consul container, set the `TF_VAR_consul_version` environment variable like this:

```
TF_VAR_consul_version=0.7.5 ./form
```

After Vaultron forms, check the Consul version with `consul members`:

```
Node      Address          Status  Type    Build  Protocol  DC    Segment
consuls0  172.17.0.2:8301  alive   server  0.7.5  2         arus  <all>
consuls1  172.17.0.3:8301  alive   server  0.7.5  2         arus  <all>
consuls2  172.17.0.4:8301  alive   server  0.7.5  2         arus  <all>
vault0    172.17.0.5:8301  alive   client  0.7.5  2         arus  <default>
vault1    172.17.0.7:8301  alive   client  0.7.5  2         arus  <default>
vault2    172.17.0.6:8301  alive   client  0.7.5  2         arus  <default>
```

> **NOTE**: Be sure to always use the same versions of Consul and Vault binaries on your host system and the container image.

This changes the OSS version only; when using a custom Vault binary, the binary itself determines the version; see the **A note about custom Binaries** section for more details about using custom binaries.

Also note that if the OSS version of Consul or Vault you want to use does not have an official Docker image available, you'll encounter an error.

### Consul DNS

The 3 Consul servers have DNS exposed to port 53 of their internal container addresses, and the Consul clients and Vault sever containers are configured to use those Consul servers for DNS as well.

Additionally Consul DNS API is also published from the first Consul server at `localhost:8600`, so you can query services and nodes using DNS like so:

```
dig -p 8600 @localhost consul.service.consul
```

which results in a response containing:

```
...
;; ANSWER SECTION:
consul.service.consul.  0 IN  A 10.10.42.102
consul.service.consul.  0 IN  A 10.10.42.100
consul.service.consul.  0 IN  A 10.10.42.101

;; ADDITIONAL SECTION:
consul.service.consul.  0 IN  TXT "consul-network-segment="
consul.service.consul.  0 IN  TXT "consul-network-segment="
consul.service.consul.  0 IN  TXT "consul-network-segment="
...
```

or

```
dig -p 8600 @localhost active.vault.service.consul
```

which results in a response containing:

```
...
;; ANSWER SECTION:
active.vault.service.consul. 0  IN  A 10.10.42.200

;; ADDITIONAL SECTION:
active.vault.service.consul. 0  IN  TXT "consul-network-segment="
...
```

or

```
dig -p 8600 @localhost vault.service.consul SRV
```

which results in a response containing:

```
...
;; ANSWER SECTION:
vault.service.consul. 0 IN  SRV 1 1 8200 0a0a2ac9.addr.arus.consul.
vault.service.consul. 0 IN  SRV 1 1 8200 0a0a2aca.addr.arus.consul.
vault.service.consul. 0 IN  SRV 1 1 8200 0a0a2ac8.addr.arus.consul.

;; ADDITIONAL SECTION:
0a0a2ac9.addr.arus.consul. 0  IN  A 10.10.42.201
consulc1.node.arus.consul. 0  IN  TXT "consul-network-segment="
0a0a2aca.addr.arus.consul. 0  IN  A 10.10.42.202
consulc2.node.arus.consul. 0  IN  TXT "consul-network-segment="
0a0a2ac8.addr.arus.consul. 0  IN  A 10.10.42.200
consulc0.node.arus.consul. 0  IN  TXT "consul-network-segment="
...
```

or

```
$ dig -p 8600 @localhost consuls0.node.consul
...
;; ANSWER SECTION:
consuls0.node.consul. 0 IN  A 10.10.42.100

;; ADDITIONAL SECTION:
consuls0.node.consul. 0 IN  TXT "consul-network-segment="
...
```

### Security Configuration?

Given the intended use cases for this project, the working solution that results when Vaultron is formed is essentially a blank canvas that emphasizes immediate unhindered usability over security.

#### Docker Container / OS

To better facilitate requirements, advanced troubleshooting, and debugging, the following capabilities are added:

- Vault containers:
  - `IPC_LOCK`
  - `NET_ADMIN`
  - `SYS_ADMIN`
  - `SYS_PTRACE`
  - `SYSLOG`
- Consul containers:
  - `NET_ADMIN`
  - `SYS_ADMIN`
  - `SYS_PTRACE`
  - `SYSLOG`

#### Consul ACLs by Default

> **Consul ACLs with a **default allow policy** are enabled for Vaultron v1.8.0 (using Vault v0.9.5/Consul v1.0.6) and beyond**

This was chosen to allow for ease of experimentation with ACL policies and the Vault Consul Secrets Engine. It is not the same as a production installation because it makes use of a shared **acl_master_token** for ease of configuration.

The value used for the shared ACL Master Token is:

- `b4c0ffee-3b77-04af-36d6-738b697872e6`

#### TLS by Default

Vaultron uses self-signed certificates for full mutual TLS communication between Vault servers and Consul agents. The certificates and keys were generated from Vault PKI Secrets Backends as described in [examples/tls/README.md](https://github.com/brianshumate/vaultron/blob/master/examples/tls/README.md).

With this in mind, you need to ensure that the certificate authority is recognized by Vault and Consul; you can do this in a number of ways:

1. Import the `black_lion/tls/ca-pundle.pem` into your OS trust store
2. For Vault: use the `VAULT_CACERT` environment variable
3. For Vault: Pass `-ca-cert=` option with path to the `ca-pundle.pem` for all `vault` commands
4. For Consul: use the `CONSUL_CACERT` environment variable
5. For Consul: pass the `-ca-file=` option with path to the `ca-pundle.pem` for all `consul` commands

Here are some additional resources related to configuring ACLs and TLS:

- [Consul ACL System guide](https://www.consul.io/docs/guides/acl.html)
- [Consul Encryption documentation](https://www.consul.io/docs/agent/encryption.html)
- [Vault TCP Listener documentation](https://www.vaultproject.io/docs/configuration/listener/tcp.html)

##### Vault PKI Secrets Engine Based TLS Configuration

All of the TLS certificates and keys used by Vaultron were created by Vaultron itself as documented in [examples/tls/README.md](https://github.com/brianshumate/vaultron/blob/master/examples/tls/README.md).

There are also some additional certificate/key pairs already generated for use in configuring TLS for other containers often used with Vaultron, and specifically including the following:

- Grafana
- LDAP
- MySQL
- MongoDB
- PostgreSQL
- Prometheus

You can even import the PKI Secrets Engines for the Root and Intermediate CAs and generate more roles, certificates, etc. as described in [examples/tls/README-IMPORT.md](https://github.com/brianshumate/vaultron/blob/master/examples/tls/README-IMPORT.md).

There's also an `examples/tls/eybeams_tls` script that will do this import for you.

### Where's My Vault Data?

Vault data are stored in Consul's key/value store, which in turn is written into the `consul/oss_server_*/data` directories for each of the three Consul servers.

Here is a tree showing the folder structure for a Consul server:

```
└── consul
    └── consuls0
        ├── config
        │   └── extra_config.hcl
        └── data
            ├── checkpoint-signature
            ├── node-id
            ├── raft
            │   ├── peers.info
            │   ├── raft.db
            │   └── snapshots
            └── serf
                ├── local.snapshot
                └── remote.snapshot
```

### What About Logs?

The Docker containers are named as shown in the [Basic Architecture Overview](#basic-architecture-overview).

You can view operational logs for any container with `docker logs` like so:

```
docker logs vaultron-vault0
```

The Vault audit logs for any given _active server_ are available as:

- `./vault/vault0/audit_log/audit.log`
- `./vault/vault1/audit_log/audit.log`
- `./vault/vault2/audit_log/audit.log`

### Telemetry Notes

Vaultron includes a comprehensive telemetry gathering and graphing stack provided by the **Yellow Lion** module. This module is optional and enabled by an environment variable value.

It provides statsd, Graphite, and Grafana from the addition of two official Grafana container images.

You can enable Yellow Lion by setting the value of the *TF_VAR_vaultron_telemetry_count* environment variable to **1**:

```
$ export TF_VAR_vaultron_telemetry_count=1
```

prior to the execution of `form`.

You can then access Grafana at: https://127.0.0.1:3000/ After Vaultron is formed and login with the following credentials:

- username: `admin`
- password: `vaultron`

Once signed in, you can access the example **Vault** dashboard; you'll need to initialize, unseal, and do some work with Vault before metrics begin to appear. Adjusting the time filtering in the Grafana UI to a more recent span can also help.

See the [Visualizing Vault Telemetry](https://github.com/brianshumate/vaultron/blob/master/examples/telemetry/README.md) documentation for more details on this setup.

### A Note About Custom Binaries

Vaultron installs the official open source Vault binaries through the official Docker container images, but if you'd prefer to use recent source builds or some other Vault binary, just drop `vault` into `custom/` and set these environment variables prior to forming Vaultron:

```
$ export TF_VAR_vault_oss_instance_count=0 \
       TF_VAR_vault_custom_instance_count=3 \
./form
```

> **NOTE**: When using custom binaries in this way the binary must be for Linux/AMD64 as that is the platform for the containers, also Vaultron ignores the value of `TF_VAR_vault_version` since the binary itself determines the version so keep that in mind as well.
>
> All OSS containers do execute _vault_ as the _vault_ user as designed, but one major disadvantage of the current custom binary scheme is that it chooses a simplest approach to introducing the `vault` binary by changing the path from which `vault` is executed. This breaks the preferred dedicated user model such that the process is executed by the _root_ user instead of the _vault_ user, so please keep this in mind when using custom binaries.

## Basic Troubleshooting Questions

### I can access the Consul UI but it states that there are no services to show

Access **Settings** in the navigation and ensure that the ACL master token is present in the text field, then click **Save** or **Close** depending on Consul version.

### Vaultron Does Not Form — Halp!

Instead of seeing the glorious interlocks activated, dyna-therms connected, infra-cells up, and mega-thrusters going, Vaultron fails to form and I get:

```
[e] Vaultron cannot form! Check terraform apply output.
```

or this:

```
[e] Vaultron cannot form! Check terraform plan output.
```

This means that Vaultron had problems during the `terraform plan` or `terraform apply` steps. You can run those commands manually and inspect their output to troubleshoot the issue.

Other red and equally frightening errors could occur, and these are usually accompanied by an explanation from Terraform regarding the nature of the problem.

### Unknown token: 208:30 IDENT var.grafana_version

If you encounter an error forming Vaultron like the following:

```
Error: Error parsing <path>/vaultron.tf: At 208:30: Unknown token: 208:30 IDENT var.grafana_version
```

where `<path>` is your Vaultron project path, then you are likely attempting to form Vaultron on a Terraform version < v0.12.0; note that the [Prerequisites](#Prerequisites) section mentions the requirement of Terraform v0.12.0+.

You can confirm your current version with `terraform version` and proceed accordingly.

### Vault is Orange/Failing in the Consul Web UI

Vault is expected to appear as failing in the Consul UI if you have not yet unsealed it.

Unsealing Vault should solve that for you!


### Vault Containers with Custom Binary are Exiting

My Vault containers are exiting and the `docker logs vaultron-vault0` output resembles this:

```
Using eth0 for VAULT_REDIRECT_ADDR: http://172.17.0.10:8200
Using eth0 for VAULT_CLUSTER_ADDR: https://172.17.0.10:8201
/vault/custom/vault: line 3: syntax error: unexpected end of file (expecting “)”)
```

This is a symptom of using the incorrect custom binary platform; the containers Vaultron uses are Linux AMD64 based, so you must place a Linux/AMD64 version of the `vault` binary into the `custom` directory to successfully use custom binaries.

### NET::ERR_CERT_AUTHORITY_INVALID or Other TLS Errors When it Was Working?!

If you encounter TLS related errors when Vaultron previously worked for you, there's a possibility that the TLS certificates were updated since you last installed the CA certificate for Vaultron.

Try removing the previous CA certificate (which will appear as "node.arus.consul") and reinstalling the current CA certificate from `etc/tls/ca.pem`.

### Vaultron cannot form; there are Vaultron containers currently stopped or running

Vaultron does not allow `form` to be used when there are already existing Vaultron Docker containers stopped or running. You can encounter an error like the following:

```
[vaultron] [!] Vaultron cannot form; there are Vaultron containers currently stopped or running
[vaultron] [i] Please unform existing Vaultron or use docker stop and docker rm to manually
[vaultron] [i] clean up the vaultron- containers shown here:

NAMES               STATUS
vaultron-vault2     Up About a minute (healthy)
vaultron-vault1     Up About a minute (healthy)
vaultron-vault0     Up About a minute (healthy)
vaultron-consulc0   Up About a minute (healthy)
vaultron-consulc1   Up About a minute (healthy)
vaultron-consulc2   Up About a minute (healthy)
vaultron-consuls1   Up About a minute (healthy)
vaultron-consuls0   Up About a minute (healthy)
vaultron-consuls2   Up About a minute (healthy)
```

If this occurs, be sure that you are not trying to `form` Vaultron while it is already up and running (hint check the output of `docker ps -a | grep vaultron`).

If `unform` fails to clean up the containers, you will need to use `docker stop` and `docker rm` to stop and remove the containers:

```
$ for i in {0..2}; do docker stop "vaultron-vault${i}" \
  && docker rm "vaultron-vault${i}"; \
  done
vaultron-vault0
vaultron-vault0
vaultron-vault1
vaultron-vault1
vaultron-vault2
vaultron-vault2
```

```
$ for i in {0..2}; do docker stop "vaultron-consuls${i}" \
  && docker rm "vaultron-consuls${i}"; \
  done
vaultron-consuls0
vaultron-consuls0
vaultron-consuls1
vaultron-consuls1
vaultron-consuls2
vaultron-consuls2
```

```
$ for i in {0..2}; do docker stop "vaultron-consulc${i}" \
  && docker rm "vaultron-consulc${i}"; \
  done
vaultron-consulc0
vaultron-consulc0
vaultron-consulc1
vaultron-consulc1
vaultron-consulc2
vaultron-consulc2
```

### Something, Something — Storage HA Problem!

High Availability mode has been shown to work as expected, however because of the current published ports method for exposing the Vault servers, you must be sure to point your client to the correct Vault server with `VAULT_ADDR` once that server becomes the new active server.

Here is simple method to watch HA mode in action using two terminal sessions:

```
Terminal 1                              Terminal 2
+-----------------------------------+   +------------------------------------+
| VAULT_ADDR=https://localhost:8210\|   | docker stop vaultron-vault0        |
| watch -n 1 vault status           |   |                                    |
|                                   |   |                                    |
| ...                               |   |                                    |
| HA Enabled             true       |   |                                    |
| HA Cluster             https://...|   |                                    |
| HA Mode                standby    |   |                                    |
| ...                               |   |                                    |
|                                   |   |                                    |
|                                   |   |                                    |
+-----------------------------------+   +------------------------------------+
```

1. In Terminal 1, set `VAULT_ADDR` to one of the two Vault standby containers and use `watch` to keep an eye on the output of `vault status` while noting the values of `Mode:` and `Leader:`
2. In Terminal 2, stop the *active* Vault instance with `docker stop`
3. You should notice that the value of `Leader:` changes instantly and if the second standby Vault is elected the new active, the value of `Mode:` will also reflect that instantly as well

### Unsupported Versions?

If you try exporting `TF_VAR_consul_version` or `TF_VAR_vault_version` to a specific version, but get this error when you attempt to form Vaultron:

```
[e] Sorry, Vaultron does not support Consul version: 0.6.4
```

or:

```
[e] Sorry, Vaultron does not support Vault version: 0.6.0
```

You are specifying either a non-existent version (maybe a typo?) or you are specifying a version for which no Docker images exists. This second case is not a problem with Vaultron, there are some versions of Consul and Vault which were released as binaries, but not available as Docker images.

### syntax error: unexpected end of file (expecting ")")

If Vaultron forms, but the Vault containers all exit and you observe this error when inspecting the docker logs for the Vault containers:

```
/vault/custom/vault: line 3: syntax error: unexpected end of file (expecting ")")
```

This is likely because you have placed a macOS version of the `vault` binary in the `custom` directory instead of a Linux binary.

This can be confirmed with the `file` command:

```
$ file custom/vault
custom/vault: Mach-O 64-bit executable x86_64
```

If this is the case, please replace the `vault` binary with the Linux AMD64 version.

### server gave HTTP response to HTTPS client

If you encounter an error like this when attempting to run `blazing_sword`:

```
Error initializing: Put https://127.0.0.1:8200/v1/sys/init: http: server gave HTTP response to HTTPS client
[vaultron] [!] Cannot initialize Vault!
[vaultron] [!]
```

Ensure that you do not have another `vault` process which was already listening at 127.0.0.1:8220 when you formed Vaultron.

Often this is caused by a `-dev` mode instance of Vault running on the Docker host.

### Error: Unable to read Docker image into resource: Unable to pull image

If you encounter an error like the following:

```
Error: Unable to read Docker image into resource: Unable to pull image vault:1.2.4: error pulling image vault:1.2.4: Error response from daemon: manifest for vault:1.2.4 not found: manifest unknown: manifest unknown
```

Then it is likely that the docker image for the version of Vault (in this example 1.2.4) is not yet published to DockerHub. You'll have to try again when the image becomes available.

### Some Other Undefined Problem!

Have you tried turning it off an on again?

No, seriously — given the nature of this project, sometimes if you cannot resolve the issue root cause, then the easiest way out of a jam could be the _old nuke it from orbit and start_ over approach.

In this case, when you're stumped and don't mind starting anew, then just `unform` and `form` Vaultron again:

```
$ ./unform
[vaultron] [=] Unform Vaultron ...
[vaultron] [*] Vaultron unformed!
```

```
$ ./form
[vaultron] [=] Form Vaultron! ...
...
```

Other things that can help include:

- Unset all related Vaultron `TF_VAR_*` environment variables
- Close terminal session/start with a fresh terminal session
- Use the latest release version from GitHub

Note that the GitHub Master branch strives to remain relatively stable, but a release is usually preferred.

## Resources

Here are some links to resources for the technologies used in this project:

1. [Vault](https://www.vaultproject.io/)
2. [Consul](https://www.consul.io/)
3. [Terraform](https://www.terraform.io/)
4. [Vault Docker Hub repository](https://hub.docker.com/_/vault/)
5. [hashicorp/docker-vault](https://github.com/hashicorp/docker-vault)
6. [Consul Docker Hub repository](https://hub.docker.com/_/consul/)
7. [hashicorp/docker-consul](https://github.com/hashicorp/docker-consul)
8. [Vault Documentation](https://www.vaultproject.io/docs/)
9. [Learn about secrets management and data protection with HashiCorp Vault](https://learn.hashicorp.com/vault/)
10. [Consul Documentation](https://www.consul.io/docs/index.html)
11. [Consul ACL System guide](https://www.consul.io/docs/guides/acl.html)
12. [Consul Encryption documentation](https://www.consul.io/docs/agent/encryption.html)
13. [Official Consul Docker Image blog post](https://www.hashicorp.com/blog/official-consul-docker-image/)
14. [Terraform CLI Documentation](https://www.terraform.io/docs/cli-index.html)
15. [Vault TCP Listener documentation](https://www.vaultproject.io/docs/configuration/listener/tcp.html)
16. [Docker](https://www.docker.com/)

## Who?

Vaultron was created by [Brian Shumate](https://github.com/brianshumate) and made possible through the generous time of the good people named in [CONTRIBUTORS.md](https://github.com/brianshumate/vaultron/blob/master/CONTRIBUTORS.md)

## Special Thanks

:robot: [Voltron Legendary Defender Theme Song Acapella](https://www.youtube.com/embed/W_yr9KvldZY)
