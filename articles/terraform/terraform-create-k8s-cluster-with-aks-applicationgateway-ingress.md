---
title: Kurz – vytvoření kontroleru Application Gateway příchozího přenosu ve službě Azure Kubernetes
description: Kurz ilustrující postup vytvoření clusteru Kubernetes pomocí služby Azure Kubernetes Service pomocí Application Gateway jako řadiče pro příchozí přenosy
ms.topic: tutorial
ms.date: 11/13/2019
ms.openlocfilehash: 6d07fc6becf76453de792c69b25aea49c39775ae
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/18/2019
ms.locfileid: "74159089"
---
# <a name="tutorial-create-an-application-gateway-ingress-controller-in-azure-kubernetes-service"></a>Kurz: vytvoření kontroleru Application Gateway příchozího přenosu ve službě Azure Kubernetes

[Azure Kubernetes Service (AKS)](/azure/aks/) spravuje hostované prostředí Kubernetes. AKS umožňuje rychle a snadno nasazovat a spravovat aplikace s využitím kontejnerů bez odbornosti pro orchestraci kontejnerů. AKS také eliminuje zatížení při práci s aplikacemi offline pro úlohy provozu a údržby. Pomocí AKS můžete provádět tyto úlohy – včetně zřizování, upgradování a škálování prostředků – dá se provést na vyžádání.

Kontroler příchozího přenosu dat poskytuje různé funkce pro služby Kubernetes Services. Mezi tyto funkce patří reverzní proxy, konfigurovatelné směrování provozu a ukončení protokolu TLS. Kubernetes příchozí prostředky se používají ke konfiguraci pravidel příchozího přenosu pro jednotlivé služby Kubernetes. Pomocí řadiče příchozího přenosu dat a pravidel příchozího přenosu dat může jedna IP adresa směrovat provoz do několika služeb v clusteru Kubernetes. Tuto funkci poskytuje Azure [Application Gateway](/azure/Application-Gateway/)a díky tomu je ideálním řadičem příchozího přenosu dat pro Kubernetes v Azure. 

V tomto kurzu se naučíte, jak provádět následující úlohy:

> [!div class="checklist"]
> * Vytvořte cluster [Kubernetes](https://www.redhat.com/en/topics/containers/what-is-kubernetes) pomocí AKS s Application Gateway jako adaptér příchozího přenosu dat.
> * Pomocí HCL (HashiCorp Language) definujte cluster Kubernetes.
> * Pomocí Terraformu vytvořit prostředek Application Gateway.
> * K vytvoření clusteru Kubernetes použijte Terraformu a AKS.
> * K otestování dostupnosti clusteru Kubernetes použijte nástroj kubectl.

## <a name="prerequisites"></a>Požadavky

- **Předplatné Azure:** Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) před tím, než začnete.

- **Nakonfigurovaný nástroj Terraform**: Postupujte podle pokynů v článku o [instalaci Terraformu a konfiguraci přístupu k Azure](/azure/virtual-machines/linux/terraform-install-configure).

