---
title: Kurz – ověření sítě centra a paprsků v Azure pomocí Terraformu
description: Kurz pro ověření síťové topologie centra a paprsků se všemi virtuálními sítěmi propojenými navzájem.
ms.topic: tutorial
ms.date: 10/26/2019
ms.openlocfilehash: 9ba4780c59e5e9da4999573abbc08ecd2738a2cd
ms.sourcegitcommit: 28688c6ec606ddb7ae97f4d0ac0ec8e0cd622889
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/18/2019
ms.locfileid: "74159201"
---
# <a name="tutorial-validate-a-hub-and-spoke-network-in-azure-using-terraform"></a>Kurz: ověření sítě centra a paprsků v Azure pomocí Terraformu

V tomto článku provedete soubory terraformu vytvořené v předchozím článku v této sérii. Výsledkem je ověření připojení mezi ukázkovými virtuálními sítěmi.

Tento kurz se zabývá následujícími úkony:

> [!div class="checklist"]
> * Použití HCL (HashiCorp Language) k implementaci virtuální sítě rozbočovače v topologii centra s paprsky
> * Použijte plán Terraformu k ověření prostředků, které se mají nasadit.
> * Použití Terraformu se používá k vytvoření prostředků v Azure
> * Ověření připojení mezi různými sítěmi
> * Zničení všech prostředků pomocí Terraformu

## <a name="prerequisites"></a>Požadavky

1. [Vytvořte topologii hybridní sítě rozbočovače a paprsku pomocí terraformu v Azure](./terraform-hub-spoke-introduction.md).
1. [Vytvořte si místní virtuální síť s terraformu v Azure](./terraform-hub-spoke-on-prem.md).
1. [Vytvořte virtuální síť centra pomocí terraformu v Azure](./terraform-hub-spoke-hub-network.md).
1. [Vytvořte zařízení virtuální sítě rozbočovače s terraformu v Azure](./terraform-hub-spoke-hub-nva.md).
1. [Vytvořte hvězdicové virtuální sítě pomocí terraformu v Azure](./terraform-hub-spoke-spoke-network.md).

## <a name="verify-your-configuration"></a>Ověření konfigurace

Po dokončení [požadovaných součástí](#prerequisites)ověřte, zda jsou k dispozici příslušné konfigurační soubory.

1. Přejděte na web [Azure Portal](https://portal.azure.com).

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

1. Spusťte příkaz `ls`, abyste ověřili, že jsou uvedené soubory `.tf` config vytvořené v předchozích kurzech:

    ![Konfigurační soubory terraformu demo](./media/terraform-hub-and-spoke-tutorial-series/hub-spoke-config-files.png)

## <a name="deploy-the-resources"></a>Nasazení prostředků

1. Inicializovat poskytovatele Terraformu:
    
    ```bash
    terraform init
    ```
    
    ![Příklady výsledků příkazu "terraformu init"](./media/terraform-hub-and-spoke-tutorial-series/hub-spoke-terraform-init.png)
    
1. Spuštěním příkazu `terraform plan` zobrazíte účinek nasazení před provedením:

    ```bash
    terraform plan
    ```
    
    ![Příklady výsledků příkazu "terraformu Plan"](./media/terraform-hub-and-spoke-tutorial-series/hub-spoke-terraform-plan.png)

1. Nasazení řešení:

    ```bash
    terraform apply
    ```
    
    Po zobrazení výzvy k potvrzení nasazení zadejte `yes`.

    ![Příklady výsledků příkazu "terraformu Apply"](./media/terraform-hub-and-spoke-tutorial-series/hub-spoke-terraform-apply.png)
    
## <a name="test-the-hub-vnet-and-spoke-vnets"></a>Otestujte virtuální síť centra a paprskový virtuální sítě

V této části se dozvíte, jak testovat připojení ze simulovaného místního prostředí do virtuální sítě centra.

1. V Azure Portal přejděte do skupiny prostředků **OnPrem-VNet-RG** .

1. Na kartě **OnPrem-VNet-RG** vyberte virtuální počítač s názvem **OnPrem-VM**.

1. Vyberte **Connect** (Připojit).

1. Vedle textového **přihlášení pomocí místního účtu virtuálního počítače**zkopírujte příkaz **SSH** do schránky.

1. Z řádku Linux, spusťte `ssh` pro připojení k simulované místní prostředí. Použijte heslo zadané v souboru parametrů `on-prem.tf`.

1. Spusťte příkaz `ping`, abyste otestovali připojení k virtuálnímu počítači s JumpBox ve virtuální síti centra:

   ```bash
   ping 10.0.0.68
   ```

1. Spusťte příkaz `ping`, který otestuje připojení k virtuálním počítačům s JumpBox v každém paprsku:

   ```bash
   ping 10.1.0.68
   ping 10.2.0.68
   ```

1. Pokud chcete ukončit relaci SSH na virtuálním počítači **OnPrem-VM** , zadejte `exit` a stiskněte &lt;Enter >.

## <a name="troubleshoot-vpn-issues"></a>Řešení potíží s VPN

Informace o řešení chyb sítě VPN najdete v článku [řešení potíží s hybridním připojením k síti VPN](/azure/architecture/reference-architectures/hybrid-networking/troubleshoot-vpn).

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, odstraňte prostředky vytvořené v řadě kurzů.

1. Odeberte prostředky deklarované v plánu:

    ```bash
    terraform destroy
    ```

    Po zobrazení výzvy k potvrzení odebrání prostředků zadejte `yes`.

1. Přejděte do složky nadřazený adresář:

    ```bash
    cd ..
    ```

1. Odstraňte adresář `hub-scope` (včetně všech jeho souborů):

    ```bash
    rm -r hub-spoke
    ```

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"] 
> [Další informace o používání Terraformu v Azure](/azure/terraform)