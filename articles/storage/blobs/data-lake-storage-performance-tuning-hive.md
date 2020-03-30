---
title: 'Ladění výkonu: Hive, HDInsight & Azure Data Lake Storage Gen2 | Dokumenty společnosti Microsoft'
description: Pokyny pro optimalizaci výkonu úložiště datového jezera Azure Gen2 Hive.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 11/18/2019
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: 66042568cede364c16302fbd85751de4113bbe0f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74327584"
---
# <a name="tune-performance-hive-hdinsight--azure-data-lake-storage-gen2"></a>Výkon ladění: Hive, HDInsight & Azure Data Lake Storage Gen2

Výchozí nastavení byla nastavena tak, aby poskytovala dobrý výkon v mnoha různých případech použití.  Pro náročné dotazy vstupně-v a. Hive můžete vyladit tak, aby získal i lepší výkon s Azure Data Lake Storage Gen2.  

## <a name="prerequisites"></a>Požadavky

* **Předplatné Azure**. Viz [Získání bezplatné zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Účet Data Lake Storage Gen2**. Pokyny k jeho vytvoření najdete v [tématu Úvodní příručka: Vytvoření účtu úložiště Azure Data Lake Storage Gen2](data-lake-storage-quickstart-create-account.md)
* **Cluster Azure HDInsight** s přístupem k účtu Data Lake Storage Gen2. Viz [Použití Azure Data Lake Storage Gen2 s clustery Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2)
* **Spuštění Hive na HDInsight**.  Další informace o spouštění úloh Hive na HDInsight upřené na jdete [na tématu Použití Hive na HDInsightu.](https://docs.microsoft.com/azure/hdinsight/hdinsight-use-hive)
* **Pokyny pro ladění výkonu pro úložiště datových jezer Gen2**.  Obecné koncepty výkonu najdete v [tématu Data Lake Storage Gen2 Performance Tuning Guidance](data-lake-storage-performance-tuning-guidance.md)

## <a name="parameters"></a>Parametry

Tady jsou nejdůležitější nastavení pro vyladění pro lepší výkon Data Lake Storage Gen2:

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
Klíčem ke zlepšení výkonu pomocí úložiště datového jezera Gen2 je zvýšit souběžnost co nejvíce.  Tez automaticky vypočítá počet úkolů, které by měly být vytvořeny, takže není nutné jej nastavit.   

## <a name="example-calculation"></a>Příklad výpočtu

Řekněme, že máte cluster D14 s 8 uzlem.  

    Total YARN memory = nodes * YARN memory per node
    Total YARN memory = 8 nodes * 96GB = 768GB
    # of YARN containers = 768GB / 3072MB = 256

## <a name="further-information-on-hive-tuning"></a>Další informace o ladění Hive

Zde je několik blogů, které vám pomohou vyladit vaše dotazy Hive:
* [Optimalizace dotazů Hive pro Hadoop v HDInsight](https://azure.microsoft.com/documentation/articles/hdinsight-hadoop-optimize-hive-query/)
* [Poradce při potížích s výkonem dotazu Hive](https://blogs.msdn.microsoft.com/bigdatasupport/2015/08/13/troubleshooting-hive-query-performance-in-hdinsight-hadoop-cluster/)
* [Zapalte diskuse o optimalizaci Hive na HDInsight](https://channel9.msdn.com/events/Machine-Learning-and-Data-Sciences-Conference/Data-Science-Summit-2016/MSDSS25)
