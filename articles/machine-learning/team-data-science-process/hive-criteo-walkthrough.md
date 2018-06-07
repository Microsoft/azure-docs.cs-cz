---
title: Proces Team dat. vědecké účely v akce – pomocí clusteru Azure HDInsight Hadoop na datovou sadu 1 TB | Microsoft Docs
description: Pomocí procesu Team dat vědecké účely začátku do konce scénář nasazení clusteru služby HDInsight Hadoop pro sestavení a nasazení modelu pomocí velké veřejně dostupné datové sady (1 TB)
services: machine-learning,hdinsight
documentationcenter: ''
author: deguhath
manager: cgronlun
editor: cgronlun
ms.assetid: 72d958c4-3205-49b9-ad82-47998d400d2b
ms.service: machine-learning
ms.component: team-data-science-process
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/29/2017
ms.author: deguhath
ms.openlocfilehash: 4c368c3f06347b1164731d056a7341bdabb759b4
ms.sourcegitcommit: 944d16bc74de29fb2643b0576a20cbd7e437cef2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/07/2018
ms.locfileid: "34837340"
---
# <a name="the-team-data-science-process-in-action---using-an-azure-hdinsight-hadoop-cluster-on-a-1-tb-dataset"></a>Proces Team dat. vědecké účely v akce – pomocí clusteru Azure HDInsight Hadoop na 1 TB datové sady

