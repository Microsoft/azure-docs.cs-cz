---
title: Kurz – ověření sítě rozbočovače a paprsku v Azure pomocí Terraform
description: Kurz k ověření topologie sítě rozbočovače a paprsku se všemi virtuálními sítěmi vzájemně propojenými.
ms.topic: tutorial
ms.date: 10/26/2019
ms.openlocfilehash: 9ba4780c59e5e9da4999573abbc08ecd2738a2cd
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "74159201"
---
# <a name="tutorial-validate-a-hub-and-spoke-network-in-azure-using-terraform"></a>Kurz: Ověření sítě rozbočovače a paprsku v Azure pomocí Terraform

V tomto článku provedete terraform soubory vytvořené v předchozím článku v této řadě. Výsledkem je ověření připojení mezi demo virtuálnísítě.

Tento kurz se zabývá následujícími úkony:

> [!div class="checklist"]
> * Použití HCL (HashiCorp Language) k implementaci virtuální sítě hubu v topologii s hubem
> * Použití plánu Terraform k ověření prostředků, které mají být nasazeny
> * Použití aplikace Terraform apply k vytvoření prostředků v Azure
> * Ověření připojení mezi různými sítěmi
> * Použijte Terraform zničit všechny zdroje

## <a name="prerequisites"></a>Požadavky

1. [Vytvořte topologii hybridní sítě rozbočovače a paprsku s Terraformem v Azure](./terraform-hub-spoke-introduction.md).
1. [Vytvořte místní virtuální síť s Terraform em v Azure](./terraform-hub-spoke-on-prem.md).
1. [Vytvořte centrální virtuální síť s Terraform v Azure](./terraform-hub-spoke-hub-network.md).
1. [Vytvořte centrální virtuální síťové zařízení s Terraform v Azure](./terraform-hub-spoke-hub-nva.md).
1. [Vytvořte virtuální sítě s paprsky s Terraformem v Azure](./terraform-hub-spoke-spoke-network.md).

## <a name="verify-your-configuration"></a>Ověření konfigurace

Po dokončení [požadavků](#prerequisites)ověřte, zda jsou k dispozici příslušné konfigurační soubory.

1. Přejděte na [portál Azure](https://portal.azure.com).

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

1. Spuštěním `ls` příkazu ověřte, zda jsou uvedeny `.tf` konfigurační soubory vytvořené v předchozích kurzech:

    ![Terraform demo konfigurační soubory](./media/terraform-hub-and-spoke-tutorial-series/hub-spoke-config-files.png)

## <a name="deploy-the-resources"></a>Nasazení prostředků

1. Inicializovat poskytovatele Terraform:
    
    ```bash
    terraform init
    ```
    
    ![Příklad výsledků příkazu "terraform init"](./media/terraform-hub-and-spoke-tutorial-series/hub-spoke-terraform-init.png)
    
1. Spuštěním `terraform plan` příkazu zobrazíte efekt nasazení před spuštěním:

    ```bash
    terraform plan
    ```
    
    ![Příklad výsledků příkazu "terraform plan"](./media/terraform-hub-and-spoke-tutorial-series/hub-spoke-terraform-plan.png)

1. Nasazení řešení:

    ```bash
    terraform apply
    ```
    
    Po `yes` zobrazení výzvy k potvrzení nasazení zadejte.

    ![Příklad výsledků příkazu "terraform apply"](./media/terraform-hub-and-spoke-tutorial-series/hub-spoke-terraform-apply.png)
    
## <a name="test-the-hub-vnet-and-spoke-vnets"></a>Testování virtuálních sítí a virtuálních sítí pro paprsky rozbočovače

Tato část ukazuje, jak otestovat připojení ze simulovaného místního prostředí do virtuální sítě rozbočovače.

1. Na webu Azure Portal přejděte na skupinu prostředků **onprem-vnet-rg.**

1. Na kartě **onprem-vnet-rg** vyberte virtuální počítač s názvem **onprem-vm**.

1. Vyberte **Connect** (Připojit).

1. Vedle textu **Přihlášení pomocí místního účtu virtuálního účtu**zkopírujte příkaz **ssh** do schránky.

1. Z výzvy Linuxu spusťte `ssh` připojení k simulovanému místnímu prostředí. Použijte heslo zadané `on-prem.tf` v souboru parametrů.

1. Spuštěním `ping` příkazu otestujte připojení k virtuálnímu virtuálnímu zařízení jumpbox u centrální virtuální sítě:

   ```bash
   ping 10.0.0.68
   ```

1. Spuštěním `ping` příkazu otestujte připojení k virtuálním sítím jumpbox v každém paprsku:

   ```bash
   ping 10.1.0.68
   ping 10.2.0.68
   ```

1. Relaci ssh ukončíte na virtuálním počítači **onprem-vm,** zadejte `exit` a stiskněte &lt;Enter>.

## <a name="troubleshoot-vpn-issues"></a>Poradce při potížích s VPN

Informace o řešení chyb SÍTĚ VPN naleznete v článku [Poradce při potížích s hybridním připojením VPN](/azure/architecture/reference-architectures/hybrid-networking/troubleshoot-vpn).

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud již není potřeba, odstraňte prostředky vytvořené v sérii kurzů.

1. Odeberte zdroje deklarované v plánu:

    ```bash
    terraform destroy
    ```

    Po `yes` zobrazení výzvy k potvrzení odebrání prostředků zadejte.

1. Změna adresářů do nadřazeného adresáře:

    ```bash
    cd ..
    ```

1. Odstranit `hub-scope` adresář (včetně všech jeho souborů):

    ```bash
    rm -r hub-spoke
    ```

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"] 
> [Další informace o používání Terraform v Azure](/azure/terraform)