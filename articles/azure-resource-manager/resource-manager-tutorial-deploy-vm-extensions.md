---
title: Nasazování rozšíření virtuálních počítačů pomocí šablon Azure Resource Manageru | Microsoft Docs
description: Zjistěte, jak nasazovat rozšíření virtuálních počítačů pomocí šablon Azure Resource Manageru.
services: azure-resource-manager
documentationcenter: ''
author: mumian
manager: dougeby
editor: ''
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 11/13/2018
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 9361c8b17d1b43b4ef63aca6ab4660571efddcde
ms.sourcegitcommit: 5978d82c619762ac05b19668379a37a40ba5755b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2019
ms.locfileid: "55492782"
---
# <a name="tutorial-deploy-virtual-machine-extensions-with-azure-resource-manager-templates"></a>Kurz: Nasazení rozšíření virtuálních počítačů pomocí šablon Azure Resource Manageru

Zjistěte, jak pomocí [rozšíření virtuálních počítačů Azure](../virtual-machines/extensions/features-windows.md) provádět na virtuálních počítačích Azure úlohy konfigurace a automatizace po nasazení. Pro použití s virtuálními počítači Azure je k dispozici řada různých rozšíření virtuálních počítačů. V tomto kurzu nasadíte rozšíření vlastních skriptů ze šablony Azure Resource Manageru pro spuštění skriptu prostředí PowerShell na virtuálním počítači s Windows.  Tento skript na virtuálním počítači nainstaluje webový server.

Tento kurz se zabývá následujícími úkony:

> [!div class="checklist"]
> * Příprava skriptu PowerShellu
> * Otevření šablony rychlého startu
> * Úprava šablony
> * Nasazení šablony
> * Ověření nasazení

Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto článku potřebujete:

