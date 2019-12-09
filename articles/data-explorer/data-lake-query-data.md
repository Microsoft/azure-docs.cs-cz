---
title: Dotazování dat v Azure Data Lake pomocí Azure Průzkumník dat
description: Naučte se, jak zadávat dotazy na data v Azure Data Lake pomocí Azure Průzkumník dat.
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 07/17/2019
ms.openlocfilehash: d572e7f3fceaf2df8ad0ec684eaa421922389e71
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2019
ms.locfileid: "74922154"
---
# <a name="query-data-in-azure-data-lake-using-azure-data-explorer-preview"></a>Dotazování dat v Azure Data Lake pomocí Azure Průzkumník dat (Preview)

Azure Data Lake Storage je vysoce škálovatelné a nákladově efektivní řešení Data Lake pro analýzu velkých objemů dat. Nabízí kombinaci vysoce výkonného systému souborů s velkým měřítkem a úsporami, takže urychluje dobu nutnou k získání potřebných informací. Řešení Data Lake Storage Gen2 rozšiřuje funkce Azure Blob Storage a je optimalizované pro analytické úlohy.
 
Azure Průzkumník dat se integruje s Azure Blob Storage a Azure Data Lake Storage Gen2 a poskytuje rychlý, uložený a indexovaný přístup k datům v Lake. Data můžete analyzovat a dotazovat v Lake bez předchozí ingestování do Azure Průzkumník dat. Můžete se také dotazovat napříč zpracovanými a nezpracovanými nativními daty Lake data současně.  

