---
title: Zobrazit znalostní bázi Knowledge Store (Preview) pomocí Průzkumník služby Storage
titleSuffix: Azure Cognitive Search
description: Umožňuje zobrazit a analyzovat Azure Kognitivní hledání Knowledge Store pomocí Průzkumník služby Storage Azure Portal. znalostní databáze je aktuálně ve verzi Public Preview.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/30/2019
ms.openlocfilehash: 167316eca1f85530a040d4543f98ae34a9fb93c6
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/08/2020
ms.locfileid: "75754068"
---
# <a name="view-a-knowledge-store-with-storage-explorer"></a>Zobrazení znalostní báze s Průzkumník služby Storage

> [!IMPORTANT] 
> znalostní databáze je aktuálně ve verzi Public Preview. Funkce Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro produkční úlohy. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). [REST API verze 2019-05-06-Preview](search-api-preview.md) poskytuje funkce ve verzi Preview. V současné době je omezená podpora portálu a žádná podpora sady .NET SDK.

V tomto článku se naučíte, jak se připojit k znalostnímu obchodu a prozkoumat ho pomocí Průzkumník služby Storage v Azure Portal.

## <a name="prerequisites"></a>Požadavky

+ Postupujte podle kroků v části [vytvoření znalostní báze v Azure Portal](knowledge-store-create-portal.md) k vytvoření ukázkového úložiště Knowledge Store používaného v tomto návodu.

+ Také budete potřebovat název účtu služby Azure Storage, který jste použili k vytvoření obchodu Knowledge Store, a jeho přístupová klávesa z Azure Portal.

## <a name="view-edit-and-query-a-knowledge-store-in-storage-explorer"></a>Zobrazení, úpravy a dotazování znalostní databáze ve znalostní bázi Průzkumník služby Storage

1. V Azure Portal [otevřete účet úložiště](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Storage%2storageAccounts/) , který jste použili k vytvoření obchodu Knowledge Store.

1. V levém navigačním podokně účtu úložiště klikněte na **Průzkumník služby Storage**.

1. Rozbalením seznamu **tabulky** zobrazíte seznam projekce tabulky Azure, které byly vytvořeny při spuštění průvodce **importem dat** v ukázkových datech pro recenze hotelu.

Vyberte libovolnou tabulku, abyste zobrazili obohacená data, včetně klíčových frází a mínění skóre.

   ![Zobrazit tabulky v Průzkumník služby Storage](media/knowledge-store-view-storage-explorer/storage-explorer-tables.png "Zobrazit tabulky v Průzkumník služby Storage")

Chcete-li změnit datový typ pro libovolnou hodnotu tabulky nebo změnit jednotlivé hodnoty v tabulce, klikněte na tlačítko **Upravit**. Když změníte datový typ pro libovolný sloupec v jednom řádku tabulky, použije se na všechny řádky.

   ![Upravit tabulku v Průzkumník služby Storage](media/knowledge-store-view-storage-explorer/storage-explorer-edit-table.png "Upravit tabulku v Průzkumník služby Storage")

Chcete-li spustit dotazy, klikněte na příkaz **dotaz** na panelu příkazů a zadejte podmínky.  

   ![Tabulka dotazů v Průzkumník služby Storage](media/knowledge-store-view-storage-explorer/storage-explorer-query-table.png "Tabulka dotazů v Průzkumník služby Storage")

## <a name="clean-up"></a>Vyčištění

Pokud pracujete s vlastním předplatným, je vhodné vždy na konci projektu zkontrolovat, jestli budete vytvořené prostředky ještě potřebovat. Prostředky, které necháte běžet, vás můžou stát peníze. Prostředky můžete odstraňovat jednotlivě nebo můžete odstranit skupinu prostředků, abyste odstranili celou sadu prostředků.

Prostředky můžete najít a spravovat na portálu pomocí odkazu **všechny prostředky** nebo **skupiny prostředků** v levém navigačním podokně.

Pokud používáte bezplatnou službu, pamatujte na to, že jste omezeni na tři indexy, indexery a zdroje dat. Jednotlivé položky na portálu můžete odstranit, aby zůstaly pod limitem.

## <a name="next-steps"></a>Další kroky

Připojte si toto úložiště znalostní báze k Power BI pro hlubší analýzu nebo přejděte vpřed s kódem pomocí REST API a post, aby se vytvořilo jiné úložiště znalostí.

> [!div class="nextstepaction"]
> [Připojení pomocí Power BI](knowledge-store-connect-power-bi.md)
> [Vytvoření úložiště znalostí v REST](knowledge-store-create-rest.md)
