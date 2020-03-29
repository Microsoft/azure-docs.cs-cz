---
title: Zpracování duplicitních dat v Průzkumníku dat Azure
description: Toto téma vám ukáže různé přístupy k řešení duplicitních dat při použití Průzkumníka dat Azure.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 12/19/2018
ms.openlocfilehash: 60ec2b86e0205060f907f1fe39d084dca3aac1cd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "68608231"
---
# <a name="handle-duplicate-data-in-azure-data-explorer"></a>Zpracování duplicitních dat v Průzkumníku dat Azure

Zařízení odesílající data do cloudu udržují místní mezipaměť dat. V závislosti na velikosti dat může být místní mezipaměti ukládání dat pro dny nebo dokonce měsíce. Chcete chránit analytické databáze před nefunkčními zařízeními, která znovu odesílají data uložená v mezipaměti a způsobují duplikaci dat v analytické databázi. Toto téma popisuje osvědčené postupy pro zpracování duplicitních dat pro tyto typy scénářů.

Nejlepším řešením pro duplikaci dat je zabránit duplikaci. Pokud je to možné, opravte problém dříve v datovém kanálu, což šetří náklady spojené s pohybem dat podél datového kanálu a zabraňuje utrácení prostředků na zvládnutí duplicitních dat přijímaných do systému. Však v situacích, kdy zdrojový systém nelze změnit, existují různé způsoby, jak řešit tento scénář.

## <a name="understand-the-impact-of-duplicate-data"></a>Pochopení dopadu duplicitních dat

Sledujte procento duplicitních dat. Po zjištění procenta duplicitních dat můžete analyzovat rozsah problému a obchodní dopad a zvolit vhodné řešení.

Ukázkový dotaz k identifikaci procenta duplicitních záznamů:

```kusto
let _sample = 0.01; // 1% sampling
let _data =
DeviceEventsAll
| where EventDateTime between (datetime('10-01-2018 10:00') .. datetime('10-10-2018 10:00'));
let _totalRecords = toscalar(_data | count);
_data
| where rand()<= _sample
| summarize recordsCount=count() by hash(DeviceId) + hash(EventId) + hash(StationId)  // Use all dimensions that make row unique. Combining hashes can be improved
| summarize duplicateRecords=countif(recordsCount  > 1)
| extend duplicate_percentage = (duplicateRecords / _sample) / _totalRecords  
```

## <a name="solutions-for-handling-duplicate-data"></a>Řešení pro zpracování duplicitních dat

### <a name="solution-1-dont-remove-duplicate-data"></a>#1 řešení: Neodstraňujte duplicitní data

Seznamte se s obchodními požadavky a tolerancí duplicitních dat. Některé datové sady lze spravovat s určitým procentem duplicitních dat. Pokud duplicitní data nemají velký dopad, můžete jejich přítomnost ignorovat. Výhodou není odebrání duplicitních dat není žádná další režie na proces ingestování nebo výkon dotazu.

### <a name="solution-2-handle-duplicate-rows-during-query"></a>#2 řešení: Zpracování duplicitních řádků během dotazu

Další možností je odfiltrovat duplicitní řádky v datech během dotazu. Agregovanou [`arg_max()`](/azure/kusto/query/arg-max-aggfunction) funkci lze použít k odfiltrování duplicitních záznamů a vrácení posledního záznamu na základě časového razítka (nebo jiného sloupce). Výhodou použití této metody je rychlejší přihlašování, protože během doby dotazu dochází k odstranění duplikace. Kromě toho jsou k dispozici všechny záznamy (včetně duplikátů) pro auditování a řešení potíží. Nevýhodou použití `arg_max` funkce je další čas dotazu a zatížení procesoru při každém dotazování na data. V závislosti na množství dotazovaných dat může být toto řešení nefunkční nebo náročné na paměť a bude vyžadovat přepnutí na jiné možnosti.

V následujícím příkladu se dotazujeme na poslední záznam, který byl ingem pro sadu sloupců, které určují jedinečné záznamy:

```kusto
DeviceEventsAll
| where EventDateTime > ago(90d)
| summarize hint.strategy=shuffle arg_max(EventDateTime, *) by DeviceId, EventId, StationId
```

