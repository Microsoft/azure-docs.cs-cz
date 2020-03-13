---
title: Šablona se závislými prostředky
description: Zjistěte, jak vytvořit šablonu Azure Resource Manageru s více prostředky a jak ji nasadit pomocí webu Azure Portal
author: mumian
ms.date: 03/04/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: f60f248ec3fbbe5adfb61bf361546d1d5e238f54
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/13/2020
ms.locfileid: "79238542"
---
# <a name="tutorial-create-azure-resource-manager-templates-with-dependent-resources"></a>Kurz: Vytváření šablon Azure Resource Manageru se závislými prostředky

Naučte se vytvořit šablonu Azure Resource Manager pro nasazení několika prostředků a konfiguraci pořadí nasazení. Po vytvoření šablonu nasadíte pomocí Cloud Shellu z webu Azure Portal.

V tomto kurzu vytvoříte účet úložiště, virtuální počítač, virtuální síť a několik dalších závislých prostředků. Některé prostředky se nedají nasadit, dokud bude existovat jiný prostředek. Nemůžete třeba vytvořit virtuální počítač, dokud bude existovat jeho účet úložiště a síťové rozhraní. Tento vztah se definuje tím, že jeden prostředek označíte jako závislý na jiných prostředcích. Resource Manager vyhodnocuje závislosti mezi prostředky a provádí nasazení v závislém pořadí. Pokud na sobě prostředky nezávisí, Resource Manager je nasadí paralelně. Další informace najdete v článku [Určení pořadí pro nasazení prostředků v šablonách Azure Resource Manageru](./define-resource-dependency.md).

![Diagram pořadí nasazení závislých prostředků pro šablonu Správce prostředků](./media/template-tutorial-create-templates-with-dependent-resources/resource-manager-template-dependent-resources-diagram.png)

Tento kurz se zabývá následujícími úkony:

> [!div class="checklist"]
> * Otevření šablony pro rychlý start
> * Prozkoumání šablony
> * Nasazení šablony

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto článku potřebujete:

* Visual Studio Code s rozšířením nástrojů Správce prostředků Tools. Pokud [chcete vytvořit Azure Resource Manager šablony](use-vs-code-to-create-template.md), přečtěte si téma použití Visual Studio Code.
* Pro zlepšení zabezpečení použijte pro účet správce virtuálního počítače vygenerované heslo. Tady ukázka generování hesla:

    ```console
    openssl rand -base64 32
    ```
    Služba Azure Key Vault je určená k ochraně kryptografických klíčů a dalších tajných klíčů. Další informace najdete v [kurzu integrace služby Azure Key Vault v nasazení šablony Resource Manageru](./template-tutorial-use-key-vault.md). Zároveň doporučujeme heslo každé tři měsíce aktualizovat.

## <a name="open-a-quickstart-template"></a>Otevření šablony pro rychlý start

