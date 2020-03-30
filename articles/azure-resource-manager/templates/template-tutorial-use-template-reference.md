---
title: Využití referenčních informací k šablonám
description: Pomocí odkazu na šablonu Azure Resource Manageru vytvořte šablonu.
author: mumian
ms.date: 03/27/2020
ms.topic: tutorial
ms.author: jgao
ms.custom: seodec18
ms.openlocfilehash: b742982121a20a2b057eba4211584b0386dde411
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "80373425"
---
# <a name="tutorial-utilize-the-arm-template-reference"></a>Kurz: Využití odkazu na šablonu ARM

Zjistěte, jak najít informace o schématu šablony a použít tyto informace k vytvoření šablon Azure Resource Manager (ARM).

V tomto kurzu použijete základní šablonu Azure pro rychlý start. Pomocí referenční dokumentace šablony můžete šablonu přizpůsobit.

![Odkaz na šablonu správce prostředků na nasazení účtu úložiště](./media/template-tutorial-use-template-reference/resource-manager-template-tutorial-deploy-storage-account.png)

Tento kurz se zabývá následujícími úkony:

> [!div class="checklist"]
> * Otevření šablony pro rychlý start
> * Vysvětlení šablony
> * Vyhledání referenčních informací k šablonám
> * Úprava šablony
> * Nasazení šablony

Pokud nemáte předplatné Azure, [vytvořte si bezplatný účet,](https://azure.microsoft.com/free/) než začnete.

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto článku potřebujete:

* Visual Studio Kód s rozšířením Nástroje Správce prostředků. Viz [Použití kódu Visual Studia k vytvoření šablon ARM](use-vs-code-to-create-template.md).

## <a name="open-a-quickstart-template"></a>Otevření šablony pro rychlý start

[Azure Quickstart Templates](https://azure.microsoft.com/resources/templates/) je úložiště pro šablony ARM. Místo vytvoření šablony úplně od začátku si můžete najít ukázkovou šablonu a přizpůsobit ji. Šablona, kterou jsme použili v tomto rychlém startu, se nazývá [Create a standard storage account](https://azure.microsoft.com/resources/templates/101-storage-account-create/) (Vytvoření standardního účtu úložiště). Šablona definuje prostředek účtu služby Azure Storage.

1. V kódu sady Visual Studio vyberte **Soubor**>**otevřít soubor**.
1. Do pole **File name** (Název souboru) vložte následující adresu URL:

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
    ```

1. Výběrem **Open** (Otevřít) soubor otevřete.
1. Vyberte **Soubor**>**Uložit jako,** chcete-li soubor uložit jako **azuredeploy.json** do místního počítače.

## <a name="understand-the-schema"></a>Vysvětlené schématu

1. V nástroji VS Code sbalte šablony na kořenovou úroveň. Vidíte nejjednodušší strukturu s následující elementy:

    ![Nejjednodušší struktura šablony Resource Manageru](./media/template-tutorial-use-template-reference/resource-manager-template-simplest-structure.png)

    * **$schema**: zadejte umístění souboru schématu JSON, který popisuje verzi jazyka šablony.
    * **contentVersion**: pro tento element můžete zvolit libovolnou hodnotu, která dokumentuje významné změny v šabloně.
    * **parameters**: přizpůsobte nasazení prostředků zadáním hodnot, které se použijí při provádění nasazení.
    * **variables**: určete hodnoty sloužící v šabloně jako fragmenty formátu JSON, aby se zjednodušily výrazy jazyka šablony.
    * **resources**: určete typy prostředků nasazovaných nebo aktualizovaných ve skupině prostředků.
    * **outputs**: uvádí hodnoty vrácené po nasazení.

1. Rozbalte element **resources**. Obsahuje definici prostředku `Microsoft.Storage/storageAccounts`.

    ![Definice účtu úložiště v šabloně Resource Manageru](./media/template-tutorial-use-template-reference/resource-manager-template-storage-resource.png)

## <a name="find-the-template-reference"></a>Vyhledání referenčních informací k šablonám

1. Přejděte na [odkaz na šablonu Azure](https://docs.microsoft.com/azure/templates/).
1. Do pole **Filtrpodle nadpisu** zadejte **účty úložiště**a vyberte první **účty úložiště** v části Odkaz **> úložiště**.

    ![Odkaz šablony Azure Resource Manageru na účet úložiště](./media/template-tutorial-use-template-reference/resource-manager-template-resources-reference-storage-accounts.png)

    Poskytovatel prostředků má obvykle několik verzí rozhraní API:

    ![Verze účtu úložiště pro odkaz na šablonu správce prostředků](./media/template-tutorial-use-template-reference/resource-manager-template-resources-reference-storage-accounts-versions.png)

1. V levém podokně vyberte **Všechny prostředky** v části **Úložiště.** Na této stránce jsou uvedeny typy prostředků a verze zprostředkovatele prostředků úložiště. Doporučujeme použít nejnovější verze rozhraní API pro typy prostředků definované v šabloně.

    ![Verze účtů úložiště pro odkazy na šablony Správce prostředků](./media/template-tutorial-use-template-reference/resource-manager-template-resources-reference-storage-accounts-types-versions.png)

1. Vyberte nejnovější verzi typu prostředku **účtu úložiště.**  Nejnovější verze je **2019-06-01** při napsání tohoto článku.

1. Na této stránce jsou uvedeny podrobnosti o typu prostředku account úložiště.  Například uvádí povolené hodnoty pro objekt **Sku.** Existuje více skus než to, co je uvedeno v šabloně rychlého startu, kterou jste otevřeli dříve. Šablonu rychlého startu můžete přizpůsobit tak, aby zahrnovala všechny dostupné typy úložišť.

    ![Účet úložiště šablony Správce prostředků skus](./media/template-tutorial-use-template-reference/resource-manager-template-resources-reference-storage-accounts-skus.png)

## <a name="edit-the-template"></a>Úprava šablony

Z kódu Visual Studia přidejte další typy účtů úložiště, jak je znázorněno na následujícím snímku obrazovky:

![Prostředky účtu úložiště šablony Správce prostředků](./media/template-tutorial-use-template-reference/resource-manager-template-storage-resources-skus.png)

## <a name="deploy-the-template"></a>Nasazení šablony

Informace o procesu nasazení najdete v části [Nasazení šablony](quickstart-create-templates-use-visual-studio-code.md#deploy-the-template) v rychlém startu pro Visual Studio Code. Při nasazení šablony zadejte parametr **storageAccountType** s nově přidanou hodnotou, například **Premium_ZRS**. Nasazení by se nezdaří, pokud použijete původní šablonu rychlého **startu,** protože Premium_ZRS nebyla povolená hodnota.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už nasazené prostředky Azure nepotřebujete, vyčistěte je odstraněním skupiny prostředků.

1. Na portálu Azure vyberte **skupinu prostředků** z levé nabídky.
2. Do pole **Filtrovat podle názvu** zadejte název skupiny prostředků.
3. Vyberte název skupiny prostředků.  Ve skupině prostředků uvidíte celkem šest prostředků.
4. V horní nabídce vyberte **Odstranit skupinu prostředků.**

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se dozvěděli, jak přizpůsobit existující šablonu pomocí odkazu na šablonu. Naučte se, jak vytvořit několik instancí účtu úložiště, viz:

> [!div class="nextstepaction"]
> [Vytvoření víc instancí](./template-tutorial-create-multiple-instances.md)
