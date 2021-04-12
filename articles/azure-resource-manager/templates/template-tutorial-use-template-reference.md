---
title: Použití referenčních informací k šablonám
description: K vytvoření šablony použijte odkaz šablony Azure Resource Manager (šablona ARM).
author: mumian
ms.date: 04/23/2020
ms.topic: tutorial
ms.author: jgao
ms.custom: seodec18
ms.openlocfilehash: a44852fb2f491dd949b58217eca3e4f3e392cf17
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "97584132"
---
# <a name="tutorial-utilize-the-arm-template-reference"></a>Kurz: Využijte odkaz na šablonu ARM.

Naučte se najít informace o schématu šablony a použít je k vytváření Azure Resource Manager šablon (šablony ARM).

V tomto kurzu použijete základní šablonu ze šablon Azure pro rychlý Start. Pomocí referenční dokumentace šablon můžete šablonu přizpůsobit.

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

* Visual Studio Code s rozšířením nástrojů Správce prostředků Tools. Další informace najdete v tématu [rychlý Start: vytvoření šablon ARM pomocí Visual Studio Code](quickstart-create-templates-use-visual-studio-code.md).

## <a name="open-a-quickstart-template"></a>Otevření šablony pro rychlý start

[Šablony pro rychlý Start Azure](https://azure.microsoft.com/resources/templates/) jsou úložiště pro šablony ARM. Místo vytvoření šablony úplně od začátku si můžete najít ukázkovou šablonu a přizpůsobit ji. Šablona, kterou jsme použili v tomto rychlém startu, se nazývá [Create a standard storage account](https://azure.microsoft.com/resources/templates/101-storage-account-create/) (Vytvoření standardního účtu úložiště). Šablona definuje prostředek účtu služby Azure Storage.

1. Z Visual Studio Code vyberte **soubor**  >  **otevřít soubor**.
1. Do pole **File name** (Název souboru) vložte následující adresu URL:

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
    ```

1. Výběrem **Open** (Otevřít) soubor otevřete.
1. Vyberte **soubor**  >  **Uložit jako** a uložte soubor jako _azuredeploy.js_ do svého místního počítače.

## <a name="understand-the-schema"></a>Vysvětlené schématu

1. Z Visual Studio Code sbalte šablonu na kořenovou úroveň. Vidíte nejjednodušší strukturu s následující elementy:

    ![Nejjednodušší struktura šablony Resource Manageru](./media/template-tutorial-use-template-reference/resource-manager-template-simplest-structure.png)

    * `$schema`: zadejte umístění souboru schématu JSON, který popisuje verzi jazyka šablony.
    * `contentVersion`: zadejte libovolnou hodnotu pro tento element, aby dokumentoval významné změny v šabloně.
    * `parameters`: zadejte hodnoty, které jsou k dispozici při spuštění nasazení za účelem přizpůsobení nasazení prostředků.
    * `variables`: zadejte hodnoty, které se použijí jako fragmenty JSON v šabloně pro zjednodušení výrazů jazyka šablony.
    * `resources`: Určete typy prostředků, které se nasazují nebo aktualizují ve skupině prostředků.
    * `outputs`: zadejte hodnoty, které se vrátí po nasazení.

1. Rozbalte `resources` . Je `Microsoft.Storage/storageAccounts` definován prostředek. Název SKU používá hodnotu parametru. Je volán parametr `storageAccountType` .

    ![Definice účtu úložiště v šabloně Resource Manageru](./media/template-tutorial-use-template-reference/resource-manager-template-storage-resource.png)

1. Rozbalením `parameters` zjistíte, jak `storageAccountType` je definováno. Parametr má čtyři povolené hodnoty. Zjistíte ostatní povolené hodnoty a pak opravíte definici parametru.

    ![Skladové jednotky prostředků účtu úložiště Správce prostředků šablony](./media/template-tutorial-use-template-reference/resource-manager-template-storage-resources-skus-old.png)

## <a name="find-the-template-reference"></a>Vyhledání referenčních informací k šablonám

1. Přejděte na [odkaz šablony Azure](/azure/templates/).
1. V poli **filtrovat podle názvu** zadejte **účty úložiště** a v části **referenční > úložiště** vyberte první **účty úložiště** .

    ![Odkaz šablony Azure Resource Manageru na účet úložiště](./media/template-tutorial-use-template-reference/resource-manager-template-resources-reference-storage-accounts.png)

    Poskytovatel prostředků má obvykle několik verzí rozhraní API:

    ![Verze účtu úložiště odkazů na šablonu Správce prostředků](./media/template-tutorial-use-template-reference/resource-manager-template-resources-reference-storage-accounts-versions.png)

1. V levém podokně vyberte **všechny prostředky** v části **úložiště** . Tato stránka obsahuje seznam typů prostředků a verzí poskytovatele prostředků úložiště. Pro typy prostředků definované v šabloně doporučujeme použít nejnovější verze rozhraní API.

    ![Verze typů účtů úložiště odkazů na šablonu Správce prostředků](./media/template-tutorial-use-template-reference/resource-manager-template-resources-reference-storage-accounts-types-versions.png)

1. Vyberte nejnovější verzi `storageAccount` typu prostředku. Nejnovější verze je **2019-06-01** , pokud je tento článek napsán. Ujistěte se, že tato verze odpovídá verzi používané pro prostředek účtu úložiště ve vaší šabloně. Pokud aktualizujete verzi rozhraní API, ověřte, zda definice prostředků odpovídá odkazu na šablonu.

1. Tato stránka obsahuje podrobné informace o typu prostředku storageAccount. Například obsahuje seznam povolených hodnot pro **objekt SKU**. Existuje více SKU, než je uvedeno v šabloně pro rychlý Start, kterou jste otevřeli dříve. Šablonu pro rychlé zprovoznění můžete přizpůsobit tak, aby zahrnovala všechny dostupné typy úložišť.

    ![Správce prostředků referenčních skladů pro šablonu úložiště](./media/template-tutorial-use-template-reference/resource-manager-template-resources-reference-storage-accounts-skus.png)

## <a name="edit-the-template"></a>Úprava šablony

Z Visual Studio Code přidejte další typy účtů úložiště, jak je znázorněno na následujícím snímku obrazovky:

![Prostředky účtu úložiště pro šablonu Správce prostředků](./media/template-tutorial-use-template-reference/resource-manager-template-storage-resources-skus.png)

## <a name="deploy-the-template"></a>Nasazení šablony

1. Přihlaste se k [Azure Cloud Shell](https://shell.azure.com)

1. Vyberte prostředí **PowerShell** nebo **bash** (pro CLI) v levém horním rohu a zvolte své preferované prostředí.  Po přepnutí se vyžaduje restartování prostředí.

    ![Azure Portal Cloud Shell nahrát soubor](./media/template-tutorial-use-template-reference/azure-portal-cloud-shell-upload-file.png)

1. Vyberte **Nahrát nebo stáhnout soubory** a potom vyberte **Nahrát**. Viz předchozí snímek obrazovky. Vyberte soubor, který jste uložili v předchozí části. Po nahrání souboru můžete pomocí `ls` příkazu a `cat` příkazu ověřit, jestli se soubor úspěšně nahrál.

1. Z Cloud Shell spusťte následující příkazy. Výběrem odpovídající karty zobrazíte kód PowerShellu nebo kód rozhraní příkazového řádku.

   Když nasadíte šablonu, zadejte `storageAccountType` parametr s nově přidanou hodnotou, například **Standard_RAGRS**. Nasazení se nezdaří, pokud jste použili původní šablonu pro rychlé spuštění, protože **Standard_RAGRS** nepovoluje hodnotu.

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

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už nasazené prostředky Azure nepotřebujete, vyčistěte je odstraněním skupiny prostředků.

1. Z Azure Portal v nabídce vlevo vyberte **Skupina prostředků** .
1. Do pole **Filtrovat podle názvu** zadejte název skupiny prostředků.
1. Vyberte název skupiny prostředků.  Ve skupině prostředků uvidíte celkem šest prostředků.
1. V horní nabídce vyberte **Odstranit skupinu prostředků** .

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se dozvěděli, jak přizpůsobit existující šablonu pomocí odkazu na šablonu. Naučte se, jak vytvořit několik instancí účtu úložiště, viz:

> [!div class="nextstepaction"]
> [Vytvoření několika instancí](./template-tutorial-create-multiple-instances.md)
