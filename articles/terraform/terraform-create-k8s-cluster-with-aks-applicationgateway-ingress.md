---
title: Vytvoření clusteru Kubernetes pomocí služby Application Gateway jako kontroler příchozího přenosu dat pomocí Azure Kubernetes Service (AKS)
description: Kurz znázorňující způsob vytvoření clusteru Kubernetes pomocí služby Azure Kubernetes Service pomocí služby Application Gateway jako kontroler příchozího přenosu dat
services: terraform
ms.service: terraform
keywords: terraform, devops, virtuální počítač, azure, kubernetes, příchozího přenosu dat, služba application gateway
author: tomarcher
manager: jeconnoc
ms.author: tarcher
ms.topic: tutorial
ms.date: 1/10/2019
ms.openlocfilehash: 6add7323fdbcf07681e8566437632aa6679828e4
ms.sourcegitcommit: e51e940e1a0d4f6c3439ebe6674a7d0e92cdc152
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/08/2019
ms.locfileid: "55891977"
---
# <a name="create-a-kubernetes-cluster-with-application-gateway-ingress-controller-using-azure-kubernetes-service-and-terraform"></a>Vytvoření clusteru Kubernetes s Application Gateway kontroler příchozího přenosu dat pomocí služby Azure Kubernetes Service a Terraformu
[Azure Kubernetes Service (AKS)](/azure/aks/) spravuje vaše hostované prostředí Kubernetes. AKS umožňuje rychle a snadno nasadit a spravovat kontejnerizované aplikace bez znalosti Orchestrace kontejnerů. Zároveň eliminuje režii spojenou s probíhajícími operacemi a údržbou díky zřizování, upgradování a škálování prostředků na vyžádání bez nutnosti odpojovat aplikace.

Řadič služby příchozího přenosu dat je část softwaru, která poskytuje reverzní proxy server, směrování provozu konfigurovatelné a ukončení protokolu TLS pro služby Kubernetes. Prostředky Kubernetesu příchozího přenosu dat se používají ke konfiguraci pravidla příchozího přenosu dat a trasy pro jednotlivé služby Kubernetes. Použití kontroler příchozího přenosu dat a pravidla příchozího přenosu dat, jednu IP adresu je možné směrovat provoz do více služeb v clusteru Kubernetes. Azure poskytuje všechny výše uvedené funkce [Application Gateway](/azure/Application-Gateway/), takže se dají ideální kontroler příchozího přenosu dat pro Kubernetes v Azure. 

V tomto kurzu se dozvíte, jak provádět následující úkoly při vytváření [Kubernetes](https://www.redhat.com/en/topics/containers/what-is-kubernetes) cluster AKS pomocí Application Gateway jako kontroler příchozího přenosu dat:

> [!div class="checklist"]
> * Použití HCL (Jazyk společnosti HashiCorp) k definování clusteru Kubernetes
> * Použijte Terraform a vytvoří prostředek služby Application Gateway
> * Použití Terraformu a AKS k vytvoření clusteru Kubernetes
> * Použití nástroje kubectl k otestování dostupnosti clusteru Kubernetes

## <a name="prerequisites"></a>Požadavky

- **Předplatné Azure**: Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) před tím, než začnete.

- **Nakonfigurujte Terraform**: Postupujte podle pokynů v článku, [Terraform a konfigurovat přístup k Azure](/azure/virtual-machines/linux/terraform-install-configure)

- **Instanční objekt Azure**: Postupujte podle pokynů v části **vytvořit instanční objekt** části tohoto článku věnované [vytvoření instančního objektu Azure pomocí rozhraní příkazového řádku Azure](/cli/azure/create-an-azure-service-principal-azure-cli?view=azure-cli-latest). Poznamenejte si hodnoty pro ID aplikace, displayName a heslo.
    - Spuštěním následujícího příkazu si poznamenejte ID objektu instanční objekt služby

    ```bash
     az ad sp list --display-name <displayName>
    ```

## <a name="create-the-directory-structure"></a>Vytvoření struktury adresáře
Prvním krokem je vytvoření adresáře s konfiguračními soubory Terraformu pro toto cvičení.

