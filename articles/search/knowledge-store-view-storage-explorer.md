---
title: Zobrazení znalostní báze s Průzkumník služby Storage
titleSuffix: Azure Cognitive Search
description: Umožňuje zobrazit a analyzovat Azure Kognitivní hledání Knowledge Store pomocí Průzkumník služby Storage Azure Portal.
manager: nitinme
author: lisaleib
ms.author: v-lilei
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 9ba11b69a51a5bc563764a7e75189bed67cb2ac6
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73485003"
---
# <a name="view-a-knowledge-store-with-storage-explorer"></a>Zobrazení znalostní báze s Průzkumník služby Storage

> [!Note]
> Znalostní báze je ve verzi Preview a neměl by se používat v produkčním prostředí. Tuto funkci poskytuje portál a [REST API pro hledání verze 2019-05-06-Preview](search-api-preview.md) . V tuto chvíli není dostupná žádná podpora sady .NET SDK.
>
V tomto článku se naučíte, jak se připojit k znalostnímu obchodu a prozkoumat ho pomocí Průzkumník služby Storage v Azure Portal.

## <a name="prerequisites"></a>Předpoklady

+ Postupujte podle kroků v části [vytvoření znalostní báze v Azure Portal](knowledge-store-create-portal.md) k vytvoření ukázkového úložiště Knowledge Store používaného v tomto návodu.

+ Také budete potřebovat název účtu služby Azure Storage, který jste použili k vytvoření obchodu Knowledge Store, a jeho přístupová klávesa z Azure Portal.

## <a name="view-edit-and-query-a-knowledge-store-in-storage-explorer"></a>Zobrazení, úprava a dotazování znalostní databáze v Průzkumník služby Storage

1. V Azure Portal [otevřete účet úložiště](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Storage%2storageAccounts/) , který jste použili k vytvoření obchodu Knowledge Store.

1. V levém navigačním podokně účtu úložiště klikněte na **Průzkumník služby Storage**.

1. Rozbalením seznamu **tabulky** zobrazíte seznam projekce tabulky Azure, které byly vytvořeny při spuštění průvodce **importem dat** v ukázkových datech pro recenze hotelu.

Vyberte libovolnou tabulku, abyste zobrazili obohacená data, včetně klíčových frází mínění skóre, zeměpisná šířka a zeměpisná délka dat a další.

   ![Zobrazit tabulky v Průzkumník služby Storage](media/knowledge-store-view-storage-explorer/storage-explorer-tables.png "Zobrazit tabulky v Průzkumník služby Storage")

Chcete-li změnit datový typ pro libovolnou hodnotu tabulky nebo změnit jednotlivé hodnoty v tabulce, klikněte na tlačítko **Upravit**. Když změníte datový typ pro libovolný sloupec v jednom řádku tabulky, použije se na všechny řádky.

   ![Upravit tabulku v Průzkumník služby Storage](media/knowledge-store-view-storage-explorer/storage-explorer-edit-table.png "Upravit tabulku v Průzkumník služby Storage")

Chcete-li spustit dotazy, klikněte na příkaz **dotaz** na panelu příkazů a zadejte podmínky.  

   ![Tabulka dotazů v Průzkumník služby Storage](media/knowledge-store-view-storage-explorer/storage-explorer-query-table.png "Tabulka dotazů v Průzkumník služby Storage")

## <a name="clean-up"></a>Vyčištění

Pokud pracujete ve vlastním předplatném, je vhodné na konci projektu zjistit, zda stále potřebujete prostředky, které jste vytvořili. Prostředky, které se na něm zbývá, můžou mít náklady na peníze. Prostředky můžete odstranit jednotlivě nebo odstranit skupinu prostředků, abyste odstranili celou sadu prostředků.

Prostředky můžete najít a spravovat na portálu pomocí odkazu **všechny prostředky** nebo **skupiny prostředků** v levém navigačním podokně.

Pokud používáte bezplatnou službu, pamatujte na to, že jste omezeni na tři indexy, indexery a zdroje dat. Jednotlivé položky na portálu můžete odstranit, aby zůstaly pod limitem.

## <a name="next-steps"></a>Další kroky

Připojte si toto úložiště znalostní báze k Power BI pro hlubší analýzu nebo přejděte vpřed s kódem pomocí REST API a post, aby se vytvořilo jiné úložiště znalostí.

> [!div class="nextstepaction"]
> [Připojení pomocí Power BI](knowledge-store-connect-power-bi.md)
> [Vytvoření úložiště znalostí v REST](knowledge-store-howto.md)
