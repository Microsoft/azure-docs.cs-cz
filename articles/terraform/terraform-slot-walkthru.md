---
title: Terraform s Azure zprostředkovatele nasazovací slot.
description: Terraform s kurz slotu nasazení Azure zprostředkovatele
keywords: terraform, devops, virtuální počítač Azure, nasazovací sloty
author: tomarcher
manager: jeconnoc
ms.author: tarcher
ms.date: 4/05/2018
ms.topic: article
ms.openlocfilehash: 34b16b5fb2b5b574d166693db346ebba15eaa1f9
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2018
---
# <a name="using-terraform-to-provision-infrastructure-with-azure-deployment-slots"></a>Pomocí Terraform zřízení infrastruktury s slotů nasazení Azure

[Sloty nasazení Azure](/azure/app-service/web-sites-staged-publishing) umožňují Prohodit mezi různými verzemi aplikace – například provozní prostředí a Fázování – Chcete-li minimalizovat dopad porušený nasazení. Tento článek ukazuje příklad použití nasazovací sloty rámci můžete prostřednictvím nasazení dvou aplikací prostřednictvím Githubu a Azure. Jednu aplikaci je hostovaná ve "produkční slot", zatímco druhý aplikace je hostován v "pracovní" slot. (Názvy "výroba" a "pracovní" jsou libovolný a může být jakýkoli chcete, která představuje váš scénář.) Po byly nakonfigurovány nasazovací sloty, pak můžete Terraform se mezi dvěma sloty Prohodit podle potřeby.

## <a name="prerequisites"></a>Požadavky

- **Předplatné Azure** – Pokud nemáte předplatné Azure, vytvořte [bezplatný účet](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) před zahájením.

