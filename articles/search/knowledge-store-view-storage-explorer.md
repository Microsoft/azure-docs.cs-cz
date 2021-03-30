---
title: Zobrazení znalostní báze s Průzkumník služby Storage
titleSuffix: Azure Cognitive Search
description: Umožňuje zobrazit a analyzovat Azure Kognitivní hledání Knowledge Store pomocí Průzkumník služby Storage Azure Portal.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/30/2020
ms.openlocfilehash: ba0b02067c032f9038051c169866588ded44af73
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "85566025"
---
# <a name="view-a-knowledge-store-with-storage-explorer"></a>Zobrazení znalostní báze s Průzkumník služby Storage

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

Pokud pracujete s vlastním předplatným, je vhodné vždy na konci projektu zkontrolovat, jestli budete vytvořené prostředky ještě potřebovat. Prostředky, které necháte běžet, vás stojí peníze. Můžete odstraňovat prostředky jednotlivě nebo odstraněním skupiny prostředků odstranit celou sadu prostředků najednou.

Prostředky můžete najít a spravovat na portálu pomocí odkazu **všechny prostředky** nebo **skupiny prostředků** v levém navigačním podokně.

Pokud používáte bezplatnou službu, pamatujte na to, že jste omezeni na tři indexy, indexery a zdroje dat. Jednotlivé položky na portálu můžete odstranit, aby zůstaly pod limitem.

## <a name="next-steps"></a>Další kroky

Připojte si toto úložiště znalostní báze k Power BI pro hlubší analýzu nebo přejděte vpřed s kódem pomocí REST API a post, aby se vytvořilo jiné úložiště znalostí.

> [!div class="nextstepaction"]
> [Připojení pomocí Power BI](knowledge-store-connect-power-bi.md) 
>  [Vytvoření úložiště znalostí v REST](knowledge-store-create-rest.md)
