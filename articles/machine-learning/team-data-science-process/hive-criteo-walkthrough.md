---
title: Použití clusteru Azure HDInsight Hadoop na datové sadě o velikosti 1 TB – proces vědecké analýzy týmových dat
description: Použití procesu vědecké analýzy týmových dat pro komplexní scénář využívající cluster HDInsight Hadoop k sestavení a nasazení modelu pomocí velké veřejně dostupné datové sady (1 TB)
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
ms.openlocfilehash: 218fb96f6960e194f0fc4a4a3a3e603388b961c8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76760806"
---
# <a name="the-team-data-science-process-in-action---using-an-azure-hdinsight-hadoop-cluster-on-a-1-tb-dataset"></a>Proces vědecké ho procesu týmových dat v akci – použití clusteru Azure HDInsight Hadoop v datové sadě o velikosti 1 TB

Tento návod ukazuje, jak používat proces vědecké analýzy týmových dat v komplexním scénáři s [clusterem Azure HDInsight Hadoop](https://azure.microsoft.com/services/hdinsight/) k ukládání, zkoumání, inženýrovi funkcí a ukázkovým datům z jedné z veřejně dostupných datových sad [Criteo.](https://labs.criteo.com/downloads/download-terabyte-click-logs/) Používá Azure Machine Learning k vytvoření binární klasifikační model na tato data. Také ukazuje, jak publikovat jeden z těchto modelů jako webovou službu.

Je také možné použít poznámkový blok IPython k provedení úloh prezentovaných v tomto návodu. Uživatelé, kteří by chtěli vyzkoušet tento přístup, by se měli poradit s [návodem Criteo pomocí tématu připojení Hive ODBC.](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/iPythonNotebooks/machine-Learning-data-science-process-hive-walkthrough-criteo.ipynb)

## <a name="criteo-dataset-description"></a><a name="dataset"></a>Popis datové sady Criteo
Data Criteo je data predikce kliknutí, která je 370 GB komprimovaných souborů TSV gzip (~ 1,3 TB nekomprimované), zahrnující více než 4,3 miliardy záznamů. To je převzato z 24 dnů kliknutí údaje zpřístupnil [Criteo](https://labs.criteo.com/downloads/download-terabyte-click-logs/). Pro pohodlí datových vědců byla data, která máme k dispozici pro experimentování, rozepnuta.

Každý záznam v této datové sadě obsahuje 40 sloupců:

* první sloupec je sloupec popisku, který označuje, zda uživatel klepne na **přídavek** (hodnota 1) nebo na něj neklepne (hodnota 0)
* dalších 13 sloupců je číselných a
* posledních 26 jsou kategorické sloupce

Sloupce jsou anonymizovány a používají řadu výčtových názvů: "Col1" (pro sloupec popisku) až "Col40" (pro poslední kategorický sloupec).

Zde je výňatek z prvních 20 sloupců dvou pozorování (řádků) z této datové sady:

    Col1    Col2    Col3    Col4    Col5    Col6    Col7    Col8    Col9    Col10    Col11    Col12    Col13    Col14    Col15            Col16            Col17            Col18            Col19        Col20

    0       40      42      2       54      3       0       0       2       16      0       1       4448    4       1acfe1ee        1b2ff61f        2e8b2631        6faef306        c6fc10d3    6fcd6dcb
    0               24              27      5               0       2       1               3       10064           9a8cb066        7a06385f        417e6103        2170fc56        acf676aa    6fcd6dcb

V číselných i kategorických sloupcích v této datové sadě chybí hodnoty. Je popsána jednoduchá metoda pro zpracování chybějících hodnot. Další podrobnosti o data jsou prozkoumány při jejich ukládání do tabulek Hive.

**Definice:** *Míra prokliku (CTR):* Tato metrika je procento kliknutí v datech. V této datové sadě Criteo je CTR asi 3,3 % nebo 0,033.

## <a name="examples-of-prediction-tasks"></a><a name="mltasks"></a>Příklady predikčních úloh
V tomto návodu jsou vyřešeny dva problémy s předpovědí vzorku:

1. **Binární klasifikace**: Předpovídá, zda uživatel klepnul na přidání:

   * Třída 0: Bez kliknutí
   * Třída 1: Klikněte na
2. **Regrese**: Předpovídá pravděpodobnost kliknutí na reklamu z uživatelských funkcí.

## <a name="set-up-an-hdinsight-hadoop-cluster-for-data-science"></a><a name="setup"></a>Nastavení clusteru HDInsight Hadoop pro datové vědy
> [!NOTE]
> Tento krok je obvykle úkol **správce.**

Nastavte prostředí Azure Data Science pro vytváření prediktivních analytických řešení s clustery HDInsight ve třech krocích:

1. [Vytvoření účtu úložiště](../../storage/common/storage-account-create.md): Tento účet úložiště se používá k ukládání dat v azure blob storage. Zde jsou uložena data použitá v clusterech HDInsight.
2. [Přizpůsobení clusterů Azure HDInsight Hadoop pro datové vědy:](customize-hadoop-cluster.md)Tento krok vytvoří cluster Azure HDInsight Hadoop s 64bitovým Anaconda Pythonem 2.7 nainstalovaným ve všech uzlech. Existují dva důležité kroky (popsané v tomto tématu) dokončení při přizpůsobení clusteru HDInsight.

   * Propojte účet úložiště vytvořený v kroku 1 s clusterem HDInsight, když je vytvořen. Tento účet úložiště se používá pro přístup k datům, která lze zpracovat v rámci clusteru.
   * Po jeho vytvoření povolte vzdálený přístup k hlavnímu uzlu clusteru. Zapamatujte si pověření vzdáleného přístupu, která zde zadáte (liší cítění od pověření zadaných při vytváření clusteru): proveďte následující postupy.
3. [Vytvořte pracovní prostor Azure Machine Learning Studio (klasický):](../studio/create-workspace.md)Tento pracovní prostor Azure Machine Learning se používá pro vytváření modelů strojového učení po počátečním zkoumání dat a vzorkování v clusteru HDInsight.

## <a name="get-and-consume-data-from-a-public-source"></a><a name="getdata"></a>Získání a využití dat z veřejného zdroje
K datové sadě [Criteo](https://labs.criteo.com/downloads/download-terabyte-click-logs/) se dostanete kliknutím na odkaz, přijetím podmínek použití a zadáním názvu. Zde je zobrazen snímek:

![Přijměte podmínky Criteo](./media/hive-criteo-walkthrough/hLxfI2E.png)

**Chcete-li se stahovat,** klepněte na tlačítko Pokračovat a přečtěte si další informace o datové sadě a její dostupnosti.

Data se nacházejí v umístění úložiště wasb://criteo@azuremlsampleexperiments.blob.core.windows.net/raw/objektů [blob Azure:](../../storage/blobs/storage-dotnet-how-to-use-blobs.md) . "Wasb" odkazuje na umístění úložiště objektů blob Azure.

1. Data v tomto úložišti objektů blob Azure se skládá ze tří podsložek rozbalených dat.

   1. Podsložka *raw/count/* obsahuje prvních 21 dní dat\_- od\_dne 00 do dne 20
   2. Podsložka *raw/train/* se skládá z jednoho dne\_dat, den 21
   3. Podsložka *raw/test/* se skládá ze dvou\_dnů dat,\_den 22 a den 23
2. Nezpracovaná data gzip jsou také k dispozici v hlavní složce *raw/* jako day_NN.gz, kde NN jde od 00 do 23.

Alternativní přístup k přístupu, prozkoumat a modelovat tato data, která nevyžaduje žádné místní stahování je vysvětleno dále v tomto návodu při vytváření tabulek Hive.

## <a name="log-in-to-the-cluster-headnode"></a><a name="login"></a>Přihlášení k hlavnímu uzlu clusteru
Chcete-li se přihlásit k hlavnímu uzlu clusteru, vyhledejte cluster pomocí [portálu Azure.](https://ms.portal.azure.com) Klikněte na ikonu SLONHdInsight vlevo a poklikejte na název clusteru. Přejděte na kartu **Konfigurace,** poklepejte na ikonu CONNECT v dolní části stránky a po zobrazení výzvy zadejte pověření pro vzdálený přístup a přejděte k hlavnímu uzlu clusteru.

Zde je to, co typické první přihlášení do clusteru headnode vypadá takto:

![Přihlásit se do clusteru](./media/hive-criteo-walkthrough/Yys9Vvm.png)

Vlevo je "Hadoop Command Line", což je náš dříč pro zkoumání dat. Všimněte si dvou užitečných adres URL - "Hadoop Yarn Status" a "Hadoop Name Node". Adresa URL stavu příze zobrazuje průběh úlohy a adresa URL uzlu názvu poskytuje podrobnosti o konfiguraci clusteru.

Teď jste nastaveni a připraveni začít první část návodu: zkoumání dat pomocí Hive a příprava dat pro Azure Machine Learning.

## <a name="create-hive-database-and-tables"></a><a name="hive-db-tables"></a>Vytvoření databáze a tabulek Hive
Chcete-li vytvořit tabulky Hive pro naši datovou sadu Criteo, otevřete ***příkazový řádek Hadoop*** na ploše hlavního uzlu a zadejte adresář Hive zadáním příkazu

    cd %hive_home%\bin

> [!NOTE]
> Spusťte všechny příkazy Hive v tomto návodu z výzvy k přihrádce podregistru/adresáře. To se postará o všechny problémy s cestou automaticky. Můžete zaměnitelné termíny "Výzva k adresáři Hive", "Hive bin/ directory prompt" a "Hadoop Command Line".
>
> [!NOTE]
> Chcete-li spustit libovolný dotaz Hive, můžete vždy použít následující příkazy:
>
>        cd %hive_home%\bin
>        hive

Po hive REPL se zobrazí s "úl >"podepsat, jednoduše vyjmout a vložit dotaz k jeho spuštění.

Následující kód vytvoří databázi "criteo" a pak generuje čtyři tabulky:

* *tabulka pro generování počtů* \_postavených ve\_dnech den 00 až 20,
* *tabulku pro použití jako datovou sadu vlaků postavenou* v den\_21 a
* dvě *tabulky pro použití jako testovací datové sady* postavené v den\_22 a den\_23.

Rozdělit testovací datové sady do dvou různých tabulek, protože jeden z dnů je svátek. Cílem je zjistit, zda model dokáže zjistit rozdíly mezi dovolenou a nedovolenou z míry prokliku.

Ukázka [skriptu&#95;podregistr&#95;vytvořit&#95;criteo&#95;databázi&#95;a&#95;tables.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_create_criteo_database_and_tables.hql) je zde zobrazen pro pohodlí:

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

Všechny tyto tabulky jsou externí, takže můžete překážet na jejich umístění azure blob storage (wasb).

**Existují dva způsoby, jak spustit libovolný dotaz Hive:**

* **Použití příkazového řádku Repl úlu**: Prvním je vydat příkaz "podregistr" a zkopírovat a vložit dotaz na příkazovém řádku Repl úlu:

        cd %hive_home%\bin
        hive

     Nyní na příkazovém řádku REPL, řezání a vkládání dotazu provede.
* **Ukládání dotazů do souboru a provádění příkazu**: Druhým je uložení dotazů do souboru '.hql'[(ukázkový podregistr&#95;&#95;vytvořit&#95;criteo&#95;databázi&#95;a&#95;tables.hql)](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_create_criteo_database_and_tables.hql)a potom vydat následující příkaz pro spuštění dotazu:

        hive -f C:\temp\sample_hive_create_criteo_database_and_tables.hql

### <a name="confirm-database-and-table-creation"></a>Potvrzení vytvoření databáze a tabulky
Dále potvrďte vytvoření databáze pomocí následujícího příkazu z příkazu Bin/Directory:

        hive -e "show databases;"

To dává:

        criteo
        default
        Time taken: 1.25 seconds, Fetched: 2 row(s)

To potvrzuje vytvoření nové databáze, "criteo".

Chcete-li zjistit, jaké tabulky byly vytvořeny, jednoduše vyjevíte příkaz zde z příkazu Bin Hive/ adresářového řádku:

        hive -e "show tables in criteo;"

Potom byste měli vidět následující výstup:

        criteo_count
        criteo_test_day_22
        criteo_test_day_23
        criteo_train
        Time taken: 1.437 seconds, Fetched: 4 row(s)

## <a name="data-exploration-in-hive"></a><a name="exploration"></a>Zkoumání dat v Úlu
Nyní jste připraveni provést některé základní zkoumání dat v Hive. Začnete počítáním počtu příkladů v tabulkách dat vlaku a testování.

### <a name="number-of-train-examples"></a>Počet příkladů vlaků
Obsah [ukázkového&#95;&#95;&#95;vlak&#95;tabulky&#95;examples.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_count_train_table_examples.hql) jsou uvedeny zde:

        SELECT COUNT(*) FROM criteo.criteo_train;

To dává:

        192215183
        Time taken: 264.154 seconds, Fetched: 1 row(s)

Případně můžete také vydat následující příkaz z příkazu Bin/Directory:

        hive -f C:\temp\sample_hive_count_criteo_train_table_examples.hql

### <a name="number-of-test-examples-in-the-two-test-datasets"></a>Počet příkladů testu ve dvou testovacích datových sadách
Nyní spočítejte počet příkladů ve dvou testovacích datových sadách. Obsah [vzorku&#95;podregistru&#95;počet&#95;criteo&#95;test&#95;den&#95;22&#95;tabulka&#95;examples.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_count_criteo_test_day_22_table_examples.hql) jsou zde:

        SELECT COUNT(*) FROM criteo.criteo_test_day_22;

To dává:

        189747893
        Time taken: 267.968 seconds, Fetched: 1 row(s)

Jako obvykle můžete také volat skript z příkazu Hive bin/ directory vydáním příkazu:

        hive -f C:\temp\sample_hive_count_criteo_test_day_22_table_examples.hql

Nakonec zkontrolujte počet příkladů testu v testovací datové sadě\_založené na den 23.

Příkaz k tomu je podobný tomu, který je zobrazen (viz [ukázkový&#95;podregistr&#95;počet&#95;criteo&#95;test&#95;den&#95;23&#95;examples.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_count_criteo_test_day_23_examples.hql)):

        SELECT COUNT(*) FROM criteo.criteo_test_day_23;

To dává:

        178274637
        Time taken: 253.089 seconds, Fetched: 1 row(s)

### <a name="label-distribution-in-the-train-dataset"></a>Distribuce popisků v datové sadě vlaku
Distribuce popisků v datové sadě vlaku je zajímavá. Chcete-li to vidět, ukažte obsah [vzorku&#95;podregistru&#95;criteo&#95;označení&#95;distribuce&#95;vlaku&#95;table.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_label_distribution_train_table.hql):

        SELECT Col1, COUNT(*) AS CT FROM criteo.criteo_train GROUP BY Col1;

To dává rozložení popisku:

        1       6292903
        0       185922280
        Time taken: 459.435 seconds, Fetched: 2 row(s)

Procento pozitivních popisků je přibližně 3,3 % (v souladu s původní datovou sadou).

### <a name="histogram-distributions-of-some-numeric-variables-in-the-train-dataset"></a>Histogramové distribuce některých číselných proměnných v datové sadě vlaku
Pomocí nativní funkce Hive "histogram\_numeric" můžete zjistit, jak vypadá distribuce číselných proměnných. Zde je obsah [vzorku&#95;úlu&#95;criteo&#95;histogram&#95;numeric.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_histogram_numeric.hql):

        SELECT CAST(hist.x as int) as bin_center, CAST(hist.y as bigint) as bin_height FROM
            (SELECT
            histogram_numeric(col2, 20) as col2_hist
            FROM
            criteo.criteo_train
            ) a
            LATERAL VIEW explode(col2_hist) exploded_table as hist;

Tím se vynakládají následující:

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

LATERÁLNÍ ZOBRAZENÍ - rozložení kombinace v Hive slouží k vytvoření výstupu podobné SQL namísto obvyklého seznamu. V této tabulce odpovídá první sloupec středu přihrádky a druhý frekvenci přihrádky.

### <a name="approximate-percentiles-of-some-numeric-variables-in-the-train-dataset"></a>Přibližné percentily některých číselných proměnných v datové sadě vlaku
Zajímavé je také výpočtem přibližných percentilů. Hive je nativní "percentil\_cca" to pro nás. Obsah [vzorku&#95;úlu&#95;criteo&#95;přibližné&#95;percentiles.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_approximate_percentiles.hql) jsou:

        SELECT MIN(Col2) AS Col2_min, PERCENTILE_APPROX(Col2, 0.1) AS Col2_01, PERCENTILE_APPROX(Col2, 0.3) AS Col2_03, PERCENTILE_APPROX(Col2, 0.5) AS Col2_median, PERCENTILE_APPROX(Col2, 0.8) AS Col2_08, MAX(Col2) AS Col2_max FROM criteo.criteo_train;

To dává:

        1.0     2.1418600917169246      2.1418600917169246    6.21887086390288 27.53454893115633       65535.0
        Time taken: 564.953 seconds, Fetched: 1 row(s)

Distribuce percentilů úzce souvisí s rozložením histogramu jakékoli číselné proměnné obvykle.

### <a name="find-number-of-unique-values-for-some-categorical-columns-in-the-train-dataset"></a>Najít počet jedinečných hodnot pro některé kategorické sloupce v datové sadě vlaku
Pokračování zkoumání dat, najít, pro některé kategorické sloupce, počet jedinečných hodnot, které berou. Chcete-li to provést, ukažte obsah [ukázkového&#95;úlu&#95;criteo&#95;jedinečné&#95;hodnoty&#95;categoricals.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_unique_values_categoricals.hql):

        SELECT COUNT(DISTINCT(Col15)) AS num_uniques FROM criteo.criteo_train;

To dává:

        19011825
        Time taken: 448.116 seconds, Fetched: 1 row(s)

Col15 má 19M jedinečné hodnoty! Použití naivních technik, jako je "jednohorké kódování", ke kódování těchto vysokorozměrných kategorických proměnných není možné. Zejména je vysvětlena a demonstrována výkonná, robustní technika nazvaná [Learning With Counts](https://blogs.technet.com/b/machinelearning/archive/2015/02/17/big-learning-made-easy-with-counts.aspx) pro efektivní řešení tohoto problému.

Nakonec se podívejte na počet jedinečných hodnot pro některé další kategorické sloupce také. Obsah [vzorku&#95;úlu&#95;criteo&#95;jedinečné hodnoty&#95;&#95;více&#95;categoricals.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_unique_values_multiple_categoricals.hql) jsou:

        SELECT COUNT(DISTINCT(Col16)), COUNT(DISTINCT(Col17)),
        COUNT(DISTINCT(Col18), COUNT(DISTINCT(Col19), COUNT(DISTINCT(Col20))
        FROM criteo.criteo_train;

To dává:

        30935   15200   7349    20067   3
        Time taken: 1933.883 seconds, Fetched: 1 row(s)

Opět si všimněte, že s výjimkou Col20, všechny ostatní sloupce mají mnoho jedinečných hodnot.

### <a name="co-occurrence-counts-of-pairs-of-categorical-variables-in-the-train-dataset"></a>Počet případů co-výskytu párů kategorických proměnných v datové sadě vlaku

Počet rozdělení párů kategorických proměnných jsou také zajímavé. To lze určit pomocí kódu v [ukázkovém&#95;podregistru&#95;criteo&#95;spárované&#95;kategorické&#95;counts.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_paired_categorical_counts.hql):

        SELECT Col15, Col16, COUNT(*) AS paired_count FROM criteo.criteo_train GROUP BY Col15, Col16 ORDER BY paired_count DESC LIMIT 15;

Reverzní pořadí počty podle jejich výskytu a podívejte se na top 15 v tomto případě. To nám dává:

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

## <a name="down-sample-the-datasets-for-azure-machine-learning"></a><a name="downsample"></a>Dole ukázka datových sad pro Azure Machine Learning
Po prozkoumání datových sad a ukázal, jak provést tento typ průzkumu pro všechny proměnné (včetně kombinací), dolů ukázku datových sad tak, aby modely v Azure Machine Learning lze sestavit. Připomeňme si, že zaměření problému je: vzhledem k tomu, soubor příklad atributy (hodnoty prvků z Col2 - Col40), předpovědět, zda Col1 je 0 (bez kliknutí) nebo 1 (kliknutí).

Chcete-li snížit vzorek vlaku a testovací datové sady na 1 % původní velikosti, použijte nativní funkci Náčího zařízení() hive. Další skript, [ukázkový&#95;podregistr&#95;criteo&#95;downsample&#95;vlaku&#95;dataset.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_downsample_train_dataset.hql) to pro datovou sadu vlaku:

        CREATE TABLE criteo.criteo_train_downsample_1perc (
        col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 string)
        ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
        LINES TERMINATED BY '\n'
        STORED AS TEXTFILE;

        ---Now downsample and store in this table

        INSERT OVERWRITE TABLE criteo.criteo_train_downsample_1perc SELECT * FROM criteo.criteo_train WHERE RAND() <= 0.01;

To dává:

        Time taken: 12.22 seconds
        Time taken: 298.98 seconds

[Ukázka skriptu&#95;podregistr&#95;criteo&#95;downsample&#95;test&#95;den&#95;22&#95;dataset.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_downsample_test_day_22_dataset.hql) to dělá pro testovací data, den\_22:

        --- Now for test data (day_22)

        CREATE TABLE criteo.criteo_test_day_22_downsample_1perc (
        col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 string)
        ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
        LINES TERMINATED BY '\n'
        STORED AS TEXTFILE;

        INSERT OVERWRITE TABLE criteo.criteo_test_day_22_downsample_1perc SELECT * FROM criteo.criteo_test_day_22 WHERE RAND() <= 0.01;

To dává:

        Time taken: 1.22 seconds
        Time taken: 317.66 seconds


Nakonec ukázka skriptu [&#95;podregistr&#95;criteo&#95;downsample&#95;test&#95;den&#95;23&#95;dataset.hql](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/DataScienceScripts/sample_hive_criteo_downsample_test_day_23_dataset.hql) to dělá pro testovací data, den\_23:

        --- Finally test data day_23
        CREATE TABLE criteo.criteo_test_day_23_downsample_1perc (
        col1 string,col2 double,col3 double,col4 double,col5 double,col6 double,col7 double,col8 double,col9 double,col10 double,col11 double,col12 double,col13 double,col14 double,col15 string,col16 string,col17 string,col18 string,col19 string,col20 string,col21 string,col22 string,col23 string,col24 string,col25 string,col26 string,col27 string,col28 string,col29 string,col30 string,col31 string,col32 string,col33 string,col34 string,col35 string,col36 string,col37 string,col38 string,col39 string,col40 srical feature; tring)
        ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t'
        LINES TERMINATED BY '\n'
        STORED AS TEXTFILE;

        INSERT OVERWRITE TABLE criteo.criteo_test_day_23_downsample_1perc SELECT * FROM criteo.criteo_test_day_23 WHERE RAND() <= 0.01;

To dává:

        Time taken: 1.86 seconds
        Time taken: 300.02 seconds

Díky tomu jste připraveni použít naše testovací vlakové a testovací sady pro vytváření modelů v Azure Machine Learning.

Před přechodem na Azure Machine Learning, která se týká tabulky počítání, je konečná důležitá součást. V další podčásti je tabulka počítání popsána v některých podrobnostech.

## <a name="a-brief-discussion-on-the-count-table"></a><a name="count"></a>Stručná diskuse o tabulce počítání
Jak jste viděli, několik kategorických proměnných má vysokou dimenzionalitu. V návodu je prezentována výkonná technika nazvaná [Učení s počty](https://blogs.technet.com/b/machinelearning/archive/2015/02/17/big-learning-made-easy-with-counts.aspx) pro kódování těchto proměnných efektivním a robustním způsobem. Další informace o této technice naleznete v uvedeném odkazu.

>[!NOTE]
>V tomto návodu je fokus na použití tabulky počtu k vytvoření kompaktní reprezentace high-dimenzionální kategorické prvky. Toto není jediný způsob, jak zakódovat kategorické funkce; Pro více informací o dalších technikách mohou uživatelé, kteří mají zájem, vyhledat [kódování jedním horkým kódováním](https://en.wikipedia.org/wiki/One-hot) a [zapisovat funkce](https://en.wikipedia.org/wiki/Feature_hashing).
>

Chcete-li vytvořit tabulky počtu na datech počtu, použijte data ve složce raw/count. V části modelování se uživatelům zobrazí, jak vytvořit tyto tabulky počítání pro kategorické funkce od začátku nebo alternativně použít předem sestavenou tabulku počítání pro jejich zkoumání. V co následuje, když jsou označovány "předem sestavené tabulky počtu", máme na mysli použití tabulek počtu, které byly poskytnuty. Podrobné pokyny, jak získat přístup k těmto tabulkám, jsou uvedeny v další části.

## <a name="build-a-model-with-azure-machine-learning"></a><a name="aml"></a>Vytvoření modelu pomocí Azure Machine Learning
Náš proces vytváření modelů v Azure Machine Learning se řídí následujícími kroky:

1. [Získání dat z tabulek Hive do Azure Machine Learning](#step1)
2. [Vytvoření experimentu: vyčistěte data a udělejte z něj funkci s tabulkami počítání](#step2)
3. [Sestavení, trénování a skóre modelu](#step3)
4. [Vyhodnocení modelu](#step4)
5. [Publikování modelu jako webové služby](#step5)

Teď jste připraveni vytvářet modely ve studiu Azure Machine Learning. Naše vzorkovaná data dolů jsou uložena jako tabulky Hive v clusteru. K čtení těchto dat použijte modul Azure Machine Learning **Import Data.** Pověření pro přístup k účtu úložiště tohoto clusteru jsou k dispozici v následujícím.

### <a name="step-1-get-data-from-hive-tables-into-azure-machine-learning-using-the-import-data-module-and-select-it-for-a-machine-learning-experiment"></a><a name="step1"></a>Krok 1: Získejte data z tabulek Hive do Azure Machine Learning pomocí modulu Import dat a vyberte ho pro experiment strojového učení
Začněte výběrem **+NEW** -> **EXPERIMENT** -> Prázdný**experiment**. Potom z pole **Hledat** v levém horním rohu vyhledejte možnost Importovat data. Přetažením modulu **Import dat** na plátno experimentu (prostřední část obrazovky) použijte modul pro přístup k datům.

Takto vypadá **importdat** při získávání dat z tabulky Hive:

![Import dat získává data](./media/hive-criteo-walkthrough/i3zRaoj.png)

Pro modul **Import dat** jsou hodnoty parametrů, které jsou uvedeny v grafice, pouze příklady druhu hodnot, které je třeba zadat. Zde je několik obecných pokynů, jak vyplnit sadu parametrů pro modul **Import dat.**

1. Zvolte "Dotaz hive" pro **zdroj dat**
2. V poli **dotaz databáze Hive** stačí jednoduchý\_SELECT\_*\_FROM\_<název databáze.název tabulky> - stačí.
3. **Identifikátor URI serveru HCATALOG**: Pokud je váš cluster "abc", je to jednoduše:https://abc.azurehdinsight.net
4. **Název uživatelského účtu Hadoop**: Uživatelské jméno zvolené v době uvedení clusteru do provozu. (NE Uživatelské jméno vzdáleného přístupu!)
5. **Heslo uživatelského účtu Hadoop**: Heslo pro uživatelské jméno zvolené v době uvedení clusteru do provozu. (NE heslo pro vzdálený přístup!)
6. **Umístění výstupních dat**: Zvolte "Azure"
7. **Název účtu Azure Storage**: Účet úložiště přidružený ke clusteru
8. **Klíč účtu Azure Storage**: Klíč účtu úložiště přidruženého ke clusteru.
9. **Název kontejneru Azure**: Pokud je název clusteru "abc", pak je to jednoduše "abc", obvykle.

Jakmile **import dat** dokončí získávání dat (vidíte zelené klíště na modulu), uložte tato data jako datovou sadu (s názvem dle vašeho výběru). Jak to vypadá:

![Importovat data pro ukládání dat](./media/hive-criteo-walkthrough/oxM73Np.png)

Klikněte pravým tlačítkem myši na výstupní port modulu **Import dat.** Tím se zobrazí možnost **Uložit jako datová sada** a možnost **Vizuace.** Volba **Vizualizovat,** pokud klepnete, zobrazí 100 řádků dat spolu s pravým panelem, který je užitečný pro některé souhrnné statistiky. Chcete-li uložit data, jednoduše vyberte **uložit jako datovou sadu** a postupujte podle pokynů.

Chcete-li vybrat uloženou datovou sadu pro použití v experimentu strojového učení, vyhledejte datové sady pomocí **vyhledávacího** pole zobrazeného na následujícím obrázku. Pak jednoduše zadejte název, který jste datové sadě dali, abyste k ní částečně měli přístup, a přetáhněte datovou sadu na hlavní panel. Jeho přetažením na hlavní panel jej vyberete pro použití v modelování strojového učení.

![Přetáhnout datovou sadu na hlavní panel](./media/hive-criteo-walkthrough/cl5tpGw.png)

> [!NOTE]
> Proveďte to pro vlak a testovací datové sady. Nezapomeňte také použít název databáze a názvy tabulek, které jste zadali pro tento účel. Hodnoty použité na obrázku jsou určeny výhradně pro ilustrační účely.**
>
>

### <a name="step-2-create-an-experiment-in-azure-machine-learning-to-predict-clicks--no-clicks"></a><a name="step2"></a>Krok 2: Vytvoření experimentu v Azure Machine Learning předpovědět kliknutí / bez kliknutí
Náš experiment Azure Machine Learning Studio (klasický) vypadá takto:

![Experiment strojového učení](./media/hive-criteo-walkthrough/xRpVfrY.png)

Nyní prozkoumejte klíčové součásti tohoto experimentu. Přetáhněte naše uložené vlakové a testovací datové sady nejprve na naše plátno experimentu.

#### <a name="clean-missing-data"></a>Vyčištění chybějících dat
Modul **Vyčistit chybějící data** provede to, co naznačuje jeho název: vyčistí chybějící data způsoby, které lze zadat uživatelem. Podívejte se do tohoto modulu a podívejte se na toto:

![Vyčistit chybějící data](./media/hive-criteo-walkthrough/0ycXod6.png)

Zde zvolte nahradit všechny chybějící hodnoty 0. Existují i další možnosti, které lze vidět při pohledu na rozevírací nabídky v modulu.

#### <a name="feature-engineering-on-the-data"></a>Inženýrské funkce na datech
Mohou existovat miliony jedinečných hodnot pro některé kategorické funkce velkých datových sad. Použití naivních metod, jako je jednohorké kódování pro reprezentaci takových vysokorozměrných kategorických prvků, je zcela neproveditelné. Tento návod ukazuje, jak používat funkce počtu pomocí integrovaných modulů Azure Machine Learning ke generování kompaktníreprezentace těchto vysokorozměrných kategorických proměnných. Konečným výsledkem je menší velikost modelu, rychlejší tréninka a metriky výkonu, které jsou srovnatelné s použitím jiných technik.

##### <a name="building-counting-transforms"></a>Transformace počítání budov
Chcete-li vytvořit funkce počtu, použijte modul **Transformace počítání sestavení,** který je k dispozici v Azure Machine Learning. Modul vypadá takto:

![Vytvořit vlastnosti](./media/hive-criteo-walkthrough/e0eqKtZ.png)
![modulu transformace počítadla modul ustavičně](./media/hive-criteo-walkthrough/OdDN0vw.png)

> [!IMPORTANT]
> Do pole **Počet sloupců** zadejte ty sloupce, na které chcete provést, počítá. Obvykle se jedná o (jak již bylo zmíněno) vysokorozměrné kategorické sloupce. Nezapomeňte, že datová sada Criteo má 26 kategorických sloupců: od Col15 do Col40. Zde se spolehněte na všechny z nich a dát své indexy (od 15 do 40 oddělených čárkami, jak je znázorněno).
>

Chcete-li použít modul v režimu MapReduce (vhodné pro velké datové sady), potřebujete přístup ke clusteru HDInsight Hadoop (ten, který se používá pro zkoumání funkcí lze znovu použít i pro tento účel) a jeho pověření. Předchozí obrázky ilustrují, jak vypadají vyplněné hodnoty (nahraďte hodnoty uvedené pro ilustraci hodnotami, které jsou relevantní pro váš vlastní případ použití).

![Parametry modulu](./media/hive-criteo-walkthrough/05IqySf.png)

Předchozí obrázek ukazuje, jak zadat vstupní umístění objektu blob. Toto umístění má data vyhrazená pro sestavení počet tabulek.

Po dokončení spuštění tohoto modulu uložte transformaci na později klepnutím pravým tlačítkem myši na modul a výběrem možnosti **Uložit jako transformace:**

![Možnost "Uložit jako transformace"](./media/hive-criteo-walkthrough/IcVgvHR.png)

V naší architektuře experimentu je uvedeno výše, datová sada "ytransform2" odpovídá přesně uložené počet transformace. Pro zbytek tohoto experimentu se předpokládá, že čtenář používá modul **Transformace počítání sestavení** na některá data ke generování počítá a pak můžete použít tyto počty ke generování funkce počítání na vlak a testovací datové sady.

##### <a name="choosing-what-count-features-to-include-as-part-of-the-train-and-test-datasets"></a>Výběr funkcí počtu, které mají být zahrnuty jako součást datové sady vlaku a testování
Jakmile je transformace počtu připravena, uživatel si může vybrat, jaké funkce zahrnout do svých datových sad vlaku a testování pomocí modulu **Změnit parametry tabulky počtu.** Pro úplnost je tento modul zobrazen zde. Ale v zájmu jednoduchosti ji ve skutečnosti nepoužívejte v našem experimentu.

![Změnit parametry tabulky počtu](./media/hive-criteo-walkthrough/PfCHkVg.png)

V tomto případě, jak je vidět, log-odds mají být použity a back off sloupec je ignorována. Můžete také nastavit parametry, jako je prahová hodnota odpadkového koše, kolik pseudo-předchozí příklady přidat pro vyhlazení a zda použít jakýkoli Laplacian hluk nebo ne. Všechny tyto jsou pokročilé funkce a je třeba poznamenat, že výchozí hodnoty jsou dobrým výchozím bodem pro uživatele, kteří jsou pro tento typ generování funkcí noví.

##### <a name="data-transformation-before-generating-the-count-features"></a>Transformace dat před generováním funkcí počítání
Nyní je kladen důraz na důležitý bod o transformaci našich vlakových a testovacích dat před skutečně generováním funkcí počítání. Existují dva moduly **Spuštění skriptu R,** které se používají před použitím transformace počtu na naše data.

![Spuštění modulů skriptu R](./media/hive-criteo-walkthrough/aF59wbc.png)

Zde je první Skript R:

![První skript R](./media/hive-criteo-walkthrough/3hkIoMx.png)

Tento skript R přejmenuje naše sloupce na názvy "Col1" na "Col40". Důvodem je, že transformace count očekává názvy tohoto formátu.

Druhý skript R vyrovnává rozdělení mezi kladné a záporné třídy (třídy 1 a 0 v uvedeném pořadí) vzorkováním negativní třídy dolů. Skript R zde ukazuje, jak to udělat:

![Druhý skript R](./media/hive-criteo-walkthrough/91wvcwN.png)

V tomto jednoduchém skriptu\_R\_se "pos neg ratio" používá k nastavení množství rovnováhy mezi kladnými a zápornými třídami. To je důležité, protože zlepšení třídy nerovnováha má obvykle výhody výkonu pro klasifikaci problémy, kde je zkosený rozdělení třídy (připomeňme, že v tomto případě máte 3,3% pozitivní třídy a 96,7% negativní třídy).

##### <a name="applying-the-count-transformation-on-our-data"></a>Uplatnění transformace počítání na naše data
Nakonec můžete použít modul **Použít transformaci** použít počet transformací na naše datové sady vlaku a testování. Tento modul přebírá uloženou hodnotu transformace jako jeden vstup a vlak nebo testovací datové sady jako druhý vstup a vrací data s funkcemi počtu. Je to znázorněno zde:

![Použít transformační modul](./media/hive-criteo-walkthrough/xnQvsYf.png)

##### <a name="an-excerpt-of-what-the-count-features-look-like"></a>Výňatek z toho, co počet funkcí vypadat
Je poučné vidět, jak vypadají funkce počítání v našem případě. Zde je výňatek z tohoto:

![Počet funkcí](./media/hive-criteo-walkthrough/FO1nNfw.png)

Tento výňatek ukazuje, že pro sloupce počítal, dostanete počty a log kurzy kromě všech relevantních backoffs.

Teď jste připraveni k vytvoření modelu Azure Machine Learning pomocí těchto transformovaných datových sad. V další části ukazuje, jak to lze provést.

### <a name="step-3-build-train-and-score-the-model"></a><a name="step3"></a>Krok 3: Sestavení, trénování a skóre modelu

#### <a name="choice-of-learner"></a>Volba žáka
Nejprve musíte vybrat studenta. Použijte dvoutřídní posílený rozhodovací strom jako náš student. Zde jsou výchozí možnosti pro tento student:

![Parametry dvoutřídového posíleného rozhodovacího stromu](./media/hive-criteo-walkthrough/bH3ST2z.png)

Pro experiment zvolte výchozí hodnoty. Výchozí hodnoty jsou smysluplné a dobrý způsob, jak získat rychlé směrné plány na výkon. Výkon můžete zlepšit zametáním parametrů, pokud se rozhodnete, jakmile budete mít směrný plán.

#### <a name="train-the-model"></a>Trénování modelu
Pro školení jednoduše vyvolat modul **Train Model.** Dva vstupy k němu jsou two-class boosted rozhodovací strom student a naše vlakové datové sady. To je znázorněno zde:

![Modul modelu vlaku](./media/hive-criteo-walkthrough/2bZDZTy.png)

#### <a name="score-the-model"></a>Ohodnocení modelu
Jakmile máte trénovaný model, jste připraveni skóre na testovací datové sady a vyhodnotit jeho výkon. To pomocí modulu **Model skóre** zobrazeného na následujícím obrázku spolu s modulem **Vyhodnotit model:**

![Modul Určení skóre modelu](./media/hive-criteo-walkthrough/fydcv6u.png)

### <a name="step-4-evaluate-the-model"></a><a name="step4"></a>Krok 4: Vyhodnocení modelu
Nakonec byste měli analyzovat výkon modelu. Obvykle pro dvě třídy (binární) klasifikace problémy, dobrým měřítkem je AUC. Chcete-li vizualizovat tuto křivku, připojte modul **Modelu skóre** k modulu **Vyhodnotit model.** Kliknutím na **vizualizovat** v modulu **Vyhodnotit model** získáte grafiku, jako je následující:

![Vyhodnocení modelu Modulu BDT](./media/hive-criteo-walkthrough/0Tl0cdg.png)

V binární (nebo dvě třídy) klasifikace problémy, dobrým měřítkem přesnosti předpovědi je oblast pod křivkou (AUC). Následující část ukazuje naše výsledky pomocí tohoto modelu na naší testovací datové sady. Klepněte pravým tlačítkem myši na výstupní port modulu **Vyhodnotit model** a potom **na Vizualizovat**.

![Vizualizujte modul Vyhodnotit model](./media/hive-criteo-walkthrough/IRfc7fH.png)

### <a name="step-5-publish-the-model-as-a-web-service"></a><a name="step5"></a>Krok 5: Publikování modelu jako webové služby
Možnost publikovat model Azure Machine Learning jako webové služby s minimálním povykem je cennou funkcí pro jeho zpřístupnění široce dostupné. Jakmile je to hotovo, kdokoli může volat webové služby se vstupními daty, které potřebují předpovědi a webové služby používá model k vrácení těchto předpovědí.

Nejprve uložte náš trénovaný model jako objekt trénovaného modelu kliknutím pravým tlačítkem myši na modul **Model vlaku** a pomocí možnosti Uložit **jako trénovaný model.**

Dále vytvořte vstupní a výstupní porty pro naši webovou službu:

* vstupní port přebírá data ve stejné podobě jako data, která potřebujete předpovědi pro
* výstupní port vrátí scoredlabels a přidružené pravděpodobnosti.

#### <a name="select-a-few-rows-of-data-for-the-input-port"></a>Výběr několika řádků dat pro vstupní port
Je vhodné použít modul **Použít transformaci SQL** k výběru pouze 10 řádků, které budou sloužit jako data vstupního portu. Vyberte pouze tyto řádky dat pro náš vstupní port pomocí zde zobrazeného dotazu SQL:

![Data vstupního portu](./media/hive-criteo-walkthrough/XqVtSxu.png)

#### <a name="web-service"></a>Webová služba
Nyní jste připraveni spustit malý experiment, který lze použít k publikování naší webové služby.

#### <a name="generate-input-data-for-webservice"></a>Generovat vstupní data pro webovou službu
Jako nultý krok, protože tabulka počítání je velký, trvat několik řádků testovacích dat a generovat výstupní data z něj s funkcemi počtu. Tento výstup může sloužit jako formát vstupních dat pro naši webovou službu, jak je znázorněno zde:

![Vytvoření vstupních dat BDT](./media/hive-criteo-walkthrough/OEJMmst.png)

> [!NOTE]
> Pro formát vstupních dat použijte výstup modulu **Count Featurizer.** Po dokončení tohoto experimentu uložte výstup z modulu **Count Featurizer** jako datovou sadu. Tato datová sada se používá pro vstupní data ve webové službě.
>
>

#### <a name="scoring-experiment-for-publishing-webservice"></a>Vyhodnocování experiment pro publikování webové služby
Za prvé, základní struktura je **modul score model,** který přijímá náš trénovaný model objektu a několik řádků vstupních dat, které byly generovány v předchozích krocích pomocí count **Featurizer** modulu. Pomocí příkazu Vybrat sloupce v datové sadě můžete promítnout popisky s hodnocením a pravděpodobnosti skóre.

![Výběr sloupců v datové sadě](./media/hive-criteo-walkthrough/kRHrIbe.png)

Všimněte **si,** jak lze použít modul Vybrat sloupce v datové sadě pro filtrování dat z datové sady. Obsah je zobrazen zde:

![Filtrování pomocí modulu Vybrat sloupce v datové sadě](./media/hive-criteo-walkthrough/oVUJC9K.png)

Chcete-li získat modré vstupní a výstupní porty, stačí kliknout **na tlačítko připravit webovou službu** v pravém dolním rohu. Spuštění tohoto experimentu nám také umožňuje publikovat webovou službu: klikněte na ikonu **PUBLIKOVAT WEBOVOU SLUŽBU** v pravém dolním rohu, která je zobrazena zde:

![Publikovat webovou službu](./media/hive-criteo-walkthrough/WO0nens.png)

Po publikování webové služby přesměrovat na stránku, která vypadá takto:

![Řídicí panel webové služby](./media/hive-criteo-walkthrough/YKzxAA5.png)

Všimněte si dvou odkazů pro webové služby na levé straně:

* **Služba REQUEST/RESPONSE** Service (nebo RRS) je určena pro jednotlivé předpovědi a je to, co bylo využito v tomto workshopu.
* Služba **BES (BATCH EXECUTION** Service) se používá pro predikce dávek a vyžaduje, aby vstupní data použitá k předpovědi byly umístěny v azure blob storage.

Kliknutím na odkaz **REQUEST/RESPONSE** se přeneseme na stránku, která nám dává předkonzervovaný kód v C#, python a R. Tento kód lze pohodlně použít pro volání webové služby. Klíč rozhraní API na této stránce je třeba použít pro ověřování.

Je vhodné zkopírovat tento kód pythonu do nové buňky v poznámkovém bloku IPython.

Zde je segment kódu pythonu se správným klíčem ROZHRANÍ API.

![Kód Pythonu](./media/hive-criteo-walkthrough/f8N4L4g.png)

Výchozí klíč rozhraní API byl nahrazen klíčem rozhraní API naší webové služby. Kliknutím na **Spustit** v této buňce v poznámkovém bloku IPython získáte následující odpověď:

![Odpověď IPython](./media/hive-criteo-walkthrough/KSxmia2.png)

Pro dva testovací příklady dotazované v rámci Python skript JSON, dostanete zpět odpovědi ve formě "Scored Labels, Scored Probabilities". V tomto případě byly vybrány výchozí hodnoty, které poskytuje předkonzervovaný kód (0 pro všechny číselné sloupce a řetězec "hodnota" pro všechny kategorické sloupce).

Na závěr náš návod ukazuje, jak zpracovat rozsáhlé datové sady pomocí Azure Machine Learning. Začali jste s terabajtem dat, vytvořili model předpovědi a nasadili ho jako webovou službu v cloudu.

