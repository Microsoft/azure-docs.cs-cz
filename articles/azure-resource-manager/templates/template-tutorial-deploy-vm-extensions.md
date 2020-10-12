---
title: Nasazení rozšíření virtuálních počítačů pomocí šablony
description: Zjistěte, jak nasazovat rozšíření virtuálních počítačů pomocí šablon Azure Resource Manageru.
author: mumian
ms.date: 04/23/2020
ms.topic: tutorial
ms.author: jgao
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: f82e0eb45f4bc7c3260554b1b1120025029336bc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89073638"
---
# <a name="tutorial-deploy-virtual-machine-extensions-with-arm-templates"></a>Kurz: nasazení rozšíření virtuálních počítačů pomocí šablon ARM

Zjistěte, jak pomocí [rozšíření virtuálních počítačů Azure](../../virtual-machines/extensions/features-windows.md) provádět na virtuálních počítačích Azure úlohy konfigurace a automatizace po nasazení. Pro použití s virtuálními počítači Azure je k dispozici řada různých rozšíření virtuálních počítačů. V tomto kurzu nasadíte rozšíření vlastních skriptů ze šablony Azure Resource Manager (ARM), ve kterém spustíte skript prostředí PowerShell na virtuálním počítači s Windows.  Tento skript na virtuálním počítači nainstaluje webový server.

Tento kurz se zabývá následujícími úkony:

> [!div class="checklist"]
> * Příprava skriptu PowerShellu
> * Otevření šablony rychlého startu
> * Úprava šablony
> * Nasazení šablony

Pokud předplatné Azure ještě nemáte, napřed si [vytvořte bezplatný účet](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto článku potřebujete:

* Visual Studio Code s rozšířením nástrojů Správce prostředků Tools. Další informace najdete v tématu [rychlý Start: vytváření Azure Resource Manager šablon pomocí Visual Studio Code](quickstart-create-templates-use-visual-studio-code.md).
* Pro zlepšení zabezpečení použijte pro účet správce virtuálního počítače vygenerované heslo. Tady ukázka generování hesla:

    ```console
    openssl rand -base64 32
    ```

    Služba Azure Key Vault je určená k ochraně kryptografických klíčů a dalších tajných klíčů. Další informace najdete v tématu [kurz: integrace Azure Key Vault v nasazení šablony ARM](./template-tutorial-use-key-vault.md). Doporučujeme vám také aktualizovat heslo každé tři měsíce.

## <a name="prepare-a-powershell-script"></a>Příprava skriptu PowerShellu

Můžete použít vložený skript prostředí PowerShell nebo soubor skriptu.  V tomto kurzu se dozvíte, jak použít soubor skriptu. Z [GitHubu](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorial-vm-extension/installWebServer.ps1)se sdílí skript prostředí PowerShell s následujícím obsahem:

```azurepowershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

Pokud se rozhodnete publikovat soubor ve vlastním umístění, aktualizujte ho `fileUri` v šabloně později v tomto kurzu.

## <a name="open-a-quickstart-template"></a>Otevření šablony rychlého startu

Šablony pro rychlý Start Azure jsou úložiště pro šablony ARM. Místo vytvoření šablony úplně od začátku si můžete najít ukázkovou šablonu a přizpůsobit ji. Šablona používaná v tomto kurzu má název [Deploy a simple Windows VM](https://azure.microsoft.com/resources/templates/101-vm-simple-windows/) (Nasazení jednoduchého virtuálního počítače s Windows).

1. V Visual Studio Code vyberte **soubor**  >  **otevřít soubor**.
1. Do pole **název souboru** vložte následující adresu URL:

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json
    ```

1. Chcete-li otevřít soubor, vyberte možnost **otevřít**.
    Šablona definuje pět prostředků:

   * [**Microsoft. Storage/storageAccounts**](/azure/templates/Microsoft.Storage/storageAccounts).
   * [**Microsoft. Network/publicIPAddresses**](/azure/templates/microsoft.network/publicipaddresses).
   * [**Microsoft. Network/networkSecurityGroups**](/azure/templates/microsoft.network/networksecuritygroups).
   * [**Microsoft. Network/virtualNetworks**](/azure/templates/microsoft.network/virtualnetworks).
   * [**Microsoft. Network/networkInterfaces**](/azure/templates/microsoft.network/networkinterfaces).
   * [**Microsoft. COMPUTE/virtualMachines**](/azure/templates/microsoft.compute/virtualmachines).

     Před přizpůsobením šablony je užitečné získat základní informace o této šabloně.

1. Uložte kopii souboru do místního počítače s názvem *azuredeploy.js* , a to tak, že vyberete **soubor**  >  **Uložit jako**.

## <a name="edit-the-template"></a>Úprava šablony

Ke stávající šabloně s následujícím obsahem přidejte prostředek rozšíření virtuálního počítače:

```json
{
  "type": "Microsoft.Compute/virtualMachines/extensions",
  "apiVersion": "2019-12-01",
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

Další informace o této definici prostředků najdete v odkazu na [rozšíření](/azure/templates/microsoft.compute/virtualmachines/extensions). Tady je několik důležitých elementů:

* **name:** Vzhledem k tomu, že prostředek rozšíření je podřízeným prostředkem objektu virtuálního počítače, musí název obsahovat předponu virtuálního počítače. Viz [Nastavení názvu a typu pro podřízené prostředky](child-resource-name-type.md).
* **dependsOn**: po vytvoření virtuálního počítače vytvořte prostředek rozšíření.
* **identifikátory URI**: umístění, kde jsou uloženy soubory skriptu. Pokud se rozhodnete, že nepoužijete zadané umístění, je nutné aktualizovat hodnoty.
* **commandToExecute**: Tento příkaz vyvolá skript.

Pokud chcete použít vložený skript, odeberte **identifikátory URI**a aktualizujte **commandToExecute** na:

```powershell
powershell.exe Install-WindowsFeature -name Web-Server -IncludeManagementTools && powershell.exe remove-item 'C:\\inetpub\\wwwroot\\iisstart.htm' && powershell.exe Add-Content -Path 'C:\\inetpub\\wwwroot\\iisstart.htm' -Value $('Hello World from ' + $env:computername)
```

Tento vložený skript také aktualizuje obsah iisstart.html.

Musíte taky otevřít port HTTP, abyste mohli získat přístup k webovému serveru.

1. V šabloně vyhledejte **securityRules** .
1. Do pole **výchozí-Allow-3389**přidejte následující pravidlo.

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

Postup nasazení najdete v části "nasazení šablony" v tématu [kurz: vytvoření šablon ARM se závislými prostředky](./template-tutorial-create-templates-with-dependent-resources.md#deploy-the-template). Doporučujeme použít vygenerované heslo pro účet správce virtuálního počítače. Viz část [požadavky](#prerequisites) tohoto článku.

Z Cloud Shell pro načtení veřejné IP adresy virtuálního počítače spusťte následující příkaz:

```azurepowershell
(Get-AzPublicIpAddress -ResourceGroupName $resourceGroupName).IpAddress
```

Vložte IP adresu do webového prohlížeče. Otevře se výchozí úvodní stránka Internetová informační služba (IIS):

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
