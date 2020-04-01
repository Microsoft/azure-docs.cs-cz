---
title: Nasazení rozšíření virtuálních počítače pomocí šablony
description: Zjistěte, jak nasazovat rozšíření virtuálních počítačů pomocí šablon Azure Resource Manageru.
author: mumian
ms.date: 03/31/2020
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 7397e9387fe3354a926ed607a9132ab6ddc7e785
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/01/2020
ms.locfileid: "80477595"
---
# <a name="tutorial-deploy-virtual-machine-extensions-with-arm-templates"></a>Kurz: Nasazení rozšíření virtuálních strojů pomocí šablon ARM

Zjistěte, jak pomocí [rozšíření virtuálních počítačů Azure](../../virtual-machines/extensions/features-windows.md) provádět na virtuálních počítačích Azure úlohy konfigurace a automatizace po nasazení. Pro použití s virtuálními počítači Azure je k dispozici řada různých rozšíření virtuálních počítačů. V tomto kurzu nasadíte rozšíření vlastního skriptu ze šablony Správce prostředků Azure (ARM) ke spuštění skriptu PowerShellu na virtuálním počítači s Windows.  Tento skript na virtuálním počítači nainstaluje webový server.

Tento kurz se zabývá následujícími úkony:

> [!div class="checklist"]
> * Příprava skriptu PowerShellu
> * Otevření šablony rychlého startu
> * Úprava šablony
> * Nasazení šablony
> * Ověření nasazení

