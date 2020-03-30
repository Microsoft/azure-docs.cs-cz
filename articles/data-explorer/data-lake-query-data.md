---
title: Dotazování na data v Azure Data Lake pomocí Azure Data Exploreru
description: Zjistěte, jak se dotazovat na data v Azure Data Lake pomocí Azure Data Exploreru.
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 07/17/2019
ms.openlocfilehash: 8240b1a01aa39e53b9ae41f73543ccf9774290b2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77161745"
---
# <a name="query-data-in-azure-data-lake-using-azure-data-explorer"></a>Dotazování na data v Azure Data Lake pomocí Azure Data Exploreru

Azure Data Lake Storage je vysoce škálovatelné a nákladově efektivní řešení datových jezer pro analýzu velkých objemů dat. Kombinuje sílu vysoce výkonného souborového systému s obrovským rozsahem a hospodárností, což vám pomůže urychlit váš čas na přehled. Data Lake Storage Gen2 rozšiřuje možnosti úložiště objektů blob Azure a je optimalizované pro analytické úlohy.
 
Azure Data Explorer se integruje s Azure Blob Storage a Azure Data Lake Storage (Gen1 a Gen2), který poskytuje rychlý, uložený a indexovaný přístup k datům v jezeře. Můžete analyzovat a dotazovat data v jezeře bez předchozího přijetí do Průzkumníka dat Azure. Můžete také dotaz ovat napříč ingestovanými a neingestovanými nativními daty jezera současně.  