Tento dotaz lze také umístit do funkce namísto přímého dotazování na tabulku:

```kusto
.create function DeviceEventsView
{
DeviceEventsAll
| where EventDateTime > ago(90d)
| summarize arg_max(EventDateTime, *) by DeviceId, EventId, StationId
}
```

### <a name="solution-3-filter-duplicates-during-the-ingestion-process"></a>#3 řešení: Filtrujte duplikáty během procesu požití

Dalším řešením je filtrovat duplikáty během procesu požití. Systém ignoruje duplicitní data během požití do kusto tabulek. Data jsou ingestována do pracovní tabulky a po odebrání duplicitních řádků zkopírována do jiné tabulky. Výhodou tohoto řešení je, že výkon dotazu se výrazně zlepší ve srovnání s předchozím řešením. Mezi nevýhody patří zvýšená doba požití a dodatečné náklady na ukládání dat. Navíc toto řešení funguje pouze v případě, že duplikace nejsou ingestovány současně. Pokud existuje více souběžných ingestování obsahujícíduplicitní záznamy, všechny mohou být požití, protože proces odstranění duplicit nenajde žádné existující odpovídající záznamy v tabulce.    

Následující příklad znázorňuje tuto metodu:

1. Vytvořte jinou tabulku se stejným schématem:

    ```kusto
    .create table DeviceEventsUnique (EventDateTime: datetime, DeviceId: int, EventId: int, StationId: int)
    ```

1. Vytvořte funkci pro odfiltrování duplicitních záznamů anti-spojením nových záznamů s dříve pozítanými záznamy.

    ```kusto
    .create function RemoveDuplicateDeviceEvents()
    {
    DeviceEventsAll
    | join hint.strategy=broadcast kind = anti
        (
        DeviceEventsUnique
        | where EventDateTime > ago(7d)   // filter the data for certain time frame
        | limit 1000000   //set some limitations (few million records) to avoid choking-up the system during outage recovery

        ) on DeviceId, EventId, StationId
    }
    ```

    > [!NOTE]
    > Spojení jsou operace vázané na procesor a přidat další zatížení systému.

1. Nastavte [zásady aktualizace](/azure/kusto/management/update-policy) v `DeviceEventsUnique` tabulce. Zásady aktualizace se aktivují, `DeviceEventsAll` když do tabulky přejdou nová data. Modul Kusto automaticky provede funkci při vytváření nových [rozsahů.](/azure/kusto/management/extents-overview) Zpracování je vymezeno na nově vytvořená data. Následující příkaz sešízdrojovou`DeviceEventsAll`tabulku (`DeviceEventsUnique`), cílovou `RemoveDuplicatesDeviceEvents` tabulku ( ) a funkci společně a vytvoří zásadu aktualizace.

    ```kusto
    .alter table DeviceEventsUnique policy update
    @'[{"IsEnabled": true, "Source": "DeviceEventsAll", "Query": "RemoveDuplicateDeviceEvents()", "IsTransactional": true, "PropagateIngestionProperties": true}]'
    ```

    > [!NOTE]
    > Zásady aktualizace prodlužují dobu trvání požití, protože data jsou filtrována během požití `DeviceEventsAll` a poté `DeviceEventsUnique` ingestována dvakrát (do tabulky a do tabulky).

1. (Nepovinné) Nastavte nižší uchovávání dat `DeviceEventsAll` v tabulce, abyste zabránili ukládání kopií dat. Zvolte počet dní v závislosti na objemu dat a době, po kterou chcete uchovávat data pro řešení potíží. Můžete ji nastavit `0d` na uchovávání dnů, abyste ušetřili COGS a zlepšili výkon, protože data se nenahrají do úložiště.

    ```kusto
    .alter-merge table DeviceEventsAll policy retention softdelete = 1d
    ```

## <a name="summary"></a>Souhrn

Duplikace dat lze zpracovat několika způsoby. Pečlivě vyhodnoťte možnosti s přihlédnutím k ceně a výkonu, abyste určili správnou metodu pro vaše podnikání.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Psaní dotazů pro Azure Data Explorer](write-queries.md)