* [Visual Studio Code](https://code.visualstudio.com/) s rozšířením Nástroje Resource Manageru Zobrazit [nainstalovat rozšíření](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#prerequisites).
* Pro zlepšení zabezpečení použijte pro účet správce virtuálního počítače vygenerované heslo. Tady ukázka generování hesla:

    ```azurecli-interactive
    openssl rand -base64 32
    ```

    Služba Azure Key Vault je určená k ochraně kryptografických klíčů a dalších tajných klíčů. Další informace najdete v tématu [kurzu: Integrace Azure Key Vault v nasazení šablony Resource Manageru](./resource-manager-tutorial-use-key-vault.md). Doporučujeme také, aktualizujte si heslo. každé tři měsíce.

## <a name="prepare-a-powershell-script"></a>Příprava skriptu PowerShellu

Sdílený Powershellový skript s následujícím obsahem ze [účtu služby Azure storage s veřejným přístupem](https://armtutorials.blob.core.windows.net/usescriptextensions/installWebServer.ps1):

```azurepowershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

Pokud vyberete možnost publikovat vlastní umístění souboru, je nutné aktualizovat `fileUri` element v šabloně v pozdější části kurzu.

## <a name="open-a-quickstart-template"></a>Otevření šablony rychlého startu

Šablony rychlý start Azure je úložiště šablon Resource Manageru. Místo vytvoření šablony úplně od začátku si můžete najít ukázkovou šablonu a přizpůsobit ji. Šablona používaná v tomto kurzu má název [Deploy a simple Windows VM](https://azure.microsoft.com/resources/templates/101-vm-simple-windows/) (Nasazení jednoduchého virtuálního počítače s Windows).

1. Ve Visual Studio Code, vyberte **souboru** > **otevřít soubor**.
1. V **název_souboru** vložte následující adresu URL: https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json

1. K otevření souboru, vyberte **otevřete**.  
    Šablona definuje pět zdrojů:

    * **Microsoft.Storage/storageAccounts**. Viz [referenční informace k šablonám](https://docs.microsoft.com/azure/templates/Microsoft.Storage/storageAccounts).
    * **Microsoft.Network/publicIPAddresses**. Viz [referenční informace k šablonám](https://docs.microsoft.com/azure/templates/microsoft.network/publicipaddresses).
    * **Microsoft.Network/virtualNetworks**. Viz [referenční informace k šablonám](https://docs.microsoft.com/azure/templates/microsoft.network/virtualnetworks).
    * **Microsoft.Network/networkInterfaces**. Viz [referenční informace k šablonám](https://docs.microsoft.com/azure/templates/microsoft.network/networkinterfaces).
    * **Microsoft.Compute/virtualMachines**. Viz [referenční informace k šablonám](https://docs.microsoft.com/azure/templates/microsoft.compute/virtualmachines).

    Je vhodné získat seznámení se základními principy šablony dříve, než ji přizpůsobit.

1. Uložit kopii souboru do místního počítače s názvem *azuredeploy.json* tak, že vyberete **souboru** > **uložit jako**.

## <a name="edit-the-template"></a>Úprava šablony

Ke stávající šabloně s následujícím obsahem přidejte prostředek rozšíření virtuálního počítače:

```json
{
    "apiVersion": "2018-06-01",
    "type": "Microsoft.Compute/virtualMachines/extensions",
    "name": "[concat(variables('vmName'),'/', 'InstallWebServer')]",
    "location": "[parameters('location')]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/',variables('vmName'))]"
    ],
    "properties": {
        "publisher": "Microsoft.Compute",
        "type": "CustomScriptExtension",
        "typeHandlerVersion": "1.7",
        "autoUpgradeMinorVersion":true,
        "settings": {
            "fileUris": [
                "https://armtutorials.blob.core.windows.net/usescriptextensions/installWebServer.ps1"
            ],
            "commandToExecute": "powershell.exe -ExecutionPolicy Unrestricted -File installWebServer.ps1"
        }
    }
}
```

Další informace o této definici prostředku, najdete v článku [odkaz na rozšíření](https://docs.microsoft.com/azure/templates/microsoft.compute/virtualmachines/extensions). Tady je několik důležitých elementů:

* **Název**: Rozšíření prostředků je prostředek podřízený objekt virtuálního počítače, musí mít název prefix názvu virtuálního počítače. Viz [Podřízené prostředky](./resource-manager-templates-resources.md#child-resources).
* **dependsOn**: Po vytvoření virtuálního počítače, vytvořte prostředek rozšíření.
* **fileUris**: Umístění, kde jsou uloženy soubory skriptu. Pokud se rozhodnete nepoužívat zadané umístění, budete muset aktualizovat hodnoty.
* **commandToExecute**: Tento příkaz spustí skript.  

## <a name="deploy-the-template"></a>Nasazení šablony

Postup nasazení najdete v části "Šablony nasazení" [kurzu: Vytváření šablon Azure Resource Manageru s závislé prostředky](./resource-manager-tutorial-create-templates-with-dependent-resources.md#deploy-the-template). Doporučujeme použít generované heslo pro účet správce virtuálního počítače. Najdete v tomto článku [požadavky](#prerequisites) oddílu.

## <a name="verify-the-deployment"></a>Ověření nasazení

1. Na webu Azure Portal vyberte virtuální počítač.
1. V přehledu virtuálního počítače, zkopírujte IP adresu tak, že vyberete **kopírování kliknutím**a vložte ji na kartě prohlížeče.  
   Výchozí internetové informační služby (IIS) úvodní stránka otevře:

![Úvodní stránka služby IIS](./media/resource-manager-tutorial-deploy-vm-extensions/resource-manager-template-deploy-extensions-customer-script-web-server.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už nepotřebujete prostředky Azure, které jste nasadili, vyčistěte je odstraněním skupiny prostředků.

1. Na webu Azure Portal, v levém podokně vyberte **skupiny prostředků**.
2. V **filtrovat podle názvu** zadejte název skupiny prostředků.
3. Vyberte název skupiny prostředků.  
    Šest materiál se zobrazí ve skupině prostředků.
4. V horní nabídce vyberte **odstranit skupinu prostředků**.

## <a name="next-steps"></a>Další postup

V tomto kurzu jste nasadili virtuální počítač a rozšíření virtuálního počítače. Toto rozšíření na virtuální počítač nainstalovalo webový server služby IIS. Další informace o použití rozšíření Azure SQL Database pro import souboru BACPAC, najdete v tématech:

> [!div class="nextstepaction"]
> [Nasazení rozšíření SQL](./resource-manager-tutorial-deploy-sql-extensions-bacpac.md)
