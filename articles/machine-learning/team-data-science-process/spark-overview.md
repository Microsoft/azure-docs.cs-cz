---
title: Datová věda využívající Spark v Azure HDInsight – proces týmové datové vědy
description: Sada nástrojů Spark MLlib přináší do distribuovaného prostředí HDInsight značné možnosti modelování strojového učení.
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
ms.openlocfilehash: 63148b99e65a5ccc49d54d4ae6c58adebc72c6d3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76718510"
---
# <a name="overview-of-data-science-using-spark-on-azure-hdinsight"></a>Přehled datové vědy pomocí Sparkna na Azure HDInsight

Tato sada témat ukazuje, jak pomocí HDInsight Spark dokončit běžné úlohy datové vědy, jako je například ingestování dat, funkce inženýrství, modelování a vyhodnocení modelu. Použitá data jsou ukázkou datové sady taxi 2013 NYC a tarifu. Vytvořené modely zahrnují logistickou a lineární regresi, náhodné doménové struktury a stromy posílené přechodem. Témata také ukazují, jak ukládat tyto modely v úložišti objektů blob Azure (WASB) a jak skóre a vyhodnotit jejich prediktivní výkon. Pokročilejší témata pokrývají, jak lze modely trénovat pomocí křížového ověřování a hyperparametrické zametání. Toto téma přehledu také odkazuje na témata, která popisují, jak nastavit cluster Spark, který potřebujete k dokončení kroků v návodech k dispozici.

