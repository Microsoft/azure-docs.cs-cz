---
title: Řešení duplicitních dat.
description: Toto téma se zobrazí různé způsoby, jak řešit duplicitních dat.
services: data-explorer
author: orspod
ms.author: v-orspod
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 12/19/2018
ms.openlocfilehash: f417ba7d0fcd6f9d6b5bd6cd43cf1730af2ca53c
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/04/2019
ms.locfileid: "54028191"
---
# <a name="deal-with-duplicate-data"></a>Řešení duplicitních dat.

Zařízení, odesílání dat do cloudu zachovat data v místní mezipaměti. V závislosti na velikosti dat v místní mezipaměti může ukládat data pro dny nebo dokonce měsíce. Chcete chránit vaše analytické databáze z nefunkční zařízení, která znovu odeslat data uložená v mezipaměti a způsobit, že duplicitních dat. analytické databáze. Toto téma popisuje doporučené postupy pro zpracování duplicitních dat pro tyto druhy scénářů.

Nejlepší řešení pro duplicity dat brání duplicity. Pokud je to možné tento problém vyřešit, dříve v kanálu dat, která šetří náklady spojené s přesuny dat podél datového kanálu a zabraňuje zbavuje prostředky zvládnutí duplicitních dat přijatých do systému. Ale v situacích, kdy nelze upravit zdrojový systém, existují různé způsoby, jak řešit tento scénář.

## <a name="understand-the-impact-of-duplicate-data"></a>Na vědomí následky duplicitních dat.

Monitorování procentuální podíl duplicitní data. Ihned po zjištění procento duplicitní data můžete analyzovat oboru problém a obchodní dopad na chod firmy a výběr vhodné řešení.

Ukázkový dotaz k identifikaci procento duplicitní záznamy:

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

## <a name="solutions-for-handling-duplicate-data"></a>Řešení pro zpracování duplicitních dat.

### <a name="solution-1-dont-remove-duplicate-data"></a>Řešení #1: Neodebírat duplicitních dat.

Pochopení vašich obchodních požadavků a odolnost duplicitních dat. Některé datové sady můžete spravovat společně procento duplicitní data. Pokud duplicitní data nemá významný vliv, můžete ignorovat jeho přítomnost. Výhodou není odstranění duplicitních dat je spojena žádná další režie na příjem výkon procesu nebo dotazu.

### <a name="solution-2-handle-duplicate-rows-during-query"></a>Řešení #2: Popisovač duplicitních řádků během zpracování dotazu

Další možností je vyfiltrovat všechny duplicitní řádky v datech během zpracování dotazu. [ `arg_max()` ](/azure/kusto/query/arg-max-aggfunction) Agregované funkci lze použít k filtrování duplicitních záznamů a vrátí poslední záznam na základě časového razítka (nebo jiný sloupec). Výhodou této metody je rychlejšího příjmu, protože duplicit spadá čas dotazu. Kromě toho jsou k dispozici pro auditování a řešení potíží s všechny záznamy (včetně duplikáty). Nevýhodou použití `arg_max` funkce je zatížení procesoru a čas dalšího dotazu pokaždé, když se dotazuje data. V závislosti na množství dat, která je dotazována toto řešení může být nefunkční nebo využívání paměti a bude vyžadovat přepínání na další možnosti.

V následujícím příkladu zadáme dotaz na poslední záznam přijatých pro sadu sloupců, které určují jedinečné záznamy:

```kusto
DeviceEventsAll
| where EventDateTime > ago(90d)
| summarize hint.strategy=shuffle arg_max(EventDateTime, *) by DeviceId, EventId, StationId
```

Tento dotaz lze také umístit uvnitř funkce namísto přímého dotazování v tabulce:

```kusto
.create function DeviceEventsView
{
DeviceEventsAll
| where EventDateTime > ago(90d)
| summarize arg_max(EventDateTime, *) by DeviceId, EventId, StationId
}
```

### <a name="solution-3-filter-duplicates-during-the-ingestion-process"></a>Řešení #3: Duplicitní filtr během příjmu

Druhým řešením je filtrovat duplicity během příjmu. Systém ignoruje duplicitních dat při ingestování do tabulek Kusto. Data se ingestují do pracovní tabulky a zkopírovány do jiné tabulky po odebrání duplicitních řádků. Výhodou tohoto řešení je výrazně zvyšuje výkon dotazů srovnání s předchozím řešením. Nevýhody zahrnují zvýšení ingestování čas a náklady na úložiště další data.

Následující příklad znázorňuje tuto metodu:

1. Vytvoření další tabulky pomocí stejné schéma:

    ```kusto
    .create table DeviceEventsUnique (EventDateTime: datetime, DeviceId: int, EventId: int, StationId: int)
    ```

1. Vytvoření funkce k filtrování duplicitních záznamů pomocí proti spojovacího nové záznamy, přičemž dříve přijaté.

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
    > Spojení operace vázané na procesor a přidat další zátěž na systém.

1. Nastavte [zásady aktualizace](/azure/kusto/management/update-policy) na `DeviceEventsUnique` tabulky. Aktualizace zásada se aktivuje, když nová data přesunou do `DeviceEventsAll` tabulky. Modul Kusto se automaticky spustí funkci jako nové [rozsahy](/azure/kusto/management/extents-overview) jsou vytvořeny. Zpracování je vymezen na nově vytvořený data. Následující příkaz spojí zdrojová tabulka (`DeviceEventsAll`), cílové tabulky (`DeviceEventsUnique`) a funkce `RemoveDuplicatesDeviceEvents` dohromady a vytvoří zásadu aktualizace.

    ```kusto
    .alter table DeviceEventsUnique policy update
    @'[{"IsEnabled": true, "Source": "DeviceEventsAll", "Query": "RemoveDuplicateDeviceEvents()", "IsTransactional": true, "PropagateIngestionProperties": true}]'
    ```

    > [!NOTE]
    > Zásady aktualizace rozšiřuje trvání ingestování, protože data jsou filtrovány během příjmu a pak přijatých dvakrát (na `DeviceEventsAll` tabulky a `DeviceEventsUnique` tabulky).

1. (Volitelné) Nastavit na nižší uchovávání dat `DeviceEventsAll` tabulka, která se vyhněte se ukládání kopie dat. Zvolte počet dní v závislosti na objemu dat a dobu, kterou chcete uchovat data pro řešení potíží. Můžete nastavit na `0d` dnů uchovávání dat pro uložení COGS a zvýšit výkon, protože se data nahrají do úložiště.

    ```kusto
    .alter-merge table DeviceEventsAll policy retention softdelete = 1d
    ```

## <a name="summary"></a>Souhrn

Duplikace dat mohou být zpracovány v několika způsoby. Zhodnotit možnosti pečlivě, s ohledem na účet cenou a výkonem, určit správnou metodu pro vaši firmu.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Psaní dotazů pro Azure Data Explorer](write-queries.md)
