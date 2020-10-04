---
title: Výstupy z Azure Stream Analytics
description: Tento článek popisuje možnosti výstupu dat, které jsou k dispozici pro Azure Stream Analytics.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.custom: contperfq1
ms.date: 10/2/2020
ms.openlocfilehash: 5f109ad719ada9728938f6e37d4ec854d3950a24
ms.sourcegitcommit: 19dce034650c654b656f44aab44de0c7a8bd7efe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/04/2020
ms.locfileid: "91708431"
---
# <a name="outputs-from-azure-stream-analytics"></a>Výstupy z Azure Stream Analytics

Azure Stream Analytics úloha se skládá ze vstupu, dotazu a výstupu. Existuje několik typů výstupu, do kterých můžete posílat transformovaná data. V tomto článku jsou uvedené podporované výstupy Stream Analytics. Pokud navrhujete Stream Analytics dotaz, přečtěte si název výstupu pomocí [klauzule into](https://docs.microsoft.com/stream-analytics-query/into-azure-stream-analytics). Můžete použít jeden výstup na úlohu nebo více výstupů na úlohu streamování (Pokud je potřebujete) přidáním více klauzulí do dotazu.

Chcete-li vytvářet, upravovat a testovat Stream Analytics výstupy úlohy, můžete použít [Azure Portal](stream-analytics-quick-create-portal.md#configure-job-output), [Azure PowerShell](stream-analytics-quick-create-powershell.md#configure-output-to-the-job), rozhraní [.NET API](https://docs.microsoft.com/dotnet/api/microsoft.azure.management.streamanalytics.ioutputsoperations?view=azure-dotnet), [REST API](https://docs.microsoft.com/rest/api/streamanalytics/stream-analytics-output)a [Visual Studio](stream-analytics-quick-create-vs.md).

Některé typy výstupů podporují [dělení](#partitioning)a [velikosti výstupních dávek](#output-batch-size) se liší při optimalizaci propustnosti. V následující tabulce jsou uvedeny funkce, které jsou podporovány pro každý typ výstupu:

| Typ výstupu | Dělení | Zabezpečení | 
|-------------|--------------|----------|
|[Azure Data Lake Storage Gen 1](azure-data-lake-storage-gen1-output.md)|Yes|Azure Active Directory uživatel </br> MSI|
|[Azure SQL Database](sql-database-output.md)|Ano, volitelné.|Ověření uživatele SQL </br> MSI (Preview)|
|[Azure Synapse Analytics](azure-synapse-analytics-output.md)|Yes|Ověření uživatele SQL|
|[Úložiště objektů BLOB a Azure Data Lake Gen 2](blob-storage-azure-data-lake-gen2-output.md)|Yes|MSI </br> Přístupový klíč|
|[Azure Event Hubs](event-hubs-output.md)|Ano, je potřeba nastavit sloupec klíče oddílu ve výstupní konfiguraci.|Přístupový klíč|
|[Power BI](power-bi-output.md)|No|Azure Active Directory uživatel </br> MSI|
|[Azure Table storage](table-storage-output.md)|Yes|Klíč účtu|
|[Fronty Azure Service Bus](service-bus-queues-output.md)|Yes|Přístupový klíč|
|[Azure Service Bus témata](service-bus-topics-output.md)|Yes|Přístupový klíč|
|[Azure Cosmos DB](azure-cosmos-db-output.md)|Yes|Přístupový klíč|
|[Azure Functions](azure-functions-output.md)|Yes|Přístupový klíč|

## <a name="partitioning"></a>Dělení

Stream Analytics podporuje oddíly pro všechny výstupy s výjimkou Power BI. Další informace o klíčích oddílů a počtu modulů pro zápis výstupu najdete v článku konkrétního typu výstupu, který vás zajímá. Všechny články v produkci jsou propojeny v předchozí části.  

Kromě toho pro pokročilejší optimalizaci oddílů je možné počet zapisovačů výstupu ovládat pomocí `INTO <partition count>` klauzule (viz v tématu) v dotazu [INTO](https://docs.microsoft.com/stream-analytics-query/into-azure-stream-analytics#into-shard-count), což může být užitečné při dosahování požadované topologie úlohy. Pokud váš výstupní adaptér není rozdělený na oddíly, způsobí nedostatek dat v jednom vstupním oddílu zpoždění až do doby, kdy se doba doručení uvolní. V takových případech se výstup sloučí do jediného zapisovače, což může způsobit kritické body ve vašem kanálu. Další informace o zásadách pozdního doručení najdete v tématu [Azure Stream Analytics požadavky na pořadí událostí](stream-analytics-out-of-order-and-late-events.md).

## <a name="output-batch-size"></a>Velikost výstupní dávky

Všechny výstupy podporují dávkování, ale explicitně se podporují jenom některé velikosti dávky. Azure Stream Analytics používá dávky variabilní velikosti pro zpracování událostí a zápis do výstupů. Modul Stream Analytics obvykle nezapisuje jednu zprávu najednou a využívá dávky k efektivitě. Když je vysoká míra příchozích i odchozích událostí, Stream Analytics využívá větší počet dávek. Když je přenosová rychlost nízká, používá menší dávky k udržení nízké latence.

## <a name="parquet-output-batching-window-properties"></a>Vlastnosti Parquet výstupního okna dávkování

Při použití Azure Resource Managerho nasazení šablony nebo REST API jsou dvě vlastnosti dávkového okna:

1. *timeWindow*

   Maximální doba čekání na dávku. Hodnota by měla být řetězec TimeSpan. Například "00:02:00" na dvě minuty. Po uplynutí této doby se dávka zapíše do výstupu i v případě, že požadavek na minimální řádky není splněn. Výchozí hodnota je 1 minuta a povolené maximum je 2 hodiny. Pokud váš výstup objektu BLOB má četnost vzorů cesty, doba čekání nesmí být vyšší než časový rozsah oddílu.

2. *sizeWindow*

   Počet minimálních řádků na dávku Pro Parquet Každá dávka vytvoří nový soubor. Aktuální výchozí hodnota je 2 000 řádků a povolené maximum je 10 000 řádků.

Tyto vlastnosti dávkového okna jsou podporovány pouze pomocí rozhraní API verze **2017-04-01-Preview**. Níže je uveden příklad datové části JSON pro REST API volání:

```json
"type": "stream",
      "serialization": {
        "type": "Parquet",
        "properties": {}
      },
      "timeWindow": "00:02:00",
      "sizeWindow": "2000",
      "datasource": {
        "type": "Microsoft.Storage/Blob",
        "properties": {
          "storageAccounts" : [
          {
            "accountName": "{accountName}",
            "accountKey": "{accountKey}",
          }
          ],
```

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
>
> [Rychlý start: Vytvoření úlohy Stream Analytics pomocí webu Azure Portal](stream-analytics-quick-create-portal.md)

<!--Link references-->
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.query.language.reference]: https://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: https://go.microsoft.com/fwlink/?LinkId=517301
