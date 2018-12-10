---
title: Vědecké zkoumání dat pomocí Sparku v Azure HDInsight – vědecké zpracování týmových dat
description: Knihovna Spark MLlib toolkit přináší značné strojového učení, funkce pro distribuované prostředí HDInsight pro modelování.
services: machine-learning
author: marktab
manager: cgronlun
editor: cgronlun
ms.service: machine-learning
ms.component: team-data-science-process
ms.topic: article
ms.date: 11/13/2017
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: b61c66dbb152914fcacb9020deab0cc4b76a6bc0
ms.sourcegitcommit: 78ec955e8cdbfa01b0fa9bdd99659b3f64932bba
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/10/2018
ms.locfileid: "53139414"
---
# <a name="overview-of-data-science-using-spark-on-azure-hdinsight"></a>Přehled vědeckého zkoumání dat pomocí Sparku v Azure HDInsight

Tato sada témat ukazuje způsob použití Spark v HDInsight dokončete běžných úloh datových věd, jako je příjem dat, vytváření funkcí, modelování a vyhodnocení modelu. Data používaná je ukázka 2013 NYC taxislužby cesty a tarif datové sady. Historické modely budou vytvářeny zahrnují logistické a lineární regrese, náhodných doménové struktury a přechodu Posílený stromové struktury. Témata také ukazují, jak k uložení těchto modelů ve službě Azure blob storage (WASB) a jak stanovení skóre a vyhodnotit prediktivní výkonu. Pokročilejší témata se týkají, jak může být modelů trénovaných pomocí křížového ověření a hyperparametrické sweeping. Toto přehledové téma také odkazuje na témata, která popisují, jak vytvořit cluster Spark, které potřebujete k dokončení kroků v návody k dispozici. 

