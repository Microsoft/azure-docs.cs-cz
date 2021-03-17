---
title: Postup přidání referenčních datových sad do prostředí – Azure Time Series Insights | Microsoft Docs
description: Tento článek popisuje, jak přidat referenční datovou sadu pro rozšíření dat v prostředí Azure Time Series Insights.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.reviewer: jasonh, kfile
ms.workload: big-data
ms.topic: conceptual
ms.date: 09/30/2020
ms.custom: seodec18
ms.openlocfilehash: d80d97a609aa3a464b9b114439fe7f4058e287c3
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "96001826"
---
# <a name="create-a-reference-data-set-for-your-azure-time-series-insights-gen1-environment-using-the-azure-portal"></a>Vytvořte referenční datovou sadu pro prostředí Azure Time Series Insights Gen1 pomocí Azure Portal

> [!CAUTION]
> Toto je Gen1 článek.

Tento článek popisuje, jak přidat referenční datovou sadu do prostředí Azure Time Series Insights. Referenční data jsou užitečná pro připojení ke svým zdrojovým datům za účelem rozšíření těchto hodnot.

Referenční sada dat je kolekce položek, které rozšiřují události ze zdroje událostí. Azure Time Series Insights vstupním modulem spojí každou událost ze zdroje událostí s odpovídajícím řádkem dat ve vaší referenční sadě dat. Tato rozšířená událost je pak k dispozici pro dotaz. Toto spojení je založené na sloupcích primárního klíče definované ve vaší referenční sadě dat.

Referenční data se nepřipojí zpětně. Proto budou odpovídat jenom aktuálnímu a budoucímu příchozímu přenosu dat a připojí se k sadě referenčního data, jakmile se nakonfigurují a nahrají.

## <a name="video"></a>Video

### <a name="learn-about-time-series-insights-reference-data-modelbr"></a>Přečtěte si referenční datový model pro přehledy časových řad.</br>

