---
author: orspod
ms.service: data-explorer
ms.topic: include
ms.date: 02/27/2020
ms.author: orspodek
ms.openlocfilehash: a2297301a0b9c0540c73c0f50483cccfc3181a0f
ms.sourcegitcommit: be53e74cd24bbabfd34597d0dcb5b31d5e7659de
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/11/2020
ms.locfileid: "79128913"
---
### <a name="event-system-properties-mapping"></a>Mapování vlastností systému událostí

> [!Note]
> * Vlastnosti systému jsou podporovány pro události s jedním záznamem.
> * Pro `csv` mapování se na začátek záznamu přidají vlastnosti. Pro `json` mapování jsou přidány vlastnosti podle názvu, který se zobrazí v rozevíracím seznamu.

Pokud jste v části **zdroj dat** v tabulce vybrali **Vlastnosti systému událostí** , musíte do schématu a mapování tabulky zahrnout následující vlastnosti.

**Příklad schématu tabulky**

Pokud vaše data obsahují tři sloupce (`Timespan`, `Metric`a `Value`) a zahrnuté vlastnosti jsou `x-opt-enqueued-time` a `x-opt-offset`, vytvořte nebo změňte schéma tabulky pomocí tohoto příkazu:

```kusto
    .create-merge table TestTable (TimeStamp: datetime, Metric: string, Value: int, EventHubEnqueuedTime:datetime, EventHubOffset:string)
```

**Příklad mapování sdílených svazků clusteru**

Spuštěním následujících příkazů přidejte data na začátek záznamu. Všimněte si řadových hodnot.

```kusto
    .create table TestTable ingestion csv mapping "CsvMapping1"
    '['
    '   { "column" : "Timespan", "Properties":{"Ordinal":"2"}},'
    '   { "column" : "Metric", "Properties":{"Ordinal":"3"}},'
    '   { "column" : "Value", "Properties":{"Ordinal":"4"}},'
    '   { "column" : "EventHubEnqueuedTime", "Properties":{"Ordinal":"0"}},'
    '   { "column" : "EventHubOffset", "Properties":{"Ordinal":"1"}}'
    ']'
```
 
**Příklad mapování JSON**

Data se přidávají pomocí názvů vlastností systému, jak se zobrazují v seznamu **Vlastnosti systému událostí** v okně **datové připojení** . Spusťte tyto příkazy:

```kusto
    .create table TestTable ingestion json mapping "JsonMapping1"
    '['
    '    { "column" : "Timespan", "Properties":{"Path":"$.timestamp"}},'
    '    { "column" : "Metric", "Properties":{"Path":"$.metric"}},'
    '    { "column" : "Value", "Properties":{"Path":"$.metric_value"}},'
    '    { "column" : "EventHubEnqueuedTime", "Properties":{"Path":"$.x-opt-enqueued-time"}},'
    '    { "column" : "EventHubOffset", "Properties":{"Path":"$.x-opt-offset"}}'
    ']'
```