> [!TIP]
> Nejlepší výkon dotazů vyžaduje přijímání dat do Azure Průzkumník dat. Možnost dotazování dat v Azure Data Lake Storage Gen2 bez předchozí ingestování by se měla použít jenom pro historická data nebo data, která se dotazují jenom zřídka. [Optimalizujte výkon dotazů v Lake](#optimize-your-query-performance) pro dosažení nejlepších výsledků.
 

## <a name="create-an-external-table"></a>Vytvoření externí tabulky

 > [!NOTE]
 > Aktuálně podporované účty úložiště jsou Blob Storage nebo Azure Data Lake Storage Gen2 Azure. Aktuálně podporované formáty dat jsou JSON, CSV, TSV a txt.

1. Pomocí příkazu `.create external table` můžete vytvořit externí tabulku ve službě Azure Průzkumník dat. Další příkazy externí tabulky, například `.show`, `.drop`a `.alter`, jsou zdokumentovány v [příkazech externích tabulek](/azure/kusto/management/externaltables).

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
    > * Větší výkon se očekává s podrobnějším rozdělením na oddíly. Například dotazy u externích tabulek s denními oddíly budou mít lepší výkon než dotazy s měsíčně rozdělenými tabulkami.
    > * Při definování externí tabulky s oddíly se očekává, že struktura úložiště bude shodná.
Pokud je tabulka definovaná například pomocí oddílu DateTime ve formátu RRRR/MM/DD (výchozí), cesta k souboru úložiště identifikátoru URI by měla být *container1/rrrr/mm/dd/all_exported_blobs*. 
    > * Pokud je externí tabulka rozdělená podle sloupce data a času, vždy do dotazu zahrňte časový filtr pro uzavřený rozsah (například dotaz-`ArchivedProducts | where Timestamp between (ago(1h) .. 10m)`-by měl být větší než tento (otevřený rozsah) jeden-`ArchivedProducts | where Timestamp > ago(1h)`). 

1. Externí tabulka se zobrazí v levém podokně webového uživatelského rozhraní.

    ![externí tabulka ve webovém uživatelském rozhraní](media/data-lake-query-data/external-tables-web-ui.png)

### <a name="create-an-external-table-with-json-format"></a>Vytvoření externí tabulky ve formátu JSON

Externí tabulku můžete vytvořit ve formátu JSON. Další informace najdete v tématu věnovaném [externím tabulkovým příkazům](/azure/kusto/management/externaltables)

1. Pomocí příkazu `.create external table` vytvořte tabulku s názvem *ExternalTableJson*:

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
 
1. Formát JSON vyžaduje druhý krok vytvoření mapování na sloupce, jak je znázorněno níže. V následujícím dotazu vytvořte specifické mapování JSON s názvem *Mapping*:

    ```kusto
    .create external table ExternalTableJson json mapping "mappingName" '[{ "column" : "rownumber", "datatype" : "int", "path" : "$.rownumber"},{ "column" : "rowguid", "path" : "$.rowguid" }]' 
    ```

### <a name="external-table-permissions"></a>Oprávnění externí tabulky
 
* Uživatel databáze může vytvořit externí tabulku. Autor tabulky se automaticky stal správcem tabulky.
* Správce clusteru, databáze nebo tabulky může upravit existující tabulku.
* Každý uživatel nebo čtenář databáze může zadat dotaz na externí tabulku.
 
## <a name="query-an-external-table"></a>Dotazování externí tabulky
 
Chcete-li zadat dotaz na externí tabulku, použijte funkci `external_table()` a zadejte název tabulky jako argument funkce. Zbytek dotazu je standardní dotazovací jazyk Kusto.

```Kusto
external_table("ArchivedProducts") | take 100
```

> [!TIP]
> Technologie IntelliSense není v současné době pro dotazy na externí tabulku podporována.

### <a name="query-an-external-table-with-json-format"></a>Dotazování externí tabulky ve formátu JSON

Chcete-li zadat dotaz na externí tabulku s formátem JSON, použijte funkci `external_table()` a jako argumenty funkce zadejte název tabulky i mapování. V níže uvedeném dotazu, pokud není zadán parametr *Mapping* , bude použito mapování, které jste vytvořili dříve.

```kusto
external_table(‘ExternalTableJson’, ‘mappingName’)
```

## <a name="query-external-and-ingested-data-together"></a>Společné dotazování na externí a ingestná data

V rámci stejného dotazu můžete zadávat dotazy na externí tabulky a tabulky s příjmem dat. Externí tabulku můžete [`join`](/azure/kusto/query/joinoperator) nebo [`union`](/azure/kusto/query/unionoperator) pomocí dalších dat ze služby Azure Průzkumník dat, serverů SQL nebo jiných zdrojů. K odkazu na externí tabulku můžete přiřadit zkrácený název pomocí [`let( ) statement`](/azure/kusto/query/letstatement) .

V následujícím příkladu je *produktem* příjmová datová tabulka a *ArchivedProducts* je externí tabulka, která obsahuje data v Azure Data Lake Storage Gen2:

```kusto
let T1 = external_table("ArchivedProducts") |  where TimeStamp > ago(100d);
let T = Products; //T is an internal table
T1 | join T on ProductId | take 10
```

## <a name="query-taxirides-external-table-in-the-help-cluster"></a>Dotaz na externí tabulku *TaxiRides* v clusteru Help

Ukázková datová sada *TaxiRides* obsahuje data taxislužby v New Yorku [, od NYC taxislužby a z Komise Limousine](https://www1.nyc.gov/site/tlc/about/tlc-trip-record-data.page).

### <a name="create-external-table-taxirides"></a>Vytvořit externí tabulku *TaxiRides* 

> [!NOTE]
> Tato část popisuje dotaz použitý k vytvoření externí tabulky *TaxiRides* v clusteru *help* . Vzhledem k tomu, že tato tabulka už je vytvořená, můžete tuto část přeskočit a provést [dotaz *TaxiRides* data externí tabulky](#query-taxirides-external-table-data). 

1. Následující dotaz byl použit k vytvoření externí tabulky *TaxiRides* v clusteru help. 

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
1. Výsledná tabulka byla vytvořena v clusteru *podpory* :

    ![Externí tabulka TaxiRides](media/data-lake-query-data/taxirides-external-table.png) 

### <a name="query-taxirides-external-table-data"></a>Data externí tabulky *TaxiRides* dotazu 

Přihlaste se k [https://dataexplorer.azure.com/clusters/help/databases/Samples](https://dataexplorer.azure.com/clusters/help/databases/Samples) pro dotaz na externí tabulku *TaxiRides* . 

#### <a name="query-taxirides-external-table-without-partitioning"></a>Dotaz na externí tabulku *TaxiRides* dotazu bez vytváření oddílů

[Spusťte tento dotaz](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAAx3LSwqAMAwFwL3gHYKreh1xL7F9YrCtElP84OEV9zM4DZo5DsZjhGt6PqWTgL1p6+qhvaTEKjeI/FqyuZbGiwJf63QAi9vEL2UbAhtMEv6jyAH6+VhS9jOr1dULfUgAm2cAAAA=) na externí tabulce *TaxiRides* , který zobrazí jezdí pro každý den v týdnu v celé sadě dat. 

```kusto
external_table("TaxiRides")
| summarize count() by dayofweek(pickup_datetime)
| render columnchart
```

Tento dotaz zobrazuje nejvytíženější den v týdnu. Vzhledem k tomu, že data nejsou rozdělená na oddíly, může tento dotaz trvat dlouhou dobu, než se výsledky vrátí (až několik minut).

![vykreslit dotaz, který není rozdělený na oddíly](media/data-lake-query-data/taxirides-no-partition.png)

#### <a name="query-taxirides-external-table-with-partitioning"></a>Externí tabulka TaxiRides dotazů s vytvářením oddílů 

[Spusťte tento dotaz](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA13NQQqDMBQE0L3gHT6ukkVF3fQepXv5SQYMNWmIP6ilh68WuinM6jHMYBPkyPMobGao5s6bv3mHpdF19aZ1QgYlbx8ljY4F4gPIQFYgkvqJGrr+eun6I5ralv58OP27t5QQOPsXiOyzRFGazE6WzSh7wtnIiA75uISdOEtdfQDLWmP+ogAAAA==) na externí tabulce *TaxiRides* , která zobrazuje taxislužby typy CAB (žluté nebo zelené), které se používají v lednu 2017. 

```kusto
external_table("TaxiRides")
| where pickup_datetime between (datetime(2017-01-01) .. datetime(2017-02-01))
| summarize count() by cab_type
| render piechart
```

Tento dotaz používá dělení na oddíly, které optimalizuje čas a výkon dotazů. Dotaz filtruje v rozděleném sloupci (pickup_datetime) a vrátí výsledky za několik sekund.

![vykreslit dotaz rozdělený na oddíly](media/data-lake-query-data/taxirides-with-partition.png)
  
Můžete napsat další dotazy ke spuštění na externí tabulce *TaxiRides* a získat další informace o datech. 

## <a name="optimize-your-query-performance"></a>Optimalizace výkonu dotazů

Optimalizujte výkon dotazů v Lake pomocí následujících osvědčených postupů pro dotazování externích dat. 
 
### <a name="data-format"></a>Formát dat
 
Pro analytické dotazy použijte sloupcový formát od:
* Číst lze pouze sloupce relevantní pro daný dotaz. 
* Techniky kódování sloupců můžou výrazně snížit velikost dat.  
Azure Průzkumník dat podporuje sloupcové formáty Parquet a ORC. Z důvodu optimalizované implementace je navržený formát Parquet. 
 
### <a name="azure-region"></a>Oblast Azure 
 
Zjistíte, že se externí data nacházejí ve stejné oblasti Azure jako cluster Azure Průzkumník dat. Tím se snižuje náklady a čas načítání dat.
 
### <a name="file-size"></a>Velikost souboru
 
Optimální velikost souboru je stovky MB (až 1 GB) na jeden soubor. Nepoužívejte mnoho malých souborů vyžadujících nepotřebnou režii, jako je například pomalejší proces výčtu souborů a omezené použití sloupcového formátu. Počítejte s tím, že počet souborů by měl být větší než počet jader procesoru v clusteru Azure Průzkumník dat. 
 
### <a name="compression"></a>Komprese
 
Pomocí komprese můžete omezit množství dat načítaných ze vzdáleného úložiště. V případě formátu Parquet použijte interní Parquet kompresní mechanizmus, který komprimuje skupiny sloupců samostatně, což vám umožní je číst samostatně. Chcete-li ověřit použití kompresního mechanismu, zkontrolujte, zda jsou soubory pojmenovány takto: "<filename>. gz. Parquet" nebo "<filename>. přichyceno. Parquet" na rozdíl od "<filename>. Parquet. gz"). 
 
### <a name="partitioning"></a>Dělení
 
Uspořádejte data pomocí oddílů "složka", které umožní dotazu přeskočit nedůležité cesty. Při plánování vytváření oddílů Zvažte velikost souborů a běžné filtry ve vašich dotazech, jako je například časové razítko nebo ID tenanta.
 
### <a name="vm-size"></a>Velikost virtuálního počítače
 
Vyberte skladové položky virtuálních počítačů s více jádry a vyšší propustností sítě (paměť je méně důležitá). Další informace najdete v tématu [Výběr správné SKU virtuálního počítače pro cluster Azure Průzkumník dat](manage-cluster-choose-sku.md).

## <a name="next-steps"></a>Další kroky

Dotazování na data v Azure Data Lake pomocí Azure Průzkumník dat. Naučte se [vytvářet dotazy](write-queries.md) a odvodit další poznatky z vašich dat.
