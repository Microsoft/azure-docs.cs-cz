---
title: Zobrazení úložiště znalostí (náhledu) pomocí Průzkumníka úložiště
titleSuffix: Azure Cognitive Search
description: Zobrazte a analyzujte úložiště znalostí Azure Cognitive Search pomocí Průzkumníka úložiště na webu Portálu Azure. úložiště znalostí je v současné době ve verzi Public Preview.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 12/30/2019
ms.openlocfilehash: 167316eca1f85530a040d4543f98ae34a9fb93c6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75754068"
---
# <a name="view-a-knowledge-store-with-storage-explorer"></a>Zobrazení úložiště znalostí pomocí Průzkumníka úložišť

> [!IMPORTANT] 
> Úložiště znalostí je v současné době ve verzi Public Preview. Funkce náhledu je k dispozici bez smlouvy o úrovni služeb a nedoporučuje se pro produkční úlohy. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). [Rozhraní REST API verze 2019-05-06-Preview](search-api-preview.md) poskytuje funkce náhledu. V současné době je omezená podpora portálu a žádná podpora sady .NET SDK.

V tomto článku se dozvíte podle příkladů, jak se připojit k úložišti znalostí a prozkoumat ho pomocí Průzkumníka úložiště na webu Azure Portal.

## <a name="prerequisites"></a>Požadavky

+ Postupujte podle kroků v [části Vytvoření úložiště znalostí na webu Azure Portal](knowledge-store-create-portal.md) a vytvořte ukázkové úložiště znalostí použité v tomto návodu.

+ Budete také potřebovat název účtu úložiště Azure, který jste použili k vytvoření úložiště znalostí, spolu s jeho přístupový klíč z portálu Azure.

## <a name="view-edit-and-query-a-knowledge-store-in-storage-explorer"></a>Zobrazení, úpravy a dotazování úložiště znalostí v Průzkumníku úložiště

1. Na webu Azure Portal [otevřete účet úložiště,](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Storage%2storageAccounts/) který jste použili k vytvoření úložiště znalostí.

1. V levém navigačním podokně účtu úložiště klikněte na **Průzkumník a úložiště**.

1. Rozbalte seznam **TABULEK** a zobrazte seznam projekcí tabulek Azure, které byly vytvořeny při spuštění Průvodce **importem dat** v ukázkových datech hodnocení hotelů.

Výběrem libovolné tabulky zobrazíte obohacená data, včetně klíčových frází a skóre mínění.

   ![Zobrazení tabulek v Průzkumníkovi úložiště](media/knowledge-store-view-storage-explorer/storage-explorer-tables.png "Zobrazení tabulek v Průzkumníkovi úložiště")

Pokud chcete změnit datový typ libovolné hodnoty tabulky nebo jednotlivé hodnoty v tabulce, klikněte na **Upravit**. Změníte-li datový typ libovolného sloupce v jednom řádku tabulky, bude použit na všechny řádky.

   ![Upravit tabulku v Průzkumníkovi úložiště](media/knowledge-store-view-storage-explorer/storage-explorer-edit-table.png "Upravit tabulku v Průzkumníkovi úložiště")

Chcete-li spouštět dotazy, klikněte na panelu příkazů na **dotaz** a zadejte své podmínky.  

   ![Tabulka dotazů v Průzkumníkovi úložiště](media/knowledge-store-view-storage-explorer/storage-explorer-query-table.png "Tabulka dotazů v Průzkumníkovi úložiště")

## <a name="clean-up"></a>Vyčištění

Pokud pracujete s vlastním předplatným, je vhodné vždy na konci projektu zkontrolovat, jestli budete vytvořené prostředky ještě potřebovat. Prostředky, které necháte běžet, vás můžou stát peníze. Prostředky můžete odstraňovat jednotlivě nebo můžete odstranit skupinu prostředků, a odstranit tak celou sadu prostředků najednou.

Můžete najít a spravovat prostředky na portálu pomocí odkazu **Všechny prostředky** nebo **skupiny prostředků** v levém navigačním podokně.

Pokud používáte bezplatnou službu, nezapomeňte, že jste omezeni na tři indexy, indexery a zdroje dat. Můžete odstranit jednotlivé položky na portálu, abyste zůstali pod limitem.

## <a name="next-steps"></a>Další kroky

Připojte toto úložiště znalostí k Power BI pro hlubší analýzu nebo se přesuňte vpřed s kódem, pomocí rozhraní REST API a Postman vytvořte jiné úložiště znalostí.

> [!div class="nextstepaction"]
> [Připojení k Power BI](knowledge-store-connect-power-bi.md)
> [Vytvoření úložiště znalostí v REST](knowledge-store-create-rest.md)
