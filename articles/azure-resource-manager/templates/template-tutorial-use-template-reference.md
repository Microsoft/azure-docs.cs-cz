---
title: Použití referenčních informací k šablonám
description: K vytvoření šablony použijte odkaz na šablonu Azure Resource Manager.
author: mumian
ms.date: 04/23/2020
ms.topic: tutorial
ms.author: jgao
ms.custom: seodec18
ms.openlocfilehash: 3709511fa8da0a40f4faf4ab2dac9505d69003ab
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "86118507"
---
# <a name="tutorial-utilize-the-resource-manager-template-reference"></a>Kurz: využití odkazu na šablonu Správce prostředků

Naučte se najít informace o schématu šablony a použít je k vytvoření šablon Azure Resource Manager (ARM).

V tomto kurzu použijete základní šablonu Azure pro rychlý start. Pomocí referenční dokumentace šablon můžete šablonu přizpůsobit.

![Odkaz na šablonu Správce prostředků nasazení účtu úložiště](./media/template-tutorial-use-template-reference/resource-manager-template-tutorial-deploy-storage-account.png)

Tento kurz se zabývá následujícími úkony:

> [!div class="checklist"]
> * Otevření šablony pro rychlý start
> * Vysvětlení šablony
> * Vyhledání referenčních informací k šablonám
> * Úprava šablony
> * Nasazení šablony

