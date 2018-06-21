---
title: Postup přidání referenční datové sady do prostředí Azure časové řady statistiky
description: Tento článek popisuje postup přidání referenční datové sady k posílení dat ve vašem prostředí Statistika Azure časové řady.
ms.service: time-series-insights
services: time-series-insights
author: ashannon7
ms.author: jasonh
manager: kfile
ms.reviewer: jasonh, kfile, anshan
ms.workload: big-data
ms.topic: conceptual
ms.date: 02/15/2018
ms.openlocfilehash: 962c3c8d439884cacf010b0bbcab03bd216dbd43
ms.sourcegitcommit: 1438b7549c2d9bc2ace6a0a3e460ad4206bad423
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/20/2018
ms.locfileid: "36294196"
---
# <a name="create-a-reference-data-set-for-your-time-series-insights-environment-using-the-azure-portal"></a>Vytvořit odkaz na sadu dat pro vaše prostředí časové řady statistika pomocí portálu Azure

Tento článek popisuje postup přidání referenční datové sady do prostředí Azure časové řady statistiky. Referenční data jsou užitečné pro připojení k datům zdroje na posílení hodnoty.

Referenční datové sady je kolekce položek, které posílení události ze zdroje událostí. Čas řady Statistika příchozího modul spojí všechny události ze zdroje událostí s odpovídající řádek dat v odkaz na sadu dat. Tato rozšířená událost je pak k dispozici pro dotaz. Toto připojení je založena na sloupce primární klíč v vaší referenční datové sadě definován.

Referenční data není zpětně připojena. To znamená, že pouze aktuálních a budoucích příchozí přenos dat je shodná a připojený k sadu referenční data, jakmile byla nakonfigurována a nahrát.

## <a name="add-a-reference-data-set"></a>Přidat odkaz datové sady

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).

2. Vyhledejte existující časové řady Statistika prostředí. Klikněte na tlačítko **všechny prostředky** v nabídce na levé straně na portálu Azure. Vyberte vaše prostředí Time Series Insights.

3. Vyberte **přehled** stránky. Vyhledejte **časové řady Statistika explorer URL** a otevřete odkaz.  

   Zobrazení Průzkumníka pro vaše prostředí TSI.

4. Rozbalte v Průzkumníku TSI modulu pro výběr prostředí. Zvolte aktivního prostředí. Vyberte ikonu referenční data v pravém horním rohu na stránce explorer.

   ![Přidat odkaz na data](media/add-reference-data-set/add_reference_data.png)

5. Vyberte **+ přidat datové sady** tlačítko začnete přidávat nové datové sady.

   ![Přidat sady dat](media/add-reference-data-set/add_data_set.png)

6. Na **nový odkaz datové sady** vyberte formát dat: 
   - Zvolte **CSV** pro data s položkami oddělenými čárkou. První řádek považován za řádek záhlaví. 
   - Zvolte **pole JSON** pro javascript object notation (JSON) formátovaných dat.

   ![Vyberte formát data.](media/add-reference-data-set/add_data.png)

7. Zadejte data, pomocí jedné ze dvou způsobů:
   - Vložení dat do textového editoru. Pak vyberte **analýzy referenční data** tlačítko.
   - Vyberte **zvolit soubor** tlačítko Přidat data z místní textový soubor. 

   Vložte například data ve formátu CSV: ![data vložit ve formátu CSV](media/add-reference-data-set/csv_data_pasted.png)

   Vložte například data pole JSON: ![data vložit JSON](media/add-reference-data-set/json_data_pasted.png)

   Pokud dojde k chybě při analýze datových hodnot, chyba se zobrazí červeně v dolní části stránky, jako například `CSV parsing error, no rows extracted`.

8. Jakmile je úspěšně analyzovat data, zobrazí se datová mřížka zobrazení sloupce a řádky, které představují data.  Zkontrolujte data mřížky zajistit správnost.

   ![Přidat odkaz na data](media/add-reference-data-set/parse_data.png)

9. Zkontrolujte každý sloupec datový typ předpokládá, že zobrazíte a změnit datový typ v případě potřeby.  Vyberte datový typ symbol v záhlaví sloupce: **#** pro dvojitou (číselná data), **T | F** pro logickou hodnotu, nebo **Abc** řetězce.

   ![Vyberte datové typy umístí na záhlaví sloupců.](media/add-reference-data-set/choose_datatypes.png)

10. Přejmenujte záhlaví sloupců, podle potřeby. Název sloupce klíče je nutné připojení k odpovídající vlastnost ve zdroji událostí. Ujistěte se, že s názvy klíčových sloupců odkaz dat se přesně shodovat s název události příchozí data, včetně rozlišování. Neklíčový sloupec názvy jsou použity k posílení příchozích dat s odpovídajícími hodnotami referenční data.

11. Klikněte na tlačítko **přidejte řádek** nebo **přidat sloupec** přidat více hodnot dat odkaz, podle potřeby.

12. Zadejte hodnotu v **filtrovat řádky...**  pole, které chcete zkontrolovat konkrétní řádky, podle potřeby. Filtr je užitečné pro Kontrola data, ale není použita při nahrávání data.
 
13. Název datové sady, vyplněním **název datové sady** pole výše dat mřížky.

   ![Název datové sady.](media/add-reference-data-set/name_reference_dataset.png)

14. Zadejte **primární klíč** sloupců v datové sadě, výběrem rozevíracím výše dat mřížky.

   ![Vyberte sloupce klíče.](media/add-reference-data-set/set_primary_key.png)

   Volitelně vyberte **+** tlačítko Přidat sekundární klíčový sloupec jako složený primární klíč. Pokud budete muset zrušit výběr, zvolte z rozevírací nabídky k odebrání sekundární klíč prázdnou hodnotu.

15.  Pokud chcete nahrát data, vyberte **nahrát řádky** tlačítko.

   ![Odeslat](media/add-reference-data-set/upload_rows.png)

   Stránce potvrdí dokončené nahrát a zobrazí se zpráva **úspěšně nahrál datovou sadu**.

## <a name="next-steps"></a>Další postup
* [Spravujte referenční data](time-series-insights-manage-reference-data-csharp.md) prostřednictvím kódu programu.
* Úplnou referenční dokumentaci k rozhraní API najdete v dokumentu [Rozhraní API referenčních dat](/rest/api/time-series-insights/time-series-insights-reference-reference-data-api).
