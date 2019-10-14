---
title: Jak přidat referenční datovou sadu do prostředí Azure Time Series Insights | Microsoft Docs
description: Tento článek popisuje, jak přidat referenční datovou sadu pro rozšíření dat v prostředí Azure Time Series Insights.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: dpalled
manager: cshankar
ms.reviewer: jasonh, kfile
ms.workload: big-data
ms.topic: conceptual
ms.date: 10/10/2019
ms.custom: seodec18
ms.openlocfilehash: c33619ba52246128526bb132dfe94416c7c4de8c
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/13/2019
ms.locfileid: "72299595"
---
# <a name="create-a-reference-data-set-for-your-time-series-insights-environment-using-the-azure-portal"></a>Vytvoření referenční sady dat pro prostředí Time Series Insights pomocí Azure Portal

Tento článek popisuje, jak přidat referenční datovou sadu do prostředí Azure Time Series Insights. Referenční data jsou užitečná pro připojení ke svým zdrojovým datům za účelem rozšíření těchto hodnot.

Referenční sada dat je kolekce položek, které rozšiřují události ze zdroje událostí. Time Series Insights vstupním modulem spojí každou událost ze zdroje událostí s odpovídajícím řádkem dat ve vaší referenční sadě dat. Tato rozšířená událost je pak k dispozici pro dotaz. Toto spojení je založené na sloupcích primárního klíče definované ve vaší referenční sadě dat.

Referenční data se nepřipojí zpětně. Proto budou odpovídat jenom aktuálnímu a budoucímu příchozímu přenosu dat a připojí se k sadě referenčního data, jakmile se nakonfigurují a nahrají.

## <a name="video"></a>Obrazový

### <a name="learn-about-time-series-insights-reference-data-modelbr"></a>Přečtěte si referenční datový model pro přehledy časových řad.</br>

> [!VIDEO https://www.youtube.com/embed/Z0NuWQUMv1o]

## <a name="add-a-reference-data-set"></a>Přidání referenční sady dat

1. Přihlaste se k [Azure Portal](https://portal.azure.com).

1. Vyhledejte existující Azure Time Series Insights prostředí. Vyberte **všechny prostředky** v nabídce na levé straně Azure Portal. Vyberte prostředí Time Series Insights.

1. Vyberte stránku **Přehled** . Vyhledejte **adresu URL aplikace Time Series Insights Explorer** a otevřete odkaz.  

   Zobrazení Průzkumníka Time Series Insightsho prostředí.

1. Rozbalte selektor prostředí v Průzkumníkovi Time Series Insights. Vyberte aktivní prostředí. V pravém horním rohu stránky v Průzkumníkovi vyberte ikonu referenční data.

   [referenční data @no__t – 1Add](media/add-reference-data-set/add-reference-data.png)](media/add-reference-data-set/add-reference-data.png#lightbox)

1. Vyberte tlačítko **+ Přidat datovou sadu** a začněte přidávat novou datovou sadu.

   [sada dat ![Add](media/add-reference-data-set/add-data-set.png)](media/add-reference-data-set/add-data-set.png#lightbox)

1. Na stránce **Nová datová sada referenčních dat** vyberte formát dat:

   - Pro data oddělená čárkami vyberte **CSV** . První řádek se považuje za řádek záhlaví.
   - Pro formátovaná data ve formátu JavaScript Object Notation (JSON) vyberte **pole JSON** .

   [Formát dat ![Choose.](media/add-reference-data-set/add-data.png)](media/add-reference-data-set/add-data.png#lightbox)

1. Data zadejte pomocí jedné ze dvou metod:

   - Vložte data do textového editoru. Pak vyberte tlačítko **analyzovat referenční data** .
   - Kliknutím na tlačítko **zvolit soubor** přidejte data z místního textového souboru.

   Například vložte data CSV: [![Pasted data CSV](media/add-reference-data-set/csv-data-pasted.png)](media/add-reference-data-set/csv-data-pasted.png#lightbox)

   Například vložte data pole JSON: [![Paste data JSON](media/add-reference-data-set/json-data-pasted.png)](media/add-reference-data-set/json-data-pasted.png#lightbox)

   Pokud při analýze hodnot dat dojde k chybě, zobrazí se chyba červeně v dolní části stránky, například `CSV parsing error, no rows extracted`.

1. Po úspěšném analyzování dat se zobrazí datová mřížka zobrazující sloupce a řádky reprezentující data.  Zkontrolujte datovou mřížku, abyste zajistili správnost.

   [referenční data @no__t – 1Add](media/add-reference-data-set/parse-data.png)](media/add-reference-data-set/parse-data.png#lightbox)

1. Projděte si jednotlivé sloupce, abyste viděli typ dat, který se předpokládá, a v případě potřeby změňte datový typ.  Vyberte symbol datového typu v záhlaví sloupce: **#** pro dvojitá (číselná data), **t | F** pro logickou hodnotu nebo **ABC** pro řetězec.

   [@no__t 1Choose datové typy pro záhlaví sloupců.](media/add-reference-data-set/choose-datatypes.png)](media/add-reference-data-set/choose-datatypes.png#lightbox)

1. V případě potřeby přejmenujte záhlaví sloupců. Název klíčového sloupce je potřebný pro připojení k odpovídající vlastnosti ve zdroji události. Zajistěte, aby se názvy sloupců klíčů referenčních dat přesně shodovaly s názvem události pro příchozí data, včetně rozlišování velkých a malých písmen. Názvy sloupců, které nejsou klíčovým sloupcem, se používají k rozšíření příchozích dat s odpovídajícími hodnotami referenčních dat.

1. Vyberte **Přidat řádek** nebo **přidejte sloupec** a přidejte do něj další hodnoty referenčních dat podle potřeby.

1. Zadejte hodnotu do pole **Filtrovat řádky...** a podle potřeby zkontrolujte konkrétní řádky. Filtr je vhodný pro kontrolu dat, ale při nahrávání dat se nepoužívá.

1. Pojmenujte sadu dat tak, že vyplníte pole **název sady dat** nad datovou mřížkou.

    [@no__t 1Name datovou sadu.](media/add-reference-data-set/name-reference-dataset.png)](media/add-reference-data-set/name-reference-dataset.png#lightbox)

1. Zadejte sloupec **primárního klíče** v sadě dat tak, že vyberete rozevírací seznam nad datovou mřížkou.

    [@no__t – 1Select sloupce klíčů.](media/add-reference-data-set/set-primary-key.png)](media/add-reference-data-set/set-primary-key.png#lightbox)

    Případně můžete vybrat tlačítko **+** a přidat sloupec sekundárního klíče jako složený primární klíč. Pokud potřebujete zrušit výběr, vyberte prázdnou hodnotu z rozevíracího seznamu a odeberte tak sekundární klíč.

1. Pokud chcete data nahrát, vyberte tlačítko **Odeslat řádky** .

    [@no__t – 1Upload](media/add-reference-data-set/upload-rows.png)](media/add-reference-data-set/upload-rows.png#lightbox)

    Na stránce se potvrdí dokončení nahrávání a zobrazí se zpráva, že **datová sada se úspěšně nahrála**.

## <a name="next-steps"></a>Další kroky

* [Spravujte referenční data](time-series-insights-manage-reference-data-csharp.md) programově.

* Úplný referenční informace k rozhraní API najdete v dokumentu [rozhraní API referenčních dat](https://docs.microsoft.com/rest/api/time-series-insights/ga-reference-data-api) .
