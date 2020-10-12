---
title: Vědecké zpracování dat pomocí Sparku v Azure HDInsight – vědecké zpracování týmových dat
description: Sada nástrojů Spark MLlib přináší do distribuovaného prostředí HDInsight značnou možnost modelování strojového učení.
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
ms.openlocfilehash: 3aa33efa9aa416ad1dfefd2fe957ce04b2b14432
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "86027456"
---
# <a name="overview-of-data-science-using-spark-on-azure-hdinsight"></a>Přehled vědeckého zpracování dat pomocí Sparku v Azure HDInsight

Tato sada témat ukazuje, jak pomocí HDInsight Spark doplňovat běžné úkoly pro datové vědy, jako je například příjem dat, vytváření funkcí, modelování a vyhodnocení modelu. Data, která se používají, jsou Ukázka datové sady 2013 NYC taxislužby Trip a jízdné. Sestavené modely zahrnují logistické a lineární regrese, náhodné doménové struktury a barevné zesílené stromy. Témata také ukazují, jak tyto modely ukládat do úložiště objektů BLOB v Azure (WASB) a jak určit skóre a vyhodnotit jejich prediktivní výkon. Pokročilejší témata popisují, jak je možné modely vyškolené pomocí křížového ověřování a s využitím úklidu Hyper-Parameter. Toto přehledové téma také odkazuje na témata, která popisují, jak nastavit cluster Spark, který potřebujete k dokončení kroků v dostupných návodech.

