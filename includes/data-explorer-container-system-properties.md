---
author: orspod
ms.service: data-explorer
ms.topic: include
ms.date: 02/27/2020
ms.author: orspodek
ms.openlocfilehash: a2297301a0b9c0540c73c0f50483cccfc3181a0f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "79128913"
---
### <a name="event-system-properties-mapping"></a>Mapování vlastností systému událostí

> [!Note]
> * Vlastnosti systému jsou podporovány pro události s jedním záznamem.
> * Pro `csv` mapování jsou vlastnosti přidány na začátek záznamu. Pro `json` mapování jsou vlastnosti přidány podle názvu, který se zobrazí v rozevíracím seznamu.

Pokud jste v tabulce vybrali **vlastnosti systému událostí** v části **Zdroj dat,** musíte do schématu a mapování tabulky zahrnout následující vlastnosti.

**Příklad schématu tabulky**

Pokud data obsahují tři`Timespan` `Metric`sloupce `Value`( , , a `x-opt-enqueued-time` `x-opt-offset`) a vlastnosti, které zahrnete, jsou a , vytvořte nebo změňte schéma tabulky pomocí tohoto příkazu:

```kusto
    .create-merge table TestTable (TimeStamp: datetime, Metric: string, Value: int, EventHubEnqueuedTime:datetime, EventHubOffset:string)
```

**Příklad mapování CSV**

Spusťte následující příkazy a přidejte data na začátek záznamu. Poznamenejte si hodnoty číselníku.

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

Data se přidávají pomocí názvů vlastností systému tak, jak jsou uvedeny v seznamu **vlastností systému událostí** **datového připojení.** Spusťte tyto příkazy:

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
