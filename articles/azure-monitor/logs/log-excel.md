---
title: Integrace Log Analytics a Excelu
description: Načte Log Analytics dotaz do Excelu a aktualizuje výsledky v Excelu.
ms.topic: conceptual
author: roygalMS
ms.author: roygal
ms.date: 11/03/2020
ms.openlocfilehash: f2834e9bd91ecbbf32e0321179c2359862a5b605
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102041106"
---
# <a name="integrate-log-analytics-and-excel"></a>Integrace Log Analytics a Excelu

Můžete integrovat Azure Monitor Log Analytics a Microsoft Excel pomocí dotazů M a rozhraní API pro Log Analytics. Tato integrace umožňuje odeslat až 500 000 záznamů do Excelu, pokud celkový objem výsledků nepřekračuje 61MiB.

> [!NOTE]
> Vzhledem k tomu, že je Excel místní klientská aplikace, mají omezení místních hardwarových a softwarových omezení dopad na výkon a možnost zpracování rozsáhlých datových sad.

## <a name="create-your-m-query-in-log-analytics"></a>Vytvoření dotazu M v Log Analytics 

1. **Vytvořte a spusťte dotaz** v Log Analytics obvyklým způsobem. Nedělejte si starosti, pokud jste dosáhli omezení 10 000 záznamů v uživatelském rozhraní.  Doporučujeme použít relativní kalendářní data, jako je funkce před nebo při výběru uživatelského rozhraní, takže Excel aktualizuje pravou sadu dat.
  
2. **Exportovat dotaz** – Jakmile budete spokojeni s dotazem a jeho výsledky, exportujte dotaz do M pomocí příkazu Log Analytics **exportovat do Power BI (dotaz m)** v nabídce *Export* :

:::image type="content" source="media/log-excel/export-query.png" alt-text="Log Analytics dotaz s možností data a exportu" border="true":::



Když vyberete tuto možnost, stáhne se soubor. txt, který obsahuje kód M, který můžete použít v Excelu.

Výše uvedený dotaz exportuje následující kód M. Tady je příklad kódu M exportovaného pro dotaz v našem příkladu:

```m
/*
The exported Power Query Formula Language (M Language ) can be used with Power Query in Excel
and Power BI Desktop.
For Power BI Desktop follow the instructions below: 
1) Download Power BI Desktop from https://powerbi.microsoft.com/desktop/
2) In Power BI Desktop select: 'Get Data' -> 'Blank Query'->'Advanced Query Editor'
3) Paste the M Language script into the Advanced Query Editor and select 'Done'
*/

let AnalyticsQuery =
let Source = Json.Document(Web.Contents("https://api.loganalytics.io/v1/workspaces/ddcfc599-cae0-48ee-9026-fffffffffffff/query", 
[Query=[#"query"="

Heartbeat 
| summarize dcount(ComputerIP) by bin(TimeGenerated, 1h)    
| render timechart
",#"x-ms-app"="OmsAnalyticsPBI",#"timespan"="P1D",#"prefer"="ai.response-thinning=true"],Timeout=#duration(0,0,4,0)])),
TypeMap = #table(
{ "AnalyticsTypes", "Type" }, 
{ 
{ "string",   Text.Type },
{ "int",      Int32.Type },
{ "long",     Int64.Type },
{ "real",     Double.Type },
{ "timespan", Duration.Type },
{ "datetime", DateTimeZone.Type },
{ "bool",     Logical.Type },
{ "guid",     Text.Type },
{ "dynamic",  Text.Type }
}),
DataTable = Source[tables]{0},
Columns = Table.FromRecords(DataTable[columns]),
ColumnsWithType = Table.Join(Columns, {"type"}, TypeMap , {"AnalyticsTypes"}),
Rows = Table.FromRows(DataTable[rows], Columns[name]), 
Table = Table.TransformColumnTypes(Rows, Table.ToList(ColumnsWithType, (c) => { c{0}, c{3}}))
in
Table
in AnalyticsQuery
```

## <a name="connect-query-to-excel"></a>Připojení dotazu k Excelu 

Import dotazu. 

1. Otevřete Microsoft Excel. 
1. Na pásu karet přejděte do nabídky **data** . Vyberte **získat data**. Z **jiných zdrojů** vyberte **prázdný dotaz**:
 
   :::image type="content" source="media/log-excel/excel-import-blank-query.png" alt-text="Možnost importu z prázdné v aplikaci Excel" border="true":::

1. V okně Power Query vyberte **Rozšířený editor**:

   :::image type="content" source="media/log-excel/advanced-editor.png" alt-text="Editor rozšířených dotazů v Excelu" border="true":::

 
1. Text v rozšířeném editoru nahraďte dotazem exportovaným z Log Analytics:

   :::image type="content" source="media/log-excel/advanced-editor-2.png" alt-text="Vytvoření prázdného dotazu" border="true":::
 
1. Vyberte **Hotovo** a pak **načíst a zavřít**. Aplikace Excel spustí dotaz pomocí rozhraní API Log Analytics a sady výsledků pak zobrazí.
 

   :::image type="content" source="media/log-excel/excel-query-result.png" alt-text="Výsledky dotazu v Excelu" border="true":::

> [!Note]
> Pokud je počet záznamů menší, než se očekávalo, může objem výsledků překročit limit 61MiB. Zkuste použít `project` nebo `project-away` v dotazu omezit sloupce na tu, kterou potřebujete.

##  <a name="refreshing--data"></a>Aktualizace dat

Data můžete aktualizovat přímo z Excelu. Ve skupině nabídky **data** na pásu karet aplikace Excel vyberte tlačítko **aktualizovat** .
 
## <a name="next-steps"></a>Další kroky

Další informace o integraci Excelu s externími zdroji dat najdete v tématu [Import dat z externích zdrojů dat (Power Query)](https://support.office.com/article/import-data-from-external-data-sources-power-query-be4330b3-5356-486c-a168-b68e9e616f5a) .