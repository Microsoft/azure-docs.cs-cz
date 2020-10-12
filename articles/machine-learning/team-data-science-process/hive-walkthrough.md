---
title: Zkoumání dat v clusteru Hadoop – vědecký proces týmových dat
description: Použití vědeckého procesu týmového zpracování dat pro kompletní scénář a využití clusteru HDInsight Hadoop k sestavení a nasazení modelu.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 991e81c46a0cd6c587ac3366b63ba4da6a07f7e7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91336509"
---
# <a name="the-team-data-science-process-in-action-use-azure-hdinsight-hadoop-clusters"></a>Vědecké zpracování týmových dat v akci: použití clusterů Azure HDInsight Hadoop
V tomto návodu použijeme [TDSP (Team data Sciences)](overview.md) v uceleném scénáři. Pro ukládání, prozkoumávání a inženýrské údaje z veřejně dostupné datové sady [taxislužby pro NYC](https://www.andresmh.com/nyctaxitrips/) a pro data ukázek používáme [cluster Azure HDInsight Hadoop](https://azure.microsoft.com/services/hdinsight/) . Pro zpracování binárních a regresních klasifikací a regresních prediktivních úkolů sestavíme modely dat s Azure Machine Learning. 

Návod, který ukazuje, jak zpracovat větší datovou sadu, najdete v tématu věnovaném [týmovému zpracování týmových dat – použití Azure HDInsight Hadoop clusterů na 1 TB datové sadě](hive-criteo-walkthrough.md).

Pomocí poznámkového bloku IPython můžete také provádět úkoly uvedené v návodu, který používá datovou sadu 1 – TB. Další informace najdete v [návodu k Criteo pomocí připojení k podregistru ODBC](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/iPythonNotebooks/machine-Learning-data-science-process-hive-walkthrough-criteo.ipynb).

## <a name="nyc-taxi-trips-dataset-description"></a><a name="dataset"></a>NYC taxislužby TRIPS – popis datové sady
Data o cestách NYC taxislužby jsou přibližně 20 GB komprimovaných souborů hodnot oddělených čárkami (CSV 48 GB nekomprimovaných). Má více než 173 000 000 jednotlivých cest a zahrnuje tarify placené za každou cestu. Každý záznam na cestách zahrnuje umístění a dobu dropoff a čas, informace o licencích pro jméno a Medallion (jedinečné ID taxislužby). Data se týkají všech cest v roce 2013 a jsou k dispozici v následujících dvou datových sadách pro každý měsíc:

- Trip_data soubory CSV obsahují podrobnosti o cestách: počet cestujících, výběr a dropoff body, doba trvání cesty a délka cesty. Tady je několik ukázkových záznamů:

  `medallion,hack_license,vendor_id,rate_code,store_and_fwd_flag,pickup_datetime,dropoff_datetime,passenger_count,trip_time_in_secs,trip_distance,pickup_longitude,pickup_latitude,dropoff_longitude,dropoff_latitude`

  `89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,1,N,2013-01-01 15:11:48,2013-01-01 15:18:10,4,382,1.00,-73.978165,40.757977,-73.989838,40.751171`

  `0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-06 00:18:35,2013-01-06 00:22:54,1,259,1.50,-74.006683,40.731781,-73.994499,40.75066`

  `0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-05 18:49:41,2013-01-05 18:54:23,1,282,1.10,-74.004707,40.73777,-74.009834,40.726002`

  `DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:54:15,2013-01-07 23:58:20,2,244,.70,-73.974602,40.759945,-73.984734,40.759388`

  `DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:25:03,2013-01-07 23:34:24,1,560,2.10,-73.97625,40.748528,-74.002586,40.747868`

- Soubory trip_fare CSV obsahují podrobnosti o tarifu placeného za každou cestu: typ platby, částka tarifů, příplatek a daně, tipy a mýtné a celková placená částka. Tady je několik ukázkových záznamů:

  `medallion, hack_license, vendor_id, pickup_datetime, payment_type, fare_amount, surcharge, mta_tax, tip_amount, tolls_amount, total_amount`

  `89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,2013-01-01 15:11:48,CSH,6.5,0,0.5,0,0,7`

  `0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-06 00:18:35,CSH,6,0.5,0.5,0,0,7`

  `0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-05 18:49:41,CSH,5.5,1,0.5,0,0,7`

  `DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:54:15,CSH,5,0.5,0.5,0,0,6`

  `DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:25:03,CSH,9.5,0.5,0.5,0,0,10.5`

Jedinečný klíč pro připojení k \_ datům cest a služební \_ tarif se skládá z těchto polí: Medallion, napadená \_ licence a data a času vyzvednutí \_ . Chcete-li získat všechny podrobnosti týkající se konkrétní cesty, stačí se připojit k těmto třem klíčům.

## <a name="examples-of-prediction-tasks"></a><a name="mltasks"></a>Příklady úkolů předpovědi
Určete druh předpovědi, který chcete vytvořit na základě analýzy dat, abyste mohli vyjasnit požadované úlohy procesu. Tady jsou tři příklady problémů s předpovědí, které řešíme v tomto návodu, a to vše na základě * \_ výše uvedené hodnoty Tip*:

- **Binární klasifikace**: předpověď bez ohledu na to, zda byl pro cestu zaplacen Tip. To znamená, že * \_ hodnota tipu* , která je větší než $0, je pozitivním příkladem, zatímco * \_ hodnota tipu* $0 je negativním příkladem.

  - Třída 0: tip_amount = $0
  - Třída 1: tip_amount > $0

- **Klasifikace s více třídami**: předpověď rozsahu částek v tipu placených pro danou cestu. * \_ Velikost tipu* rozdělíme na pět tříd:

  - Třída 0: tip_amount = $0
  - Třída 1: tip_amount > $0 a tip_amount <= $5
  - Třída 2: tip_amount > $5 a tip_amount <= $10
  - Třída 3: tip_amount > $10 a tip_amount <= $20
  - Třída 4: tip_amount > $20

- **Regresní úloha**: předpověď množství tipu placeného pro cestu.  

## <a name="set-up-an-hdinsight-hadoop-cluster-for-advanced-analytics"></a><a name="setup"></a>Nastavení clusteru HDInsight Hadoop pro pokročilou analýzu
> [!NOTE]
> Obvykle se jedná o úlohu správce.
> 
> 

Prostředí Azure pro pokročilou analýzu, která využívá cluster HDInsight, můžete nastavit v třech krocích:

1. [Vytvoření účtu úložiště](../../storage/common/storage-account-create.md): Tento účet úložiště se používá k ukládání dat v úložišti objektů BLOB v Azure. Data používaná v clusterech HDInsight se taky nacházejí v tomto umístění.
2. [Přizpůsobte Azure HDInsight Hadoop clusterů pro proces a technologii pokročilé analýzy](customize-hadoop-cluster.md). Tento krok vytvoří cluster systému Hadoop HDInsight s 64 Anaconda Python 2,7 nainstalovaný na všech uzlech. Existují dva důležité kroky, které byste si měli pamatovat při přizpůsobování clusteru HDInsight.
   
   * Nezapomeňte propojit účet úložiště vytvořený v kroku 1 s clusterem HDInsight při jeho vytváření. Tento účet úložiště přistupuje k datům zpracovávaným v rámci clusteru.
   * Po vytvoření clusteru Povolte vzdálený přístup k hlavnímu uzlu clusteru. Přejděte na kartu **Konfigurace** a vyberte **Povolit vzdálenou**. Tento krok určuje přihlašovací údaje uživatele používané pro vzdálené přihlášení.
3. [Vytvoření pracovního prostoru Azure Machine Learning](../classic/create-workspace.md): pomocí tohoto pracovního prostoru sestavíte modely strojového učení. Tato úloha se řeší po dokončení počátečního zkoumání dat a vzorkování dolů pomocí clusteru HDInsight.

## <a name="get-the-data-from-a-public-source"></a><a name="getdata"></a>Získat data z veřejného zdroje
> [!NOTE]
> Obvykle se jedná o úlohu správce.
> 
> 

Pokud chcete zkopírovat datovou sadu [NYC taxislužby TRIPS](https://www.andresmh.com/nyctaxitrips/) na váš počítač ze svého veřejného umístění, použijte kteroukoli z metod popsaných v tématu [přesun dat do a z úložiště objektů BLOB v Azure](move-azure-blob.md).

Tady popisujeme, jak použít AzCopy k přenosu souborů obsahujících data. Pokud chcete stáhnout a nainstalovat AzCopy, postupujte podle pokynů v tématu [Začínáme s nástrojem příkazového řádku AzCopy](../../storage/common/storage-use-azcopy.md).

1. V okně příkazového řádku spusťte následující příkazy AzCopy a nahraďte *\<path_to_data_folder>* požadované místo:

    ```console
    "C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:https://nyctaxitrips.blob.core.windows.net/data /Dest:<path_to_data_folder> /S
    ```

1. Až se kopie dokončí, zobrazí se ve vybrané složce s daty celkem 24 souborů zip. Rozbalte stažené soubory do stejného adresáře na místním počítači. Poznamenejte si složku, do které se nacházejí nekomprimované soubory. Tato složka je označována jako *\<path\_to\_unzipped_data\_files\>* v následujícím příkladu.

## <a name="upload-the-data-to-the-default-container-of-the-hdinsight-hadoop-cluster"></a><a name="upload"></a>Nahrajte data do výchozího kontejneru clusteru HDInsight Hadoop.
> [!NOTE]
> Obvykle se jedná o úlohu správce.
> 
> 

V následujících příkazech AzCopy nahraďte následující parametry skutečnými hodnotami, které jste zadali při vytváření clusteru Hadoop, a rozzipovává datových souborů.

* ***\<path_to_data_folder>*** Adresář (spolu s cestou) na počítači, který obsahuje soubory s nekomprimovanými daty.  
* ***\<storage account name of Hadoop cluster>*** Účet úložiště přidružený k vašemu clusteru HDInsight.
* ***\<default container of Hadoop cluster>*** Výchozí kontejner používaný clusterem. Název výchozího kontejneru má obvykle stejný název jako samotný cluster. Pokud má například cluster název "abc123.azurehdinsight.net", výchozí kontejner je abc123.
* ***\<storage account key>*** Klíč pro účet úložiště používaný clusterem

Z příkazového řádku nebo okna Windows PowerShellu spusťte následující dva příkazy AzCopy.

Tento příkaz nahraje data pro cestu do adresáře ***nyctaxitripraw*** ve výchozím kontejneru clusteru Hadoop.

```console
"C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:<path_to_unzipped_data_files> /Dest:https://<storage account name of Hadoop cluster>.blob.core.windows.net/<default container of Hadoop cluster>/nyctaxitripraw /DestKey:<storage account key> /S /Pattern:trip_data_*.csv
```

Tento příkaz nahraje data tarifů do adresáře ***nyctaxifareraw*** ve výchozím kontejneru clusteru Hadoop.

```console
"C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:<path_to_unzipped_data_files> /Dest:https://<storage account name of Hadoop cluster>.blob.core.windows.net/<default container of Hadoop cluster>/nyctaxifareraw /DestKey:<storage account key> /S /Pattern:trip_fare_*.csv
```

Data by teď měla být v úložišti objektů BLOB a připravená k využití v clusteru HDInsight.

## <a name="sign-in-to-the-head-node-of-hadoop-cluster-and-prepare-for-exploratory-data-analysis"></a><a name="#download-hql-files"></a>Přihlaste se k hlavnímu uzlu clusteru Hadoop a připravte se na analýzu dat pro průzkumné testování.
> [!NOTE]
> Obvykle se jedná o úlohu správce.
> 
> 

Pokud chcete získat přístup k hlavnímu uzlu clusteru pro průzkumné analýzy dat a vzorkování dat dolů, postupujte podle kroků uvedených v části [přístup k hlavnímu uzlu clusteru Hadoop](customize-hadoop-cluster.md).

V tomto návodu primárně používáme dotazy napsané v [podregistru](https://hive.apache.org/), dotazovací jazyk, který je typu SQL, k provádění předběžných průzkumných dat. Dotazy na podregistr se ukládají do souborů. HQL. Následně dolů – ukázková data se použijí v rámci Machine Learning pro vytváření modelů.

Chcete-li připravit cluster pro průzkumné analýzy dat, Stáhněte soubory '. HQL ', které obsahují relevantní skripty podregistru z [GitHubu](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts) , do místního adresáře (C:\Temp) do hlavního uzlu. Otevřete příkazový řádek z hlavního uzlu v clusteru a spusťte následující dva příkazy:

```console
set script='https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/DataScienceProcess/DataScienceScripts/Download_DataScience_Scripts.ps1'

@powershell -NoProfile -ExecutionPolicy unrestricted -Command "iex ((new-object net.webclient).DownloadString(%script%))"
```

Tyto dva příkazy stáhnou všechny soubory '. HQL ' potřebné v tomto návodu do místního adresáře ***C:\temp&#92;*** do hlavního uzlu.

## <a name="create-hive-database-and-tables-partitioned-by-month"></a><a name="#hive-db-tables"></a>Vytvoření databáze podregistru a tabulek dělených za měsíc
> [!NOTE]
> Tento úkol je typicky pro správce.
> 
> 

Nyní jste připraveni vytvořit tabulky podregistru pro datovou sadu NYC taxislužby.
V hlavním uzlu clusteru Hadoop otevřete příkazový řádek Hadoop na ploše hlavního uzlu. Spuštěním následujícího příkazu zadejte adresář podregistru:

```console
cd %hive_home%\bin
```

> [!NOTE]
> Spustí všechny příkazy podregistru v tomto návodu z adresáře bin/adresáře podadresáře. To zpracovává všechny problémy s cestou automaticky. V tomto návodu použijeme výrazy "výzva k adresáři pro podregistr", "přihrádku na podregistr/adresář" a "příkazový řádek Hadoop".
> 
> 

Z příkazového řádku adresáře podregistr spusťte následující příkaz v příkazovém řádku Hadoop hlavního uzlu, který vytvoří databázi a tabulky podregistru:

```console
hive -f "C:\temp\sample_hive_create_db_and_tables.hql"
```

Tady je obsah ** \_ podregistru C:\temp\sample \_ Create \_ DB \_ a \_ Tables. HQL** , který vytváří podregistr Database **nyctaxidb**a tabulky **TRIPS** a **jízdné**.

```hiveql
create database if not exists nyctaxidb;

create external table if not exists nyctaxidb.trip
(
    medallion string,
    hack_license string,
    vendor_id string,
    rate_code string,
    store_and_fwd_flag string,
    pickup_datetime string,
    dropoff_datetime string,
    passenger_count int,
    trip_time_in_secs double,
    trip_distance double,
    pickup_longitude double,
    pickup_latitude double,
    dropoff_longitude double,
    dropoff_latitude double)  
PARTITIONED BY (month int)
ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' lines terminated by '\n'
STORED AS TEXTFILE LOCATION 'wasb:///nyctaxidbdata/trip' TBLPROPERTIES('skip.header.line.count'='1');

create external table if not exists nyctaxidb.fare
(
    medallion string,
    hack_license string,
    vendor_id string,
    pickup_datetime string,
    payment_type string,
    fare_amount double,
    surcharge double,
    mta_tax double,
    tip_amount double,
    tolls_amount double,
    total_amount double)
PARTITIONED BY (month int)
ROW FORMAT DELIMITED FIELDS TERMINATED BY ',' lines terminated by '\n'
STORED AS TEXTFILE LOCATION 'wasb:///nyctaxidbdata/fare' TBLPROPERTIES('skip.header.line.count'='1');
```

Tento skript podregistru vytvoří dvě tabulky:

* Tabulka **Trip** obsahuje podrobnosti o cestě každé jízdní (podrobnosti o ovladačích, doba vyzvednutí, vzdálenost na cestách a časy).
* Tabulka **tarifů** obsahuje podrobnosti tarifů (částka tarifů, částka hrotu, mýtné a příplatek).

Pokud potřebujete další pomoc s těmito postupy nebo chcete prozkoumat alternativy, prostudujte si část [odeslání dotazů na podregistr přímo z příkazového řádku Hadoop](move-hive-tables.md#submit).

## <a name="load-data-to-hive-tables-by-partitions"></a><a name="#load-data"></a>Načtení dat do tabulek podregistru podle oddílů
> [!NOTE]
> Tento úkol je typicky pro správce.
> 
> 

Datová sada taxislužby NYC má přirozený segmentaci za měsíc, který používáme k zajištění rychlejšího zpracování a času dotazování. Následující příkazy PowerShellu (vydané z adresáře podregistru pomocí příkazového řádku Hadoop) načítají data do tabulek pro jízdu na cestě a tarify dělené po měsících.

```powershell
for /L %i IN (1,1,12) DO (hive -hiveconf MONTH=%i -f "C:\temp\sample_hive_load_data_by_partitions.hql")
```

**Ukázkový \_ podregistr \_ načíst \_ data \_ pomocí \_ oddílů. HQL** obsahuje následující příkazy **načtení** :

```hiveql
LOAD DATA INPATH 'wasb:///nyctaxitripraw/trip_data_${hiveconf:MONTH}.csv' INTO TABLE nyctaxidb.trip PARTITION (month=${hiveconf:MONTH});
LOAD DATA INPATH 'wasb:///nyctaxifareraw/trip_fare_${hiveconf:MONTH}.csv' INTO TABLE nyctaxidb.fare PARTITION (month=${hiveconf:MONTH});
```

Počet dotazů na podregistr, které se tady používají v procesu průzkumu, zahrnuje prohlížení jenom jednoho nebo dvou oddílů. Tyto dotazy ale můžete spustit napříč celou datovou sadou.

### <a name="show-databases-in-the-hdinsight-hadoop-cluster"></a><a name="#show-db"></a>Zobrazit databáze v clusteru HDInsight Hadoop
Pokud chcete zobrazit databáze vytvořené v clusteru HDInsight Hadoop uvnitř okna příkazového řádku Hadoop, spusťte na příkazovém řádku Hadoop následující příkaz:

```console
hive -e "show databases;"
```

### <a name="show-the-hive-tables-in-the-nyctaxidb-database"></a><a name="#show-tables"></a>Zobrazit tabulky podregistru v databázi **nyctaxidb**
Chcete-li zobrazit tabulky v databázi **nyctaxidb** , spusťte následující příkaz v příkazovém řádku Hadoop:

```console
hive -e "show tables in nyctaxidb;"
```

Spuštěním následujícího příkazu si můžete ověřit, že jsou tabulky rozdělené na oddíly:

```console
hive -e "show partitions nyctaxidb.trip;"
```

Tady je očekávaný výstup:

```output
month=1
month=10
month=11
month=12
month=2
month=3
month=4
month=5
month=6
month=7
month=8
month=9
Time taken: 2.075 seconds, Fetched: 12 row(s)
```

Podobně je možné zajistit, aby tabulka tarifů byla rozdělená spuštěním následujícího příkazu:

```console
hive -e "show partitions nyctaxidb.fare;"
```

Tady je očekávaný výstup:

```output
month=1
month=10
month=11
month=12
month=2
month=3
month=4
month=5
month=6
month=7
month=8
month=9
Time taken: 1.887 seconds, Fetched: 12 row(s)
```

## <a name="data-exploration-and-feature-engineering-in-hive"></a><a name="#explore-hive"></a>Zkoumání dat a strojírenství funkcí v podregistru
> [!NOTE]
> Obvykle se jedná o úkol odborníka na data.
> 
> 

Dotazy na podregistry můžete použít k provádění úloh pro zkoumání a zpracování dat pro data načtená do tabulek podregistru. Tady jsou příklady těchto úloh:

* Zobrazení prvních 10 záznamů v obou tabulkách.
* Prozkoumejte distribuci dat několika polí v různých časových oknech.
* Prozkoumejte kvalitu dat v polích Zeměpisná délka a zeměpisná šířka.
* Vygenerujte binární a mezitřídní popisky klasifikace na základě velikosti tipu.
* Vygenerujte funkce vynásobením vzdálenosti přímých cest.

### <a name="exploration-view-the-top-10-records-in-table-trip"></a>Průzkum: zobrazení prvních 10 záznamů v tabulce výlet
> [!NOTE]
> Obvykle se jedná o úkol odborníka na data.
> 
> 

Chcete-li zjistit, co data vypadají, Prohlédněte si 10 záznamů z každé tabulky. Chcete-li zkontrolovat záznamy, spusťte následující dva dotazy odděleně od výzvy ke spuštění adresáře podregistr v konzole příkazového řádku Hadoop.

Pokud chcete získat prvních 10 záznamů v tabulce cest od prvního měsíce:

```console
hive -e "select * from nyctaxidb.trip where month=1 limit 10;"
```

Pokud chcete získat prvních 10 záznamů v tabulce tarifů od prvního měsíce:

```console
hive -e "select * from nyctaxidb.fare where month=1 limit 10;"
```

Záznamy můžete uložit do souboru pro pohodlné zobrazení s malou změnou v předchozím dotazu:

```console
hive -e "select * from nyctaxidb.fare where month=1 limit 10;" > C:\temp\testoutput
```

### <a name="exploration-view-the-number-of-records-in-each-of-the-12-partitions"></a>Průzkum: zobrazení počtu záznamů v jednotlivých 12 oddílech
> [!NOTE]
> Obvykle se jedná o úkol odborníka na data.
> 
> 

Důležité je, jak se počet cest v kalendářním roce mění. Seskupení podle měsíce znázorňuje distribuci cest.

```console
hive -e "select month, count(*) from nyctaxidb.trip group by month;"
```

Tento příkaz vytvoří následující výstup:

```output
1       14776615
2       13990176
3       15749228
4       15100468
5       15285049
6       14385456
7       13823840
8       12597109
9       14107693
10      15004556
11      14388451
12      13971118
Time taken: 283.406 seconds, Fetched: 12 row(s)
```

Zde je první sloupec měsíc a druhým je počet cest k tomuto měsíci.

Celkový počet záznamů v naší datové sadě můžete také spočítat spuštěním následujícího příkazu na příkazovém řádku adresáře podregistru:

```console
hive -e "select count(*) from nyctaxidb.trip;"
```

Tento příkaz vypočítá:

```output
173179759
Time taken: 284.017 seconds, Fetched: 1 row(s)
```

Pomocí příkazů, které jsou podobné těm, které jsou zobrazené pro datovou sadu pro cestu, můžeme vystavit dotazy na podregistry z adresáře podadresáře pro datovou sadu jízdného a ověřit počet záznamů.

```console
hive -e "select month, count(*) from nyctaxidb.fare group by month;"
```

Tento příkaz vytvoří tento výstup:

```output
1       14776615
2       13990176
3       15749228
4       15100468
5       15285049
6       14385456
7       13823840
8       12597109
9       14107693
10      15004556
11      14388451
12      13971118
Time taken: 253.955 seconds, Fetched: 12 row(s)
```

V obou datových sadách se vrátí přesně stejný počet cest za měsíc, který poskytuje první ověření, že data byla načtena správně.

Celkový počet záznamů v datové sadě tarifů můžete spočítat pomocí následujícího příkazu na příkazovém řádku adresáře podregistru:

```console
hive -e "select count(*) from nyctaxidb.fare;"
```

Tento příkaz vypočítá:

```output
173179759
Time taken: 186.683 seconds, Fetched: 1 row(s)
```

Celkový počet záznamů v obou tabulkách je také stejný a poskytuje druhé ověření, že data byla načtena správně.

### <a name="exploration-trip-distribution-by-medallion"></a>Průzkum: distribuce cest pomocí Medallion
> [!NOTE]
> Tato analýza je obvykle úkol odborníka na data.
> 
> 

Tento příklad identifikuje medallions (taxislužby čísla) s více než 100 TRIPS během daného časového období. Dotaz je výhodou z přístupu k dělenému tabulce, protože je podmíněna proměnnou month za **měsíc**. Výsledky dotazu jsou zapsány do místního souboru **queryoutput. TSV**v `C:\temp` hlavní uzel.

```console
hive -f "C:\temp\sample_hive_trip_count_by_medallion.hql" > C:\temp\queryoutput.tsv
```

Tady je obsah **ukázkového \_ počtu cest k podregistru \_ podle souboru \_ \_ \_ Medallion. HQL** pro kontrolu.

```hiveql
SELECT medallion, COUNT(*) as med_count
FROM nyctaxidb.fare
WHERE month<=3
GROUP BY medallion
HAVING med_count > 100
ORDER BY med_count desc;
```

Medallion v datové sadě NYC taxislužby identifikuje jedinečný soubor CAB. Můžete určit, které kabiny jsou poměrně zaneprázdněny tím, že zjistíte, které z nich v určitém časovém období provedly více než určitý počet cest. Následující příklad identifikuje kabiny, které provedly více než sto cest v prvních třech měsících, a uloží výsledky dotazu do místního souboru **C:\temp\queryoutput.TSV**.

Tady je obsah **ukázkového \_ počtu cest k podregistru \_ podle souboru \_ \_ \_ Medallion. HQL** pro kontrolu.

```hiveql
SELECT medallion, COUNT(*) as med_count
FROM nyctaxidb.fare
WHERE month<=3
GROUP BY medallion
HAVING med_count > 100
ORDER BY med_count desc;
```

Na příkazovém řádku adresáře podregistru spusťte následující příkaz:

```console
hive -f "C:\temp\sample_hive_trip_count_by_medallion.hql" > C:\temp\queryoutput.tsv
```

### <a name="exploration-trip-distribution-by-medallion-and-hack-license"></a>Průzkum: distribuce cest pomocí Medallion a licence pro napadení
> [!NOTE]
> Tato úloha je typicky pro datový vědecký pracovník.
> 
> 

Při zkoumání datové sady často chceme prozkoumat distribuce skupin hodnot. V této části najdete příklad toho, jak provést tuto analýzu pro kabiny a ovladače.

**Ukázkový \_ Počet cest k podregistru \_ \_ \_ podle \_ Medallion \_ License. HQL** seskupuje datovou sadu tarifů v **Medallion** a **hack_license**a vrátí počty jednotlivých kombinací. Tady jsou jeho obsah:

```hiveql
SELECT medallion, hack_license, COUNT(*) as trip_count
FROM nyctaxidb.fare
WHERE month=1
GROUP BY medallion, hack_license
HAVING trip_count > 100
ORDER BY trip_count desc;
```

Tento dotaz vrátí kombinace souborů CAB a ovladačů seřazený podle sestupného počtu cest.

Na příkazovém řádku adresáře podregistru spusťte příkaz:

```console
hive -f "C:\temp\sample_hive_trip_count_by_medallion_license.hql" > C:\temp\queryoutput.tsv
```

Výsledky dotazu jsou zapsány do místního souboru **C:\temp\queryoutput.TSV**.

### <a name="exploration-assessing-data-quality-by-checking-for-invalid-longitude-or-latitude-records"></a>Průzkum: vyhodnocení kvality dat kontrolou neplatných zeměpisných zeměpisných hodnot nebo zeměpisných údajů
> [!NOTE]
> Obvykle se jedná o úkol odborníka na data.
> 
> 

Běžným cílem analýzy dat pro průzkumné účely je vytvoření neplatných nebo špatných záznamů. Příklad v této části určuje, zda pole Zeměpisná délka nebo zeměpisná šířka obsahují hodnotu daleko mimo NYC oblast. Vzhledem k tomu, že tyto záznamy mají chybnou zeměpisnou délku, chceme je eliminovat z dat, která se mají použít pro modelování.

Tady je obsah **ukázkového souboru \_ \_ \_ . HQL vyhodnocení kvality podregistru** pro kontrolu.

```hiveql
    SELECT COUNT(*) FROM nyctaxidb.trip
    WHERE month=1
    AND  (CAST(pickup_longitude AS float) NOT BETWEEN -90 AND -30
    OR    CAST(pickup_latitude AS float) NOT BETWEEN 30 AND 90
    OR    CAST(dropoff_longitude AS float) NOT BETWEEN -90 AND -30
    OR    CAST(dropoff_latitude AS float) NOT BETWEEN 30 AND 90);
```

Na příkazovém řádku adresáře podregistru spusťte příkaz:

```console
hive -S -f "C:\temp\sample_hive_quality_assessment.hql"
```

Argument *-S* , který je součástí tohoto příkazu, potlačí výpis stavu obrazovky mapy podregistru nebo omezení úloh. Tento příkaz je užitečný, protože usnadňuje tisk na obrazovce výstup dotazu na podregistr.

### <a name="exploration-binary-class-distributions-of-trip-tips"></a>Průzkum: popisy binárních tříd cest
> [!NOTE]
> Obvykle se jedná o úkol odborníka na data.
> 
> 

Pro problém binární klasifikace, který je popsaný v části [příklady úkolů předpovědi](hive-walkthrough.md#mltasks) , je užitečné zjistit, zda byl Tip uveden nebo nikoli. Tato distribuce tipů je binární:

* zadaný Tip (třída 1, \_ hodnota tipu > $0)  
* žádný Tip (třída 0, \_ hodnota tipu = $0)

Následující **ukázkový soubor \_ \_ \_ hqlch frekvencí podregistru** zobrazuje příkaz, který se má spustit:

```hiveql
SELECT tipped, COUNT(*) AS tip_freq
FROM
(
    SELECT if(tip_amount > 0, 1, 0) as tipped, tip_amount
    FROM nyctaxidb.fare
)tc
GROUP BY tipped;
```

Na příkazovém řádku adresáře podregistru spusťte příkaz:

```console
hive -f "C:\temp\sample_hive_tipped_frequencies.hql"
```


### <a name="exploration-class-distributions-in-the-multiclass-setting"></a>Průzkum: distribuce tříd v nastavení více tříd
> [!NOTE]
> Obvykle se jedná o úkol odborníka na data.
> 
> 

V případě problému s více třídami, který je popsaný v části [příklady úkolů předpovědi](hive-walkthrough.md#mltasks) , se tato datová sada také přímo zaplní do přirozené klasifikace pro předpověď množství předaných tipů. K definování rozsahů tipů v dotazu můžeme použít přihrádky. Chcete-li získat distribuce tříd pro různé rozsahy tipů, použijte soubor ** \_ \_ \_ \_ . HQL s ukázkovým koncem podregistru** . Tady je jeho obsah.

```hiveql
SELECT tip_class, COUNT(*) AS tip_freq
FROM
(
    SELECT if(tip_amount=0, 0,
        if(tip_amount>0 and tip_amount<=5, 1,
        if(tip_amount>5 and tip_amount<=10, 2,
        if(tip_amount>10 and tip_amount<=20, 3, 4)))) as tip_class, tip_amount
    FROM nyctaxidb.fare
)tc
GROUP BY tip_class;
```

Z konzoly příkazového řádku Hadoop spusťte následující příkaz:

```console
hive -f "C:\temp\sample_hive_tip_range_frequencies.hql"
```

### <a name="exploration-compute-the-direct-distance-between-two-longitude-latitude-locations"></a>Průzkum: výpočet přímé vzdálenosti mezi dvěma zeměpisnou šířkou a místy Zeměpisná šířka
> [!NOTE]
> Obvykle se jedná o úkol odborníka na data.
> 
> 

Možná budete chtít zjistit, jestli existuje rozdíl mezi přímou vzdáleností mezi dvěma umístěními a skutečnou délkou taxislužby. Pokud se dostanou, může se cestující považovat za to, že si ho v rámci delšího postupu úmyslně převzal.

Pokud chcete zobrazit srovnání mezi skutečnou délkou cest a [Haversine vzdáleností](https://en.wikipedia.org/wiki/Haversine_formula) mezi dvěma zeměpisnou délkou (vzdálenost "skvělého kruhu"), můžete použít trigonometrické funkce dostupné v rámci podregistru:

```hiveql
set R=3959;
set pi=radians(180);

insert overwrite directory 'wasb:///queryoutputdir'

select pickup_longitude, pickup_latitude, dropoff_longitude, dropoff_latitude, trip_distance, trip_time_in_secs,
${hiveconf:R}*2*2*atan((1-sqrt(1-pow(sin((dropoff_latitude-pickup_latitude)
 *${hiveconf:pi}/180/2),2)-cos(pickup_latitude*${hiveconf:pi}/180)
 *cos(dropoff_latitude*${hiveconf:pi}/180)*pow(sin((dropoff_longitude-pickup_longitude)*${hiveconf:pi}/180/2),2)))
 /sqrt(pow(sin((dropoff_latitude-pickup_latitude)*${hiveconf:pi}/180/2),2)
 +cos(pickup_latitude*${hiveconf:pi}/180)*cos(dropoff_latitude*${hiveconf:pi}/180)*
 pow(sin((dropoff_longitude-pickup_longitude)*${hiveconf:pi}/180/2),2))) as direct_distance
from nyctaxidb.trip
where month=1
and pickup_longitude between -90 and -30
and pickup_latitude between 30 and 90
and dropoff_longitude between -90 and -30
and dropoff_latitude between 30 and 90;
```

V předchozím dotazu R je poloměr země zeminy v mílích a PI je převedena na radiány. Zeměpisná délka – Zeměpisná šířka se filtruje tak, aby se odebraly hodnoty, které jsou daleko z oblasti NYC.

V tomto případě zapíšeme výsledky do adresáře s názvem **queryoutputdir**. Sekvence následujících příkazů nejprve vytvoří tento výstupní adresář a potom spustí příkaz podregistr.

Na příkazovém řádku adresáře podregistru spusťte příkaz:

```hiveql
hdfs dfs -mkdir wasb:///queryoutputdir

hive -f "C:\temp\sample_hive_trip_direct_distance.hql"
```

Výsledky dotazu jsou zapisovány do devíti objektů blob Azure (**queryoutputdir/000000 \_ 0** do  **queryoutputdir/000008 \_ 0**) ve výchozím kontejneru clusteru Hadoop.

Chcete-li zobrazit velikost jednotlivých objektů blob, spusťte následující příkaz na příkazovém řádku adresáře podregistr:

```hiveql
hdfs dfs -ls wasb:///queryoutputdir
```

Pokud chcete zobrazit obsah daného souboru, řekněme, že **000000 \_ 0**, použijte příkaz Hadoop `copyToLocal` .

```hiveql
hdfs dfs -copyToLocal wasb:///queryoutputdir/000000_0 C:\temp\tempfile
```

> [!WARNING]
> `copyToLocal` může být u velkých souborů velmi pomalé a nedoporučuje se je používat s nimi.  
> 
> 

Klíčovou výhodou toho, aby se tato data nacházela v objektu blob Azure, je to, že můžeme prozkoumat data v rámci Machine Learning pomocí modulu [Import dat][import-data] .

## <a name="down-sample-data-and-build-models-in-machine-learning"></a><a name="#downsample"></a>Nefunkční vzorová data a modely sestavení v Machine Learning
> [!NOTE]
> Obvykle se jedná o úkol odborníka na data.
> 
> 

Po fázi analýzy průzkumného testování teď jsme připraveni k vyvzorkování dat pro vytváření modelů v Machine Learning. V této části ukážeme, jak používat dotaz na podmnožinu dat. Machine Learning pak k němu přistupuje z modulu [Import dat][import-data] .

### <a name="down-sampling-the-data"></a>Vypnutí vzorkování dat
Tento postup obsahuje dva kroky. Nejdřív se připojte k tabulkám **nyctaxidb. Trip** a **nyctaxidb. tarif** na třech klíčích, které jsou přítomné ve všech záznamech: **Medallion**, oprávnění k **napadení \_ **a **data a \_ času vyzvednutí**. Pak vygenerujeme popisek binární **klasifikace, nezobrazený a popisek**klasifikace s více třídami, ** \_ Třída Tip**.

Aby bylo možné používat data z rozevíracího seznamu přímo z modulu [Import dat][import-data] v Machine Learning, uložte výsledky předchozího dotazu do interní tabulky podregistru. V následujícím příkladu vytvoříme interní tabulku podregistru a naplníme její obsah s připojenými a postupnými ukázkovými daty.

Dotaz aplikuje standardní funkce podregistru přímo k vygenerování následujících časových parametrů z **pole \_ Datum vyzvednutí** :
- hodina dne
- týden roku
- den v týdnu (' 1 ' znamená pondělí a ' 7 ' představuje neděli)

Dotaz také generuje přímou vzdálenost mezi místy pro výběr a umístění dropoff. Úplný seznam takových funkcí najdete v tématu [LANGUAGEMANUAL UDF](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF).

Dotaz pak vyvzorkuje data, aby se výsledky dotazu vešly na Azure Machine Learning Studio. Do studia se naimportuje jenom přibližně 1 procento původní datové sady.

Tady je obsah **ukázkového \_ podregistru \_ připravo \_ pro \_ AML \_ úplný soubor. HQL** , který připraví data pro sestavování modelu v Machine Learning:

```hiveql
set R = 3959;
set pi=radians(180);

create table if not exists nyctaxidb.nyctaxi_downsampled_dataset (

medallion string,
hack_license string,
vendor_id string,
rate_code string,
store_and_fwd_flag string,
pickup_datetime string,
dropoff_datetime string,
pickup_hour string,
pickup_week string,
weekday string,
passenger_count int,
trip_time_in_secs double,
trip_distance double,
pickup_longitude double,
pickup_latitude double,
dropoff_longitude double,
dropoff_latitude double,
direct_distance double,
payment_type string,
fare_amount double,
surcharge double,
mta_tax double,
tip_amount double,
tolls_amount double,
total_amount double,
tipped string,
tip_class string
)
row format delimited fields terminated by ','
lines terminated by '\n'
stored as textfile;

--- now insert contents of the join into the above internal table

insert overwrite table nyctaxidb.nyctaxi_downsampled_dataset
select
t.medallion,
t.hack_license,
t.vendor_id,
t.rate_code,
t.store_and_fwd_flag,
t.pickup_datetime,
t.dropoff_datetime,
hour(t.pickup_datetime) as pickup_hour,
weekofyear(t.pickup_datetime) as pickup_week,
from_unixtime(unix_timestamp(t.pickup_datetime, 'yyyy-MM-dd HH:mm:ss'),'u') as weekday,
t.passenger_count,
t.trip_time_in_secs,
t.trip_distance,
t.pickup_longitude,
t.pickup_latitude,
t.dropoff_longitude,
t.dropoff_latitude,
t.direct_distance,
f.payment_type,
f.fare_amount,
f.surcharge,
f.mta_tax,
f.tip_amount,
f.tolls_amount,
f.total_amount,
if(tip_amount>0,1,0) as tipped,
if(tip_amount=0,0,
if(tip_amount>0 and tip_amount<=5,1,
if(tip_amount>5 and tip_amount<=10,2,
if(tip_amount>10 and tip_amount<=20,3,4)))) as tip_class

from
(
select
medallion,
hack_license,
vendor_id,
rate_code,
store_and_fwd_flag,
pickup_datetime,
dropoff_datetime,
passenger_count,
trip_time_in_secs,
trip_distance,
pickup_longitude,
pickup_latitude,
dropoff_longitude,
dropoff_latitude,
${hiveconf:R}*2*2*atan((1-sqrt(1-pow(sin((dropoff_latitude-pickup_latitude)
*${hiveconf:pi}/180/2),2)-cos(pickup_latitude*${hiveconf:pi}/180)
*cos(dropoff_latitude*${hiveconf:pi}/180)*pow(sin((dropoff_longitude-pickup_longitude)*${hiveconf:pi}/180/2),2)))
/sqrt(pow(sin((dropoff_latitude-pickup_latitude)*${hiveconf:pi}/180/2),2)
+cos(pickup_latitude*${hiveconf:pi}/180)*cos(dropoff_latitude*${hiveconf:pi}/180)*pow(sin((dropoff_longitude-pickup_longitude)*${hiveconf:pi}/180/2),2))) as direct_distance,
rand() as sample_key

from nyctaxidb.trip
where pickup_latitude between 30 and 90
    and pickup_longitude between -90 and -30
    and dropoff_latitude between 30 and 90
    and dropoff_longitude between -90 and -30
)t
join
(
select
medallion,
hack_license,
vendor_id,
pickup_datetime,
payment_type,
fare_amount,
surcharge,
mta_tax,
tip_amount,
tolls_amount,
total_amount
from nyctaxidb.fare
)f
on t.medallion=f.medallion and t.hack_license=f.hack_license and t.pickup_datetime=f.pickup_datetime
where t.sample_key<=0.01
```

Chcete-li spustit tento dotaz z adresáře podregistr:

```console
hive -f "C:\temp\sample_hive_prepare_for_aml_full.hql"
```

Teď máme interní tabulku, **nyctaxidb.nyctaxi_downsampled_dataset**, ke které se dá dostat pomocí modulu [Import dat][import-data] z Machine Learning. Kromě toho můžeme použít tuto datovou sadu pro vytváření Machine Learningch modelů.  

### <a name="use-the-import-data-module-in-machine-learning-to-access-the-down-sampled-data"></a>Použití modulu import dat v Machine Learning k získání přístupu k datům, která mají nižší vzorek
K vydávání dotazů na podregistr v modulu Machine Learning [importu dat][import-data] potřebujete přístup k pracovnímu prostoru Machine Learning. Potřebujete také přístup k přihlašovacím údajům ke clusteru a k přidruženému účtu úložiště.

Zde jsou některé podrobnosti o modulu [Import dat][import-data] a parametry, které je potřeba zadat:

**Identifikátor URI serveru HCatalog**: Pokud je název clusteru **abc123**, pak použijte: https: \/ /abc123.azurehdinsight.NET.

**Název uživatelského účtu Hadoop**: uživatelské jméno zvolené pro cluster (nikoli uživatelské jméno vzdáleného přístupu).

**Heslo uživatelského účtu Hadoop**: heslo zvolené pro cluster (nikoli heslo vzdáleného přístupu).

**Umístění výstupních dat**: zvolené pro Azure.

**Azure Storage název účtu**: název výchozího účtu úložiště přidruženého ke clusteru.

**Název kontejneru Azure**: výchozí název kontejneru pro cluster, který je obvykle stejný jako název clusteru. Pro cluster s názvem **abc123**je název abc123.

> [!IMPORTANT]
> Každá tabulka, kterou chceme použít k dotazování pomocí modulu [Import dat][import-data] v Machine Learning, musí být interní tabulkou.
> 
> 

Zde je postup určení, zda je tabulka **T** v databázi **D. DB** interní tabulkou. Na příkazovém řádku adresáře podregistru spusťte následující příkaz:

```hiveql
hdfs dfs -ls wasb:///D.db/T
```

Pokud je tabulka interní tabulkou a je naplněná, její obsah se musí zobrazit tady.

Dalším způsobem, jak určit, zda je tabulka interní tabulkou, je použití Průzkumník služby Azure Storage. Použijte ho k přechodu na výchozí název kontejneru clusteru a pak ho vyfiltrujte podle názvu tabulky. Pokud se zobrazí tabulka a její obsah, potvrdí se tím, že se jedná o interní tabulku.

Tady je snímek obrazovky s dotazem na podregistr a modulem [importovat data][import-data] :

![Snímek obrazovky s dotazem na podregistr pro modul import dat](./media/hive-walkthrough/1eTYf52.png)

Vzhledem k tomu, že se naše data v ukázce nacházejí ve výchozím kontejneru, je výsledný dotaz podregistru z Machine Learning jednoduchý. Je to jenom **výběrový znak * z nyctaxidb. nyctaxi \_ downsampled \_ data**.

Datovou sadu teď můžete použít jako výchozí bod pro vytváření Machine Learningch modelů.

### <a name="build-models-in-machine-learning"></a><a name="mlmodel"></a>Modely sestavení v Machine Learning
Nyní můžete přejít k sestavení modelu a nasazení modelu v [Machine Learning](https://studio.azureml.net). Data jsou připravená na použití při řešení problémů s předpovědí zjištěných dříve:

- **Binární klasifikace**: pro předpověď, zda byl pro cestu zaplacen Tip.

  **Použil se učí:** Logistická regrese dvou tříd

  a. Pro tento problém je popisek cíl (nebo třída) na stejném **řádku.** Původní datová sada v ukázce má několik sloupců, které jsou nevrácenými cíli pro tento experiment klasifikace. Konkrétně ** \_ Třída Tip**, ** \_ hodnota Tip**a **Celková \_ Částka** odhalují informace o cílovém popisku, který není k dispozici v době testování. Tyto sloupce odebereme z úvahy pomocí modulu [Výběr sloupců v datové sadě][select-columns] .

  Následující diagram znázorňuje náš experiment, který předpovídá, zda byl pro danou cestu zaplacen Tip:

  ![Diagram experimentu pro předpověď, zda byl zaplacen Tip](./media/hive-walkthrough/QGxRz5A.png)

  b. Pro tento experiment byly naše cílové distribuce popisků zhruba 1:1.

   Následující graf znázorňuje distribuci popisků třídy Tip pro problém binární klasifikace:

  ![Graf distribuce popisků třídy Tip](./media/hive-walkthrough/9mM4jlD.png)

    V důsledku toho získáme plochu pod křivkou (AUC) 0,987, jak je znázorněno na následujícím obrázku:

  ![Graf hodnoty AUC](./media/hive-walkthrough/8JDT0F8.png)

- **Třídy s více třídami**: pro předpověď rozsahu částek v rámci této cesty, které jsou vyplaceny pro danou cestu, pomocí dříve definovaných tříd.

  **Použil se učí:** Mikrotřída logistické regrese

  a. Pro tento problém je naším cílem (nebo třídou) popisek ** \_ Třída**, která může mít jednu z pěti hodnot (0, 1, 2, 3, 4). Stejně jako v případě binárního klasifikačního případu máme pro tento experiment několik sloupců, které jsou nevrácenými cíli. Konkrétně množství informací o cílovém **popisku, který**není k ** \_ ** dispozici v době testování. ** \_ ** Tyto sloupce odebereme pomocí modulu [Výběr sloupců v datové sadě][select-columns] .

  Následující diagram znázorňuje experiment, ve kterém je možné předpovědět, který z nich je pravděpodobný. Tyto přihrádky jsou: Class 0: Tip = $0, třída 1: Tip > $0 a Tip <= $5, třída 2: Tip > $5 a Tip <= $10, třída 3: Tip > $10 a Tip <= $20 a třída 4: Tip > $20.

  ![Diagram experimentu pro předpověď bin pro Tip](./media/hive-walkthrough/5ztv0n0.png)

  Nyní ukážeme, jak vypadá skutečná distribuce třídy testu. Třídy 0 a třídy 1 jsou předané a ostatní třídy jsou zřídka.

  ![Graf distribuce testovacích tříd](./media/hive-walkthrough/Vy1FUKa.png)

  b. Pro tento experiment používáme nejasnou matrici k zobrazení předpovědi přesností, jak je znázorněno zde:

  ![Konfuzní matice](./media/hive-walkthrough/cxFmErM.png)

  I když je třída přesností na předaných třídách dobrá, model není dobrým úkolem "učení" na třídách rarer.

- **Regresní úloha**: pro předpověď množství tipu placeného pro cestu.

  **Použil se učí:** Zesílený rozhodovací strom

  a. Pro tento problém je popisek cíl (nebo třída) ** \_ hodnota špičky**. Cílové nevracení v tomto případě jsou: **šikmé**, ** \_ Třída Tip**a **Celková \_ Částka**. Všechny tyto proměnné odhalují informace o velikosti tipu, která je obvykle k dispozici v době testování. Tyto sloupce odebereme pomocí modulu [Výběr sloupců v datové sadě][select-columns] .

  Následující diagram znázorňuje experiment pro předpověď množství daného tipu:

  ![Diagram experimentu pro předpověď množství tipů](./media/hive-walkthrough/11TZWgV.png)

  b. V případě regresních problémů měříme přesností předpověď tím, že prohlížíte druhou chybu v předpovědi a součiniteli určení:

  ![Snímek obrazovky s statistikou předpovědi](./media/hive-walkthrough/Jat9mrz.png)

  Zde je koeficient stanovení 0,709, což znamená, že o 71 procent odchylky je vysvětleno v koeficientech modelu.

> [!IMPORTANT]
> Další informace o Machine Learning a o tom, jak k nim přistupovat a jak ji používat, najdete v tématu [co je Machine Learning](../classic/index.yml). Kromě toho [Azure AI Gallery](https://gallery.cortanaintelligence.com/) pokrývá rozsah experimentů a poskytuje důkladný úvod do rozsahu možností Machine Learning.
> 
> 

## <a name="license-information"></a>Informace o licenci
Tento ukázkový návod a jeho doprovodné skripty sdílí Microsoft v rámci licence MIT. Další informace najdete v souboru **LICENSE.txt** v adresáři ukázkového kódu na GitHubu.

## <a name="references"></a>Reference
•    [Stránka pro stažení Andrés MONROY NYC taxislužby](https://www.andresmh.com/nyctaxitrips/)  
•    [Fólie NYC data taxislužby na cestách pomocí chrisho Whongu](https://chriswhong.com/open-data/foil_nyc_taxi/)   
•    [NYC taxislužby a Limousine výzkumu a statistiky Komise](https://www1.nyc.gov/site/tlc/about/tlc-trip-record-data.page)

[2]: ./media/hive-walkthrough/output-hive-results-3.png
[11]: ./media/hive-walkthrough/hive-reader-properties.png
[12]: ./media/hive-walkthrough/binary-classification-training.png
[13]: ./media/hive-walkthrough/create-scoring-experiment.png
[14]: ./media/hive-walkthrough/binary-classification-scoring.png
[15]: ./media/hive-walkthrough/amlreader.png

<!-- Module References -->
[select-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/
