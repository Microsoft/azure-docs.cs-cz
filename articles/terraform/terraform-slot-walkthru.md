---
title: Terraform se sloty nasazení zprostředkovatele Azure
description: Kurz o používání Terraformu se sloty nasazení zprostředkovatele Azure
services: terraform
ms.service: terraform
keywords: terraform, devops, virtuální počítač, Azure, sloty nasazení
author: tomarcher
manager: jeconnoc
ms.author: tarcher
ms.topic: tutorial
ms.date: 4/05/2018
ms.openlocfilehash: a85e7c46aa41309bc031f59ed841dc8149d878b3
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/21/2018
ms.locfileid: "53723956"
---
# <a name="use-terraform-to-provision-infrastructure-with-azure-deployment-slots"></a>Použití Terraformu ke zřízení infrastruktury pomocí slotů nasazení Azure

[Sloty nasazení Azure](/azure/app-service/deploy-staging-slots) můžete použít k přepínání mezi různými verzemi aplikace. Tato možnost vám pomůže minimalizovat dopad přerušených nasazení. 

Tento článek vám použití slotů nasazení ukáže na nasazením dvou aplikací prostřednictvím GitHubu a Azure. Jedna aplikace je hostovaná v produkčním slotu. Druhá aplikace je hostovaná v přípravném slotu. (Názvy "produkční" a "přípravný" jsou nahodilé a můžou se jmenovat i jinak, aby se hodily do vašeho scénáře.) Po dokončení konfigurace nasazovacích slotů můžete Terraform použít k přepínání mezi dvěma sloty podle potřeby.

## <a name="prerequisites"></a>Požadavky

- **Předplatné Azure**: Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) před tím, než začnete.

