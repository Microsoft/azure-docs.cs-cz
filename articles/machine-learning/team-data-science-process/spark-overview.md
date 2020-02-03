---
title: Vědecké zkoumání dat pomocí Sparku v Azure HDInsight – vědecké zpracování týmových dat
description: Knihovna Spark MLlib toolkit přináší značné strojového učení, funkce pro distribuované prostředí HDInsight pro modelování.
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
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2020
ms.locfileid: "76718510"
---
# <a name="overview-of-data-science-using-spark-on-azure-hdinsight"></a>Přehled vědeckého zkoumání dat pomocí Sparku v Azure HDInsight

Tato sada témat ukazuje způsob použití Spark v HDInsight dokončete běžných úloh datových věd, jako je příjem dat, vytváření funkcí, modelování a vyhodnocení modelu. Data používaná je ukázka 2013 NYC taxislužby cesty a tarif datové sady. Historické modely budou vytvářeny zahrnují logistické a lineární regrese, náhodných doménové struktury a přechodu Posílený stromové struktury. Témata také ukazují, jak k uložení těchto modelů ve službě Azure blob storage (WASB) a jak stanovení skóre a vyhodnotit prediktivní výkonu. Pokročilejší témata se týkají, jak může být modelů trénovaných pomocí křížového ověření a hyperparametrické sweeping. Toto přehledové téma také odkazuje na témata, která popisují, jak vytvořit cluster Spark, které potřebujete k dokončení kroků v návody k dispozici.

