---
title: Jak přidat referenční sady dat do vašeho prostředí - Azure Time Series Insights | Dokumenty společnosti Microsoft
description: Tento článek popisuje, jak přidat referenční sadu dat pro rozšíření dat v prostředí Azure Time Series Insights.
ms.service: time-series-insights
services: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.reviewer: jasonh, kfile
ms.workload: big-data
ms.topic: conceptual
ms.date: 02/07/2020
ms.custom: seodec18~~~~
ms.openlocfilehash: 79628ed44753577023464ef6208027e1b7996d30
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77087244"
---
# <a name="create-a-reference-data-set-for-your-time-series-insights-environment-using-the-azure-portal"></a>Vytvoření referenční sady dat pro prostředí Time Series Insights pomocí portálu Azure

Tento článek popisuje, jak přidat referenční sadu dat do prostředí Azure Time Series Insights. Referenční data jsou užitečná pro připojení ke zdrojovým datům za účelem zvýšení hodnot.

Referenční sada dat je kolekce položek, které rozšiřují události ze zdroje událostí. Modul příchozího přenosu dat Time Series Insights připojí každou událost ze zdroje událostí s odpovídajícím datovým řádkem v sadě referenčních dat. Tato rozšířená událost je pak k dispozici pro dotaz. Toto spojení je založeno na sloupech primárního klíče definovaných v sadě referenčních dat.

Referenční data nejsou spojena se zpětnou platností. Proto pouze aktuální a budoucí příchozí přenos dat je spárována a připojena k referenční sadu dat, jakmile byla nakonfigurována a odeslána.

## <a name="video"></a>Video

### <a name="learn-about-time-series-insights-reference-data-modelbr"></a>Přečtěte si o referenčním datovém modelu Time Series Insight.</br>