1. Přejděte na web [Azure Portal](http://portal.azure.com).

1. Otevřete službu [Azure Cloud Shell](/azure/cloud-shell/overview). Pokud jste prostředí ještě nevybrali, vyberte prostředí **Bash**.

    ![Příkazový řádek Cloud Shellu](./media/terraform-k8s-cluster-appgw-with-tf-aks/azure-portal-cloud-shell-button-min.png)

1. Přejděte do adresáře `clouddrive`.

    ```bash
    cd clouddrive
    ```

1. Vytvořte adresář s názvem `terraform-aks-k8s`.

    ```bash
    mkdir terraform-aks-appgw-ingress
    ```

1. Přejděte do nového adresáře:

    ```bash
    cd terraform-aks-appgw-ingress
    ```

## <a name="declare-the-azure-provider"></a>Deklarování zprostředkovatele Azure
Vytvořte konfigurační soubor Terraformu, který deklaruje zprostředkovatele Azure.

1. Ve Cloud Shellu vytvořte soubor s názvem `main.tf`.

    ```bash
    vi main.tf
    ```

1. Stisknutím klávesy I přejděte do režimu vkládání.

1. Do editoru vložte následující kód:

    ```JSON
    provider "azurerm" {
        version = "~>1.18"
    }

    terraform {
        backend "azurerm" {}
    }
    ```

1. Stisknutím klávesy **Esc** ukončete režim vkládání.

1. Uložte soubor a zadáním následujícího příkazu ukončete editor vi:

    ```bash
    :wq
    ```
## <a name="define-input-variables"></a>Definujte vstupní proměnné
Vytvoření konfiguračního souboru Terraform, který zobrazí seznam všech proměnných potřebných pro toto nasazení
1. Ve službě Cloud Shell vytvořte soubor s názvem `variables.tf`
    ```bash
    vi variables.tf
    ```
1. Stisknutím klávesy I přejděte do režimu vkládání.

2. Do editoru vložte následující kód:
    
    ```JSON
    variable "resource_group_name" {
      description = "Name of the resource group already created."
    }
    
    variable "location" {
      description = "Location of the cluster."
    }
    
    variable "aks_service_principal_app_id" {
      description = "Application ID/Client ID  of the service principal. Used by AKS to manage AKS related resources on Azure like vms, subnets."
    }
    
    variable "aks_service_principal_client_secret" {
      description = "Secret of the service principal. Used by AKS to manage Azure."
    }
    
    variable "aks_service_principal_object_id" {
      description = "Object ID of the service principal."
    }
    
    variable "virtual_network_name" {
      description = "Virtual network name"
      default     = "aksVirtualNetwork"
    }
    
    variable "virtual_network_address_prefix" {
      description = "Containers DNS server IP address."
      default     = "15.0.0.0/8"
    }
    
    variable "aks_subnet_name" {
      description = "AKS Subnet Name."
      default     = "kubesubnet"
    }
    
    variable "aks_subnet_address_prefix" {
      description = "Containers DNS server IP address."
      default     = "15.0.0.0/16"
    }
    
    variable "app_gateway_subnet_address_prefix" {
      description = "Containers DNS server IP address."
      default     = "15.1.0.0/16"
    }
    
    variable "app_gateway_name" {
      description = "Name of the Application Gateway."
      default = "ApplicationGateway1"
    }
    
    variable "app_gateway_sku" {
      description = "Name of the Application Gateway SKU."
      default = "Standard_v2"
    }
    
    
    variable "app_gateway_tier" {
      description = "Tier of the Application Gateway SKU."
      default = "Standard_v2"
    }
    
    
    variable "aks_name" {
      description = "Name of the AKS cluster."
      default     = "aks-cluster1"
    }
    variable "aks_dns_prefix" {
      description = "Optional DNS prefix to use with hosted Kubernetes API server FQDN."
      default     = "aks"
    }
    
    
    variable "aks_agent_os_disk_size" {
      description = "Disk size (in GB) to provision for each of the agent pool nodes. This value ranges from 0 to 1023. Specifying 0 will apply the default disk size for that agentVMSize."
      default     = 40
    }
    
    variable "aks_agent_count" {
      description = "The number of agent nodes for the cluster."
      default     = 3
    }
    
    variable "aks_agent_vm_size" {
      description = "The size of the Virtual Machine."
      default     = "Standard_D3_v2"
    }
    
    variable "kubernetes_version" {
      description = "The version of Kubernetes."
      default     = "1.11.5"
    }
    
    variable "aks_service_cidr" {
      description = "A CIDR notation IP range from which to assign service cluster IPs."
      default     = "10.0.0.0/16"
    }
    
    variable "aks_dns_service_ip" {
      description = "Containers DNS server IP address."
      default     = "10.0.0.10"
    }
    
    variable "aks_docker_bridge_cidr" {
      description = "A CIDR notation IP for Docker bridge."
      default     = "172.17.0.1/16"
    }
    
    variable "aks_enable_rbac" {
      description = "Enable RBAC on the AKS cluster. Defaults to false."
      default     = "false"
    }
    
    variable "vm_user_name" {
      description = "User name for the VM"
      default     = "vmuser1"
    }
    
    variable "public_ssh_key_path" {
      description = "Public key path for SSH."
      default     = "~/.ssh/id_rsa.pub"
    }
    
    variable "tags" {
      type = "map"
    
      default = {
        source = "terraform"
      }
    }
    ```

## <a name="define-the-resources"></a>Definování prostředků 
Vytvořte Terraformu konfigurační soubor, který vytvoří všechny prostředky. 

1. Ve Cloud Shellu vytvořte soubor s názvem `resources.tf`.

    ```bash
    vi resources.tf
    ```

1. Stisknutím klávesy I přejděte do režimu vkládání.

1. Do editoru vložte následující bloky kódu:

    a. Vytvořit lokální blok pro vypočítaný proměnné jak znovu použít

    ```JSON
    # # Locals block for hardcoded names. 
    locals {
        backend_address_pool_name      = "${azurerm_virtual_network.test.name}-beap"
        frontend_port_name             = "${azurerm_virtual_network.test.name}-feport"
        frontend_ip_configuration_name = "${azurerm_virtual_network.test.name}-feip"
        http_setting_name              = "${azurerm_virtual_network.test.name}-be-htst"
        listener_name                  = "${azurerm_virtual_network.test.name}-httplstn"
        request_routing_rule_name      = "${azurerm_virtual_network.test.name}-rqrt"
        app_gateway_subnet_name = "appgwsubnet"
    }
    ```
    b. Vytvořit zdroj dat pro skupinu prostředků, novou identitu uživatele
    ```JSON
    data "azurerm_resource_group" "rg" {
      name = "${var.resource_group_name}"
    }
    
    # User Assigned Idntities 
    resource "azurerm_user_assigned_identity" "testIdentity" {
      resource_group_name = "${data.azurerm_resource_group.rg.name}"
      location            = "${data.azurerm_resource_group.rg.location}"
    
      name = "identity1"
    
      tags = "${var.tags}"
    }
    ```
    c. Vytvoření základní síťových prostředků
   ```JSON
    resource "azurerm_virtual_network" "test" {
      name                = "${var.virtual_network_name}"
      location            = "${data.azurerm_resource_group.rg.location}"
      resource_group_name = "${data.azurerm_resource_group.rg.name}"
      address_space       = ["${var.virtual_network_address_prefix}"]
    
      subnet {
        name           = "${var.aks_subnet_name}"
        address_prefix = "${var.aks_subnet_address_prefix}" 
      }
    
      subnet {
        name           = "appgwsubnet"
        address_prefix = "${var.app_gateway_subnet_address_prefix}"
      }
    
      tags = "${var.tags}"
    }
    
    data "azurerm_subnet" "kubesubnet" {
      name                 = "${var.aks_subnet_name}"
      virtual_network_name = "${azurerm_virtual_network.test.name}"
      resource_group_name  = "${data.azurerm_resource_group.rg.name}"
    }
    
    data "azurerm_subnet" "appgwsubnet" {
      name                 = "appgwsubnet"
      virtual_network_name = "${azurerm_virtual_network.test.name}"
      resource_group_name  = "${data.azurerm_resource_group.rg.name}"
    }
    
    # Public Ip 
    resource "azurerm_public_ip" "test" {
      name                         = "publicIp1"
      location                     = "${data.azurerm_resource_group.rg.location}"
      resource_group_name          = "${data.azurerm_resource_group.rg.name}"
      public_ip_address_allocation = "static"
      sku                          = "Standard"
    
      tags = "${var.tags}"
    }
    ```
    d. Vytvořit prostředek služby Application Gateway
    ```JSON
    resource "azurerm_application_gateway" "network" {
      name                = "${var.app_gateway_name}"
      resource_group_name = "${data.azurerm_resource_group.rg.name}"
      location            = "${data.azurerm_resource_group.rg.location}"
    
      sku {
        name     = "${var.app_gateway_sku}"
        tier     = "Standard_v2"
        capacity = 2
      }
    
      gateway_ip_configuration {
        name      = "appGatewayIpConfig"
        subnet_id = "${data.azurerm_subnet.appgwsubnet.id}"
      }
    
      frontend_port {
        name = "${local.frontend_port_name}"
        port = 80
      }
    
      frontend_port {
        name = "httpsPort"
        port = 443
      }
    
      frontend_ip_configuration {
        name                 = "${local.frontend_ip_configuration_name}"
        public_ip_address_id = "${azurerm_public_ip.test.id}"
      }
    
      backend_address_pool {
        name = "${local.backend_address_pool_name}"
      }
    
      backend_http_settings {
        name                  = "${local.http_setting_name}"
        cookie_based_affinity = "Disabled"
        port                  = 80
        protocol              = "Http"
        request_timeout       = 1
      }
    
      http_listener {
        name                           = "${local.listener_name}"
        frontend_ip_configuration_name = "${local.frontend_ip_configuration_name}"
        frontend_port_name             = "${local.frontend_port_name}"
        protocol                       = "Http"
      }
    
      request_routing_rule {
        name                       = "${local.request_routing_rule_name}"
        rule_type                  = "Basic"
        http_listener_name         = "${local.listener_name}"
        backend_address_pool_name  = "${local.backend_address_pool_name}"
        backend_http_settings_name = "${local.http_setting_name}"
      }
    
      tags = "${var.tags}"
    
      depends_on = ["azurerm_virtual_network.test", "azurerm_public_ip.test"]
    }
    ```
    e. Vytvořit přiřazení role
    ```JSON
    resource "azurerm_role_assignment" "ra1" {
      scope                = "${data.azurerm_subnet.kubesubnet.id}"
      role_definition_name = "Network Contributor"
      principal_id         = "${var.aks_service_principal_object_id }"
    
      depends_on = ["azurerm_virtual_network.test"]
    }
    
    resource "azurerm_role_assignment" "ra2" {
      scope                = "${azurerm_user_assigned_identity.testIdentity.id}"
      role_definition_name = "Managed Identity Operator"
      principal_id         = "${var.aks_service_principal_object_id}"
      depends_on           = ["azurerm_user_assigned_identity.testIdentity"]
    }
    
    resource "azurerm_role_assignment" "ra3" {
      scope                = "${azurerm_application_gateway.network.id}"
      role_definition_name = "Contributor"
      principal_id         = "${azurerm_user_assigned_identity.testIdentity.principal_id}"
      depends_on           = ["azurerm_user_assigned_identity.testIdentity", "azurerm_application_gateway.network"]
    }
    
    resource "azurerm_role_assignment" "ra4" {
      scope                = "${data.azurerm_resource_group.rg.id}"
      role_definition_name = "Reader"
      principal_id         = "${azurerm_user_assigned_identity.testIdentity.principal_id}"
      depends_on           = ["azurerm_user_assigned_identity.testIdentity", "azurerm_application_gateway.network"]
    }
    ```
    f. Vytvoření clusteru Kubernetes
    ```JSON
    resource "azurerm_kubernetes_cluster" "k8s" {
      name       = "${var.aks_name}"
      location   = "${data.azurerm_resource_group.rg.location}"
      dns_prefix = "${var.aks_dns_prefix}"
    
      resource_group_name = "${data.azurerm_resource_group.rg.name}"
    
      linux_profile {
        admin_username = "${var.vm_user_name}"
    
        ssh_key {
          key_data = "${file(var.public_ssh_key_path)}"
        }
      }
    
      addon_profile {
        http_application_routing {
          enabled = false
        }
      }
    
      agent_pool_profile {
        name            = "agentpool"
        count           = "${var.aks_agent_count}"
        vm_size         = "${var.aks_agent_vm_size}"
        os_type         = "Linux"
        os_disk_size_gb = "${var.aks_agent_os_disk_size}"
        vnet_subnet_id  = "${data.azurerm_subnet.kubesubnet.id}"
      }
    
      service_principal {
        client_id     = "${var.aks_service_principal_app_id}"
        client_secret = "${var.aks_service_principal_client_secret}"
      }
    
      network_profile {
        network_plugin     = "azure"
        dns_service_ip     = "${var.aks_dns_service_ip}"
        docker_bridge_cidr = "${var.aks_docker_bridge_cidr}"
        service_cidr       = "${var.aks_service_cidr}"
      }
    
      depends_on = ["azurerm_virtual_network.test", "azurerm_application_gateway.network"]
      tags       = "${var.tags}"
    }
    
    ```

    Výše uvedený kód nastaví název clusteru, umístění a název skupiny prostředků. Kromě toho nastaví i hodnotu dns_prefix (předpona DNS), která je součástí plně kvalifikovaného názvu domény (FQDN) používaného pro přístup ke clusteru.

    Záznam **linux_profile** vám umožňuje nakonfigurovat nastavení, která prostřednictvím protokolu SSH povolují přihlášení k pracovním uzlům.

    Se službou AKS platíte jenom za pracovní uzly. Záznam **agent_pool_profile** nakonfiguruje podrobnosti těchto pracovních uzlů. Záznam **agent_pool_profile** obsahuje počet pracovních uzlů, které se mají vytvořit, a jejich typ. Pokud budete potřebovat vertikálně navýšit nebo snížit kapacitu clusteru, můžete u tohoto záznamu změnit hodnotu **count** (počet).

1. Stisknutím klávesy **Esc** ukončete režim vkládání.

1. Uložte soubor a zadáním následujícího příkazu ukončete editor vi:

    ```bash
    :wq
    ```


## <a name="create-a-terraform-output-file"></a>Vytvoření výstupního souboru Terraformu
[Výstupy Terraformu](https://www.terraform.io/docs/configuration/outputs.html) vám umožňují definovat hodnoty, které se uživateli zvýrazní, když Terraform použije plán a bude možné se na něj dotázat příkazem `terraform output`. V této části vytvoříte výstupní soubor, který pomocí [kubectl](https://kubernetes.io/docs/reference/kubectl/overview/) umožňuje přístup ke clusteru.

1. Ve službě Cloud Shell vytvořte soubor s názvem `output.tf`.

    ```bash
    vi output.tf
    ```

1. Stisknutím klávesy I přejděte do režimu vkládání.

1. Do editoru vložte následující kód:

    ```JSON
    output "client_key" {
        value = "${azurerm_kubernetes_cluster.k8s.kube_config.0.client_key}"
    }

    output "client_certificate" {
        value = "${azurerm_kubernetes_cluster.k8s.kube_config.0.client_certificate}"
    }

    output "cluster_ca_certificate" {
        value = "${azurerm_kubernetes_cluster.k8s.kube_config.0.cluster_ca_certificate}"
    }

    output "cluster_username" {
        value = "${azurerm_kubernetes_cluster.k8s.kube_config.0.username}"
    }

    output "cluster_password" {
        value = "${azurerm_kubernetes_cluster.k8s.kube_config.0.password}"
    }

    output "kube_config" {
        value = "${azurerm_kubernetes_cluster.k8s.kube_config_raw}"
    }

    output "host" {
        value = "${azurerm_kubernetes_cluster.k8s.kube_config.0.host}"
    }
    ```

1. Stisknutím klávesy **Esc** ukončete režim vkládání.

1. Uložte soubor a zadáním následujícího příkazu ukončete editor vi:

    ```bash
    :wq
    ```

## <a name="set-up-azure-storage-to-store-terraform-state"></a>Nastavení služby Azure Storage k uložení stavu Terraformu
Terraform sleduje stav místně prostřednictvím souboru `terraform.tfstate`. Tento model funguje dobře v prostředí s jednou osobou. Nicméně, v prostředí s více praktické účasti několika osob, potřebujete ke sledování stavu na serveru pomocí [služby Azure storage](/azure/storage/). V této části získáte potřebné informace o účtu úložiště (název účtu a klíč účtu) a vytvoříte kontejner úložiště, do kterého se uloží informace o stavu Terraformu.

1. Na webu Azure Portal vyberte v levé nabídce **Všechny služby**.

1. Vyberte **Účty úložiště**.

1. Na kartě **Účty úložiště** vyberte název účtu úložiště, do kterého má Terraform ukládat stav. Můžete například použít účet úložiště, který se vytvoří při prvním otevření služby Cloud Shell.  Název účtu úložiště vytvořeného službou Cloud Shell obvykle začíná na `cs` a následuje ho řetězec náhodných čísel a písmen. **Poznamenejte si název účtu úložiště, kterou vyberete, jako potřebujeme ho později.**

1. Na kartě účtu úložiště vyberte **Přístupové klíče**.

    ![Nabídka účtu úložiště](./media/terraform-k8s-cluster-appgw-with-tf-aks/storage-account.png)

1. Poznamenejte si hodnotu **Klíč** v části **klíč1**. (Výběr ikony napravo od klíče zkopíruje příslušnou hodnotu do schránky.)

    ![Přístupové klíče účtu úložiště](./media/terraform-k8s-cluster-appgw-with-tf-aks/storage-account-access-key.png)

1. Ve službě Cloud Shell vytvořte v účtu Azure Storage kontejner (nahraďte zástupné hodnoty &lt;YourAzureStorageAccountName> a &lt;YourAzureStorageAccountAccessKey> příslušnými hodnotami vašeho účtu Azure Storage).

    ```bash
    az storage container create -n tfstate --account-name <YourAzureStorageAccountName> --account-key <YourAzureStorageAccountKey>
    ```

## <a name="create-the-kubernetes-cluster"></a>Vytvoření clusteru Kubernetes
V této části zjistíte, jak použít příkaz `terraform init` k vytvoření prostředků definovaných konfiguračními soubory, které jste vytvořili v předchozích částech.

1. Ve službě Cloud Shell inicializujte Terraform (nahraďte zástupné hodnoty &lt;YourAzureStorageAccountName> a &lt;YourAzureStorageAccountAccessKey> příslušnými hodnotami vašeho účtu Azure Storage).

    ```bash
    terraform init -backend-config="storage_account_name=<YourAzureStorageAccountName>" -backend-config="container_name=tfstate" -backend-config="access_key=<YourStorageAccountAccessKey>" -backend-config="key=codelab.microsoft.tfstate" 
    ```
    
    Příkaz `terraform init` zobrazí úspěch inicializace back-endu a modulu plug-in poskytovatele:

    ![Příklad výsledků příkazu „terraform init“](./media/terraform-k8s-cluster-appgw-with-tf-aks/terraform-init-complete.png)

1. Vytvoření souboru proměnných zadávat vstupní hodnoty ve službě Cloud Shell, vytvořte soubor s názvem `main.tf`.

    ```bash
    vi terraform.tfvars
    ```

1. Stisknutím klávesy I přejděte do režimu vkládání.

1. Vložte následující proměnné na do editoru vytvořili dříve:

    ```JSON
      resource_group_name = <Name of the Resource Group already created>

      location = <Location of the Resource Group>
        
      aks_service_principal_app_id = <Service Principal AppId>
        
      aks_service_principal_client_secret = <Service Principal Client Secret>
        
      aks_service_principal_object_id = <Service Principal Object Id>
        
    ```

1. Stisknutím klávesy **Esc** ukončete režim vkládání.

1. Uložte soubor a zadáním následujícího příkazu ukončete editor vi:

    ```bash
    :wq
    ```

1. Spuštěním příkazu `terraform plan` vytvořte plán Terraformu, který definuje prvky infrastruktury. 

    ```bash
    terraform plan -out out.plan
    ```

    Příkaz `terraform plan` zobrazí prostředky, které se po spuštění příkazu `terraform apply` vytvoří:

    ![Příklad výsledků příkazu „terraform plan“](./media/terraform-k8s-cluster-appgw-with-tf-aks/terraform-plan-complete.png)

1. Spuštěním příkazu `terraform apply` použijte plán a vytvořte cluster Kubernetes. Vytvoření clusteru Kubernetes může trvat několik minut a relace služby Cloud Shell může vypršet. Pokud vyprší časový limit relace Cloud Shellu, provedením kroků v části "Obnovit z časový limit Cloud Shellu" vám k dokončení tohoto kurzu.

    ```bash
    terraform apply out.plan
    ```

    Příkaz `terraform apply` zobrazí výsledky vytvoření prostředků definovaných v konfiguračních souborech:

    ![Příklad výsledků příkazu „terraform apply“](./media/terraform-k8s-cluster-appgw-with-tf-aks/terraform-apply-complete.png)

1. Na webu Azure Portal, vyberte **skupiny prostředků** v levé nabídce zobrazíte prostředky vytvořené pro nový cluster Kubernetes ve vybrané skupině prostředků.

    ![Příkazový řádek Cloud Shellu](./media/terraform-k8s-cluster-appgw-with-tf-aks/k8s-resources-created.png)

## <a name="recover-from-a-cloud-shell-timeout"></a>Zotavení z vypršení relace služby Cloud Shell
Pokud vyprší časový limit relace Cloud Shell, můžete obnovit takto:

1. Spusťte relaci služby Cloud Shell.

1. Přejděte do adresáře, který obsahuje konfigurační soubory Terraformu.

    ```bash
    cd /clouddrive/terraform-aks-k8s
    ```

1. Spusťte následující příkaz:

    ```bash
    export KUBECONFIG=./azurek8s
    ```
    
## <a name="test-the-kubernetes-cluster"></a>Test clusteru Kubernetes
Nově vytvořený cluster můžete pomocí nástrojů Kubernetes ověřit.

1. Získejte konfiguraci Kubernetes ze stavu Terraformu a uložte ji do souboru, který dokáže nástroj kubectl přečíst.

    ```bash
    echo "$(terraform output kube_config)" > ./azurek8s
    ```

1. Nastavte proměnnou prostředí, aby kubectl převzal správnou konfiguraci.

    ```bash
    export KUBECONFIG=./azurek8s
    ```

1. Ověřte stav clusteru.

    ```bash
    kubectl get nodes
    ```

    Měly by se zobrazit podrobnosti o pracovních uzlech a všechny uzly by měli být ve stavu **Ready** (Připravené), jak je znázorněno na následujícím obrázku:

    ![Nástroj kubectl vám umožňuje ověřit stav clusteru Kubernetes.](./media/terraform-k8s-cluster-appgw-with-tf-aks/kubectl-get-nodes.png)


## <a name="next-steps"></a>Další postup
V tomto článku jste zjistili, jak pomocí Terraformu a AKS vytvořit cluster Kubernetes. Tady jsou některé další prostředky, které vám pomohou získat informace o Terraformu v Azure.
 
 > [!div class="nextstepaction"] 
 > [Dokumentace k Terraformu v Azure](https://docs.microsoft.com/azure/terraform/)
 
