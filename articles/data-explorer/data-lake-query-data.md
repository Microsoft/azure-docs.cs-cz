---
title: Dotazování dat ve službě Azure Data Lake pomocí Průzkumníka dat služby Azure
description: Zjistěte, jak zadávat dotazy na data ve službě Azure Data Lake pomocí Průzkumníka dat služby Azure.
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/25/2019
ms.openlocfilehash: d6a58d144482e17f7e4b615134115d1da46af6f0
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/28/2019
ms.locfileid: "67453174"
---
# <a name="query-data-in-azure-data-lake-using-azure-data-explorer-preview"></a>Dotazování dat ve službě Azure Data Lake pomocí Průzkumníku dat Azure (Preview)

Azure Data Lake Storage je vysoce škálovatelný a hospodárný data lake řešení pro analýzu velkých objemů dat. Nabízí kombinaci vysoce výkonného systému souborů s velkým měřítkem a úsporami, takže urychluje dobu nutnou k získání potřebných informací. Řešení Data Lake Storage Gen2 rozšiřuje funkce Azure Blob Storage a je optimalizované pro analytické úlohy.
 
Průzkumník dat služby Azure se integruje s Azure Blob Storage a Azure Data Lake Storage Gen2, poskytuje rychlou a uložit do mezipaměti a indexovat přístup k datům do jezera. Můžete analyzovat a dotazovat data do jezera bez předchozího ingestování do Průzkumníku dat Azure. Můžete také zadávat dotazy v data lake nativní přijaté a uningested současně.  

> [!TIP]
> Dotazy vracely co nejlepší zpočátku vyžaduje 7denní zpracováním příjmu dat do Průzkumníku dat Azure. Možnost provádět dotazy na data v Azure Data Lake Storage Gen2 bez předchozího ingestování by měla sloužit pouze pro historická data nebo data, která je jen zřídka dotazován.
 
## <a name="optimize-query-performance-in-the-lake"></a>Optimalizaci výkonu dotazů do jezera 

* Dělení dat pro vylepšení výkonu a době zpracování dotazu optimalizované.
* Komprese dat za účelem vylepšení výkonu (pro nejlepší kompresi gzip, lz4 pro zajištění nejlepšího výkonu).
* Použití služby Azure Blob Storage nebo Azure Data Lake Storage Gen2 pomocí stejné oblasti jako váš cluster Průzkumník dat Azure. 

## <a name="create-an-external-table"></a>Vytvoření externí tabulky

1. Použití `.create external table` příkaz pro vytvoření externí tabulky v Průzkumníku dat Azure. Příkazy, jako další externí tabulky `.show`, `.drop`, a `.alter` jsou dokumentovány v článku [externí tabulky příkazů](/azure/kusto/management/externaltables).

    ```Kusto
    .create external table ArchivedProducts(
    Timestamp:datetime,
    ProductId:long, ProductDescription:string) 
    kind=blob
    partition by bin(Timestamp, 1d) 
    dataformat=csv (h@'http://storageaccount.blob.core.windows.net/container1;secretKey') 
    with (compressed = true)  
    ```

    Tento dotaz vytvoří denního dělení *container1/yyyy/MM/dd/all_exported_blobs.csv*. Očekává se zvýšení výkonu s podrobnější dělení. Dotazy na externí tabulky s oddíly denní, jako je ten výše, bude mít například vyšší výkon než tyto dotazy s měsíční dělené tabulky.

    > [!NOTE]
    > Azure Blob Storage nebo Azure Data Lake Storage Gen2 jsou účty úložiště aktuálně podporuje. Data aktuálně podporované formáty jsou csv, tsv a txt.

1. Externí tabulky je viditelný v levém podokně webového uživatelského rozhraní

    ![externí tabulky ve webovém uživatelském rozhraní](media/data-lake-query-data/external-tables-web-ui.png)
 
### <a name="external-table-permissions"></a>Oprávnění externí tabulky
 
* Databázový uživatel může vytvoření externí tabulky. Tvůrce tabulky automaticky stává správcem tabulky.
* Cluster, databáze nebo tabulky správce můžete upravit existující tabulku.
* Každý uživatel databáze nebo Čtenář se můžete dotazovat externí tabulky.
 
## <a name="query-an-external-table"></a>Dotaz externí tabulky
 
Dotaz externí tabulky, použijte `external_table()` pracovat a zadejte název tabulky jako argument funkce. Zbývající část dotazu je standardní Kusto dotazovací jazyk.

```Kusto
external_table("ArchivedProducts") | take 100
```

> [!TIP]
> Technologie IntelliSense není aktuálně podporována na dotazy na externí tabulky.

## <a name="query-external-and-ingested-data-together"></a>Dotazování dat externí a přijaté dohromady

