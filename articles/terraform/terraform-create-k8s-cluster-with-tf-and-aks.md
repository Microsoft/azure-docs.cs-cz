---
title: Vytvoření clusteru Kubernetes pomocí služby Azure Kubernetes Service (AKS) a Terraformu
description: Kurz vysvětlující, jak vytvořit cluster Kubernetes pomocí služby Azure Kubernetes Service a Terraformu
services: terraform
ms.service: terraform
keywords: terraform, devops, virtuální počítač, azure, kubernetes
author: tomarchermsft
manager: jeconnoc
ms.author: tarcher
ms.topic: tutorial
ms.date: 12/04/2018
ms.openlocfilehash: f8047960c1e52d77534742d85448ef566f851811
ms.sourcegitcommit: ba035bfe9fab85dd1e6134a98af1ad7cf6891033
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/01/2019
ms.locfileid: "55567430"
---
# <a name="create-a-kubernetes-cluster-with-azure-kubernetes-service-and-terraform"></a>Vytvoření clusteru Kubernetes pomocí služby Azure Kubernetes Service a Terraformu
[Azure Kubernetes Service (AKS)](/azure/aks/) spravuje hostované prostředí Kubernetes a umožňuje rychle a snadno nasazovat a spravovat kontejnerizované aplikace bez znalosti orchestrace kontejnerů. Zároveň eliminuje režii spojenou s probíhajícími operacemi a údržbou díky zřizování, upgradování a škálování prostředků na vyžádání bez nutnosti odpojovat aplikace.

V tomto kurzu se dozvíte, jak provedením následujících úkolů vytvořit pomocí [Terraformu](http://terraform.io) a AKS cluster [Kubernetes](https://www.redhat.com/en/topics/containers/what-is-kubernetes):

> [!div class="checklist"]
> * Použití HCL (Jazyk společnosti HashiCorp) k definování clusteru Kubernetes
> * Použití Terraformu a AKS k vytvoření clusteru Kubernetes
> * Použití nástroje kubectl k otestování dostupnosti clusteru Kubernetes

## <a name="prerequisites"></a>Požadavky

- **Předplatné Azure**: Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) před tím, než začnete.

- **Nakonfigurujte Terraform**: Postupujte podle pokynů v článku, [Terraform a konfigurovat přístup k Azure](/azure/virtual-machines/linux/terraform-install-configure)

