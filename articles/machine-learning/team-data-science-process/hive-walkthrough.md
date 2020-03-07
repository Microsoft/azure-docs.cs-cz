---
title: Zkoumání dat v clusteru Hadoop – vědecké zpracování týmových dat
description: Vědecké zpracování týmových dat pomocí začátku do konce scénář, když cluster systému HDInsight Hadoop k vytvoření a nasazení modelu.
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
ms.openlocfilehash: 005d4fe1b6ec59e7f05be3dd2ab3e72d0e7aa8e0
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2020
ms.locfileid: "78393388"
---
# <a name="the-team-data-science-process-in-action-use-azure-hdinsight-hadoop-clusters"></a>Vědecké zpracování týmových dat v akci: použití Azure HDInsight Hadoop clusterů
V tomto návodu použijeme [TDSP (Team data Sciences)](overview.md) v uceleném scénáři. Pro ukládání, prozkoumávání a inženýrské údaje z veřejně dostupné datové sady [taxislužby pro NYC](https://www.andresmh.com/nyctaxitrips/) a pro data ukázek používáme [cluster Azure HDInsight Hadoop](https://azure.microsoft.com/services/hdinsight/) . Pro zpracování víc tříd a binární klasifikace a úlohy prediktivní regrese, jsme integrovali modely dat pomocí Azure Machine Learning. 

Návod, který ukazuje, jak zpracovat větší datovou sadu, najdete v tématu věnovaném [týmovému zpracování týmových dat – použití Azure HDInsight Hadoop clusterů na 1 TB datové sadě](hive-criteo-walkthrough.md).

Pomocí poznámkového bloku IPython můžete také provádět úkoly uvedené v návodu, který používá datovou sadu 1 – TB. Další informace najdete v [návodu k Criteo pomocí připojení k podregistru ODBC](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/iPythonNotebooks/machine-Learning-data-science-process-hive-walkthrough-criteo.ipynb).

## <a name="dataset"></a>NYC taxislužby TRIPS – popis datové sady
Data o jízdách taxislužby NYC je přibližně 20 GB komprimované hodnot oddělených čárkami (CSV) souborů (nekomprimovaný ~ 48 GB). Má více než milion 173 jednotlivé cesty a zahrnuje tarify placené pro každou cestu. Každý záznam o jízdách zahrnuje odběr a dropoff umístění a čas, anonymizované hack (ovladač) číslo licence a Medailon počet (jedinečné ID taxislužby). Data v roce 2013 zahrnuje všechny cesty a je dostupné pro každý měsíc následující dvě datové sady:

- Trip_data soubory CSV obsahují podrobnosti o cestách: počet cestujících, výběr a dropoff body, doba trvání cesty a délka cesty. Tady je několik ukázkových záznamů:
   
        medallion,hack_license,vendor_id,rate_code,store_and_fwd_flag,pickup_datetime,dropoff_datetime,passenger_count,trip_time_in_secs,trip_distance,pickup_longitude,pickup_latitude,dropoff_longitude,dropoff_latitude
        89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,1,N,2013-01-01 15:11:48,2013-01-01 15:18:10,4,382,1.00,-73.978165,40.757977,-73.989838,40.751171
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-06 00:18:35,2013-01-06 00:22:54,1,259,1.50,-74.006683,40.731781,-73.994499,40.75066
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-05 18:49:41,2013-01-05 18:54:23,1,282,1.10,-74.004707,40.73777,-74.009834,40.726002
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:54:15,2013-01-07 23:58:20,2,244,.70,-73.974602,40.759945,-73.984734,40.759388
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:25:03,2013-01-07 23:34:24,1,560,2.10,-73.97625,40.748528,-74.002586,40.747868
- Soubory trip_fare CSV obsahují podrobnosti o tarifu placeného za každou cestu: typ platby, částka tarifů, příplatek a daně, tipy a mýtné a celková placená částka. Tady je několik ukázkových záznamů:
   
        medallion, hack_license, vendor_id, pickup_datetime, payment_type, fare_amount, surcharge, mta_tax, tip_amount, tolls_amount, total_amount
        89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,2013-01-01 15:11:48,CSH,6.5,0,0.5,0,0,7
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-06 00:18:35,CSH,6,0.5,0.5,0,0,7
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-05 18:49:41,CSH,5.5,1,0.5,0,0,7
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:54:15,CSH,5,0.5,0.5,0,0,6
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:25:03,CSH,9.5,0.5,0.5,0,0,10.5

Jedinečný klíč pro připojení cest\_dat a cest\_tarif se skládá z těchto polí: Medallion, napadení\_licence a vyzvednutí\_data a času. Chcete-li získat všechny podrobnosti relevantní pro konkrétní cesty, stačí Seznamte se s tyto tři klíče.

## <a name="mltasks"></a>Příklady úkolů předpovědi
Určete druh předpovědi, který chcete vytvořit na základě analýzy dat, abyste mohli vyjasnit požadované úlohy procesu. Tady jsou tři příklady problémů s předpovědí, které řešíme v tomto návodu, a to vše na základě *\_výše v tipu*:

- **Binární klasifikace**: předpověď bez ohledu na to, zda byl pro cestu zaplacen Tip. To znamená, že *tip\_množství* , které je větší než $0, je kladným příkladem, zatímco *tip\_hodnota* $0 je negativní příklad.
   
        Class 0: tip_amount = $0
        Class 1: tip_amount > $0
- **Klasifikace s více třídami**: předpověď rozsahu částek v tipu placených pro danou cestu. \_je pro *Tip* rozdělená na pět tříd:
   
        Class 0: tip_amount = $0
        Class 1: tip_amount > $0 and tip_amount <= $5
        Class 2: tip_amount > $5 and tip_amount <= $10
        Class 3: tip_amount > $10 and tip_amount <= $20
        Class 4: tip_amount > $20
- **Regresní úloha**: předpověď množství tipu placeného pro cestu.  

## <a name="setup"></a>Nastavení clusteru HDInsight Hadoop pro pokročilou analýzu
> [!NOTE]
> Obvykle se jedná úlohu správy.
> 
> 

Můžete nastavit prostředí Azure pro pokročilé analýzy, která používá cluster služby HDInsight ve třech krocích:

1. [Vytvoření účtu úložiště](../../storage/common/storage-account-create.md): Tento účet úložiště se používá k ukládání dat v úložišti objektů BLOB v Azure. Data používaná v clusterech HDInsight se také nachází zde.
2. [Přizpůsobte Azure HDInsight Hadoop clusterů pro proces a technologii pokročilé analýzy](customize-hadoop-cluster.md). Tento krok vytvoří cluster HDInsight Hadoop s 64bitovým kompilátorem Anaconda Python 2.7 nainstalované na všech uzlech. Existují dva důležité kroky a mějte na paměti při přizpůsobení vašeho clusteru HDInsight.
   
   * Nezapomeňte propojit účet úložiště vytvořený v kroku 1 s vaším clusterem HDInsight při jeho vytváření. Tomuto účtu úložiště přistupuje k datům, která je zpracována v rámci clusteru.
   * Po vytvoření clusteru povolte vzdálený přístup k hlavnímu uzlu clusteru. Přejděte na kartu **Konfigurace** a vyberte **Povolit vzdálenou**. Tento krok určuje uživatelská pověření sloužící ke vzdálenému přihlášení.
3. [Vytvoření pracovního prostoru Azure Machine Learning](../studio/create-workspace.md): pomocí tohoto pracovního prostoru sestavíte modely strojového učení. Tato úloha je určeno po dokončení počáteční zkoumání a dolů – vzorkování, pomocí clusteru HDInsight.

## <a name="getdata"></a>Získat data z veřejného zdroje
> [!NOTE]
> Obvykle se jedná úlohu správy.
> 
> 

Pokud chcete zkopírovat datovou sadu [NYC taxislužby TRIPS](https://www.andresmh.com/nyctaxitrips/) na váš počítač ze svého veřejného umístění, použijte kteroukoli z metod popsaných v tématu [přesun dat do a z úložiště objektů BLOB v Azure](move-azure-blob.md).

Zde zjistíte, jak pomocí AzCopy můžete přenášet soubory obsahující data. Pokud chcete stáhnout a nainstalovat AzCopy, postupujte podle pokynů v tématu [Začínáme s nástrojem příkazového řádku AzCopy](../../storage/common/storage-use-azcopy.md).

1. V okně příkazového řádku spusťte následující příkazy AzCopy, které nahradí *\<path_to_data_folder >* s požadovaným umístěním:

        "C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:https://nyctaxitrips.blob.core.windows.net/data /Dest:<path_to_data_folder> /S

1. Po dokončení kopírování, zobrazí se celkem 24 zip soubory ve složce data zvolili. Rozbalte stažené soubory do stejného adresáře na místním počítači. Poznamenejte si složku, ve kterém jsou umístěny dekomprimovaných souborů. Tato složka se označuje jako *\<cesta\_\_soubory unzipped_data\_* v následujícím pořadí.\>

## <a name="upload"></a>Nahrajte data do výchozího kontejneru clusteru HDInsight Hadoop.
> [!NOTE]
> Obvykle se jedná úlohu správy.
> 
> 

V následující příkazy AzCopy a nahradit skutečnými hodnotami, které jste zadali při vytváření clusteru Hadoop následující parametry a rozbalení datových souborů.

* ***\<path_to_data_folder >*** Adresář (spolu s cestou) na počítači, který obsahuje soubory s nekomprimovanými daty.  
* ***\<název účtu úložiště clusteru Hadoop >*** Účet úložiště přidružený k vašemu clusteru HDInsight.
* ***\<výchozí kontejner clusteru Hadoop >*** Výchozí kontejner používaný clusterem. Název výchozího kontejneru má obvykle stejný název jako samotný cluster. Například pokud clusteru se nazývá "abc123.azurehdinsight.net", je výchozí kontejner abc123.
* ***\<Key účtu úložiště >*** Klíč pro účet úložiště používaný clusterem

Z příkazového řádku nebo v okně prostředí Windows PowerShell spusťte následující dva příkazy AzCopy.

Tento příkaz nahraje data pro cestu do adresáře ***nyctaxitripraw*** ve výchozím kontejneru clusteru Hadoop.

        "C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:<path_to_unzipped_data_files> /Dest:https://<storage account name of Hadoop cluster>.blob.core.windows.net/<default container of Hadoop cluster>/nyctaxitripraw /DestKey:<storage account key> /S /Pattern:trip_data_*.csv

Tento příkaz nahraje data tarifů do adresáře ***nyctaxifareraw*** ve výchozím kontejneru clusteru Hadoop.

        "C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:<path_to_unzipped_data_files> /Dest:https://<storage account name of Hadoop cluster>.blob.core.windows.net/<default container of Hadoop cluster>/nyctaxifareraw /DestKey:<storage account key> /S /Pattern:trip_fare_*.csv

Data by měla být nyní v úložišti objektů Blob a jste připravení využívat v rámci clusteru HDInsight.

## <a name="#download-hql-files"></a>Přihlaste se k hlavnímu uzlu clusteru Hadoop a připravte se na analýzu dat pro průzkumné testování.
> [!NOTE]
> Obvykle se jedná úlohu správy.
> 
> 

Pokud chcete získat přístup k hlavnímu uzlu clusteru pro průzkumné analýzy dat a vzorkování dat dolů, postupujte podle kroků uvedených v části [přístup k hlavnímu uzlu clusteru Hadoop](customize-hadoop-cluster.md).

V tomto návodu primárně používáme dotazy napsané v [podregistru](https://hive.apache.org/), dotazovací jazyk, který je typu SQL, k provádění předběžných průzkumných dat. Dotazy na podregistr se ukládají do souborů. HQL. Jsme pak dolů – ukázka tato data se použije v Machine Learning pro vytváření modelů.

Chcete-li připravit cluster pro průzkumné analýzy dat, Stáhněte soubory '. HQL ', které obsahují relevantní skripty podregistru z [GitHubu](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/DataScienceProcess/DataScienceScripts) , do místního adresáře (C:\Temp) do hlavního uzlu. Otevřete příkazový řádek z hlavního uzlu v clusteru a spusťte následující dva příkazy:

    set script='https://raw.githubusercontent.com/Azure/Azure-MachineLearning-DataScience/master/Misc/DataScienceProcess/DataScienceScripts/Download_DataScience_Scripts.ps1'

    @powershell -NoProfile -ExecutionPolicy unrestricted -Command "iex ((new-object net.webclient).DownloadString(%script%))"

Tyto dva příkazy stáhnou všechny soubory '. HQL ' potřebné v tomto návodu do místní složky ***C:\Temp&#92;***  v hlavním uzlu.

## <a name="#hive-db-tables"></a>Vytvoření databáze podregistru a tabulek dělených za měsíc
> [!NOTE]
> Tento úkol je typicky pro správce.
> 
> 

Nyní jste připraveni k vytvoření tabulky Hive pro datovou sadu NYC taxislužby.
V k hlavnímu uzlu clusteru Hadoop otevřete příkazový řádek systému Hadoop v klientských počítačích k hlavnímu uzlu. Zadejte adresář Hive spuštěním následujícího příkazu:

    cd %hive_home%\bin

> [!NOTE]
> Spustit všechny příkazy Hive v tomto názorném postupu z koše Hive / directory řádku. To řeší problémy cestu automaticky. Používáme termíny "Hive directory řádku", "Hive bin / directory řádku" a "Příkazového řádku Hadoopu" Zaměnitelně v tomto názorném postupu.
> 
> 

Z příkazového řádku adresáře podregistr spusťte následující příkaz v příkazovém řádku Hadoop hlavního uzlu, který vytvoří databázi a tabulky podregistru:

    hive -f "C:\temp\sample_hive_create_db_and_tables.hql"

Tady je obsah **podregistru C:\temp\sample\_\_vytvoření souboru\_db\_a\_Tables. HQL** , který vytváří podregistr **nyctaxidb**a tabulky a **cestovní** **tarify**.

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

* Tabulka **Trip** obsahuje podrobnosti o cestě každé jízdní (podrobnosti o ovladačích, doba vyzvednutí, vzdálenost na cestách a časy).
* Tabulka **tarifů** obsahuje podrobnosti tarifů (částka tarifů, částka hrotu, mýtné a příplatek).

Pokud potřebujete další pomoc s těmito postupy nebo chcete prozkoumat alternativy, prostudujte si část [odeslání dotazů na podregistr přímo z příkazového řádku Hadoop](move-hive-tables.md#submit).

## <a name="#load-data"></a>Načtení dat do tabulek podregistru podle oddílů
> [!NOTE]
> Tento úkol je typicky pro správce.
> 
> 

Datová sada taxislužby NYC má přirozené dělení podle měsíce, které používáme umožňuje rychlejší zpracování a dotazu. Následující příkazy Powershellu (vydaného adresáři Hive pomocí příkazového řádku Hadoopu) načtení dat do cesty a jízdenky tabulek Hive, rozdělený podle měsíce.

    for /L %i IN (1,1,12) DO (hive -hiveconf MONTH=%i -f "C:\temp\sample_hive_load_data_by_partitions.hql")

**Vzorový\_\_načíst\_data\_souborem\_partitions. HQL** obsahuje následující příkazy **načtení** :

    LOAD DATA INPATH 'wasb:///nyctaxitripraw/trip_data_${hiveconf:MONTH}.csv' INTO TABLE nyctaxidb.trip PARTITION (month=${hiveconf:MONTH});
    LOAD DATA INPATH 'wasb:///nyctaxifareraw/trip_fare_${hiveconf:MONTH}.csv' INTO TABLE nyctaxidb.fare PARTITION (month=${hiveconf:MONTH});

Počet dotazů na podregistr, které se tady používají v procesu průzkumu, zahrnuje prohlížení jenom jednoho nebo dvou oddílů. Ale tyto dotazy lze spouštět napříč celou datovou sadu.

### <a name="#show-db"></a>Zobrazit databáze v clusteru HDInsight Hadoop
Chcete-li zobrazit databáze vytvořená v clusteru HDInsight Hadoop v okně příkazového řádku systému Hadoop, spusťte následující příkaz v příkazovém řádku Hadoop:

    hive -e "show databases;"

### <a name="#show-tables"></a>Zobrazit tabulky podregistru v databázi **nyctaxidb**
Chcete-li zobrazit tabulky v databázi **nyctaxidb** , spusťte následující příkaz v příkazovém řádku Hadoop:

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

## <a name="#explore-hive"></a>Zkoumání dat a strojírenství funkcí v podregistru
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

Záznamy můžete uložit do souboru pro pohodlné zobrazení s malou změnou v předchozím dotazu:

    hive -e "select * from nyctaxidb.fare where month=1 limit 10;" > C:\temp\testoutput

### <a name="exploration-view-the-number-of-records-in-each-of-the-12-partitions"></a>Zkoumání: Zobrazte několik záznamů v každé z 12 oddíly
> [!NOTE]
> Obvykle se jedná o úlohu mezi odborníky přes data.
> 
> 

Zajímavé je, jak se liší počet cest v kalendářním roce. Seskupení podle kategorie month zobrazuje distribuci zkracuje dobu odezvy.

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

Tady první sloupec je měsíc a druhý je počet cest pro daný měsíc.

Můžete také počítáme celkový počet záznamů v naší datové sadě cesty spuštěním následujícího příkazu do příkazového řádku adresář Hive:

    hive -e "select count(*) from nyctaxidb.trip;"

Tento příkaz vypočítá:

    173179759
    Time taken: 284.017 seconds, Fetched: 1 row(s)

Pomocí příkazů, které jsou podobné těm, které jsou zobrazeny pro sadu dat o jízdách, nám můžete posílat dotazy Hive z příkazového řádku adresář Hive pro datovou sadu tarif ověření počet záznamů.

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

V obou datových sadách se vrátí přesně stejný počet cest za měsíc, který poskytuje první ověření, že data byla načtena správně.

Celkový počet záznamů v datové sadě tarif můžete počítat s použitím následujícího příkazu z příkazového řádku adresář Hive:

    hive -e "select count(*) from nyctaxidb.fare;"

Tento příkaz vypočítá:

    173179759
    Time taken: 186.683 seconds, Fetched: 1 row(s)

Celkový počet záznamů v obou tabulkách je také stejný a poskytuje druhé ověření, že data byla načtena správně.

### <a name="exploration-trip-distribution-by-medallion"></a>Zkoumání: Distribuce latence podle Medailon
> [!NOTE]
> Tato analýza je obvykle úkol odborníka na data.
> 
> 

V tomto příkladu identifikuje medallions (taxislužby čísla) s více než 100 zkracuje dobu odezvy v daném časovém období. Dotaz je výhodou z přístupu k dělenému tabulce, protože je podmíněna proměnnou month za **měsíc**. Výsledky dotazu jsou zapsány do místního souboru **queryoutput. TSV**v `C:\temp` na hlavní uzel.

    hive -f "C:\temp\sample_hive_trip_count_by_medallion.hql" > C:\temp\queryoutput.tsv

Tady je obsah **ukázkového\_podregistru\_trips\_\_pro kontrolu pomocí souboru\_. HQL** .

    SELECT medallion, COUNT(*) as med_count
    FROM nyctaxidb.fare
    WHERE month<=3
    GROUP BY medallion
    HAVING med_count > 100
    ORDER BY med_count desc;

Medailon v sadě dat taxislužby NYC identifikuje jedinečné souboru cab. Můžete určit, které soubory CAB jsou relativně zaneprázdněný požádá ty, které provedli větší než počet cest v konkrétním časovém období. Následující příklad identifikuje kabiny, které provedly více než sto cest v prvních třech měsících, a uloží výsledky dotazu do místního souboru **C:\temp\queryoutput.TSV**.

Tady je obsah **ukázkového\_podregistru\_trips\_\_pro kontrolu pomocí souboru\_. HQL** .

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
> Tato úloha je typicky pro datový vědecký pracovník.
> 
> 

Při zkoumání datové sady často chceme prozkoumat distribuce skupin hodnot. V této části najdete příklad toho, jak provést tuto analýzu pro kabiny a ovladače.

**Vzorový\_\_trips\_count\_souborem\_medallion\_License. HQL** seskupuje datovou sadu tarifů na **Medallion** a **hack_license**a vrátí počty jednotlivých kombinací. Zde jsou jeho obsah:

    SELECT medallion, hack_license, COUNT(*) as trip_count
    FROM nyctaxidb.fare
    WHERE month=1
    GROUP BY medallion, hack_license
    HAVING trip_count > 100
    ORDER BY trip_count desc;

Tento dotaz vrátí kombinace souboru cab a ovladače, seřazených podle sestupných počet cest.

Z adresáře řádku Hive spusťte:

    hive -f "C:\temp\sample_hive_trip_count_by_medallion_license.hql" > C:\temp\queryoutput.tsv

Výsledky dotazu jsou zapsány do místního souboru **C:\temp\queryoutput.TSV**.

### <a name="exploration-assessing-data-quality-by-checking-for-invalid-longitude-or-latitude-records"></a>Zkoumání: Hodnocení kvality dat tak, že zkontrolujete neplatnou zeměpisnou délku nebo záznamy zeměpisné šířky
> [!NOTE]
> Obvykle se jedná o úlohu mezi odborníky přes data.
> 
> 

Běžné cílem analýzy dat průzkumného testování je odstraňování plevele si neplatná nebo chybné záznamy. Příklad v této části určuje, zda šířky nebo délky pole obsahovat hodnotu úplně mimo oblast NYC. Protože je pravděpodobné, že tyto záznamy mají hodnotu chybné zeměpisné šířky, chcete vyloučit z všechna data, která má být použito pro modelování.

Tady je obsah **ukázkového\_podregistru\_kvality\_souboru Assessment. HQL** pro kontrolu.

        SELECT COUNT(*) FROM nyctaxidb.trip
        WHERE month=1
        AND  (CAST(pickup_longitude AS float) NOT BETWEEN -90 AND -30
        OR    CAST(pickup_latitude AS float) NOT BETWEEN 30 AND 90
        OR    CAST(dropoff_longitude AS float) NOT BETWEEN -90 AND -30
        OR    CAST(dropoff_latitude AS float) NOT BETWEEN 30 AND 90);


Z adresáře řádku Hive spusťte:

    hive -S -f "C:\temp\sample_hive_quality_assessment.hql"

Argument *-S* , který je součástí tohoto příkazu, potlačí výpis stavu obrazovky mapy podregistru nebo omezení úloh. Tento příkaz je užitečný, protože usnadňuje tisk na obrazovce výstup dotazu na podregistr.

### <a name="exploration-binary-class-distributions-of-trip-tips"></a>Zkoumání: Binární třída distribuce latence tipy
> [!NOTE]
> Obvykle se jedná o úlohu mezi odborníky přes data.
> 
> 

Pro problém binární klasifikace, který je popsaný v části [příklady úkolů předpovědi](hive-walkthrough.md#mltasks) , je užitečné zjistit, zda byl Tip uveden nebo nikoli. Toto rozdělení tipy je binární:

* zadaný Tip (třída 1, Tip\_> $0)  
* žádný Tip (třída 0, Tip\_částka = $0)

Následující **vzorový\_podregistr\_hqld\_četnosti** běhu zobrazuje příkaz, který se má spustit:

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

V případě problému s více třídami, který je popsaný v části [příklady úkolů předpovědi](hive-walkthrough.md#mltasks) , se tato datová sada také přímo zaplní do přirozené klasifikace pro předpověď množství předaných tipů. Přihrádky můžeme použít k definování tip rozsahů v dotazu. Chcete-li získat distribuce tříd pro různé rozsahy tipů, použijte **vzorový\_podregistr\_\_\_Range. HQL** . Zde jsou jeho obsah.

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

Pokud chcete zobrazit srovnání mezi skutečnou délkou cest a [Haversine vzdáleností](https://en.wikipedia.org/wiki/Haversine_formula) mezi dvěma zeměpisnou délkou (vzdálenost "skvělého kruhu"), můžete použít trigonometrické funkce dostupné v rámci podregistru:

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

V předchozím dotazu jazyka R je radius Earth v mil a pi je převést na radiány. Zeměpisná délka – Zeměpisná šířka se filtruje tak, aby se odebraly hodnoty, které jsou daleko z oblasti NYC.

V tomto případě zapíšeme výsledky do adresáře s názvem **queryoutputdir**. Pořadí následující příkazy nejprve vytvoří tento výstupní adresář a pak spustí příkaz Hive.

Z adresáře řádku Hive spusťte:

    hdfs dfs -mkdir wasb:///queryoutputdir

    hive -f "C:\temp\sample_hive_trip_direct_distance.hql"


Výsledky dotazu se zapisují do devíti objektů blob Azure (**queryoutputdir/000000\_0** až **queryoutputdir/000008\_0**), a to pod výchozím kontejnerem clusteru Hadoop.

Pokud chcete zobrazit množství jednotlivých objektů BLOB, spusťte následující příkaz z příkazového řádku adresář Hive:

    hdfs dfs -ls wasb:///queryoutputdir

Pokud chcete zobrazit obsah daného souboru, řekněme, že **000000\_0**, pomocí příkazu `copyToLocal` Hadoop.

    hdfs dfs -copyToLocal wasb:///queryoutputdir/000000_0 C:\temp\tempfile

> [!WARNING]
> `copyToLocal` může být pro velké soubory velmi pomalé a nedoporučuje se je používat.  
> 
> 

Klíčovou výhodou toho, aby se tato data nacházela v objektu blob Azure, je to, že můžeme prozkoumat data v rámci Machine Learning pomocí modulu [Import dat][import-data] .

## <a name="#downsample"></a>Nefunkční vzorová data a modely sestavení v Machine Learning
> [!NOTE]
> Obvykle se jedná o úlohu mezi odborníky přes data.
> 
> 

Po fázi analýzy průzkumné data můžeme nyní připraveni na nižší data pro vytváření modelů ve službě Machine Learning. V této části ukážeme, jak používat dotazy Hive na nižší data. Machine Learning pak k němu přistupuje z modulu [Import dat][import-data] .

### <a name="down-sampling-the-data"></a>Seznam – vzorkování dat
Existují dva kroky v tomto postupu. Nejdřív se připojíme k tabulkám **nyctaxidb. Trip** a **nyctaxidb. tarif** na třech klíčích, které jsou přítomné ve všech záznamech: **Medallion**, oprávnění k **napadení\_** a **\_data a času vyzvednutí**. Následně vygenerujeme popisek binární **klasifikace, byl zobrazen a popisek**klasifikace s více třídami, **Tip\_třídy**.

Aby bylo možné používat data z rozevíracího seznamu přímo z modulu [Import dat][import-data] v Machine Learning, uložte výsledky předchozího dotazu do interní tabulky podregistru. V následující vytvoříme interní tabulky Hive a naplnit daty připojeného k a předvýpočtem zredukovaných jeho obsah.

Dotaz aplikuje standardní funkce podregistru přímo k vygenerování následujících časových parametrů z pole **Datum vyzvednutí\_** :
- hodiny dne
- týden roku
- den v týdnu (' 1 ' znamená pondělí a ' 7 ' představuje neděli)

Přímé vzdálenost mezi umístěními odběr a dropoff také vygeneruje dotaz. Úplný seznam takových funkcí najdete v tématu [LANGUAGEMANUAL UDF](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+UDF).

Dotaz potom dolů samples data tak, že výsledky dotazu můžete začlenit do Azure Machine Learning Studio. Pouze asi 1 % z původní datové sady je importovat do nástroje studio.

Tady je obsah **ukázkového\_podregistru\_příprava\_pro soubor\_aml\_Full. HQL** , který připraví data pro sestavování modelu v Machine Learning:

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

Teď máme interní tabulku **nyctaxidb. nyctaxi_downsampled_dataset**, ke které se dá dostat pomocí modulu [Import dat][import-data] z Machine Learning. Kromě toho můžeme použít tuto datovou sadu pro vytváření modelů Machine Learning.  

### <a name="use-the-import-data-module-in-machine-learning-to-access-the-down-sampled-data"></a>Můžete získat přístup k datům předvýpočtem zredukovaných pomocí modulu Import dat ve službě Machine Learning
K vydávání dotazů na podregistr v modulu Machine Learning [importu dat][import-data] potřebujete přístup k pracovnímu prostoru Machine Learning. Potřebujete také přístup k přihlašovacím údajům clusteru a jeho přidružený účet úložiště.

Zde jsou některé podrobnosti o modulu [Import dat][import-data] a parametry, které je potřeba zadat:

**Identifikátor URI serveru HCatalog**: Pokud je název clusteru **abc123**, pak použijte: https://abc123.azurehdinsight.net.

**Název uživatelského účtu Hadoop**: uživatelské jméno zvolené pro cluster (nikoli uživatelské jméno vzdáleného přístupu).

**Heslo uživatelského účtu Hadoop**: heslo zvolené pro cluster (nikoli heslo vzdáleného přístupu).

**Umístění výstupních dat**: zvolené pro Azure.

**Azure Storage název účtu**: název výchozího účtu úložiště přidruženého ke clusteru.

**Název kontejneru Azure**: výchozí název kontejneru pro cluster, který je obvykle stejný jako název clusteru. Pro cluster s názvem **abc123**je název abc123.

> [!IMPORTANT]
> Každá tabulka, kterou chceme použít k dotazování pomocí modulu [Import dat][import-data] v Machine Learning, musí být interní tabulkou.
> 
> 

Zde je postup určení, zda je tabulka **T** v databázi **D. DB** interní tabulkou. Z adresáře řádku Hive spusťte následující příkaz:

    hdfs dfs -ls wasb:///D.db/T

Pokud je tabulka interní tabulku a je vyplněný, musí tady zobrazit jeho obsah.

Dalším způsobem, jak určit, zda tabulka je interní tabulku je použití Průzkumníka služby Azure Storage. Můžete přejít na výchozí název kontejneru clusteru a potom filtrovat podle názvu tabulky. Pokud tabulka a její obsah uveden, tím potvrdíte, že je interní tabulku.

Tady je snímek obrazovky s dotazem na podregistr a modulem [importovat data][import-data] :

![Snímek obrazovky z dotazu Hive v modulu Import dat](./media/hive-walkthrough/1eTYf52.png)

Vzhledem k tomu, že se naše data v ukázce nacházejí ve výchozím kontejneru, je výsledný dotaz podregistru z Machine Learning jednoduchý. Je to jenom **výběrový znak * z nyctaxidb. nyctaxi\_downsampled\_data**.

Datovou sadu můžete teď použít jako výchozí bod pro vytváření modelů Machine Learning.

### <a name="mlmodel"></a>Modely sestavení v Machine Learning
Nyní můžete přejít k sestavení modelu a nasazení modelu v [Machine Learning](https://studio.azureml.net). Data jsou připravené, abyste mohli používat v řeší problémy předpovědi dřív identifikovali:

- **Binární klasifikace**: pro předpověď, zda byl pro cestu zaplacen Tip.

  **Použil se učí:** Logistická regrese dvou tříd

  a. Pro tento problém je popisek cíl (nebo třída) na stejném **řádku.** Původní datové sady předvýpočtem zredukovaných má několik sloupců, které jsou cílového únikům pro tento experiment klasifikace. Konkrétně **\_třída Tip**, **\_částka**a **Celková hodnota\_** odhalují informace o cílovém popisku, který není k dispozici v době testování. Tyto sloupce odebereme z úvahy pomocí modulu [Výběr sloupců v datové sadě][select-columns] .

  Následující diagram znázorňuje naše experiment předpovědět, jestli byla zaplacena tip pro danou cestu:

  ![Diagram experimentu předpovědět, pokud byla zaplacena tip](./media/hive-walkthrough/QGxRz5A.png)

  b. Pro tento experiment distribuce popisek náš cíl byly přibližně 1:1.

   Následující graf ukazuje distribuci tip třídy popisky pro problém binární klasifikace:

  ![Graf distribuce tip třídy popisků](./media/hive-walkthrough/9mM4jlD.png)

    V důsledku toho jsme získat oblasti pod křivkou (AUC) z 0.987, jak je znázorněno na následujícím obrázku:

  ![Graf hodnotu AUC](./media/hive-walkthrough/8JDT0F8.png)

- **Třídy s více třídami**: pro předpověď rozsahu částek v rámci této cesty, které jsou vyplaceny pro danou cestu, pomocí dříve definovaných tříd.

  **Použil se učí:** Mikrotřída logistické regrese

  a. Pro tento problém je naším cílem (nebo třídou) popisek **\_třídy**, který může mít jednu z pěti hodnot (0, 1, 2, 3, 4). Stejně jako v případě binární klasifikace budeme mít několik sloupců, které jsou cílového únikům pro tento experiment. Konkrétně **\_množství** **,** Tip a **Celková\_částka** odhalují informace o cílovém popisku, který není k dispozici v době testování. Tyto sloupce odebereme pomocí modulu [Výběr sloupců v datové sadě][select-columns] .

  Následující diagram znázorňuje experiment předpovědět, ve které bin je pravděpodobné, aby tip. Jsou přihrádek: Třída 0: tip = 0 USD, třídy 1: tip > 0 USD a tip < = 5 USD, třídy 2: tip > 5 USD a tip < = 10 USD, třídy 3: tip > 10 USD a tip < = 20 USD a třída 4: tip > $20.

  ![Diagram experimentu předpovědět bin pro tip](./media/hive-walkthrough/5ztv0n0.png)

  Zobrazujeme vypadá skutečné testovací třídy rozdělení. Třída 0 a 1 třídy jsou běžně se vyskytujícím a jiné třídy se vyskytují jen vzácně.

  ![Diagram rozložení třídy testu](./media/hive-walkthrough/Vy1FUKa.png)

  b. Pro tento experiment používáme nejasnou matrici k zobrazení předpovědi přesností, jak je znázorněno zde:

  ![Chybová matice](./media/hive-walkthrough/cxFmErM.png)

  I když je třída přesností na předaných třídách dobrá, model není dobrým úkolem "učení" na třídách rarer.

- **Regresní úloha**: pro předpověď množství tipu placeného pro cestu.

  **Použil se učí:** Zesílený rozhodovací strom

  a. Pro tento problém je popisek cíle (nebo třídy) **\_ou velikostí**. Cílové nevracení v tomto případě **jsou:** , **Tip\_Class**a **Celková\_á částka**. Tyto proměnné zobrazí informace o velikosti tip, který je obvykle není k dispozici na testování čas. Tyto sloupce odebereme pomocí modulu [Výběr sloupců v datové sadě][select-columns] .

  Následující diagram znázorňuje experiment odhadnout množství dané tip:

  ![Diagram experimentu odhadnout množství tip](./media/hive-walkthrough/11TZWgV.png)

  b. Regrese problémů měříme přesností do predikce. prohlédněte kvadratické chyby v předpovědi a koeficient spolehlivosti:

  ![Snímek obrazovky statistik predikcí](./media/hive-walkthrough/Jat9mrz.png)

  Koeficient spolehlivosti 0.709 se tady zdání, který o 71 procent odchylku je vysvětleno koeficienty modelu.

> [!IMPORTANT]
> Další informace o Machine Learning a o tom, jak k nim přistupovat a jak ji používat, najdete v tématu [co je Machine Learning](../studio/what-is-machine-learning.md). Kromě toho [Azure AI Gallery](https://gallery.cortanaintelligence.com/) pokrývá rozsah experimentů a poskytuje důkladný úvod do rozsahu možností Machine Learning.
> 
> 

## <a name="license-information"></a>Informace o licenci
Tento ukázkový názorný postup a související skripty sdílí Microsoft v rámci licence MIT. Další informace najdete v souboru **License. txt** v adresáři ukázkového kódu na GitHubu.

## <a name="references"></a>Odkazy
• [Stránka pro stažení Andrés MONROY NYC taxislužby](https://www.andresmh.com/nyctaxitrips/)  
• [Fólie NYC data taxislužby na cestách pomocí Chris Whong](https://chriswhong.com/open-data/foil_nyc_taxi/)   
• [NYC taxislužby a Limousine výzkumu a statistiky Komise](https://www1.nyc.gov/site/tlc/about/tlc-trip-record-data.page)

[2]: ./media/hive-walkthrough/output-hive-results-3.png
[11]: ./media/hive-walkthrough/hive-reader-properties.png
[12]: ./media/hive-walkthrough/binary-classification-training.png
[13]: ./media/hive-walkthrough/create-scoring-experiment.png
[14]: ./media/hive-walkthrough/binary-classification-scoring.png
[15]: ./media/hive-walkthrough/amlreader.png

<!-- Module References -->
[select-columns]: https://msdn.microsoft.com/library/azure/1ec722fa-b623-4e26-a44e-a50c6d726223/
[import-data]: https://msdn.microsoft.com/library/azure/4e1b0fe6-aded-4b3f-a36f-39b8862b9004/



