---
title: Ověření hvězdicové sítě s využitím Terraformu v Azure
description: Kurz k ověření rozbočovač a uvedenou síťovou topologii se všechny virtuální sítě připojené k mezi sebou.
services: terraform
ms.service: terraform
keywords: terraform, střed a paprsek, sítí, hybridní sítě, devops, virtuální počítač, azure, vnet peering
author: VaijanathB
manager: jeconnoc
ms.author: vaangadi
ms.topic: tutorial
ms.date: 03/01/2019
ms.openlocfilehash: eac86ac5d56d4aef4a8220ff9b40f053d241a356
ms.sourcegitcommit: 94305d8ee91f217ec98039fde2ac4326761fea22
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2019
ms.locfileid: "57411416"
---
# <a name="tutorial-validate-a-hub-and-spoke-network-with-terraform-in-azure"></a>Kurz: Ověření hvězdicové sítě s využitím Terraformu v Azure

V tomto článku provedete terraformu soubory vytvořené v předchozím článku v této sérii. Výsledkem je ověření připojení mezi virtuálními sítěmi ukázku.

Tento kurz se zabývá následujícími úkony:

> [!div class="checklist"]
> * Můžete implementovat virtuální síť centra v topologii centra s paprsky HCL (HashiCorp Language)
> * Terraform plán můžete použít k ověření prostředků, které mají být nasazeny
> * Použití Terraformu použít k vytváření prostředků v Azure
> * Ověření připojení mezi sítěmi
> * Použijte Terraform ke zničení všech prostředků

## <a name="prerequisites"></a>Požadavky

1. [Vytvoření centra hvězdicové hybridní topologie sítě s využitím Terraformu v Azure](./terraform-hub-spoke-introduction.md).
1. [Vytvořit místní virtuální sítě s využitím Terraformu v Azure](./terraform-hub-spoke-on-prem.md).
1. [S využitím Terraformu v Azure vytvořit virtuální síť centra](./terraform-hub-spoke-hub-network.md).
1. [S využitím Terraformu v Azure vytvořit virtuální síťové zařízení centra](./terraform-hub-spoke-hub-nva.md).
1. [Vytvoření jednoho paprsku virtuálních sítí s využitím Terraformu v Azure](./terraform-hub-spoke-spoke-network.md).

## <a name="verify-your-configuration"></a>Ověřit konfiguraci

Po dokončení [požadavky](#prerequisites), ověřte, zda jsou k dispozici odpovídající konfigurační soubory.

1. Přejděte na web [Azure Portal](http://portal.azure.com).

1. Otevřete službu [Azure Cloud Shell](/azure/cloud-shell/overview). Pokud jste prostředí ještě nevybrali, vyberte prostředí **Bash**.

    ![Příkazový řádek Cloud Shellu](./media/terraform-common/azure-portal-cloud-shell-button-min.png)

1. Přejděte do adresáře `clouddrive`.

    ```bash
    cd clouddrive
    ```

1. Přejděte do nového adresáře:

    ```bash
    cd hub-spoke
    ```

1. Spustit `ls` příkazu ověřte, že `.tf` konfigurační soubory vytvořené v předchozích kurzech patří:

    ![Terraform ukázku konfiguračních souborů](./media/terraform-hub-and-spoke-tutorial-series/hub-spoke-config-files.png)

## <a name="deploy-the-resources"></a>Nasazení prostředků

1. Inicializace Terraformu zprostředkovatele:
    
    ```bash
    terraform init
    ```
    
    ![Příklad výsledky příkazu "terraformu init"](./media/terraform-hub-and-spoke-tutorial-series/hub-spoke-terraform-init.png)
    
1. Spustit `terraform plan` a vidět její účinek nasazení před spuštěním příkazu:

    ```bash
    terraform plan
    ```
    
    ![Příklad výsledky příkazu "plánu terraformu"](./media/terraform-hub-and-spoke-tutorial-series/hub-spoke-terraform-plan.png)

1. Nasazení řešení:

    ```bash
    terraform apply
    ```
    
    Zadejte `yes` po zobrazení výzvy k potvrzení nasazení.

    ![Příklad výsledků příkaz "použít terraformu"](./media/terraform-hub-and-spoke-tutorial-series/hub-spoke-terraform-apply.png)
    
## <a name="test-the-hub-vnet-and-spoke-vnets"></a>Testovat virtuální síti centra a virtuální sítě paprsků

Tato část ukazuje, jak otestovat připojení ze simulované místní prostředí k virtuální síti centra.

1. Na webu Azure Portal, přejděte **onprem-připojení typu vnet-rg** skupinu prostředků.

1. V **onprem-připojení typu vnet-rg** kartu, vyberte virtuální počítač s názvem **onprem-vm**.

1. Vyberte **Connect** (Připojit).

1. Vedle textu **přihlásit se pomocí místního účtu virtuálního počítače**, kopie **ssh** příkaz do schránky.

1. Z řádku Linux, spusťte `ssh` pro připojení k simulované místní prostředí. Použijte heslo zadané v `on-prem.tf` soubor s parametry.

1. Spustit `ping` příkazu k testování připojení k virtuálnímu počítači jumpboxu ve virtuální síti centra:

   ```bash
   ping 10.0.0.68
   ```

1. Spustit `ping` příkazu k testování připojení k jumpboxu virtuálních počítačů v každém paprsku:

   ```bash
   ping 10.1.0.68
   ping 10.2.0.68
   ```

1. Ukončete ssh na relaci **onprem virtuálního počítače** virtuální počítač, zadejte `exit` a stiskněte klávesu &lt;Enter >.

## <a name="troubleshoot-vpn-issues"></a>Řešení potíží s VPN

Informace o řešení chyb sítě VPN, najdete v článku, [řešení potíží s hybridní připojení k síti VPN](/azure/architecture/reference-architectures/hybrid-networking/troubleshoot-vpn).

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, odstraňte prostředky vytvořené v této sérii kurzů.

1. Odeberte prostředky, které jsou deklarovány v plánu:

    ```bash
    terraform destroy
    ```

    Zadejte `yes` po zobrazení výzvy k potvrzení odebrání prostředků.

1. Změňte adresář na nadřazeném adresáři:

    ```bash
    cd ..
    ```

1. Odstranit `hub-scope` adresáři (včetně všechny jeho soubory):

    ```bash
    rm -r hub-spoke
    ```

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"] 
> [Další informace o použití Terraformu v Azure](/azure/terraform)