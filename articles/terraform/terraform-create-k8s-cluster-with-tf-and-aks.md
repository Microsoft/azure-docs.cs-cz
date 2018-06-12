---
title: Vytvoření clusteru s podporou Kubernetes s Azure Kubernetes služby (AKS) a Terraform
description: Kurz ilustrující postup vytvoření clusteru s podporou Kubernetes službou Azure Kubernetes a Terraform
keywords: terraform, devops, virtuální počítač, azure, kubernetes
author: tomarcher
manager: jeconnoc
ms.author: tarcher
ms.date: 06/11/2018
ms.topic: article
ms.openlocfilehash: bd00a0cc8446802a03570edd58949a46c0769101
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/11/2018
ms.locfileid: "35303500"
---
# <a name="create-a-kubernetes-cluster-with-azure-kubernetes-service-and-terraform"></a>Vytvoření clusteru s podporou Kubernetes službou Azure Kubernetes a Terraform
[Služba Azure Kubernetes (AKS)](/azure/aks/) spravuje hostovaného prostředí Kubernetes, což rychlé a snadné ho nasadit a spravovat kontejnerové aplikace bez znalosti orchestration kontejneru. Zároveň eliminuje režii spojenou s probíhajícími operacemi a údržbou díky zřizování, upgradování a škálování prostředků na vyžádání bez nutnosti odpojovat aplikace.

V tomto kurzu zjistíte, jak provést následující úlohy při vytváření [Kubernetes](https://www.redhat.com/en/topics/containers/what-is-kubernetes) clusteru pomocí [Terraform](http://terraform.io) a AKS:

> [!div class="checklist"]
> * Použijte k definování Kubernetes cluster kompatibilního hardwaru (HashiCorp Language)
> * Použít k vytvoření clusteru s podporou Kubernetes Terraform a AKS
> * Test dostupnosti Kubernetes clusteru pomocí nástroje kubectl

## <a name="prerequisites"></a>Požadavky

- **Předplatné Azure:** Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) před tím, než začnete.

- **Konfigurace Terraform**: postupujte podle pokynů v článku, [Terraform a konfigurace přístupu k Azure](/azure/virtual-machines/linux/terraform-install-configure)