> [!VIDEO https://www.youtube.com/embed/Z0NuWQUMv1o]

## <a name="add-a-reference-data-set"></a>Přidání sady referenčních dat

1. Přihlaste se k [portálu Azure](https://portal.azure.com).

1. Vyhledejte stávající prostředí Azure Time Series Insights. Vyberte **všechny prostředky** v nabídce na levé straně portálu Azure. Vyberte vaše prostředí Time Series Insights.

1. Vyberte stránku **Přehled.** Rozbalte oddíl **Essentials** v horní části stránky, vyhledejte **adresu URL průzkumníka Time Series Insights** a otevřete odkaz.  

   [![Sekce Rozbalit základní věci](media/add-reference-data-set/essentials.png)](media/add-reference-data-set/essentials.png#lightbox)

   Prohlédněte si průzkumníka pro prostředí Time Series Insights.

1. Rozbalte volič prostředí v průzkumníku Time Series Insights. Zvolte aktivní prostředí. Vyberte ikonu referenčních dat v pravém horním horním textu na stránce průzkumníka.

   [![Přidání referenčních dat](media/add-reference-data-set/tsi-select-environment-and-data-icons.png)](media/add-reference-data-set/tsi-select-environment-and-data-icons.png#lightbox)

1. Chcete-li začít přidávat novou sadu dat, vyberte tlačítko **+ Přidat sadu dat.**

   [![Přidání sady dat](media/add-reference-data-set/tsi-add-a-reference-data-set.png)](media/add-reference-data-set/tsi-add-a-reference-data-set.png#lightbox)

1. Na stránce **Nová referenční sada dat** zvolte formát dat:

   - Zvolte **CSV** pro data oddělená čárkami. První řádek je považován za řádek záhlaví.
   - Zvolte **JSON Array** pro data ve formátu JavaScriptu (JSON).

   [![Zvolte formát dat.](media/add-reference-data-set/tsi-select-data-upload-option.png)](media/add-reference-data-set/tsi-select-data-upload-option.png#lightbox)

1. Zadejte data pomocí jedné ze dvou metod:

   - Vložte data do textového editoru. Potom vyberte **analyzovat referenční data** tlačítko.
   - Chcete-li přidat data z místního textového souboru, vyberte **tlačítko Vybrat soubor.**

   Například vložit data CSV: [ ![Vložená data CSV](media/add-reference-data-set/select-csv-and-enter-data.png)](media/add-reference-data-set/select-csv-and-enter-data.png#lightbox)

   Například vložte data pole JSON: [ ![Vložte data JSON](media/add-reference-data-set/select-json-option-and-enter-data.png)](media/add-reference-data-set/select-json-option-and-enter-data.png#lightbox)

   Pokud dojde k chybě při analýzě datových hodnot, zobrazí se v dolní `CSV parsing error, no rows extracted`části stránky červeně, například .

1. Po úspěšném proanalýzu dat se zobrazí datová mřížka zobrazující sloupce a řádky představující data. Zkontrolujte datovou mřížku, abyste zajistili správnost.

   [![Kontrola referenčních dat](media/add-reference-data-set/review-displayed-data-grid.png)](media/add-reference-data-set/review-displayed-data-grid.png#lightbox)

1. Zkontrolujte každý sloupec, abyste pochopili předpokládaný datový typ, a v případě potřeby změňte datový typ.  Vyberte symbol datového typu **#** v záhlaví sloupce: pro dvojité (číselné údaje), **T| F** pro logickou hodnotu nebo **Abc** pro řetězec.

   [![V záhlaví sloupců zvolte datové typy.](media/add-reference-data-set/select-column-types.png)](media/add-reference-data-set/select-column-types.png#lightbox)

1. V případě potřeby přejmenujte záhlaví sloupců. Název sloupce klíče je nutné připojit k odpovídající vlastnost ve zdroji událostí. 

   > [!IMPORTANT]
   > Ujistěte se, že názvy sloupců klíče referenčních dat přesně odpovídají názvu události příchozím datům, včetně rozlišování velkých a malých písmen. Názvy sloupců, které nejsou klíčové, se používají k rozšíření příchozích dat o odpovídající hodnoty referenčních dat.

1. Zadejte hodnotu do pole **Filtrovat řádky... a** podle potřeby zkontrolujte určité řádky. Filtr je užitečný pro kontrolu dat, ale není použit při nahrávání dat.

1. Pojmenujte sadu dat vyplněním pole **Název datové sady** nad mřížkou dat.

    [![Pojmenujte sadu dat.](media/add-reference-data-set/enter-reference-data-set-name.png)](media/add-reference-data-set/enter-reference-data-set-name.png#lightbox)

1. Zadejte sloupec **Primární klíč** v datové sadě výběrem rozevíracího políčka nad datovou mřížkou.

    [![Vyberte klíčové sloupce.](media/add-reference-data-set/select-primary-key-column.png)](media/add-reference-data-set/select-primary-key-column.png#lightbox)

    **(Nepovinné)** Vyberte **+** tlačítko pro přidání sloupce sekundárního klíče jako složeného primárního klíče. Pokud potřebujete výběr vrátit se změnami, zvolte prázdnou hodnotu z rozevíracího pole a odeberte sekundární klíč.

1. Chcete-li data nahrát, vyberte tlačítko **Odeslat řádky.**

    [![Nahrajte řádky a potvrďte data.](media/add-reference-data-set/confirm-upload-reference-data.png)](media/add-reference-data-set/confirm-upload-reference-data.png#lightbox)

    Stránka potvrdí dokončené nahrání a zobrazí zprávu **Úspěšně nahraná datová sada**.

    > [!WARNING]
    > Sloupce nebo vlastnosti sdílené mezi sadami referenčních dat zobrazí chybu odeslání **duplicitního názvu vlastnosti.** Chyba nezabrání úspěšnému nahrání referenčních datových sad. Lze jej odstranit kombinací řádků sdílejících duplicitní název vlastnosti.

1. Vyberte **Přidat řádek**, **Hromadně importovat řádky**nebo Podle potřeby přidat **sloupec,** chcete-li přidat další hodnoty referenčních dat.

    [![Přidejte řádek, řádky hromadného importu nebo přidání sloupce.](media/add-reference-data-set/add-row-or-bulk-upload.png)](media/add-reference-data-set/add-row-or-bulk-upload.png#lightbox)

   > [!IMPORTANT]
   > Každý řádek, který sdílí jedinečný klíč s jiným řádkem, bude mít své sloupce přepsány posledním dalším dalším kliknutím, který tento jedinečný klíč sdílí.

   > [!NOTE]
   > Přidané řádky **nemusí** být *obdélníkové* – mohou mít méně, větší nebo různé sloupce z ostatních položek v sadě referenčních dat.

## <a name="next-steps"></a>Další kroky

* [Spravujte referenční data](time-series-insights-manage-reference-data-csharp.md) prostřednictvím kódu programu.

* Úplný odkaz na rozhraní API najdete v dokumentu [reference data API.](https://docs.microsoft.com/rest/api/time-series-insights/ga-reference-data-api)