## <a name="spark-and-mllib"></a>Spark a MLlib
[Spark](http://spark.apache.org/) zpracovává rozšiřovatelnou platformu pro open source paralelním zpracováním, která podporuje v paměti pro zvýšení výkonu velkých objemů dat analytických aplikací. Modul zpracování Spark je postaven pro rychlost, snadné použití a sofistikované analýzy. Spark v paměti distribuovanou výpočetní možnosti usnadňují dobrou volbou pro iterativní algoritmy používané ve výpočtech machine learning a grafů. [MLlib](http://spark.apache.org/mllib/) je Spark škálovatelná knihovna pro machine learning, která přináší vylepšením funkce do této distribuované prostředí pro modelování. 

## <a name="hdinsight-spark"></a>HDInsight Spark
[HDInsight Spark](../../hdinsight/spark/apache-spark-overview.md) je hostované nabídku Azure z open-source Spark. Zahrnuje taky podporu **poznámkové bloky Jupyter PySpark** v clusteru Spark, který může spouštět interaktivní dotazy Spark SQL pro transformaci, filtrování a vizualizace dat uložených v Azure BLOB (WASB). PySpark je rozhraní Python API pro Spark. Fragmenty kódu, které poskytují řešení a zobrazit příslušné grafy k vizualizaci dat zde spustit v poznámkových blocích Jupyter nainstalované v clusterech Spark. Kroky modelování v těchto tématech obsahovat kód, který ukazuje, jak pro trénování, vyhodnocení, uložit a používat každý typ modelu. 

## <a name="setup-spark-clusters-and-jupyter-notebooks"></a>Instalační program: Clustery Spark a Jupyter notebooks
Postup instalace a kódu jsou k dispozici v tomto názorném postupu pro používání HDInsight Spark 1.6. Ale poznámkové bloky Jupyter jsou k dispozici pro clustery HDInsight Spark 1.6 i Spark 2.0. Popis poznámkových bloků a odkazy na nich jsou součástí [Readme.md](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Readme.md) pro úložiště GitHub, které je obsahují. Kromě toho kód tady v propojených poznámkových bloků je obecný a by mělo fungovat jakéhokoli jiného clusteru Spark. Pokud nepoužíváte HDInsight Spark, může být mírně lišit od co je znázorněna zde kroky instalace a správy clusteru. Pro usnadnění práce tady jsou odkazy na poznámkové bloky Jupyter pro Spark 1.6 (ke spuštění v jádra pySpark aplikace Jupyter Notebook server) a Spark 2.0 (ke spuštění v pySpark3 jádra aplikace Jupyter Notebook serveru):

### <a name="spark-16-notebooks"></a>Poznámkové bloky Spark 1.6
Tyto poznámkové bloky jsou ke spuštění v jádra pySpark server poznámkového bloku Jupyter.

- [pySpark-machine-learning-data-science-spark-data-exploration-modeling.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark1.6/pySpark-machine-learning-data-science-spark-data-exploration-modeling.ipynb): poskytuje informace o tom, jak provádět zkoumání dat, modelování a vyhodnocování se několik různých algoritmů.
- [pySpark-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark1.6/pySpark-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb): obsahuje témata v poznámkovém bloku #1 a vývoje modelů pomocí hyperparametrů a křížového ověření.
- [pySpark-machine-learning-data-science-spark-model-consumption.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark1.6/pySpark-machine-learning-data-science-spark-model-consumption.ipynb): ukazuje, jak na zprovoznění modelu uložené pomocí Pythonu v clusterech HDInsight.

### <a name="spark-20-notebooks"></a>Poznámkové bloky Spark 2.0
Tyto poznámkové bloky se mají spustit v jádru pySpark3 server poznámkového bloku Jupyter.

- [Spark2.0-pySpark3-Machine-Learning-data-Science-Spark-Advanced-data-Exploration-Modeling.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark2.0/Spark2.0-pySpark3-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb): Tento soubor obsahuje informace o tom, jak provádět zkoumání dat, modelování, a hodnocení ve Spark 2.0 clusterů pomocí cesty taxíkem NYC a tarif popsané na sady dat [tady](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-spark-overview#the-nyc-2013-taxi-data). Tento poznámkový blok může být dobrým výchozím bodem rychle prozkoumat kód, který jsme připravili pro Spark 2.0. Poznámkový blok podrobnější analyzuje data taxislužby NYC, najdete v části Další poznámkového bloku v tomto seznamu. Naleznete v poznámkách za tímto seznamem, které porovnávají tyto poznámkové bloky. 
- [Spark2.0 pySpark3_NYC_Taxi_Tip_Regression.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark2.0/Spark2.0_pySpark3_NYC_Taxi_Tip_Regression.ipynb): Tento soubor ukazuje, jak provádět tahání dat (Spark SQL a datový rámec operace), průzkum, modelování a vyhodnocování pomocí cesty taxíkem NYC a tarif datové sady je popsáno [zde ](https://docs.microsoft.com/azure/machine-learning/machine-learning-data-science-spark-overview#the-nyc-2013-taxi-data).
- [Spark2.0 pySpark3_Airline_Departure_Delay_Classification.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark2.0/Spark2.0_pySpark3_Airline_Departure_Delay_Classification.ipynb): Tento soubor ukazuje, jak provádět tahání dat (Spark SQL a datový rámec operace), průzkum, modelování a vyhodnocování pomocí dobře známých odeslání letecká společnost v čase datové sady z 2011 a 2012. Integrovali jsme letecká společnost datovou sadu s daty o počasí letiště (například rychlost větru, teplota, výška atd.) před modelování, takže tyto funkce počasí mohou být součástí modelu.

<!-- -->

> [!NOTE]
> Datová sada letecká společnost byl přidán do poznámkových bloků Spark 2.0 abychom vám lépe předvedli použití klasifikace algoritmů. V následujících tématech o letecká společnost včas odeslání datovou sadu a datovou sadu weather:

>- Data o včasných odeslání letecká společnost: [http://www.transtats.bts.gov/ONTIME/](http://www.transtats.bts.gov/ONTIME/)

>- Data o počasí letiště: [https://www.ncdc.noaa.gov/](https://www.ncdc.noaa.gov/) 
> 
> 

<!-- -->

<!-- -->

> [!NOTE]
Poznámkové bloky Spark 2.0 na NYC taxislužby města a letecká společnost letu zpoždění-sady dat může trvat 10 minut nebo déle ke spuštění (v závislosti na velikosti vašeho clusteru Hdinsight). První poznámkového bloku v seznamu nahoře ukazuje spoustu aspektů zkoumání dat, vizualizace a ML, model školení v poznámkovém bloku, která přebírá méně času na spuštění pomocí předvýpočtem zredukovaných NYC datové sady, ve kterém byly soubory taxislužby města a tarif předem připojené k doméně: [ Spark2.0-pySpark3-Machine-Learning-data-Science-Spark-Advanced-data-Exploration-Modeling.ipynb](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark2.0/Spark2.0-pySpark3-machine-learning-data-science-spark-advanced-data-exploration-modeling.ipynb) tento poznámkový blok trvá mnohem kratší dobu dokončení (2 až 3 minut) a může být vhodná výchozí bod pro rychlé zkoumání kódu uvádíme pro Spark 2.0. 

<!-- -->

Pokyny k operacionalizaci modelu Spark 2.0 a spotřeby modelu pro vyhodnocení, najdete v článku [Spark 1.6 dokumentu o spotřebě](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/pySpark/Spark1.6/pySpark-machine-learning-data-science-spark-model-consumption.ipynb) příklad sbalování kroky potřebné. Použít na Spark 2.0, nahraďte soubor kódu Python s [tento soubor](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/Spark/Python/Spark2.0_ConsumeRFCV_NYCReg.py).

### <a name="prerequisites"></a>Požadavky
Následující postupy se vztahují na Spark 1.6. Pro verzi Spark 2.0 pomocí poznámkových bloků popsaných a propojit s dříve. 

1. budete musíte mít předplatné Azure. Pokud není ještě nemáte, přečtěte si téma [získání bezplatné zkušební verze Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

2. budete potřebovat cluster Spark 1.6 k dokončení tohoto návodu. Pokud chcete jeden vytvořit, postupujte podle pokynů v [Začínáme: vytvoření Apache Spark v Azure HDInsight](../../hdinsight/spark/apache-spark-jupyter-spark-sql.md). Typ clusteru a verze určen **vybrat typ clusteru** nabídky. 

![Konfigurace clusteru](./media/spark-overview/spark-cluster-on-portal.png)

<!-- -->

> [!NOTE]
> Téma, které ukazuje, jak pomocí Scala namísto Pythonu k provedení úloh pro proces vědy data začátku do konce, najdete v článku [vědecké zkoumání dat pomocí Sparku v Azure pomocí Scala](scala-walkthrough.md).
> 
> 

<!-- -->

> [!INCLUDE [delete-cluster-warning](../../../includes/hdinsight-delete-cluster-warning.md)]
> 
> 

## <a name="the-nyc-2013-taxi-data"></a>Data taxislužby NYC 2013
Data o jízdách taxislužby NYC je přibližně 20 GB komprimované hodnot oddělených čárkami (CSV) souborů (nekomprimovaný ~ 48 GB), zahrnující 173 milionů jednotlivé trips a tarify placené pro každou cestu. Každý záznam o jízdách zahrnuje výběr nahoru a dropoff umístění a čas, číslo řidičského anonymizované hack (ovladače) a číslo Medailon (jedinečné id taxislužby.). Data v roce 2013 zahrnuje všechny cesty a je dostupné pro každý měsíc následující dvě datové sady:

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

Jsme přijata ukázku 0,1 % těchto souborů a připojená k cesty\_a dat o jízdách\_jízdenky CVS soubory do jedné datové sady má použít jako vstupní datovou sadu pro tento návod. Jedinečný klíč pro připojení o jízdách\_a dat o jízdách\_tarif se skládá z polí: medailonu, najděte, co je\_licence a vyzvednutí\_data a času. Každý záznam datové sady, která obsahuje následující atributy představující cesty taxíkem NYC:

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
| direct_distance |Přímé vzdálenost mezi výběru nahoru a dropoff umístění |
| payment_type |Typ platby (certifikačních autorit, platební karty atd.) |
| fare_amount |Tarif částka v |
| Příplatek za |Příplatek za |
| mta_tax |Daň za MTA |
| tip_amount |Velikost špičky |
| tolls_amount |Velikost mýtné |
| total_amount |Celková částka |
| šikmý |Šikmý (0 nebo 1 – Ne nebo Ano) |
| tip_class |Tip třídy (0: $0, 1: 0 – 5 USD, 2: $6-10, 3: $11-20, 4: > $20) |

## <a name="execute-code-from-a-jupyter-notebook-on-the-spark-cluster"></a>Spouštění kódu z poznámkového bloku Jupyter v clusteru Spark
Můžete spustit Poznámkový blok Jupyter na webu Azure Portal. Najít váš cluster Spark na řídicím panelu a klikněte na stránce Správa zadejte pro váš cluster. Otevřete Poznámkový blok přidružené ke clusteru Spark, klikněte na tlačítko **řídicí panely clusteru** -> **Poznámkový blok Jupyter** .

![Řídicí panely clusteru](./media/spark-overview/spark-jupyter-on-portal.png)

Můžete také přejít na ***https://CLUSTERNAME.azurehdinsight.net/jupyter*** pro přístup k poznámkové bloky Jupyter. Nahraďte názvem vašeho vlastního clusteru CLUSTERNAME část této adresy URL. Budete potřebovat heslo pro účet správce pro přístup k poznámkových bloků.

![Procházet poznámkové bloky Jupyter](./media/spark-overview/spark-jupyter-notebook.png)

Vyberte PySpark zobrazíte adresář, který obsahuje několik příkladů předem zabalené poznámkových bloků, které používají rozhraní API PySpark. Poznámkové bloky, které obsahují ukázky kódu pro tuto sadu Spark tématu najdete na adrese [Githubu](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/Spark/pySpark)

Můžete nahrát poznámkových bloků přímo z [Githubu](https://github.com/Azure/Azure-MachineLearning-DataScience/tree/master/Misc/Spark/pySpark) server poznámkového bloku Jupyter na svém clusteru Spark. Na domovské stránce vašeho Jupyter, klikněte na tlačítko **nahrát** tlačítko v pravé části obrazovky. Otevře se Průzkumník souborů. Tady můžete vložte adresu URL Githubu (nezpracovaný obsah) Poznámkový blok a klikněte na tlačítko **otevřít**. 

Název souboru se zobrazí v seznamu souboru Jupyter s **nahrát** tlačítko znovu. Kliknutím na tuto **nahrát** tlačítko. Jste teď importovali poznámkového bloku. Opakujte tyto kroky pro nahrání poznámkových bloků v tomto názorném postupu.

> [!TIP]
> Můžete kliknout pravým tlačítkem na odkazy v prohlížeči a vyberte **Kopírovat odkaz** k získání adresy URL githubu nezpracované obsahu. Vložte tuto adresu URL do Jupyter nahrání souboru explorer dialogového okna.
> 
> 

Nyní můžete:

* Zobrazit kód kliknutím poznámkového bloku.
* Spuštění každou buňku stisknutím kombinace kláves **SHIFT + ENTER**.
* Celý poznámkový blok spustit kliknutím na **buňky** -> **spustit**.
* Použijte automatické vizualizaci dotazy.

> [!TIP]
> Jádra PySpark automaticky vizualizuje výstup příkazů jazyka SQL (HiveQL). Budete mít možnost vybrat si mezi několika různé typy vizualizací (tabulky, výsečové, řádek, oblasti nebo panelu) s použitím **typ** tlačítka nabídky v poznámkovém bloku:
> 
> 

![Křivka roc s více TŘÍDAMI logistické regrese pro obecný přístup](./media/spark-overview/pyspark-jupyter-autovisualization.png)

## <a name="whats-next"></a>Co dále?
Teď, když se nastavují s clusterem HDInsight Spark a nahrání poznámkových bloků Jupyter, jste připraveni projít témata, která odpovídají tři poznámkových bloků PySpark. Zobrazí se postup zkoumání dat a jak vytvářet a využívat modely. Poznámkový blok pro zkoumání a modelování pokročilými datovými ukazuje, jak zahrnout křížového ověřování, hyperparametrické cílit na konkrétní a vyhodnocení modelů. 

**Zkoumání a modelování se Sparkem:** prozkoumat datovou sadu a vytvořit, stanovení skóre a vyhodnotit modely strojového učení z práce prostřednictvím [vytvořit binární klasifikačních a regresních modelů pro data s knihovna Spark MLlib Sada nástrojů](spark-data-exploration-modeling.md) tématu.

**Model spotřeby:** další postupy skóre klasifikačních a regresních modelů vytvořených v tomto tématu najdete v tématu [skóre a vyhodnocení modelů strojového učení předdefinovaných Spark](spark-model-consumption.md).

**Křížové ověření a hyperparameter sweeping**: naleznete v tématu [rozšířené zkoumání a modelování se Sparkem](spark-advanced-data-exploration-modeling.md) na to, jak modely můžete školení pomocí křížového ověření a hyperparametrické sweeping

