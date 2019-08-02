---
title: Zpracování duplicitních dat v Azure Průzkumník dat
description: V tomto tématu se dozvíte o různých přístupech k práci s duplicitními daty při použití Azure Průzkumník dat.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 12/19/2018
ms.openlocfilehash: 60ec2b86e0205060f907f1fe39d084dca3aac1cd
ms.sourcegitcommit: 6cff17b02b65388ac90ef3757bf04c6d8ed3db03
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/29/2019
ms.locfileid: "68608231"
---
# <a name="handle-duplicate-data-in-azure-data-explorer"></a>Zpracování duplicitních dat v Azure Průzkumník dat

Zařízení odesílající data do cloudu udržují místní mezipaměť dat. V závislosti na velikosti dat můžou místní mezipaměť ukládat data pro dny nebo dokonce měsíce. Chcete chránit vaše analytické databáze před nefunkčními zařízeními, která znovu odesílají data z mezipaměti a způsobují duplicity dat v analytické databázi. Toto téma popisuje osvědčené postupy pro zpracování duplicitních dat pro tyto typy scénářů.

Nejlepší řešení pro duplikaci dat znemožňuje duplikaci. Pokud je to možné, vyřešte problém dříve v datovém kanálu, který šetří náklady spojené s pohybem dat v datovém kanálu, a vyhnete se tak útratě prostředků v kopírování s duplicitními daty, která se do systému ingestují. V situacích, kdy se zdrojový systém nedá upravit, existují různé způsoby, jak s tímto scénářem pracovat.

## <a name="understand-the-impact-of-duplicate-data"></a>Pochopení dopadu duplicitních dat

Monitoruje procento duplicitních dat. Po zjištění procentuální hodnoty duplicitních dat můžete analyzovat rozsah problému a dopad na firmu a vybrat vhodné řešení.

Vzorový dotaz pro identifikaci procenta duplicitních záznamů:

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

### <a name="solution-1-dont-remove-duplicate-data"></a>#1 řešení: Neodstraňovat duplicitní data

Pochopení podnikových požadavků a tolerance duplicitních dat. Některé datové sady mohou spravovat s určitou procentuální hodnotou duplicitních dat. Pokud duplicitní data nemají významný dopad, můžete tuto přítomnost ignorovat. Výhodou neodebrání duplicitních dat není žádná další režie na proces příjmu nebo výkon dotazů.

### <a name="solution-2-handle-duplicate-rows-during-query"></a>#2 řešení: Zpracování duplicitních řádků během dotazu

Další možností je vyfiltrovat duplicitní řádky v datech během dotazu. [`arg_max()`](/azure/kusto/query/arg-max-aggfunction) Agregovaná funkce se dá použít k vyfiltrování duplicitních záznamů a vrácení posledního záznamu na základě časového razítka (nebo jiného sloupce). Výhodou použití této metody je rychlejší příjem dat, protože během doby dotazu dojde ke zrušení duplicity. Kromě toho jsou k dispozici všechny záznamy (včetně duplicitních) pro účely auditování a řešení potíží. Nevýhodou použití `arg_max` funkce je další doba dotazování a zatížení procesoru pokaždé, když se data dotazují. V závislosti na objemu dat, která se dotazují, může být toto řešení nefunkční nebo náročné na paměť a bude vyžadovat přepnutí na jiné možnosti.

V následujícím příkladu se dotazuje na poslední záznam, který se ingestuje pro sadu sloupců, které určují jedinečné záznamy:

```kusto
DeviceEventsAll
| where EventDateTime > ago(90d)
| summarize hint.strategy=shuffle arg_max(EventDateTime, *) by DeviceId, EventId, StationId
```

Tento dotaz lze také umístit do funkce místo přímého dotazování na tabulku:

```kusto
.create function DeviceEventsView
{
DeviceEventsAll
| where EventDateTime > ago(90d)
| summarize arg_max(EventDateTime, *) by DeviceId, EventId, StationId
}
```

### <a name="solution-3-filter-duplicates-during-the-ingestion-process"></a>#3 řešení: Filtrovat duplicity během procesu přijímání

Dalším řešením je filtrovat duplicity během procesu přijímání. Systém ignoruje duplicitní data během přijímání do tabulek Kusto. Data se ingestují do pracovní tabulky a po odebrání duplicitních řádků se zkopírují do jiné tabulky. Výhodou tohoto řešení je, že výkon dotazů se výrazně zvyšuje ve srovnání s předchozím řešením. Nevýhody zahrnují zvýšenou dobu přijímání a další náklady na úložiště dat. Další toto řešení funguje jenom v případě, že duplikace nejsou souběžně ingestovaná. Pokud existuje více souběžných přijímání, které obsahují duplicitní záznamy, mohou být všechny z nich ingestované, protože proces odstranění duplicitních dat nenalezne žádné záznamy v tabulce, které by odpovídaly.    

Následující příklad znázorňuje tuto metodu:

1. Vytvořte další tabulku se stejným schématem:

    ```kusto
    .create table DeviceEventsUnique (EventDateTime: datetime, DeviceId: int, EventId: int, StationId: int)
    ```

1. Vytvořte funkci pro odfiltrování duplicitních záznamů tím, že se spojí nové záznamy s dříve ingesty.

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
    > Spojení jsou operace vázané na procesor a v systému se dodávají další zatížení.

1. Nastavte [zásady aktualizace](/azure/kusto/management/update-policy) pro `DeviceEventsUnique` tabulku. Zásady aktualizace se aktivují, když se do `DeviceEventsAll` tabulky přecházejí nová data. Modul Kusto bude tuto funkci automaticky spouštět, protože se [](/azure/kusto/management/extents-overview) vytvoří nové rozsahy. Zpracování je vymezeno na nově vytvořená data. Následující příkaz spojí zdrojovou tabulku (`DeviceEventsAll`), cílovou tabulku (`DeviceEventsUnique`) a funkci `RemoveDuplicatesDeviceEvents` dohromady a vytvoří zásadu aktualizace.

    ```kusto
    .alter table DeviceEventsUnique policy update
    @'[{"IsEnabled": true, "Source": "DeviceEventsAll", "Query": "RemoveDuplicateDeviceEvents()", "IsTransactional": true, "PropagateIngestionProperties": true}]'
    ```

    > [!NOTE]
    > Zásada aktualizace rozšiřuje dobu trvání příjmu, protože data se filtrují během ingestování a pak se pokaždé ingestují `DeviceEventsAll` dvakrát (do tabulky `DeviceEventsUnique` a do tabulky).

1. Volitelné Nastavte menší dobu uchovávání dat v `DeviceEventsAll` tabulce, aby nedocházelo k ukládání kopií dat. Vyberte počet dnů v závislosti na objemu dat a dobu, po kterou chcete uchovávat data pro řešení potíží. Můžete ho nastavit na `0d` dny uchovávání za účelem úspory nákladů a zvýšení výkonu, protože data nejsou nahrána do úložiště.

    ```kusto
    .alter-merge table DeviceEventsAll policy retention softdelete = 1d
    ```

## <a name="summary"></a>Souhrn

Duplikaci dat lze zpracovat několika způsoby. Pečlivě vyhodnoťte možnosti a vezměte v úvahu cenu a výkon a určete tak správnou metodu pro vaši firmu.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Psaní dotazů pro Azure Data Explorer](write-queries.md)