- **Skupina prostředků Azure**: Pokud ještě nemáte skupinu prostředků Azure, která se má použít pro ukázku, [vytvořte skupinu prostředků Azure](/azure/azure-resource-manager/manage-resource-groups-portal#create-resource-groups). Poznamenejte si název a umístění skupiny prostředků, protože tyto hodnoty jsou použity v ukázce.

- **Instanční objekt Azure:** Postupujte podle pokynů v části **Vytvoření instančního objektu** v článku [Vytvoření instančního objektu Azure pomocí Azure CLI](/cli/azure/create-an-azure-service-principal-azure-cli?view=azure-cli-latest). Poznamenejte si hodnoty pro parametr appId, DisplayName a Password.

- **Získání ID instančního objektu služby**: spusťte následující příkaz v Cloud Shell: `az ad sp list --display-name <displayName>`

## <a name="create-the-directory-structure"></a>Vytvoření struktury adresáře

Prvním krokem je vytvoření adresáře s konfiguračními soubory Terraformu pro toto cvičení.

1. Přejděte na web [Azure Portal](https://portal.azure.com).

1. Otevřete službu [Azure Cloud Shell](/azure/cloud-shell/overview).

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
    code main.tf
    ```

1. Do editoru vložte následující kód:

    ```hcl
    provider "azurerm" {
        version = "~>1.18"
    }

    terraform {
        backend "azurerm" {}
    }
    ```

1. Uložte soubor ( **&lt;ctrl >** ) a ukončete editor ( **&lt;CTRL > Q**).

## <a name="define-input-variables"></a>Definování vstupních proměnných

Vytvořte konfigurační soubor Terraformu, který obsahuje seznam všech proměnných potřebných pro toto nasazení.

1. Ve Cloud Shellu vytvořte soubor s názvem `variables.tf`.

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

1. Uložte soubor ( **&lt;ctrl >** ) a ukončete editor ( **&lt;CTRL > Q**).

## <a name="define-the-resources"></a>Definování prostředků 
Vytvořte konfigurační soubor Terraformu, který vytvoří všechny prostředky. 

1. Ve Cloud Shellu vytvořte soubor s názvem `resources.tf`.

    ```bash
    code resources.tf
    ```

1. Vložte následující blok kódu pro vytvoření bloku lokálních hodnot pro vypočítané proměnné pro opakované použití:

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

1. Vložte následující blok kódu pro vytvoření zdroje dat pro skupinu prostředků, novou identitu uživatele:

    ```hcl
    data "azurerm_resource_group" "rg" {
      name = var.resource_group_name
    }

    # User Assigned Idntities 
    resource "azurerm_user_assigned_identity" "testIdentity" {
      resource_group_name = data.azurerm_resource_group.rg.name
      location            = data.azurerm_resource_group.rg.location

      name = "identity1"

      tags = var.tags
    }
    ```

1. Vložte následující blok kódu k vytvoření základních síťových prostředků:

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

1. Pro vytvoření prostředku Application Gateway vložte následující blok kódu:

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

1. Vložte následující blok kódu pro vytvoření přiřazení rolí:

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

1. Vložte následující blok kódu pro vytvoření clusteru Kubernetes:

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

      agent_pool_profile {
        name            = "agentpool"
        count           = var.aks_agent_count
        vm_size         = var.aks_agent_vm_size
        os_type         = "Linux"
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

1. Uložte soubor ( **&lt;ctrl >** ) a ukončete editor ( **&lt;CTRL > Q**).

Kód uvedený v této části nastaví název clusteru, umístění a resource_group_name. Hodnota `dns_prefix` – ta tvoří část plně kvalifikovaného názvu domény (FQDN), která se používá pro přístup ke clusteru – je nastavena.

Záznam `linux_profile` umožňuje nakonfigurovat nastavení, která umožňují přihlašovat se k pracovním uzlům pomocí SSH.

Se službou AKS platíte jenom za pracovní uzly. Záznam `agent_pool_profile` nakonfiguruje podrobnosti pro tyto pracovní uzly. `agent_pool_profile record` obsahuje počet pracovních uzlů, které se mají vytvořit, a typ pracovních uzlů. Pokud potřebujete v budoucnu horizontální navýšení nebo snížení kapacity clusteru, upravte hodnotu `count` v tomto záznamu.

## <a name="create-a-terraform-output-file"></a>Vytvoření výstupního souboru Terraformu

[Terraformuové výstupy](https://www.terraform.io/docs/configuration/outputs.html) umožňují definovat hodnoty, které jsou zvýrazněné uživateli, když terraformu použije plán, a dá se dotázat pomocí příkazu `terraform output`. V této části vytvoříte výstupní soubor, který pomocí [kubectl](https://kubernetes.io/docs/reference/kubectl/overview/) umožňuje přístup ke clusteru.

1. Ve Cloud Shellu vytvořte soubor s názvem `output.tf`.

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

1. Uložte soubor ( **&lt;ctrl >** ) a ukončete editor ( **&lt;CTRL > Q**).

## <a name="configure-azure-storage-to-store-terraform-state"></a>Konfigurace úložiště Azure pro ukládání stavu Terraformu

Terraform sleduje stav místně prostřednictvím souboru `terraform.tfstate`. Tento model funguje dobře v prostředí s jednou osobou. Ve více praktických prostředích pro více uživatelů je ale potřeba sledovat stav na serveru pomocí služby [Azure Storage](/azure/storage/). V této části se dozvíte, jak načíst potřebné informace o účtu úložiště a vytvořit kontejner úložiště. Informace o stavu Terraformu jsou následně uloženy v tomto kontejneru.

1. V Azure Portal v části **služby Azure**vyberte **účty úložiště**. (Pokud se možnost **účty úložiště** na hlavní stránce nezobrazuje, vyberte **Další služby** a pak ji vyhledejte a vyberte.)

1. Na stránce **účty úložiště** vyberte název účtu úložiště, do kterého má terraformu ukládat stav. Můžete například použít účet úložiště, který se vytvoří při prvním otevření služby Cloud Shell.  Název účtu úložiště vytvořeného službou Cloud Shell obvykle začíná na `cs` a následuje ho řetězec náhodných čísel a písmen. 

    Poznamenejte si vybraný účet úložiště, jak ho budete potřebovat později.

1. Na stránce účtu úložiště vyberte **Přístupové klíče**.

    ![Nabídka účtu úložiště](./media/terraform-k8s-cluster-appgw-with-tf-aks/storage-account.png)

1. Poznamenejte si hodnotu **Klíč** v části **klíč1**. (Výběr ikony napravo od klíče zkopíruje příslušnou hodnotu do schránky.)

    ![Přístupové klíče účtu úložiště](./media/terraform-k8s-cluster-appgw-with-tf-aks/storage-account-access-key.png)

1. V Cloud Shell vytvořte kontejner v účtu úložiště Azure. Zástupné symboly nahraďte odpovídajícími hodnotami pro svůj účet úložiště Azure.

    ```azurecli
    az storage container create -n tfstate --account-name <YourAzureStorageAccountName> --account-key <YourAzureStorageAccountKey>
    ```

## <a name="create-the-kubernetes-cluster"></a>Vytvoření clusteru Kubernetes
V této části zjistíte, jak použít příkaz `terraform init` k vytvoření prostředků definovaných konfiguračními soubory, které jste vytvořili v předchozích částech.

1. V Cloud Shell inicializujte Terraformu. Zástupné symboly nahraďte odpovídajícími hodnotami pro svůj účet úložiště Azure.

    ```bash
    terraform init -backend-config="storage_account_name=<YourAzureStorageAccountName>" -backend-config="container_name=tfstate" -backend-config="access_key=<YourStorageAccountAccessKey>" -backend-config="key=codelab.microsoft.tfstate" 
    ```
  
    Příkaz `terraform init` zobrazuje úspěšnost inicializace modulu plug-in back-end a poskytovatele:

    ![Příklad výsledků příkazu „terraform init“](./media/terraform-k8s-cluster-appgw-with-tf-aks/terraform-init-complete.png)

1. V Cloud Shell vytvořte soubor s názvem `terraform.tfvars`:

    ```bash
    code terraform.tfvars
    ```

1. Do editoru vložte následující proměnné, které jste vytvořili dříve. Pro získání hodnoty umístění pro vaše prostředí použijte `az account list-locations`.

    ```hcl
    resource_group_name = "<Name of the Resource Group already created>"

    location = "<Location of the Resource Group>"
      
    aks_service_principal_app_id = "<Service Principal AppId>"
      
    aks_service_principal_client_secret = "<Service Principal Client Secret>"
      
    aks_service_principal_object_id = "<Service Principal Object Id>"
        
    ```

1. Uložte soubor ( **&lt;ctrl >** ) a ukončete editor ( **&lt;CTRL > Q**).

1. Spuštěním příkazu `terraform plan` vytvořte plán Terraformu, který definuje prvky infrastruktury. 

    ```bash
    terraform plan -out out.plan
    ```

    Příkaz `terraform plan` zobrazuje prostředky, které jsou vytvořeny při spuštění příkazu `terraform apply`:

    ![Příklad výsledků příkazu „terraform plan“](./media/terraform-k8s-cluster-appgw-with-tf-aks/terraform-plan-complete.png)

1. Spuštěním příkazu `terraform apply` použijte plán a vytvořte cluster Kubernetes. Proces vytvoření clusteru Kubernetes může trvat několik minut, což vede k vypršení časového limitu relace Cloud Shell. Pokud Cloud Shell časový limit relace, můžete postupovat podle kroků v části obnovení z Cloud Shell časového limitu, který vám umožní dokončit tento kurz.

    ```bash
    terraform apply out.plan
    ```

    Příkaz `terraform apply` zobrazí výsledky vytvoření prostředků definovaných v konfiguračních souborech:

    ![Příklad výsledků příkazu „terraform apply“](./media/terraform-k8s-cluster-appgw-with-tf-aks/terraform-apply-complete.png)

1. V Azure Portal v nabídce vlevo vyberte **skupiny prostředků** , aby se zobrazily prostředky vytvořené pro nový cluster Kubernetes ve vybrané skupině prostředků.

    ![Příkazový řádek Cloud Shellu](./media/terraform-k8s-cluster-appgw-with-tf-aks/k8s-resources-created.png)

## <a name="recover-from-a-cloud-shell-timeout"></a>Zotavení z vypršení relace služby Cloud Shell

Pokud Cloud Shell časový limit relace, můžete k obnovení použít následující postup:

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

## <a name="install-azure-ad-pod-identity"></a>Nainstalovat službu Azure AD pod identitou

Azure Active Directory pod identitou poskytuje přístup založený na tokenech k [Azure Resource Manager](/azure/azure-resource-manager/resource-group-overview).

[Služba Azure AD pod identitou](https://github.com/Azure/aad-pod-identity) přidá do clusteru Kubernetes následující součásti:

  - Kubernetes [CRDs](https://kubernetes.io/docs/tasks/access-kubernetes-api/custom-resources/custom-resource-definitions/): `AzureIdentity`, `AzureAssignedIdentity``AzureIdentityBinding`
  - Komponenta [spravovaného řadiče identity (MIC)](https://github.com/Azure/aad-pod-identity#managed-identity-controllermic)
  - Komponenta [spravované identity (NMI) uzlů](https://github.com/Azure/aad-pod-identity#node-managed-identitynmi)

Pokud je **povolená**funkce RBAC, spusťte následující příkaz, který do svého clusteru nainstaluje IDENTITU Azure AD pod:

```bash
kubectl create -f https://raw.githubusercontent.com/Azure/aad-pod-identity/master/deploy/infra/deployment-rbac.yaml
```

Pokud je funkce RBAC **zakázaná**, spusťte následující příkaz, který do vašeho clusteru nainstaluje IDENTITU Azure AD pod:

```bash
kubectl create -f https://raw.githubusercontent.com/Azure/aad-pod-identity/master/deploy/infra/deployment.yaml
```

## <a name="install-helm"></a>Nainstalovat Helm

Kód v této části používá správce balíčků [Helm](/azure/aks/kubernetes-helm) -Kubernetes – k instalaci balíčku `application-gateway-kubernetes-ingress`:

1. Pokud je **povolena**akce RBAC, spusťte následující sadu příkazů pro instalaci a konfiguraci Helm:

    ```bash
    kubectl create serviceaccount --namespace kube-system tiller-sa
    kubectl create clusterrolebinding tiller-cluster-rule --clusterrole=cluster-admin --serviceaccount=kube-system:tiller-sa
    helm init --tiller-namespace kube-system --service-account tiller-sa
    ```

1. Pokud je nastavení RBAC **zakázané**, spusťte následující příkaz, který nainstaluje a nakonfiguruje Helm:

    ```bash
    helm init
    ```

1. Přidejte úložiště AGIC Helm:

    ```bash
    helm repo add application-gateway-kubernetes-ingress https://appgwingress.blob.core.windows.net/ingress-azure-helm-package/
    helm repo update
    ```

## <a name="install-ingress-controller-helm-chart"></a>Helm graf instalace adaptéru příchozího přenosu dat

1. Ke konfiguraci AGIC Stáhněte `helm-config.yaml`:

    ```bash
    wget https://raw.githubusercontent.com/Azure/application-gateway-kubernetes-ingress/master/docs/examples/sample-helm-config.yaml -O helm-config.yaml
    ```

1. Upravte `helm-config.yaml` a zadejte příslušné hodnoty pro oddíly `appgw` a `armAuth`.

    ```bash
    code helm-config.yaml
    ```

    Hodnoty jsou popsány takto:

    - `verbosityLevel`: nastaví úroveň podrobností infrastruktury protokolování AGIC. Možné hodnoty najdete v tématu [úrovně protokolování](https://github.com/Azure/application-gateway-kubernetes-ingress/blob/463a87213bbc3106af6fce0f4023477216d2ad78/docs/troubleshooting.md#logging-levels) .
    - `appgw.subscriptionId`: ID předplatného Azure pro službu App Gateway. Příklad: `a123b234-a3b4-557d-b2df-a0bc12de1234`
    - `appgw.resourceGroup`: název skupiny prostředků Azure, ve které se aplikace App Gateway vytvořila. 
    - `appgw.name`: název Application Gateway. Příklad: `applicationgateway1`.
    - `appgw.shared`: Tento logický příznak by měl být nastaven na výchozí hodnotu `false`. Pokud potřebujete [sdílenou bránu aplikace](https://github.com/Azure/application-gateway-kubernetes-ingress/blob/072626cb4e37f7b7a1b0c4578c38d1eadc3e8701/docs/setup/install-existing.md#multi-cluster--shared-app-gateway), nastavte na `true`.
    - `kubernetes.watchNamespace`: zadejte obor názvů, který má AGIC sledovat. Obor názvů může být jediná řetězcová hodnota nebo seznam oborů názvů oddělených čárkami. Tato proměnná se odvolá nebo ji nastaví na prázdnou nebo prázdný řetězec. Výsledkem je, že kontroler příchozího přístupu se všemi dostupnými obory názvů ponechává.
    - `armAuth.type`: hodnota buď `aadPodIdentity` nebo `servicePrincipal`.
    - `armAuth.identityResourceID`: ID prostředku spravované identity.
    - `armAuth.identityClientId`: ID klienta identity.
    - `armAuth.secretJSON`: je potřeba jenom v případě, že je zvolený tajný typ objektu služby (Pokud `armAuth.type` nastavená na `servicePrincipal`).

    Klíčové poznámky:
    - Hodnota `identityResourceID` je vytvořena ve skriptu terraformu a lze ji najít spuštěním: `echo "$(terraform output identity_client_id)"`.
    - Hodnota `identityClientID` je vytvořena ve skriptu terraformu a lze ji najít spuštěním: `echo "$(terraform output identity_resource_id)"`.
    - Hodnota `<resource-group>` je skupina prostředků vaší aplikační brány.
    - Hodnota `<identity-name>` je název vytvořené identity.
    - Všechny identity pro dané předplatné můžou být uvedené pomocí: `az identity list`.

1. Nainstalujte balíček Application Gateway příchozího řadiče pro příchozí přenosy:

    ```bash
    helm install -f helm-config.yaml application-gateway-kubernetes-ingress/ingress-azure
    ```

### <a name="install-a-sample-app"></a>Instalace ukázkové aplikace

Jakmile máte nainstalovanou bránu App Gateway, AKS a AGIC, můžete pomocí [Azure Cloud Shell](https://shell.azure.com/)nainstalovat ukázkovou aplikaci:

1. K stažení souboru YAML použijte příkaz kudrlinkou:

    ```bash
    curl https://raw.githubusercontent.com/Azure/application-gateway-kubernetes-ingress/master/docs/examples/aspnetapp.yaml -o aspnetapp.yaml
    ```

2. Použít soubor YAML:

    ```bash
    kubectl apply -f aspnetapp.yaml
    ```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, odstraňte prostředky vytvořené v tomto článku.  

Zástupný text nahraďte příslušnou hodnotou. Odstraní se všechny prostředky v zadané skupině prostředků.

```bash
az group delete -n <resource-group>
```

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"] 
> [Application Gateway adaptér příchozího přenosu dat](https://azure.github.io/application-gateway-kubernetes-ingress/)