Tento návod ukazuje, jak používat proces vědecké účely Team dat ve scénáři s začátku do konce s [clusteru Azure HDInsight Hadoop](https://azure.microsoft.com/services/hdinsight/) k uložení, prozkoumejte, funkci pracovníka a dolů ukázková data z jednoho z veřejně dostupné [ Criteo](http://labs.criteo.com/downloads/download-terabyte-click-logs/) datové sady. Azure Machine Learning používá k vytvoření modelu binární klasifikace na tato data. Také ukazuje, jak k publikování některého z těchto modelů jako webovou službu.

Je také možné použít k provádění úloh uvedené v tomto návodu IPython poznámkového bloku. Uživatelé, kteří by chtěli opakujte tento postup by se měli obrátit [Criteo návod pomocí připojení Hive ODBC](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/iPythonNotebooks/machine-Learning-data-science-process-hive-walkthrough-criteo.ipynb) tématu.

## <a name="dataset"></a>Popis Criteo datové sady
Criteo dat je předpovědi klikněte na datovou sadu, která je přibližně 370 gzip komprimované TSV souborů (~1.3TB nekomprimovaným), která obsahuje více než miliardu 4.3 záznamů. Jsou převzaty z 24 dní klikněte na tlačítko dat, které poskytují [Criteo](http://labs.criteo.com/downloads/download-terabyte-click-logs/). Pro usnadnění práce datových vědců byla data dostupná pro nás a experimentovat s rozbalené.

Každý záznam v této datové sadě obsahuje 40 sloupce:

* první sloupec je sloupec popisek, který označuje, zda uživatel klikne **přidat** (hodnota 1) nebo není klikněte na jednu (hodnota 0)
* Další 13 sloupce jsou číselné, a
* poslední 26 jsou kategorií sloupců

Sloupce jsou anonymizovány a použijte řadu výčtové názvy: "Sloupec1" (pro popisek sloupce) do "Col40" (pro poslední kategorií sloupec).            

Tady je výňatek prvních 20 sloupců dvě hodnoty (řádky) s tuto datovou sadu:

    Col1    Col2    Col3    Col4    Col5    Col6    Col7    Col8    Col9    Col10    Col11    Col12    Col13    Col14    Col15            Col16            Col17            Col18            Col19        Col20

    0       40      42      2       54      3       0       0       2       16      0       1       4448    4       1acfe1ee        1b2ff61f        2e8b2631        6faef306        c6fc10d3    6fcd6dcb           
    0               24              27      5               0       2       1               3       10064           9a8cb066        7a06385f        417e6103        2170fc56        acf676aa    6fcd6dcb                      

V této datové sady je chybějící hodnoty ve sloupcích číselné a kategorií. Je popsána jednoduchý způsob zpracování chybějící hodnoty. Další podrobnosti dat jsou prozkoumali při ukládání do tabulek Hive.

**Definice:** *Interaktivní kurz (PEV.cenu):* Toto je procento kliknutí v datech. V této datové sadě Criteo PEV.cenu je přibližně % 3.3 nebo 0.033.

## <a name="mltasks"></a>Příklady předpovědi úlohy
Dva ukázkové předpovědi problémy řešeny v tomto průvodci:

1. **Binární klasifikace**: předpovídá, zda uživatel klikli add:
   
   * Třída 0: Žádné klikněte na
   * Třídy 1: klikněte na
2. **Regrese**: předpovídá pravděpodobnost ad klikněte na z funkcí uživatelů.

## <a name="setup"></a>Nastavit až HDInsight Hadoop clusteru pro vědecké zpracování dat
**Poznámka:** se obvykle jedná **správce** úloh.

Nastavení prostředí vědecké zpracování dat Azure pro vytváření řešení prediktivní analýzy s clustery HDInsight v tři kroky:

1. [Vytvoření účtu úložiště](../../storage/common/storage-create-storage-account.md): Tento účet úložiště se používá k ukládání dat do Azure Blob Storage. Data použitá v clusterech HDInsight je zde uloženy.
2. [Přizpůsobení clusterů systému Hadoop HDInsight Azure pro vědecké zpracování dat](customize-hadoop-cluster.md): Tento krok vytvoří s 64-bit Anaconda Python 2.7 nainstalované na všech uzlech clusteru Azure HDInsight Hadoop. Existují dva důležité kroky (popsaný v tomto tématu) provést při přizpůsobení clusteru HDInsight.
   
   * Je nutné propojit vytvořili v kroku 1 k vašemu clusteru HDInsight při vytvoření účtu úložiště. Tento účet úložiště se používá pro přístup k datům, která může být zpracována v rámci clusteru.
   * Po vytvoření je nutné povolit vzdálený přístup k hlavnímu uzlu clusteru. Pamatovat přihlašovací údaje vzdáleného přístupu, zde určíte (liší od nastavení zadané pro cluster při jeho vytváření): je třeba, aby proveďte následující postupy.
3. [Vytvořit pracovní prostor služby Azure ML](../studio/create-workspace.md): Tento Azure Machine Learning prostoru slouží k vytváření modelů machine learning po zkoumání počáteční data a dolů vzorkování v clusteru HDInsight.

## <a name="getdata"></a>Získání a využívat data z veřejné zdroje
[Criteo](http://labs.criteo.com/downloads/download-terabyte-click-logs/) datové sady je přístupná kliknutím na odkaz, vyjádření souhlasu s podmínkami použití a poskytnutím název. Zobrazí se zde snímek co vypadá takto:

![Přijměte podmínky Criteo](./media/hive-criteo-walkthrough/hLxfI2E.png)

Klikněte na tlačítko **pokračovat ke stažení** Další informace o sadě dat a jeho dostupnost.

Data se nachází v veřejné [úložiště objektů blob Azure](../../storage/blobs/storage-dotnet-how-to-use-blobs.md) umístění: wasb://criteo@azuremlsampleexperiments.blob.core.windows.net/raw/. "wasb" odkazuje na umístění úložiště objektů Blob Azure. 

1. Data v této veřejného objektu blob úložiště se skládá z tři podsložky rozbalené data.
   
   1. Podsložky *nezpracovanou nebo počet/* obsahuje první 21 dnů dat – dni\_00 den\_20
   2. Podsložky *nezpracovanou nebo train/* se skládá z jednoho dne dat, den\_21
   3. Podsložky *nezpracovaná/testovací/* se skládá ze dvou dnů dat, den\_22 a den\_23
2. Pro osoby, které chcete začít s daty nezpracovaná gzip, ty jsou také k dispozici ve složce hlavní *nezpracovanou nebo* jako day_NN.gz, kde NN přejde od 00 do 23.

Alternativní způsob přístup, prozkoumat a model, který tato data, která nevyžaduje žádné místní stažení se vysvětluje dále v tomto návodu při vytváření tabulek Hive.

## <a name="login"></a>Přihlaste se k headnode clusteru
K přihlášení do headnode clusteru, použijte [portál Azure](https://ms.portal.azure.com) najít clusteru. Klikněte na ikonu Slon HDInsight na levé straně a pak dvakrát klikněte na název clusteru. Přejděte na **konfigurace** kartě, dvakrát klikněte na ikonu připojit v dolní části stránky a zadejte své přihlašovací údaje vzdáleného přístupu při zobrazení výzvy. Tím přejdete na headnode clusteru.

Zde je typické první protokol v na clusteru headnode vypadá takto:

![Přihlaste se ke clusteru](./media/hive-criteo-walkthrough/Yys9Vvm.png)

Na levé straně je "Hadoop příkazového řádku", což je naše Centrem pro zkoumání dat. Všimněte si dvě užitečné adresy URL - "Hadoop Yarn stav" a "Hadoop název uzlu". Adresa URL yarn stavu zobrazuje průběh úlohy a adresu URL uzlu název obsahuje údaje o konfiguraci clusteru.

Nyní jsou nastavené a připravené k zahájení první část tohoto návodu: zkoumání dat pomocí Hive a získání dat připravené pro Azure Machine Learning.

## <a name="hive-db-tables"></a> Vytvoření databáze Hive a tabulky
Chcete-li vytvořit tabulek Hive pro naše Criteo datovou sadu, otevřete ***Hadoop příkazového řádku*** na ploše hlavního uzlu a zadejte adresář Hive zadáním příkazu

    cd %hive_home%\bin

> [!NOTE]
> Spusťte všechny příkazy Hive v tomto návodu z podregistru bin / directory řádku. To postará o všech problémech, cesta automaticky. Můžete použít s podmínkami "Hive directory výzva", "Hive bin / directory řádku", "Hadoop příkazový řádek" a zcela zaměnitelným významem.
> 
> [!NOTE]
> K provedení jakýkoli dotaz Hive, jeden vždy použít následující příkazy:
> 
> 

        cd %hive_home%\bin
        hive

Jakmile se objeví Hive REPL s "hive >"přihlásit, stačí vyjmout a vložit dotaz tak, aby ho provést.

Následující kód vytvoří databázi "criteo" a poté generuje 4 tabulky:

* *tabulku pro generování počty* založený na den dnů\_00 den\_20,
* *tabulky pro použití jako datové sady train* založený na den\_21, a
* dva *tabulky pro použití jako testovací datové sady* založený na den\_22 a den\_23 v uvedeném pořadí.

Rozdělte testovací datové sady na dvou různých tabulek, protože jedna dny volný den. Cílem je určit, pokud model zjistit rozdíly mezi svátek a bez svátek kurz, klikněte na tlačítko prostřednictvím.

Skript [ukázka&#95;hive&#95;vytvořit&#95;criteo&#95;databáze&#95;a&#95;tables.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_create_criteo_database_and_tables.hql) se zde zobrazí ke zvýšení pohodlí:

    CREATE DATABASE IF NOT EXISTS criteo;
    DROP TABLE IF EXISTS criteo.criteo_count;
    CREATE TABLE criteo.criteo_count (
    col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 string)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
    LINES TERMINATED BY '\n'
    STORED AS TEXTFILE LOCATION 'wasb://criteo@azuremlsampleexperiments.blob.core.windows.net/raw/count';

    DROP TABLE IF EXISTS criteo.criteo_train;
    CREATE TABLE criteo.criteo_train (
    col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 string)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
    LINES TERMINATED BY '\n'
    STORED AS TEXTFILE LOCATION 'wasb://criteo@azuremlsampleexperiments.blob.core.windows.net/raw/train';

    DROP TABLE IF EXISTS criteo.criteo_test_day_22;
    CREATE TABLE criteo.criteo_test_day_22 (
    col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 string)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
    LINES TERMINATED BY '\n'
    STORED AS TEXTFILE LOCATION 'wasb://criteo@azuremlsampleexperiments.blob.core.windows.net/raw/test/day_22';

    DROP TABLE IF EXISTS criteo.criteo_test_day_23;
    CREATE TABLE criteo.criteo_test_day_23 (
    col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 string)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
    LINES TERMINATED BY '\n'
    STORED AS TEXTFILE LOCATION 'wasb://criteo@azuremlsampleexperiments.blob.core.windows.net/raw/test/day_23';

Tyto tabulky jsou externí, takže můžete jednoduše přejděte na jejich umístění Azure Blob Storage (wasb).

**Existují dva způsoby, jak provést dotaz ANY Hive:**

1. **Pomocí příkazového řádku Hive REPL**: první je vydat příkaz "hive" a zkopírujte a vložte dotaz na REPL Hive, příkazového řádku. Chcete-li to provést, postupujte:
   
        cd %hive_home%\bin
        hive
   
     Nyní v REPL příkazového řádku, vyjímání a vkládání dotaz provede ji.
2. **Uložení dotazů do souboru a provádění příkazu**: druhý přístup spočívá v dotazech uložit do souboru .hql ([ukázka&#95;hive&#95;vytvořit&#95;criteo&#95;databáze&#95;a&#95;tables.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_create_criteo_database_and_tables.hql)) a potom vydat příkaz k provedení dotazu:
   
        hive -f C:\temp\sample_hive_create_criteo_database_and_tables.hql

### <a name="confirm-database-and-table-creation"></a>Potvrďte vytváření databáze a tabulky
Dále zkontrolujte vytvoření databáze pomocí následujícího příkazu z podregistru bin / directory řádku:

        hive -e "show databases;"

Díky tomu jsou:

        criteo
        default
        Time taken: 1.25 seconds, Fetched: 2 row(s)

Vytvoření nové databáze, "criteo" potvrdíte.

Pokud chcete zobrazit, jaké tabulky byly vytvořeny, jednoduše vydejte příkaz zde z koše Hive / directory řádku:

        hive -e "show tables in criteo;"

Potom byste měli vidět následující výstup:

        criteo_count
        criteo_test_day_22
        criteo_test_day_23
        criteo_train
        Time taken: 1.437 seconds, Fetched: 4 row(s)

## <a name="exploration"></a> Zkoumání dat v Hive
Nyní jste připraveni provést některé základní data zkoumání v Hive. Začněte tím, že určovat počet příklady vlaku a testování datových tabulek.

### <a name="number-of-train-examples"></a>Počet train příklady
Obsah [ukázka&#95;hive&#95;počet&#95;cvičení&#95;tabulky&#95;examples.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_count_train_table_examples.hql) zde se zobrazují:

        SELECT COUNT(*) FROM criteo.criteo_train;

Dostaneme:

        192215183
        Time taken: 264.154 seconds, Fetched: 1 row(s)

Alternativně jeden může také vydejte následující příkaz z podregistru bin / directory řádku:

        hive -f C:\temp\sample_hive_count_criteo_train_table_examples.hql

### <a name="number-of-test-examples-in-the-two-test-datasets"></a>Počet testů příklady v dvě datové sady testů
Nyní určený počet příklady v dvě testovací datové sady. Obsah [ukázka&#95;hive&#95;počet&#95;criteo&#95;testování&#95;den&#95;22&#95;tabulky&#95;examples.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_count_criteo_test_day_22_table_examples.hql) jsou tady:

        SELECT COUNT(*) FROM criteo.criteo_test_day_22;

Dostaneme:

        189747893
        Time taken: 267.968 seconds, Fetched: 1 row(s)

Obvyklým způsobem, může také zavolat skript z podregistru bin / directory řádku vydání příkazu:

        hive -f C:\temp\sample_hive_count_criteo_test_day_22_table_examples.hql

Nakonec zkontrolujte počet příklady testů v testovací datové sady na základě dne\_23.

Příkaz k tomu je podobná právě uvedeno (odkazovat na [ukázka&#95;hive&#95;počet&#95;criteo&#95;testování&#95;den&#95;23&#95;examples.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_count_criteo_test_day_23_examples.hql)):

        SELECT COUNT(*) FROM criteo.criteo_test_day_23;

Díky tomu jsou:

        178274637
        Time taken: 253.089 seconds, Fetched: 1 row(s)

### <a name="label-distribution-in-the-train-dataset"></a>Popisek distribuce v datové sadě train
Popisek distribuce v datové sadě train je určen. Tím zobrazíte zobrazit obsah [ukázka&#95;hive&#95;criteo&#95;popisek&#95;distribuční&#95;cvičení&#95;table.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_label_distribution_train_table.hql):

        SELECT Col1, COUNT(*) AS CT FROM criteo.criteo_train GROUP BY Col1;

Popisek distribuce dostaneme:

        1       6292903
        0       185922280
        Time taken: 459.435 seconds, Fetched: 2 row(s)

Všimněte si, že procento kladné popisky přibližně 3.3 % (konzistentní s původní datové sady).

### <a name="histogram-distributions-of-some-numeric-variables-in-the-train-dataset"></a>Histogram distribuce některých číselné proměnných v datové sadě train
Můžete použít na Hive nativní "histogram\_číselné" funkce a zjistěte, distribuci číselné proměnné, která bude vypadat takto. Tady jsou obsah [ukázka&#95;hive&#95;criteo&#95;histogram&#95;numeric.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_histogram_numeric.hql):

        SELECT CAST(hist.x as int) as bin_center, CAST(hist.y as bigint) as bin_height FROM
            (SELECT
            histogram_numeric(col2, 20) as col2_hist
            FROM
            criteo.criteo_train
            ) a
            LATERAL VIEW explode(col2_hist) exploded_table as hist;

Dostaneme následující:

        26      155878415
        2606    92753
        6755    22086
        11202   6922
        14432   4163
        17815   2488
        21072   1901
        24113   1283
        27429   1225
        30818   906
        34512   723
        38026   387
        41007   290
        43417   312
        45797   571
        49819   428
        53505   328
        56853   527
        61004   160
        65510   3446
        Time taken: 317.851 seconds, Fetched: 20 row(s)

LATERÁLNÍ zobrazení – rozbalit kombinace v Hive slouží k vytváření výstupu SQL jako místo obvyklé seznamu. Všimněte si, že se v této tabulce, první sloupec odpovídá center Koš a druhou pro frekvenci Koš.

### <a name="approximate-percentiles-of-some-numeric-variables-in-the-train-dataset"></a>Přibližná percentily některé číselné proměnné v datové sadě train
Také týkající se s číselnou proměnné je výpočet přibližnou percentily. Hive je nativní "percentilu\_přibl" tomu pro nás. Obsah [ukázka&#95;hive&#95;criteo&#95;přibližnou&#95;percentiles.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_approximate_percentiles.hql) jsou:

        SELECT MIN(Col2) AS Col2_min, PERCENTILE_APPROX(Col2, 0.1) AS Col2_01, PERCENTILE_APPROX(Col2, 0.3) AS Col2_03, PERCENTILE_APPROX(Col2, 0.5) AS Col2_median, PERCENTILE_APPROX(Col2, 0.8) AS Col2_08, MAX(Col2) AS Col2_max FROM criteo.criteo_train;

Dostaneme:

        1.0     2.1418600917169246      2.1418600917169246    6.21887086390288 27.53454893115633       65535.0
        Time taken: 564.953 seconds, Fetched: 1 row(s)

Distribuce percentily souvisí s distribuční histogram jakékoli číselné proměnné obvykle.         

### <a name="find-number-of-unique-values-for-some-categorical-columns-in-the-train-dataset"></a>Najít počet jedinečných hodnot pro některé kategorií sloupců v datové sadě train
Pokračováním zkoumání dat, najdete, pro některé sloupce kategorií počet jedinečných hodnot, které jejich trvat. K tomuto účelu zobrazit obsah [ukázka&#95;hive&#95;criteo&#95;jedinečný&#95;hodnoty&#95;categoricals.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_unique_values_categoricals.hql):

        SELECT COUNT(DISTINCT(Col15)) AS num_uniques FROM criteo.criteo_train;

Dostaneme:

        19011825
        Time taken: 448.116 seconds, Fetched: 1 row(s)

Všimněte si, že Col15 má jedinečné hodnoty 19M! Pomocí technik naïve jako "horkou jeden kódování" ke kódování takovéto vysokou dimenzí kategorií proměnné není možné je. Konkrétně efektivní a výkonné technika volá [Learning s počty](http://blogs.technet.com/b/machinelearning/archive/2015/02/17/big-learning-made-easy-with-counts.aspx) pro efektivní řešení tohoto problému je vysvětlené a ukázán.

Nakonec se podívejte na počet jedinečných hodnot pro některé kategorií sloupce také. Obsah [ukázka&#95;hive&#95;criteo&#95;jedinečný&#95;hodnoty&#95;více&#95;categoricals.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_unique_values_multiple_categoricals.hql) jsou:

        SELECT COUNT(DISTINCT(Col16)), COUNT(DISTINCT(Col17)),
        COUNT(DISTINCT(Col18), COUNT(DISTINCT(Col19), COUNT(DISTINCT(Col20))
        FROM criteo.criteo_train;

Dostaneme:

        30935   15200   7349    20067   3
        Time taken: 1933.883 seconds, Fetched: 1 row(s)

Znovu si všimněte, že s výjimkou Col20, všechny sloupce mají mnoho jedinečné hodnoty.

### <a name="co-occurrence-counts-of-pairs-of-categorical-variables-in-the-train-dataset"></a>Společné výskyt počty párů kategorií proměnných v datové sadě train

Počty společné výskyt párů kategorií proměnných je také určen. To se dá určit pomocí kódu v [ukázka&#95;hive&#95;criteo&#95;spárované&#95;kategorií&#95;counts.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_paired_categorical_counts.hql):

        SELECT Col15, Col16, COUNT(*) AS paired_count FROM criteo.criteo_train GROUP BY Col15, Col16 ORDER BY paired_count DESC LIMIT 15;

Zpětného řadit počty podle jejich výskytu a podívejte se na horní 15 v tomto případě. To nám poskytuje:

        ad98e872        cea68cd3        8964458
        ad98e872        3dbb483e        8444762
        ad98e872        43ced263        3082503
        ad98e872        420acc05        2694489
        ad98e872        ac4c5591        2559535
        ad98e872        fb1e95da        2227216
        ad98e872        8af1edc8        1794955
        ad98e872        e56937ee        1643550
        ad98e872        d1fade1c        1348719
        ad98e872        977b4431        1115528
        e5f3fd8d        a15d1051        959252
        ad98e872        dd86c04a        872975
        349b3fec        a52ef97d        821062
        e5f3fd8d        a0aaffa6        792250
        265366bf        6f5c7c41        782142
        Time taken: 560.22 seconds, Fetched: 15 row(s)

## <a name="downsample"></a> Dolů ukázkové sady dat pro Azure Machine Learning
S prozkoumali datové sady a ukázal, jak lze provést tento typ zkoumání pro všechny proměnné (včetně kombinace), dolů ukázkových datových sad, aby se dají vytvářet modely v Azure Machine Learning. Odvolání, který je zaměřená problém: danou sadu atributů příklad (funkce hodnoty z Col2 - Col40), předpovědi, pokud je Sloupec1 0 (žádné kliknutím) nebo 1 (kliknutím).

Ukázka vlaku a testovací datové sady, které 1 % původní velikost používat nativní funkce RAND() na Hive. Další skriptu [ukázka&#95;hive&#95;criteo&#95;převzorkovat&#95;cvičení&#95;dataset.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_downsample_train_dataset.hql) tomu pro datovou sadu train:

        CREATE TABLE criteo.criteo_train_downsample_1perc (
        col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 string)
        ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
        LINES TERMINATED BY '\n'
        STORED AS TEXTFILE;

        ---Now downsample and store in this table

        INSERT OVERWRITE TABLE criteo.criteo_train_downsample_1perc SELECT * FROM criteo.criteo_train WHERE RAND() <= 0.01;

Dostaneme:

        Time taken: 12.22 seconds
        Time taken: 298.98 seconds

Skript [ukázka&#95;hive&#95;criteo&#95;převzorkovat&#95;testování&#95;den&#95;22&#95;dataset.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_downsample_test_day_22_dataset.hql) nemá pro testovací data den\_22:

        --- Now for test data (day_22)

        CREATE TABLE criteo.criteo_test_day_22_downsample_1perc (
        col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 string)
        ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
        LINES TERMINATED BY '\n'
        STORED AS TEXTFILE;

        INSERT OVERWRITE TABLE criteo.criteo_test_day_22_downsample_1perc SELECT * FROM criteo.criteo_test_day_22 WHERE RAND() <= 0.01;

Dostaneme:

        Time taken: 1.22 seconds
        Time taken: 317.66 seconds


Nakonec skript [ukázka&#95;hive&#95;criteo&#95;převzorkovat&#95;testování&#95;den&#95;23&#95;dataset.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_downsample_test_day_23_dataset.hql) nemá pro testovací data den\_23:

        --- Finally test data day_23
        CREATE TABLE criteo.criteo_test_day_23_downsample_1perc (
        col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 srical feature; tring)
        ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
        LINES TERMINATED BY '\n'
        STORED AS TEXTFILE;

        INSERT OVERWRITE TABLE criteo.criteo_test_day_23_downsample_1perc SELECT * FROM criteo.criteo_test_day_23 WHERE RAND() <= 0.01;

Dostaneme:

        Time taken: 1.86 seconds
        Time taken: 300.02 seconds

S tím budete chtít používat naše dolů jen Vzorkovaná train a testovací datové sady pro vytváření modelů v Azure Machine Learning.

Než přejdete na Azure Machine Learning, která se týká počtu tabulky není konečné důležité součásti. V následující dílčí části tabulky počet podrobněji některé.

## <a name="count"></a> Stručný diskuzi na počet tabulky
Jak už jste viděli, několik kategorií proměnné mají velmi vysoká dimenzionalitu. V tomto návodu výkonné technika volá [Learning s počty](http://blogs.technet.com/b/machinelearning/archive/2015/02/17/big-learning-made-easy-with-counts.aspx) ke kódování těchto proměnných v efektivní, se zobrazí robustní způsobem. Další informace o této technice je v odkazu.

[!NOTE]
>V tomto návodu je zaměřená na použití počet tabulek k vytvoření compact reprezentace vysokou dimenzí kategorií funkce. Toto není jediný způsob, jak kódování kategorií funkcí. Další informace o jinými technikami, můžete také zajímat, uživatelé rezervovat [jeden hot kódování](http://en.wikipedia.org/wiki/One-hot) a [hashování](http://en.wikipedia.org/wiki/Feature_hashing).
>

Pro sestavení počet tabulek na základě počtu dat, použijte data ve složce nezpracovanou nebo počet. Uživatelům se zobrazí v části modelování, jak vytvářet tyto tabulky počet kategorií funkcí od začátku, nebo můžete také použít tabulku předdefinovaných počet pro jejich explorations. V jaké způsobem když "předem vytvořené tabulky počet" se označují, jsme znamenat pomocí počet tabulek, které byly zadány. Podrobné pokyny o tom, jak tyto tabulky přístupu jsou uvedené v následující části.

## <a name="aml"></a> Vytvoření modelu pomocí Azure Machine Learning
Naše model procesu v Azure Machine Learning vytváření zahrnuje následující kroky:

1. [Získat data z tabulek Hive do Azure Machine Learning](#step1)
2. [Vytvoření experimentu: Vyčištění dat a featurize s počet tabulek](#step2)
3. [Sestavení, školení a modul score model](#step3)
4. [Vyhodnocení modelu](#step4)
5. [Publikování model jako webovou službu](#step5)

Nyní jste připraveni k sestavení modely v Azure Machine Learning studio. Naše dolů jen Vzorkovaná data se uloží jako tabulek Hive v clusteru. Pomocí Azure Machine Learning **importovat Data** modul tato data přečíst. Přihlašovací údaje pro přístup k účtu úložiště tohoto clusteru jsou uvedené v následující.

### <a name="step1"></a> Krok 1: Načíst data do Azure Machine Learning pomocí modulu importovat Data z tabulek Hive a vyberte pro experimentu strojového učení
Začněte výběrem **+ nový** -> **EXPERIMENTU** -> **prázdný Experiment**. Potom z **vyhledávání** pole nahoře vlevo, vyhledejte "Importovat Data". Přetáhnout myší **importovat Data** modulu k experimentu plátno (střední část obrazovky) pro použití modulu pro přístup k datům.

To znamená, co **importovat Data** vypadá podobně jako při získávání dat z tabulky Hive:

![Umožňuje importovat Data získá data](./media/hive-criteo-walkthrough/i3zRaoj.png)

Pro **importovat Data** modulu, hodnoty parametrů, které jsou součástí na obrázku jsou jenom příklady řazení hodnot, budete muset zadat. Tady je některé obecné pokyny o tom, jak vyplnit samostatné sady parametrů **importovat Data** modulu.

1. Volba "Dotaz Hive" pro **zdroje dat**
2. V **databázový dotaz Hive** pole jednoduchý příkaz SELECT * FROM < vaše\_databáze\_name.your\_tabulky\_název >-je dost.
3. **Identifikátor URI serveru Hcatalog**: Pokud je váš cluster "abc", pak toto je jednoduše: https://abc.azurehdinsight.net
4. **Název uživatelského účtu Hadoop**: uživatelské jméno zvolena při uvedení do provozu clusteru. (Není název vzdáleného přístupu uživatele!)
5. **Heslo uživatelského účtu Hadoop**: heslo pro uživatelské jméno zvolena při uvedení do provozu clusteru. (Není heslo vzdáleného přístupu!)
6. **Umístění výstupu dat**: Zvolte "Azure"
7. **Název účtu úložiště Azure**: účet úložiště, který je přidružen ke clusteru
8. **Klíč účtu úložiště Azure**: klíč účtu úložiště, které jsou přidruženy ke clusteru.
9. **Název kontejneru Azure**: Pokud je název clusteru "abc", pak toto je jednoduše "abc", obvykle.

Jednou **importovat Data** dokončení získání dat (zobrazí zelená značka na modul), uložte tato data jako datové sady (s názvem podle svého výběru). Co to vypadá takto:

![Umožňuje importovat Data uložit data](./media/hive-criteo-walkthrough/oxM73Np.png)

Klikněte pravým tlačítkem na výstupní port modulu **importovat Data** modulu. Zobrazí se **uložit jako datovou sadu** možnost a **vizualizovat** možnost. **Vizualizovat** možnost, pokud kliknutí zobrazí 100 řádků dat, včetně pravém panelu, které jsou užitečné pro některé souhrnné statistiky. Chcete-li uložit data, jednoduše vyberte **uložit jako datovou sadu** a postupujte podle pokynů.

Chcete-li vybrat uložené datové sady pro použití v experimentu machine learning, vyhledejte pomocí datové sady **vyhledávání** okno zobrazené na následujícím obrázku. Jednoduše zadejte název dáte datovou sadu částečně k přístupu a přetáhněte datovou sadu na hlavní panel. Umístíte jej na hlavní panel vybere pro použití v machine learning modelování.

![Datová sada Drage na hlavní panel](./media/hive-criteo-walkthrough/cl5tpGw.png)

> [!NOTE]
> To lze proveďte pro vlaku a testovací datové sady. Kromě toho musíte použít název databáze a názvy tabulek, které jste zadali pro tento účel. Hodnoty používané na obrázku jsou výhradně pro obrázek purposes.* *
> 
> 

### <a name="step2"></a> Krok 2: Vytvoření jednoduchého experimentu v nástroji Azure Machine Learning k předvídání klikne na tlačítko nebo bez kliknutí
Naše Azure ML experimentu vypadá takto:

![Machine Learning experimentu](./media/hive-criteo-walkthrough/xRpVfrY.png)

Nyní zkontrolujte klíčové komponenty tohoto experimentu. Přetáhněte naše uložené train a nejdřív otestovat datové sady na našem plátno experimentu.

#### <a name="clean-missing-data"></a>Vyčištění chybějících dat
**Vyčištění chybějících dat** modul nemá naznačuje název: ho vyčistí chybějící data způsobem, který může být zadán uživatel. Podívejte se do tohoto modulu toto:

![Vyčištění chybějících dat](./media/hive-criteo-walkthrough/0ycXod6.png)

Zde se rozhodli nahraďte všechny chybějící hodnoty 0. Existují další možnosti, které se můžete podívat prohlížením rozevíracích seznamů v modulu.

#### <a name="feature-engineering-on-the-data"></a>Konstruování na data
Může existovat miliony jedinečné hodnoty pro některé kategorií funkce rozsáhlých datových sad. Pomocí metod naïve třeba jeden horkou kódování představující vysokou dimenzí kategorií funkce, je zcela nebude proveditelné. Tento návod ukazuje, jak používat funkce count pomocí integrované moduly Azure Machine Learning k vygenerování compact reprezentace těchto vysoce dimenzí kategorií proměnných. Konečný výsledek je menší velikost modelu, školení rychlejší a metriky výkonu, které jsou poměrně srovnatelná pomocí jiných metod.

##### <a name="building-counting-transforms"></a>Vytváření počítání transformací
Chcete-li vytvořit funkce count, použijte **sestavení počítání transformace** modul, který je k dispozici v Azure Machine Learning. Modul vypadá takto:

![Sestavení modulu počítání transformace](./media/hive-criteo-walkthrough/e0eqKtZ.png)
![sestavení počítání transformace modulu](./media/hive-criteo-walkthrough/OdDN0vw.png)

> [!IMPORTANT] 
> V **počet sloupců** zadejte tyto sloupce, které chcete provést počty na. Obvykle jde o (jak je uvedeno) vysokou dimenzí kategorií sloupců. Mějte na paměti, že Criteo datová sada má 26 kategorií sloupce: z Col15 k Col40. Zde počet všem z nich a poskytněte jejich indexy (od 15 do 40 oddělených čárkami, jak je znázorněno).
> 

Pokud chcete použít modul v režimu MapReduce (vhodné pro velké datové sady), potřebujete přístup ke clusteru HDInsight Hadoop (používaný pro zkoumání funkce lze znovu použít pro tento účel také) a přihlašovací údaje. Předchozí následující obrázky znázorňují, jaké vyplněné hodnoty vypadá jako (Nahraďte hodnoty ilustrativní s těmi, které jsou relevantní pro vlastní případ použití).

![Parametry modulu](./media/hive-criteo-walkthrough/05IqySf.png)

Předchozí obrázek ukazuje, jak zadat umístění vstupního objektu blob. Toto umístění obsahuje data vyhrazené pro vytváření počet tabulek.

Po dokončení spuštění tento modul uložit transformace pro později kliknutím pravým tlačítkem myši na modul a výběrem možnosti **uložit jako transformace** možnost:

![Možnost "Uložit jako transformace"](./media/hive-criteo-walkthrough/IcVgvHR.png)

V našem experimentu architektuře výše uvedeném datovou sadu "ytransform2" odpovídá přesněji uložené počet transformace. Pro zbývající část tohoto experimentu, se předpokládá, že čtečka používat **sestavení počítání transformace** modul na některá data pro generování počty a můžete pak použít tyto počty ke generování počet funkce na train a testovací datové sady.

##### <a name="choosing-what-count-features-to-include-as-part-of-the-train-and-test-datasets"></a>Výběr, jaký počet funkce má být zahrnut jako součást train a testovací datové sady
Jednou počet transformace připravené, uživatel může vybrat, jaké funkce, které chcete zahrnout do své train a testování pomocí datové sady **upravit parametry tabulky počet** modulu. Pro úplnost je tento modul zobrazeny zde. Ale v zájmu jednoduchosti nepoužívejte ve skutečnosti ho v našem experimentu.

![Upravit parametry počet tabulky](./media/hive-criteo-walkthrough/PfCHkVg.png)

V takovém případě jak je vidět, je pravděpodobnost protokolu mají být použita a regrese sloupec je ignorována. Můžete také nastavit parametry, třeba bin prahová hodnota paměti, kolik částečně předchozí příklady přidat pro vyhlazení a jestli se má použít jakékoli Laplacian šumu nebo ne. Všechny tyto pokročilé funkce a je potřeba poznamenat, že výchozí hodnoty jsou to dobrý výchozí bod pro uživatele, kteří jsou nové pro tento typ funkce generování.

##### <a name="data-transformation-before-generating-the-count-features"></a>Transformace dat před vygenerováním počet funkcí
Teď zaměřuje se na důležité bodu o transformace naše train a testovacích dat před ve skutečnosti generování funkce count. Všimněte si, že existují dvě **spustit skript jazyka R** moduly používané před použitím transformace počet pro naše data.

![Spustit skript jazyka R moduly](./media/hive-criteo-walkthrough/aF59wbc.png)

Tady je první R skript:

![První skript jazyka R](./media/hive-criteo-walkthrough/3hkIoMx.png)

Tento skript jazyka R přejmenuje naše sloupců na názvy "Sloupec1" na "Col40". Je to proto, že počet transformace očekává názvy tohoto formátu.

Druhý skript jazyka R vyrovnává rozdělení mezi kladné a záporné třídy (třídy 1 a 0) podle nižší – vzorkování záporné – třída. R skript ukazuje, jak to udělat:

![Druhý skript jazyka R](./media/hive-criteo-walkthrough/91wvcwN.png)

V této jednoduché skript jazyka R "pos\_záporné\_poměr" se používá k nastavení velikosti rovnováhu mezi kladnou a zápornou třídy. To je důležité pro protože vylepšení nevyváženosti třída obvykle má výhody výkonu pro klasifikaci problémy, kde je třída distribuce nesouměrně rozdělí (odvolání, v takovém případě máte 3.3 % kladné a záporné třída 96.7 %).

##### <a name="applying-the-count-transformation-on-our-data"></a>Použití transformace počet na našich dat
Nakonec můžete použít **použít transformaci** modulu pro použití transformace počet na našem train a testovací datové sady. Tento modul trvá transformace uložené počet jako jeden vstup a train nebo testovací datové sady jako jiného vstupu a vrátí data pomocí funkce count. V tomto poli se zobrazí:

![Použít modul transformace](./media/hive-criteo-walkthrough/xnQvsYf.png)

##### <a name="an-excerpt-of-what-the-count-features-look-like"></a>Výňatek funkcí počet vypadat
Je významné vzhled počet funkcí v našem případě zobrazit. Tady je výňatek tohoto:

![Počet funkcí](./media/hive-criteo-walkthrough/FO1nNfw.png)

Tato výňatek ze ukazuje, že pro sloupce počítají, můžete získat počty a protokolu pravděpodobnost kromě všechny relevantní backoffs.

Nyní jste připraveni k sestavení model Azure Machine Learning použití těchto transformovaných datových sad. V další části ukazuje, jak to lze provést.

### <a name="step3"></a> Krok 3: Vytvoření, školení a modul score model

#### <a name="choice-of-learner"></a>Volba student
Nejprve budete muset zvolit student. Použijte jako naše student two-class boosted rozhodovacího stromu. Tady jsou výchozí možnosti pro tento student:

![Two-Class Boosted Decision Tree parametry](./media/hive-criteo-walkthrough/bH3ST2z.png)

Experiment vyberte výchozí hodnoty. Všimněte si, že výchozí hodnoty jsou obvykle smysluplný a vhodný způsob, jak získat rychlý standardních hodnot na výkon. Můžete zlepšit výkon komínů parametry, pokud se rozhodnete, až budete mít směrný plán.

#### <a name="train-the-model"></a>Trénování modelu
Školení, jednoduše vyvolání **Train Model** modulu. Dva vstupy do ní jsou student Two-Class Boosted Decision Tree a naší datové sadě vlaku. To je znázorněno zde:

![Modulu Train Model](./media/hive-criteo-walkthrough/2bZDZTy.png)

#### <a name="score-the-model"></a>Ohodnocení modelu
Jakmile máte modulu trained model, jste připraveni ke stanovení skóre na základě testovací datové a vyhodnotit jeho výkon. To provedete pomocí **Score Model** modulu vidět na následujícím obrázku, spolu s **Evaluate Model** modul:

![Modul Určení skóre modelu](./media/hive-criteo-walkthrough/fydcv6u.png)

### <a name="step4"></a> Krok 4: Vyhodnocení modelu
Nakonec byste měli provést analýzu výkonu modelu. Pro dva – třída (binární) klasifikaci problémy, je dobré míru obvykle AUC. K vizualizaci to, propojte **Score Model** modulu **Evaluate Model** modulu pro tento. Kliknutím na tlačítko **vizualizovat** na **Evaluate Model** modulu vypočítá obrázek stejný, jako je následující:

![Vyhodnocení modelu BDT modulu](./media/hive-criteo-walkthrough/0Tl0cdg.png)

Binární soubor (nebo dvě třídy) oblasti pod křivky (AUC) je klasifikace problémy, dobrý míru přesnost předpovědi. Následující část popisuje naše výsledků pomocí tohoto modelu na naše testovací datové sady. Potřebujete, klikněte pravým tlačítkem na výstupní port modulu **Evaluate Model** modul a potom **vizualizovat**.

![Vizualizace modulu pro vyhodnocení modelu](./media/hive-criteo-walkthrough/IRfc7fH.png)

### <a name="step5"></a> Krok 5: Publikování model jako webovou službu
Umožňuje publikovat jako webové služby s minimální fuss model Azure Machine Learning je cenné funkce pro vytváření široce dostupné. Po dokončení, každý, kdo může provádět volání k webové službě s vstupní data, která potřebují předpovědi a webové služby používá model vrátit těchto předpovědi.

Nejprve je třeba uložte naše trained model jako objekt Trained Model. K tomu je potřeba kliknete pravým tlačítkem **Train Model** modul a pomocí **uložit jako Trained Model** možnost.

V dalším kroku vytvoření vstupní a výstupní porty pro naše webové služby:

* vstupní port vezme všechna data ve formuláři stejné jako data, která potřebujete předpovědi
* na výstupní port vrátí popisky vyhodnocení a spojena pravděpodobnost.

#### <a name="select-a-few-rows-of-data-for-the-input-port"></a>Vyberte pár řádků dat pro vstupní port
Je možné použít **použít transformaci SQL** modul a vyberte právě 10 řádků, který má sloužit jako vstupní port data. Vyberte pouze na tyto řádky dat pro naše vstupní port pomocí dotazu SQL znázorněno zde:

![Vstupní port dat](./media/hive-criteo-walkthrough/XqVtSxu.png)

#### <a name="web-service"></a>Webová služba
Nyní jste připraveni ke spuštění malý experiment, kterou můžete použít k publikování naše webové služby.

#### <a name="generate-input-data-for-webservice"></a>Generovat vstupní data pro webovou službu
Jako zeroth krok vzhledem k tomu, že je velký počet tabulka, trvat pár řádků testovací data a vygenerovat výstupní data z něj pomocí funkce count. To může sloužit jako formát vstupní data pro naše webovou službu. To je znázorněno zde:

![Vytvoření vstupní data BDT](./media/hive-criteo-walkthrough/OEJMmst.png)

> [!NOTE]
> Pro vstupní data formátu, použijte výstup **počet Featurizer** modulu. Jakmile to experimentovat dokončení systémem, uložte si výstup z **počet Featurizer** modulu jako datové sady. Tato datová sada se používá pro vstupní data v webovou službu.
> 
> 

#### <a name="scoring-experiment-for-publishing-webservice"></a>Vyhodnocování experimentu pro publikování webovou službu
Nejprve je zobrazen, jak to vypadá. Základní struktura je **Score Model** modul, který přijímá objekt naše trained model a pár řádků vstupních dat, které byly vygenerovány v předchozích krocích pomocí **počet Featurizer** modulu. "Vyberte sloupců v datové sadě" pomocí projektu popisky Scored a pravděpodobnostech skóre.

![Výběr sloupců v datové sadě](./media/hive-criteo-walkthrough/kRHrIbe.png)

Všimněte si jak **výběr sloupců v datové sadě** modul lze použít pro 'filtrování, data z datové sady. Zde se zobrazují obsah:

![Filtrování s výběr sloupců v datové sadě modulu](./media/hive-criteo-walkthrough/oVUJC9K.png)

Pokud chcete získat blue vstupní a výstupní porty, klepněte na tlačítko **Příprava webservice** v pravé dolní části. Spuštění tohoto experimentu také umožňuje nám publikování webové služby: klikněte na tlačítko **publikování webové služby** ikonu v dolní správné, uvedené tady:

![Publikování webové služby](./media/hive-criteo-walkthrough/WO0nens.png)

Po publikování webovou službu přesměrováni na stránku, která vypadá takto:

![Řídicí panel webové služby](./media/hive-criteo-walkthrough/YKzxAA5.png)

Všimněte si dva odkazy pro webovým službám na levé straně:

* **Požadavků a odpovědí** služby (nebo RRS) je určená pro jeden předpovědi a je, co bylo dílčí tento Workshop.
* **BATCH EXECUTION** služby (BES) se používá pro předpovědi batch a vyžaduje, aby vstupních dat umožňuje provádět předpovědi, které jsou umístěny v úložišti objektů Blob Azure.

Kliknutím na odkaz **požadavků a odpovědí** trvá nám stránky, který nám dává předem konzervované kódu v C#, python a R. Tento kód můžete pohodlně použít pro volání webovou službu. Všimněte si, že klíč rozhraní API na této stránce musí být použit pro ověřování.

Je vhodné tento kód python zkopírovat do nové buňky v poznámkovém bloku IPython.

Zde je segment kódu python se správným klíčem rozhraní API.

![Kód jazyka Python](./media/hive-criteo-walkthrough/f8N4L4g.png)

Všimněte si, že výchozí klíč rozhraní API byla nahrazena s klíčem rozhraní API naše webovým službám. Kliknutím na tlačítko **spustit** v této buňce v IPython vypočítá poznámkového bloku následující odpověď:

![IPython odpovědi](./media/hive-criteo-walkthrough/KSxmia2.png)

Pro dva testovací příklady požádáni o (v rámci JSON skript pythonu), můžete získat zpátky odpovědi ve formátu "Scored označení Scored Pravděpodobnostech". V takovém případě výchozí hodnoty bylo vybráno, poskytuje předdefinovaným kódu (0 je pro všechny číselné sloupce a řetězec "value" pro všechny sloupce kategorií).

Tím končí naše návod znázorňující způsob zpracování rozsáhlé datové sady pomocí Azure Machine Learning. Práce s terabajt dat, sestavený předpovědi modelu a nasadit jako webovou službu v cloudu.

