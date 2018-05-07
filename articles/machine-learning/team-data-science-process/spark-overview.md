---
title: Přehled vědecké zpracování dat pomocí Spark v Azure HDInsight | Microsoft Docs
description: Sada nástrojů Spark MLlib přináší značné strojové učení modelování funkcí, které distribuovaném prostředí HDInsight.
services: machine-learning
documentationcenter: ''
author: deguhath
manager: cgronlun
editor: cgronlun
ms.assetid: a4e1de99-a554-4240-9647-2c6d669593c8
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/13/2017
ms.author: deguhath
ms.openlocfilehash: d7b4a87b72349375df71a5dd47c2bb544f3b4e3b
ms.sourcegitcommit: ca05dd10784c0651da12c4d58fb9ad40fdcd9b10
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/03/2018
---
# <a name="overview-of-data-science-using-spark-on-azure-hdinsight"></a>Přehled vědecké zpracování dat pomocí Spark v Azure HDInsight
[!INCLUDE [machine-learning-spark-modeling](../../../includes/machine-learning-spark-modeling.md)]

Tato sada témata ukazuje způsob použití HDInsight Spark pro dokončení běžné úkoly vědecké účely data jako přijímání dat, funkce analýzy, modelování a vyhodnocení modelu. Data použitá je ukázka 2013 NYC taxíkem služební cestě a tarif datové sady. Modely vytvářeny zahrnují logistic a lineární regrese, náhodné doménové struktury a přechodu boosted stromy. Témata také ukazují, jak k uložení těchto modelů v Azure blob storage (WASB) a jak stanovení skóre a vyhodnotit jejich prediktivní výkonu. Pokročilejší témata zahrnují, jak může být modely Trénink pomocí sweeping křížové ověření a technologie hyper parametr. Toto přehledové téma také odkazuje na témata, která popisují, jak nastavit, které potřebujete k dokončení kroků v návody zadaný cluster Spark. 

