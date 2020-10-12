---
title: Zjistit vlastnosti prostředku
description: Popisuje, jak vyhledat vlastnosti prostředku.
ms.topic: conceptual
ms.date: 06/10/2020
ms.openlocfilehash: c8bbe0dcb1bc9dc9751a1dc0d0b98a6368473546
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91327744"
---
# <a name="discover-resource-properties"></a>Zjistit vlastnosti prostředku

Před vytvořením Správce prostředků šablon potřebujete pochopit, jaké typy prostředků jsou k dispozici a jaké hodnoty mají být použity v šabloně. V tomto článku se dozvíte, jak můžete najít vlastnosti, které chcete zahrnout do šablony.

## <a name="find-resource-provider-namespaces"></a>Najít obory názvů poskytovatele prostředků

Prostředky v šabloně ARM jsou definované pomocí oboru názvů poskytovatele prostředků a typu prostředku. Například Microsoft. Storage/storageAccounts je úplný název typu prostředku účtu úložiště. Microsoft. Storage je obor názvů. Pokud ještě neznáte obory názvů pro typy prostředků, které chcete použít, přečtěte si téma [poskytovatelé prostředků pro služby Azure](../management/azure-services-resource-providers.md).

![Správce prostředků mapování oboru názvů poskytovatele prostředků](./media/view-resources/resource-provider-namespace-and-azure-service-mapping.png)

## <a name="export-templates"></a>Export šablon

Nejjednodušší způsob, jak získat vlastnosti šablony pro stávající prostředky, je exportovat šablonu. Další informace najdete v tématu [Export jednoho a více prostředků do šablony v Azure Portal](./export-template-portal.md).

## <a name="use-resource-manager-tools-extension"></a>Použít rozšíření Správce prostředků nástrojů