Externí tabulky a tabulky přijatých dat v rámci stejného dotazu se můžete dotazovat. Můžete [ `join` ](/azure/kusto/query/joinoperator) nebo [ `union` ](/azure/kusto/query/unionoperator) externí tabulky s dalšími daty z Průzkumníku dat Azure, SQL servery nebo jiné zdroje. Použití [ `let( ) statement` ](/azure/kusto/query/letstatement) přiřadit zkrácený název k odkazu na externí tabulky.

V následujícím příkladu *produkty* je tabulka přijatých dat a *ArchivedProducts* externí tabulky, který obsahuje data v Azure Data Lake Storage Gen2 je:

```kusto
let T1 = external_table("ArchivedProducts") |  where TimeStamp > ago(100d);
let T = Products; //T is an internal table
T1 | join T on ProductId | take 10
```

## <a name="query-taxirides-external-table-in-the-help-cluster"></a>Dotaz *TaxiRides* externí tabulky v clusteru help

*TaxiRides* ukázkové datové sadě obsahuje data taxislužby města New York z [NYC taxislužby a Limousine Komise](https://www1.nyc.gov/site/tlc/about/tlc-trip-record-data.page).

### <a name="create-external-table-taxirides"></a>Vytvoření externí tabulky *TaxiRides* 

> [!NOTE]
> Tato část popisuje dotaz použitý k vytvoření *TaxiRides* externí tabulky v *pomáhají* clusteru. Protože byl vytvořen v této tabulce můžete tuto část přeskočit a provádět [dotazu *TaxiRides* externí tabulky dat](#query-taxirides-external-table-data). 

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
    h@'https://externalkustosamples.blob.core.windows.net/taxiridesbyday?st=2019-06-18T14%3A59%3A00Z&se=2029-06-19T14%3A59%3A00Z&sp=rl&sv=2016-05-31&sr=c&sig=yEaO%2BrzFHzAq7lvd4d9PeQ%2BTi3AWnho8Rn8hGU0X30M%3D'
    )
    ```
1. Výsledná tabulka byla vytvořena v *pomáhají* clusteru:

    ![TaxiRides externí tabulky](media/data-lake-query-data/taxirides-external-table.png) 

### <a name="query-taxirides-external-table-data"></a>Dotaz *TaxiRides* externí tabulky dat 

Přihlaste se k [ https://dataexplorer.azure.com/clusters/help/databases/Samples ](https://dataexplorer.azure.com/clusters/help/databases/Samples) k dotazu *TaxiRides* externí tabulky. 

#### <a name="query-taxirides-external-table-without-partitioning"></a>Dotaz *TaxiRides* externí tabulky bez vytváření oddílů

[Spuštění tohoto dotazu](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAAx3LSwqAMAwFwL3gHYKreh1xL7F9YrCtElP84OEV9zM4DZo5DsZjhGt6PqWTgL1p6+qhvaTEKjeI/FqyuZbGiwJf63QAi9vEL2UbAhtMEv6jyAH6+VhS9jOr1dULfUgAm2cAAAA=) na externí tabulky *TaxiRides* ke znázornění lyžovačku za každý den v týdnu, napříč celou datovou sadu. 

```kusto
external_table("TaxiRides")
| summarize count() by dayofweek(pickup_datetime)
| render columnchart
```

Tento dotaz zobrazí za nejvytíženější den v týdnu. Protože data není rozdělena na oddíly, tento dotaz může trvat dlouhou dobu vrátit výsledky (maximálně několik minut).

![vykreslení dotazů bez oddílů](media/data-lake-query-data/taxirides-no-partition.png)

#### <a name="query-taxirides-external-table-with-partitioning"></a>Dotaz TaxiRides externí tabulky s dělení 

[Spuštění tohoto dotazu](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA13NQQqDMBQE0L3gHT6ukkVF3fQepXv5SQYMNWmIP6ilh68WuinM6jHMYBPkyPMobGao5s6bv3mHpdF19aZ1QgYlbx8ljY4F4gPIQFYgkvqJGrr+eun6I5ralv58OP27t5QQOPsXiOyzRFGazE6WzSh7wtnIiA75uISdOEtdfQDLWmP+ogAAAA==) na externí tabulky *TaxiRides* zobrazení taxislužby cab typů (žlutě nebo zeleně) používá v lednu 2017. 

```kusto
external_table("TaxiRides")
| where pickup_datetime between (datetime(2017-01-01) .. datetime(2017-02-01))
| summarize count() by cab_type
| render piechart
```

Tento dotaz používá dělení, který optimalizuje čas dotazu a výkonu. Dotaz vyfiltruje na dělené sloupec (pickup_datetime) a vrátí výsledky za pár sekund.

![vykreslení oddílů dotazu](media/data-lake-query-data/taxirides-with-partition.png)
  
Můžete napsat další dotazy ke spuštění na externí tabulky *TaxiRides* a další informace o datech. 

## <a name="next-steps"></a>Další postup

Dotazování na data ve službě Azure Data Lake pomocí Průzkumníka dat služby Azure. Zjistěte, jak [psát dotazy](write-queries.md) a získávat z nich další přehledy z vašich dat.