---
title: Zkoumání dat v clusteru Hadoop a vytváření modelů ve službě Azure Machine Learning | Dokumentace Microsoftu
description: Vědecké zpracování týmových dat pomocí začátku do konce scénář, když cluster systému HDInsight Hadoop k vytvoření a nasazení modelu.
services: machine-learning,hdinsight
documentationcenter: ''
author: deguhath
manager: cgronlun
editor: cgronlun
ms.assetid: e9e76c91-d0f6-483d-bae7-2d3157b86aa0
ms.service: machine-learning
ms.component: team-data-science-process
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/29/2017
ms.author: deguhath
ms.openlocfilehash: 09ca6fdc40aec84bcc7523bae0dee348d00f6d9f
ms.sourcegitcommit: 5843352f71f756458ba84c31f4b66b6a082e53df
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/01/2018
ms.locfileid: "47586117"
---
# <a name="the-team-data-science-process-in-action-use-azure-hdinsight-hadoop-clusters"></a>Vědecké zpracování týmových dat v akci: použití Azure HDInsight Hadoop clusterů
V tomto názorném postupu používáme [vědecké zpracování týmových dat (TDSP)](overview.md) ve scénáři začátku do konce. Používáme [clusteru Azure HDInsight Hadoop](https://azure.microsoft.com/services/hdinsight/) k ukládání, prozkoumat a funkce analýzu dat z veřejně dostupných [cesty taxíkem NYC](http://www.andresmh.com/nyctaxitrips/) datovou sadu a na nižší data. Pro zpracování víc tříd a binární klasifikace a úlohy prediktivní regrese, jsme integrovali modely dat pomocí Azure Machine Learning. 

Názorný postup ukazuje, jak zpracovávat větší datové sady, naleznete v tématu [vědecké zpracování týmových dat – pomocí Azure HDInsight Hadoop clusterů v datové sadě 1 TB](hive-criteo-walkthrough.md).

IPython notebook můžete také použít k provádění úloh, které jsou uvedené v tomto návodu, který používá datovou sadu 1 TB. Další informace najdete v tématu [Criteo návod používání Hive ODBC připojení](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/iPythonNotebooks/machine-Learning-data-science-process-hive-walkthrough-criteo.ipynb).

## <a name="dataset"></a>Popis cesty taxíkem NYC datové sady
Data o jízdách taxislužby NYC je přibližně 20 GB komprimované hodnot oddělených čárkami (CSV) souborů (nekomprimovaný ~ 48 GB). Má více než milion 173 jednotlivé cesty a zahrnuje tarify placené pro každou cestu. Každý záznam o jízdách zahrnuje odběr a dropoff umístění a čas, anonymizované hack (ovladač) číslo licence a Medailon počet (jedinečné ID taxislužby). Data v roce 2013 zahrnuje všechny cesty a je dostupné pro každý měsíc následující dvě datové sady:

- Soubory CSV trip_data obsahují podrobnosti o jízdách. Jedná se o počet cestujících, odběr a dropoff body, doba trvání cesty a délka cesty. Tady je několik ukázkových záznamů:
   
        medallion,hack_license,vendor_id,rate_code,store_and_fwd_flag,pickup_datetime,dropoff_datetime,passenger_count,trip_time_in_secs,trip_distance,pickup_longitude,pickup_latitude,dropoff_longitude,dropoff_latitude
        89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,1,N,2013-01-01 15:11:48,2013-01-01 15:18:10,4,382,1.00,-73.978165,40.757977,-73.989838,40.751171
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-06 00:18:35,2013-01-06 00:22:54,1,259,1.50,-74.006683,40.731781,-73.994499,40.75066
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-05 18:49:41,2013-01-05 18:54:23,1,282,1.10,-74.004707,40.73777,-74.009834,40.726002
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:54:15,2013-01-07 23:58:20,2,244,.70,-73.974602,40.759945,-73.984734,40.759388
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:25:03,2013-01-07 23:34:24,1,560,2.10,-73.97625,40.748528,-74.002586,40.747868
- Soubory CSV trip_fare obsahovat podrobnosti o tarif placené pro každou cestu. To zahrnuje typ platby, částka tarif, příplatek za a daní, tipy a mýtné a celkové částky zaplacené. Tady je několik ukázkových záznamů:
   
        medallion, hack_license, vendor_id, pickup_datetime, payment_type, fare_amount, surcharge, mta_tax, tip_amount, tolls_amount, total_amount
        89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,2013-01-01 15:11:48,CSH,6.5,0,0.5,0,0,7
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-06 00:18:35,CSH,6,0.5,0.5,0,0,7
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-05 18:49:41,CSH,5.5,1,0.5,0,0,7
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:54:15,CSH,5,0.5,0.5,0,0,6
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:25:03,CSH,9.5,0.5,0.5,0,0,10.5

Jedinečný klíč pro připojení o jízdách\_a dat o jízdách\_tarif se skládá z polí: medailonu, najděte, co je\_licence a vyzvednutí\_data a času. Chcete-li získat všechny podrobnosti relevantní pro konkrétní cesty, stačí Seznamte se s tyto tři klíče.

## <a name="mltasks"></a>Příklady úloh predikcí
Určete druh předpovědi, které mají být založené na analýze dat. Díky tomu vysvětlení úloh, které potřebujete k zahrnutí do procesu. Tady jsou tři příklady předpovědi problémy, které jsme v tomto názorném postupu vyřešit. Tyto jsou založeny na *tip\_částka*:

- **Binární klasifikace**: předpovědět, jestli byl tip placené cesty. To znamená *tip\_částka* , který je větší než 0 USD je kladné příklad, zatímco *tip\_částka* $ 0 je záporná příklad.
   
        Class 0: tip_amount = $0
        Class 1: tip_amount > $0
- **Klasifikace víc tříd**: předpověď rozsah tip částky zaplacené pro cestu. Doporučujeme rozdělit *tip\_částka* do pěti tříd:
   
        Class 0: tip_amount = $0
        Class 1: tip_amount > $0 and tip_amount <= $5
        Class 2: tip_amount > $5 and tip_amount <= $10
        Class 3: tip_amount > $10 and tip_amount <= $20
        Class 4: tip_amount > $20
- **Úloha regrese**: předpověď částky spropitného placené cesty.  

## <a name="setup"></a>Nastavení clusteru HDInsight Hadoop pro pokročilou analýzu
> [!NOTE]
> Obvykle se jedná úlohu správy.
> 
> 

Můžete nastavit prostředí Azure pro pokročilé analýzy, která používá cluster služby HDInsight ve třech krocích:

1. [Vytvoření účtu úložiště](../../storage/common/storage-quickstart-create-account.md): Tento účet úložiště se používá k ukládání dat ve službě Azure Blob storage. Data používaná v clusterech HDInsight se také nachází zde.
2. [Přizpůsobení clusterů Azure HDInsight Hadoop pro pokročilé analýzy proces a technologie](customize-hadoop-cluster.md). Tento krok vytvoří cluster HDInsight Hadoop s 64bitovým kompilátorem Anaconda Python 2.7 nainstalované na všech uzlech. Existují dva důležité kroky a mějte na paměti při přizpůsobení vašeho clusteru HDInsight.
   
   * Nezapomeňte propojit účet úložiště vytvořený v kroku 1 s vaším clusterem HDInsight při jeho vytváření. Tomuto účtu úložiště přistupuje k datům, která je zpracována v rámci clusteru.
   * Po vytvoření clusteru povolte vzdálený přístup k hlavnímu uzlu clusteru. Přejděte **konfigurace** kartu a vyberte **povolit vzdálené**. Tento krok určuje uživatelská pověření sloužící ke vzdálenému přihlášení.
3. [Vytvoření pracovního prostoru Azure Machine Learning](../studio/create-workspace.md): Tento pracovní prostor použijete k sestavení modelů strojového učení. Tato úloha je určeno po dokončení počáteční zkoumání a dolů – vzorkování, pomocí clusteru HDInsight.

## <a name="getdata"></a>Získávají data z veřejných zdrojů
> [!NOTE]
> Obvykle se jedná úlohu správy.
> 
> 

Pro kopírování [cesty taxíkem NYC](http://www.andresmh.com/nyctaxitrips/) datovou sadu do počítače z veřejné umístění, použijte některou z metod popsaných v [přesun dat do a z úložiště objektů Blob v Azure](move-azure-blob.md).

Zde zjistíte, jak pomocí AzCopy můžete přenášet soubory obsahující data. Ke stažení a instalaci AzCopy, postupujte podle pokynů na adrese [Začínáme s nástrojem příkazového řádku azcopy](../../storage/common/storage-use-azcopy.md).

1. Z okna příkazového řádku, spusťte následující příkazy AzCopy a nahrazení *< path_to_data_folder >* požadovaný cíl:

        "C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:https://nyctaxitrips.blob.core.windows.net/data /Dest:<path_to_data_folder> /S

1. Po dokončení kopírování, zobrazí se celkem 24 zip soubory ve složce data zvolili. Rozbalte stažené soubory do stejného adresáře na místním počítači. Poznamenejte si složku, ve kterém jsou umístěny dekomprimovaných souborů. Tato složka se označuje jako *< cesta\_k\_unzipped_data\_soubory\>*  v jaké způsobem.

## <a name="upload"></a>Nahrát data do výchozího kontejneru cluster HDInsight Hadoop
> [!NOTE]
> Obvykle se jedná úlohu správy.
> 
> 

V následující příkazy AzCopy a nahradit skutečnými hodnotami, které jste zadali při vytváření clusteru Hadoop následující parametry a rozbalení datových souborů.

* ***< Path_to_data_folder >*** adresáři (spolu s cesta) v počítači, který obsahuje rozzipovaný datové soubory.  
* ***<storage account name of Hadoop cluster>*** Účet úložiště spojené s vaším clusterem HDInsight.
* ***<default container of Hadoop cluster>*** Výchozí kontejner používat váš cluster. Všimněte si, že název výchozího kontejneru je obvykle stejný název jako samotného clusteru. Například pokud clusteru se nazývá "abc123.azurehdinsight.net", je výchozí kontejner abc123.
* ***<storage account key>*** Klíč pro účet úložiště používané ve vašem clusteru.

Z příkazového řádku nebo v okně prostředí Windows PowerShell spusťte následující dva příkazy AzCopy.

Tento příkaz odešle data o jízdách ***nyctaxitripraw*** adresáře ve výchozím kontejneru Hadoop cluster.

        "C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:<path_to_unzipped_data_files> /Dest:https://<storage account name of Hadoop cluster>.blob.core.windows.net/<default container of Hadoop cluster>/nyctaxitripraw /DestKey:<storage account key> /S /Pattern:trip_data_*.csv

Tento příkaz odešle datový tarif na ***nyctaxifareraw*** adresáře ve výchozím kontejneru Hadoop cluster.

        "C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:<path_to_unzipped_data_files> /Dest:https://<storage account name of Hadoop cluster>.blob.core.windows.net/<default container of Hadoop cluster>/nyctaxifareraw /DestKey:<storage account key> /S /Pattern:trip_fare_*.csv

Data by měla být nyní v úložišti objektů Blob a jste připravení využívat v rámci clusteru HDInsight.

## <a name="#download-hql-files"></a>Přihlaste se k hlavnímu uzlu clusteru Hadoop a připravit pro analýzu dat průzkumného testování
> [!NOTE]
> Obvykle se jedná úlohu správy.
> 
> 

Chcete-li získat přístup k hlavnímu uzlu clusteru pro analýzu dat průzkumné a dolů – vzorkování dat, postupujte podle postupu uvedeného v [přístup k hlavnímu uzlu clusteru Hadoop](customize-hadoop-cluster.md).

V tomto názorném postupu používáme primárně dotazy napsané [Hive](https://hive.apache.org/), SQL jako dotazovací jazyk, provádět předběžnou dat průzkumů. Dotazy Hive jsou uloženy v souborech .hql. Jsme pak dolů – ukázka tato data se použije v Machine Learning pro vytváření modelů.

Příprava clusteru pro analýzu dat průzkumného testování, stahovat soubory .hql obsahující příslušných skriptů Hive z [Githubu](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts) do místního adresáře (C:\temp) hlavního uzlu. Chcete-li to provést, otevřete příkazový řádek z v rámci hlavního uzlu clusteru a spusťte následující dva příkazy:

    set script='https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/DataScienceProcess/DataScienceScripts/Download_DataScience_Scripts.ps1'

    @powershell -NoProfile -ExecutionPolicy unrestricted -Command "iex ((new-object net.webclient).DownloadString(%script%))"

Tyto dva příkazy stáhnout všechny potřebné v tomto názorném průvodci k místnímu adresáři soubory .hql ***C:\temp&#92;***  v hlavní uzel.

## <a name="#hive-db-tables"></a>Vytvořit databázi Hive a tabulky dělené podle kategorie month
> [!NOTE]
> Obvykle se jedná úlohu správy.
> 
> 

Nyní jste připraveni k vytvoření tabulky Hive pro datovou sadu NYC taxislužby.
V k hlavnímu uzlu clusteru Hadoop otevřete příkazový řádek systému Hadoop v klientských počítačích k hlavnímu uzlu. Zadejte adresář Hive spuštěním následujícího příkazu:

    cd %hive_home%\bin

> [!NOTE]
> Spustit všechny příkazy Hive v tomto názorném postupu z koše Hive / directory řádku. To řeší problémy cestu automaticky. Používáme termíny "Hive directory řádku", "Hive bin / directory řádku" a "Příkazového řádku Hadoopu" Zaměnitelně v tomto názorném postupu.
> 
> 

Z adresáře řádku Hive spusťte následující příkaz v příkazovém řádku Hadoopu hlavního uzlu. Odešle se dotaz Hive pro vytvoření databáze Hive a tabulky:

    hive -f "C:\temp\sample_hive_create_db_and_tables.hql"

Tady je obsah **C:\temp\sample\_hive\_vytvořit\_db\_a\_tables.hql** souboru. Tím se vytvoří databáze Hive **nyctaxidb**, tabulkách a **o jízdách** a **tarif**.

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

* **o jízdách** tabulka obsahuje podrobnosti o jízdách každý jízdní (podrobnosti o ovladači, čas odběr, vzdálenost odezvy a časy).
* **Tarif** tabulka obsahuje podrobnosti o tarif (tarif, částka tip, mýtné a příplatků k poplatkům).

Pokud potřebujete jakékoli další pomoc s tyto postupy, nebo chcete prozkoumat alternativní ty, najdete v části [dotazy Hive odeslat přímo z příkazového řádku Hadoopu](move-hive-tables.md#submit).

## <a name="#load-data"></a>Načtení dat do tabulek Hive oddíly
> [!NOTE]
> Obvykle se jedná úlohu správy.
> 
> 

Datová sada taxislužby NYC má přirozené dělení podle měsíce, které používáme umožňuje rychlejší zpracování a dotazu. Následující příkazy Powershellu (vydaného adresáři Hive pomocí příkazového řádku Hadoopu) načtení dat do cesty a jízdenky tabulek Hive, rozdělený podle měsíce.

    for /L %i IN (1,1,12) DO (hive -hiveconf MONTH=%i -f "C:\temp\sample_hive_load_data_by_partitions.hql")

**Ukázka\_hive\_načíst\_data\_podle\_partitions.hql** soubor obsahuje následující **NAČÍST** příkazy:

    LOAD DATA INPATH 'wasb:///nyctaxitripraw/trip_data_${hiveconf:MONTH}.csv' INTO TABLE nyctaxidb.trip PARTITION (month=${hiveconf:MONTH});
    LOAD DATA INPATH 'wasb:///nyctaxifareraw/trip_fare_${hiveconf:MONTH}.csv' INTO TABLE nyctaxidb.fare PARTITION (month=${hiveconf:MONTH});

Všimněte si, že počet dotazů Hive tady použít v procesu průzkumu zahrnují podíváme jenom jeden nebo dva oddíly. Ale tyto dotazy lze spouštět napříč celou datovou sadu.

### <a name="#show-db"></a>Zobrazení databází v clusteru HDInsight Hadoop
Chcete-li zobrazit databáze vytvořená v clusteru HDInsight Hadoop v okně příkazového řádku systému Hadoop, spusťte následující příkaz v příkazovém řádku Hadoop:

    hive -e "show databases;"

### <a name="#show-tables"></a>Zobrazení tabulek Hive v **nyctaxidb** databáze
Chcete-li zobrazit tabulky v **nyctaxidb** databáze, spusťte následující příkaz v příkazovém řádku Hadoop:

    hive -e "show tables in nyctaxidb;"

Můžeme potvrdit, že tabulky dělí podle následujícího příkazu:

    hive -e "show partitions nyctaxidb.trip;"

Tady je očekávaný výstup:

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

Podobně můžete zajišťujeme, že tabulka tarif je rozdělená na oddíly spuštěním následujícího příkazu:

    hive -e "show partitions nyctaxidb.fare;"

Tady je očekávaný výstup:

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

## <a name="#explore-hive"></a>Zkoumání dat a vytváření funkcí v Hivu
> [!NOTE]
> Obvykle se jedná o úlohu mezi odborníky přes data.
> 
> 

Dotazy Hive můžete použít k provedení zkoumání dat a funkce technické úkoly pro data načtena do tabulek Hive. Tady jsou příklady těchto úkolů:

* Zobrazení prvních 10 záznamů v obou tabulkách.
* Prozkoumejte data distribuce několik polí v různých časových oken.
* Prozkoumejte data kvality zeměpisnou šířku a délku pole.
* Generovat popisky klasifikace binární a víc tříd na základě doby, tip.
* Generovat funkce výpočtem vzdálenosti přímé o jízdách.

### <a name="exploration-view-the-top-10-records-in-table-trip"></a>Zkoumání: Zobrazte prvních 10 záznamů v tabulce cesty
> [!NOTE]
> Obvykle se jedná o úlohu mezi odborníky přes data.
> 
> 

Chcete-li zjistit, jak data funguje, zkontrolujte 10 záznamů z každé tabulky. Chcete-li prověřit záznamy, spusťte následující dotazy dvě samostatně z příkazového řádku adresář Hive v konzole nástroje příkazového řádku Hadoopu.

Pokud chcete získat prvních 10 záznamů v tabulce cesty od prvního měsíce:

    hive -e "select * from nyctaxidb.trip where month=1 limit 10;"

Pokud chcete získat prvních 10 záznamů v tabulce tarif během prvního měsíce:

    hive -e "select * from nyctaxidb.fare where month=1 limit 10;"

Záznamy můžete uložit do souboru pro pohodlné zobrazení. Menší změnu, která předchozí dotaz toho dosahuje:

    hive -e "select * from nyctaxidb.fare where month=1 limit 10;" > C:\temp\testoutput

### <a name="exploration-view-the-number-of-records-in-each-of-the-12-partitions"></a>Zkoumání: Zobrazte několik záznamů v každé z 12 oddíly
> [!NOTE]
> Obvykle se jedná o úlohu mezi odborníky přes data.
> 
> 

Zajímavé je, jak se liší počet cest v kalendářním roce. Seskupení podle kategorie month zobrazuje distribuci zkracuje dobu odezvy.

    hive -e "select month, count(*) from nyctaxidb.trip group by month;"

Tento produkt nám nabízí následující výstup:

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

Tady první sloupec je měsíc a druhý je počet cest pro daný měsíc.

Můžete také počítáme celkový počet záznamů v naší datové sadě cesty spuštěním následujícího příkazu do příkazového řádku adresář Hive:

    hive -e "select count(*) from nyctaxidb.trip;"

To poskytuje:

    173179759
    Time taken: 284.017 seconds, Fetched: 1 row(s)

Pomocí příkazů, které jsou podobné těm, které jsou zobrazeny pro sadu dat o jízdách, nám můžete posílat dotazy Hive z příkazového řádku adresář Hive pro datovou sadu tarif ověření počet záznamů.

    hive -e "select month, count(*) from nyctaxidb.fare group by month;"

Tento produkt nám nabízí následující výstup:

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

Všimněte si, že se vrátí přesně stejný počet cest za měsíc pro oba datové sady. To poskytuje první ověření správně načíst data.

Celkový počet záznamů v datové sadě tarif můžete počítat s použitím následujícího příkazu z příkazového řádku adresář Hive:

    hive -e "select count(*) from nyctaxidb.fare;"

To poskytuje:

    173179759
    Time taken: 186.683 seconds, Fetched: 1 row(s)

Celkový počet záznamů v obou tabulkách je také stejný. To poskytuje druhé ověření správně načíst data.

### <a name="exploration-trip-distribution-by-medallion"></a>Zkoumání: Distribuce latence podle Medailon
> [!NOTE]
> Obvykle se jedná o úlohu mezi odborníky přes data.
> 
> 

V tomto příkladu identifikuje medallions (taxislužby čísla) s více než 100 zkracuje dobu odezvy v daném časovém období. Dotaz využívá výhod přístupu dělenou tabulku, protože je záleží oddíl proměnnou **měsíc**. Výsledky dotazu jsou zapisovány do místního souboru **queryoutput.tsv**v `C:\temp` hlavního uzlu.

    hive -f "C:\temp\sample_hive_trip_count_by_medallion.hql" > C:\temp\queryoutput.tsv

Tady je obsah **ukázka\_hive\_o jízdách\_počet\_podle\_medallion.hql** soubor ke kontrole.

    SELECT medallion, COUNT(*) as med_count
    FROM nyctaxidb.fare
    WHERE month<=3
    GROUP BY medallion
    HAVING med_count > 100
    ORDER BY med_count desc;

Medailon v sadě dat taxislužby NYC identifikuje jedinečné souboru cab. Můžete určit, které soubory CAB jsou relativně zaneprázdněný požádá ty, které provedli větší než počet cest v konkrétním časovém období. Následující příklad určuje soubory CAB, které provádí více než 100 zkracuje dobu odezvy v první tři měsíce a uloží do místního souboru, výsledky dotazu **C:\temp\queryoutput.tsv**.

Tady je obsah **ukázka\_hive\_o jízdách\_počet\_podle\_medallion.hql** soubor ke kontrole.

    SELECT medallion, COUNT(*) as med_count
    FROM nyctaxidb.fare
    WHERE month<=3
    GROUP BY medallion
    HAVING med_count > 100
    ORDER BY med_count desc;

Z adresáře řádku Hive spusťte následující příkaz:

    hive -f "C:\temp\sample_hive_trip_count_by_medallion.hql" > C:\temp\queryoutput.tsv

### <a name="exploration-trip-distribution-by-medallion-and-hack-license"></a>Průzkumu: Distribuce latence podle Medailon a hack licence
> [!NOTE]
> Obvykle se jedná o úlohu mezi odborníky přes data.
> 
> 

Při prohlížení datové sady, často chcete zkontrolovat počet výskytů co skupin hodnot. Tato část poskytuje příklad toho, jak to provést u souborů CAB a ovladače.

**Ukázka\_hive\_o jízdách\_počet\_podle\_Medailon\_license.hql** skupin souborů na datovou sadu tarif **Medailon** a **hack_license**a vrátí počet každá kombinace. Zde jsou jeho obsah:

    SELECT medallion, hack_license, COUNT(*) as trip_count
    FROM nyctaxidb.fare
    WHERE month=1
    GROUP BY medallion, hack_license
    HAVING trip_count > 100
    ORDER BY trip_count desc;

Tento dotaz vrátí kombinace souboru cab a ovladače, seřazených podle sestupných počet cest.

Z adresáře řádku Hive spusťte:

    hive -f "C:\temp\sample_hive_trip_count_by_medallion_license.hql" > C:\temp\queryoutput.tsv

Výsledky dotazu jsou zapisovány do místního souboru **C:\temp\queryoutput.tsv**.

### <a name="exploration-assessing-data-quality-by-checking-for-invalid-longitude-or-latitude-records"></a>Zkoumání: Hodnocení kvality dat tak, že zkontrolujete neplatnou zeměpisnou délku nebo záznamy zeměpisné šířky
> [!NOTE]
> Obvykle se jedná o úlohu mezi odborníky přes data.
> 
> 

Běžné cílem analýzy dat průzkumného testování je odstraňování plevele si neplatná nebo chybné záznamy. Příklad v této části určuje, zda šířky nebo délky pole obsahovat hodnotu úplně mimo oblast NYC. Protože je pravděpodobné, že tyto záznamy mají hodnotu chybné zeměpisné šířky, chcete vyloučit z všechna data, která má být použito pro modelování.

Tady je obsah **ukázka\_hive\_kvality\_assessment.hql** soubor ke kontrole.

        SELECT COUNT(*) FROM nyctaxidb.trip
        WHERE month=1
        AND  (CAST(pickup_longitude AS float) NOT BETWEEN -90 AND -30
        OR    CAST(pickup_latitude AS float) NOT BETWEEN 30 AND 90
        OR    CAST(dropoff_longitude AS float) NOT BETWEEN -90 AND -30
        OR    CAST(dropoff_latitude AS float) NOT BETWEEN 30 AND 90);


Z adresáře řádku Hive spusťte:

    hive -S -f "C:\temp\sample_hive_quality_assessment.hql"

*-S* argument zahrnuté v tomto příkazu potlačí výtisku obrazovku stavu úloh mapování/zmenšování Hive. To je užitečné, protože to ztěžuje obrazovky tisku z výstup dotazu Hive lépe čitelný.

### <a name="exploration-binary-class-distributions-of-trip-tips"></a>Zkoumání: Binární třída distribuce latence tipy
> [!NOTE]
> Obvykle se jedná o úlohu mezi odborníky přes data.
> 
> 

Binární klasifikace problému uvedených v [příklady úloh předpovědi](hive-walkthrough.md#mltasks) části, je užitečné vědět, zda byl zadán tip, nebo ne. Toto rozdělení tipy je binární:

* Zadaný Tip (třídy 1, tip\_amount > 0 USD)  
* tip (třída 0, tip\_částka = 0 USD)

Následující **ukázka\_hive\_šikmý\_frequencies.hql** souboru toho dosahuje:

    SELECT tipped, COUNT(*) AS tip_freq
    FROM
    (
        SELECT if(tip_amount > 0, 1, 0) as tipped, tip_amount
        FROM nyctaxidb.fare
    )tc
    GROUP BY tipped;

Z adresáře řádku Hive spusťte:

    hive -f "C:\temp\sample_hive_tipped_frequencies.hql"


### <a name="exploration-class-distributions-in-the-multiclass-setting"></a>Zkoumání: Rozdělení třídy v nastavení víc tříd
> [!NOTE]
> Obvykle se jedná o úlohu mezi odborníky přes data.
> 
> 

Pro problém klasifikace víc tříd uvedených v [příklady úloh předpovědi](hive-walkthrough.md#mltasks) části tato datová sada také různě přirozené klasifikace odhadnout množství tipy zadaný. Přihrádky můžeme použít k definování tip rozsahů v dotazu. Třída distribuce pro různé rozsahy tip, použijte **ukázka\_hive\_tip\_rozsah\_frequencies.hql** souboru. Zde jsou jeho obsah.

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

Spusťte následující příkaz z příkazového řádku konzoly Hadoop:

    hive -f "C:\temp\sample_hive_tip_range_frequencies.hql"

### <a name="exploration-compute-the-direct-distance-between-two-longitude-latitude-locations"></a>Zkoumání: Compute přímé vzdálenost mezi dvěma umístěními zeměpisná délka a šířka
> [!NOTE]
> Obvykle se jedná o úlohu mezi odborníky přes data.
> 
> 

Můžete chtít vědět, jestli je rozdíl mezi přímé vzdálenost mezi dvěma umístěními a vzdálenost skutečné cesty taxislužby. Osobní může být méně pravděpodobné, že tip, pokud se zjistit, že ovladač je záměrně provedenou je delší trasy.

Chcete-li zobrazit srovnání vzdálenost skutečné cesty a [Haversine vzdálenost](http://en.wikipedia.org/wiki/Haversine_formula) mezi dvěma body zeměpisná délka a šířka (vzdálenost "velké kruh"), můžete použít k dispozici trigonometrické funkce v rámci Hive:

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

V předchozím dotazu jazyka R je radius Earth v mil a pi je převést na radiány. Všimněte si, že body zeměpisná délka a šířka jsou filtrovány odebrat hodnoty, které jsou daleko od oblasti NYC.

V tomto případě jsme napsali výsledky na adresář s názvem **queryoutputdir**. Pořadí následující příkazy nejprve vytvoří tento výstupní adresář a pak spustí příkaz Hive.

Z adresáře řádku Hive spusťte:

    hdfs dfs -mkdir wasb:///queryoutputdir

    hive -f "C:\temp\sample_hive_trip_direct_distance.hql"


Výsledky dotazu se zapisují do devět objekty BLOB Azure (**queryoutputdir/000000\_0** k **queryoutputdir/000008\_0**), v rámci výchozího kontejneru Hadoop cluster.

Pokud chcete zobrazit množství jednotlivých objektů BLOB, spusťte následující příkaz z příkazového řádku adresář Hive:

    hdfs dfs -ls wasb:///queryoutputdir

Pokud chcete zobrazit obsah daného souboru, Řekněme, že **000000\_0**, používat Hadoop `copyToLocal` příkazu.

    hdfs dfs -copyToLocal wasb:///queryoutputdir/000000_0 C:\temp\tempfile

> [!WARNING]
> `copyToLocal` může být velmi pomalé pro velké soubory a nedoporučuje se používat pro použití s nimi.  
> 
> 

Hlavní výhodou, že máte tato data se nacházejí v objektu blob Azure je, že jsme prozkoumat data v Machine Learning pomocí [Import dat] [ import-data] modulu.

## <a name="#downsample"></a>Seznam – ukázková data a vytvářet modely ve službě Machine Learning
> [!NOTE]
> Obvykle se jedná o úlohu mezi odborníky přes data.
> 
> 

Po fázi analýzy průzkumné data můžeme nyní připraveni na nižší data pro vytváření modelů ve službě Machine Learning. V této části ukážeme, jak používat dotazy Hive na nižší data. Machine Learning pak přistupuje z [Import dat] [ import-data] modulu.

### <a name="down-sampling-the-data"></a>Seznam – vzorkování dat
Existují dva kroky v tomto postupu. Nejdřív se nám připojit **nyctaxidb.trip** a **nyctaxidb.fare** tabulek na tři klíče, které jsou k dispozici ve všech záznamech: **Medailon**, **hack\_ licence**, a **vyzvednutí\_data a času**. Potom se vygeneruje binární klasifikační popisek, **šikmý**a popisek klasifikace víc tříd **tip\_třídy**.

Abyste mohli používat předvýpočtem zredukovaných data přímo z [Import dat] [ import-data] modulu ve službě Machine Learning, měli byste uložit výsledky předchozí dotaz do interní tabulky Hive. V následující vytvoříme interní tabulky Hive a naplnit daty připojeného k a předvýpočtem zredukovaných jeho obsah.

Dotaz použije standardní funkce Hive přímo ke generování těchto věcí **vyzvednutí\_data a času** pole:
- hodiny dne
- týden roku
- den v týdnu (1 zastupuje pondělí a 7 zastupuje neděle)

Přímé vzdálenost mezi umístěními odběr a dropoff také vygeneruje dotaz. Úplný seznam takových funkcí, naleznete v tématu [LanguageManual UDF](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF).

Dotaz potom dolů samples data tak, že výsledky dotazu můžete začlenit do Azure Machine Learning Studio. Pouze asi 1 % z původní datové sady je importovat do nástroje studio.

Tady je obsah **ukázka\_hive\_Příprava\_pro\_aml\_full.hql** soubor, který připraví datového modelu vytvářet ve službě Machine Learning:

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

Dotaz můžete spustit z příkazového řádku adresář Hive:

    hive -f "C:\temp\sample_hive_prepare_for_aml_full.hql"

Teď máme interní tabulka **nyctaxidb.nyctaxi_downsampled_dataset**, který je přístupný pomocí [Import dat] [ import-data] modulu ze služby Machine Learning. Kromě toho můžeme použít tuto datovou sadu pro vytváření modelů Machine Learning.  

### <a name="use-the-import-data-module-in-machine-learning-to-access-the-down-sampled-data"></a>Můžete získat přístup k datům předvýpočtem zredukovaných pomocí modulu Import dat ve službě Machine Learning
K vydávání dotazů Hive v [Import dat] [ import-data] modul Machine Learning, kterým potřebujete přístup pracovního prostoru Machine Learning. Potřebujete také přístup k přihlašovacím údajům clusteru a jeho přidružený účet úložiště.

Tady jsou některé podrobnosti o [Import dat] [ import-data] modulu a parametry pro vstup:

**Identifikátor URI serveru HCatalog**: Pokud je název clusteru **abc123**, to je jednoduše: https://abc123.azurehdinsight.net.

**Název uživatelského účtu systému Hadoop**: uživatelské jméno, které jste zvolili pro cluster (nikoli název vzdáleného přístupu uživatele).

**Heslo účtu ser Hadoop**: heslo pro cluster (ne heslo vzdáleného přístupu).

**Umístění výstupních dat**: to je vybrán jako Azure.

**Název účtu služby Azure storage**: název výchozí účet úložiště přidružené ke clusteru.

**Název kontejneru Azure**: Toto je výchozí název kontejneru clusteru a je obvykle stejný jako název clusteru. Volá se, pro cluster **abc123**, toto je abc123.

> [!IMPORTANT]
> Všechny tabulky, které bychom chtěli provádět dotazy pomocí [Import dat] [ import-data] interní tabulky musí být modul ve službě Machine Learning.
> 
> 

Tady je postup, chcete-li zjistit, jestli tabulku **T** v databázi **D.db** je interní tabulku. Z adresáře řádku Hive spusťte následující příkaz:

    hdfs dfs -ls wasb:///D.db/T

Pokud je tabulka interní tabulku a je vyplněný, musí tady zobrazit jeho obsah.

Dalším způsobem, jak určit, zda tabulka je interní tabulku je použití Průzkumníka služby Azure Storage. Můžete přejít na výchozí název kontejneru clusteru a potom filtrovat podle názvu tabulky. Pokud tabulka a její obsah uveden, tím potvrdíte, že je interní tabulku.

Zde je snímek obrazovky s dotazem Hive a [Import dat] [ import-data] modul:

![Snímek obrazovky z dotazu Hive v modulu Import dat](./media/hive-walkthrough/1eTYf52.png)

Vzhledem k tomu naše předvýpočtem zredukovaných data nachází ve výchozím kontejneru, výsledný dotaz Hivu ze služby Machine Learning je velmi snadné. Jsou tam jen **vybrat * z nyctaxidb.nyctaxi\_po převzorkování dolů\_data**.

Datovou sadu můžete teď použít jako výchozí bod pro vytváření modelů Machine Learning.

### <a name="mlmodel"></a>Vytváření modelů Machine Learning.
Teď můžete přejít k vytváření modelů a nasazení modelů v [Machine Learning](https://studio.azureml.net). Data jsou připravené, abyste mohli používat v řeší problémy předpovědi dřív identifikovali:

- **Binární klasifikace**: předpovědět, zda je či není tip byla zaplacena cesty.

  **Student používá:** logistické regrese Two-class

  a. Pro daný problém a cíle (nebo třídy) popisek je **šikmý**. Původní datové sady předvýpočtem zredukovaných má několik sloupců, které jsou cílového únikům pro tento experiment klasifikace. Zejména **tip\_třídy**, **tip\_částka**, a **celkový\_částka** zobrazit informace o cíli popisek, který není k dispozici na testování čas. Můžeme odebrat tyto sloupce v úvahu pomocí [výběr sloupců v datové sadě] [ select-columns] modulu.

  Následující diagram znázorňuje naše experiment předpovědět, jestli byla zaplacena tip pro danou cestu:

  ![Diagram experimentu](./media/hive-walkthrough/QGxRz5A.png)

  b. Pro tento experiment distribuce popisek náš cíl byly přibližně 1:1.

   Následující graf ukazuje distribuci tip třídy popisky pro problém binární klasifikace:

  ![Graf distribuce tip třídy popisků](./media/hive-walkthrough/9mM4jlD.png)

    V důsledku toho jsme získat oblasti pod křivkou (AUC) z 0.987, jak je znázorněno na následujícím obrázku:

  ![Graf hodnotu AUC](./media/hive-walkthrough/8JDT0F8.png)

- **Klasifikace víc tříd**: K předpovědi rozsahu tip částky zaplacené pro cestu s použitím dříve definovaných tříd.

  **Student používá:** víc tříd logistické regrese

  a. Pro tento problém je náš cíl (nebo třídy) popisku **tip\_třída**, které můžete provést jednu z pěti hodnot (0,1,2,3,4). Stejně jako v případě binární klasifikace budeme mít několik sloupců, které jsou cílového únikům pro tento experiment. Zejména **šikmý**, **tip\_částka**, a **celkový\_částka** zobrazit informace o cílové popisek, který není k dispozici na testování čas. Můžeme odebrat tyto sloupce s použitím [výběr sloupců v datové sadě] [ select-columns] modulu.

  Následující diagram znázorňuje experiment předpovědět, ve které bin je pravděpodobné, aby tip. Jsou přihrádek: Třída 0: tip = 0 USD, třídy 1: tip > 0 USD a tip < = 5 USD, třídy 2: tip > 5 USD a tip < = 10 USD, třídy 3: tip > 10 USD a tip < = 20 USD a třída 4: tip > $20.

  ![Diagram experimentu](./media/hive-walkthrough/5ztv0n0.png)

  Zobrazujeme vypadá skutečné testovací třídy rozdělení. Třída 0 a 1 třídy jsou běžně se vyskytujícím a jiné třídy se vyskytují jen vzácně.

  ![Diagram rozložení třídy testu](./media/hive-walkthrough/Vy1FUKa.png)

  b. Pro tento experiment používáme chybovou matici podívat se na předpověď přesností. To je znázorněna zde:

  ![Chybová matice](./media/hive-walkthrough/cxFmErM.png)

  Všimněte si, že třída přesností na běžně se vyskytujícím třídy jsou poměrně dobré, model nenabízí Dobrá práce "učení" u vzácnějších tříd.

- **Úloha regrese**: odhadnout množství tip placené cesty.

  **Student používá:** Boosted rozhodovací strom

  a. Pro daný problém a cíle (nebo třídy) popisek je **tip\_částka**. V tomto případě jsou cílového únikům: **šikmý**, **tip\_třídy**, a **celkový\_částka**. Tyto proměnné zobrazí informace o velikosti tip, který je obvykle není k dispozici na testování čas. Můžeme odebrat tyto sloupce s použitím [výběr sloupců v datové sadě] [ select-columns] modulu.

  Následující diagram znázorňuje experiment odhadnout množství dané tip:

  ![Diagram experimentu](./media/hive-walkthrough/11TZWgV.png)

  b. Regrese problémů měříme přesností do predikce. prohlédněte kvadratické chyby v předpovědi a koeficient spolehlivosti:

  ![Snímek obrazovky statistik predikcí](./media/hive-walkthrough/Jat9mrz.png)

  Koeficient spolehlivosti 0.709 se tady zdání, který o 71 procent odchylku je vysvětleno koeficienty modelu.

> [!IMPORTANT]
> Další informace o Machine Learning a jak přistupovat a používat ho, naleznete v tématu [co je Machine Learning](../studio/what-is-machine-learning.md). Kromě toho [galerii Azure AI](https://gallery.cortanaintelligence.com/) zahrnuje škálu experimenty a poskytuje důkladný Úvod do rozsahu schopností služby Machine Learning.
> 
> 

## <a name="license-information"></a>Informace o licenci
Tento ukázkový názorný postup a související skripty sdílí Microsoft v rámci licence MIT. Další podrobnosti najdete v tématu **LICENSE.txt** soubor v adresáři ukázkového kódu na Githubu.

## <a name="references"></a>Odkazy
• [Cesty taxíkem NYC Andrés Monroy stránce pro stažení](http://www.andresmh.com/nyctaxitrips/)  
• [FOILing NYC Taxi Data o jízdách podle Chris Whong](http://chriswhong.com/open-data/foil_nyc_taxi/)   
• [NYC taxislužby a Limousine Komise výzkumu a statistiky](http://www.nyc.gov/html/tlc/html/technology/aggregated_data.shtml)

[2]: ./media/hive-walkthrough/output-hive-results-3.png
[11]: ./media/hive-walkthrough/hive-reader-properties.png
[12]: ./media/hive-walkthrough/binary-classification-training.png
[13]: ./media/hive-walkthrough/create-scoring-experiment.png
[14]: ./media/hive-walkthrough/binary-classification-scoring.png
[15]: ./media/hive-walkthrough/amlreader.png

<!-- Module References -->
[select-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/



