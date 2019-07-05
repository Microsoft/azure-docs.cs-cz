---
title: Postup přidání referenční sady dat do prostředí Azure Time Series Insights | Dokumentace Microsoftu
description: Tento článek popisuje postup přidání referenční sady dat pro rozšíření dat ve vašem prostředí Azure Time Series Insights.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: dpalled
manager: cshankar
ms.reviewer: jasonh, kfile
ms.workload: big-data
ms.topic: conceptual
ms.date: 06/26/2019
ms.custom: seodec18
ms.openlocfilehash: 99933fa36cc822598ec9c173a470f90264d06d54
ms.sourcegitcommit: aa66898338a8f8c2eb7c952a8629e6d5c99d1468
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/28/2019
ms.locfileid: "67461282"
---
# <a name="create-a-reference-data-set-for-your-time-series-insights-environment-using-the-azure-portal"></a>Vytvoření referenční sady dat pro prostředí Time Series Insights pomocí webu Azure portal

Tento článek popisuje postup přidání referenční sady dat do prostředí Azure Time Series Insights. Referenční data jsou užitečné pro připojení k vašim datům zdroje k posílení hodnoty.

Referenční sady dat je kolekce položek, které rozšiřují události ze zdroje událostí. Modul příchozího přenosu dat čas Series Insights spojí každé události ze zdroje událostí s odpovídající řádek dat v referenční sadě dat. Tato rozšířená událost je pak k dispozici pro dotaz. Toto připojení je založené na sloupců primárního klíče, které jsou definované v referenční sadě dat.

Referenční data není připojený zpětně. Proto pouze aktuální a budoucí příchozího přenosu dat je odpovídající a připojené k sady referenční data, jakmile byl nakonfigurován a nahráli.

## <a name="video"></a>Video

### <a name="learn-about-time-series-insights-reference-data-modelbr"></a>Další informace o Time Series Insights odkaz na datový model.</br>

> [!VIDEO https://www.youtube.com/embed/Z0NuWQUMv1o]

## <a name="add-a-reference-data-set"></a>Přidání referenční sady dat

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).

1. Vyhledejte existující prostředí Time Series Insights. Vyberte **všechny prostředky** v nabídce na levé straně webu Azure portal. Vyberte vaše prostředí Time Series Insights.

1. Vyberte **přehled** stránky. Vyhledejte **URL Průzkumníka Time Series Insights** a otevřete odkaz.  

   Zobrazte v Průzkumníku TSI prostředí.

1. Rozšiřte výběr prostředí v Průzkumníku TSI. Zvolte aktivní prostředí. Vyberte ikonu referenční data v pravém horním rohu na stránce explorer.

   [![Přidejte referenční data](media/add-reference-data-set/add-reference-data.png)](media/add-reference-data-set/add-reference-data.png#lightbox)

1. Vyberte **+ přidat datovou sadu** tlačítko začnete přidávat nové datové sady.

   [![Přidat sadu dat](media/add-reference-data-set/add-data-set.png)](media/add-reference-data-set/add-data-set.png#lightbox)

1. Na **nový odkaz datové sady** stránky, vyberte formát data:
   - Zvolte **sdíleného svazku clusteru** pro data oddělených čárkami. První řádek je považován za řádek záhlaví.
   - Zvolte **pole JSON** pro javascript object notation (JSON) ve formátu data.

   [![Zvolte formát data.](media/add-reference-data-set/add-data.png)](media/add-reference-data-set/add-data.png#lightbox)

1. Zadejte data, pomocí jedné ze dvou způsobů:
   - Vložte data do textového editoru. Vyberte **Parsovat referenční data** tlačítko.
   - Vyberte **zvolit soubor** tlačítko pro přidání dat z místního textového souboru.

   Například vložte data ve formátu CSV: [![Vložená data ve formátu CSV](media/add-reference-data-set/csv-data-pasted.png)](media/add-reference-data-set/csv-data-pasted.png#lightbox)

   Například vložte data JSON pole: [![Vložit JSON data](media/add-reference-data-set/json-data-pasted.png)](media/add-reference-data-set/json-data-pasted.png#lightbox)

   Pokud dojde k chybě při analýze hodnoty dat, chyba se zobrazí červeně v dolní části stránky, jako například `CSV parsing error, no rows extracted`.

1. Jakmile se úspěšně analyzovat data, datové mřížce se zobrazuje zobrazení sloupce a řádky, které představují data.  Zobrazit datovou mřížku zajistit správnost.

   [![Přidejte referenční data](media/add-reference-data-set/parse-data.png)](media/add-reference-data-set/parse-data.png#lightbox)

1. Projděte si jednotlivé sloupce do viz předpokládá, že datový typ a změnit datový typ v případě potřeby.  Vyberte datový typ symbol v záhlaví sloupce: **#** pro double (číselná data), **T | F** pro logickou hodnotu, nebo **Abc** řetězce.

   [![Vyberte datové typy na záhlaví sloupce.](media/add-reference-data-set/choose-datatypes.png)](media/add-reference-data-set/choose-datatypes.png#lightbox)

1. Přejmenujte záhlaví sloupců v případě potřeby. Název klíčového sloupce je potřebné pro připojení k příslušné vlastnosti ve zdroji událostí. Ujistěte se, že referenční názvy klíčových sloupců dat se přesně shodovat název události k příchozí data, včetně rozlišování. Názvy neklíčových sloupců se používají k posílení příchozích dat s odpovídajícími hodnotami referenční data.

1. Vyberte **přidejte řádek** nebo **přidat sloupec** přidat více hodnot dat odkaz, podle potřeby.

1. Zadejte hodnotu do **filtrovat řádky...**  pole, které chcete zkontrolovat konkrétní řádky podle potřeby. Filtr je užitečné při data, ale není použita, když data nahrávají.

1. Název datové sady, vyplněním **název datové sady** pole nad datové mřížce.

    [![Název datové sady.](media/add-reference-data-set/name-reference-dataset.png)](media/add-reference-data-set/name-reference-dataset.png#lightbox)

1. Zadejte **primární klíč** sloupců v datové sadě, tak, že vyberete rozevírací nabídky výše datové mřížce.

    [![Vyberte sloupce klíče.](media/add-reference-data-set/set-primary-key.png)](media/add-reference-data-set/set-primary-key.png#lightbox)

    Volitelně můžete vybrat **+** tlačítko pro přidání sekundární klíčový sloupec jako složený primární klíč. Pokud je potřeba zrušit výběr, zvolte prázdnou hodnotu z rozevíracího seznamu k odebrání sekundární klíč.

1. Pokud chcete nahrát data, vyberte **nahrát řádky** tlačítko.

    [![Nahrávání](media/add-reference-data-set/upload-rows.png)](media/add-reference-data-set/upload-rows.png#lightbox)

    Na stránce potvrdí dokončené nahrávání a zobrazení zprávy **byl úspěšně nahrán datovou sadu**.

## <a name="next-steps"></a>Další postup

* [Spravujte referenční data](time-series-insights-manage-reference-data-csharp.md) prostřednictvím kódu programu.

* Úplnou referenční dokumentaci k rozhraní API najdete v dokumentu [Rozhraní API referenčních dat](/rest/api/time-series-insights/ga-reference-data-api).
