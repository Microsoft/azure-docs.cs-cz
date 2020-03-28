---
title: Kurz – vytvoření řadiče příchozího přenosu dat aplikační brány ve službě Azure Kubernetes
description: V tomto kurzu vytvoříte cluster Kubernetes se službou Azure Kubernetes se službou Application Gateway jako řadičem příchozího přenosu dat
keywords: azure devops terraform aplikační brána ingress aks kubernetes
ms.topic: tutorial
ms.date: 03/09/2020
ms.openlocfilehash: 6b48d0acb654f0b0643c0754e53f6bc6ea76bb45
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "78945325"
---
# <a name="tutorial-create-an-application-gateway-ingress-controller-in-azure-kubernetes-service"></a>Kurz: Vytvoření řadiče příchozího přenosu dat aplikační brány ve službě Azure Kubernetes

[Služba Azure Kubernetes Service (AKS)](/azure/aks/) spravuje vaše hostované prostředí Kubernetes. AKS umožňuje rychlé a snadné nasazení a správu kontejnerizovaných aplikací bez odborných znalostí orchestrace kontejnerů. AKS také eliminuje zátěž spojenou s přepnutím aplikací do režimu offline pro provozní a údržbové úkoly. Pomocí AKS, tyto úkoly – včetně zřizování, upgradování a škálování prostředků – lze provést na vyžádání.

Řadič příchozího přenosu dat poskytuje různé funkce pro služby Kubernetes. Mezi tyto funkce patří reverzní proxy server, konfigurovatelné směrování provozu a ukončení TLS. Kubernetes ingress prostředky se používají ke konfiguraci pravidel příchozího přenosu dat pro jednotlivé služby Kubernetes. Pomocí řadiče příchozího přenosu dat a pravidel příchozího přenosu dat může jedna adresa IP směrovat provoz do více služeb v clusteru Kubernetes. Všechny tyto funkce poskytuje Azure [Application Gateway](/azure/Application-Gateway/), takže je ideální ingress řadič pro Kubernetes v Azure. 

V tomto kurzu se dozvíte, jak provést následující úkoly:

> [!div class="checklist"]
> * Vytvořte cluster [Kubernetes](https://www.redhat.com/en/topics/containers/what-is-kubernetes) pomocí AKS s aplikační bránou jako řadičem příchozího přenosu dat.
> * Pomocí hcl (HashiCorp jazyk) definovat clusterU Kubernetes.
> * Pomocí terraformu vytvořte prostředek aplikační brány.
> * Terraform a AKS slouží k vytvoření clusteru Kubernetes.
> * Pomocí nástroje kubectl otestujte dostupnost clusteru Kubernetes.

## <a name="prerequisites"></a>Požadavky

- **Předplatné Azure:** Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) před tím, než začnete.

- **Nakonfigurovaný nástroj Terraform**: Postupujte podle pokynů v článku o [instalaci Terraformu a konfiguraci přístupu k Azure](terraform-install-configure.md).