> [!TIP]
> Nejlepší výkon dotazu vyžaduje přijetí dat do Průzkumníka dat Azure. Možnost dotazování externích dat bez předchozího požití by měla být použita pouze pro historická data nebo data, která jsou dotazována jen zřídka. [Optimalizujte výkon dotazů v jezeře](#optimize-your-query-performance) pro dosažení nejlepších výsledků.
 

## <a name="create-an-external-table"></a>Vytvoření externí tabulky

 > [!NOTE]
 > Aktuálně podporované účty úložiště jsou Azure Blob Storage nebo Azure Data Lake Storage (Gen1 a Gen2).

1. Pomocí `.create external table` příkazu vytvořte externí tabulku v Průzkumníku dat Azure. Další příkazy externí `.show`tabulky, `.drop` `.alter` například , a jsou popsány v [příkazech externí tabulky](/azure/kusto/management/externaltables).

    ```Kusto
    .create external table ArchivedProducts(
    Timestamp:datetime,
    ProductId:long, ProductDescription:string) 
    kind=blob
    partition by bin(Timestamp, 1d) 
    dataformat=csv (h@'http://storageaccount.blob.core.windows.net/container1;secretKey') 
    with (compressed = true)  
    ```
    
    > [!NOTE]
    > * Zvýšený výkon se očekává s podrobnější dělení. Například dotazy přes externí tabulky s denní oddíly, bude mít lepší výkon než ty dotazy s měsíční rozdělené tabulky.
    > * Při definování externí tabulky s oddíly, struktura úložiště se očekává, že bude totožný.
Pokud je například tabulka definována oddílem DateTime ve formátu yyyy/MM/dd (výchozí), měla by být cesta k souboru úložiště URI *kontejner1/yyyy/MM/dd/all_exported_blobs*. 
    > * Pokud je externí tabulka rozdělena podle sloupce datetime, vždy v dotazu zahrňte časový `ArchivedProducts | where Timestamp between (ago(1h) .. 10m)` filtr pro uzavřenou oblast (například dotaz - - by měl fungovat lépe než tento (otevřený rozsah) jeden - `ArchivedProducts | where Timestamp > ago(1h)` ). 
    > * Všechny [podporované formáty ingestování](ingest-data-overview.md#supported-data-formats) lze dotazovat pomocí externích tabulek.

1. Externí tabulka je viditelná v levém podokně webového uživatelského uživatelského nastavení.

    ![externí tabulka ve webovém uživatelském okně](media/data-lake-query-data/external-tables-web-ui.png)

### <a name="create-an-external-table-with-json-format"></a>Vytvoření externí tabulky s formátem json

Můžete vytvořit externí tabulku s formátem json. Další informace naleznete [v tématu Příkazy externí tabulky](/azure/kusto/management/externaltables)

1. Příkaz `.create external table` slouží k vytvoření tabulky s názvem *ExternalTableJson*:

    ```kusto
    .create external table ExternalTableJson (rownumber:int, rowguid:guid) 
    kind=blob
    dataformat=json
    ( 
       h@'http://storageaccount.blob.core.windows.net/container1;secretKey'
    )
    with 
    (
       docstring = "Docs",
       folder = "ExternalTables",
       namePrefix="Prefix"
    ) 
    ```
 
1. Formát Json vyžaduje druhý krok vytváření mapování na sloupce, jak je znázorněno níže. V následujícím dotazu vytvořte konkrétní mapování json s názvem *mappingName*:

    ```kusto
    .create external table ExternalTableJson json mapping "mappingName" '[{ "column" : "rownumber", "datatype" : "int", "path" : "$.rownumber"},{ "column" : "rowguid", "path" : "$.rowguid" }]' 
    ```

### <a name="external-table-permissions"></a>Oprávnění externí tabulky
 
* Uživatel databáze může vytvořit externí tabulku. Tvůrce tabulky se automaticky stane správcem tabulky.
* Správce clusteru, databáze nebo tabulky může upravit existující tabulku.
* Každý uživatel databáze nebo čtenář může dotazovat externí tabulku.
 
## <a name="query-an-external-table"></a>Dotaz na externí tabulku
 
Chcete-li zadat dotaz `external_table()` na externí tabulku, použijte funkci a zadejte název tabulky jako argument funkce. Zbytek dotazu je standardní kusto dotazovací jazyk.

```Kusto
external_table("ArchivedProducts") | take 100
```

> [!TIP]
> Technologie Intellisense není aktuálně podporována v externích dotazech v tabulce.

### <a name="query-an-external-table-with-json-format"></a>Dotaz na externí tabulku ve formátu json

Chcete-li zadat dotaz na externí tabulku `external_table()` s formátem json, použijte funkci a jako argumenty funkce zadejte název tabulky i název mapování. V níže uvedeném dotazu, pokud *mappingName* není zadán, mapování, které jste dříve vytvořili, bude použito.

```kusto
external_table(‘ExternalTableJson’, ‘mappingName’)
```

## <a name="query-external-and-ingested-data-together"></a>Dotaz na externí a ingemovaná data společně

V rámci stejného dotazu můžete dotazovat jak externí tabulky, tak tabulky ingestovaných dat. [`join`](/azure/kusto/query/joinoperator) Vy [`union`](/azure/kusto/query/unionoperator) nebo externí tabulka s dalšími daty z Průzkumníka dat Azure, serverů SQL nebo jiných zdrojů. Slouží [`let( ) statement`](/azure/kusto/query/letstatement) k přiřazení zkráceného názvu externímu odkazu na tabulku.

V níže uvedeném příkladu je *produkty* ingestované tabulky dat a *ArchivedProducts* externí tabulka, která obsahuje data v Azure Data Lake Storage Gen2:

```kusto
let T1 = external_table("ArchivedProducts") |  where TimeStamp > ago(100d);
let T = Products; //T is an internal table
T1 | join T on ProductId | take 10
```

## <a name="query-taxirides-external-table-in-the-help-cluster"></a>Externí tabulka *Query TaxiRides* v clusteru nápovědy

Ukázková datová sada *TaxiRides* obsahuje údaje o taxi službách v New Yorku od [společnosti NYC Taxi and Limousine Commission](https://www1.nyc.gov/site/tlc/about/tlc-trip-record-data.page).

### <a name="create-external-table-taxirides"></a>Vytvořit externí tabulku *TaxiRides* 

> [!NOTE]
> Tato část znázorňuje dotaz použitý k vytvoření externí tabulky *TaxiRides* v clusteru *nápovědy.* Vzhledem k tomu, že tato tabulka již byla vytvořena, můžete tuto část přeskočit a provést [dotaz *TaxiRides* externí data tabulky](#query-taxirides-external-table-data). 

1. Následující dotaz byl použit k vytvoření externí tabulky *TaxiRides* v clusteru nápovědy. 

    ```kusto
    .create external table TaxiRides
    (
    trip_id: long,
    vendor_id: string, 
    pickup_datetime: datetime,
    dropoff_datetime: datetime,
    store_and_fwd_flag: string,
    rate_code_id: int,
    pickup_longitude: real,
    pickup_latitude: real,
    dropoff_longitude: real,
    dropoff_latitude: real,
    passenger_count: int,
    trip_distance: real,
    fare_amount: real,
    extra: real,
    mta_tax: real,
    tip_amount: real,
    tolls_amount: real,
    ehail_fee: real,
    improvement_surcharge: real,
    total_amount: real,
    payment_type: string,
    trip_type: int,
    pickup: string,
    dropoff: string,
    cab_type: string,
    precipitation: int,
    snow_depth: int,
    snowfall: int,
    max_temperature: int,
    min_temperature: int,
    average_wind_speed: int,
    pickup_nyct2010_gid: int,
    pickup_ctlabel: string,
    pickup_borocode: int,
    pickup_boroname: string,
    pickup_ct2010: string,
    pickup_boroct2010: string,
    pickup_cdeligibil: string,
    pickup_ntacode: string,
    pickup_ntaname: string,
    pickup_puma: string,
    dropoff_nyct2010_gid: int,
    dropoff_ctlabel: string,
    dropoff_borocode: int,
    dropoff_boroname: string,
    dropoff_ct2010: string,
    dropoff_boroct2010: string,
    dropoff_cdeligibil: string,
    dropoff_ntacode: string,
    dropoff_ntaname: string,
    dropoff_puma: string
    )
    kind=blob 
    partition by bin(pickup_datetime, 1d)
    dataformat=csv
    ( 
        h@'http://storageaccount.blob.core.windows.net/container1;secretKey''
    )
    ```
1. Výsledná tabulka byla vytvořena v clusteru *nápovědy:*

    ![TaxiRides externí stůl](media/data-lake-query-data/taxirides-external-table.png) 

### <a name="query-taxirides-external-table-data"></a>Dotaz *TaxiRides* externí data tabulky 

Přihlaste [https://dataexplorer.azure.com/clusters/help/databases/Samples](https://dataexplorer.azure.com/clusters/help/databases/Samples) se k dotazu na externí tabulku *TaxiRides.* 

#### <a name="query-taxirides-external-table-without-partitioning"></a>Externí tabulka *Query TaxiRides* bez dělení

[Spusťte tento dotaz](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAAx3LSwqAMAwFwL3gHYKreh1xL7F9YrCtElP84OEV9zM4DZo5DsZjhGt6PqWTgL1p6+qhvaTEKjeI/FqyuZbGiwJf63QAi9vEL2UbAhtMEv6jyAH6+VhS9jOr1dULfUgAm2cAAAA=) v externí tabulce *TaxiRides,* abyste zobrazili jízdy pro každý den v týdnu v celé sadě dat. 

```kusto
external_table("TaxiRides")
| summarize count() by dayofweek(pickup_datetime)
| render columnchart
```

Tento dotaz zobrazuje nejrušnější den v týdnu. Vzhledem k tomu, že data nejsou rozdělena na oddíly, může tento dotaz trvat dlouhou dobu vrátit výsledky (až několik minut).

![vykreslení nerozděleného dotazu](media/data-lake-query-data/taxirides-no-partition.png)

#### <a name="query-taxirides-external-table-with-partitioning"></a>Externí tabulka Query TaxiRides s dělením 

[Spusťte tento dotaz](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA13NQQqDMBQE0L3gHT6ukkVF3fQepXv5SQYMNWmIP6ilh68WuinM6jHMYBPkyPMobGao5s6bv3mHpdF19aZ1QgYlbx8ljY4F4gPIQFYgkvqJGrr+eun6I5ralv58OP27t5QQOPsXiOyzRFGazE6WzSh7wtnIiA75uISdOEtdfQDLWmP+ogAAAA==) v externí tabulce *TaxiRides* zobrazující typy taxi (žluté nebo zelené) používané v lednu 2017. 

```kusto
external_table("TaxiRides")
| where pickup_datetime between (datetime(2017-01-01) .. datetime(2017-02-01))
| summarize count() by cab_type
| render piechart
```

Tento dotaz používá dělení, které optimalizuje čas dotazu a výkon. Dotaz filtruje na dělený sloupec (pickup_datetime) a vrátí výsledky během několika sekund.

![vykreslení rozděleného dotazu](media/data-lake-query-data/taxirides-with-partition.png)
  
Můžete napsat další dotazy ke spuštění na externí tabulce *TaxiRides* a dozvědět se více o datech. 

## <a name="optimize-your-query-performance"></a>Optimalizace výkonu dotazu

Optimalizujte výkon dotazů v jezeře pomocí následujících doporučených postupů pro dotazování externích dat. 
 
### <a name="data-format"></a>Formát dat
 
Pro analytické dotazy použijte sloupcový formát, protože:
* Lze číst pouze sloupce relevantní pro dotaz. 
* Techniky kódování sloupců mohou výrazně zmenšit velikost dat.  
Azure Data Explorer podporuje parkety a ORC sloupcové formáty. Formát parket je navržen z důvodu optimalizované implementace. 
 
### <a name="azure-region"></a>Oblast Azure 
 
Zkontrolujte, že externí data jsou umístěna ve stejné oblasti Azure jako cluster Azure Data Explorer. To snižuje náklady a čas načítání dat.
 
### <a name="file-size"></a>Velikost souboru
 
Optimální velikost souboru je stovky Mb (až 1 Gb) na soubor. Vyhněte se mnoha malým souborům, které vyžadují nepotřebnou režii, jako je pomalejší proces výčtu souborů a omezené použití sloupcového formátu. Všimněte si, že počet souborů by měl být větší než počet jader procesoru v clusteru Průzkumníka dat Azure. 
 
### <a name="compression"></a>Komprese
 
Pomocí komprese můžete snížit množství dat načtených ze vzdáleného úložiště. Pro formát parket použijte vnitřní kompresní mechanismus parket, který komprimuje skupiny sloupců samostatně, což vám umožní číst samostatně. Chcete-li ověřit použití kompresního mechanismu, zkontrolujte, zda jsou soubory pojmenovány takto: ".gz.parquet"<filename>nebo ".snappy.parquet"<filename>na rozdíl od ".parketquet.gz").<filename> 
 
### <a name="partitioning"></a>Dělení
 
Uspořádejte data pomocí oddílů "složka", které umožňují dotazpřeskočit irelevantní cesty. Při plánování dělení zvážit velikost souboru a běžné filtry v dotazech, jako je časové razítko nebo ID klienta.
 
### <a name="vm-size"></a>Velikost virtuálního počítače
 
Vyberte virtuální chudinské jednotky s více jádry a vyšší propustnost sítě (paměť je méně důležitá). Další informace najdete [v tématu Vyberte správnou skladovou položku virtuálního počítače pro cluster Průzkumníka dat Azure](manage-cluster-choose-sku.md).

## <a name="next-steps"></a>Další kroky

Dotaz ujte data v Datovém jezírku Azure pomocí Azure Data Exploreru. Naučte se [psát dotazy](write-queries.md) a odvodit další přehledy z vašich dat.