- **Účet GitHub** – [Githubu](http://www.github.com) účet je nutná k rozvětvovat a použití testu úložiště GitHub.

## <a name="create-and-apply-the-terraform-plan"></a>Vytvoření a použití plánu Terraform

1. Vyhledejte [portálu Azure](http://portal.azure.com)

1. Otevřete [prostředí cloudu Azure](/azure/cloud-shell/overview)a pokud ještě není inicializovaná - vyberte **Bash** jako vaše prostředí.

    ![Řádku prostředí cloudu](./media/terraform-slot-walkthru/azure-portal-cloud-shell-button-min.png)

1. Přejděte do adresáře `clouddrive` adresáře.

    ```bash
    cd clouddrive
    ```

1. Vytvořte adresář s názvem `deploy`.

    ```bash
    mkdir deploy
    ```

1. Vytvořte adresář s názvem `swap`.

    ```bash
    mkdir swap
    ```

1. Ověřte, že oba adresáře byly úspěšně vytvořeny pomocí `ls` bash příkaz.

    ![Cloudové prostředí po vytvoření adresáře](./media/terraform-slot-walkthru/cloud-shell-after-creating-dirs.png)

1. Přejděte do adresáře `deploy` adresáře.

    ```bash
    cd deploy
    ```

1. Pomocí [vi editor](https://www.debian.org/doc/manuals/debian-tutorial/ch-editor.html), vytvořte soubor s názvem `deploy.tf`, který bude obsahovat [Terraform konfigurace](https://www.terraform.io/docs/configuration/index.html).

    ```bash
    vi deploy.tf
    ```

1. Zadejte režim vložení stisknutím písmeno `i` klíč.

1. Vložte následující kód do editoru:

    ```JSON
    # Configure the Azure Provider
    provider "azurerm" { }

    resource "azurerm_resource_group" "slotDemo" {
        name = "slotDemoResourceGroup"
        location = "westus2"
    }

    resource "azurerm_app_service_plan" "slotDemo" {
        name                = "slotAppServicePlan"
        location            = "${azurerm_resource_group.slotDemo.location}"
        resource_group_name = "${azurerm_resource_group.slotDemo.name}"
        sku {
            tier = "Standard"
            size = "S1"
        }
    }

    resource "azurerm_app_service" "slotDemo" {
        name                = "slotAppService"
        location            = "${azurerm_resource_group.slotDemo.location}"
        resource_group_name = "${azurerm_resource_group.slotDemo.name}"
        app_service_plan_id = "${azurerm_app_service_plan.slotDemo.id}"
    }

    resource "azurerm_app_service_slot" "slotDemo" {
        name                = "slotAppServiceSlotOne"
        location            = "${azurerm_resource_group.slotDemo.location}"
        resource_group_name = "${azurerm_resource_group.slotDemo.name}"
        app_service_plan_id = "${azurerm_app_service_plan.slotDemo.id}"
        app_service_name    = "${azurerm_app_service.slotDemo.name}"
    }
    ```

1. Stiskněte  **&lt;Esc >** klíč pro ukončení režimu vkládání.

1. Uložte tento soubor a ukončete vi editor tak, že zadáte následující příkaz, a potom stisknutím  **&lt;Enter >**:

    ```bash
    :wq
    ```

1. Po vytvoření souboru můžete ověřit její obsah.

    ```bash
    cat deploy.tf
    ```

1. Inicializujte Terraform.

    ```bash
    terraform init
    ```

1. Vytvořte plán Terraform.

    ```bash
    terraform plan
    ```

1. Zřízení prostředků definovaný v `deploy.tf` konfigurační soubor. (Tuto akci potvrďte zadáním `yes` příkazového řádku.)

    ```bash
    terraform apply
    ```

1. Zavřete okno cloudové prostředí.

1. Na Azure portálu hlavní nabídky, vyberte **skupiny prostředků**.

    ![Portál Azure skupiny prostředků](./media/terraform-slot-walkthru/resource-groups-menu-option.png)

1. Na **skupiny prostředků** vyberte **slotDemoResourceGroup**.

    ![Skupiny prostředků vytvořené Terraform](./media/terraform-slot-walkthru/resource-group.png)

Po dokončení se zobrazí všechny prostředky, které jsou vytvořené Terraform.

![Prostředky, které jsou vytvořené Terraform](./media/terraform-slot-walkthru/resources.png)

## <a name="fork-the-test-project"></a>Rozvětvení k testovacímu projektu

Před vytvořením a vzájemná záměna směřující nasazovací sloty můžete otestovat, budete muset rozvětvit k testovacímu projektu z Githubu.

1. Vyhledejte [Super terraform úložišti na Githubu](https://github.com/Azure/awesome-terraform).

1. Rozvětvení **úložišti Super terraform**.

    ![Rozvětvení Super terraform úložiště GitHub](./media/terraform-slot-walkthru/fork-repo.png)

1. Postupujte podle výzev, chcete-li rozvětvit pro vaše prostředí.

## <a name="deploy-from-github-to-your-deployment-slots"></a>Nasazení z webu GitHub na nasazovací sloty

Jednou rozvětvení úložišti projekt testu, nakonfigurujte nasazovací sloty prostřednictvím následujících kroků:

1. Na Azure portálu hlavní nabídky, vyberte **skupiny prostředků**.

1. Vyberte **slotDemoResourceGroup**.

1. Vyberte **slotAppService**.

1. Vyberte **možnosti nasazení**.

    ![Možnosti nasazení pro prostředek služby App Service](./media/terraform-slot-walkthru/deployment-options.png)

1. Na **možnost nasazení** vyberte **zvolit zdroj**a potom vyberte **Githubu**.

    ![Vyberte zdroj nasazení.](./media/terraform-slot-walkthru/select-source.png)

1. Jakmile Azure umožňuje připojení a zobrazí všechny možnosti, vyberte **autorizace**.

1. Na **autorizace** vyberte **Authorize**a zadejte pověření nezbytné pro Azure pro přístup k účtu GitHub. 

1. Po Azure ověřuje přihlašovací údaje Githubu, zobrazí se zpráva označující, že proces autorizace byla dokončena. Vyberte **OK** zavřete **autorizace** kartě.

1. Vyberte **zvolte organizaci** a vyberte svoji organizaci.

1. Vyberte **projektu zvolte**.

1. Na **projektu zvolte** vyberte **Super terraform** projektu.

    ![Zvolte Super terraform projektu](./media/terraform-slot-walkthru/choose-project.png)

1. Vyberte **zvolte větve**.

1. Na **zvolte větve** vyberte **hlavní**.

    ![Vyberte hlavní větve](./media/terraform-slot-walkthru/choose-branch-master.png)

1. Na **možnost nasazení** vyberte **OK**.

V tomto okamžiku jste nasadili produkční slot. Pokud chcete nasadit přípravný slot, proveďte všechny předchozí kroky v této části s těmito změnami:

- V kroku 3 **slotAppServiceSlotOne** prostředků.

- V kroku 13 vyberte větev "práce" namísto hlavní větve.

    ![Zvolte práce firemní pobočky](./media/terraform-slot-walkthru/choose-branch-working.png)

## <a name="test-the-app-deployments"></a>Testovací nasazení aplikace

V předchozích částech nastavte dva sloty - **slotAppService** a **slotAppServiceSlotOne** – k nasazení z různých větve na Githubu. Umožňuje zobrazit náhled webové aplikace k ověření, že byly úspěšně nasazeny.

Proveďte následující kroky dvakrát kde v kroku 3 vybrat **slotAppService** poprvé a potom vyberte **slotAppServiceSlotOne** druhém:

1. Na Azure portálu hlavní nabídky, vyberte **skupiny prostředků**.

1. Vyberte **slotDemoResourceGroup**.

1. Vyberte buď **slotAppService** nebo **slotAppServiceSlotOne**.

1. Na stránce Přehled vyberte **URL**.

    ![Vyberte adresu URL na kartě Přehled k vykreslení aplikace](./media/terraform-slot-walkthru/resource-url.png)

> [!NOTE]
> Ho může trvat několik minut, než Azure k vytváření a nasazování webu z Githubu.
>
>

Pro **slotAppService** webové aplikace, můžete blue se nezobrazí stránka název stránky o **Slot ukázkové aplikace 1**. Pro **slotAppServiceSlotOne** webové aplikace, zobrazí zelené stránka s názvem stránky z **Slot ukázkové aplikace 2**.

![Náhled aplikace pro testování, aby byly nasazeny správně](./media/terraform-slot-walkthru/app-preview.png)

## <a name="swap-the-two-deployment-slots"></a>Prohodit sloty dvě nasazení

Pokud chcete otestovat, odkládací dvě nasazovací sloty, proveďte následující kroky:
 
1. Přepnout na záložce prohlížeče systémem **slotAppService** (aplikace s stránce modré). 

1. Vraťte se k portálu Azure v samostatné kartě.

1. Otevřete prostředí cloudu.

1. Přejděte do adresáře **clouddrive/swap** adresáře.

    ```bash
    cd clouddrive/swap
    ```

1. Pomocí editoru vi, vytvořte soubor s názvem `swap.tf`.

    ```bash
    vi swap.tf
    ```

1. Zadejte režim vložení stisknutím písmeno `i` klíč.

1. Vložte následující kód do editoru:

    ```JSON
    # Configure the Azure Provider
    provider "azurerm" { }

    # Swap the production slot and the staging slot
    resource "azurerm_app_service_active_slot" "slotDemoActiveSlot" {
        resource_group_name   = "slotDemoResourceGroup"
        app_service_name      = "slotAppService"
        app_service_slot_name = "slotappServiceSlotOne"
    }
    ```

1. Stiskněte  **&lt;Esc >** klíč pro ukončení režimu vkládání.

1. Uložte tento soubor a ukončete vi editor tak, že zadáte následující příkaz, a potom stisknutím  **&lt;Enter >**:

    ```bash
    :wq
    ```

1. Inicializujte Terraform.

    ```bash
    terraform init
    ```

1. Vytvořte plán Terraform.

    ```bash
    terraform plan
    ```

1. Zřízení prostředků definovaný v `swap.tf` konfigurační soubor. (Tuto akci potvrďte zadáním `yes` příkazového řádku.)

    ```bash
    terraform apply
    ```

1. Po dokončení Terraform prohození slotů, vraťte se do prohlížeče, který je vykreslování **slotAppService** webová aplikace a aktualizujte stránku. 

Webové aplikace ve vaší **slotAppServiceSlotOne** pracovní pozici byla si místo, se produkční slot a nyní vykreslí zeleně. 

![Nasazovací sloty byla vzájemně zaměněny](./media/terraform-slot-walkthru/slots-swapped.png)

Pokud chcete vrátit na původní produkční verzi aplikace, znovu použít plán Terraform vytvořené z `swap.tf` konfigurační soubor.

```bash
terraform apply
```

Jakmile si místo, zobrazí se původní konfiguraci.