Visual Studio Code a rozšíření nástroje Azure Resource Manager Tools vám pomohou zobrazit přesně ty vlastnosti, které jsou potřeba pro jednotlivé typy prostředků. Poskytují IntelliSense a fragmenty kódu, které zjednodušují definování prostředku ve vaší šabloně. Další informace najdete v tématu [rychlý Start: vytvoření Azure Resource Manager šablon pomocí Visual Studio Code](./quickstart-create-templates-use-visual-studio-code.md#add-an-azure-resource).

Na následujícím snímku obrazovky vidíte, že je prostředek účtu úložiště přidaný do šablony:

![Fragmenty rozšíření Správce prostředkůch nástrojů](./media/view-resources/resource-manager-tools-extension-snippets.png)

Rozšíření také poskytuje seznam možností pro vlastnosti konfigurace.

![Konfigurovatelné hodnoty rozšíření nástrojů pro Správce prostředků](./media/view-resources/resource-manager-tools-extension-configurable-properties.png)

## <a name="use-template-reference"></a>Použití referenčních informací k šablonám

Odkaz na šablonu Azure Resource Manager je nejucelenější prostředek pro schéma šablon. Můžete najít verze rozhraní API, formát šablony a informace o vlastnostech.

1. Přejděte na [Azure Resource Manager odkaz na šablonu](/azure/templates/).
1. V levém navigačním panelu vyberte **úložiště**a pak vyberte **všechny prostředky**. Stránka všechny prostředky shrnuje typy prostředků a verze.

    ![verze prostředků odkazu na šablonu](./media/view-resources/resource-manager-template-reference-resource-versions.png)

    Pokud znáte typ prostředku, můžete přejít přímo na tuto stránku pomocí následujícího formátu adresy URL: `https://docs.microsoft.com/azure/templates/{provider-namespace}/{resource-type}` .

1. Vyberte nejnovější verzi. Doporučuje se použít nejnovější verzi rozhraní API.

    Oddíl **formát šablony** obsahuje seznam všech vlastností účtu úložiště. **SKU** je v seznamu:

    ![Formát účtu úložiště odkazů na šablonu](./media/view-resources/resource-manager-template-reference-storage-account-sku.png)

    Posuňte se dolů a zobrazte **objekt SKU** v části **hodnoty vlastností** . V tomto článku jsou uvedeny povolené hodnoty pro název SKU:

    ![Referenční informace o šabloně – hodnoty SKU účtu úložiště](./media/view-resources/resource-manager-template-reference-storage-account-sku-values.png)

    Na konci stránky se v části šablony pro **rychlé** zprovoznění zobrazí seznam některých šablon Azure pro rychlý Start, které obsahují typ prostředku:

    ![šablony pro rychlý Start pro účet úložiště odkazů na šablonu](./media/view-resources/resource-manager-template-reference-quickstart-templates.png)

Odkaz na šablonu je propojený z každého webu dokumentace ke službě Azure.  Například [Web dokumentace Key Vault](../../key-vault/general/overview.md):

![Odkaz na šablonu Správce prostředků Key Vault](./media/view-resources/resource-manager-template-reference-key-vault.png)

## <a name="use-resource-explorer"></a>Použít Průzkumník prostředků

Průzkumník prostředků je vložen do Azure Portal. Před použitím této metody potřebujete účet úložiště. Pokud ho nemáte, vyberte následující tlačítko, abyste ho vytvořili:

[![Nasazení do Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-storage-account-create%2fazuredeploy.json)

1. Přihlaste se k [portálu Azure Portal](https://portal.azure.com).
1. Do vyhledávacího pole zadejte **Průzkumník prostředků**a potom vyberte **Průzkumník prostředků**.

    ![Snímek obrazovky ukazuje hledání Průzkumník prostředků v Azure Portal.](./media/view-resources/azure-portal-resource-explorer.png)

1. Vlevo rozbalte položku **předplatná**a potom rozbalte své předplatné Azure. Účet úložiště můžete najít buď v části **poskytovatelé** , nebo v **ResourceGroups**.

    ![Azure Portal Průzkumník prostředků](./media/view-resources/azure-portal-resource-explorer-home.png)

    - **Zprostředkovatelé**: rozbalte položku **poskytovatelé**  ->  **Microsoft. Storage**  ->  **storageAccounts**a pak vyberte svůj účet úložiště.
    - **ResourceGroups**: vyberte skupinu prostředků, která obsahuje účet úložiště, vyberte **prostředky**a potom vyberte účet úložiště.

    Napravo se zobrazí konfigurace SKU pro existující účet úložiště podobná této:

    ![SKU účtu úložiště Azure Portal Průzkumník prostředků](./media/view-resources/azure-portal-resource-explorer-sku.png)

## <a name="use-resourcesazurecom"></a>Použití Resources.azure.com

Resources.azure.com je veřejný web, ke kterému může přistupovat kdokoli s předplatným Azure. Je ve verzi Preview.  Místo toho zvažte použití [Průzkumník prostředků](#use-resource-explorer) . Tento nástroj nabízí tyto funkce:

- Seznamte se s rozhraními API pro správu prostředků Azure.
- Získejte dokumentaci k rozhraní API a informace o schématu.
- Volání rozhraní API přímo ve svých vlastních předplatných.

K předvedení toho, jak načíst informace o schématu pomocí tohoto nástroje, potřebujete účet úložiště. Pokud ho nemáte, vyberte následující tlačítko, abyste ho vytvořili:

[![Nasazení do Azure](https://aka.ms/deploytoazurebutton)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3a%2f%2fraw.githubusercontent.com%2fAzure%2fazure-quickstart-templates%2fmaster%2f101-storage-account-create%2fazuredeploy.json)

1. Přejděte na [Resources.Azure.com](https://resources.azure.com/). Může chvíli trvat, než se nástroj oblíbený v levém podokně.
1. Vyberte **odběry**.

    ![mapování rozhraní API resource.azure.com](./media/view-resources/resources-azure-com-api-mapping.png)

    Uzel na levé straně odpovídá volání rozhraní API na pravé straně. Volání rozhraní API můžete provést tak, že vyberete tlačítko **získat** .
1. Vlevo rozbalte položku **předplatná**a potom rozbalte své předplatné Azure. Účet úložiště můžete najít buď v části **poskytovatelé** , nebo v **ResourceGroups**.

    - **Poskytovatelé**: rozbalte položku **poskytovatelé**  ->  **Microsoft. Storage**  ->  **storageAccounts**a potom přejděte k účtu úložiště.
    - **ResourceGroups**: vyberte skupinu prostředků, která obsahuje účet úložiště, a pak vyberte **prostředky**.

    Napravo se zobrazí konfigurace SKU pro existující účet úložiště podobná této:

    ![SKU účtu úložiště Azure Portal Průzkumník prostředků](./media/view-resources/azure-portal-resource-explorer-sku.png)

## <a name="next-steps"></a>Další kroky

V tomto článku jste zjistili, jak najít informace o schématu šablony. Další informace o vytváření šablon Správce prostředků najdete v tématu [pochopení struktury a syntaxe šablon ARM](./template-syntax.md).