- **Účet GitHub**: Je nutné [Githubu](http://www.github.com) účet vytvořit fork a používání testů úložiště GitHub.

## <a name="create-and-apply-the-terraform-plan"></a>Vytvoření a použití plánu Terraformu

1. Přejděte na web [Azure Portal](http://portal.azure.com).

1. Otevřete službu [Azure Cloud Shell](/azure/cloud-shell/overview). Pokud jste prostředí ještě nevybrali, vyberte prostředí **Bash**.

    ![Příkazový řádek Cloud Shellu](./media/terraform-slot-walkthru/azure-portal-cloud-shell-button-min.png)

1. Přejděte do adresáře `clouddrive`.

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

1. Použijte příkaz Bash `ls`, abyste ověřili, že se oba adresáře úspěšně vytvořily.

    ![Cloud Shell po vytvoření adresářů](./media/terraform-slot-walkthru/cloud-shell-after-creating-dirs.png)

1. Přejděte do adresáře `deploy`.

    ```bash
    cd deploy
    ```

1. Pomocí [editoru vi](https://www.debian.org/doc/manuals/debian-tutorial/ch-editor.html) vytvořte soubor s názvem `deploy.tf`. Tento soubor bude obsahovat [konfiguraci Terraformu](https://www.terraform.io/docs/configuration/index.html).

    ```bash
    vi deploy.tf
    ```

1. Stisknutím klávesy I přejděte do režimu vkládání.

1. Do editoru vložte následující kód:

    ```JSON
    # Configure the Azure provider
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

1. Stisknutím klávesy Esc ukončete režim vkládání.

1. Uložte soubor a zadáním následujícího příkazu ukončete editor vi:

    ```bash
    :wq
    ```

1. Když máte soubor vytvořený, ověřte jeho obsah.

    ```bash
    cat deploy.tf
    ```

1. Inicializujte Terraform.

    ```bash
    terraform init
    ```

1. Vytvořte plán Terraformu.

    ```bash
    terraform plan
    ```

1. Zřiďte prostředky definované v konfiguračním souboru `deploy.tf`. (Akci potvrďte zadáním `yes` po vyzvání.)

    ```bash
    terraform apply
    ```

1. Zavřete okno Cloud Shellu.

1. Na webu Azure Portal vyberte v hlavní nabídce možnost **Skupiny prostředků**.

    ![Výběr skupin prostředků na webu Azure Portal](./media/terraform-slot-walkthru/resource-groups-menu-option.png)

1. Na kartě **Skupiny prostředků** vyberte **slotDemoResourceGroup**.

    ![Skupina prostředků vytvořená nástrojem Terraform](./media/terraform-slot-walkthru/resource-group.png)

Teď uvidíte všechny prostředky vytvořené nástrojem Terraform.

![Prostředky vytvořené nástrojem Terraform](./media/terraform-slot-walkthru/resources.png)

## <a name="fork-the-test-project"></a>Vytvoření forku projektu testů

Než budete moct otestovat, co jste vytvořili, a prohazovat sloty nasazení, musíte na GitHubu vytvořit fork projektu testů.

1. Přejděte do [úložiště awesome-terraform na GitHubu](https://github.com/Azure/awesome-terraform).

1. Vytvořte fork úložiště **awesome-terraform**.

    ![Vytvoření forku úložiště awesome-terraform](./media/terraform-slot-walkthru/fork-repo.png)

1. Postupujte podle výzev pro vytvoření forku do vašeho prostředí.

## <a name="deploy-from-github-to-your-deployment-slots"></a>Nasazení z GitHubu do slotů nasazení

Po vytvoření forku úložiště projektu testů nakonfigurujte sloty nasazení následujícím způsobem:

1. Na webu Azure Portal vyberte v hlavní nabídce možnost **Skupiny prostředků**.

1. Vyberte **slotDemoResourceGroup**.

1. Vyberte **slotAppService**.

1. Vyberte **Možnosti nasazení**.

    ![Možnosti nasazení pro prostředky App Service](./media/terraform-slot-walkthru/deployment-options.png)

1. Na kartě **Možnost nasazení** zvolte možnost **Vyberte zdroj** a pak vyberte **GitHub**.

    ![Výběr zdroje nasazení](./media/terraform-slot-walkthru/select-source.png)

1. Jakmile Azure naváže spojení a zobrazí všechny možnosti, vyberte možnost **Autorizace**.

1. Na kartě **Autorizace** vyberte **Autorizovat** a zadejte přihlašovací údaje, které Azure potřebuje pro přístup k účtu GitHub. 

1. Jakmile Azure přihlašovací údaje GitHubu ověří, zobrazí se zpráva potvrzující, že se autorizace dokončila. Vyberte **OK** a kartu **Autorizace** zavřete.

1. Vyberte **Zvolte si organizaci** a vyberte svou organizaci.

1. Vyberte **Zvolte projekt**.

1. Na kartě **Zvolte projekt** vyberte projekt **awesome-terraform**.

    ![Volba projektu awesome-terraform](./media/terraform-slot-walkthru/choose-project.png)

1. Vyberte **Zvolte větev**.

1. Na kartě **Zvolte větev** vyberte **master**.

    ![Volba větve master](./media/terraform-slot-walkthru/choose-branch-master.png)

1. Na kartě **Možnosti nasazení** vyberte **OK**.

Momentálně máte nasazený produkční slot. Pokud chcete nasadit přípravný slot, proveďte všechny předchozí kroky v této části s těmito změnami:

- V kroku 3 vyberte prostředek **slotAppServiceSlotOne**.

- V kroku 13 vyberte místo větve master pracovní větev.

    ![Volba pracovní větve](./media/terraform-slot-walkthru/choose-branch-working.png)

## <a name="test-the-app-deployments"></a>Test nasazení aplikací

V předchozích částech jste nastavili dva sloty – **slotAppService** a **slotAppServiceSlotOne** – které se nasadí z různých větví na GitHubu. Pojďme si zobrazit náhled webových aplikací, abychom si ověřili, že se úspěšně nasadily.

Následující kroky proveďte dvakrát. V kroku 3 vyberete poprvé **slotAppService** a podruhé vyberte **slotAppServiceSlotOne**.

1. Na webu Azure Portal vyberte v hlavní nabídce možnost **Skupiny prostředků**.

1. Vyberte **slotDemoResourceGroup**.

1. Vyberte buď **slotAppService**, nebo **slotAppServiceSlotOne**.

1. Na stránce přehledu vyberte **URL**.

    ![Výběr URL na kartě přehledu pro vykreslení aplikace](./media/terraform-slot-walkthru/resource-url.png)

> [!NOTE]
> Může to trvat několik minut, než Azure web z GitHubu sestaví a nasadí.
>
>

U webové aplikace **slotAppService** uvidíte modrou stránku s nadpisem **Slot Demo App 1**. U webové aplikace **slotAppServiceSlotOne** uvidíte zelenou stránku s nadpisem **Slot Demo App 2**.

![Náhled aplikací pro otestování správného nasazení](./media/terraform-slot-walkthru/app-preview.png)

## <a name="swap-the-two-deployment-slots"></a>Prohození dvou slotů nasazení

Prohození dvou slotů nasazení otestujete následovně:
 
1. Přepněte se na kartu prohlížeče, na které běží **slotAppService** (aplikace s modrou stránkou). 

1. Na jiné kartě se vraťte na web Azure Portal.

1. Otevřete Cloud Shell.

1. Přejděte do adresáře **clouddrive/swap**.

    ```bash
    cd clouddrive/swap
    ```

1. Pomocí editoru vi vytvořte soubor s názvem `swap.tf`.

    ```bash
    vi swap.tf
    ```

1. Stisknutím klávesy I přejděte do režimu vkládání.

1. Do editoru vložte následující kód:

    ```JSON
    # Configure the Azure provider
    provider "azurerm" { }

    # Swap the production slot and the staging slot
    resource "azurerm_app_service_active_slot" "slotDemoActiveSlot" {
        resource_group_name   = "slotDemoResourceGroup"
        app_service_name      = "slotAppService"
        app_service_slot_name = "slotappServiceSlotOne"
    }
    ```

1. Stisknutím klávesy Esc ukončete režim vkládání.

1. Uložte soubor a zadáním následujícího příkazu ukončete editor vi:

    ```bash
    :wq
    ```

1. Inicializujte Terraform.

    ```bash
    terraform init
    ```

1. Vytvořte plán Terraformu.

    ```bash
    terraform plan
    ```

1. Zřiďte prostředky definované v konfiguračním souboru `swap.tf`. (Akci potvrďte zadáním `yes` po vyzvání.)

    ```bash
    terraform apply
    ```

1. Jakmile Terraform prohození slotů dokončí, přejděte na kartu prohlížeče, která vykresluje webovou aplikaci **slotAppService** a stránku aktualizujte. 

Webová aplikace v přípravném slotu **slotAppServiceSlotOne** se nyní prohodila s aplikací v produkčním slotu a je nyní vykreslená zeleně. 

![Sloty nasazení se prohodily](./media/terraform-slot-walkthru/slots-swapped.png)

Pokud se chcete vrátit k původní produkční verzi aplikace, použijte znovu plán Terraformu, který jste vytvořili z konfiguračního souboru `swap.tf`.

```bash
terraform apply
```

Po prohození aplikace uvidíte původní konfiguraci.