Pokud nemáte předplatné Azure, [vytvořte si bezplatný účet,](https://azure.microsoft.com/free/) než začnete.

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto článku potřebujete:

* Visual Studio Kód s rozšířením Nástroje Správce prostředků. Viz [Použití kódu Visual Studia k vytvoření šablon ARM](use-vs-code-to-create-template.md).
* Pro zlepšení zabezpečení použijte pro účet správce virtuálního počítače vygenerované heslo. Tady ukázka generování hesla:

    ```console
    openssl rand -base64 32
    ```

    Služba Azure Key Vault je určená k ochraně kryptografických klíčů a dalších tajných klíčů. Další informace naleznete [v tématu: Integrace trezoru klíčů Azure v nasazení šablony ARM](./template-tutorial-use-key-vault.md). Doporučujeme také aktualizovat heslo každé tři měsíce.

## <a name="prepare-a-powershell-script"></a>Příprava skriptu PowerShellu

Skript PowerShellu s následujícím obsahem je sdílen z [GitHubu](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorial-vm-extension/installWebServer.ps1):

```azurepowershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

Pokud se rozhodnete publikovat soubor do vlastního umístění, `fileUri` je nutné aktualizovat prvek v šabloně dále v kurzu.

## <a name="open-a-quickstart-template"></a>Otevření šablony rychlého startu

Azure Quickstart Templates je úložiště pro šablony ARM. Místo vytvoření šablony úplně od začátku si můžete najít ukázkovou šablonu a přizpůsobit ji. Šablona používaná v tomto kurzu má název [Deploy a simple Windows VM](https://azure.microsoft.com/resources/templates/101-vm-simple-windows/) (Nasazení jednoduchého virtuálního počítače s Windows).

1. V kódu sady Visual Studio vyberte **Soubor** > **otevřít soubor**.
1. Do pole **Název souboru** vložte následující adresu URL:https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json

1. Chcete-li soubor otevřít, vyberte **otevřít**.
    Šablona definuje pět zdrojů:

   * **Microsoft.Storage/storageAccounts**. Viz [referenční informace k šablonám](https://docs.microsoft.com/azure/templates/Microsoft.Storage/storageAccounts).
   * **Microsoft.Network/publicIPAdresy**. Viz [referenční informace k šablonám](https://docs.microsoft.com/azure/templates/microsoft.network/publicipaddresses).
   * **Microsoft.Network/virtualNetworks**. Viz [referenční informace k šablonám](https://docs.microsoft.com/azure/templates/microsoft.network/virtualnetworks).
   * **Microsoft.Network/networkInterfaces**. Viz [referenční informace k šablonám](https://docs.microsoft.com/azure/templates/microsoft.network/networkinterfaces).
   * **Microsoft.Compute/virtualMachines**. Viz [referenční informace k šablonám](https://docs.microsoft.com/azure/templates/microsoft.compute/virtualmachines).

     Je užitečné získat některé základní znalosti šablony před přizpůsobením.

1. Uložte kopii souboru do místního počítače s názvem *azuredeploy.json* výběrem **možnosti Uložit soubor** > **jako**.

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

Další informace o této definici prostředků naleznete v [odkazu na rozšíření](https://docs.microsoft.com/azure/templates/microsoft.compute/virtualmachines/extensions). Tady je několik důležitých elementů:

* **name:** Vzhledem k tomu, že prostředek rozšíření je podřízeným prostředkem objektu virtuálního počítače, musí název obsahovat předponu virtuálního počítače. Viz [Nastavit název a typ pro podřízené prostředky](child-resource-name-type.md).
* **dependsOn**: Vytvořte prostředek rozšíření po vytvoření virtuálního počítače.
* **fileUris**: Umístění, kde jsou uloženy soubory skriptu. Pokud se rozhodnete nepoužívat zadaný umístění, je třeba aktualizovat hodnoty.
* **commandToExecute**: Tento příkaz vyvolá skript.

Je také nutné otevřít port HTTP, abyste měli přístup k webovému serveru.

1. V šabloně najdete **pravidla zabezpečení.**
1. Přidejte následující pravidlo vedle **default-allow-3389**.

    ```json
    {
      "name": "AllowHTTPInBound",
      "properties": {
        "priority": 1010,
        "access": "Allow",
        "direction": "Inbound",
        "destinationPortRange": "80",
        "protocol": "Tcp",
        "sourcePortRange": "*",
        "sourceAddressPrefix": "*",
        "destinationAddressPrefix": "*"
      }
    }
    ```

## <a name="deploy-the-template"></a>Nasazení šablony

Postup nasazení naleznete v části "Nasazení šablony" [v kurzu: Vytvoření šablon ARM se závislými prostředky](./template-tutorial-create-templates-with-dependent-resources.md#deploy-the-template). Doporučujeme použít vygenerované heslo pro účet správce virtuálního počítače. Viz část [Požadavky](#prerequisites) tohoto článku.

## <a name="verify-the-deployment"></a>Ověření nasazení

1. Na webu Azure Portal vyberte virtuální počítač.
1. V přehledu virtuálního počítačů zkopírujte IP adresu tak, že vyberete **Kliknutím zkopírujete**a pak ji vložte na kartu prohlížeče. Otevře se výchozí úvodní stránka Internetové informační služby (IIS):

![Uvítací stránka Internetové informační služby](./media/template-tutorial-deploy-vm-extensions/resource-manager-template-deploy-extensions-customer-script-web-server.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Když už nepotřebujete prostředky Azure, které jste nasadili, vyčistěte je odstraněním skupiny prostředků.

1. Na portálu Azure v levém podokně vyberte **skupinu prostředků**.
2. Do pole **Filtr podle názvu** zadejte název skupiny prostředků.
3. Vyberte název skupiny prostředků.
    Ve skupině prostředků je zobrazeno šest prostředků.
4. V horní nabídce vyberte **Odstranit skupinu prostředků**.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste nasadili virtuální počítač a rozšíření virtuálního počítače. Toto rozšíření na virtuální počítač nainstalovalo webový server služby IIS. Informace o tom, jak importovat soubor BACPAC pomocí rozšíření Azure SQL Database, najdete v tématu:

> [!div class="nextstepaction"]
> [Nasazení rozšíření SQL](./template-tutorial-deploy-sql-extensions-bacpac.md)