Šablony pro rychlý start Azure slouží jako úložiště šablon Resource Manageru. Místo vytvoření šablony úplně od začátku si můžete najít ukázkovou šablonu a přizpůsobit ji. Šablona používaná v tomto kurzu má název [Deploy a simple Windows VM](https://azure.microsoft.com/resources/templates/101-vm-simple-windows/) (Nasazení jednoduchého virtuálního počítače s Windows).

1. V nástroji Visual Studio Code vyberte **File** (Soubor) >**Open File** (Otevřít soubor).
2. Do pole **File name** (Název souboru) vložte následující adresu URL:

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json
    ```
3. Výběrem **Open** (Otevřít) soubor otevřete.
4. Vyberte **File** (Soubor) >**Save As** (Uložit jako) a soubor uložte na místní počítač pod názvem **azuredeploy.json**.

## <a name="explore-the-template"></a>Prozkoumání šablony

Při zkoumání šablony v této části zkuste zodpovědět tyto otázky:

* Kolik prostředků Azure se v této šabloně definuje?
* Jedním z prostředků je účet úložiště Azure.  Vypadá jeho definice jako ta, kterou jsme použili v posledním kurzu?
* Najdete referenční informace k šablonám pro prostředky definované v této šabloně?
* Najdete závislosti těchto prostředků?

1. V nástroji Visual Studio Code sbalte elementy tak, abyste viděli jenom elementy první úrovně a u položky **resources** elementy druhé úrovně:

    ![Šablony Azure Resource Manageru ve Visual Studio Code](./media/template-tutorial-create-templates-with-dependent-resources/resource-manager-template-visual-studio-code.png)

    Šablona definuje pět prostředků:

   * `Microsoft.Storage/storageAccounts`. Viz [referenční informace k šablonám](https://docs.microsoft.com/azure/templates/Microsoft.Storage/storageAccounts).
   * `Microsoft.Network/publicIPAddresses`. Viz [referenční informace k šablonám](https://docs.microsoft.com/azure/templates/microsoft.network/publicipaddresses).
   * `Microsoft.Network/virtualNetworks`. Viz [referenční informace k šablonám](https://docs.microsoft.com/azure/templates/microsoft.network/virtualnetworks).
   * `Microsoft.Network/networkInterfaces`. Viz [referenční informace k šablonám](https://docs.microsoft.com/azure/templates/microsoft.network/networkinterfaces).
   * `Microsoft.Compute/virtualMachines`. Viz [referenční informace k šablonám](https://docs.microsoft.com/azure/templates/microsoft.compute/virtualmachines).

     Než začnete šablonu přizpůsobovat, je vhodné se s ní nejprve trochu seznámit.

2. Rozbalte první prostředek. Jedná se o účet úložiště. Porovnejte definici prostředku s [referenčními informacemi k šablonám](https://docs.microsoft.com/azure/templates/Microsoft.Storage/storageAccounts).

    ![Šablony Azure Resource Manageru ve Visual Studio Code – definice účtu úložiště](./media/template-tutorial-create-templates-with-dependent-resources/resource-manager-template-storage-account-definition.png)

3. Rozbalte druhý prostředek. Typ prostředku je `Microsoft.Network/publicIPAddresses`. Porovnejte definici prostředku s [referenčními informacemi k šablonám](https://docs.microsoft.com/azure/templates/microsoft.network/publicipaddresses).

    ![Šablony Azure Resource Manageru ve Visual Studio Code – definice veřejné IP adresy](./media/template-tutorial-create-templates-with-dependent-resources/resource-manager-template-public-ip-address-definition.png)
4. Rozbalte čtvrtý prostředek. Typ prostředku je `Microsoft.Network/networkInterfaces`:

    ![Šablony Visual Studio Code Azure Resource Manager dependsOn](./media/template-tutorial-create-templates-with-dependent-resources/resource-manager-template-visual-studio-code-dependson.png)

    Element DependsOn umožňuje definovat jeden prostředek jako závislý na jednom nebo více prostředcích. Prostředek závisí na dvou dalších prostředcích:

    * `Microsoft.Network/publicIPAddresses`
    * `Microsoft.Network/virtualNetworks`

5. Rozbalte pátý prostředek. Tento prostředek je virtuální počítač. Ten závisí na dvou dalších prostředcích:

    * `Microsoft.Storage/storageAccounts`
    * `Microsoft.Network/networkInterfaces`

Následující diagram znázorňuje prostředky a informace o závislostech pro tuto šablonu:

![Šablony Azure Resource Manageru ve Visual Studio Code – diagram](./media/template-tutorial-create-templates-with-dependent-resources/resource-manager-template-visual-studio-code-dependency-diagram.png)

Určení závislostí umožňuje Resource Manageru účinně nasadit řešení. Paralelně nasadí účet úložiště, veřejnou IP adresu a virtuální síť, protože tyto prostředky nemají žádné závislosti. Po nasazení veřejné IP adresy a virtuální sítě se vytvoří síťové rozhraní. Po nasazení všech ostatních prostředků Resource Manager nasadí virtuální počítač.

## <a name="deploy-the-template"></a>Nasazení šablony

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Šablony můžete nasadit mnoha způsoby.  V tomto kurzu použijete Cloud Shell z webu Azure Portal.

1. Přihlaste se do služby [Cloud Shell](https://shell.azure.com).
1. V levém horním rohu služby Cloud Shell vyberte **PowerShell** a pak **Potvrdit**.  V tomto kurzu použijete prostředí PowerShell.
1. V Cloud Shellu vyberte **Nahrát soubor**:

    ![Nahrání souboru v Cloud Shellu na portálu Azure Portal](./media/template-tutorial-create-templates-with-dependent-resources/azure-portal-cloud-shell-upload-file.png)
1. Vyberte šablonu, kterou jste si v rámci tohoto kurzu uložili. Výchozí název je **azuredeploy.json**.  Pokud máte soubor se stejným názvem, starý soubor se bez upozornění přepíše.

    Volitelně můžete pomocí příkazu **ls $Home** a příkazu **Cat $Home/azuredeploy.JSON** ověřit, jestli se soubory úspěšně nahrály.

1. Ve službě Cloud Shell spusťte následující příkazy PowerShellu. Pro zlepšení zabezpečení použijte pro účet správce virtuálního počítače vygenerované heslo. Viz [Požadavky](#prerequisites).

    ```azurepowershell
    $resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
    $adminUsername = Read-Host -Prompt "Enter the virtual machine admin username"
    $adminPassword = Read-Host -Prompt "Enter the admin password" -AsSecureString
    $dnsLabelPrefix = Read-Host -Prompt "Enter the DNS label prefix"

    New-AzResourceGroup -Name $resourceGroupName -Location "$location"
    New-AzResourceGroupDeployment `
        -ResourceGroupName $resourceGroupName `
        -adminUsername $adminUsername `
        -adminPassword $adminPassword `
        -dnsLabelPrefix $dnsLabelPrefix `
        -TemplateFile "$HOME/azuredeploy.json"
    Write-Host "Press [ENTER] to continue ..."
    ```

1. Spuštěním následujícího příkazu PowerShellu zobrazíte nově vytvořený virtuální počítač:

    ```azurepowershell
    $resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
    Get-AzVM -Name SimpleWinVM -ResourceGroupName $resourceGroupName
    Write-Host "Press [ENTER] to continue ..."
    ```

    V šabloně je pevně zakódovaný název virtuálního počítače **SimpleWinVM**.

1. Ověřte úspěšné vytvoření virtuálního počítače tím, že se k němu připojíte přes protokol RDP.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už nasazené prostředky Azure nepotřebujete, vyčistěte je odstraněním skupiny prostředků.

1. Na portálu Azure Portal vyberte v nabídce nalevo **Skupina prostředků**.
2. Do pole **Filtrovat podle názvu** zadejte název skupiny prostředků.
3. Vyberte název skupiny prostředků.  Ve skupině prostředků uvidíte celkem šest prostředků.
4. V nabídce nahoře vyberte **Odstranit skupinu prostředků**.

## <a name="next-steps"></a>Další kroky

V tomto kurzu vyvinete a nasadíte šablonu pro vytvoření virtuálního počítače, virtuální sítě a závislých prostředků. Informace o tom, jak pomocí skriptů pro nasazení provádět operace před nasazením, najdete v tématech:

> [!div class="nextstepaction"]
> [Použití skriptu nasazení](./template-tutorial-deployment-script.md)