## <a name="spark-and-mllib"></a>Spark a MLlib
[Spark](https://spark.apache.org/) je open source platforma pro paralelní zpracování, která podporuje zpracování v paměti, aby se zvýšil výkon analytických aplikací s velkým objemem dat. Modul zpracování Spark je postaven pro rychlost, snadné použití a sofistikované analýzy. Spark v paměti distribuovanou výpočetní možnosti usnadňují dobrou volbou pro iterativní algoritmy používané ve výpočtech machine learning a grafů. [MLlib](https://spark.apache.org/mllib/) je škálovatelná knihovna strojového učení Sparku, která přináší do tohoto distribuovaného prostředí možnosti algoritmu modelování.

## <a name="hdinsight-spark"></a>HDInsight Spark
[HDInsight Spark](../../hdinsight/spark/apache-spark-overview.md) je Azure hostovaná nabídka Open Source Sparku. Zahrnuje taky podporu pro **poznámkové bloky Jupyter PySpark** v clusteru Spark, které můžou spouštět interaktivní dotazy Spark SQL pro transformaci, filtrování a vizualizaci dat uložených v Azure BLOBs (WASB). PySpark je rozhraní Python API pro Spark. Fragmenty kódu, které poskytují řešení a zobrazit příslušné grafy k vizualizaci dat zde spustit v poznámkových blocích Jupyter nainstalované v clusterech Spark. Kroky modelování v těchto tématech obsahovat kód, který ukazuje, jak pro trénování, vyhodnocení, uložit a používat každý typ modelu.

## <a name="setup-spark-clusters-and-jupyter-notebooks"></a>Instalační program: Clustery Spark a Jupyter notebooks
Postup instalace a kódu jsou k dispozici v tomto názorném postupu pro používání HDInsight Spark 1.6. Ale poznámkové bloky Jupyter jsou k dispozici pro clustery HDInsight Spark 1.6 i Spark 2.0. Popis poznámkových bloků a odkazů jsou k dispozici v [Readme.MD](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Readme.md) pro úložiště GitHubu, které je obsahuje. Kromě toho kód tady v propojených poznámkových bloků je obecný a by mělo fungovat jakéhokoli jiného clusteru Spark. Pokud nepoužíváte HDInsight Spark, může být mírně lišit od co je znázorněna zde kroky instalace a správy clusteru. Tady jsou odkazy na poznámkové bloky Jupyter pro Spark 1,6 (ke spuštění v jádru pySpark serveru Jupyter Notebook) a Spark 2,0 (ke spuštění v jádru pySpark3 serveru Jupyter Notebook):

### <a name="spark-16-notebooks"></a>Poznámkové bloky Spark 1.6
Tyto poznámkové bloky jsou ke spuštění v jádra pySpark server poznámkového bloku Jupyter.

- [pySpark-Machine-Learning-data-věda-Spark-data-prozkoumávání-Modeling. ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark1.6/pySpark-machine-learning-data-science-spark-data-exploration-modeling.ipynb): poskytuje informace o tom, jak provádět zkoumání dat, modelování a bodování s několika různými algoritmy.
- [pySpark-Machine-Learning-data-věda-Spark-Advanced-data-prozkoumává se Modeling. ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark1.6/pySpark-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb): obsahuje témata v poznámkových blocích #1 a vývoj modelů pomocí ladění parametrů a křížového ověřování.
- [pySpark-Machine-Learning-data-věda-Spark-model-spotřeba. ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark1.6/pySpark-machine-learning-data-science-spark-model-consumption.ipynb): ukazuje, jak zprovoznění uložený model pomocí Pythonu v clusterech HDInsight.

### <a name="spark-20-notebooks"></a>Poznámkové bloky Spark 2.0
Tyto poznámkové bloky se mají spustit v jádru pySpark3 server poznámkového bloku Jupyter.

- [Spark 2.0 – pySpark3-Machine-Learning-data-věda-Spark-Advanced-data-prozkoumání-Modeling. ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark2.0/Spark2.0-pySpark3-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb): Tento soubor poskytuje informace o tom, jak provádět zkoumání dat, modelování a bodování v clusterech Spark 2,0 pomocí dat o cestách NYC taxislužby, které jsou popsané [tady](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-spark-overview#the-nyc-2013-taxi-data). Tento poznámkový blok může být dobrým výchozím bodem rychle prozkoumat kód, který jsme připravili pro Spark 2.0. Poznámkový blok podrobnější analyzuje data taxislužby NYC, najdete v části Další poznámkového bloku v tomto seznamu. Podívejte se na poznámky uvedené v tomto seznamu, které porovnávají tyto poznámkové bloky.
- [Spark 2.0-pySpark3_NYC_Taxi_Tip_Regression. ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark2.0/Spark2.0_pySpark3_NYC_Taxi_Tip_Regression.ipynb): Tento soubor ukazuje, jak provádět data tahání (operace Spark SQL a dataframe), zkoumat, modelování a vyhodnocování pomocí sady dat NYC taxislužby, která je popsaná [zde](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-spark-overview#the-nyc-2013-taxi-data).
- [Spark 2.0-pySpark3_Airline_Departure_Delay_Classification. ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark2.0/Spark2.0_pySpark3_Airline_Departure_Delay_Classification.ipynb): v tomto souboru se dozvíte, jak provádět data tahání (operace Spark SQL a dataframe), zkoumat, modelování a vyhodnocování pomocí známé datové sady pro dobu odeslání v čase v čase od 2011 do 2012. Integruje datovou sadu s daty o počasí na letišti (například Windspeed, teplotu, nadmořskou výšku atd.) před modelováním, takže tyto funkce počasí mohou být zahrnuty v modelu.

<!-- -->

> [!NOTE]
> Datová sada letecká společnost byl přidán do poznámkových bloků Spark 2.0 abychom vám lépe předvedli použití klasifikace algoritmů. V následujících tématech o letecká společnost včas odeslání datovou sadu a datovou sadu weather:
> 
> - Data o odchodu za čas letecké společnosti: [https://www.transtats.bts.gov/ONTIME/](https://www.transtats.bts.gov/ONTIME/)
> 
> - Data o počasí z letiště: [https://www.ncdc.noaa.gov/](https://www.ncdc.noaa.gov/)

<!-- -->

<!-- -->

> [!NOTE]
> Poznámkové bloky Spark 2.0 na NYC taxislužby města a letecká společnost letu zpoždění-sady dat může trvat 10 minut nebo déle ke spuštění (v závislosti na velikosti vašeho clusteru Hdinsight). První Poznámkový blok v seznamu výše obsahuje mnoho aspektů školení pro zkoumání dat, vizualizaci a analýzu modelů ML v poznámkovém bloku, který trvá méně času na spuštění s NYC sadou dat, ve které jsou soubory taxislužby a jízdné předem spojené: [Spark 2.0-pySpark3-Machine-Learning-data-věda-Spark-Advanced-data-prozkoumává-Modeling. ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark2.0/Spark2.0-pySpark3-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb). Tento Poznámkový blok trvá mnohem kratší dobu (2-3 minut) a může být dobrým výchozím bodem pro rychlé prozkoumání kódu, který jsme poskytli pro Spark 2,0.

<!-- -->

Informace o tom, jak vytvořit model Spark 2,0 a spotřebu modelu pro účely bodování, najdete v [dokumentu Spark 1,6](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark1.6/pySpark-machine-learning-data-science-spark-model-consumption.ipynb) pro použití v příkladu popisujícího, co je potřeba. Chcete-li použít tento příklad na Spark 2,0, nahraďte soubor kódu Pythonu [tímto souborem](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/Python/Spark2.0_ConsumeRFCV_NYCReg.py).

### <a name="prerequisites"></a>Předpoklady

Následující postupy se vztahují na Spark 1.6. Pro verzi Spark 2.0 pomocí poznámkových bloků popsaných a propojit s dříve.

1. Mít předplatné Azure. Pokud ho ještě nemáte, přečtěte si téma [získání bezplatné zkušební verze Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

2. K dokončení tohoto Názorného postupu potřebujete cluster Spark 1,6. Pokud ho chcete vytvořit, přečtěte si pokyny uvedené v článku [Začínáme: vytvoření Apache Spark v Azure HDInsight](../../hdinsight/spark/apache-spark-jupyter-spark-sql.md). Typ clusteru a verze se zadává v nabídce **Vybrat typ clusteru** .

![Konfigurace clusteru](./media/spark-overview/spark-cluster-on-portal.png)

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
Data o jízdách taxislužby NYC je přibližně 20 GB komprimované hodnot oddělených čárkami (CSV) souborů (nekomprimovaný ~ 48 GB), zahrnující 173 milionů jednotlivé trips a tarify placené pro každou cestu. Každý záznam o jízdách obsahuje číslo řidičského sbírat míčky a dropoff umístění a čas, anonymizované hack (ovladače) a číslo Medailon (jedinečné id taxislužby.). Data v roce 2013 zahrnuje všechny cesty a je dostupné pro každý měsíc následující dvě datové sady:

1. Soubory CSV "trip_data" obsahují podrobnosti o jízdách, jako je třeba počet cestujících vyzvednutí a odkazuje dropoff dojít doba trvání a délka cesty. Tady je několik ukázkových záznamů:

        medallion,hack_license,vendor_id,rate_code,store_and_fwd_flag,pickup_datetime,dropoff_datetime,passenger_count,trip_time_in_secs,trip_distance,pickup_longitude,pickup_latitude,dropoff_longitude,dropoff_latitude
        89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,1,N,2013-01-01 15:11:48,2013-01-01 15:18:10,4,382,1.00,-73.978165,40.757977,-73.989838,40.751171
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-06 00:18:35,2013-01-06 00:22:54,1,259,1.50,-74.006683,40.731781,-73.994499,40.75066
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,1,N,2013-01-05 18:49:41,2013-01-05 18:54:23,1,282,1.10,-74.004707,40.73777,-74.009834,40.726002
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:54:15,2013-01-07 23:58:20,2,244,.70,-73.974602,40.759945,-73.984734,40.759388
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,1,N,2013-01-07 23:25:03,2013-01-07 23:34:24,1,560,2.10,-73.97625,40.748528,-74.002586,40.747868
2. Soubory CSV "trip_fare" obsahovat podrobnosti o tarif placené pro každou cestu, například typ platby, velikost tarif, příplatek za a daní, tipy a mýtné a celkové částky zaplacené. Tady je několik ukázkových záznamů:

        medallion, hack_license, vendor_id, pickup_datetime, payment_type, fare_amount, surcharge, mta_tax, tip_amount, tolls_amount, total_amount
        89D227B655E5C82AECF13C3F540D4CF4,BA96DE419E711691B9445D6A6307C170,CMT,2013-01-01 15:11:48,CSH,6.5,0,0.5,0,0,7
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-06 00:18:35,CSH,6,0.5,0.5,0,0,7
        0BD7C8F5BA12B88E0B67BED28BEA73D8,9FD8F69F0804BDB5549F40E9DA1BE472,CMT,2013-01-05 18:49:41,CSH,5.5,1,0.5,0,0,7
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:54:15,CSH,5,0.5,0.5,0,0,6
        DFD2202EE08F7A8DC9A57B02ACB81FE2,51EE87E3205C985EF8431D850C786310,CMT,2013-01-07 23:25:03,CSH,9.5,0.5,0.5,0,0,10.5

Provedli jsme 0,1% vzor těchto souborů a připojili jste se k ní\_data a cesty\_jízdného tarifu do jedné datové sady, která se použije jako vstupní datová sada pro tento návod. Jedinečný klíč pro připojení cesty\_dat a jízdě\_tarif se skládá z těchto polí: Medallion, napadení\_a vyzvednutí\_data a času. Každý záznam datové sady, která obsahuje následující atributy představující cesty taxíkem NYC:

| Pole | Stručný popis |
| --- | --- |
| Medailon |Anonymizované taxislužby Medailon (taxislužby jedinečné id) |
| hack_license |Anonymizované číslo řidičského Hackney návrat na začátek řádku |
| vendor_id |Id dodavatele taxislužby města |
| rate_code |Míra taxislužby NYC tarif |
| store_and_fwd_flag |Store a příznak vpřed |
| pickup_datetime |Vyberte si datum a čas |
| dropoff_datetime |Dropoff datum a čas |
| pickup_hour |Sbírání hodinu |
| pickup_week |Sbírání týden v roce |
| Den v týdnu |Den v týdnu (rozsah 1-7) |
| passenger_count |Počet cestujících v cesty taxíkem |
| trip_time_in_secs |Doba odezvy v sekundách |
| trip_distance |Cesty v mil vzdálenost o jízdách |
| pickup_longitude |Sbírání zeměpisné délky |
| pickup_latitude |Sbírání zeměpisné šířky |
| dropoff_longitude |Zeměpisná délka Dropoff |
| dropoff_latitude |Zeměpisná šířka Dropoff |
| direct_distance |Přímá vzdálenost mezi výstupními a dropoff místy |
| payment_type |Typ platby (hotovost, kreditní karta atd.) |
| fare_amount |Tarif částka v |
| Příplatek za |Příplatek za |
| mta_tax |Daň za přepravu Metro pro MTA |
| tip_amount |Velikost špičky |
| tolls_amount |Velikost mýtné |
| total_amount |Celková částka |
| šikmý |Šikmý (0 nebo 1 – Ne nebo Ano) |
| tip_class |Tip třídy (0: $0, 1: 0 – 5 USD, 2: $6-10, 3: $11-20, 4: > $20) |

## <a name="execute-code-from-a-jupyter-notebook-on-the-spark-cluster"></a>Spouštění kódu z poznámkového bloku Jupyter v clusteru Spark
Můžete spustit Poznámkový blok Jupyter na webu Azure Portal. Najít váš cluster Spark na řídicím panelu a klikněte na stránce Správa zadejte pro váš cluster. Pokud chcete otevřít poznámkový blok přidružený ke clusteru Spark, klikněte na **řídicí panely clusteru** -> **Jupyter notebook**.

![Řídicí panely clusteru](./media/spark-overview/spark-jupyter-on-portal.png)

Pro přístup k poznámkovým blokům Jupyter můžete také přejít na ***https://CLUSTERNAME.azurehdinsight.net/jupyter*** . Nahraďte názvem vašeho vlastního clusteru CLUSTERNAME část této adresy URL. Budete potřebovat heslo pro účet správce pro přístup k poznámkových bloků.

![Procházet poznámkové bloky Jupyter](./media/spark-overview/spark-jupyter-notebook.png)

Pokud chcete zobrazit adresář obsahující několik příkladů předbalených poznámkových bloků, které používají rozhraní PySpark API, vyberte PySpark. Poznámkové bloky obsahující ukázky kódu pro tuto sadu Sparku jsou k dispozici na [GitHubu](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/Spark/pySpark) .

Poznámkové bloky můžete nahrát přímo z [GitHubu](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/Spark/pySpark) na server Jupyter notebook v clusteru Spark. Na domovské stránce Jupyter klikněte na tlačítko **nahrát** v pravé části obrazovky. Otevře se Průzkumník souborů. Sem můžete vložit adresu URL webu GitHub (nezpracovaný obsah) poznámkového bloku a kliknout na **otevřít**.

V seznamu souborů Jupyter se znovu zobrazí název souboru s tlačítkem **Odeslat** . Klikněte na toto tlačítko **nahrát** . Jste teď importovali poznámkového bloku. Opakujte tyto kroky pro nahrání poznámkových bloků v tomto názorném postupu.

> [!TIP]
> Můžete kliknout pravým tlačítkem na odkazy v prohlížeči a vybrat **Kopírovat odkaz** a získat adresu URL nezpracovaného obsahu GitHubu. Vložte tuto adresu URL do Jupyter nahrání souboru explorer dialogového okna.
> 
> 

Nyní můžete:

* Zobrazit kód kliknutím poznámkového bloku.
* Každou buňku spustíte stisknutím **klávesy SHIFT + ENTER**.
* Kliknutím na **buňku** -> **Spustit**spusťte celý Poznámkový blok.
* Použijte automatické vizualizaci dotazy.

> [!TIP]
> Jádra PySpark automaticky vizualizuje výstup příkazů jazyka SQL (HiveQL). Máte možnost vybrat si z několika různých typů vizualizací (tabulka, výsečový, spojnicový, plošný nebo pruh) pomocí tlačítek nabídky **typ** v poznámkovém bloku:
>
>

![Křivka roc s více TŘÍDAMI logistické regrese pro obecný přístup](./media/spark-overview/pyspark-jupyter-autovisualization.png)

## <a name="whats-next"></a>Co dále?
Teď, když se nastavují s clusterem HDInsight Spark a nahrání poznámkových bloků Jupyter, jste připraveni projít témata, která odpovídají tři poznámkových bloků PySpark. Zobrazí se postup zkoumání dat a jak vytvářet a využívat modely. Poznámkový blok pro zkoumání a modelování pokročilými datovými ukazuje, jak zahrnout křížového ověřování, hyperparametrické cílit na konkrétní a vyhodnocení modelů.

**Zkoumání a modelování dat pomocí Sparku:** Prozkoumejte datovou sadu a vytvářejte, myslete a vyhodnoťte modely strojového učení pomocí tématu [Vytvoření binární klasifikace a regresní modely pro data pomocí sady nástrojů Spark MLlib Toolkit](spark-data-exploration-modeling.md) .

**Spotřeba modelu:** Informace o tom, jak určit skóre modelů klasifikace a regrese vytvořených v tomto tématu, najdete v tématu [skóre a vyhodnocení modelů strojového učení](spark-model-consumption.md)s využitím Sparku.

**Křížové ověřování a mazání pomocí parametrů**: viz [Pokročilá kontrola a modelování dat pomocí Sparku](spark-advanced-data-exploration-modeling.md) , jak lze modely vyškolené pomocí křížového ověřování a s možnostími úklidu Hyper-Parameter.

