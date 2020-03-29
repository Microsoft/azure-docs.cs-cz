---
title: Pokyny pro optimalizaci výkonu úložiště datového jezera Azure ) Dokumenty společnosti Microsoft
description: Pokyny pro optimalizaci výkonu azure datového jezera Gen1 Hive
services: data-lake-store
documentationcenter: ''
author: stewu
manager: amitkul
editor: stewu
ms.assetid: ebde7b9f-2e51-4d43-b7ab-566417221335
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.date: 12/19/2016
ms.author: stewu
ms.openlocfilehash: 433c6b7d70cea9406b67d65e23cc357939cb5aa0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "61437272"
---
# <a name="performance-tuning-guidance-for-hive-on-hdinsight-and-azure-data-lake-storage-gen1"></a>Pokyny k ladění výkonu pro Hive na HDInsight a Azure Data Lake Storage Gen1

Výchozí nastavení byla nastavena tak, aby poskytovala dobrý výkon v mnoha různých případech použití.  Pro náročné dotazy vstupně-v a. Hive můžete vyladit tak, aby získal i lepší výkon s Azure Data Lake Storage Gen1.  

## <a name="prerequisites"></a>Požadavky

* **Předplatné Azure**. Viz [Získání bezplatné zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Účet Gen1 úložiště datového jezera**. Pokyny k jeho vytvoření najdete v tématu [Začínáme s Azure Data Lake Storage Gen1.](data-lake-store-get-started-portal.md)
* **Cluster Azure HDInsight** s přístupem k účtu Data Lake Storage Gen1. Viz [Vytvoření clusteru HDInsight s gen1 úložiště datového jezera](data-lake-store-hdinsight-hadoop-use-portal.md). Ujistěte se, že jste pro cluster povolili vzdálenou plochu.
* **Spuštění Hive na HDInsight**.  Další informace o spouštění úloh Hive na HDInsight upřené na jdete [na tématu Použití Hive na HDInsightu.](https://docs.microsoft.com/azure/hdinsight/hdinsight-use-hive)
* **Pokyny pro ladění výkonu pro úložiště datových jezer Gen1**.  Obecné koncepty výkonu najdete v [tématu Data Lake Storage Gen1 Performance Tuning Guidance](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-performance-tuning-guidance)

## <a name="parameters"></a>Parametry

Tady jsou nejdůležitější nastavení pro vyladění pro lepší výkon Data Lake Storage Gen1:

* **hive.tez.container.size** – množství paměti sužovky sužovaná jednotlivými úkoly

* **tez.grouping.min-size** – minimální velikost každého mapovače

* **tez.grouping.max-size** – maximální velikost každého mapovače

* **hive.exec.reducer.bytes.per.reducer** – velikost každého reduktoru

**hive.tez.container.size** – Velikost kontejneru určuje, kolik paměti je k dispozici pro každou úlohu.  Toto je hlavní vstup pro řízení souběžnosti v Hive.  

**tez.grouping.min-size** – Tento parametr umožňuje nastavit minimální velikost každého mapovače.  Pokud je počet mapovačů, které Tez vybere, menší než hodnota tohoto parametru, pak Tez použije zde nastavenou hodnotu.

**tez.grouping.max-size** – Parametr umožňuje nastavit maximální velikost každého mapovače.  Pokud je počet mapovačů, které Tez vybere, větší než hodnota tohoto parametru, pak Tez použije zde nastavenou hodnotu.

**hive.exec.reducer.bytes.per.reducer** – Tento parametr nastaví velikost každého reduktoru.  Ve výchozím nastavení je každý reduktor 256 MB.  

## <a name="guidance"></a>Doprovodné materiály

**Nastavte hive.exec.reducer.bytes.per.reducer** – Výchozí hodnota funguje dobře, když jsou data nekomprimovaná.  U komprimovaných dat byste měli zmenšit velikost reduktoru.  

**Nastavte hodnotu hive.tez.container.size** – V každém uzlu je paměť určena yarn.nodemanager.resource.memory-mb a ve výchozím nastavení by měla být správně nastavena v clusteru HDI.  Další informace o nastavení příslušné paměti v YARN naleznete v tomto [příspěvku](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-hive-out-of-memory-error-oom).

Vstupně-rozložitelné úlohy mohou těžit z více paralelismu snížením velikosti kontejneru Tez. To dává uživateli více kontejnerů, které zvyšuje souběžnost.  Některé dotazy Hive však vyžadují značné množství paměti (např.  Pokud úloha nemá dostatek paměti, získáte výjimku z paměti během běhu.  Pokud obdržíte výjimky z paměti, pak byste měli zvýšit paměť.   

Souběžný počet spuštěných úloh nebo paralelismu bude ohraničen celkovou pamětí YARN.  Počet kontejnerů YARN bude určovat, kolik souběžných úloh lze spustit.  Chcete-li najít paměť YARN na uzel, můžete přejít na Ambari.  Přejděte na YARN a zobrazte kartu Configs.  V tomto okně se zobrazí paměť YARN.  

        Total YARN memory = nodes * YARN memory per node
        # of YARN containers = Total YARN memory / Tez container size
Klíčem ke zlepšení výkonu pomocí úložiště datového jezera Gen1 je zvýšit souběžnost co nejvíce.  Tez automaticky vypočítá počet úkolů, které by měly být vytvořeny, takže není nutné jej nastavit.   

## <a name="example-calculation"></a>Příklad výpočtu

Řekněme, že máte cluster D14 s 8 uzlem.  

    Total YARN memory = nodes * YARN memory per node
    Total YARN memory = 8 nodes * 96GB = 768GB
    # of YARN containers = 768GB / 3072MB = 256

## <a name="limitations"></a>Omezení

**Omezení úložiště datového jezera Gen1** 

Pokud dosáhnete limitů šířky pásma poskytovaných data lake storage gen1, začnete vidět selhání úloh. To lze identifikovat sledováním chyb omezení v protokolech úloh.  Můžete snížit paralelismus zvýšením velikosti kontejneru Tez.  Pokud potřebujete více souběžnosti pro vaši práci, kontaktujte nás.

Chcete-li zkontrolovat, zda jste stále omezení, je třeba povolit protokolování ladění na straně klienta. Zde je návod, jak to můžete udělat:

1. Vložte následující vlastnost do vlastností log4j v konfiguraci Hive. To lze provést ze zobrazení Ambari: log4j.logger.com.microsoft.azure.datalake.store=DEBUG Restartujte všechny uzly/službu, aby se konfigurační konfigurace projevila.

2. Pokud jste stále omezení, uvidíte kód chyby HTTP 429 v souboru protokolu úlu. Soubor protokolu podregistru je v&lt;/tmp/ user&gt;/hive.log

## <a name="further-information-on-hive-tuning"></a>Další informace o ladění Hive

Zde je několik blogů, které vám pomohou vyladit vaše dotazy Hive:
* [Optimalizace dotazů Hive pro Hadoop v HDInsight](https://azure.microsoft.com/documentation/articles/hdinsight-hadoop-optimize-hive-query/)
* [Poradce při potížích s výkonem dotazu Hive](https://blogs.msdn.microsoft.com/bigdatasupport/2015/08/13/troubleshooting-hive-query-performance-in-hdinsight-hadoop-cluster/)
* [Zapalte diskuse o optimalizaci Hive na HDInsight](https://channel9.msdn.com/events/Machine-Learning-and-Data-Sciences-Conference/Data-Science-Summit-2016/MSDSS25)