## <a name="spark-and-mllib"></a>Spark a MLlib
[Spark](https://spark.apache.org/) je open source platforma pro paralelní zpracování, která podporuje zpracování v paměti, aby se zvýšil výkon analytických aplikací s velkým objemem dat. Modul pro zpracování Spark je založený na rychlosti, snadném použití a propracované analýze. Funkce distribuovaného výpočtu v paměti Sparku nabízí dobrou volbu pro iterativní algoritmy používané v strojovém učení a výpočtech grafů. [MLlib](https://spark.apache.org/mllib/) je škálovatelná knihovna strojového učení Sparku, která přináší do tohoto distribuovaného prostředí možnosti algoritmu modelování.

## <a name="hdinsight-spark"></a>HDInsight Spark
[HDInsight Spark](../../hdinsight/spark/apache-spark-overview.md) je Azure hostovaná nabídka Open Source Sparku. Zahrnuje taky podporu pro **poznámkové bloky Jupyter PySpark** v clusteru Spark, které můžou spouštět interaktivní dotazy Spark SQL pro transformaci, filtrování a vizualizaci dat uložených v Azure BLOBs (WASB). PySpark je rozhraní Python API pro Spark. Fragmenty kódu, které poskytují řešení a zobrazují relevantní zobrazení k vizualizaci dat, která se spouštějí v poznámkových blocích Jupyter nainstalovaných v clusterech Spark. Postup modelování v těchto tématech obsahuje kód, který ukazuje, jak vlakovat, vyhodnocovat, ukládat a spotřebovat jednotlivé typy modelů.

## <a name="setup-spark-clusters-and-jupyter-notebooks"></a>Nastavení: Clustery Spark a poznámkové bloky Jupyter
Kroky a kód pro instalaci najdete v tomto návodu pro použití HDInsight Spark 1,6. Ale Jupyter poznámkové bloky jsou k dispozici pro clustery HDInsight Spark 1,6 a Spark 2,0. Popis poznámkových bloků a odkazů jsou k dispozici v [Readme.MD](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Readme.md) pro úložiště GitHubu, které je obsahuje. Kromě toho kód zde a v propojených poznámkových blocích je obecný a měl by fungovat na jakémkoli clusteru Spark. Pokud nepoužíváte HDInsight Spark, kroky pro instalaci a správu clusteru se můžou mírně lišit od toho, co se tady zobrazuje. Tady jsou odkazy na poznámkové bloky Jupyter pro Spark 1,6 (ke spuštění v jádru pySpark serveru Jupyter Notebook) a Spark 2,0 (ke spuštění v jádru pySpark3 serveru Jupyter Notebook):

### <a name="spark-16-notebooks"></a>Notebooky Spark 1,6
Tyto poznámkové bloky se spouštějí v jádru pySpark serveru Jupyter notebook.

- [pySpark-Machine-Learning-data-věda-Spark-data-prozkoumávání-Modeling. ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark1.6/pySpark-machine-learning-data-science-spark-data-exploration-modeling.ipynb): poskytuje informace o tom, jak provádět zkoumání dat, modelování a bodování s několika různými algoritmy.
- [pySpark-Machine-Learning-data-věda-Spark-Advanced-data-prozkoumává se Modeling. ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark1.6/pySpark-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb): obsahuje témata v poznámkových blocích #1 a vývoj modelů pomocí ladění parametrů a křížového ověřování.
- [pySpark-Machine-Learning-data-věda-Spark-model-spotřeba. ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark1.6/pySpark-machine-learning-data-science-spark-model-consumption.ipynb): ukazuje, jak zprovoznění uložený model pomocí Pythonu v clusterech HDInsight.

### <a name="spark-20-notebooks"></a>Notebooky Spark 2,0
Tyto poznámkové bloky se spouštějí v jádru pySpark3 serveru Jupyter notebook.

- [Spark 2.0 – pySpark3-Machine-Learning-data-věda-Spark-Advanced-data-prozkoumání-Modeling. ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark2.0/Spark2.0-pySpark3-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb): Tento soubor poskytuje informace o tom, jak provádět zkoumání dat, modelování a bodování v clusterech Spark 2,0 pomocí dat o cestách NYC taxislužby, které jsou popsané [tady](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-spark-overview#the-nyc-2013-taxi-data). Tento Poznámkový blok může být dobrým výchozím bodem pro rychlé zkoumání kódu, který jsme poskytli pro Spark 2,0. Podrobnější Poznámkový blok analyzuje data taxislužby NYC. Další informace najdete v dalším poznámkovém bloku v tomto seznamu. Podívejte se na poznámky uvedené v tomto seznamu, které porovnávají tyto poznámkové bloky.
- [Spark 2.0-pySpark3_NYC_Taxi_Tip_Regression. ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark2.0/Spark2.0_pySpark3_NYC_Taxi_Tip_Regression.ipynb): Tento soubor ukazuje, jak provádět data tahání (operace Spark SQL a dataframe), zkoumat, modelování a vyhodnocování pomocí sady dat NYC taxislužby, která je popsaná [zde](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-spark-overview#the-nyc-2013-taxi-data).
- [Spark 2.0-pySpark3_Airline_Departure_Delay_Classification. ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark2.0/Spark2.0_pySpark3_Airline_Departure_Delay_Classification.ipynb): v tomto souboru se dozvíte, jak provádět data tahání (operace Spark SQL a dataframe), zkoumat, modelování a vyhodnocování pomocí známé datové sady pro dobu odeslání v čase v čase od 2011 do 2012. Integruje datovou sadu s daty o počasí na letišti (například Windspeed, teplotu, nadmořskou výšku atd.) před modelováním, takže tyto funkce počasí mohou být zahrnuty v modelu.

<!-- -->

> [!NOTE]
> Datová sada leteckých dat byla přidána do notebooků Spark 2,0, aby lépe ilustroval použití algoritmů klasifikace. V následujících odkazech najdete informace o datové sadě a počasí v době leteckého letu:
> 
> - Data o odchodu leteckých společností: [https://www.transtats.bts.gov/ONTIME/](https://www.transtats.bts.gov/ONTIME/)
> 
> - Data o počasí z letiště: [https://www.ncdc.noaa.gov/](https://www.ncdc.noaa.gov/)

<!-- -->

<!-- -->

> [!NOTE]
> Poznámkové bloky Spark 2,0 na NYC taxislužby a zpoždění letového letu můžou v závislosti na velikosti vašeho clusteru HDI trvat 10 minut nebo i déle. První Poznámkový blok v seznamu výše obsahuje mnoho aspektů školení pro zkoumání dat, vizualizaci a analýzu modelů ML v poznámkovém bloku, který trvá méně času na spuštění s NYC sadou dat, ve které jsou soubory taxislužby a jízdné předem spojené: [Spark 2.0-pySpark3-Machine-Learning-data-věda-Spark-Advanced-data-prozkoumává-Modeling. ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark2.0/Spark2.0-pySpark3-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb). Tento Poznámkový blok trvá mnohem kratší dobu (2-3 minut) a může být dobrým výchozím bodem pro rychlé prozkoumání kódu, který jsme poskytli pro Spark 2,0.

<!-- -->

Informace o tom, jak vytvořit model Spark 2,0 a spotřebu modelu pro účely bodování, najdete v [dokumentu Spark 1,6](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark1.6/pySpark-machine-learning-data-science-spark-model-consumption.ipynb) pro použití v příkladu popisujícího, co je potřeba. Chcete-li použít tento příklad na Spark 2,0, nahraďte soubor kódu Pythonu [tímto souborem](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/Python/Spark2.0_ConsumeRFCV_NYCReg.py).

### <a name="prerequisites"></a>Požadavky

Následující postupy se týkají Spark 1,6. Pro verzi Spark 2,0 použijte Poznámkový blok popsaný a připojený k předchozímu.

1. Mít předplatné Azure. Pokud ho ještě nemáte, přečtěte si téma [získání bezplatné zkušební verze Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

2. K dokončení tohoto Názorného postupu potřebujete cluster Spark 1,6. Pokud ho chcete vytvořit, přečtěte si pokyny uvedené v článku [Začínáme: vytvoření Apache Spark v Azure HDInsight](../../hdinsight/spark/apache-spark-jupyter-spark-sql.md). Typ clusteru a verze se zadává v nabídce **Vybrat typ clusteru** .

![Konfigurovat cluster](./media/spark-overview/spark-cluster-on-portal.png)

<!-- -->

> [!NOTE]
> Téma, které ukazuje, jak používat Scala místo Pythonu k dokončení úloh pro komplexní proces zpracování dat, najdete v článku [vědy k datům pomocí Scala se Sparkem v Azure](scala-walkthrough.md).
>
>

<!-- -->

> [!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]
>
>

## <a name="the-nyc-2013-taxi-data"></a>Data taxislužby NYC 2013
Data NYC taxislužby TRIPS jsou přibližně 20 GB komprimovaných souborů hodnot oddělených čárkami (~ 48 GB nekomprimovaných), které obsahují více než 173 000 000 jednotlivých cest a tarifů placených za každou cestu. Každý záznam na cestách zahrnuje vyzvednutí a dropoff umístění a čas, číslo licence v anonymního napadení (strojvedoucí) a číslo Medallion (jedinečné ID taxislužby). Data se týkají všech cest v roce 2013 a jsou k dispozici v následujících dvou datových sadách pro každý měsíc:

1. Soubory CSV trip_data obsahují podrobnosti o cestě, jako je třeba počet cestujících, výběr a dropoff body, doba trvání cesty a délka cesty. Tady je několik ukázkových záznamů:

   `medallion,hack_license,vendor_id,rate_code,store_and_fwd_flag,pickup_datetime,dropoff_datetime,passenger_count,trip_time_in_secs,trip_distance,pickup_longitude,pickup_latitude,dropoff_longitude,dropoff_latitude`

   `89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,1,N,2013-01-01 15:11:48,2013-01-01 15:18:10,4,382,1.00,-73.978165,40.757977,-73.989838,40.751171`

   `0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-06 00:18:35,2013-01-06 00:22:54,1,259,1.50,-74.006683,40.731781,-73.994499,40.75066`

   `0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-05 18:49:41,2013-01-05 18:54:23,1,282,1.10,-74.004707,40.73777,-74.009834,40.726002`

   `DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:54:15,2013-01-07 23:58:20,2,244,.70,-73.974602,40.759945,-73.984734,40.759388`

   `DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:25:03,2013-01-07 23:34:24,1,560,2.10,-73.97625,40.748528,-74.002586,40.747868`

2. Soubory CSV trip_fare obsahují podrobnosti o tarifech placených za každou cestu, jako je typ platby, částka tarifů, příplatek a daně, tipy a mýtné a celková placená částka. Tady je několik ukázkových záznamů:

   `medallion, hack_license, vendor_id, pickup_datetime, payment_type, fare_amount, surcharge, mta_tax, tip_amount, tolls_amount, total_amount`

   `89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,2013-01-01 15:11:48,CSH,6.5,0,0.5,0,0,7`

   `0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-06 00:18:35,CSH,6,0.5,0.5,0,0,7`

   `0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-05 18:49:41,CSH,5.5,1,0.5,0,0,7`

   `DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:54:15,CSH,5,0.5,0.5,0,0,6`

   `DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:25:03,CSH,9.5,0.5,0.5,0,0,10.5`

Provedli jsme 0,1% vzor těchto souborů a připojili jsme soubory cest pro služební cestu \_ \_ do jedné datové sady, kterou použijete jako vstupní datovou sadu pro tento návod. Jedinečný klíč pro připojení k \_ datům cest a služební \_ tarif se skládá z těchto polí: Medallion, licence pro napadení \_ a \_ data a času vyzvednutí. Každý záznam datové sady obsahuje následující atributy reprezentující cestu NYC taxislužby:

| Pole | Stručný popis |
| --- | --- |
| medallion |Taxislužby Medallion (jedinečné ID taxislužby) |
| hack_license |Číslo licence Hackney |
| vendor_id |ID dodavatele taxislužby |
| rate_code |Sazba tarifu NYC taxislužby |
| store_and_fwd_flag |Příznak uložení a přeposlání |
| pickup_datetime |Datum &ho výběru času |
| dropoff_datetime |Dropoff data & času |
| pickup_hour |Vybrat hodinu |
| pickup_week |Vybrat týden v roce |
| názvy |Weekday (rozsah 1-7) |
| passenger_count |Počet cestujících v taxislužby Trip |
| trip_time_in_secs |Doba odezvy v sekundách |
| trip_distance |Dráha cesty v mílích |
| pickup_longitude |Vybrat zeměpisnou délku |
| pickup_latitude |Vybrat zeměpisnou šířku |
| dropoff_longitude |Dropoff Zeměpisná délka |
| dropoff_latitude |Dropoff Zeměpisná šířka |
| direct_distance |Přímá vzdálenost mezi výstupními a dropoff místy |
| payment_type |Typ platby (hotovost, kreditní karta atd.) |
| fare_amount |Částka tarifů v |
| plat |Plat |
| mta_tax |Daň za přepravu Metro pro MTA |
| tip_amount |Částka tipu |
| tolls_amount |Částka mýtné |
| total_amount |Total amount |
| po obskládkované |Po obdobu (0/1 pro ne nebo Ano) |
| tip_class |Tip Class (0: $0, 1: $0-5, 2: $6-10, 3: $11-20, 4: > $20) |

## <a name="execute-code-from-a-jupyter-notebook-on-the-spark-cluster"></a>Spouštění kódu z poznámkového bloku Jupyter v clusteru Spark
Jupyter Notebook můžete spustit z Azure Portal. Ve svém řídicím panelu najděte svůj cluster Spark a kliknutím na něj zadejte stránku správy pro svůj cluster. Pokud chcete otevřít poznámkový blok přidružený ke clusteru Spark, klikněte na **řídicí panely clusteru**  ->  **Jupyter notebook**.

![Řídicí panely clusteru](./media/spark-overview/spark-jupyter-on-portal.png)

Můžete také přejít k ***`https://CLUSTERNAME.azurehdinsight.net/jupyter`*** poznámkovým blokům Jupyter a získat k nim přístup. V části této adresy URL nahraďte název clusteru názvem vašeho vlastního clusteru. Pro přístup k poznámkovým blokům potřebujete heslo pro účet správce.

![Procházet Jupyter poznámkové bloky](./media/spark-overview/spark-jupyter-notebook.png)

Pokud chcete zobrazit adresář obsahující několik příkladů předbalených poznámkových bloků, které používají rozhraní PySpark API, vyberte PySpark. Poznámkové bloky obsahující ukázky kódu pro tuto sadu Sparku jsou k dispozici na [GitHubu](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/Spark/pySpark) .

Poznámkové bloky můžete nahrát přímo z [GitHubu](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/Spark/pySpark) na server Jupyter notebook v clusteru Spark. Na domovské stránce Jupyter klikněte na tlačítko **nahrát** v pravé části obrazovky. Otevře se Průzkumník souborů. Sem můžete vložit adresu URL webu GitHub (nezpracovaný obsah) poznámkového bloku a kliknout na **otevřít**.

V seznamu souborů Jupyter se znovu zobrazí název souboru s tlačítkem **Odeslat** . Klikněte na toto tlačítko **nahrát** . Nyní jste naimportovali Poznámkový blok. Zopakováním těchto kroků nahrajte další poznámkové bloky z tohoto návodu.

> [!TIP]
> Můžete kliknout pravým tlačítkem na odkazy v prohlížeči a vybrat **Kopírovat odkaz** a získat adresu URL nezpracovaného obsahu GitHubu. Tuto adresu URL můžete vložit do dialogového okna Jupyter odeslat Průzkumníka souborů.
> 
> 

Nyní můžete:

* Přečtěte si kód kliknutím na Poznámkový blok.
* Každou buňku spustíte stisknutím **klávesy SHIFT + ENTER**.
* Kliknutím na spustit v **buňce**spustíte celý Poznámkový blok  ->  **Run**.
* Použijte automatickou vizualizaci dotazů.

> [!TIP]
> Jádro PySpark automaticky vizualizuje výstup dotazů SQL (HiveQL). Máte možnost vybrat si z několika různých typů vizualizací (tabulka, výsečový, spojnicový, plošný nebo pruh) pomocí tlačítek nabídky **typ** v poznámkovém bloku:
>
>

![Křivka logistické regresní ROC pro obecný přístup](./media/spark-overview/pyspark-jupyter-autovisualization.png)

## <a name="whats-next"></a>Co dále?
Teď, když jste nastavili cluster HDInsight Spark a nahráli Jupyter poznámkové bloky, jste připraveni pracovat s tématy, která odpovídají třem notebookům PySpark. Ukazují, jak prozkoumat data a jak vytvářet a spotřebovávat modely. Poznámkový blok pokročilého zkoumání a modelování dat ukazuje, jak zahrnout křížové ověřování, navzájemování a vyhodnocování modelů.

**Zkoumání a modelování dat pomocí Sparku:** Prozkoumejte datovou sadu a vytvářejte, myslete a vyhodnoťte modely strojového učení pomocí tématu [Vytvoření binární klasifikace a regresní modely pro data pomocí sady nástrojů Spark MLlib Toolkit](spark-data-exploration-modeling.md) .

**Spotřeba modelu:** Informace o tom, jak určit skóre modelů klasifikace a regrese vytvořených v tomto tématu, najdete v tématu [skóre a vyhodnocení modelů strojového učení](spark-model-consumption.md)s využitím Sparku.

**Křížové ověřování a mazání pomocí parametrů**: viz [Pokročilá kontrola a modelování dat pomocí Sparku](spark-advanced-data-exploration-modeling.md) , jak lze modely vyškolené pomocí křížového ověřování a s možnostími úklidu Hyper-Parameter.

