---
title: Dotazování dat v Azure Data Lake pomocí Azure Průzkumník dat
description: Naučte se, jak zadávat dotazy na data v Azure Data Lake pomocí Azure Průzkumník dat.
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 07/17/2019
ms.openlocfilehash: ef4dfc4370c71eac1978a6f3535b571a5e6009b5
ms.sourcegitcommit: 78ebf29ee6be84b415c558f43d34cbe1bcc0b38a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/12/2019
ms.locfileid: "68950143"
---
# <a name="query-data-in-azure-data-lake-using-azure-data-explorer-preview"></a>Dotazování dat v Azure Data Lake pomocí Azure Průzkumník dat (Preview)

Azure Data Lake Storage je vysoce škálovatelné a nákladově efektivní řešení Data Lake pro analýzu velkých objemů dat. Nabízí kombinaci vysoce výkonného systému souborů s velkým měřítkem a úsporami, takže urychluje dobu nutnou k získání potřebných informací. Řešení Data Lake Storage Gen2 rozšiřuje funkce Azure Blob Storage a je optimalizované pro analytické úlohy.
 
Azure Průzkumník dat se integruje s Azure Blob Storage a Azure Data Lake Storage Gen2 a poskytuje rychlý, uložený a indexovaný přístup k datům v Lake. Data můžete analyzovat a dotazovat v Lake bez předchozí ingestování do Azure Průzkumník dat. Můžete se také dotazovat napříč zpracovanými a nezpracovanými nativními daty Lake data současně.  

> [!TIP]
> Nejlepší výkon dotazů vyžaduje přijímání dat do Azure Průzkumník dat. Možnost dotazování dat v Azure Data Lake Storage Gen2 bez předchozí ingestování by se měla použít jenom pro historická data nebo data, která se dotazují jenom zřídka.
 
## <a name="optimize-query-performance-in-the-lake"></a>Optimalizace výkonu dotazů v Lake 

* Data oddílů pro zlepšení výkonu a optimalizovaného času dotazu.
* Zkomprimujte data pro zlepšení výkonu (gzip pro nejlepší výkon LZ4).
* Použijte Azure Blob Storage nebo Azure Data Lake Storage Gen2 se stejnou oblastí jako cluster Azure Průzkumník dat. 

## <a name="create-an-external-table"></a>Vytvoření externí tabulky

 > [!NOTE]
 > Aktuálně podporované účty úložiště jsou Blob Storage nebo Azure Data Lake Storage Gen2 Azure. Aktuálně podporované formáty dat jsou JSON, CSV, TSV a txt.

1. K vytvoření externí tabulky v Azure Průzkumník dat použijte příkaz.`.create external table` `.show`Další příkazy externí tabulky, například, `.drop`a `.alter` jsou zdokumentovány v [příkazech externích tabulek](/azure/kusto/management/externaltables).

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

1. Externí tabulka se zobrazí v levém podokně webového uživatelského rozhraní.

    ![externí tabulka ve webovém uživatelském rozhraní](media/data-lake-query-data/external-tables-web-ui.png)

### <a name="create-an-external-table-with-json-format"></a>Vytvoření externí tabulky ve formátu JSON

Externí tabulku můžete vytvořit ve formátu JSON. Další informace najdete v [](/azure/kusto/management/externaltables) tématu věnovaném externím tabulkovým příkazům

1. Pomocí příkazu vytvořte tabulku s názvem ExternalTableJson: `.create external table`

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
 
Chcete-li zadat dotaz na externí tabulku `external_table()` , použijte funkci a zadejte název tabulky jako argument funkce. Zbytek dotazu je standardní dotazovací jazyk Kusto.

```Kusto
external_table("ArchivedProducts") | take 100
```

> [!TIP]
> Technologie IntelliSense není v současné době pro dotazy na externí tabulku podporována.

### <a name="query-an-external-table-with-json-format"></a>Dotazování externí tabulky ve formátu JSON

Chcete-li zadat dotaz na externí tabulku s formátem `external_table()` JSON, použijte funkci a jako argumenty funkce zadejte název tabulky i mapování. V níže uvedeném dotazu, pokud není zadán parametr *Mapping* , bude použito mapování, které jste vytvořili dříve.

```kusto
external_table(‘ExternalTableJson’, ‘mappingName’)
```

## <a name="query-external-and-ingested-data-together"></a>Společné dotazování na externí a ingestná data

V rámci stejného dotazu můžete zadávat dotazy na externí tabulky a tabulky s příjmem dat. Vy [`join`](/azure/kusto/query/joinoperator) [nebo`union`](/azure/kusto/query/unionoperator) externí tabulka s dalšími daty z Azure Průzkumník dat, SQL servery nebo jiných zdrojů. [`let( ) statement`](/azure/kusto/query/letstatement) Použijte k přiřazení zkrácený název k odkazu na externí tabulku.

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

Přihlaste [https://dataexplorer.azure.com/clusters/help/databases/Samples](https://dataexplorer.azure.com/clusters/help/databases/Samples) se k pro dotazování externí tabulky *TaxiRides* . 

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

Tento dotaz používá dělení na oddíly, které optimalizuje čas a výkon dotazů. Dotaz filtruje sloupce děleného sloupce (pickup_datetime) a vrátí výsledky za několik sekund.

![vykreslit dotaz rozdělený na oddíly](media/data-lake-query-data/taxirides-with-partition.png)
  
Můžete napsat další dotazy ke spuštění na externí tabulce *TaxiRides* a získat další informace o datech. 

## <a name="next-steps"></a>Další kroky

Dotazování na data v Azure Data Lake pomocí Azure Průzkumník dat. Naučte se [vytvářet dotazy](write-queries.md) a odvodit další poznatky z vašich dat.