Pokud předplatné Azure ještě nemáte, napřed si [vytvořte bezplatný účet](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto článku potřebujete:

* Visual Studio Code s rozšířením nástrojů Správce prostředků Tools. Další informace najdete v tématu [rychlý Start: vytváření Azure Resource Manager šablon pomocí Visual Studio Code](quickstart-create-templates-use-visual-studio-code.md).

## <a name="open-a-quickstart-template"></a>Otevření šablony pro rychlý start

[Šablony pro rychlý Start Azure](https://azure.microsoft.com/resources/templates/) jsou úložiště pro šablony ARM. Místo vytvoření šablony úplně od začátku si můžete najít ukázkovou šablonu a přizpůsobit ji. Šablona, kterou jsme použili v tomto rychlém startu, se nazývá [Create a standard storage account](https://azure.microsoft.com/resources/templates/101-storage-account-create/) (Vytvoření standardního účtu úložiště). Šablona definuje prostředek účtu služby Azure Storage.

1. Z Visual Studio Code vyberte **soubor** > **otevřít soubor**.
1. Do pole **File name** (Název souboru) vložte následující adresu URL:

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
    ```

1. Výběrem **Open** (Otevřít) soubor otevřete.
1. Vyberte **soubor** > **Uložit jako** a uložte soubor jako **azuredeploy.js** do svého místního počítače.

## <a name="understand-the-schema"></a>Vysvětlené schématu

1. V nástroji VS Code sbalte šablony na kořenovou úroveň. Vidíte nejjednodušší strukturu s následující elementy:

    ![Nejjednodušší struktura šablony Resource Manageru](./media/template-tutorial-use-template-reference/resource-manager-template-simplest-structure.png)

    * **$schema**: zadejte umístění souboru schématu JSON, který popisuje verzi jazyka šablony.
    * **contentVersion**: pro tento element můžete zvolit libovolnou hodnotu, která dokumentuje významné změny v šabloně.
    * **parameters**: přizpůsobte nasazení prostředků zadáním hodnot, které se použijí při provádění nasazení.
    * **variables**: určete hodnoty sloužící v šabloně jako fragmenty formátu JSON, aby se zjednodušily výrazy jazyka šablony.
    * **resources**: určete typy prostředků nasazovaných nebo aktualizovaných ve skupině prostředků.
    * **outputs**: uvádí hodnoty vrácené po nasazení.

1. Rozbalte element **resources**. Obsahuje definici prostředku `Microsoft.Storage/storageAccounts`. Název SKU používá hodnotu parametru.  Parametr se nazývá **storageAccountType**.

    ![Definice účtu úložiště v šabloně Resource Manageru](./media/template-tutorial-use-template-reference/resource-manager-template-storage-resource.png)

1. Rozbalením **parametrů** zjistíte, jak je definována **storageAccountType** . Parametr má čtyři povolené hodnoty. Zjistíte ostatní povolené hodnoty a pak opravíte definici parametru.

    ![Skladové jednotky prostředků účtu úložiště Správce prostředků šablony](./media/template-tutorial-use-template-reference/resource-manager-template-storage-resources-skus-old.png)

## <a name="find-the-template-reference"></a>Vyhledání referenčních informací k šablonám

1. Přejděte na [odkaz šablony Azure](/azure/templates/).
1. V poli **filtrovat podle názvu** zadejte **účty úložiště**a v části **referenční > úložiště**vyberte první **účty úložiště** .

    ![Odkaz šablony Azure Resource Manageru na účet úložiště](./media/template-tutorial-use-template-reference/resource-manager-template-resources-reference-storage-accounts.png)

    Poskytovatel prostředků má obvykle několik verzí rozhraní API:

    ![Verze účtu úložiště odkazů na šablonu Správce prostředků](./media/template-tutorial-use-template-reference/resource-manager-template-resources-reference-storage-accounts-versions.png)

1. V levém podokně vyberte **všechny prostředky** v části **úložiště** . Tato stránka obsahuje seznam typů prostředků a verzí poskytovatele prostředků úložiště. Pro typy prostředků definované v šabloně doporučujeme použít nejnovější verze rozhraní API.

    ![Verze typů účtů úložiště odkazů na šablonu Správce prostředků](./media/template-tutorial-use-template-reference/resource-manager-template-resources-reference-storage-accounts-types-versions.png)

1. Vyberte nejnovější verzi typu prostředku **storageAccount** . Nejnovější verze je **2019-06-01** , pokud je tento článek napsán. Ujistěte se, že tato verze odpovídá verzi používané pro prostředek účtu úložiště ve vaší šabloně. Pokud aktualizujete verzi rozhraní API, ověřte, zda definice prostředků odpovídá odkazu na šablonu.

1. Tato stránka obsahuje podrobné informace o typu prostředku storageAccount.  Například obsahuje seznam povolených hodnot pro objekt **SKU** . Existuje více SKU, než je uvedeno v šabloně pro rychlý Start, kterou jste otevřeli dříve. Šablonu pro rychlé zprovoznění můžete přizpůsobit tak, aby zahrnovala všechny dostupné typy úložišť.

    ![Správce prostředků referenčních skladů pro šablonu úložiště](./media/template-tutorial-use-template-reference/resource-manager-template-resources-reference-storage-accounts-skus.png)

## <a name="edit-the-template"></a>Úprava šablony

Z Visual Studio Code přidejte další typy účtů úložiště, jak je znázorněno na následujícím snímku obrazovky:

![Prostředky účtu úložiště pro šablonu Správce prostředků](./media/template-tutorial-use-template-reference/resource-manager-template-storage-resources-skus.png)

## <a name="deploy-the-template"></a>Nasazení šablony

1. Přihlaste se k [Azure Cloud Shell](https://shell.azure.com)

1. Vyberte prostředí **PowerShell** nebo **bash** (pro CLI) v levém horním rohu a zvolte své preferované prostředí.  Po přepnutí se vyžaduje restartování prostředí.

    ![Azure Portal Cloud Shell nahrát soubor](./media/template-tutorial-use-template-reference/azure-portal-cloud-shell-upload-file.png)

1. Vyberte **Nahrát nebo stáhnout soubory** a potom vyberte **Nahrát**. Viz předchozí snímek obrazovky. Vyberte soubor, který jste uložili v předchozí části. Po nahrání souboru můžete pomocí příkazu **ls** a příkazu **Cat** ověřit, jestli se soubor úspěšně nahrál.

1. Z Cloud Shell spusťte následující příkazy. Výběrem odpovídající karty zobrazíte kód PowerShellu nebo kód rozhraní příkazového řádku.

    # <a name="cli"></a>[Rozhraní příkazového řádku](#tab/CLI)

    ```azurecli
    echo "Enter a project name that is used to generate resource group name:" &&
    read projectName &&
    echo "Enter the location (i.e. centralus):" &&
    read location &&
    resourceGroupName="${projectName}rg" &&
    az group create --name $resourceGroupName --location "$location" &&
    az deployment group create --resource-group $resourceGroupName --template-file "$HOME/azuredeploy.json" --parameters storageAccountType='Standard_RAGRS'
    ```

    # <a name="powershell"></a>[PowerShell](#tab/PowerShell)

    ```azurepowershell
    $projectName = Read-Host -Prompt "Enter a project name that is used to generate resource group name"
    $location = Read-Host -Prompt "Enter the location (i.e. centralus)"
    $resourceGroupName = "${projectName}rg"

    New-AzResourceGroup -Name $resourceGroupName -Location "$location"
    New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateFile "$HOME/azuredeploy.json" -storageAccountType "Standard_RAGRS"
    ```

    ---

 Když nasadíte šablonu, zadejte parametr **storageAccountType** s nově přidanou hodnotou, například **Standard_RAGRS**. Nasazení se nezdaří, pokud použijete původní šablonu pro rychlé spuštění, protože **Standard_RAGRS** nebyla povolená hodnota.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už nasazené prostředky Azure nepotřebujete, vyčistěte je odstraněním skupiny prostředků.

1. Z Azure Portal v nabídce vlevo vyberte **Skupina prostředků** .
2. Do pole **Filtrovat podle názvu** zadejte název skupiny prostředků.
3. Vyberte název skupiny prostředků.  Ve skupině prostředků uvidíte celkem šest prostředků.
4. V horní nabídce vyberte **Odstranit skupinu prostředků** .

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se dozvěděli, jak přizpůsobit existující šablonu pomocí odkazu na šablonu. Naučte se, jak vytvořit několik instancí účtu úložiště, viz:

> [!div class="nextstepaction"]
> [Vytvoření několika instancí](./template-tutorial-create-multiple-instances.md)