> [!VIDEO <https://www.youtube.com/embed/Z0NuWQUMv1o>]

## <a name="add-a-reference-data-set"></a>Přidání referenční sady dat

1. Přihlaste se na [Azure Portal](https://portal.azure.com).

1. Vyhledejte existující Azure Time Series Insights prostředí. Vyberte **všechny prostředky** v nabídce na levé straně Azure Portal. Vyberte prostředí Azure Time Series Insights.

1. Vyberte stránku **Overview**. Rozbalením části **základy** v horní části stránky vyhledejte **adresu URL aplikace Time Series Insights Explorer** a otevřete odkaz.  

   [![Rozbalit oddíl Essentials](media/add-reference-data-set/essentials.png)](media/add-reference-data-set/essentials.png#lightbox)

   Zobrazení Průzkumníka Azure Time Series Insightsho prostředí.

1. Rozbalte selektor prostředí v Průzkumníkovi Azure Time Series Insights. Vyberte aktivní prostředí. V pravém horním rohu stránky v Průzkumníkovi vyberte ikonu referenční data.

   [![Přidat referenční data](media/add-reference-data-set/tsi-select-environment-and-data-icons.png)](media/add-reference-data-set/tsi-select-environment-and-data-icons.png#lightbox)

1. Vyberte tlačítko **+ Přidat datovou sadu** a začněte přidávat novou datovou sadu.

   [![Přidat datovou sadu](media/add-reference-data-set/tsi-add-a-reference-data-set.png)](media/add-reference-data-set/tsi-add-a-reference-data-set.png#lightbox)

1. Na stránce **Nová datová sada referenčních dat** vyberte formát dat:

   - Pro data oddělená čárkami vyberte **CSV** . První řádek se považuje za řádek záhlaví.
   - Pro formátovaná data ve formátu JavaScript Object Notation (JSON) vyberte **pole JSON** .

   [![Vyberte formát dat.](media/add-reference-data-set/tsi-select-data-upload-option.png)](media/add-reference-data-set/tsi-select-data-upload-option.png#lightbox)

1. Data zadejte pomocí jedné ze dvou metod:

   - Vložte data do textového editoru. Pak vyberte tlačítko **analyzovat referenční data** .
   - Kliknutím na tlačítko **zvolit soubor** přidejte data z místního textového souboru.

   Například vložte data sdíleného svazku clusteru: [ ![ vložená data CSV](media/add-reference-data-set/select-csv-and-enter-data.png)](media/add-reference-data-set/select-csv-and-enter-data.png#lightbox)

   Například vložte data pole JSON: [ ![ Vložit data JSON](media/add-reference-data-set/select-json-option-and-enter-data.png)](media/add-reference-data-set/select-json-option-and-enter-data.png#lightbox) .

   Pokud při analýze hodnot dat dojde k chybě, zobrazí se chyba červeně v dolní části stránky, například `CSV parsing error, no rows extracted` .

1. Po úspěšném analyzování dat se zobrazí datová mřížka zobrazující sloupce a řádky reprezentující data. Zkontrolujte datovou mřížku, abyste zajistili správnost.

   [![Kontrola referenčních dat](media/add-reference-data-set/review-displayed-data-grid.png)](media/add-reference-data-set/review-displayed-data-grid.png#lightbox)

1. Projděte si jednotlivé sloupce, abyste porozuměli předpokládanému typu dat a v případě potřeby změnili datový typ.  Vyberte symbol datového typu v záhlaví sloupce: **#** pro dvojitá (číselná data), **T | F** pro logickou hodnotu nebo **ABC** pro řetězec.

   [![Vyberte datové typy pro záhlaví sloupců.](media/add-reference-data-set/select-column-types.png)](media/add-reference-data-set/select-column-types.png#lightbox)

1. V případě potřeby přejmenujte záhlaví sloupců. Název klíčového sloupce je potřebný pro připojení k odpovídající vlastnosti ve zdroji události.

   > [!IMPORTANT]
   > Zajistěte, aby se názvy sloupců klíčů referenčních dat přesně shodovaly s názvem události pro příchozí data, včetně rozlišování velkých a malých písmen. Názvy sloupců, které nejsou klíčovým sloupcem, se používají k rozšíření příchozích dat s odpovídajícími hodnotami referenčních dat.

1. Zadejte hodnotu do pole **Filtrovat řádky...** a podle potřeby zkontrolujte konkrétní řádky. Filtr je vhodný pro kontrolu dat, ale při nahrávání dat se nepoužívá.

1. Pojmenujte sadu dat tak, že vyplníte pole **název sady dat** nad datovou mřížkou.

    [![Pojmenujte datovou sadu.](media/add-reference-data-set/enter-reference-data-set-name.png)](media/add-reference-data-set/enter-reference-data-set-name.png#lightbox)

1. Zadejte sloupec **primárního klíče** v sadě dat tak, že vyberete rozevírací seznam nad datovou mřížkou.

    [![Vyberte sloupce klíče.](media/add-reference-data-set/select-primary-key-column.png)](media/add-reference-data-set/select-primary-key-column.png#lightbox)

    **(Volitelné)** Vyberte **+** tlačítko a přidejte sloupec sekundárního klíče jako složený primární klíč. Pokud potřebujete zrušit výběr, vyberte prázdnou hodnotu z rozevíracího seznamu a odeberte tak sekundární klíč.

1. Pokud chcete data nahrát, vyberte tlačítko **Odeslat řádky** .

    [![Nahrajte řádky a potvrďte data.](media/add-reference-data-set/confirm-upload-reference-data.png)](media/add-reference-data-set/confirm-upload-reference-data.png#lightbox)

    Na stránce se potvrdí dokončení nahrávání a zobrazí se zpráva, že **datová sada se úspěšně nahrála**.

    > [!WARNING]
    > Sloupce nebo vlastnosti sdílené mezi referenčními datovými sadami zobrazí chybu při nahrávání **duplicitního názvu vlastnosti** . Chyba nezabrání úspěšnému nahrání referenčních datových sad. Dá se odebrat kombinováním řádků, které sdílí název duplicitní vlastnosti.

1. Vyberte **Přidat řádek**, **hromadné importy řádků** nebo **přidejte sloupec** pro přidání dalších hodnot referenčních dat podle potřeby.

    [![Přidejte řádek, hromadné importy řádků nebo přidejte sloupec.](media/add-reference-data-set/add-row-or-bulk-upload.png)](media/add-reference-data-set/add-row-or-bulk-upload.png#lightbox)

   > [!IMPORTANT]
   > Všechny řádky, které sdílí jedinečný klíč s jiným řádkem, budou mít své sloupce přepsané posledním přidaným řádkem, který tento jedinečný klíč sdílí.

   > [!NOTE]
   > Přidané řádky **nemusejí být** *pravoúhlé* , mohou mít méně, více nebo různé sloupce z dalších položek v referenční sadě dat.

## <a name="next-steps"></a>Další kroky

- [Spravujte referenční data](time-series-insights-manage-reference-data-csharp.md) prostřednictvím kódu programu.

- Úplný odkaz na rozhraní API najdete v dokumentu [rozhraní API pro referenční data](/rest/api/time-series-insights/gen1-reference-data-api) .