- **Objekt zabezpečení služby Azure**: postupujte podle pokynů v části **vytvořit objekt služby** v článku, [vytvořit objekt služby Azure pomocí Azure CLI 2.0](/cli/azure/create-an-azure-service-principal-azure-cli?view=azure-cli-latest#create-the-service-principal). Povšimněte si hodnoty pro appId, displayName, heslo a klienta.

## <a name="create-the-directory-structure"></a>Vytvořit strukturu adresáře
Prvním krokem je vytvoření adresáře, který obsahuje konfigurační soubory Terraform pro výkon.

1. Vyhledejte [portál Azure](http://portal.azure.com).

1. Otevřete [prostředí cloudu Azure](/azure/cloud-shell/overview). Pokud jste nevybrali prostředí dříve, vyberte **Bash** jako vaše prostředí.

    ![Řádku prostředí cloudu](./media/terraform-create-k8s-cluster-with-tf-and-aks/azure-portal-cloud-shell-button-min.png)

1. Přejděte do adresáře `clouddrive` adresáře.

    ```bash
    cd clouddrive
    ```

1. Vytvořte adresář s názvem `terraform-aks-k8s`.

    ```bash
    mkdir terraform-aks-k8s
    ```

1. Změňte adresáře do nového adresáře:

    ```bash
    cd terraform-aks-k8s
    ```

## <a name="declare-the-azure-provider"></a>Deklarovat Azure zprostředkovatele
Vytvořte Terraform konfigurační soubor, který deklaruje Azure zprostředkovatele.

1. V prostředí cloudu, vytvořte soubor s názvem `main.tf`.

    ```bash
    vi main.tf
    ```

1. Zadejte režim vložení výběrem I klíč.

1. Vložte následující kód do editoru:

    ```JSON
    provider "azurerm" {
        version = "=1.5.0"
    }

    terraform {
        backend "azurerm" {}
    }

    ```

1. Ukončit režim vložení výběrem **Esc** klíč.

1. Uložte tento soubor a ukončete vi editor tak, že zadáte následující příkaz:

    ```bash
    :wq
    ```

## <a name="define-a-kubernetes-cluster"></a>Definování Kubernetes clusteru
Vytvořte Terraform konfigurační soubor, který deklaruje prostředky pro Kubernetes clusteru.

1. V prostředí cloudu, vytvořte soubor s názvem `k8s.tf`.

    ```bash
    vi k8s.tf
    ```

1. Zadejte režim vložení výběrem I klíč.

1. Vložte následující kód do editoru:

    ```JSON
    resource "azurerm_resource_group" "k8s" {
        name     = "${var.resource_group_name}"
        location = "${var.location}"
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
            name            = "default"
            count           = "${var.agent_count}"
            vm_size         = "Standard_D2"
            os_type         = "Linux"
            os_disk_size_gb = 30
        }

        service_principal {
            client_id     = "${var.client_id}"
            client_secret = "${var.client_secret}"
        }

        tags {
            Environment = "Development"
        }
    }
    ```

    Předchozí kód nastaví název clusteru, umístění a resource_group_name. Kromě toho je nastavena hodnota dns_prefix - součástí plně kvalifikovaný název domény (FQDN) používá pro přístup ke clusteru.

    **Linux_profile** záznam umožňuje nakonfigurovat nastavení, které umožňují přihlášení k pracovním uzlům pomocí protokolu SSH.

    S AKS platí pouze pro uzly pracovního procesu. **Agent_pool_profile** záznam nakonfiguruje podrobnosti pro tyto uzly pracovního procesu. **Agent_pool_profile záznam** zahrnuje počet uzlů pracovního procesu k vytvoření a typ uzlů pracovního procesu. Pokud potřebujete škálovat nahoru i dolů clusteru v budoucnosti, můžete upravit **počet** hodnotu v tomto záznamu.

1. Ukončit režim vložení výběrem **Esc** klíč.

1. Uložte tento soubor a ukončete vi editor tak, že zadáte následující příkaz:

    ```bash
    :wq
    ```

## <a name="declare-the-variables"></a>Deklarace proměnné

1. V prostředí cloudu, vytvořte soubor s názvem `variables.tf`.

    ```bash
    vi variables.tf
    ```

1. Zadejte režim vložení výběrem I klíč.

1. Vložte následující kód do editoru:

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
    ```

1. Ukončit režim vložení výběrem **Esc** klíč.

1. Uložte tento soubor a ukončete vi editor tak, že zadáte následující příkaz:

    ```bash
    :wq
    ```

## <a name="create-a-terraform-output-file"></a>Vytvoření výstupního souboru Terraform
[Výstupy Terraform](https://www.terraform.io/docs/configuration/outputs.html) umožňují definovat hodnoty, které budou mít zvýrazněná uživateli, když Terraform platí plánu a lze dotazovat pomocí `terraform output` příkaz. V této části vytvoříte výstupní soubor, který umožňuje přístup ke clusteru s [kubectl](https://kubernetes.io/docs/reference/kubectl/overview/).

1. V prostředí cloudu, vytvořte soubor s názvem `output.tf`.

    ```bash
    vi output.tf
    ```

1. Zadejte režim vložení výběrem I klíč.

1. Vložte následující kód do editoru:

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

1. Ukončit režim vložení výběrem **Esc** klíč.

1. Uložte tento soubor a ukončete vi editor tak, že zadáte následující příkaz:

    ```bash
    :wq
    ```

## <a name="set-up-azure-storage-to-store-terraform-state"></a>Nastavení úložiště Azure pro uložení stavu Terraform
Terraform sleduje stav místně prostřednictvím `terraform.tfstate` souboru. Tento vzor dobře funguje v prostředí s jednou osobou. Ale v prostředí s více praktické více osobami, musíte ke sledování stavu na serveru využívá [úložiště Azure](/azure/storage/). V této části načíst nezbytné informace o účtu úložiště (název účtu a klíč účtu) a vytvořit kontejner úložiště, do kterého se uloží informace o stavu Terraform.

1. Na portálu Azure vyberte **všechny služby** v levé nabídce.

1. Vyberte **účty úložiště**.

1. Na **účty úložiště** , vyberte název účtu úložiště, do kterého je Terraform k uložení stavu. Například můžete použít účet úložiště vytvoří, když jste otevřeli cloudové prostředí poprvé.  Název účtu úložiště vytvořené cloudové prostředí obvykle začíná `cs` následuje náhodný řetězec číslic a písmen. **Pamatovat název účtu úložiště, kterou vyberete, protože je potřeba později.**

1. Na kartě účet úložiště vyberte **přístupové klíče**.

    ![Nabídce účtů úložiště](./media/terraform-create-k8s-cluster-with-tf-and-aks/storage-account.png)

1. Poznamenejte si **key1** **klíč** hodnotu. (Výběrem ikony napravo od klíč zkopírujete příslušnou hodnotu do schránky.)

    ![Přístupových klíčů k účtu úložiště](./media/terraform-create-k8s-cluster-with-tf-and-aks/storage-account-access-key.png)

1. V prostředí cloudu, vytvořit kontejner ve vašem účtu úložiště Azure (Nahraďte &lt;YourAzureStorageAccountName > a &lt;YourAzureStorageAccountAccessKey > zástupné symboly s příslušnými hodnotami pro váš účet úložiště Azure ).

    ```bash
    az storage container create -n tfstate --account-name <YourAzureStorageAccountName> --account-key <YourAzureStorageAccountKey>
    ```

## <a name="create-the-kubernetes-cluster"></a>Vytvoření clusteru Kubernetes
V této části najdete postup použití `terraform init` příkaz pro vytvoření prostředků definovaný konfigurační soubory, které jste vytvořili v předchozí části.

1. V prostředí cloudu inicializovat Terraform (Nahraďte &lt;YourAzureStorageAccountName > a &lt;YourAzureStorageAccountAccessKey > zástupné symboly s příslušnými hodnotami pro váš účet úložiště Azure).

    ```bash
    terraform init -backend-config="storage_account_name=<YourAzureStorageAccountName>" -backend-config="container_name=tfstate" -backend-config="access_key=<YourStorageAccountAccessKey>" -backend-config="key=codelab.microsoft.tfstate" 
    ```
    
    `terraform init` Příkaz zobrazí úspěchu při inicializaci modulu plug-in back-end a zprostředkovatele:

    ![Příklad výsledků "terraform init"](./media/terraform-create-k8s-cluster-with-tf-and-aks/terraform-init-complete.png)

1. Spustit `terraform plan` příkaz pro vytvoření Terraform plán, který definuje prvky infrastruktury. Příkaz bude požadovat dvou hodnot: **var.client_id** a **var.client_secret**. Pro **var.client_id** proměnnou, zadejte **appId** hodnotu přidruženou instanční objekt. Pro **var.client_secret** proměnnou, zadejte **heslo** hodnotu přidruženou instanční objekt.

    ```bash
    terraform plan -out out.plan
    ```

    `terraform plan` Příkaz zobrazí prostředky, které se vytvoří při spuštění `terraform apply` příkaz:

    ![Příklad výsledků "plán terraform"](./media/terraform-create-k8s-cluster-with-tf-and-aks/terraform-plan-complete.png)

1. Spustit `terraform apply` příkaz, který chcete vytvořit Kubernetes cluster použít. Postup vytvoření clusteru s podporou Kubernetes může trvat několik minut, výsledkem je relaci prostředí cloudu vypršení časového limitu. Pokud vyprší časový limit relace cloudové prostředí, můžete podle kroků v části ["Zotavit vypršení časového limitu cloudové prostředí"](#recover-from-a-dloud-shell-timeout) umožnit k dokončení tohoto kurzu.

    ```bash
    terraform apply out.plan
    ```

    `terraform apply` Příkaz zobrazí výsledky vytváření prostředků definované v konfiguračních souborech:

    ![Příklad výsledků "terraform použijí"](./media/terraform-create-k8s-cluster-with-tf-and-aks/terraform-apply-complete.png)

1. Na portálu Azure vyberte **všechny služby** v levé nabídce zobrazíte prostředky vytvořené pro nový cluster Kubernetese.

    ![Řádku prostředí cloudu](./media/terraform-create-k8s-cluster-with-tf-and-aks/k8s-resources-created.png)

## <a name="recover-from-a-cloud-shell-timeout"></a>Obnovit z vypršení časového limitu cloudové prostředí
Pokud vyprší časový limit relace cloudové prostředí, provedením následujících kroků k obnovení:

1. Spusťte relaci prostředí cloudu.

1. Přejděte do adresáře obsahující Terraform konfiguračních souborů.

    ```bash
    cd /clouddrive/terraform-aks-k8s
    ```

1. Spusťte následující příkaz:

    ```bash
    export KUBECONFIG=./azurek8s
    ```
    
## <a name="test-the-kubernetes-cluster"></a>Testování Kubernetes clusteru
Kubernetes nástroje můžete použít k ověření nově vytvořený cluster.

1. Získání konfigurace Kubernetes ze stavu Terraform a že kubectl uložit do souboru může číst.

    ```bash
    echo "$(terraform output kube_config)" > ./azurek8s
    ```

1. Nastavte proměnnou prostředí tak, aby kubectl převezme správné konfigurace.

    ```bash
    export KUBECONFIG=./azurek8s
    ```

1. Zkontrolujte stav clusteru.

    ```bash
    kubectl get nodes
    ```

    Zobrazí podrobnosti o pracovní uzly, a musí mít stav **připraven**, jak je znázorněno na následujícím obrázku:

    ![Nástroj kubectl umožňuje ověření stavu Kubernetes clusteru](./media/terraform-create-k8s-cluster-with-tf-and-aks/kubectl-get-nodes.png)

## <a name="next-steps"></a>Další postup
V tomto článku jste zjistili, jak používat Terraform a AKS k vytvoření clusteru s podporou Kubernetes. Zde jsou některé další zdroje, které obsahují další informace o Terraform v Azure: 

 [Centrum Terraform v doméně Microsoft.com](https://docs.microsoft.com/azure/terraform/)  
 [Dokumentaci k Terraform Azure zprostředkovatele](http://aka.ms/terraform)  
 [Zprostředkovatel zdroje Terraform Azure](http://aka.ms/tfgit)  
 [Moduly Terraform Azure](http://aka.ms/tfmodules)