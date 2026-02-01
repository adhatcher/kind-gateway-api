# Kubevela

## Links

- [Kubevela docs](https://kubevela.io/docs/)
- [Kubevela Github Repo](https://github.com/kubevela/kubevela)
- [kubevela Helloworld example](https://github.com/kubevela/samples/tree/master/01.Helloworld)
- [Abstraction Example](https://github.com/kubevela/samples/blob/master/01.Helloworld/app.yaml)

![kubevela](https://github.com/kubevela/kubevela/raw/master/docs/resources/what-is-kubevela.png)

## Highlights

KubeVela practices the "render, orchestrate, deploy" workflow with below highlighted values added to existing ecosystem:

**Deployment as Code**

Declare your deployment plan as workflow, run it automatically with any CI/CD or GitOps system, extend or re-program the workflow steps with [CUE](https://cuelang.org/). No ad-hoc scripts, no dirty glue code, just deploy. The deployment workflow in KubeVela is powered by [Open Application Model](https://oam.dev/).

**Built-in observability, multi-tenancy and security support**

Choose from the wide range of LDAP integrations we provided out-of-box, enjoy enhanced [multi-tenancy and multi-cluster authorization and authentication](https://kubevela.net/docs/platform-engineers/auth/advance), pick and apply fine-grained RBAC modules and customize them as per your own supply chain requirements. All delivery process has [fully automated observability dashboards](https://kubevela.net/docs/platform-engineers/operations/observability).

**Multi-cloud/hybrid-environments app delivery as first-class citizen**

Natively supports multi-cluster/hybrid-cloud scenarios such as progressive rollout across test/staging/production environments, automatic canary, blue-green and continuous verification, rich placement strategy across clusters and clouds, along with automated cloud environments provision.

**Lightweight but highly extensible architecture**

Minimize your control plane deployment with only one pod and 0.5c1g resources to handle thousands of application delivery. Glue and orchestrate all your infrastructure capabilities as reusable modules with a highly extensible architecture and share the large growing community [addons](https://kubevela.net/docs/reference/addons/overview).

## Getting Started

- [Introduction](https://kubevela.io/docs)
- [Installation](https://kubevela.io/docs/install)
- [Deploy Your Application](https://kubevela.io/docs/quick-start/)


## Installation

```bash
brew update
brew install kubevela
vela install
vela addon enable velaux
Addon velaux enabled successfully.
Please access addon-velaux from the following endpoints:
+---------+---------------+-----------------------------------+--------------------------------+-------+
| CLUSTER |   COMPONENT   |     REF(KIND/NAMESPACE/NAME)      |            ENDPOINT            | INNER |
+---------+---------------+-----------------------------------+--------------------------------+-------+
| local   | velaux-server | Service/vela-system/velaux-server | velaux-server.vela-system:8000 | true  |
+---------+---------------+-----------------------------------+--------------------------------+-------+
    To open the dashboard directly by port-forward:

    vela port-forward -n vela-system addon-velaux 8000:8000   
```

Created httproute and enabled access through: [http://vela.local](http://vela.local)
username: adhatcher/AS02AP05

## Create 1st application

### create prod namespace

**This command will create a namespace in the local cluster**

```bash
vela env init prod --namespace prod
```

<details>
  <summary>Expected Output</summary>

```bash
environment prod with namespace prod created
```

</details>

## Deploy application

```bash
vela up -f first-app.yaml
```