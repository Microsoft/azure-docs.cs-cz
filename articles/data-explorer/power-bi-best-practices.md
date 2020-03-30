---
title: Doporučené postupy pro používání Power BI k dotazování a vizualizaci dat Azure Data Exploreru
description: V tomto článku se dozvíte osvědčené postupy pro použití Power BI k dotazování a vizualizaci dat Azure Data Exploreru.
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/26/2019
ms.openlocfilehash: db1d530c9cab77ae612c83a0d4f52478fb9ee270
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79251736"
---
# <a name="best-practices-for-using-power-bi-to-query-and-visualize-azure-data-explorer-data"></a>Doporučené postupy pro používání Power BI k dotazování a vizualizaci dat Azure Data Exploreru

Průzkumník dat Azure je rychlá a vysoce škálovatelná služba pro zkoumání dat protokolů a telemetrie. [Power BI](https://docs.microsoft.com/power-bi/) je řešení pro obchodní analýzu, které vám umožní vizualizovat data a sdílet výsledky v celé organizaci. Azure Data Explorer nabízí tři možnosti pro připojení k datům v Power BI. Použijte [integrovaný konektor](power-bi-connector.md), [importujte dotaz z Azure Data Exploreru do Power BI](power-bi-imported-query.md)nebo použijte dotaz [SQL](power-bi-sql-query.md). Tento článek vám poskytne tipy pro dotazování a vizualizaci dat Azure Data Exploreru pomocí Power BI. 

## <a name="best-practices-for-using-power-bi"></a>Doporučené postupy pro používání Power BI 

Při práci s terabajty čerstvých nezpracovaných dat postupujte podle těchto pokynů, aby řídicí panely A sestavy Power BI byly přehledné a aktualizované:

* **Cestovní světlo** – Do Power BI přenesete jenom data, která potřebujete pro sestavy. Pro hloubkovou interaktivní analýzu použijte [webové uživatelské rozhraní Průzkumníka dat Azure,](web-query-data.md) které je optimalizované pro průzkum ad hoc pomocí dotazovacího jazyka Kusto.

* **Složený model** – Pomocí [složeného modelu](https://docs.microsoft.com/power-bi/desktop-composite-models) můžete kombinovat agregovaná data pro řídicí panely nejvyšší úrovně s filtrovanými provozními nezpracovanými daty. Můžete jasně definovat, kdy a kdy použít agregované zobrazení. 

* **Režim importu versus režim DirectQuery** – pro interakci menších datových sad použijte režim **importu.** Režim **DirectQuery** použijte pro velké, často aktualizované sady dat. Tabulky dimenzí můžete například vytvářet pomocí režimu **importu,** protože jsou malé a často se nemění. Nastavte interval aktualizace podle očekávané rychlosti aktualizací dat. Vytvořte tabulky faktů pomocí režimu **DirectQuery,** protože tyto tabulky jsou velké a obsahují nezpracovaná data. Tyto tabulky slouží k prezentaci filtrovaných dat pomocí [podrobné analýzy](https://docs.microsoft.com/power-bi/desktop-drillthrough)Power BI .

* **Paralelismus** – Azure Data Explorer je lineárně škálovatelná datová platforma, proto můžete zlepšit výkon vykreslování řídicího panelu zvýšením paralelismu toku od konce na konec takto:

   * Zvyšte počet [souběžných připojení v DirectQuery v Power BI](https://docs.microsoft.com/power-bi/desktop-directquery-about#maximum-number-of-connections-option-for-directquery).

   * Použijte [slabou konzistenci ke zlepšení paralelismu](/azure/kusto/concepts/queryconsistency). To může mít vliv na čerstvost dat.

* **Efektivní průřezy** – Pomocí [synchronizačních průřezů](https://docs.microsoft.com/power-bi/visuals/power-bi-visualization-slicers#sync-and-use-slicers-on-other-pages) zabraňte sestavám v načítání dat dříve, než budete připraveni. Po strukturované sadě dat, umístění všech vizuálů a označení všech průřezů můžete vybrat synchronizační průřez, který načte pouze potřebná data.

* **Použití filtrů** – Použijte co nejvíce filtrů Power BI k zaostření hledání Průzkumníka dat Azure na příslušné úlomky dat.

* **Efektivní vizuály** – Vyberte nejvýkonnější vizuály pro vaše data.

## <a name="tips-for-using-the-azure-data-explorer-connector-for-power-bi-to-query-data"></a>Tipy pro použití konektoru Azure Data Explorer pro Power BI k dotazování dat

Následující část obsahuje tipy a triky pro používání dotazovacího jazyka Kusto s Power BI. Vizualizace dat pomocí [konektoru Průzkumníka dat Azure pro Power BI](power-bi-connector.md)

### <a name="complex-queries-in-power-bi"></a>Komplexní dotazy v Power BI

Složité dotazy se snadněji vyjadřují v Kusto než v Power Query. Měly by být implementovány jako [funkce Kusto](/azure/kusto/query/functions)a vyvolány v Power BI. Tato metoda je vyžadována `let` při použití **DirectQuery** s příkazy v dotazu Kusto. Vzhledem k tomu, že `let` Power BI spojuje dva `join` dotazy a příkazy nelze použít s operátorem, může dojít k chybám syntaxe. Proto uložte každou část spojení jako funkci Kusto a umožněte Power BI spojit tyto dvě funkce dohromady.

### <a name="how-to-simulate-a-relative-date-time-operator"></a>Jak simulovat operátor relativní datum a čas

Power BI neobsahuje *operátor relativní* datum a `ago()`čas, například .
K `ago()`simulaci použijte `DateTime.FixedLocalNow()` kombinaci funkcí A `#duration` Power BI.

Namísto tohoto dotazu pomocí operátoru: `ago()`

```kusto
    StormEvents | where StartTime > (now()-5d)
    StormEvents | where StartTime > ago(5d)
``` 

Použijte následující ekvivalentní dotaz:

```powerquery-m
let
    Source = Kusto.Contents("help", "Samples", "StormEvents", []),
    #"Filtered Rows" = Table.SelectRows(Source, each [StartTime] > (DateTime.FixedLocalNow()-#duration(5,0,0,0)))
in
    #"Filtered Rows"
```

### <a name="reaching-kusto-query-limits"></a>Dosažení limitů dotazů Kusto 

Kusto dotazy vrátit ve výchozím nastavení až 500.000 řádků nebo 64 MB, jak je popsáno v [omezení dotazu](/azure/kusto/concepts/querylimits). Tyto výchozí hodnoty můžete přepsat pomocí **rozšířených možností** v okně připojení **Průzkumníka dat (Kusto):**

![Rozšířené možnosti](media/power-bi-best-practices/advanced-options.png)

Tyto možnosti problém [nastavit příkazy](/azure/kusto/query/setstatement) s dotazem změnit výchozí limity dotazu:

  * **Limit ní číslo záznamu výsledku** generuje`set truncationmaxrecords`
  * **Omezit velikost dat výsledků dotazu v bajtech** generuje`set truncationmaxsize`
  * **Zakázat zkrácení sady výsledků** generuje`set notruncation`

### <a name="using-query-parameters"></a>Použití parametrů dotazu

[Parametry dotazu](/azure/kusto/query/queryparametersstatement) můžete použít k dynamické úpravě dotazu. 

#### <a name="using-a-query-parameter-in-the-connection-details"></a>Použití parametru dotazu v podrobnostech o připojení

Pomocí parametru dotazu můžete filtrovat informace v dotazu a optimalizovat výkon dotazu.
 
V okně **Upravit dotazy,** **Home** > **Advanced Editor**

1. Najděte následující část dotazu:

    ```powerquery-m
    Source = Kusto.Contents("<Cluster>", "<Database>", "<Query>", [])
    ```
   
   Například:

    ```powerquery-m
    Source = Kusto.Contents("Help", "Samples", "StormEvents | where State == 'ALABAMA' | take 100", [])
    ```

1. Nahraďte příslušnou část dotazu parametrem. Rozdělte dotaz na více částí a zřetězit je zpět pomocí ampersand (&) spolu s parametrem.

   Například ve výše uvedeném dotazu `State == 'ALABAMA'` vezmeme část a `State == '` rozdělíme ji na: a `'` umístíme `State` parametr mezi ně:
   
    ```kusto
    "StormEvents | where State == '" & State & "' | take 100"
    ```

1. Pokud dotaz obsahuje uvozovky, zakódujte je správně. Například následující dotaz: 

   ```kusto
   "StormEvents | where State == "ALABAMA" | take 100" 
   ```

   se zobrazí v **rozšířeném editoru** takto se dvěma uvozovkami:

   ```kusto
    "StormEvents | where State == ""ALABAMA"" | take 100"
   ```

   Měl by být nahrazen následujícím dotazem třemi uvozovkami:

   ```kusto
   "StormEvents | where State == """ & State & """ | take 100"
   ```

#### <a name="use-a-query-parameter-in-the-query-steps"></a>Použití parametru dotazu v krocích dotazu

Parametr dotazu můžete použít v libovolném kroku dotazu, který jej podporuje. Můžete například filtrovat výsledky na základě hodnoty parametru.

![filtrovat výsledky pomocí parametru](media/power-bi-best-practices/filter-using-parameter.png)

### <a name="dont-use-power-bi-data-refresh-scheduler-to-issue-control-commands-to-kusto"></a>Nepoužívat plánovač aktualizace dat Power BI k vydávání řídicích příkazů kusto

Power BI obsahuje plánovač aktualizací dat, který může pravidelně vydávat dotazy na zdroj dat. Tento mechanismus by se neměl používat k plánování řídicích příkazů do Kusuto, protože Power BI předpokládá, že všechny dotazy jsou jen pro čtení.

### <a name="power-bi-can-send-only-short-lt2000-characters-queries-to-kusto"></a>Power BI může do&lt;Kusto odesílat pouze krátké dotazy ( 2000 znaků)

Pokud spuštění dotazu v Power BI vede k následující chybě: _DataSource.Error: Web.Contents se nepodařilo získat obsah z..."_ dotaz je pravděpodobně delší než 2000 znaků. Power BI používá **PowerQuery** k dotazování Kusto vydáním požadavku HTTP GET, který kóduje dotaz jako součást uri načítání. Proto kusto dotazy vydané Power BI jsou omezeny na maximální délku požadavku URI (2000 znaků, minus malý posun). Jako zástupné řešení můžete definovat [uloženou funkci](/azure/kusto/query/schema-entities/stored-functions) v Kusto a nechat Power BI tuto funkci použít v dotazu.

## <a name="next-steps"></a>Další kroky

[Vizualizace dat pomocí konektoru Azure Data Explorer pro Power BI](power-bi-connector.md)