- **Skupina prostředků Azure**: Pokud nemáte skupinu prostředků Azure, kterou byste měli použít pro ukázku, [vytvořte skupinu prostředků Azure](/azure/azure-resource-manager/manage-resource-groups-portal#create-resource-groups). Vezměte na vědomí název skupiny prostředků a umístění, protože tyto hodnoty se používají v ukázce.

- **Instanční objekt Azure:** Postupujte podle pokynů v části **Vytvoření instančního objektu** v článku [Vytvoření instančního objektu Azure pomocí Azure CLI](/cli/azure/create-an-azure-service-principal-azure-cli?view=azure-cli-latest). Poznamenejte si hodnoty pro appId, displayName a heslo.

- **Získání ID objektu instančního objektu**: Spusťte následující příkaz v prostředí Cloud:`az ad sp list --display-name <displayName>`

## <a name="create-the-directory-structure"></a>Vytvoření struktury adresáře

Prvním krokem je vytvoření adresáře s konfiguračními soubory Terraformu pro toto cvičení.

1. Přejděte na [portál Azure](https://portal.azure.com).

1. Otevřete službu [Azure Cloud Shell](/azure/cloud-shell/overview).

1. Přejděte do adresáře `clouddrive`.

    ```bash
    cd clouddrive
    ```

1. Vytvořte adresář s názvem `terraform-aks-appgw-ingress`.

    ```bash
    mkdir terraform-aks-appgw-ingress
    ```

1. Přejděte do nového adresáře:

    ```bash
    cd terraform-aks-appgw-ingress
    ```

## <a name="declare-the-azure-provider"></a>Deklarování zprostředkovatele Azure

Vytvořte konfigurační soubor Terraformu, který deklaruje zprostředkovatele Azure.

1. Ve službě Cloud Shell vytvořte soubor s názvem `main.tf`.

    ```bash
    code main.tf
    ```

1. Do editoru vložte následující kód:

    ```hcl
    provider "azurerm" {
      # The "feature" block is required for AzureRM provider 2.x. 
      # If you are using version 1.x, the "features" block is not allowed.
      version = "~>2.0"
      features {}
    }

    terraform {
        backend "azurerm" {}
    }
    ```

1. Uložte soubor**&lt;(Ctrl>S**) a ukončete editor**&lt;(Ctrl>Q**).

## <a name="define-input-variables"></a>Definování vstupních proměnných

Vytvořte konfigurační soubor Terraform, který obsahuje seznam všech proměnných požadovaných pro toto nasazení.

1. Ve službě Cloud Shell vytvořte soubor s názvem `variables.tf`.

    ```bash
    code variables.tf
    ```

1. Do editoru vložte následující kód:
    
    ```hcl
    variable "resource_group_name" {
      description = "Name of the resource group."
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
      description = "Disk size (in GB) to provision for each of the agent pool nodes. This value ranges from 0 to 1023. Specifying 0 applies the default disk size for that agentVMSize."
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

1. Uložte soubor**&lt;(Ctrl>S**) a ukončete editor**&lt;(Ctrl>Q**).

## <a name="define-the-resources"></a>Definování zdrojů 
Vytvořte konfigurační soubor Terraform, který vytvoří všechny prostředky. 

1. Ve službě Cloud Shell vytvořte soubor s názvem `resources.tf`.

    ```bash
    code resources.tf
    ```

1. Vložte následující blok kódu a vytvořte místní blok pro vypočítané proměnné pro opakované použití:

    ```hcl
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

1. Vložením následujícího bloku kódu vytvořte zdroj dat pro skupinu prostředků, novou identitu uživatele:

    ```hcl
    data "azurerm_resource_group" "rg" {
      name = var.resource_group_name
    }

    # User Assigned Identities 
    resource "azurerm_user_assigned_identity" "testIdentity" {
      resource_group_name = data.azurerm_resource_group.rg.name
      location            = data.azurerm_resource_group.rg.location

      name = "identity1"

      tags = var.tags
    }
    ```

1. Chcete-li vytvořit základní síťové prostředky, vložte následující blok kódu:

    ```hcl
    resource "azurerm_virtual_network" "test" {
      name                = var.virtual_network_name
      location            = data.azurerm_resource_group.rg.location
      resource_group_name = data.azurerm_resource_group.rg.name
      address_space       = [var.virtual_network_address_prefix]

      subnet {
        name           = var.aks_subnet_name
        address_prefix = var.aks_subnet_address_prefix
      }

      subnet {
        name           = "appgwsubnet"
        address_prefix = var.app_gateway_subnet_address_prefix
      }

      tags = var.tags
    }

    data "azurerm_subnet" "kubesubnet" {
      name                 = var.aks_subnet_name
      virtual_network_name = azurerm_virtual_network.test.name
      resource_group_name  = data.azurerm_resource_group.rg.name
    }

    data "azurerm_subnet" "appgwsubnet" {
      name                 = "appgwsubnet"
      virtual_network_name = azurerm_virtual_network.test.name
      resource_group_name  = data.azurerm_resource_group.rg.name
    }

    # Public Ip 
    resource "azurerm_public_ip" "test" {
      name                         = "publicIp1"
      location                     = data.azurerm_resource_group.rg.location
      resource_group_name          = data.azurerm_resource_group.rg.name
      allocation_method            = "Static"
      sku                          = "Standard"

      tags = var.tags
    }
    ```

1. Chcete-li vytvořit prostředek aplikační brány, vložte následující blok kódu:

    ```hcl
    resource "azurerm_application_gateway" "network" {
      name                = var.app_gateway_name
      resource_group_name = data.azurerm_resource_group.rg.name
      location            = data.azurerm_resource_group.rg.location

      sku {
        name     = var.app_gateway_sku
        tier     = "Standard_v2"
        capacity = 2
      }

      gateway_ip_configuration {
        name      = "appGatewayIpConfig"
        subnet_id = data.azurerm_subnet.appgwsubnet.id
      }

      frontend_port {
        name = local.frontend_port_name
        port = 80
      }

      frontend_port {
        name = "httpsPort"
        port = 443
      }

      frontend_ip_configuration {
        name                 = local.frontend_ip_configuration_name
        public_ip_address_id = azurerm_public_ip.test.id
      }

      backend_address_pool {
        name = local.backend_address_pool_name
      }

      backend_http_settings {
        name                  = local.http_setting_name
        cookie_based_affinity = "Disabled"
        port                  = 80
        protocol              = "Http"
        request_timeout       = 1
      }

      http_listener {
        name                           = local.listener_name
        frontend_ip_configuration_name = local.frontend_ip_configuration_name
        frontend_port_name             = local.frontend_port_name
        protocol                       = "Http"
      }

      request_routing_rule {
        name                       = local.request_routing_rule_name
        rule_type                  = "Basic"
        http_listener_name         = local.listener_name
        backend_address_pool_name  = local.backend_address_pool_name
        backend_http_settings_name = local.http_setting_name
      }

      tags = var.tags

      depends_on = ["azurerm_virtual_network.test", "azurerm_public_ip.test"]
    }
    ```

1. Chcete-li vytvořit přiřazení rolí, vložte následující blok kódu:

    ```hcl
    resource "azurerm_role_assignment" "ra1" {
      scope                = data.azurerm_subnet.kubesubnet.id
      role_definition_name = "Network Contributor"
      principal_id         = var.aks_service_principal_object_id 

      depends_on = ["azurerm_virtual_network.test"]
    }

    resource "azurerm_role_assignment" "ra2" {
      scope                = azurerm_user_assigned_identity.testIdentity.id
      role_definition_name = "Managed Identity Operator"
      principal_id         = var.aks_service_principal_object_id
      depends_on           = ["azurerm_user_assigned_identity.testIdentity"]
    }

    resource "azurerm_role_assignment" "ra3" {
      scope                = azurerm_application_gateway.network.id
      role_definition_name = "Contributor"
      principal_id         = azurerm_user_assigned_identity.testIdentity.principal_id
      depends_on           = ["azurerm_user_assigned_identity.testIdentity", "azurerm_application_gateway.network"]
    }

    resource "azurerm_role_assignment" "ra4" {
      scope                = data.azurerm_resource_group.rg.id
      role_definition_name = "Reader"
      principal_id         = azurerm_user_assigned_identity.testIdentity.principal_id
      depends_on           = ["azurerm_user_assigned_identity.testIdentity", "azurerm_application_gateway.network"]
    }
    ```

1. Chcete-li vytvořit cluster Kubernetes, vložte následující blok kódu:

    ```hcl
    resource "azurerm_kubernetes_cluster" "k8s" {
      name       = var.aks_name
      location   = data.azurerm_resource_group.rg.location
      dns_prefix = var.aks_dns_prefix

      resource_group_name = data.azurerm_resource_group.rg.name

      linux_profile {
        admin_username = var.vm_user_name

        ssh_key {
          key_data = file(var.public_ssh_key_path)
        }
      }

      addon_profile {
        http_application_routing {
          enabled = false
        }
      }

      default_node_pool {
        name            = "agentpool"
        node_count      = var.aks_agent_count
        vm_size         = var.aks_agent_vm_size
        os_disk_size_gb = var.aks_agent_os_disk_size
        vnet_subnet_id  = data.azurerm_subnet.kubesubnet.id
      }

      service_principal {
        client_id     = var.aks_service_principal_app_id
        client_secret = var.aks_service_principal_client_secret
      }

      network_profile {
        network_plugin     = "azure"
        dns_service_ip     = var.aks_dns_service_ip
        docker_bridge_cidr = var.aks_docker_bridge_cidr
        service_cidr       = var.aks_service_cidr
      }

      depends_on = ["azurerm_virtual_network.test", "azurerm_application_gateway.network"]
      tags       = var.tags
    }

    ```

1. Uložte soubor**&lt;(Ctrl>S**) a ukončete editor**&lt;(Ctrl>Q**).

Kód uvedený v této části nastaví název clusteru, umístění a resource_group_name. Hodnota `dns_prefix` , která je součástí plně kvalifikovaného názvu domény (FQDN) používaného pro přístup ke clusteru, je nastavena.

Záznam `linux_profile` umožňuje konfigurovat nastavení, která umožňují přihlášení do pracovních uzlů pomocí SSH.

Se službou AKS platíte jenom za pracovní uzly. Záznam `agent_pool_profile` konfiguruje podrobnosti pro tyto pracovní uzly. Obsahuje `agent_pool_profile record` počet pracovních uzlů, které mají být vytesány, a typ pracovních uzlů. Pokud potřebujete vertikálně navýšit kapacitu nebo vertikálně navýšit kapacitu clusteru v budoucnu, můžete upravit hodnotu `count` v tomto záznamu.

## <a name="create-a-terraform-output-file"></a>Vytvoření výstupního souboru Terraformu

[Výstupy Terraform](https://www.terraform.io/docs/configuration/outputs.html) umožňují definovat hodnoty, které jsou zvýrazněny pro uživatele, když Terraform použije `terraform output` plán a může být dotazován pomocí příkazu. V této části vytvoříte výstupní soubor, který pomocí [kubectl](https://kubernetes.io/docs/reference/kubectl/overview/) umožňuje přístup ke clusteru.

1. Ve službě Cloud Shell vytvořte soubor s názvem `output.tf`.

    ```bash
    code output.tf
    ```

1. Do editoru vložte následující kód:

    ```hcl
    output "client_key" {
        value = azurerm_kubernetes_cluster.k8s.kube_config.0.client_key
    }

    output "client_certificate" {
        value = azurerm_kubernetes_cluster.k8s.kube_config.0.client_certificate
    }

    output "cluster_ca_certificate" {
        value = azurerm_kubernetes_cluster.k8s.kube_config.0.cluster_ca_certificate
    }

    output "cluster_username" {
        value = azurerm_kubernetes_cluster.k8s.kube_config.0.username
    }

    output "cluster_password" {
        value = azurerm_kubernetes_cluster.k8s.kube_config.0.password
    }

    output "kube_config" {
        value = azurerm_kubernetes_cluster.k8s.kube_config_raw
    }

    output "host" {
        value = azurerm_kubernetes_cluster.k8s.kube_config.0.host
    }

    output "identity_resource_id" {
        value = azurerm_user_assigned_identity.testIdentity.id
    }

    output "identity_client_id" {
        value = azurerm_user_assigned_identity.testIdentity.client_id
    }
    ```

1. Uložte soubor**&lt;(Ctrl>S**) a ukončete editor**&lt;(Ctrl>Q**).

## <a name="configure-azure-storage-to-store-terraform-state"></a>Konfigurace úložiště Azure pro ukládání stavu Terraform

Terraform sleduje stav místně prostřednictvím souboru `terraform.tfstate`. Tento model funguje dobře v prostředí s jednou osobou. V praktičtějším prostředí pro více osob však musíte sledovat stav na serveru pomocí [úložiště Azure](/azure/storage/). V této části se naučíte načíst potřebné informace o účtu úložiště a vytvořit kontejner úložiště. Informace o stavu Terraform jsou pak uloženy v tomto kontejneru.

1. Na webu Azure Portal v části **Služby Azure**vyberte **Účty úložiště**. (Pokud možnost **Účty úložiště** není na hlavní stránce viditelná, vyberte **Další služby** a pak je vyhledejte a vyberte.)

1. Na stránce **Účty úložiště** vyberte název účtu úložiště, do kterého má Terraform ukládat stav. Můžete například použít účet úložiště, který se vytvoří při prvním otevření služby Cloud Shell.  Název účtu úložiště vytvořeného službou Cloud Shell obvykle začíná na `cs` a následuje ho řetězec náhodných čísel a písmen. 

    Poznamenejte si účet úložiště, který vyberete, jak budete později potřebovat.

1. Na stránce účtu úložiště vyberte **Přístupové klíče**.

    ![Nabídka účtu úložiště](./media/terraform-k8s-cluster-appgw-with-tf-aks/storage-account.png)

1. Poznamenejte si hodnotu **klíče** **key1.** (Výběr ikony napravo od klíče zkopíruje příslušnou hodnotu do schránky.)

    ![Přístupové klíče účtu úložiště](./media/terraform-k8s-cluster-appgw-with-tf-aks/storage-account-access-key.png)

1. V Cloud Shellu vytvořte kontejner ve svém účtu úložiště Azure. Nahraďte zástupné symboly příslušnými hodnotami pro váš účet úložiště Azure.

    ```azurecli
    az storage container create -n tfstate --account-name <YourAzureStorageAccountName> --account-key <YourAzureStorageAccountKey>
    ```

## <a name="create-the-kubernetes-cluster"></a>Vytvoření clusteru Kubernetes
V této části zjistíte, jak použít příkaz `terraform init` k vytvoření prostředků definovaných konfiguračními soubory, které jste vytvořili v předchozích částech.

1. V prostředí Cloud Shell inicializujte Terraform. Nahraďte zástupné symboly příslušnými hodnotami pro váš účet úložiště Azure.

    ```bash
    terraform init -backend-config="storage_account_name=<YourAzureStorageAccountName>" -backend-config="container_name=tfstate" -backend-config="access_key=<YourStorageAccountAccessKey>" -backend-config="key=codelab.microsoft.tfstate" 
    ```
  
    Příkaz `terraform init` zobrazuje úspěch inicializace modulu plug-in-in-in-

    ![Příklad výsledků příkazu „terraform init“](./media/terraform-k8s-cluster-appgw-with-tf-aks/terraform-init-complete.png)

1. V prostředí Cloud Shell `terraform.tfvars`vytvořte soubor s názvem :

    ```bash
    code terraform.tfvars
    ```

1. Vložte do editoru následující proměnné vytvořené dříve. Chcete-li získat hodnotu umístění `az account list-locations`pro vaše prostředí, použijte .

    ```hcl
    resource_group_name = "<Name of the Resource Group already created>"

    location = "<Location of the Resource Group>"
      
    aks_service_principal_app_id = "<Service Principal AppId>"
      
    aks_service_principal_client_secret = "<Service Principal Client Secret>"
      
    aks_service_principal_object_id = "<Service Principal Object Id>"
        
    ```

1. Uložte soubor**&lt;(Ctrl>S**) a ukončete editor**&lt;(Ctrl>Q**).

1. Spuštěním příkazu `terraform plan` vytvořte plán Terraformu, který definuje prvky infrastruktury. 

    ```bash
    terraform plan -out out.plan
    ```

    Příkaz `terraform plan` zobrazí prostředky, které jsou vytvořeny `terraform apply` při spuštění příkazu:

    ![Příklad výsledků příkazu „terraform plan“](./media/terraform-k8s-cluster-appgw-with-tf-aks/terraform-plan-complete.png)

1. Spuštěním příkazu `terraform apply` použijte plán a vytvořte cluster Kubernetes. Proces vytvoření clusteru Kubernetes může trvat několik minut, což má za následek vypršení časového limitu prostředí cloudu. Pokud časový čas relace prostředí Cloud, můžete postupovat podle kroků v části "Obnovení z časového úseku cloudového prostředí", abyste mohli dokončit kurz.

    ```bash
    terraform apply out.plan
    ```

    Příkaz `terraform apply` zobrazí výsledky vytvoření prostředků definovaných v konfiguračních souborech:

    ![Příklad výsledků příkazu „terraform apply“](./media/terraform-k8s-cluster-appgw-with-tf-aks/terraform-apply-complete.png)

1. Na webu Azure Portal vyberte **skupiny prostředků** v levé nabídce, abyste viděli prostředky vytvořené pro váš nový cluster Kubernetes ve vybrané skupině prostředků.

    ![Příkazový řádek Cloud Shellu](./media/terraform-k8s-cluster-appgw-with-tf-aks/k8s-resources-created.png)

## <a name="recover-from-a-cloud-shell-timeout"></a>Zotavení z vypršení relace služby Cloud Shell

Pokud časový čas relace prostředí Cloud Shell, můžete použít následující kroky k obnovení:

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

## <a name="install-azure-ad-pod-identity"></a>Instalace identity služby Azure AD Pod

Azure Active Directory Pod Identity poskytuje přístup ke [Správci prostředků Azure](/azure/azure-resource-manager/resource-group-overview)na základě tokenů .

[Azure AD Pod Identity](https://github.com/Azure/aad-pod-identity) přidá do clusteru Kubernetes následující součásti:

  - [Krsované kubernetes](https://kubernetes.io/docs/tasks/access-kubernetes-api/custom-resources/custom-resource-definitions/) `AzureIdentity` `AzureAssignedIdentity`: , ,`AzureIdentityBinding`
  - Součást [Spravovaného řadiče identity (MIC)](https://github.com/Azure/aad-pod-identity#managed-identity-controllermic)
  - Součást [Identity spravované uzlu (NMI)](https://github.com/Azure/aad-pod-identity#node-managed-identitynmi)

Pokud je **rbac povolen**, spusťte následující příkaz pro instalaci identity služby Azure AD Pod do clusteru:

```bash
kubectl create -f https://raw.githubusercontent.com/Azure/aad-pod-identity/master/deploy/infra/deployment-rbac.yaml
```

Pokud je RBAC **zakázán**, spusťte následující příkaz pro instalaci identity služby Azure AD Pod do clusteru:

```bash
kubectl create -f https://raw.githubusercontent.com/Azure/aad-pod-identity/master/deploy/infra/deployment.yaml
```

## <a name="install-helm"></a>Instalace helmu

Kód v této části používá [Helm](/azure/aks/kubernetes-helm) - Kubernetes `application-gateway-kubernetes-ingress` správce balíčků - k instalaci balíčku:

1. Pokud je **povolen**rbac , spusťte následující sadu příkazů pro instalaci a konfiguraci helmu:

    ```bash
    kubectl create serviceaccount --namespace kube-system tiller-sa
    kubectl create clusterrolebinding tiller-cluster-rule --clusterrole=cluster-admin --serviceaccount=kube-system:tiller-sa
    helm init --tiller-namespace kube-system --service-account tiller-sa
    ```

1. Pokud je rbac **zakázán**, spusťte následující příkaz k instalaci a konfiguraci helmu:

    ```bash
    helm init
    ```

1. Přidejte úložiště AGIC Helm:

    ```bash
    helm repo add application-gateway-kubernetes-ingress https://appgwingress.blob.core.windows.net/ingress-azure-helm-package/
    helm repo update
    ```

## <a name="install-ingress-controller-helm-chart"></a>Instalace helmového grafu řadiče příchozího přenosu dat

1. Ke `helm-config.yaml` stažení nakonfigurujete AGIC:

    ```bash
    wget https://raw.githubusercontent.com/Azure/application-gateway-kubernetes-ingress/master/docs/examples/sample-helm-config.yaml -O helm-config.yaml
    ```

1. Upravte `helm-config.yaml` a zadejte `appgw` `armAuth` příslušné hodnoty pro oddíly a.

    ```bash
    code helm-config.yaml
    ```

    Hodnoty jsou popsány následovně:

    - `verbosityLevel`: Nastaví úroveň podrobností infrastruktury protokolování AGIC. Možné hodnoty naleznete [v tématu Úrovně protokolování.](https://github.com/Azure/application-gateway-kubernetes-ingress/blob/463a87213bbc3106af6fce0f4023477216d2ad78/docs/troubleshooting.md#logging-levels)
    - `appgw.subscriptionId`: ID předplatného Azure pro bránu aplikací. Příklad: `a123b234-a3b4-557d-b2df-a0bc12de1234`
    - `appgw.resourceGroup`: Název skupiny prostředků Azure, ve kterém byla vytvořena služba App Gateway. 
    - `appgw.name`: Název aplikační brány. Příklad: `applicationgateway1`.
    - `appgw.shared`: Tento logický příznak by `false`měl být výchozí pro . Nastavte `true` na pokud potřebujete [sdílenou bránu aplikací](https://github.com/Azure/application-gateway-kubernetes-ingress/blob/072626cb4e37f7b7a1b0c4578c38d1eadc3e8701/docs/setup/install-existing.md#multi-cluster--shared-app-gateway).
    - `kubernetes.watchNamespace`: Zadejte obor názvů, který by měl AGIC sledovat. Obor názvů může být hodnota jednoho řetězce nebo seznam oborů názvů oddělených čárkami. Ponechání této proměnné komentované nebo nastavení na prázdný nebo prázdný řetězec má za následek ingress controller pozorování všech přístupných oborů názvů.
    - `armAuth.type`: Hodnota buď `aadPodIdentity` `servicePrincipal`nebo .
    - `armAuth.identityResourceID`: ID prostředku spravované identity.
    - `armAuth.identityClientId`: ID klienta identity.
    - `armAuth.secretJSON`: Je potřeba pouze v případě, `armAuth.type` že je `servicePrincipal`vybrán typ tajného objektu hlavního nastavení služby (pokud byla nastavena na).

    Klíčové poznámky:
    - Hodnota `identityResourceID` je vytvořena ve skriptu terraform a `echo "$(terraform output identity_resource_id)"`lze ji nalézt spuštěním: .
    - Hodnota `identityClientID` je vytvořena ve skriptu terraform a `echo "$(terraform output identity_client_id)"`lze ji nalézt spuštěním: .
    - Hodnota `<resource-group>` je skupina prostředků vaší brány aplikací.
    - Hodnota `<identity-name>` je název vytvořené identity.
    - Všechny identity pro dané předplatné mohou `az identity list`být uvedeny pomocí: .

1. Nainstalujte balíček řadiče příchozího přenosu dat aplikační brány:

    ```bash
    helm install -f helm-config.yaml application-gateway-kubernetes-ingress/ingress-azure
    ```

### <a name="install-a-sample-app"></a>Instalace ukázkové aplikace

Jakmile máte nainstalovanou bránu aplikací, AKS a AGIC, můžete nainstalovat ukázkovou aplikaci přes [Azure Cloud Shell](https://shell.azure.com/):

1. Ke stažení souboru YAML použijte příkaz curl:

    ```bash
    curl https://raw.githubusercontent.com/Azure/application-gateway-kubernetes-ingress/master/docs/examples/aspnetapp.yaml -o aspnetapp.yaml
    ```

2. Použijte soubor YAML:

    ```bash
    kubectl apply -f aspnetapp.yaml
    ```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud již není potřeba, odstraňte prostředky vytvořené v tomto článku.  

Nahraďte zástupný symbol příslušnou hodnotou. Všechny prostředky v rámci zadané skupiny prostředků budou odstraněny.

```azurecli
az group delete -n <resource-group>
```

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"] 
> [Kontroler příchozího přenosu dat služby Application Gateway](https://azure.github.io/application-gateway-kubernetes-ingress/)