## <a name="spark-and-mllib"></a>Jiskra a MLlib
[Spark](https://spark.apache.org/) je open source paralelní zpracování framework, který podporuje zpracování v paměti pro zvýšení výkonu aplikací pro analýzu velkých objemů dat. Modul pro zpracování Spark je navržen pro rychlost, snadné použití a sofistikované analýzy. Možnosti distribuovaného výpočtu sparku v paměti z něj činí dobrou volbu pro iterativní algoritmy používané ve strojovém učení a výpočtech grafů. [MLlib](https://spark.apache.org/mllib/) je škálovatelná knihovna strojového učení společnosti Spark, která přináší možnosti algoritmického modelování do tohoto distribuovaného prostředí.

## <a name="hdinsight-spark"></a>HDInsight Spark
[HDInsight Spark](../../hdinsight/spark/apache-spark-overview.md) je hostitelská nabídka Open source Spark hostované v Azure. Obsahuje také podporu **pro poznámkové bloky Jupyter PySpark** v clusteru Spark, které můžou spouštět interaktivní dotazy Spark SQL pro transformaci, filtrování a vizualizaci dat uložených v objektech BLOB Azure (WASB). PySpark je Python API pro Spark. Fragmenty kódu, které poskytují řešení a zobrazují příslušné obrázky pro vizualizaci dat zde spuštěných v poznámkových blocích Jupyter nainstalované v clusterech Spark. Kroky modelování v těchto tématech obsahují kód, který ukazuje, jak trénovat, vyhodnocovat, ukládat a využívat každý typ modelu.

## <a name="setup-spark-clusters-and-jupyter-notebooks"></a>Nastavení: Clustery Spark a poznámkové bloky Jupyter
Kroky nastavení a kód jsou k dispozici v tomto návodu pro použití HDInsight Spark 1.6. Ale notebooky Jupyter jsou k dispozici pro clustery HDInsight Spark 1.6 a Spark 2.0. Popis poznámkových bloků a odkazy na ně jsou k dispozici v [Readme.md](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Readme.md) pro úložiště GitHub, které je obsahuje. Kromě toho je kód zde a v propojených poznámkových blocích obecný a měl by fungovat na libovolném clusteru Spark. Pokud nepoužíváte HDInsight Spark, kroky nastavení a správy clusteru se mohou mírně lišit od toho, co je zobrazeno zde. Pro větší pohodlí, zde jsou odkazy na notebooky Jupyter pro Spark 1.6 (které mají být spuštěny v jádru pySpark serveru Jupyter Notebook) a Spark 2.0 (ke spuštění v jádru pySpark3 jupyternotebooku):

### <a name="spark-16-notebooks"></a>Spark 1,6 notebooky
Tyto notebooky mají být spuštěny v jádru pySpark notebookového serveru Jupyter.

- [pySpark-machine-learning-data-science-spark-data-exploration-modeling.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark1.6/pySpark-machine-learning-data-science-spark-data-exploration-modeling.ipynb): Poskytuje informace o tom, jak provádět zkoumání dat, modelování a vyhodnocování pomocí několika různých algoritmů.
- [pySpark-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark1.6/pySpark-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb): Zahrnuje témata v #1 notebooků a vývoj modelů pomocí hyperparametrizování a křížového ověřování.
- [pySpark-machine-learning-data-science-spark-model-consumption.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark1.6/pySpark-machine-learning-data-science-spark-model-consumption.ipynb): Ukazuje, jak zprovoznit uložený model pomocí Pythonu v clusterech HDInsight.

### <a name="spark-20-notebooks"></a>Spark 2.0 notebooky
Tyto notebooky mají být spuštěny v jádru pySpark3 notebookového serveru Jupyter.

- [Spark2.0-pySpark3-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark2.0/Spark2.0-pySpark3-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb): Tento soubor poskytuje informace o tom, jak provádět zkoumání dat, modelování a bodování v clusterech Spark 2.0 pomocí nyc taxi výlet a tarif data-set je popsáno [zde](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-spark-overview#the-nyc-2013-taxi-data). Tento poznámkový blok může být dobrým výchozím bodem pro rychlé prozkoumání kódu, který jsme poskytli pro Spark 2.0. Podrobnější poznámkový blok analyzuje data nyc taxi, najdete v dalším poznámkový blok v tomto seznamu. Podívejte se na poznámky následující v tomto seznamu, který porovnává tyto poznámkové bloky.
- [Spark2.0-pySpark3_NYC_Taxi_Tip_Regression.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark2.0/Spark2.0_pySpark3_NYC_Taxi_Tip_Regression.ipynb): Tento soubor ukazuje, jak provádět data hádky (Spark SQL a dataframe operace), průzkum, modelování a bodování pomocí NYC Taxi výlet a jízdné-soubor údajů je popsáno [zde](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-spark-overview#the-nyc-2013-taxi-data).
- [Spark2.0-pySpark3_Airline_Departure_Delay_Classification.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark2.0/Spark2.0_pySpark3_Airline_Departure_Delay_Classification.ipynb): Tento soubor ukazuje, jak provádět datové hádky (Spark SQL a dataframe operace), průzkum, modelování a bodování pomocí známé letecké společnosti On-time odletu datové sady z 2011 a 2012. Před modelováním jsme integrovali datovou sadu leteckých společností s údaji o počasí na letišti (například rychlost větru, teploty, nadmořskou výškou atd.), takže tyto funkce počasí mohou být zahrnuty do modelu.

<!-- -->

> [!NOTE]
> Datová sada leteckých společností byla přidána do notebooků Spark 2.0, aby lépe ilustrovala použití klasifikačních algoritmů. Informace o datové sadě v yoletu letecké společnosti a datové matné datové sadě počasí naleznete na následujících odkazech:
> 
> - Údaje o letecké společnosti o odletu:[https://www.transtats.bts.gov/ONTIME/](https://www.transtats.bts.gov/ONTIME/)
> 
> - Údaje o počasí na letišti:[https://www.ncdc.noaa.gov/](https://www.ncdc.noaa.gov/)

<!-- -->

<!-- -->

> [!NOTE]
> Spuštění notebooků Spark 2.0 na datových sadách zpoždění taxi a leteckých letů může trvat 10 minut nebo déle (v závislosti na velikosti clusteru HDI). První poznámkový blok ve výše uvedeném seznamu ukazuje mnoho aspektů zkoumání dat, vizualizace a ml model školení v notebooku, který trvá méně času běžet s down-sampled NYC datové sady, ve kterém taxi a jízdné soubory byly pre-joined: [Spark2.0-pySpark3-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark2.0/Spark2.0-pySpark3-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb). Tento notebook trvá mnohem kratší dobu dokončení (2-3 min) a může být dobrým výchozím bodem pro rychlé zkoumání kódu, který jsme poskytli pro Spark 2.0.

<!-- -->

Pokyny k zprovoznění modelu Spark 2.0 a spotřeby modelu pro vyhodnocování, najdete v [tématu Spark 1.6 dokument o spotřebě](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark1.6/pySpark-machine-learning-data-science-spark-model-consumption.ipynb) pro příklad popisující požadované kroky. Chcete-li použít tento příklad na Spark 2.0, nahraďte soubor kódu Pythonu [tímto souborem](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/Python/Spark2.0_ConsumeRFCV_NYCReg.py).

### <a name="prerequisites"></a>Požadavky

Následující postupy se vztahují k Spark 1.6. Pro verzi Spark 2.0 použijte poznámkové bloky popsané a propojené s dříve.

1. Mít předplatné Azure. Pokud ještě nemáte, najdete v tématu [získat azure bezplatnou zkušební verzi](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

2. K dokončení tohoto návodu potřebujete cluster Spark 1.6. Pokud chcete ho vytvořit, přečtěte si pokyny k dispozici v [tématu Začínáme: vytvoření Apache Spark na Azure HDInsight](../../hdinsight/spark/apache-spark-jupyter-spark-sql.md). Typ a verze clusteru je určen z nabídky **Vybrat typ clusteru.**

![Konfigurace clusteru](./media/spark-overview/spark-cluster-on-portal.png)

<!-- -->

> [!NOTE]
> Téma, které ukazuje, jak používat Scala místo Pythonu k dokončení úloh pro proces datové vědy od konce na konec, najdete v [tématu Datové vědy pomocí Scala se Sparkem v Azure](scala-walkthrough.md).
>
>

<!-- -->

> [!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]
>
>

## <a name="the-nyc-2013-taxi-data"></a>Nyc 2013 Taxi data
NYC Taxi Trip data je asi 20 GB komprimovaných čárek-oddělené hodnoty (CSV) soubory (~ 48 GB nekomprimované), zahrnující více než 173 milionů jednotlivých cest a tarify zaplacené za každou cestu. Každý záznam cesty obsahuje místo vyzvednutí a předání a čas, anonymizované číslo licence hack (řidiče) a medailon (jedinečné číslo taxi( jedinečné číslo taxíku). Údaje se týkají všech cest v roce 2013 a jsou uvedeny v následujících dvou datových souborech za každý měsíc:

1. Soubory CSV "trip_data" obsahují podrobnosti o cestě, jako je počet cestujících, místa vyzvednutí a předání, doba trvání cesty a délka cesty. Zde je několik ukázkových záznamů:

        medallion,hack_license,vendor_id,rate_code,store_and_fwd_flag,pickup_datetime,dropoff_datetime,passenger_count,trip_time_in_secs,trip_distance,pickup_longitude,pickup_latitude,dropoff_longitude,dropoff_latitude
        89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,1,N,2013-01-01 15:11:48,2013-01-01 15:18:10,4,382,1.00,-73.978165,40.757977,-73.989838,40.751171
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-06 00:18:35,2013-01-06 00:22:54,1,259,1.50,-74.006683,40.731781,-73.994499,40.75066
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-05 18:49:41,2013-01-05 18:54:23,1,282,1.10,-74.004707,40.73777,-74.009834,40.726002
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:54:15,2013-01-07 23:58:20,2,244,.70,-73.974602,40.759945,-73.984734,40.759388
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:25:03,2013-01-07 23:34:24,1,560,2.10,-73.97625,40.748528,-74.002586,40.747868
2. Soubory CSV "trip_fare" obsahují podrobnosti o jízdném zaplacené za každou cestu, jako je typ platby, výše jízdného, příplatek a daně, tipy a mýtné a celková zaplacená částka. Zde je několik ukázkových záznamů:

        medallion, hack_license, vendor_id, pickup_datetime, payment_type, fare_amount, surcharge, mta_tax, tip_amount, tolls_amount, total_amount
        89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,2013-01-01 15:11:48,CSH,6.5,0,0.5,0,0,7
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-06 00:18:35,CSH,6,0.5,0.5,0,0,7
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-05 18:49:41,CSH,5.5,1,0.5,0,0,7
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:54:15,CSH,5,0.5,0.5,0,0,6
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:25:03,CSH,9.5,0.5,0.5,0,0,10.5

Vzali jsme 0,1% vzorek těchto souborů\_a připojil\_data o cestě a výlet jízdné CVS soubory do jedné datové sady použít jako vstupní datovou sadu pro tento návod. Jedinečný klíč pro\_připojení údajů\_o cestě a jízdného se\_skládá\_z polí: medailon, hack licence a vyzvednutí datetime. Každý záznam datové sady obsahuje následující atributy představující cestu nyc taxi:

| Pole | Stručný popis |
| --- | --- |
| Medailon |Anonymizovaný taxi medailon (unikátní taxi id) |
| hack_license |Anonymizované číslo řidičského průkazu Hackney |
| vendor_id |Id dodavatele taxislužby |
| rate_code |NYC taxi sazba jízdného |
| store_and_fwd_flag |Uložit a předat vlajku |
| pickup_datetime |Vyzvednout datum & čas |
| dropoff_datetime |Datum předání & čas |
| pickup_hour |Hodina vyzvednutí |
| pickup_week |Týden vyzvednutí v roce |
| Všední den |Den v týdnu (rozmezí 1-7) |
| passenger_count |Počet cestujících v taxi jízdě |
| trip_time_in_secs |Doba jízdy v sekundách |
| trip_distance |Vzdálenost jízdy ujetá v mílích |
| pickup_longitude |Vyzvednout zeměpisnou dobu |
| pickup_latitude |Vyzvednout zeměpisnou šířku |
| dropoff_longitude |Dropoff zeměpisná diatura |
| dropoff_latitude |Zeměpisná šířka |
| direct_distance |Přímá vzdálenost mezi místy vyzvednutí a vysazování |
| payment_type |Typ platby (hotovost, kreditní karta atd.) |
| fare_amount |Částka jízdného v |
| Příplatek |Příplatek |
| mta_tax |MTA Metro Dopravní daň |
| tip_amount |Částka tipu |
| tolls_amount |Výše mýtného |
| total_amount |Total amount |
| Hrotem |Šikmé (0/1 pro ne nebo ano) |
| tip_class |Tip třída (0: $0, 1: $0-5, 2: $6-10, 3: $11-20, 4: > $20) |

## <a name="execute-code-from-a-jupyter-notebook-on-the-spark-cluster"></a>Spuštění kódu z poznámkového bloku Jupyter v clusteru Spark
Jupyterový poznámkový blok můžete spustit z portálu Azure. Najděte svůj cluster Spark na řídicím panelu a kliknutím na něj zadejte stránku správy pro svůj cluster. Chcete-li otevřít poznámkový blok přidružený k clusteru Spark, klepněte na položku -> **Jupyter poznámkový blok** **řídicích panelů clusteru**.

![Řídicí panely clusteru](./media/spark-overview/spark-jupyter-on-portal.png)

Můžete také vyhledat ***https://CLUSTERNAME.azurehdinsight.net/jupyter*** přístup k poznámkovým blokům Jupyter. Nahraďte část CLUSTERNAME této adresy URL názvem vlastního clusteru. Pro přístup k poznámkovým blokům potřebujete heslo pro účet správce.

![Procházet poznámkové bloky Jupyter](./media/spark-overview/spark-jupyter-notebook.png)

Výběrem možnosti PySpark zobrazíte adresář, který obsahuje několik příkladů předbalených poznámkových bloků, které používají rozhraní API PySpark. Poznámkové bloky, které obsahují ukázky kódu pro tuto sadu tématu Spark jsou k dispozici na [GitHubu](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/Spark/pySpark)

Poznámkové bloky můžete nahrát přímo z [GitHubu](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/Spark/pySpark) na notebook Jupyter v clusteru Spark. Na domovské stránce vašeho Jupyteru klikněte na tlačítko **Nahrát** v pravé části obrazovky. Otevře průzkumník souborů. Zde můžete vložit adresu URL GitHubu (nezpracovaný obsah) poznámkového bloku a kliknout na **otevřít**.

Název souboru se znovu zobrazí v seznamu souborů Jupyter s tlačítkem **Nahrát.** Klikněte na toto tlačítko **Nahrát.** Nyní jste importovali poznámkový blok. Opakováním těchto kroků nahrajte ostatní poznámkové bloky z tohoto návodu.

> [!TIP]
> Kliknutím pravým tlačítkem myši na odkazy v prohlížeči a výběrem **možnosti Kopírovat odkaz** získáte adresu URL nezpracovaného obsahu GitHubu. Tuto adresu URL můžete vložit do dialogového okna Jupyter Upload file explorer.
> 
> 

Nyní můžete:

* Kliknutím na poznámkový blok zobrazíte kód.
* Proveďte každou buňku stisknutím **klávesshift-ENTER**.
* Spusťte celý poznámkový blok kliknutím na **Spustit buňku** -> **.**
* Použijte automatickou vizualizaci dotazů.

> [!TIP]
> Jádro PySpark automaticky vizualizuje výstup dotazů SQL (HiveQL). Možnost vybrat z několika různých typů vizualizací (tabulka, výsečový graf, čára, plocha nebo pruh) pomocí tlačítek nabídky **Typ** v poznámkovém bloku:
>
>

![Logická regresní ROC křivka pro obecný přístup](./media/spark-overview/pyspark-jupyter-autovisualization.png)

## <a name="whats-next"></a>Co dále?
Teď, když jste nastaveni s clusterem HDInsight Spark a nahráli poznámkové bloky Jupyter, jste připraveni pracovat s tématy, která odpovídají třem poznámkovým blokům PySpark. Ukazují, jak prozkoumat data a pak, jak vytvářet a využívat modely. Poznámkový blok pro zkoumání a modelování pokročilých dat ukazuje, jak zahrnout křížové ověřování, hyperparametrické zametání a vyhodnocení modelu.

**Průzkum dat a modelování se Sparkem:** Prozkoumejte datovou sadu a vytvořte, vyhodnoťte a vyhodnoťte modely strojového učení pomocí [vytváření binárních klasifikačních a regresních modelů pro data pomocí tématu sady nástrojů Spark MLlib.](spark-data-exploration-modeling.md)

**Spotřeba modelu:** Informace o tom, jak skóre klasifikace a regresní modely vytvořené v tomto tématu, najdete v [tématu skóre a vyhodnotit Spark-postavený modely strojového učení](spark-model-consumption.md).

**Křížové ověřování a hyperparametrické zametání**: Podívejte se na [pokročilé zkoumání dat a modelování se Sparkem](spark-advanced-data-exploration-modeling.md) o tom, jak lze modely trénovat pomocí křížového ověřování a hyperparametrické zametání

