---
title: Nasazení rozšíření virtuálních počítačů pomocí šablony
description: Zjistěte, jak nasazovat rozšíření virtuálních počítačů pomocí šablon Azure Resource Manageru.
author: mumian
ms.date: 11/13/2018
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 5379bf3c0a5127e5114ac819bd3e0e2ad12e8d69
ms.sourcegitcommit: 05cdbb71b621c4dcc2ae2d92ca8c20f216ec9bc4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/16/2020
ms.locfileid: "76045254"
---
# <a name="tutorial-deploy-virtual-machine-extensions-with-azure-resource-manager-templates"></a>Kurz: Nasazování rozšíření virtuálních počítačů pomocí šablon Azure Resource Manageru

Zjistěte, jak pomocí [rozšíření virtuálních počítačů Azure](../../virtual-machines/extensions/features-windows.md) provádět na virtuálních počítačích Azure úlohy konfigurace a automatizace po nasazení. Pro použití s virtuálními počítači Azure je k dispozici řada různých rozšíření virtuálních počítačů. V tomto kurzu nasadíte rozšíření vlastních skriptů z Azure Resource Manager šablony pro spuštění skriptu PowerShellu na virtuálním počítači s Windows.  Tento skript na virtuálním počítači nainstaluje webový server.

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

* Visual Studio Code s rozšířením nástrojů Správce prostředků Tools. Pokud [chcete vytvořit Azure Resource Manager šablony](use-vs-code-to-create-template.md), přečtěte si téma použití Visual Studio Code.
* Pro zlepšení zabezpečení použijte pro účet správce virtuálního počítače vygenerované heslo. Tady ukázka generování hesla:

    ```azurecli-interactive
    openssl rand -base64 32
    ```

    Služba Azure Key Vault je určená k ochraně kryptografických klíčů a dalších tajných klíčů. Další informace najdete v [kurzu integrace služby Azure Key Vault v nasazení šablony Resource Manageru](./template-tutorial-use-key-vault.md). Doporučujeme vám také aktualizovat heslo každé tři měsíce.

## <a name="prepare-a-powershell-script"></a>Příprava skriptu PowerShellu

Z [GitHubu](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorial-vm-extension/installWebServer.ps1)se sdílí skript prostředí PowerShell s následujícím obsahem:

```azurepowershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

Pokud se rozhodnete publikovat soubor ve svém vlastním umístění, musíte aktualizovat element `fileUri` v šabloně později v tomto kurzu.

## <a name="open-a-quickstart-template"></a>Otevření šablony rychlého startu

Šablony pro rychlý Start Azure jsou úložiště pro šablony Správce prostředků. Místo vytvoření šablony úplně od začátku si můžete najít ukázkovou šablonu a přizpůsobit ji. Šablona používaná v tomto kurzu má název [Deploy a simple Windows VM](https://azure.microsoft.com/resources/templates/101-vm-simple-windows/) (Nasazení jednoduchého virtuálního počítače s Windows).

1. V Visual Studio Code vyberte **soubor** > **otevřít soubor**.
1. Do pole **název souboru** vložte následující adresu URL: https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json

1. Chcete-li otevřít soubor, vyberte možnost **otevřít**.
    Šablona definuje pět prostředků:

   * **Microsoft. Storage/storageAccounts**. Viz [referenční informace k šablonám](https://docs.microsoft.com/azure/templates/Microsoft.Storage/storageAccounts).
   * **Microsoft. Network/publicIPAddresses**. Viz [referenční informace k šablonám](https://docs.microsoft.com/azure/templates/microsoft.network/publicipaddresses).
   * **Microsoft. Network/virtualNetworks**. Viz [referenční informace k šablonám](https://docs.microsoft.com/azure/templates/microsoft.network/virtualnetworks).
   * **Microsoft. Network/networkInterfaces**. Viz [referenční informace k šablonám](https://docs.microsoft.com/azure/templates/microsoft.network/networkinterfaces).
   * **Microsoft. COMPUTE/virtualMachines**. Viz [referenční informace k šablonám](https://docs.microsoft.com/azure/templates/microsoft.compute/virtualmachines).

     Před přizpůsobením šablony je užitečné získat základní informace o této šabloně.

1. Uložte kopii souboru do místního počítače s názvem *azuredeploy. JSON* , a to tak, že vyberete **soubor** > **Uložit jako**.

## <a name="edit-the-template"></a>Úprava šablony

Ke stávající šabloně s následujícím obsahem přidejte prostředek rozšíření virtuálního počítače:

```json
{
    "type": "Microsoft.Compute/virtualMachines/extensions",
    "apiVersion": "2018-06-01",
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
                "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorial-vm-extension/installWebServer.ps1"
            ],
            "commandToExecute": "powershell.exe -ExecutionPolicy Unrestricted -File installWebServer.ps1"
        }
    }
}
```

Další informace o této definici prostředků najdete v odkazu na [rozšíření](https://docs.microsoft.com/azure/templates/microsoft.compute/virtualmachines/extensions). Tady je několik důležitých elementů:

* **name:** Vzhledem k tomu, že prostředek rozšíření je podřízeným prostředkem objektu virtuálního počítače, musí název obsahovat předponu virtuálního počítače. Viz [Nastavení názvu a typu pro podřízené prostředky](child-resource-name-type.md).
* **dependsOn**: po vytvoření virtuálního počítače vytvořte prostředek rozšíření.
* **identifikátory URI**: umístění, kde jsou uloženy soubory skriptu. Pokud se rozhodnete, že nepoužijete zadané umístění, je nutné aktualizovat hodnoty.
* **commandToExecute**: Tento příkaz vyvolá skript.

## <a name="deploy-the-template"></a>Nasazení šablony

Postup nasazení najdete v části "nasazení šablony" v tématu [kurz: vytvoření šablon Azure Resource Manager se závislými prostředky](./template-tutorial-create-templates-with-dependent-resources.md#deploy-the-template). Doporučujeme použít vygenerované heslo pro účet správce virtuálního počítače. Viz část [požadavky](#prerequisites) tohoto článku.

## <a name="verify-the-deployment"></a>Ověření nasazení

1. V Azure Portal vyberte virtuální počítač.
1. V přehledu virtuálních počítačů zkopírujte IP adresu tak, že **kliknete na tlačítko Kopírovat**a pak ho vložíte na kartu prohlížeče. Otevře se výchozí úvodní stránka Internetová informační služba (IIS):

![Úvodní stránka Internetová informační služba](./media/template-tutorial-deploy-vm-extensions/resource-manager-template-deploy-extensions-customer-script-web-server.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už prostředky Azure, které jste nasadili, nepotřebujete, vyčistěte je tak, že odstraníte skupinu prostředků.

1. V Azure Portal v levém podokně vyberte **skupinu prostředků**.
2. Do pole **filtrovat podle názvu** zadejte název skupiny prostředků.
3. Vyberte název skupiny prostředků.
    Ve skupině prostředků se zobrazí šest prostředků.
4. V horní nabídce vyberte **Odstranit skupinu prostředků**.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste nasadili virtuální počítač a rozšíření virtuálního počítače. Toto rozšíření na virtuální počítač nainstalovalo webový server služby IIS. Informace o použití rozšíření Azure SQL Database k importu souboru BACPAC naleznete v tématu:

> [!div class="nextstepaction"]
> [Nasazení rozšíření SQL](./template-tutorial-deploy-sql-extensions-bacpac.md)
