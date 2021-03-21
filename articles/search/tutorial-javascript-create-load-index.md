---
title: 'Kurz JavaScriptu: Přidání vyhledávání do Web Apps'
titleSuffix: Azure Cognitive Search
description: Pomocí sady npm SDK vytvořte index a importujte data CSV do indexu vyhledávání pomocí JavaScriptu @azure/search-documents .
manager: nitinme
author: diberry
ms.author: diberry
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 03/18/2021
ms.custom: devx-track-js
ms.devlang: javascript
ms.openlocfilehash: 0fd28262f4a4b852386fa354037e69c5097109c5
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "104726857"
---
# <a name="2---create-and-load-search-index-with-javascript"></a>2. vytvoření a načtení indexu vyhledávání pomocí JavaScriptu

Pokračujte v sestavování webu s povoleným vyhledáváním:
* Vytvoření prostředku vyhledávání pomocí rozšíření VS Code
* Vytvoření nového indexu a import dat pomocí JavaScriptu pomocí ukázkového skriptu a sady Azure SDK [@azure/search-documents](https://www.npmjs.com/package/@azure/search-documents) .

## <a name="create-an-azure-search-resource"></a>Vytvoření prostředku Azure Search 

Vytvořte nový prostředek vyhledávání s rozšířením [Azure kognitivní hledání](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurecognitivesearch) pro Visual Studio Code.

1. V Visual Studio Code otevřete [řádek aktivity](https://code.visualstudio.com/docs/getstarted/userinterface)a vyberte ikonu Azure. 

1. Na bočním panelu **klikněte pravým tlačítkem na své předplatné Azure** v `Azure: Cognitive Search` oblasti a vyberte **vytvořit novou vyhledávací službu**.

    :::image type="content" source="./media/tutorial-javascript-create-load-index/visual-studio-code-create-search-resource.png" alt-text="Na bočním panelu klikněte pravým tlačítkem myši na předplatné Azure v oblasti * * Azure: Kognitivní hledání * * a vyberte * * vytvořit novou vyhledávací službu * *.":::

1. Podle zobrazených výzev zadejte následující informace:

    |Výzva|Enter|
    |--|--|
    |Zadejte globálně jedinečný název pro nový Search Service.|**Zapamatujte si tento název**. Tento název prostředku se stal součástí vašeho koncového bodu prostředku.|
    |Vyberte skupinu prostředků pro nové prostředky.|Použijte skupinu prostředků, kterou jste vytvořili pro tento kurz.|
    |Vyberte SKLADOVOU položku pro vaši vyhledávací službu.|Pro tento kurz vyberte **zdarma** . Po vytvoření služby nemůžete změnit cenovou úroveň SKU.|
    |Vyberte umístění pro nové prostředky.|Vyberte oblast blízko od vás.|

1. Po dokončení výzev se vytvoří nový prostředek vyhledávání. 

## <a name="get-your-search-resource-admin-key"></a>Získat klíč správce prostředků hledání

Získejte klíč správce prostředků hledání pomocí rozšíření Visual Studio Code. 

1. V Visual Studio Code na bočním panelu klikněte pravým tlačítkem na prostředek hledání a vyberte **Kopírovat klíč správce**.

    :::image type="content" source="./media/tutorial-javascript-create-load-index/visual-studio-code-copy-admin-key.png" alt-text="Na bočním panelu klikněte pravým tlačítkem na prostředek hledání a vyberte * * Kopírovat klíč správce * *.":::

1. Tento klíč správce uložte, budete ho muset použít v [pozdější části](#prepare-the-bulk-import-script-for-search). 

## <a name="prepare-the-bulk-import-script-for-search"></a>Příprava skriptu hromadného importu pro hledání

Tento skript používá sadu Azure SDK pro Kognitivní hledání:

* [balíček npm @azure/search-documents](https://www.npmjs.com/package/@azure/search-documents)
* [Referenční dokumentace](/javascript/api/overview/azure/search-documents-readme)

1. V Visual Studio Code otevřete `bulk_insert_books.js` soubor v podadresáři,  `search-web/bulk-insert` nahraďte následující proměnné vlastními hodnotami pro ověření pomocí sady Azure Search SDK:

    * VAŠE VYHLEDÁVÁNÍ – NÁZEV PROSTŘEDKU
    * VÁŠ KLÍČ PRO HLEDÁNÍ A SPRÁVU

    :::code language="javascript" source="~/azure-search-javascript-samples/search-website/bulk-insert/bulk_insert_books.js" highlight="16,17" :::

1. Otevřete integrovaný terminál v aplikaci Visual Studio pro podadresář adresáře projektu, `search-web/bulk-insert` a spuštěním následujícího příkazu nainstalujte závislosti. 

    ```bash
    npm install 
    ```

## <a name="run-the-bulk-import-script-for-search"></a>Spuštění skriptu hromadného importu pro hledání

1. Pokračujte v používání integrovaného terminálu v aplikaci Visual Studio pro podadresář adresáře projektu, `search-web/bulk-insert` Spusťte následující příkaz bash ke spuštění `bulk_insert_books.js` skriptu:

    ```javascript
    npm start
    ```

1. Při spuštění kódu se v konzole zobrazí průběh. 
1. Po dokončení nahrávání se poslední příkaz vytiskl do konzoly "Hotovo".

## <a name="review-the-new-search-index"></a>Kontrola nového indexu vyhledávání

Až se nahrávání dokončí, index vyhledávání je připravený k použití. Zkontrolujte nový index.

1. V Visual Studio Code otevřete rozšíření Azure Kognitivní hledání a vyberte prostředek hledání.  

    :::image type="content" source="media/tutorial-javascript-create-load-index/visual-studio-code-search-extension-view-resource.png" alt-text="V Visual Studio Code otevřete rozšíření Azure Kognitivní hledání a otevřete prostředek hledání.":::

1. Rozbalte položku indexy a pak dokumenty, potom `good-books` Vyberte dokument, abyste zobrazili všechna data specifická pro dokument.
 
    :::image type="content" source="media/tutorial-javascript-create-load-index/visual-studio-code-search-extension-view-docs.png" lightbox="media/tutorial-javascript-create-load-index/visual-studio-code-search-extension-view-docs.png" alt-text="Rozbalte položku indexy a pak ' dobré knihy ' a pak vyberte dokument.":::

## <a name="copy-your-search-resource-name"></a>Kopírovat název hledaného prostředku

Poznamenejte si **název hledaného prostředku**. Budete ho potřebovat pro připojení aplikace funkce Azure k vašemu prostředku vyhledávání. 

> [!CAUTION]
> I když se můžete rozhodnout, že použijete klíč správce vyhledávání ve funkci Azure, která nenásleduje po principu nejnižší úrovně oprávnění. Funkce Azure Functions použije klíč dotazu k dodržení minimálního oprávnění. 

## <a name="next-steps"></a>Další kroky

[Nasazení statické webové aplikace](tutorial-javascript-deploy-static-web-app.md)