- **Instanční objekt Azure**: Postupujte podle pokynů v části **vytvořit instanční objekt** části tohoto článku věnované [vytvoření instančního objektu Azure pomocí rozhraní příkazového řádku Azure](/cli/azure/create-an-azure-service-principal-azure-cli?view=azure-cli-latest#create-the-service-principal). Poznamenejte si hodnoty appId, displayName, password a tenant.

## <a name="create-the-directory-structure"></a>Vytvoření struktury adresáře
Prvním krokem je vytvoření adresáře s konfiguračními soubory Terraformu pro toto cvičení.

1. Přejděte na web [Azure Portal](http://portal.azure.com).

1. Otevřete službu [Azure Cloud Shell](/azure/cloud-shell/overview). Pokud jste prostředí ještě nevybrali, vyberte prostředí **Bash**.

    ![Příkazový řádek Cloud Shellu](./media/terraform-create-k8s-cluster-with-tf-and-aks/azure-portal-cloud-shell-button-min.png)

1. Přejděte do adresáře `clouddrive`.

    ```bash
    cd clouddrive
    ```

1. Vytvořte adresář s názvem `terraform-aks-k8s`.

    ```bash
    mkdir terraform-aks-k8s
    ```

1. Přejděte do nového adresáře:

    ```bash
    cd terraform-aks-k8s
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
        version = "~>1.5"
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

## <a name="define-a-kubernetes-cluster"></a>Definování clusteru Kubernetes
Vytvořte konfigurační soubor Terraformu, který deklaruje prostředky pro cluster Kubernetes.

1. Ve službě Cloud Shell vytvořte soubor s názvem `k8s.tf`.

    ```bash
    vi k8s.tf
    ```

1. Stisknutím klávesy I přejděte do režimu vkládání.

1. Do editoru vložte následující kód:

    ```JSON
    resource "azurerm_resource_group" "k8s" {
        name     = "${var.resource_group_name}"
        location = "${var.location}"
    }

    resource "azurerm_log_analytics_workspace" "test" {
        name                = "${var.log_analytics_workspace_name}"
        location            = "${var.log_analytics_workspace_location}"
        resource_group_name = "${azurerm_resource_group.k8s.name}"
        sku                 = "${var.log_analytics_workspace_sku}"
    }

    resource "azurerm_log_analytics_solution" "test" {
        solution_name         = "ContainerInsights"
        location              = "${azurerm_log_analytics_workspace.test.location}"
        resource_group_name   = "${azurerm_resource_group.k8s.name}"
        workspace_resource_id = "${azurerm_log_analytics_workspace.test.id}"
        workspace_name        = "${azurerm_log_analytics_workspace.test.name}"

        plan {
            publisher = "Microsoft"
            product   = "OMSGallery/ContainerInsights"
        }
    }

    resource "azurerm_kubernetes_cluster" "k8s" {
        name                = "${var.cluster_name}"
        location            = "${azurerm_resource_group.k8s.location}"
        resource_group_name = "${azurerm_resource_group.k8s.name}"
        dns_prefix          = "${var.dns_prefix}"

        linux_profile {
            admin_username = "ubuntu"

            ssh_key {
                key_data = "${file("${var.ssh_public_key}")}"
            }
        }

        agent_pool_profile {
            name            = "agentpool"
            count           = "${var.agent_count}"
            vm_size         = "Standard_DS1_v2"
            os_type         = "Linux"
            os_disk_size_gb = 30
        }

        service_principal {
            client_id     = "${var.client_id}"
            client_secret = "${var.client_secret}"
        }

        addon_profile {
            oms_agent {
            enabled                    = true
            log_analytics_workspace_id = "${azurerm_log_analytics_workspace.test.id}"
            }
        }

        tags {
            Environment = "Development"
        }
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

## <a name="declare-the-variables"></a>Deklarování proměnných

1. Ve službě Cloud Shell vytvořte soubor s názvem `variables.tf`.

    ```bash
    vi variables.tf
    ```

1. Stisknutím klávesy I přejděte do režimu vkládání.

1. Do editoru vložte následující kód:

    ```JSON
    variable "client_id" {}
    variable "client_secret" {}

    variable "agent_count" {
        default = 3
    }

    variable "ssh_public_key" {
        default = "~/.ssh/id_rsa.pub"
    }

    variable "dns_prefix" {
        default = "k8stest"
    }

    variable cluster_name {
        default = "k8stest"
    }

    variable resource_group_name {
        default = "azure-k8stest"
    }

    variable location {
        default = "Central US"
    }

    variable log_analytics_workspace_name {
        default = "testLogAnalyticsWorkspaceName"
    }

    # refer https://azure.microsoft.com/global-infrastructure/services/?products=monitor for log analytics available regions
    variable log_analytics_workspace_location {
        default = "eastus"
    }

   # refer https://azure.microsoft.com/pricing/details/monitor/ for log analytics pricing 
   variable log_analytics_workspace_sku {
        default = "PerGB2018"
   }
    ```

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
Terraform sleduje stav místně prostřednictvím souboru `terraform.tfstate`. Tento model funguje dobře v prostředí s jednou osobou. V praktičtějším prostředí s více osobami ale musíte stav na serveru sledovat pomocí [Azure Storage](/azure/storage/). V této části získáte potřebné informace o účtu úložiště (název účtu a klíč účtu) a vytvoříte kontejner úložiště, do kterého se uloží informace o stavu Terraformu.

1. Na webu Azure Portal vyberte v levé nabídce **Všechny služby**.

1. Vyberte **Účty úložiště**.

1. Na kartě **Účty úložiště** vyberte název účtu úložiště, do kterého má Terraform ukládat stav. Můžete například použít účet úložiště, který se vytvoří při prvním otevření služby Cloud Shell.  Název účtu úložiště vytvořeného službou Cloud Shell obvykle začíná na `cs` a následuje ho řetězec náhodných čísel a písmen. **Název vybraného účtu úložiště si zapamatujte, budete ho později potřebovat.**

1. Na kartě účtu úložiště vyberte **Přístupové klíče**.

    ![Nabídka účtu úložiště](./media/terraform-create-k8s-cluster-with-tf-and-aks/storage-account.png)

1. Poznamenejte si hodnotu **Klíč** v části **klíč1**. (Výběr ikony napravo od klíče zkopíruje příslušnou hodnotu do schránky.)

    ![Přístupové klíče účtu úložiště](./media/terraform-create-k8s-cluster-with-tf-and-aks/storage-account-access-key.png)

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

    ![Příklad výsledků příkazu „terraform init“](./media/terraform-create-k8s-cluster-with-tf-and-aks/terraform-init-complete.png)

1. Exportujte své přihlašovací údaje instančního objektu. Nahraďte zástupné hodnoty &lt;your-client-id > a &lt;your-client-secret > hodnotami **appId** a **password** (heslo) přidruženými k vašemu instančnímu objektu.

    ```bash
    export TF_VAR_client_id=<your-client-id>
    export TF_VAR_client_secret=<your-client-secret>
    ```

1. Spuštěním příkazu `terraform plan` vytvořte plán Terraformu, který definuje prvky infrastruktury. 

    ```bash
    terraform plan -out out.plan
    ```

    Příkaz `terraform plan` zobrazí prostředky, které se po spuštění příkazu `terraform apply` vytvoří:

    ![Příklad výsledků příkazu „terraform plan“](./media/terraform-create-k8s-cluster-with-tf-and-aks/terraform-plan-complete.png)

1. Spuštěním příkazu `terraform apply` použijte plán a vytvořte cluster Kubernetes. Vytvoření clusteru Kubernetes může trvat několik minut a relace služby Cloud Shell může vypršet. Pokud relace služby Cloud Shell vyprší, můžete postupovat podle kroků v části [Zotavení z vypršení relace služby Cloud Shell](#recover-from-a-dloud-shell-timeout), které vám umožní kurz dokončit.

    ```bash
    terraform apply out.plan
    ```

    Příkaz `terraform apply` zobrazí výsledky vytvoření prostředků definovaných v konfiguračních souborech:

    ![Příklad výsledků příkazu „terraform apply“](./media/terraform-create-k8s-cluster-with-tf-and-aks/terraform-apply-complete.png)

1. Na webu Azure Portal, vyberte **všechny služby** v levé nabídce zobrazíte prostředky vytvořené pro nový cluster Kubernetes.

    ![Příkazový řádek Cloud Shellu](./media/terraform-create-k8s-cluster-with-tf-and-aks/k8s-resources-created.png)

## <a name="recover-from-a-cloud-shell-timeout"></a>Zotavení z vypršení relace služby Cloud Shell
Pokud vyprší časový limit relace služby Cloud Shell, můžete postupovat následovně:

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

    ![Nástroj kubectl vám umožňuje ověřit stav clusteru Kubernetes.](./media/terraform-create-k8s-cluster-with-tf-and-aks/kubectl-get-nodes.png)

## <a name="monitor-health-and-logs"></a>Monitorování stavu a protokolů
Při vytvoření clusteru AKS se povolilo monitorování za účelem zachycování metrik stavu podů i uzlů clusteru. Tyto metriky stavu jsou k dispozici na webu Azure Portal. Další informace o monitorování stavu kontejneru, naleznete v tématu [monitorování Azure Kubernetes Service health](https://docs.microsoft.com/azure/azure-monitor/insights/container-insights-overview).

## <a name="next-steps"></a>Další postup
V tomto článku jste zjistili, jak pomocí Terraformu a AKS vytvořit cluster Kubernetes. Pokud chcete o nástroji Terraform v Azure získat více informací, můžou vám pomoct následující prostředky: 

 [Dokumentace k Terraformu v Azure](https://docs.microsoft.com/azure/terraform/)  
 [Dokumentace k Terraformu zprostředkovatele Azure](https://aka.ms/terraform)  
 [Zdrojová data k Terraformu zprostředkovatele Azure](https://aka.ms/tfgit)  
 [Moduly Terraformu pro Azure](https://aka.ms/tfmodules)