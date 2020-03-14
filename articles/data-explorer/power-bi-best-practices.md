---
title: Osvědčené postupy pro použití Power BI k dotazování a vizualizaci dat Azure Průzkumník dat
description: V tomto článku se seznámíte s osvědčenými postupy pro použití Power BI k dotazování a vizualizaci dat Azure Průzkumník dat.
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/26/2019
ms.openlocfilehash: db1d530c9cab77ae612c83a0d4f52478fb9ee270
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/13/2020
ms.locfileid: "79251736"
---
# <a name="best-practices-for-using-power-bi-to-query-and-visualize-azure-data-explorer-data"></a>Osvědčené postupy pro použití Power BI k dotazování a vizualizaci dat Azure Průzkumník dat

Azure Data Explorer je rychlá a vysoce škálovatelná služba pro zkoumání dat protokolů a telemetrie. [Power BI](https://docs.microsoft.com/power-bi/) je řešení obchodní analýzy, které umožňuje vizualizovat data a sdílet výsledky napříč vaší organizací. Azure Průzkumník dat poskytuje tři možnosti pro připojení k datům v Power BI. Použijte [integrovaný konektor](power-bi-connector.md), [importujte dotaz z Azure Průzkumník dat do Power BI](power-bi-imported-query.md)nebo použijte [dotaz SQL](power-bi-sql-query.md). Tento článek poskytuje tipy pro dotazování a vizualizaci dat Průzkumník dat Azure pomocí Power BI. 

## <a name="best-practices-for-using-power-bi"></a>Osvědčené postupy pro používání Power BI 

Když pracujete s terabajty čerstvých nezpracovaných dat, postupujte podle těchto pokynů, abyste zachovali Power BI přichycení a aktualizovali řídicí panely a sestavy:

* **Cestovní světlo** – přeneste do Power BI jenom data, která potřebujete k sestavování sestav. Pro rozsáhlou interaktivní analýzu použijte [webové uživatelské rozhraní Azure Průzkumník dat](web-query-data.md) , které je optimalizované pro průzkum ad hoc pomocí dotazovacího jazyka Kusto.

* **Složený model** – pomocí [složeného modelu](https://docs.microsoft.com/power-bi/desktop-composite-models) můžete kombinovat agregovaná data pro řídicí panely nejvyšší úrovně s filtrovanými provozními nezpracovanými daty. Můžete jasně definovat, kdy se mají používat neupravená data a kdy použít agregované zobrazení. 

* **Režim importu versus režim DirectQuery** – pro interakci menších datových sad použijte režim **importu** . Pro velké, často aktualizované datové sady použijte režim **DirectQuery** . Například Vytvářejte tabulky dimenzí pomocí režimu **importu** , protože jsou malé a často se nemění. Nastavte interval aktualizace podle očekávané míry aktualizace dat. Vytvářejte tabulky faktů pomocí režimu **DirectQuery** , protože tyto tabulky jsou velké a obsahují nezpracovaná data. Pomocí těchto tabulek můžete prezentovat filtrovaná data pomocí Power BI [podrobné analýzy](https://docs.microsoft.com/power-bi/desktop-drillthrough).

* **Paralelismus** – Azure Průzkumník dat je lineární škálovatelná datová platforma, takže můžete zlepšit výkon vykreslování řídicích panelů, a to tak, že zvýšíte paralelismus v rámci koncového toku následujícím způsobem:

   * Zvyšte počet [souběžných připojení v DirectQuery v Power BI](https://docs.microsoft.com/power-bi/desktop-directquery-about#maximum-number-of-connections-option-for-directquery).

   * [Pro zlepšení paralelismu používejte slabou konzistenci](/azure/kusto/concepts/queryconsistency). To může mít dopad na aktuálnost dat.

* **Efektivní průřezy** – pomocí [synchronizace průřezů](https://docs.microsoft.com/power-bi/visuals/power-bi-visualization-slicers#sync-and-use-slicers-on-other-pages) zabráníte načítání dat, než budete připraveni. Po vytvoření struktury datové sady, umístění všech vizuálů a označení všech průřezů můžete vybrat synchronizaci průřezu a načíst jenom data, která potřebujete.

* **Použití filtrů** – můžete použít tolik Power BI filtrů, aby bylo možné zaměřit se na Azure Průzkumník dat hledání relevantních horizontálních oddílů dat.

* **Efektivní vizuály** – vyberte nejvíce vizuálních vizuálů pro vaše data.

## <a name="tips-for-using-the-azure-data-explorer-connector-for-power-bi-to-query-data"></a>Tipy pro použití konektoru služby Azure Průzkumník dat pro Power BI k dotazování na data

V následující části najdete tipy a triky pro používání dotazovacího jazyka Kusto s Power BI. Použití [konektoru Azure Průzkumník dat Power BI k](power-bi-connector.md) vizualizaci dat

### <a name="complex-queries-in-power-bi"></a>Složité dotazy v Power BI

Složité dotazy jsou snadněji vyjádřené v Kusto než v Power Query. Měly by být implementovány jako [Kusto funkce](/azure/kusto/query/functions)a vyvolány v Power BI. Tato metoda je vyžadována při použití **DirectQuery** s příkazy `let` ve vašem dotazu Kusto. Protože Power BI spojí dva dotazy a příkazy `let` nelze použít s operátorem `join`, může dojít k chybám syntaxe. Proto každou část JOIN uložte jako funkci Kusto a umožněte Power BI připojit tyto dvě funkce dohromady.

### <a name="how-to-simulate-a-relative-date-time-operator"></a>Postup simulace relativního operátoru data a času

Power BI neobsahuje *relativní* operátor data a času, jako je například `ago()`.
Chcete-li simulovat `ago()`, použijte kombinaci Power BI funkcí `DateTime.FixedLocalNow()` a `#duration`.

Místo tohoto dotazu pomocí operátoru `ago()`:

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

### <a name="reaching-kusto-query-limits"></a>Dosažení omezení Kusto dotazů 

Dotazy Kusto ve výchozím nastavení vrací až 500 000 řádků nebo 64 MB, jak je popsáno v tématu [omezení dotazů](/azure/kusto/concepts/querylimits). Tato výchozí nastavení můžete přepsat pomocí **pokročilých možností** v okně připojení **Azure Průzkumník dat (Kusto)** :

![Rozšířené možnosti](media/power-bi-best-practices/advanced-options.png)

Tyto možnosti nastavují [příkazy set](/azure/kusto/query/setstatement) v dotazu pro změnu výchozích omezení dotazu:

  * Hodnota **omezit číslo záznamu výsledku dotazu** generuje `set truncationmaxrecords`
  * **Omezení velikosti dat výsledků dotazu v bajtech** generuje `set truncationmaxsize`
  * **Zakázat zkrácení sady výsledků** generuje `set notruncation`

### <a name="using-query-parameters"></a>Použití parametrů dotazu

K dynamické úpravě dotazu můžete použít [parametry dotazu](/azure/kusto/query/queryparametersstatement) . 

#### <a name="using-a-query-parameter-in-the-connection-details"></a>Použití parametru dotazu v podrobnostech připojení

Pomocí parametru dotazu můžete filtrovat informace v dotazu a optimalizovat výkon dotazů.
 
V okně **Upravit dotazy** Rozšířený editor **Home** > 

1. Vyhledejte následující část dotazu:

    ```powerquery-m
    Source = Kusto.Contents("<Cluster>", "<Database>", "<Query>", [])
    ```
   
   Příklad:

    ```powerquery-m
    Source = Kusto.Contents("Help", "Samples", "StormEvents | where State == 'ALABAMA' | take 100", [])
    ```

1. Nahraďte příslušnou část dotazu parametrem. Rozdělit dotaz na více částí a zřetězit je zpět pomocí ampersandu (&) spolu s parametrem.

   Například v dotazu výše pobereme `State == 'ALABAMA'` část a rozdělíme ji na: `State == '` a `'`. do tohoto pole zařadíme parametr `State`:
   
    ```kusto
    "StormEvents | where State == '" & State & "' | take 100"
    ```

1. Pokud dotaz obsahuje uvozovky, zakódovat je správně. Například následující dotaz: 

   ```kusto
   "StormEvents | where State == "ALABAMA" | take 100" 
   ```

   zobrazí se v **Rozšířený editor** následujícím způsobem, a to pomocí dvou uvozovek:

   ```kusto
    "StormEvents | where State == ""ALABAMA"" | take 100"
   ```

   Měl by se nahradit následujícím dotazem se třemi uvozovkami:

   ```kusto
   "StormEvents | where State == """ & State & """ | take 100"
   ```

#### <a name="use-a-query-parameter-in-the-query-steps"></a>Použití parametru dotazu v krocích dotazu

Parametr dotazu můžete použít v jakémkoli kroku dotazu, který ho podporuje. Vyfiltrujte například výsledky na základě hodnoty parametru.

![filtrování výsledků pomocí parametru](media/power-bi-best-practices/filter-using-parameter.png)

### <a name="dont-use-power-bi-data-refresh-scheduler-to-issue-control-commands-to-kusto"></a>Nepoužívejte Power BI Plánovač aktualizace dat k vydávání řídicích příkazů do Kusto

Power BI obsahuje Plánovač aktualizace dat, který může pravidelně vydávat dotazy na zdroj dat. Tento mechanismus by neměl být použit k naplánování řídicích příkazů pro Kusto, protože Power BI předpokládá, že všechny dotazy jsou jen pro čtení.

### <a name="power-bi-can-send-only-short-lt2000-characters-queries-to-kusto"></a>Power BI mohou odesílat pouze krátké (&lt;2000 znaky) dotazy do Kusto

Při spuštění dotazu v Power BI dojde k následující chybě: _"DataSource. Error: Web. Contents se nepodařilo získat obsah z..."_ dotaz je pravděpodobně delší než 2000 znaků. Power BI používá **PowerQuery** k dotazování Kusto vyvoláním požadavku HTTP GET, který tento dotaz zakóduje jako součást NAČTENého identifikátoru URI. Proto jsou dotazy Kusto vydané Power BI omezeny na maximální délku identifikátoru URI požadavku (2000 znaků, mínus malý posun). Jako alternativní řešení můžete definovat [uloženou funkci](/azure/kusto/query/schema-entities/stored-functions) v Kusto a nechat ji Power BI použít v dotazu.

## <a name="next-steps"></a>Další kroky

[Vizualizace dat pomocí konektoru služby Azure Průzkumník dat pro Power BI](power-bi-connector.md)




