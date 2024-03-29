# Terraform


参考:

- [Terraform](https://www.terraform.io/)
- [什么是基础架构即代码](https://www.redhat.com/zh/topics/automation/what-is-infrastructure-as-code-iac)

<br/>

---

<!--more-->

<br/>

# 基础架构即代码

基础架构即代码(IaC, Infrastructure as Code)，是通过代码（而非手动流程）来管理和置备基础架构（网络、虚拟机、负载均衡器和连接拓扑）的方法。

利用IaC，我们可以创建包含基础架构规范的配置文件，从而便于编辑和分发配置。此外，它还可确保每次置备的环境都完全相同。

版本控制是IaC的一个重要组成部分，就像其他任何软件源代码文件一样，配置文件也应该在源代码控制之下。

<br/>

---

<br/>

# 介绍

<br/>

## Terraform是什么

Terraform是一个基础设施即代码的工具，可让你安全有效地构建，更改和版本化基础设施。这包含了两个组件，低级别的如计算实例，存储和网络；高级别的DNS和SaaS功能。

Terraform通过其API在云平台或其它服务上创建和管理资源。供应商(provider)使得Terraform几乎可以使用任何平台和服务。可以在[Terrafrom Registry](https://registry.terraform.io/)上查询对应的供应商。如aws, gcp, azure, alicloud, tencentcloud等。

![](/images/Terraform/assets.png)

<br/>

Terraform的工作流由三个阶段组成：

- **Write**: 定义资源。
- **Plan**: 基于现有基础架构和配置，描述是创建、更新或摧毁基础架构。
- **Apply**: 执行操作。

![工作流](/images/Terraform/write-plan-apply.png)

<br/>
<br/>

## 用例

- 多云部署
- 应用基础设施部署，扩展和监控工具
- 自助集群
- 政策合规性和管理
- Paas应用配置
- 软件定义的网络
- Kubernetes
- 并行环境
- 软件演示

<br/>
<br/>

## 快速开始

<br/>

### AWS示例

<br/>

#### 导入aws相关认证

```bash
export AWS_ACCESS_KEY_ID="<YOUR_AWS_ACCESS_KEY_ID>"
export AWS_SECRET_ACCESS_KEY="<YOUR_AWS_SECRET_ACCESS_KEY>"
export AWS_DEFAULT_REGION="<YOUR_AWS_DEFAULT_REGION>"
```

<br/>

#### 编写配置

```bash
mkdir learn-terraform-aws-instance

cd learn-terraform-aws-instance

touch main.tf
```

<br/>

#### 配置内容

```tf
# terraform块包含Terraform设置
# 包含需要的provider
terraform {
  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = "~> 3.27"
    }
  }

  required_version = ">= 0.14.9"
}

# provider块配置特定的provider
provider "aws" {
  profile = "default"
  region  = "us-west-2"
}

# resource块定义基础设施的组件
resource "aws_instance" "app_server" {
  ami           = "ami-830c94e3"
  instance_type = "t2.micro"

  tags = {
    Name = "ExampleAppServerInstance"
  }
}
```

<br/>

#### 初始化目录

```bash
terraform init
```

<br/>

#### 格式化和验证配置

```bash
terraform fmt

terraform validate
```

<br/>

#### 查看变更和创建基础设施

```bash
terraform plan

terraform apply
```

<br/>
<br/>

#### 查看状态

```bash
terraform show

terraform state list
```

<br/>

#### 定义变量

创建`variables.tf`文件，定义变量。

```tf
variable "instance_name" {
  description = "value of the name tag for ec2 instance"
  type = string
  default = "ExampleAppServerInstance"
}
```

使用变量

```tf
resource "aws_instance" "app_server" {
  ami           = "ami-08d70e59c07c61a3a"
  instance_type = "t2.micro"

  tags = {
-   Name = "ExampleAppServerInstance"
+   Name = var.instance_name
   }
 }
```

也可以在命令上上使用`-var "instance_name=YetAnotherName"`来设置变量。

<br/>

#### 在输出中查询数据

编写`output.tf`

```tf
output "instance_id" {
  description = "ID of the EC2 instance"
  value       = aws_instance.app_server.id
}

output "instance_public_ip" {
  description = "Public IP address of the EC2 instance"
  value       = aws_instance.app_server.public_ip
}

```

<br/>

查看输出内容

```bash
terraform output
instance_id = "i-0bf954919ed765de1"
instance_public_ip = "54.186.202.254"
```

<br/>

---

<br/>

# 语言

Terraform使用HCL(Hashicorp Configuration Language)。

Terraform语言的语法由一些基础元素组成:

```tf
resource "aws_vpc" "main" {
  cidr_block = var.base_cidr_block
}

<BLOCK TYPE> "<BLOCK LABEL>" "<BLOCK LABEL>" {
  # Block body
  <IDENTIFIER>  = <EXPRESSION> # Agrument
}
```

<br/>

示例:

```tf
terraform {
  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = "~> 1.0.4"
    }
  }
}

variable "aws_region" {}

variable "base_cidr_block" {
  description = "A /16 CIDR range definition, such as 10.1.0.0/16, that the VPC will use"
  default = "10.1.0.0/16"
}

variable "availability_zones" {
  description = "A list of availability zones in which to create subnets"
  type = list(string)
}

provider "aws" {
  region = var.aws_region
}

resource "aws_vpc" "main" {
  # Referencing the base_cidr_block variable allows the network address
  # to be changed without modifying the configuration.
  cidr_block = var.base_cidr_block
}

resource "aws_subnet" "az" {
  # Create one subnet for each given availability zone.
  count = length(var.availability_zones)

  # For each subnet, use one of the specified availability zones.
  availability_zone = var.availability_zones[count.index]

  # By referencing the aws_vpc.main object, Terraform knows that the subnet
  # must be created only after the VPC is created.
  vpc_id = aws_vpc.main.id

  # Built-in functions and operators can be used for simple transformations of
  # values, such as computing a subnet address. Here we create a /20 prefix for
  # each subnet, using consecutive addresses for each availability zone,
  # such as 10.1.16.0/20 .
  cidr_block = cidrsubnet(aws_vpc.main.cidr_block, 4, count.index+1)
}
```

<br/>
<br/>

# 栗子

列举一些tf操作常见cloud provider的示例。

<br/>
<br/>

## 操作阿里云

参考:

- [terraform-provider-alicloud](https://registry.terraform.io/providers/aliyun/alicloud/)
- [tf-alicloud-examples](https://github.com/aliyun/terraform-provider-alicloud/tree/master/examples)

<br/>
<br/>

### 阿里云资源组

创建阿里云资源组的示例

```tf
# 资源组
resource "alicloud_resource_manager_resource_group" "main" {
  resource_group_name = "test"
  display_name = "test"
}
```

<br/>
<br/>

### 阿里云VPC

创建阿里云VPC的示例。

```tf
# main.tf
provider "alicloud" { 
}

# vpc
resource "alicloud_vpc" "main" {
  vpc_name = "test-vpc01"
  cidr_block = "172.16.0.0/16"

  description = "测试vpc"
  tags = {
    k1 = "v1"
  }
}

# vswitch
resource "alicloud_vswitch" "main" {
  vpc_id = alicloud_vpc.main.id
  cidr_block = "172.16.0.0/16"
  zone_id = "cn-hangzhou-b"
  vswitch_name = "test-vsw"

  description = "测试vswitch"
  tags = {
    k1 = "v1"
    k2 = "v2"
  }
}
```

<br/>
<br/>

### 阿里云NAT GATEWAY

创建阿里云NAT GATEWAY的示例

```tf
# NAT网关
resource "alicloud_nat_gateway" "main" {
  depends_on = [alicloud_vswitch.main]
  vpc_id = alicloud_vpc.main.id
  vswitch_id = alicoud_vswitch.main.id
  nat_gateway_name = "test-nat"

  payment_type = "PayAsYouGo"
  nat_type = "Enhanced"
  deletion_protection = "false"
  description = "测试NAT网关"
  tags = {
    k1 = "v1"
  }
}
```

<br/>
<br/>

### 阿里云安全组

创建阿里云安全组的示例

```tf
# 安全组
resource "alicloud_security_group" "group" {
  name = "test-sg"

  vpc_id = alicloud_vpc.main.id
  description = "测试安全组"
  tags = {
    k1 = "v1"
  }
}

# 安全组规则
resource "alicloud_security_group_rule" "deny-all" {
  security_group_id = alicloud_security_group.group.id
  type = "ingress"
  ip_protocol = "all"
  nic_type = "internet"
  policy = "drop"
  port_range = "1/65535"
  cidr_ip = "0.0.0.0/0"
  priority = 100
  description = "deny all"
}
# 安全组规则
resource "alicloud_security_group_rule" "http-80" {
  security_group_id = alicloud_security_group.group.id
  type = "ingress"
  ip_protocol = "tcp"
  nic_type = "internet"
  policy = "accept"
  port_range = "80/80"
  cidr_ip = "0.0.0.0/0"
  priority = 1
  description = "accept 80/tcp"
}
```

<br/>
<br/>

### 阿里云ECS

创建阿里云ECS的示例

```tf
resource "alicloud_instance" "instance" {
  security_groups = alicloud_security_group.group.*.id
  vswitch_id = alicloud_vswitch.main.id
  resource_group_id = alicloud_resource_manager_resource_group.main.id

  instance_name = "test01"
  hostname = "test01"
  instance_type = "ecs.n4.larger"
  image_id = ""
  system_disk_name = "test01-root"
  system_disk_category = "cloud_efficiency"
  system_disk_size = 40

  data_disks {
    name = "data"
    size = 100
    category    = "cloud_efficiency"
    description = "data"
  }
  data_disks {
    name = "data1"
    size = 100
    category    = "cloud_efficiency"
    description = "data1"
  }
}
```

<br/>
<br/>




















