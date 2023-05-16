Terraform插件Provider管理，搜索、定义、下载


Provider即插件

Provider可理解为插件，Terraform是支持多云基础设施编排的，但光terraform这个程序只是核心功能，对于不同的云平台，需要不同的Provider来支持。这样可以非常灵活的添加平台，需要AWS的部署，就添加AWS的Provider；需要Kubernetes，就添加Kubernetes的功能。

其实一个Provider就一个程序，它是一个独立的进程，terrafrom会跟Provider通信，以完成所有功能。
搜索Provider

Provider分为四种：

    Official：官方提供的；
    Verified：官方认证的；
    Community： 社区提供的；
    Custom： 自定义的；

我们可以到（ https://registry.terraform.io/browse/providers ）去搜索，这里已经提供了极其丰富的Provider，基本是够用的了。

而且每个Provider都提供了很好的文档说明，如GCP：https://registry.terraform.io/providers/hashicorp/google/latest/docs

˛
定义Provider

我们可以定义需要用到哪些Provider和对应的版本，新建一个versions.tf文件，内容如下：

terraform {
  required_version = "= v0.15.4"

  required_providers {
    kubernetes = {
          source  = "hashicorp/kubernetes"
          version = "= 2.2.0"
        }
    docker = {
          source = "kreuzwerker/docker"
          version = "= 2.12.2"
        }
  }
}



版本号可以用=或者>=等，灵活方便。
下载Provider

当我们定义好了Provider和对应的版本号后，就可以通过terraform init命令下载了。如下：

$ terraform init
Initializing provider plugins...
- Finding hashicorp/kubernetes versions matching "2.2.0"...
- Finding kreuzwerker/docker versions matching "2.12.2"...
- Installing kreuzwerker/docker v2.12.2...
- Installed kreuzwerker/docker v2.12.2 (self-signed, key ID 24E54F214569A8A5)
- Installing hashicorp/kubernetes v2.2.0...
- Installed hashicorp/kubernetes v2.2.0 (signed by HashiCorp)



这里有两个问题需要解决：

（1）它从哪里下载？

（2）它下载到什么地方去了？

对于Provider的定义有一个source值，格式如下：

[<HOSTNAME>]<NAMESPACE>/<TYPE>

HostName是选填的，默认是官方的 registry.terraform.io，所以它是从这个地址去下载的，当然也可以自建Terraform仓库，特别是许多大公司，不会直接连外网。

那它会下载到哪里呢？以版本Terraform v0.15.4 on darwin_amd64为例，它会下载在项目当前目录下：

$ tree -a
.
├── .terraform
│   ├── modules
│   │   └── modules.json
│   └── providers
│       └── registry.terraform.io
│           ├── hashicorp
│           │   └── kubernetes
│           │       └── 2.2.0
│           │           └── darwin_amd64
│           │               └── terraform-provider-kubernetes_v2.2.0_x5
│           └── kreuzwerker
│               └── docker
│                   └── 2.12.2
│                       └── darwin_amd64
│                           ├── CHANGELOG.md
│                           ├── LICENSE
│                           ├── README.md
│                           └── terraform-provider-docker_v2.12.2
├── .terraform.lock.hcl
├── main.tf
├── nginx
│   ├── main.tf
│   └── variables.tf
└── versions.tf

13 directories, 11 files


但如果每个项目都要单独下载一次，那可是太麻烦了。我们可以把所有插件都放在同一个地方，然后通过-plugin-dir来指定，如下：

$ rm -rf ./.terraform*

$ terraform init -plugin-dir=/Users/larry/Software/terraform/plugins
Initializing provider plugins...
- Finding kreuzwerker/docker versions matching "2.12.2"...
- Finding hashicorp/kubernetes versions matching "2.2.0"...
- Installing kreuzwerker/docker v2.12.2...
- Installed kreuzwerker/docker v2.12.2 (unauthenticated)
- Installing hashicorp/kubernetes v2.2.0...
- Installed hashicorp/kubernetes v2.2.0 (unauthenticated)


$ tree -a
.
├── .terraform
│   ├── modules
│   │   └── modules.json
│   ├── plugin_path
│   └── providers
│       └── registry.terraform.io
│           ├── hashicorp
│           │   └── kubernetes
│           │       └── 2.2.0
│           │           └── darwin_amd64 -> /Users/larry/Software/terraform/plugins/registry.terraform.io/hashicorp/kubernetes/2.2.0/darwin_amd64
│           └── kreuzwerker
│               └── docker
│                   └── 2.12.2
│                       └── darwin_amd64 -> /Users/larry/Software/terraform/plugins/registry.terraform.io/kreuzwerker/docker/2.12.2/darwin_amd64
├── .terraform.lock.hcl
├── main.tf
├── nginx
│   ├── main.tf
│   └── variables.tf
└── versions.tf

13 directories, 7 files



可以看到它只是在当前目录创建了一个链接到指定plugin目录的Provider上。

如果想要自己手动下载，可以到这个网址：https://releases.hashicorp.com/

其实工作时用到的Provider就几个，直接下载好放在plugin目录即可。
