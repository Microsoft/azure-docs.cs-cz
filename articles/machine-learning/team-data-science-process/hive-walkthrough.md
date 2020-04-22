---
title: Prozkoumejte data v clusteru Hadoop – proces vědecké vědy o týmových datech
description: Pomocí procesu vědecké analýzy týmových dat pro komplexní scénář, využívající cluster HDInsight Hadoop k sestavení a nasazení modelu.
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
ms.openlocfilehash: bf69786f56f52874bd9358ae44a6b88b466e77f4
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2020
ms.locfileid: "81677460"
---
# <a name="the-team-data-science-process-in-action-use-azure-hdinsight-hadoop-clusters"></a>Proces vědecké ho procesu týmových dat v akci: Použijte clustery Azure HDInsight Hadoop
V tomto návodu používáme [proces vědecké analýzy týmových dat (TDSP)](overview.md) v komplexním scénáři. Používáme [cluster Azure HDInsight Hadoop](https://azure.microsoft.com/services/hdinsight/) k ukládání, zkoumání a inženýrských funkcí dat z veřejně dostupné datové sady [NYC Taxi Trips](https://www.andresmh.com/nyctaxitrips/) a k down-sample data. Chcete-li zpracovat binární a vícetřídní klasifikace a regresní prediktivní úkoly, vytváříme modely dat s Azure Machine Learning. 

Návod, který ukazuje, jak zpracovat větší datovou sadu, najdete v článku [Proces vědecké analýzy týmových dat – použití clusterů Azure HDInsight Hadoop v datové sadě o velikosti 1 TB](hive-criteo-walkthrough.md).

Poznámkový blok IPython můžete také použít k provedení úloh prezentovaných v návodu, který používá datovou sadu 1 TB. Další informace naleznete v [návodu criteo pomocí připojení HIVE ODBC](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/iPythonNotebooks/machine-Learning-data-science-process-hive-walkthrough-criteo.ipynb).

## <a name="nyc-taxi-trips-dataset-description"></a><a name="dataset"></a>NYC Taxi Výlety popis datové sady
Nyc Taxi Trip data je asi 20 GB komprimovaných hodnot oddělených čárkami (CSV) souborů (~ 48 GB nekomprimované). Má více než 173 milionů individuálních cest a zahrnuje tarify zaplacené za každou cestu. Každý záznam cesty obsahuje místo a čas vyzvednutí a předání, anonymizované číslo licence hacku (řidiče) a číslo medailonu (jedinečné ID taxi). Údaje se týkají všech cest v roce 2013 a jsou uvedeny v následujících dvou datových souborech za každý měsíc:

- Soubory trip_data CSV obsahují podrobnosti o cestě: počet cestujících, místa vyzvednutí a předání, trvání cesty a délku cesty. Zde je několik ukázkových záznamů:
   
        medallion,hack_license,vendor_id,rate_code,store_and_fwd_flag,pickup_datetime,dropoff_datetime,passenger_count,trip_time_in_secs,trip_distance,pickup_longitude,pickup_latitude,dropoff_longitude,dropoff_latitude
        89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,1,N,2013-01-01 15:11:48,2013-01-01 15:18:10,4,382,1.00,-73.978165,40.757977,-73.989838,40.751171
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-06 00:18:35,2013-01-06 00:22:54,1,259,1.50,-74.006683,40.731781,-73.994499,40.75066
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-05 18:49:41,2013-01-05 18:54:23,1,282,1.10,-74.004707,40.73777,-74.009834,40.726002
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:54:15,2013-01-07 23:58:20,2,244,.70,-73.974602,40.759945,-73.984734,40.759388
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:25:03,2013-01-07 23:34:24,1,560,2.10,-73.97625,40.748528,-74.002586,40.747868
- soubory trip_fare CSV obsahují podrobnosti o jízdném zaplaceném za každou cestu: typ platby, výši jízdného, příplatek a daně, tipy a mýtné a celkovou zaplacenou částku. Zde je několik ukázkových záznamů:
   
        medallion, hack_license, vendor_id, pickup_datetime, payment_type, fare_amount, surcharge, mta_tax, tip_amount, tolls_amount, total_amount
        89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,2013-01-01 15:11:48,CSH,6.5,0,0.5,0,0,7
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-06 00:18:35,CSH,6,0.5,0.5,0,0,7
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-05 18:49:41,CSH,5.5,1,0.5,0,0,7
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:54:15,CSH,5,0.5,0.5,0,0,6
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:25:03,CSH,9.5,0.5,0.5,0,0,10.5

Jedinečný klíč pro\_připojení údajů\_o cestě a jízdného se\_skládá z\_polí: medailon, hack licence, a vyzvednutí datetime. Chcete-li získat všechny podrobnosti týkající se konkrétní cesty, stačí se spojit s těmito třemi klíči.

## <a name="examples-of-prediction-tasks"></a><a name="mltasks"></a>Příklady predikčních úloh
Určete druh předpovědi, které chcete provést na základě analýzy dat, které vám pomohou objasnit požadované úlohy procesu. Zde jsou tři příklady problémů s předpovědí, které řešíme v tomto návodu, vše na základě *částky tipu\_*:

- **Binární klasifikace**: Předpovědět, zda tip byl zaplacen za cestu. To znamená, že *částka\_tipu,* která je větší než $0, je kladným příkladem, zatímco částka *\_tipu* 0 Kč je záporným příkladem.
   
        Class 0: tip_amount = $0
        Class 1: tip_amount > $0
- **Klasifikace více tříd**: Předvídejte rozsah částek spropitného zaplacené za cestu. *Částku špičky\_* rozdělíme do pěti tříd:
   
        Class 0: tip_amount = $0
        Class 1: tip_amount > $0 and tip_amount <= $5
        Class 2: tip_amount > $5 and tip_amount <= $10
        Class 3: tip_amount > $10 and tip_amount <= $20
        Class 4: tip_amount > $20
- **Regresní úkol**: Předpovědět částku tip uhrazeného za cestu.  

## <a name="set-up-an-hdinsight-hadoop-cluster-for-advanced-analytics"></a><a name="setup"></a>Nastavení clusteru HDInsight Hadoop pro pokročilou analýzu
> [!NOTE]
> Obvykle se jedná o úlohu správce.
> 
> 

Prostředí Azure pro pokročilou analýzu, které využívá cluster HDInsight, můžete nastavit ve třech krocích:

1. [Vytvoření účtu úložiště](../../storage/common/storage-account-create.md): Tento účet úložiště se používá pro ukládání dat v úložišti objektů Blob Azure. Data používaná v clusterech HDInsight jsou také umístěna zde.
2. [Přizpůsobte clustery Azure HDInsight Hadoop pro pokročilý analytický proces a technologii](customize-hadoop-cluster.md). Tento krok vytvoří cluster HDInsight Hadoop s 64bitovým Anaconda Pythonem 2.7 nainstalovaným ve všech uzlech. Při přizpůsobování clusteru HDInsight je třeba si zapamatovat dva důležité kroky.
   
   * Nezapomeňte propojit účet úložiště vytvořený v kroku 1 s clusterem HDInsight při jeho vytváření. Tento účet úložiště přistupuje k datům, která jsou zpracovávána v rámci clusteru.
   * Po vytvoření clusteru povolte vzdálený přístup k hlavnímu uzlu clusteru. Přejděte na kartu **Konfigurace** a vyberte **Povolit vzdálené**. Tento krok určuje pověření uživatele používaná pro vzdálené přihlášení.
3. [Vytvoření pracovního prostoru Azure Machine Learning:](../studio/create-workspace.md)Tento pracovní prostor použijete k vytváření modelů strojového učení. Tato úloha je řešena po dokončení počátečního zkoumání dat a vzorkování dolů pomocí clusteru HDInsight.

## <a name="get-the-data-from-a-public-source"></a><a name="getdata"></a>Získání dat z veřejného zdroje
> [!NOTE]
> Obvykle se jedná o úlohu správce.
> 
> 

Chcete-li zkopírovat datovou sadu [NYC Taxi Trips](https://www.andresmh.com/nyctaxitrips/) do počítače z jeho veřejného umístění, použijte některou z metod popsaných v části [Přesunout data do úložiště objektů blob Azure a z ní](move-azure-blob.md).

Zde popisujeme, jak používat AzCopy k přenosu souborů obsahujících data. Chcete-li stáhnout a nainstalovat azCopy, postupujte podle pokynů na [Začínáme s nástrojem příkazového řádku AzCopy](../../storage/common/storage-use-azcopy.md).

1. V okně příkazového řádku spusťte následující příkazy * \<* AzCopy a nahraďte path_to_data_folder>požadovaným cílem:

        "C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:https://nyctaxitrips.blob.core.windows.net/data /Dest:<path_to_data_folder> /S

1. Po dokončení kopírování se ve zvolené datové složce zobrazí celkem 24 souborů na zip. Rozbalte stažené soubory do stejného adresáře v místním počítači. Poznamenejte si složku, ve které jsou umístěny nekomprimované soubory. Tato složka se označuje jako * \<cesta\_k\_unzipped_data\_souborův\> * následujícím.

## <a name="upload-the-data-to-the-default-container-of-the-hdinsight-hadoop-cluster"></a><a name="upload"></a>Nahrání dat do výchozího kontejneru clusteru HDInsight Hadoop
> [!NOTE]
> Obvykle se jedná o úlohu správce.
> 
> 

V následujících příkazech AzCopy nahraďte následující parametry skutečnými hodnotami, které jste zadali při vytváření clusteru Hadoop a rozvířivání datových souborů.

* *** \<path_to_data_folder>*** Adresář (spolu s cestou) v počítači, který obsahuje rozbalené datové soubory.  
* ***název účtu úložiště clusteru Hadoop>\<*** Účet úložiště přidružený k clusteru HDInsight.
* ***výchozí kontejner clusteru Hadoop>\<*** Výchozí kontejner používaný clusterem. Název výchozího kontejneru je obvykle stejný název jako samotný cluster. Například pokud se cluster nazývá "abc123.azurehdinsight.net", výchozí kontejner je abc123.
* klíč úložiště>*** \<*** Klíč pro účet úložiště používaný vaším clusterem.

Z příkazového řádku nebo okna prostředí Windows PowerShell spusťte následující dva příkazy AzCopy.

Tento příkaz odešle data cesty do adresáře ***nyctaxitripraw*** ve výchozím kontejneru clusteru Hadoop.

        "C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:<path_to_unzipped_data_files> /Dest:https://<storage account name of Hadoop cluster>.blob.core.windows.net/<default container of Hadoop cluster>/nyctaxitripraw /DestKey:<storage account key> /S /Pattern:trip_data_*.csv

Tento příkaz odešle data jízdného do ***adresáře nyctaxifareraw*** ve výchozím kontejneru clusteru Hadoop.

        "C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:<path_to_unzipped_data_files> /Dest:https://<storage account name of Hadoop cluster>.blob.core.windows.net/<default container of Hadoop cluster>/nyctaxifareraw /DestKey:<storage account key> /S /Pattern:trip_fare_*.csv

Data by teď měla být v úložišti objektů Blob a připravená ke spotřebovávakům v clusteru HDInsight.

## <a name="sign-in-to-the-head-node-of-hadoop-cluster-and-prepare-for-exploratory-data-analysis"></a><a name="#download-hql-files"></a>Přihlaste se k hlavnímu uzlu clusteru Hadoop a připravte se na analýzu průzkumných dat
> [!NOTE]
> Obvykle se jedná o úlohu správce.
> 
> 

Chcete-li získat přístup k hlavnímu uzlu clusteru pro analýzu průzkumných dat a vzorkování dat dolů, postupujte podle postupu popsaného v [části Přístup k hlavnímu uzlu clusteru Hadoop](customize-hadoop-cluster.md).

V tomto návodu používáme především dotazy napsané v [Hive](https://hive.apache.org/), sql jako dotazovací jazyk, k provedení předběžné zkoumání dat. Dotazy Hive jsou uloženy v souborech '.hql'. Tato data pak utápíme dolů a použijeme je v rámci Strojového učení pro vytváření modelů.

Chcete-li připravit cluster na analýzu průzkumných dat, stáhněte soubory '.hql' obsahující příslušné skripty Hive z [GitHubu](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts) do místního adresáře (C:\temp) v hlavním uzlu. Otevřete příkazový řádek z hlavního uzlu clusteru a spusťte následující dva příkazy:

    set script='https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/DataScienceProcess/DataScienceScripts/Download_DataScience_Scripts.ps1'

    @powershell -NoProfile -ExecutionPolicy unrestricted -Command "iex ((new-object net.webclient).DownloadString(%script%))"

Tyto dva příkazy stáhnou všechny soubory '.hql' potřebné v tomto návodu do místního adresáře ***C:\temp&#92;*** v hlavním uzlu.

## <a name="create-hive-database-and-tables-partitioned-by-month"></a><a name="#hive-db-tables"></a>Vytvořit databázi Hive a tabulky rozdělené podle měsíců
> [!NOTE]
> Tento úkol je obvykle pro správce.
> 
> 

Nyní jste připraveni vytvořit tabulky Hive pro datovou sadu taxi NYC.
V hlavním uzlu clusteru Hadoop otevřete příkazový řádek Hadoop na ploše hlavního uzlu. Zadejte adresář Hive spuštěním následujícího příkazu:

    cd %hive_home%\bin

> [!NOTE]
> Spusťte všechny příkazy Hive v tomto návodu z výzvy k přihrádce podregistru/adresáře. To zpracovává všechny problémy s cestou automaticky. V tomto návodu používáme termíny "Výzva k adresáři Hive", "Hive bin/ directory prompt" a "Hadoop command line".
> 
> 

V řádku adresáře Hive spusťte následující příkaz v příkazovém řádku Hadoop hlavního uzlu, který vytváří databázi a tabulky Hive:

    hive -f "C:\temp\sample_hive_create_db_and_tables.hql"

Zde je obsah **souboru\_C:\temp\sample\_\_\_vytvořit\_db a tables.hql,** který vytvoří databázi Hive **nyctaxidb**a tabulky **trip** a **fare**.

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

Tento skript Hive vytvoří dvě tabulky:

* Tabulka **jízd** obsahuje podrobnosti o jízdě (podrobnosti o řidiči, čas vyzvednutí, vzdálenost jízdy a časy).
* Tabulka **tarifů** obsahuje podrobnosti o jízdném (výše jízdného, výše spropitného, mýtné a příplatky).

Pokud potřebujete další pomoc s těmito postupy nebo chcete prozkoumat alternativní, přečtěte si část [Odeslat dotazy úlu přímo z příkazového řádku Hadoop](move-hive-tables.md#submit).

## <a name="load-data-to-hive-tables-by-partitions"></a><a name="#load-data"></a>Načítání dat do tabulek Hive pomocí oddílů
> [!NOTE]
> Tento úkol je obvykle pro správce.
> 
> 

NYC taxi datová sada má přirozené dělení podle měsíce, které používáme k povolení rychlejší zpracování a dotaz časy. Následující příkazy prostředí PowerShell (vydané z adresáře Hive pomocí příkazového řádku Hadoop) načítají data do tabulek trip a fare Hive, rozdělených podle měsíce.

    for /L %i IN (1,1,12) DO (hive -hiveconf MONTH=%i -f "C:\temp\sample_hive_load_data_by_partitions.hql")

**Ukázkový\_\_podregistr\_\_načítá data\_souborem partitions.hql** obsahuje následující příkazy **LOAD:**

    LOAD DATA INPATH 'wasb:///nyctaxitripraw/trip_data_${hiveconf:MONTH}.csv' INTO TABLE nyctaxidb.trip PARTITION (month=${hiveconf:MONTH});
    LOAD DATA INPATH 'wasb:///nyctaxifareraw/trip_fare_${hiveconf:MONTH}.csv' INTO TABLE nyctaxidb.fare PARTITION (month=${hiveconf:MONTH});

Počet dotazů Hive zde použitých v procesu průzkumu zahrnuje při pohledu pouze na jeden nebo dva oddíly. Ale tyto dotazy lze spustit v celé datové sady.

### <a name="show-databases-in-the-hdinsight-hadoop-cluster"></a><a name="#show-db"></a>Zobrazení databází v clusteru HDInsight Hadoop
Chcete-li zobrazit databáze vytvořené v clusteru HDInsight Hadoop v okně příkazového řádku Hadoop, spusťte v příkazovém řádku Hadoop následující příkaz:

    hive -e "show databases;"

### <a name="show-the-hive-tables-in-the-nyctaxidb-database"></a><a name="#show-tables"></a>Zobrazení tabulek Hive v databázi **nyctaxidb**
Chcete-li zobrazit tabulky v databázi **nyctaxidb,** spusťte v příkazovém řádku Hadoop následující příkaz:

    hive -e "show tables in nyctaxidb;"

Můžeme potvrdit, že tabulky jsou rozděleny na oddíly spuštěním následujícího příkazu:

    hive -e "show partitions nyctaxidb.trip;"

Zde je očekávaný výstup:

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

Podobně můžeme zajistit, že tabulka jízdného je rozdělena pomocí následujícího příkazu:

    hive -e "show partitions nyctaxidb.fare;"

Zde je očekávaný výstup:

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

## <a name="data-exploration-and-feature-engineering-in-hive"></a><a name="#explore-hive"></a>Data průzkum u a funkce inženýrství v Úlu
> [!NOTE]
> Obvykle se jedná o úlohu datového vědce.
> 
> 

Dotazy Hive můžete použít k provedení úloh průzkumu dat a inženýrských funkcí pro data načtená do tabulek Hive. Zde jsou příklady těchto úkolů:

* Zobrazení 10 nejlepších záznamů v obou tabulkách.
* Prozkoumejte rozdělení dat několika polí v různých časových oknech.
* Prozkoumejte kvalitu dat polí zeměpisné délky a zeměpisné šířky.
* Vygenerujte binární a vícetřídní klasifikační popisky na základě částky špičky.
* Vytvářejte funkce výpočtem přímých vzdáleností.

### <a name="exploration-view-the-top-10-records-in-table-trip"></a>Průzkum: Zobrazit 10 nejlepších záznamů v tabulce výlet
> [!NOTE]
> Obvykle se jedná o úlohu datového vědce.
> 
> 

Chcete-li zjistit, jak data vypadají, zkontrolujte 10 záznamů z každé tabulky. Chcete-li záznamy zkontrolovat, spusťte následující dva dotazy odděleně od řádku adresáře Hive v konzole příkazového řádku Hadoop.

Chcete-li získat 10 nejlepších záznamů v tabulce výletů od prvního měsíce:

    hive -e "select * from nyctaxidb.trip where month=1 limit 10;"

Chcete-li získat 10 nejlepších záznamů v tabulce tarifů od prvního měsíce:

    hive -e "select * from nyctaxidb.fare where month=1 limit 10;"

Záznamy můžete uložit do souboru pro pohodlné zobrazení s malou změnou předchozího dotazu:

    hive -e "select * from nyctaxidb.fare where month=1 limit 10;" > C:\temp\testoutput

### <a name="exploration-view-the-number-of-records-in-each-of-the-12-partitions"></a>Průzkum: Zobrazení počtu záznamů v každém z 12 oddílů
> [!NOTE]
> Obvykle se jedná o úlohu datového vědce.
> 
> 

Zajímavé je, jak se počet cest liší v průběhu kalendářního roku. Seskupování podle měsíce zobrazuje rozdělení cest.

    hive -e "select month, count(*) from nyctaxidb.trip group by month;"

Tento příkaz vytvoří následující výstup:

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

Zde je první sloupec měsíc a druhý je počet cest pro tento měsíc.

Celkový počet záznamů v naší datové sadě cesty můžeme také spočítat spuštěním následujícího příkazu na příkazu adresáře Hive:

    hive -e "select count(*) from nyctaxidb.trip;"

Tento příkaz dává:

    173179759
    Time taken: 284.017 seconds, Fetched: 1 row(s)

Pomocí příkazů podobných těm, které jsou zobrazeny pro datovou sadu cesty, můžeme vydat dotazy Hive z výzvy adresáře Hive pro datovou sadu tarifů k ověření počtu záznamů.

    hive -e "select month, count(*) from nyctaxidb.fare group by month;"

Tento příkaz vytvoří tento výstup:

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

Pro obě datové sady je vrácen přesně stejný počet cest za měsíc, který poskytuje první ověření, že data byla načtena správně.

Celkový počet záznamů v datové sadě tarifů můžete spočítat pomocí následujícího příkazu z řádku adresáře Hive:

    hive -e "select count(*) from nyctaxidb.fare;"

Tento příkaz dává:

    173179759
    Time taken: 186.683 seconds, Fetched: 1 row(s)

Celkový počet záznamů v obou tabulkách je také stejný, poskytuje druhé ověření, že data byla načtena správně.

### <a name="exploration-trip-distribution-by-medallion"></a>Průzkum: Rozdělení cesty medailonem
> [!NOTE]
> Tato analýza je obvykle úloha datového vědce.
> 
> 

Tento příklad identifikuje medailonky (čísla taxi) s více než 100 cesty v daném časovém období. Dotaz těží z přístupu k rozdělené tabulce, protože je podmíněn **proměnnou měsíc**oddílu . Výsledky dotazu jsou zapsány do místního souboru `C:\temp` **queryoutput.tsv**, v hlavním uzlu.

    hive -f "C:\temp\sample_hive_trip_count_by_medallion.hql" > C:\temp\queryoutput.tsv

Zde je obsah **vzorku\_\_úlu\_\_počet\_výlet podle medallion.hql** soubor pro kontrolu.

    SELECT medallion, COUNT(*) as med_count
    FROM nyctaxidb.fare
    WHERE month<=3
    GROUP BY medallion
    HAVING med_count > 100
    ORDER BY med_count desc;

Medailon v datové sadě taxi NYC identifikuje jedinečnou kabinu. Můžete zjistit, které kabiny jsou poměrně zaneprázdněné, tím, že se zeptáte, které z nich provedly více než určitý počet cest v určitém časovém období. Následující příklad identifikuje kabiny, které provedly více než sto cest v prvních třech měsících, a uloží výsledky dotazu do místního souboru **C:\temp\queryoutput.tsv**.

Zde je obsah **vzorku\_\_úlu\_\_počet\_výlet podle medallion.hql** soubor pro kontrolu.

    SELECT medallion, COUNT(*) as med_count
    FROM nyctaxidb.fare
    WHERE month<=3
    GROUP BY medallion
    HAVING med_count > 100
    ORDER BY med_count desc;

V řádku adresáře Hive spusťte následující příkaz:

    hive -f "C:\temp\sample_hive_trip_count_by_medallion.hql" > C:\temp\queryoutput.tsv

### <a name="exploration-trip-distribution-by-medallion-and-hack-license"></a>Průzkum: Výlet distribuce medailon emedilion a hack licence
> [!NOTE]
> Tento úkol je obvykle pro datovévědce.
> 
> 

Při zkoumání datové sady, často chceme prozkoumat rozdělení skupin hodnot. Tato část obsahuje příklad, jak provést tuto analýzu pro kabiny a řidiče.

**Počet\_\_výletů\_podutěk ukázkového podregistru\_podle\_souboru license.hql medallion\_** seskupuje datovou sadu tarifů na **medailona** a **hack_license**a vrátí počty jednotlivých kombinací. Zde je jeho obsah:

    SELECT medallion, hack_license, COUNT(*) as trip_count
    FROM nyctaxidb.fare
    WHERE month=1
    GROUP BY medallion, hack_license
    HAVING trip_count > 100
    ORDER BY trip_count desc;

Tento dotaz vrátí kombinace kabiny a ovladače seřazené podle sestupného počtu jízd.

Na řádku adresáře Hive spusťte:

    hive -f "C:\temp\sample_hive_trip_count_by_medallion_license.hql" > C:\temp\queryoutput.tsv

Výsledky dotazu jsou zapsány do místního souboru **C:\temp\queryoutput.tsv**.

### <a name="exploration-assessing-data-quality-by-checking-for-invalid-longitude-or-latitude-records"></a>Průzkum: Posouzení kvality dat kontrolou záznamů o neplatné zeměpisné délky nebo zeměpisné šířce
> [!NOTE]
> Obvykle se jedná o úlohu datového vědce.
> 
> 

Společným cílem analýzy průzkumných dat je vyřadit neplatné nebo chybné záznamy. Příklad v této části určuje, zda pole zeměpisné délky nebo šířky obsahují hodnotu daleko mimo oblast NYC. Vzhledem k tomu, že je pravděpodobné, že tyto záznamy mají chybnou hodnotu zeměpisné šířky, chceme je vyloučit z jakýchkoli dat, která mají být použita pro modelování.

Zde je obsah **\_vzorku\_hive quality\_assessment.hql** soubor pro kontrolu.

        SELECT COUNT(*) FROM nyctaxidb.trip
        WHERE month=1
        AND  (CAST(pickup_longitude AS float) NOT BETWEEN -90 AND -30
        OR    CAST(pickup_latitude AS float) NOT BETWEEN 30 AND 90
        OR    CAST(dropoff_longitude AS float) NOT BETWEEN -90 AND -30
        OR    CAST(dropoff_latitude AS float) NOT BETWEEN 30 AND 90);


Na řádku adresáře Hive spusťte:

    hive -S -f "C:\temp\sample_hive_quality_assessment.hql"

Argument *-S* obsažený v tomto příkazu potlačí výtisk stavové obrazovky úloh Podregistru/Snížení. Tento příkaz je užitečný, protože umožňuje, aby byl výtisk výstupu dotazu Hive čitelnější.

### <a name="exploration-binary-class-distributions-of-trip-tips"></a>Průzkum: Binární třídy distribuce výlet tipy
> [!NOTE]
> Obvykle se jedná o úlohu datového vědce.
> 
> 

Pro binární klasifikace problém popsaný v [příklady predikce úkoly](hive-walkthrough.md#mltasks) části, je užitečné vědět, zda tip byl uveden nebo ne. Toto rozdělení tipů je binární:

* daný tip (třída\_1, částka hrotu > $0)  
* bez spropitného\_(třída 0, částka spropitného = $0)

Následující **\_ukázkový\_soubor\_s hrotem podregistru ukazuje** příkaz, který chcete spustit:

    SELECT tipped, COUNT(*) AS tip_freq
    FROM
    (
        SELECT if(tip_amount > 0, 1, 0) as tipped, tip_amount
        FROM nyctaxidb.fare
    )tc
    GROUP BY tipped;

Na řádku adresáře Hive spusťte:

    hive -f "C:\temp\sample_hive_tipped_frequencies.hql"


### <a name="exploration-class-distributions-in-the-multiclass-setting"></a>Průzkum: Rozdělení tříd v nastavení více tříd
> [!NOTE]
> Obvykle se jedná o úlohu datového vědce.
> 
> 

Pro vícetřídní klasifikace problém popsaný v [příklady úlohy předpověď,](hive-walkthrough.md#mltasks) tato datová sada také půjčuje sám přírodní klasifikace předpovědět množství uvedených tipů. Můžete použít přihrádky k definování rozsahů špiček v dotazu. Chcete-li získat rozdělení tříd pro různé rozsahy špiček, použijte soubor **\_rozsah tipu\_\_\_vzorku.hql.** Zde je jeho obsah.

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

Spusťte následující příkaz z konzoly příkazového řádku Hadoop:

    hive -f "C:\temp\sample_hive_tip_range_frequencies.hql"

### <a name="exploration-compute-the-direct-distance-between-two-longitude-latitude-locations"></a>Průzkum: Vypočítat přímou vzdálenost mezi dvěma zeměpisnou šířkou
> [!NOTE]
> Obvykle se jedná o úlohu datového vědce.
> 
> 

Možná budete chtít vědět, zda existuje rozdíl mezi přímou vzdáleností mezi dvěma místy a skutečnou vzdáleností jízdy taxíku. Cestující může být méně pravděpodobné, že tip, pokud zjistí, že řidič úmyslně vzal je delší cestou.

Chcete-li zobrazit srovnání mezi skutečnou vzdáleností jízdy a [délkou Haversine](https://en.wikipedia.org/wiki/Haversine_formula) mezi dvěma body zeměpisné šířky (vzdálenost "velkého kruhu"), můžete použít goniometrické funkce dostupné v úlu:

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

V předchozím dotazu R je poloměr Země v mílích a pí je převeden na radiány. Body zeměpisné šířky zeměpisné délky jsou filtrovány, aby se odstranily hodnoty, které jsou daleko od oblasti NYC.

V tomto případě zapíšeme výsledky do adresáře s názvem **queryoutputdir**. Posloupnost následujících příkazů nejprve vytvoří tento výstupní adresář a potom spustí příkaz Hive.

Na řádku adresáře Hive spusťte:

    hdfs dfs -mkdir wasb:///queryoutputdir

    hive -f "C:\temp\sample_hive_trip_direct_distance.hql"


Výsledky dotazu jsou zapsány do devíti objektů BLOB Azure **(queryoutputdir/0000000\_0** na **queryoutputdir/000008\_0**), pod výchozím kontejnerem clusteru Hadoop.

Chcete-li zobrazit velikost jednotlivých objektů BLOB, spusťte následující příkaz z řádku adresáře Hive:

    hdfs dfs -ls wasb:///queryoutputdir

Chcete-li zobrazit obsah daného souboru, **řekněme\_000000** `copyToLocal` 0 , použijte příkaz Hadoop.

    hdfs dfs -copyToLocal wasb:///queryoutputdir/000000_0 C:\temp\tempfile

> [!WARNING]
> `copyToLocal`může být velmi pomalé pro velké soubory, a není doporučeno pro použití s nimi.  
> 
> 

Klíčovou výhodou, že tato data jsou umístěna v objektu blob Azure je, že můžeme prozkoumat data v rámci Machine Learning, pomocí modulu [import dat.][import-data]

## <a name="down-sample-data-and-build-models-in-machine-learning"></a><a name="#downsample"></a>Ukázková data dolů a sestavení modelů v Machine Learningu
> [!NOTE]
> Obvykle se jedná o úlohu datového vědce.
> 
> 

Po fázi analýzy průzkumných dat jsme nyní připraveni snížit vzorkování dat pro vytváření modelů v Machine Learningu. V této části ukážeme, jak použít dotaz Hive k down-sample data. Strojové učení pak přistupuje z modulu [Import dat.][import-data]

### <a name="down-sampling-the-data"></a>Vzorkování dat dolů
V tomto postupu jsou dva kroky. Nejprve jsme se připojit **nyctaxidb.trip** a **nyctaxidb.fare** tabulky na tři klíče, které jsou přítomny ve všech záznamech: **medailon**, **hack\_licence**, a **vyzvednutí\_datetime**. Poté vygenerujeme binární klasifikační štítek, **šikmé a**vícetřídní klasifikační štítek **třídy špičky\_**.

Chcete-li mít možnost použít data dolů vzorkované přímo z modulu [Import dat][import-data] v Machine Learning, měli byste uložit výsledky předchozího dotazu do interní tabulky Hive. V následujícím případě vytvoříme interní tabulku Hive a naplníme její obsah spojenými a vzorky dolů.

Dotaz použije standardní funkce Hive přímo ke generování následujících časových parametrů z pole **data vyzvednutí:\_**
- hodina dne
- týden v roce
- den v týdnu ('1' je zkratka pro pondělí a "7" je zkratka pro neděli)

Dotaz také generuje přímou vzdálenost mezi místy vyzvednutí a dropoff. Úplný seznam těchto funkcí naleznete v [tématu LanguageManual UDF](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF).

Dotaz pak vzorky dat dolů tak, aby výsledky dotazu se vejde do Azure Machine Learning Studio. Do studia je importováno pouze asi 1 procento původní datové sady.

Tady je obsah **\_ukázkového\_\_podregistru, který připraví soubor\_aml\_full.hql,** který připraví data pro vytváření modelů v Machine Learningu:

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

Spuštění tohoto dotazu z výzvy adresáře Hive:

    hive -f "C:\temp\sample_hive_prepare_for_aml_full.hql"

Nyní máme interní **tabulku, nyctaxidb.nyctaxi_downsampled_dataset**, ke které lze přistupovat pomocí modulu [Import dat][import-data] z Machine Learning. Kromě toho můžeme použít tuto datovou sadu pro vytváření modelů Machine Learning.  

### <a name="use-the-import-data-module-in-machine-learning-to-access-the-down-sampled-data"></a>Použití modulu Import dat v machine learningu pro přístup k datům se vzorky dolů
Chcete-li vydat dotazy Hive v modulu [Import dat][import-data] strojového učení, potřebujete přístup k pracovnímu prostoru Machine Learning. Potřebujete také přístup k pověření clusteru a jeho přidružené ho účtu úložiště.

Zde jsou některé podrobnosti o modulu [Import dat][import-data] a parametry pro vstup:

**Identifikátor URI serveru HCatalog**: Pokud je název clusteru\/ **abc123**, použijte: https: /abc123.azurehdinsight.net.

**Název uživatelského účtu Hadoop**: Uživatelské jméno zvolené pro cluster (nikoli uživatelské jméno vzdáleného přístupu).

**Heslo uživatelského účtu Hadoop**: Heslo zvolené pro cluster (nikoli heslo pro vzdálený přístup).

**Umístění výstupních dat:** Vybráno jako Azure.

**Název účtu Azure Storage**: Název výchozího účtu úložiště přidruženého ke clusteru.

**Název kontejneru Azure**: Výchozí název kontejneru pro cluster a je obvykle stejný jako název clusteru. Pro cluster s názvem **abc123**, název je abc123.

> [!IMPORTANT]
> Každá tabulka, na kterou se chceme dotazovat pomocí modulu [Import dat][import-data] v machine learningu, musí být interní tabulka.
> 
> 

Zde je postup, jak zjistit, zda tabulka **T** v databázi **D.db** je interní tabulka. V řádku adresáře Hive spusťte následující příkaz:

    hdfs dfs -ls wasb:///D.db/T

Pokud je tabulka vnitřní tabulka a je naplněna, musí se zde zobrazit její obsah.

Dalším způsobem, jak zjistit, zda je tabulka interní tabulka, je použití Průzkumníka úložiště Azure. Slouží k přechodu na výchozí název kontejneru clusteru a potom filtrovat podle názvu tabulky. Pokud se tabulka a její obsah zobrazí, potvrdí se, že se jedná o interní tabulku.

Zde je snímek obrazovky dotazu Hive a modulu [Import dat:][import-data]

![Snímek obrazovky s dotazem Hive pro modul Import dat](./media/hive-walkthrough/1eTYf52.png)

Vzhledem k tomu, že naše data se vzorky dolů jsou umístěna ve výchozím kontejneru, výsledný dotaz Hive z Machine Learning je jednoduchý. Je to jen **SELECT * Z nyctaxidb.nyctaxi\_downsampled\_data**.

Datovou sadu lze nyní použít jako výchozí bod pro vytváření modelů Machine Learning.

### <a name="build-models-in-machine-learning"></a><a name="mlmodel"></a>Vytváření modelů ve strojovém učení
Nyní můžete přejít k vytváření modelů a nasazení modelu v [Machine Learning](https://studio.azureml.net). Data jsou připravena k použití při řešení dříve zjištěných problémů s predikcí:

- **Binární klasifikace**: Chcete-li předpovědět, zda byl za cestu zaplacen tip.

  **Student používá:** Dvoustupňová logistická regrese

  a. V případě tohoto problému je **vyklopena**značka cíle (nebo třídy). Původní datová sada se vzorky dolů obsahuje několik sloupců, které jsou cílové nevracení pro tento experiment klasifikace. Zejména **\_tip třídy**, **tip\_množství**a **celkové\_množství** odhalit informace o cílový popisek, který není k dispozici v době testování. Tyto sloupce odebereme z úvahy pomocí modulu [Vybrat sloupce v datové sadě.][select-columns]

  Následující diagram znázorňuje náš experiment, který předpovídá, zda byl za danou cestu zaplacen tip:

  ![Diagram experimentu předpovědět, zda tip byla zaplacena](./media/hive-walkthrough/QGxRz5A.png)

  b. Pro tento experiment byly naše cílové distribuce štítků zhruba 1:1.

   Následující graf znázorňuje rozložení popisků tříd tipu pro problém binární klasifikace:

  ![Graf distribuce popisků tříd tipu](./media/hive-walkthrough/9mM4jlD.png)

    V důsledku toho získáme oblast pod křivkou (AUC) 0,987, jak je znázorněno na následujícím obrázku:

  ![Graf hodnoty AUC](./media/hive-walkthrough/8JDT0F8.png)

- **Klasifikace více tříd**: Předpovědět rozsah částek tipu zaplacených za cestu pomocí dříve definovaných tříd.

  **Student používá:** Vícetřídní logistická regrese

  a. Pro tento problém je náš cílový (nebo třídní) popisek **třídy tip\_**, který může mít jednu z pěti hodnot (0,1,2,3,4). Stejně jako v případě binární klasifikace máme několik sloupců, které jsou cílové nevracení pro tento experiment. Zejména **vychýlky**, **množství spropitného\_** a **celkové\_množství** odhalují informace o cílovém štítku, který není v době testování k dispozici. Tyto sloupce odebereme pomocí modulu [Vybrat sloupce v datové sadě.][select-columns]

  Následující diagram znázorňuje experiment k předvídání, ve které přihrádce pravděpodobně spadne tip. Koše jsou: Třída 0: tip = $ 0, třída 1: tip > $ 0 a tip <= $ 5, třída 2: tip > $ 5 a tip <= $ 10, třída 3: tip > $ 10 a tip <= $ 20 a třída 4: tip > $ 20.

  ![Diagram experimentu pro předpověď přihrádky pro tip](./media/hive-walkthrough/5ztv0n0.png)

  Nyní ukazujeme, jak vypadá skutečná distribuce testovací třídy. Třídy 0 a třídy 1 jsou převládající a ostatní třídy jsou vzácné.

  ![Graf distribuce testovací třídy](./media/hive-walkthrough/Vy1FUKa.png)

  b. Pro tento experiment používáme matici záměny, abychom se podívali na přesnosti předpovědi, jak je znázorněno zde:

  ![Matice záměny](./media/hive-walkthrough/cxFmErM.png)

  Zatímco přesnosti tříd y na převládající třídy jsou dobré, model neprovádí dobrou práci "učení" na vzácnější třídy.

- **Regresní úkol**: Předpovědět množství tipu zaplacené za cestu.

  **Student používá:** Posílený rozhodovací strom

  a. Pro tento problém cíl (nebo třída) popisek je **tip\_množství**. Cílové netěsnosti jsou v tomto případě: **hrot**, **\_tip třída**a **celková\_částka**. Všechny tyto proměnné odhalují informace o částce tipu, která je obvykle nedostupná v době testování. Tyto sloupce odebereme pomocí modulu [Vybrat sloupce v datové sadě.][select-columns]

  Následující diagram znázorňuje experiment předpovědět množství daného tipu:

  ![Diagram experimentu předpovědět množství tip](./media/hive-walkthrough/11TZWgV.png)

  b. U regresních problémů měříme přesnosti předpovědi pohledem na kvadratické chyby v předpovědi a koeficient stanovení:

  ![Snímek obrazovky se statistikami předpovědí](./media/hive-walkthrough/Jat9mrz.png)

  Zde je koeficient stanovení 0,709, což znamená, že přibližně 71 procent rozptylu je vysvětleno modelovými koeficienty.

> [!IMPORTANT]
> Další informace o strojovém učení a jeho přístupu k němu a jeho použití najdete v [tématu What's Machine Learning](../studio/what-is-machine-learning.md). Galerie Azure [AI](https://gallery.cortanaintelligence.com/) navíc pokrývá celou škálu experimentů a poskytuje podrobný úvod do škály možností machine learningu.
> 
> 

## <a name="license-information"></a>Informace o licenci
Tento ukázkový návod a jeho doprovodné skripty jsou sdíleny společností Microsoft pod licencí MIT. Další informace naleznete v souboru **LICENSE.txt** v adresáři ukázkového kódu na GitHubu.

## <a name="references"></a>Odkazy
• [Andrés Monroy NYC Taxi Výlety Ke stažení Page](https://www.andresmh.com/nyctaxitrips/)  
• [FOILing NYC taxi výlet data Chris Whong](https://chriswhong.com/open-data/foil_nyc_taxi/)   
• [NYC Taxi a limuzína Komise výzkum a statistiky](https://www1.nyc.gov/site/tlc/about/tlc-trip-record-data.page)

[2]: ./media/hive-walkthrough/output-hive-results-3.png
[11]: ./media/hive-walkthrough/hive-reader-properties.png
[12]: ./media/hive-walkthrough/binary-classification-training.png
[13]: ./media/hive-walkthrough/create-scoring-experiment.png
[14]: ./media/hive-walkthrough/binary-classification-scoring.png
[15]: ./media/hive-walkthrough/amlreader.png

<!-- Module References -->
[select-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/