## <a name="spark-and-mllib"></a>Spark a MLlib
[Spark](http://spark.apache.org/) zpracovává představuje rozhraní open-source paralelní zpracování, které podporuje v paměti pro zvýšení výkonu velkých objemů dat analytických aplikací. Modul zpracování Spark je vytvořené pro rychlost, snadné použití a sofistikované analytics. Možnosti v paměti distribuované výpočtů Spark díky správnou volbu pro iterativní algoritmy použité v machine learning a grafů výpočty. [MLlib](http://spark.apache.org/mllib/) je modelování Spark škálovatelné machine learning knihovny, která přináší algoritmické funkce, které tento distribuovaném prostředí. 

## <a name="hdinsight-spark"></a>HDInsight Spark
[HDInsight Spark](../../hdinsight/spark/apache-spark-overview.md) je nabídku Azure hostované Spark open source. Zahrnuje taky podporu **poznámkové bloky Jupyter PySpark** na clusteru Spark, která se může spustit interaktivních dotazů Spark SQL pro transformaci, filtrování a vizualizace dat uložených v Azure BLOB (WASB). PySpark je rozhraní API jazyka Python pro Spark. Fragmenty kódu, které poskytují řešení a zobrazit relevantní pozemků k vizualizaci dat zde spustit v poznámkové bloky Jupyter nainstalovat na clusteru Spark. Modelování kroky v těchto tématech obsahovat kód, který ukazuje, jak cvičení, hodnocení, uložit a používat každý typ modelu. 

## <a name="setup-spark-clusters-and-jupyter-notebooks"></a>Instalační program: Clustery Spark a poznámkové bloky Jupyter
Kroky instalace a kódu jsou uvedené v tomto názorném postupu pro používání HDInsight Spark 1.6. Ale poznámkové bloky Jupyter jsou k dispozici pro clustery HDInsight Spark 1.6 a Spark 2.0. Popis poznámkových bloků a odkazy na ně jsou součástí [Readme.md](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Readme.md) úložiště Githubu, které je obsahují. Kromě toho kód sem a v propojených poznámkových bloků je obecný a by měla fungovat v jakémkoliv clusteru Spark. Pokud nepoužíváte HDInsight Spark, může být mírně lišit od co je tady uvedené kroky nastavení a Správa clusteru. Pro větší pohodlí si zde jsou uvedeny odkazy na poznámkové bloky Jupyter pro Spark 1.6 (musí být spuštěny v jádra pySpark Poznámkový blok Jupyter serveru) a 2.0 Spark (Chcete-li spustit v jádru pySpark3 Poznámkový blok Jupyter serveru):

### <a name="spark-16-notebooks"></a>Spark 1.6 poznámkových bloků
Tyto poznámkových bloků se mají spustit v jádra pySpark serveru poznámkového bloku Jupyter.

- [pySpark-machine-learning-data-science-spark-data-exploration-modeling.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark1.6/pySpark-machine-learning-data-science-spark-data-exploration-modeling.ipynb): poskytuje informace o tom, jak provést zkoumání dat, modelování a vyhodnocování se několik různých algoritmů.
- [pySpark-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark1.6/pySpark-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb): obsahuje témata v poznámkovém bloku #1 a modelu vývoj pomocí hyperparameter ladění a křížové ověření.
- [pySpark-machine-learning-data-science-spark-model-consumption.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark1.6/pySpark-machine-learning-data-science-spark-model-consumption.ipynb): ukazuje, jak zprovoznit model uložené v clusterech prostředí HDInsight pomocí Pythonu.

### <a name="spark-20-notebooks"></a>Spark 2.0 poznámkových bloků
Tyto poznámkových bloků se mají spustit v jádru pySpark3 serveru poznámkového bloku Jupyter.

- [Spark2.0-pySpark3-Machine-Learning-data-Science-Spark-Advanced-data-Exploration-Modeling.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark2.0/Spark2.0-pySpark3-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb): Tento soubor obsahuje informace o tom, jak provést zkoumání dat, modelování a vyhodnocování v rámci Spark 2.0 clusterů pomocí cesty NYC taxíkem a tarif popsané na sadu dat [zde](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-spark-overview#the-nyc-2013-taxi-data). Tento poznámkový blok, může být to dobrý výchozí bod pro zkoumání rychle kód, který jsme připravili pro Spark 2.0. Podrobnější Poznámkový blok analyzuje data taxíkem NYC, naleznete další poznámkového bloku v tomto seznamu. Najdete v poznámkách k následující tento seznam porovnávající tyto poznámkových bloků. 
- [Spark2.0 pySpark3_NYC_Taxi_Tip_Regression.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark2.0/Spark2.0_pySpark3_NYC_Taxi_Tip_Regression.ipynb): Tento soubor ukazuje, jak provést data wrangling (Spark SQL a dataframe operations), zkoumání, modelování a vyhodnocování pomocí cesty NYC taxíkem a tarif sady dat popsané [sem ](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-spark-overview#the-nyc-2013-taxi-data).
- [Spark2.0 pySpark3_Airline_Departure_Delay_Classification.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark2.0/Spark2.0_pySpark3_Airline_Departure_Delay_Classification.ipynb): Tento soubor ukazuje, jak provést data wrangling (Spark SQL a dataframe operations), zkoumání, modelování a vyhodnocování pomocí známých na čas odeslání letecká společnost datové sady z 2011 a 2012. Jsme integrované letecká společnost datovou sadu s daty počasí letiště (např. větru, teploty, výška atd.) před modelování, takže tyto funkce počasí můžou být součástí modelu.

<!-- -->

> [!NOTE]
> Datová sada letecká společnost byl přidán do poznámkových bloků Spark 2.0 abychom vám lépe předvedli použití algoritmů klasifikace. V následujících tématech informace o letecká společnost v době odeslání datovou sadu a počasí datové sady:

>- Data na čas odeslání letecká společnost: [http://www.transtats.bts.gov/ONTIME/](http://www.transtats.bts.gov/ONTIME/)

>- Letiště počasí data: [https://www.ncdc.noaa.gov/](https://www.ncdc.noaa.gov/) 
> 
> 

<!-- -->

<!-- -->

> [!NOTE]
Poznámkové bloky Spark 2.0 na NYC taxíkem a letecká společnost letu zpoždění-sady dat může trvat 10 minut nebo déle ke spuštění (v závislosti na velikosti vašeho clusteru HDI). První poznámkového bloku v seznamu nahoře zobrazí mnoho aspektů zkoumání dat, vizualizace a ML školení v poznámkovém bloku, který zabere to méně času se spouští s nižší vzorkovat NYC datové sady, ve kterém byly soubory taxíkem a tarif předem připojený k modelu: [Spark2.0-pySpark3-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark2.0/Spark2.0-pySpark3-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb) tento poznámkový blok trvá mnohem kratší dobu dokončit (v minutách 2-3) a může být dobrou výchozí bod pro rychle prohlížení kódu uvádíme Spark 2.0. 

<!-- -->

Pokyny v operationalization Spark 2.0 model a model spotřeby pro vyhodnocování najdete v tématu [Spark 1.6 dokumentu o spotřebě](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark1.6/pySpark-machine-learning-data-science-spark-model-consumption.ipynb) příklad osnovy kroky. Pokud chcete použít tento na Spark 2.0, nahraďte soubor kód Python s [tento soubor](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/Python/Spark2.0_ConsumeRFCV_NYCReg.py).

### <a name="prerequisites"></a>Požadavky
Následující postupy se vztahují k Spark 1.6. Pro verzi Spark 2.0 použijte poznámkových bloků popsané a propojené s dříve. 

1 musíte mít předplatné Azure. Pokud není již nemáte, přečtěte si téma [získání bezplatné zkušební verze Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

2. budete potřebovat cluster Spark 1.6 k dokončení tohoto názorného postupu. Pokud chcete vytvořit, postupujte podle pokynů uvedených v [Začínáme: Vytvořte Apache Spark v Azure HDInsight](../../hdinsight/spark/apache-spark-jupyter-spark-sql.md). Typ clusteru a verze je určené z **vybrat typ clusteru** nabídky. 

![Konfigurace clusteru](./media/spark-overview/spark-cluster-on-portal.png)

<!-- -->

> [!NOTE]
> Téma, které ukazuje, jak k dokončení úloh pro proces vědecké účely začátku do konce dat použít Scala spíše než Python, najdete v článku [vědecké zpracování dat pomocí Spark v Azure Scala](scala-walkthrough.md).
> 
> 

<!-- -->

> [!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]
> 
> 

## <a name="the-nyc-2013-taxi-data"></a>Data taxíkem NYC 2013
Data NYC taxíkem cesty je přibližně 20 GB komprimované hodnot oddělených čárkami (CSV) souborů (nekomprimovaným ~ 48 GB), skládající se z více než 173 milionů jednotlivých cest a tarify placené pro každou cestu. Každý záznam cestě zahrnuje vybrat nahoru a odkládací umístění a čas, číslo licence anonymizovaná hackerský (ovladač) a číslo Medailon (taxi na jedinečné id). Data obsahuje všechny služebních cest v roku 2013 a je dostupné pro každý měsíc následující dvě datové sady:

1. CSV soubory 'trip_data' obsahují podrobnosti o cestě, například na počtu cestujících, vyzvednutí a body dropoff dojít doba trvání a délka cesty. Tady je několik ukázkových záznamů:
   
        medallion,hack_license,vendor_id,rate_code,store_and_fwd_flag,pickup_datetime,dropoff_datetime,passenger_count,trip_time_in_secs,trip_distance,pickup_longitude,pickup_latitude,dropoff_longitude,dropoff_latitude
        89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,1,N,2013-01-01 15:11:48,2013-01-01 15:18:10,4,382,1.00,-73.978165,40.757977,-73.989838,40.751171
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-06 00:18:35,2013-01-06 00:22:54,1,259,1.50,-74.006683,40.731781,-73.994499,40.75066
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-05 18:49:41,2013-01-05 18:54:23,1,282,1.10,-74.004707,40.73777,-74.009834,40.726002
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:54:15,2013-01-07 23:58:20,2,244,.70,-73.974602,40.759945,-73.984734,40.759388
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:25:03,2013-01-07 23:34:24,1,560,2.10,-73.97625,40.748528,-74.002586,40.747868
2. CSV soubory 'trip_fare' obsahují podrobnosti o tarif placené pro každou cestu, například typ platby, velikost tarif, příplatek a daně, tipy a mýtné a celkovou velikost placené. Tady je několik ukázkových záznamů:
   
        medallion, hack_license, vendor_id, pickup_datetime, payment_type, fare_amount, surcharge, mta_tax, tip_amount, tolls_amount, total_amount
        89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,2013-01-01 15:11:48,CSH,6.5,0,0.5,0,0,7
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-06 00:18:35,CSH,6,0.5,0.5,0,0,7
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-05 18:49:41,CSH,5.5,1,0.5,0,0,7
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:54:15,CSH,5,0.5,0.5,0,0,6
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:25:03,CSH,9.5,0.5,0.5,0,0,10.5

Jsme prováděné ukázku 0,1 % těchto souborů a připojené k cesta\_dat a cesty\_jízdenky CVS soubory do jedné datové sady sloužící jako vstupní datové sady pro účely tohoto postupu. Jedinečný klíč pro připojení k cestě\_dat a cesty\_tarif se skládá z pole: medailonu, hackerský\_licence a vyzvednutí\_data a času. Každý záznam datová sada obsahuje následující atributy představující NYC taxíkem cesty:

| Pole | Stručný popis |
| --- | --- |
| Medailon |Anonymizovaná taxíkem Medailon (taxi jedinečné id) |
| hack_license |Anonymizovaná číslo licence Hackney znaků CR |
| vendor_id |Id dodavatele taxíkem |
| rate_code |Míra taxíkem NYC tarif |
| store_and_fwd_flag |Úložiště a předat dál příznak |
| pickup_datetime |Vyzvednutí datum a čas |
| dropoff_datetime |Dropoff datum a čas |
| pickup_hour |Vyzvedne, až hodinu |
| pickup_week |Vyzvednutí týden v roce |
| den v týdnu |Den v týdnu (rozsah 1-7) |
| passenger_count |Počet cestujících v cestě taxíkem |
| trip_time_in_secs |Času v sekundách |
| trip_distance |Vzdálenost služební cestě, kterou urazit v miles |
| pickup_longitude |Vyzvednutí zeměpisná délka |
| pickup_latitude |Vyzvednutí zeměpisná šířka |
| dropoff_longitude |Zeměpisná délka Dropoff |
| dropoff_latitude |Zeměpisná šířka Dropoff |
| direct_distance |Přímá vzdálenost mezi vybrat nahoru a dropoff umístění |
| payment_type |Typ platby (certifikační autority, platební karty atd.) |
| fare_amount |Tarif částka v |
| Příplatek. |Příplatek. |
| mta_tax |Daň prostředí MTA |
| tip_amount |Velikost tipu |
| tolls_amount |Velikost mýtné |
| total_amount |Celková velikost |
| vysypávány |Šikmý (0 nebo 1 – Ne nebo Ano) |
| tip_class |Tip – třída (0: 0, 1: $0-5, 2: $6-10, 3: $11-20, 4: > 20) |

## <a name="execute-code-from-a-jupyter-notebook-on-the-spark-cluster"></a>Spustí kód z poznámkového bloku Jupyter v clusteru Spark
Můžete spustit poznámkového bloku Jupyter z portálu Azure. Najít váš cluster Spark na řídicím panelu a klikněte na něj na stránce Správa zadejte pro váš cluster. Otevřete Poznámkový blok přidruženého k clusteru Spark klikněte na **řídicí panely clusteru** -> **Poznámkový blok Jupyter** .

![Řídicí panely clusteru](./media/spark-overview/spark-jupyter-on-portal.png)

Můžete také vyhledat ***https://CLUSTERNAME.azurehdinsight.net/jupyter*** pro přístup k Jupyter Notebooks. Nahraďte název vlastní cluster CLUSTERNAME součástí tuto adresu URL. Potřebujete heslo pro váš účet správce pro přístup k poznámkových bloků.

![Procházet poznámkové bloky Jupyter](./media/spark-overview/spark-jupyter-notebook.png)

Vyberte PySpark zobrazíte adresář, který obsahuje několik příkladů předem zabalené poznámkových bloků, které používají rozhraní API PySpark. Jsou k dispozici v poznámkových bloků, které obsahují ukázky kódu pro tuto sadu Spark tématu [Githubu](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/Spark/pySpark)

Můžete nahrát poznámkových bloků přímo z [Githubu](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/Spark/pySpark) server Poznámkový blok Jupyter v clusteru Spark. Na domovské stránce vaší Jupyter, klikněte na **nahrát** tlačítko na pravou část obrazovky. Otevře se Průzkumník souborů. Zde můžete vložte adresu URL GitHub (nezpracovaná obsah) poznámkového bloku a klikněte na tlačítko **otevřete**. 

Zobrazí název souboru v seznamu souborů Jupyter s **nahrát** tlačítko znovu. Klikněte na tlačítko to **nahrát** tlačítko. Nyní jste importovali poznámkového bloku. Opakováním těchto kroků nahrajte jiných poznámkových bloků z tohoto návodu.

> [!TIP]
> Můžete kliknout pravým tlačítkem odkazů na prohlížeč a vyberte **Kopírovat odkaz** k získání adresy URL githubu nezpracovaná obsahu. Tuto adresu URL můžete vložit do Jupyter nahrát soubor explorer dialogových oken.
> 
> 

Nyní můžete:

* Zobrazit kód klikněte poznámkového bloku.
* Spusťte jednotlivých buněk tak, že stisknete **zadejte SHIFT**.
* Kliknutím na spustit celý poznámkový blok **buňky** -> **spustit**.
* Použijte automatické vizualizace dotazů.

> [!TIP]
> Jádra PySpark automaticky vizualizuje výstup dotazy SQL (HiveQL). Nyní máte možnost vybrat z několika různých typů vizualizace (tabulky, kruhový, řádku, oblasti nebo panelu) pomocí **typ** tlačítka nabídky v poznámkovém bloku:
> 
> 

![Křivka ROC logistic regression pro obecný přístup](./media/spark-overview/pyspark-jupyter-autovisualization.png)

## <a name="whats-next"></a>Co dále?
Teď, když jsou vytvořeny pomocí clusteru služby HDInsight Spark a odeslali Jupyter notebooks, jste připraveni na témata, které odpovídají tři poznámkových bloků PySpark spolupracovat. Ukazují jak prozkoumat vaše data a jak vytvářet a využívat modelů. Poznámkový blok pro zkoumání a modelování pokročilé data ukazuje, jak křížového ověřování, technologie hyper parametr komínů, zahrnout a vyhodnocení modelu. 

**Zkoumání dat a modelování pomocí Spark:** prozkoumat datovou sadu a vytvářet, stanovení skóre a vyhodnotit strojového učení modely projdete [vytvořit binární klasifikace a regrese modely pro data s Spark MLlib Sada nástrojů](spark-data-exploration-modeling.md) tématu.

**Model spotřeby:** další postupy skóre pro klasifikaci a regrese modely, které jsou vytvořené v tomto tématu najdete v tématu [skóre a vyhodnocení modelů learning vytvořené Spark počítač](spark-model-consumption.md).

**Křížové ověření a hyperparameter (vymetání) komínů**: najdete v části [Advanced zkoumání dat a modelování pomocí Spark](spark-advanced-data-exploration-modeling.md) na tom, jak může být modely Trénink pomocí (vymetání) křížové ověření a technologie hyper parametr komínů

