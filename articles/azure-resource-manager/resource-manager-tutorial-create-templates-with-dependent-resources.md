---
title: Vytváření šablon Azure Resource Manageru se závislými prostředky | Microsoft Docs
description: Zjistěte, jak vytvořit šablonu Azure Resource Manageru s více prostředky a jak ji nasadit pomocí webu Azure Portal
services: azure-resource-manager
documentationcenter: ''
author: mumian
manager: dougeby
editor: tysonn
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 09/07/2018
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: fe6313c059a1dd1050240ead5f7ca8e3e1512aa6
ms.sourcegitcommit: 5843352f71f756458ba84c31f4b66b6a082e53df
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/01/2018
ms.locfileid: "47584509"
---
# <a name="tutorial-create-azure-resource-manager-templates-with-dependent-resources"></a>Kurz: Vytváření šablon Azure Resource Manageru se závislými prostředky

Zjistěte, jak vytvořit šablonu Azure Resource Manageru a nasadit více prostředků.  Po vytvoření šablonu nasadíte pomocí Cloud Shellu z webu Azure Portal.

V tomto kurzu vytvoříte účet úložiště, virtuální počítač, virtuální síť a několik dalších závislých prostředků. Některé prostředky se nedají nasadit, dokud bude existovat jiný prostředek. Nemůžete třeba vytvořit virtuální počítač, dokud bude existovat jeho účet úložiště a síťové rozhraní. Tento vztah se definuje tím, že jeden prostředek označíte jako závislý na jiných prostředcích. Resource Manager vyhodnocuje závislosti mezi prostředky a provádí nasazení v závislém pořadí. Pokud na sobě prostředky nezávisí, Resource Manager je nasadí paralelně. Další informace najdete v článku [Určení pořadí pro nasazení prostředků v šablonách Azure Resource Manageru](./resource-group-define-dependencies.md).

Tento kurz se zabývá následujícími úkony:

> [!div class="checklist"]
> * Otevření šablony rychlého startu
> * Prozkoumání šablony
> * Nasazení šablony

Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto článku potřebujete:

* [Visual Studio Code](https://code.visualstudio.com/).
* Rozšíření Nástroje Resource Manageru.  Přečtěte si, [jak toto rozšíření nainstalovat](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#prerequisites).

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

- Kolik prostředků Azure se v této šabloně definuje?
- Jedním z prostředků je účet úložiště Azure.  Vypadá jeho definice jako ta, kterou jsme použili v posledním kurzu?
- Najdete referenční informace k šablonám pro prostředky definované v této šabloně?
- Najdete závislosti těchto prostředků?

1. V nástroji Visual Studio Code sbalte elementy tak, abyste viděli jenom elementy první úrovně a u položky **resources** elementy druhé úrovně:

    ![Šablony Azure Resource Manageru ve Visual Studio Code](./media/resource-manager-tutorial-create-templates-with-dependent-resources/resource-manager-template-visual-studio-code.png)

    Šablona definuje pět prostředků.
2. Rozbalte první prostředek. Jedná se o účet úložiště. Jeho definice by měla být stejná jako ta, kterou jsme použili na začátku posledního kurzu.

    ![Šablony Azure Resource Manageru ve Visual Studio Code – definice účtu úložiště](./media/resource-manager-tutorial-create-templates-with-dependent-resources/resource-manager-template-storage-account-definition.png)

3. Rozbalte druhý prostředek. Typ tohoto prostředku je **Microsoft.Network/publicIPAddresses**. Pokud chcete najít referenční informace k šablonám, přejděte na stránku s [referenčními informacemi k šablonám](https://docs.microsoft.com/azure/templates/) a do pole **Filtrovat podle názvu** zadejte **veřejná IP adresa** nebo **veřejné IP adresy**. Porovnejte definici prostředku s referenčními informacemi k šablonám.

    ![Šablony Azure Resource Manageru ve Visual Studio Code – definice veřejné IP adresy](./media/resource-manager-tutorial-create-templates-with-dependent-resources/resource-manager-template-public-ip-address-definition.png)
4. Zopakujte poslední krok a vyhledejte referenční informace k šablonám pro ostatní prostředky definované v této šabloně.  Porovnejte definice prostředků s referenčními informacemi.
5. Rozbalte čtvrtý prostředek:

    ![Šablony Azure Resource Manageru ve Visual Studio Code – dependson](./media/resource-manager-tutorial-create-templates-with-dependent-resources/resource-manager-template-visual-studio-code-dependson.png)

    Element DependsOn umožňuje definovat jeden prostředek jako závislý na jednom nebo více prostředcích. V tomto příkladu se jedná o prostředek networkInterface.  Ten závisí na dvou dalších prostředcích:

    * publicIPAddress
    * virtualNetwork

6. Rozbalte pátý prostředek. Tento prostředek je virtuální počítač. Ten závisí na dvou dalších prostředcích:

    * storageAccount
    * networkInterface

Následující diagram znázorňuje prostředky a informace o závislostech pro tuto šablonu:

![Šablony Azure Resource Manageru ve Visual Studio Code – diagram](./media/resource-manager-tutorial-create-templates-with-dependent-resources/resource-manager-template-visual-studio-code-dependency-diagram.png)

Určení závislostí umožňuje Resource Manageru účinně nasadit řešení. Paralelně nasadí účet úložiště, veřejnou IP adresu a virtuální síť, protože tyto prostředky nemají žádné závislosti. Po nasazení veřejné IP adresy a virtuální sítě se vytvoří síťové rozhraní. Po nasazení všech ostatních prostředků Resource Manager nasadí virtuální počítač.

## <a name="deploy-the-template"></a>Nasazení šablony

Šablony můžete nasadit mnoha způsoby.  V tomto kurzu použijete Cloud Shell z webu Azure Portal.

1. Přihlaste se k portálu [Azure Portal](https://portal.azure.com).
2. V pravém horním rohu vyberte **Cloud Shell**, jak je znázorněno na tomto obrázku:

    ![Cloud Shell na portálu Azure Portal](./media/resource-manager-tutorial-create-templates-with-dependent-resources/azure-portal-cloud-shell.png)
3. V levém horním rohu Cloud Shellu vyberte **PowerShell**.  V tomto kurzu použijete prostředí PowerShell.
4. Vyberte **Restartovat**.
5. V Cloud Shellu vyberte **Nahrát soubor**:

    ![Nahrání souboru v Cloud Shellu na portálu Azure Portal](./media/resource-manager-tutorial-create-templates-with-dependent-resources/azure-portal-cloud-shell-upload-file.png)
6. Vyberte soubor, který jste si v rámci tohoto kurzu uložili. Výchozí název je **azuredeploy.json**.  Pokud máte soubor se stejným názvem, starý soubor se bez upozornění přepíše.
7. V Cloud Shellu spusťte následující příkaz, který ověří úspěšné nahrání souboru. 

    ```shell
    ls
    ```

    ![Zobrazení souboru v Cloud Shellu na portálu Azure Portal](./media/resource-manager-tutorial-create-templates-with-dependent-resources/azure-portal-cloud-shell-list-file.png)

    Název souboru na snímku obrazovky je azuredeploy.json.

8. V Cloud Shellu spusťte následující příkaz, který ověří obsah souboru JSON:

    ```shell
    cat azuredeploy.json
    ```
9. V Cloud Shellu spusťte následující příkazy PowerShellu:

    ```powershell
    $resourceGroupName = "<Enter the resource group name>"
    $location = "<Enter the Azure location>"
    $vmAdmin = "<Enter the admin username>"
    $vmPassword = "<Enter the password>"
    $dnsLabelPrefix = "<Enter the prefix>"

    New-AzureRmResourceGroup -Name $resourceGroupName -Location $location
    $vmPW = ConvertTo-SecureString -String $vmPassword -AsPlainText -Force
    New-AzureRmResourceGroupDeployment -Name mydeployment0710 -ResourceGroupName $resourceGroupName `
        -TemplateFile azuredeploy.json -adminUsername $vmAdmin -adminPassword $vmPW `
        -dnsLabelPrefix $dnsLabelPrefix
    ```
    Tady je snímek obrazovky při ukázkovém nasazení:

    ![Šablona nasazení v Cloud Shellu na portálu Azure Portal](./media/resource-manager-tutorial-create-templates-with-dependent-resources/azure-portal-cloud-shell-deploy-template.png)

    Na snímku obrazovky jsou použity tyto hodnoty:

    * **$resourceGroupName**: myresourcegroup0710. 
    * **$location**: eastus2
    * **&lt;DeployName>**: mydeployment0710
    * **&lt;TemplateFile>**: azuredeploy.json
    * **Parametry šablony**:

        * **adminUsername**: JohnDole
        * **adminPassword**: Pass@word123
        * **dnsLabelPrefix**: myvm0710

10. Spuštěním následujícího příkazu PowerShellu zobrazíte nově vytvořený virtuální počítač:

    ```powershell
    Get-AzureRmVM -Name SimpleWinVM -ResourceGroupName <ResourceGroupName>
    ```

    V šabloně je pevně zakódovaný název virtuálního počítače **SimpleWinVM**.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už nasazené prostředky Azure nepotřebujete, vyčistěte je odstraněním skupiny prostředků.

1. Na portálu Azure Portal vyberte v nabídce nalevo **Skupina prostředků**.
2. Do pole **Filtrovat podle názvu** zadejte název skupiny prostředků.
3. Vyberte název skupiny prostředků.  Ve skupině prostředků uvidíte celkem šest prostředků.
4. V nabídce nahoře vyberte **Odstranit skupinu prostředků**.

## <a name="next-steps"></a>Další kroky

V tomto kurzu vyvinete a nasadíte šablonu pro vytvoření virtuálního počítače, virtuální sítě a závislých prostředků. Informace o tom, jak nasazovat prostředky Azure na základě podmínek, najdete v tomto tématu:


> [!div class="nextstepaction"]
> [Použití podmínek](./resource-manager-tutorial-use-conditions.md)

