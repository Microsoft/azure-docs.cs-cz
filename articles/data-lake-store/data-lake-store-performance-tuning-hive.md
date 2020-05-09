---
title: Optimalizace výkonu – podregistr na Azure Data Lake Storage Gen1
description: Pokyny k ladění výkonu pro podregistr v HdInsight a Azure Data Lake Storage Gen1.
author: stewu
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 12/19/2016
ms.author: stewu
ms.openlocfilehash: 2e44332ddab9387c05a45d15101ccd2bdec3ada4
ms.sourcegitcommit: 366e95d58d5311ca4b62e6d0b2b47549e06a0d6d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/01/2020
ms.locfileid: "82690522"
---
# <a name="performance-tuning-guidance-for-hive-on-hdinsight-and-azure-data-lake-storage-gen1"></a>Pokyny k ladění výkonu pro podregistr v HDInsight a Azure Data Lake Storage Gen1

Výchozí nastavení byla nastavena tak, aby poskytovala dobrý výkon v mnoha různých případech použití.  U dotazů náročných na vstupně-výstupní operace může být podregistr vyladěn, aby bylo možné dosáhnout vyššího výkonu Azure Data Lake Storage Gen1.  

## <a name="prerequisites"></a>Požadavky

* **Předplatné Azure**. Viz [Získání bezplatné zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Účet Data Lake Storage Gen1**. Pokyny, jak ho vytvořit, najdete v tématu Začínáme [s Azure Data Lake Storage Gen1](data-lake-store-get-started-portal.md) .
* **Cluster Azure HDInsight** s přístupem k účtu Data Lake Storage Gen1. Další informace najdete v tématu [Vytvoření clusteru HDInsight s Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md). Ujistěte se, že jste pro cluster povolili vzdálenou plochu.
* **Spuštění podregistru v HDInsight**.  Další informace o spouštění úloh podregistru v HDInsight najdete v tématu [použití podregistru v HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-use-hive) .
* **Pokyny k ladění výkonu na data Lake Storage Gen1**.  Obecné koncepty výkonu najdete v tématu [Data Lake Storage Gen1 pokyny k ladění výkonu](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-performance-tuning-guidance) .

## <a name="parameters"></a>Parametry

Tady je nejdůležitější nastavení pro optimalizaci pro zlepšení výkonu Data Lake Storage Gen1:

* **podregistr. TEZ. Container. Size** – množství paměti využité jednotlivými úkoly

* **TEZ. Grouping. min-Size** – minimální velikost každého mapování

* **TEZ. Grouping. Max-Size** – maximální velikost každého mapování

* **podregistr. Exec. snižoval. bytes. per.** – velikost každého zmenšení

**podregistr. TEZ. Container. Size** – velikost kontejneru určuje, kolik paměti je k dispozici pro každý úkol.  Toto je hlavní vstup pro řízení souběžnosti v podregistru.  

**TEZ. Grouping. min-Size** – tento parametr umožňuje nastavit minimální velikost každého mapovače.  Pokud počet mapovačů, které tez zvolí, je menší než hodnota tohoto parametru, pak tez použije nastavenou hodnotu.

**TEZ. Grouping. Max-Size** – parametr umožňuje nastavit maximální velikost každého mapovače.  Pokud počet mapovačů, které tez zvolí, je větší než hodnota tohoto parametru, pak tez použije nastavenou hodnotu.

**podregistr. Exec. snižoval. bytes. per. snižoval** – tento parametr nastavuje velikost každého zmenšení.  Ve výchozím nastavení má každý zpomalení 256 MB.  

## <a name="guidance"></a>Doprovodné materiály

**Nastavit podregistr. Exec. snižovalo. bytes. per.** – výchozí hodnota je vhodná, když jsou data nekomprimovaná.  Pro komprimovaná data byste měli zmenšit velikost zmenšení.  

**Nastavení podregistru. TEZ. Container. Size** – v každém uzlu je paměť určena pomocí příze. NodeManager. Resource. Memory-MB a měla by být ve výchozím nastavení správně nastavena v clusteru HDI.  Další informace o nastavení vhodné paměti v PŘÍZi najdete v tomto [příspěvku](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-hive-out-of-memory-error-oom).

Úlohy náročné na vstupně-výstupní operace můžou těžit z více paralelismu díky snížení velikosti kontejneru TEZ. Tím se uživateli poskytne více kontejnerů, které zvyšují souběžnost.  Některé dotazy podregistru ale vyžadují značné množství paměti (např. MapJoin).  Pokud úloha nemá dostatek paměti, během běhu se zobrazí výjimka z důvodu nedostatku paměti.  Pokud se dostanou výjimky z paměti, měli byste zvětšit paměť.   

Souběžný počet probíhajících úloh nebo paralelismu bude svázán s celkovou pamětí PŘÍZe.  Počet kontejnerů PŘÍZe určí, kolik souběžných úloh může být spuštěno.  Pro nalezení paměti PŘÍZe na uzel můžete přejít na Ambari.  Přejděte na PŘÍZe a zobrazte kartu konfigurace.  V tomto okně se zobrazí paměť PŘÍZe.  

        Total YARN memory = nodes * YARN memory per node
        # of YARN containers = Total YARN memory / Tez container size
Klíčem ke zvýšení výkonu pomocí Data Lake Storage Gen1 je co nejvíc zvýšit souběžnost.  Tez automaticky vypočítá počet úloh, které by se měly vytvořit, takže je nemusíte nastavovat.   

## <a name="example-calculation"></a>Příklad výpočtu

Řekněme, že máte cluster D14 s 8 uzly.  

    Total YARN memory = nodes * YARN memory per node
    Total YARN memory = 8 nodes * 96GB = 768GB
    # of YARN containers = 768GB / 3072MB = 256

## <a name="limitations"></a>Omezení

**Omezení Data Lake Storage Gen1** 

Pokud jste dosáhli limitu šířky pásma poskytovaného Data Lake Storage Gen1, začali byste zobrazovat selhání úloh. To je možné identifikovat pozorováním chyb omezení v protokolech úloh.  Paralelismus můžete snížit zvýšením velikosti kontejneru TEZ.  Pokud potřebujete pro svoji úlohu víc souběžnosti, kontaktujte nás prosím.

Chcete-li zjistit, zda se vám omezilo omezení, je nutné povolit protokolování ladění na straně klienta. Můžete to udělat takto:

1. Do vlastností log4j v části Konfigurace podregistru vložte následující vlastnost. To se dá udělat z Ambari zobrazení: log4j. protokolovací. com. Microsoft. Azure. datalake. Store = ladění restartujte všechny uzly/služby, aby se konfigurace projevila.

2. Pokud se vám omezí omezení, zobrazí se v souboru protokolu podregistru kód chyby HTTP 429. Soubor protokolu podregistru je v/tmp/&lt;User&gt;/Hive.log

## <a name="further-information-on-hive-tuning"></a>Další informace o optimalizaci podregistru

Tady je několik blogů, které vám pomůžou při optimalizaci dotazů na podregistr:
* [Optimalizace dotazů na podregistr pro Hadoop v HDInsight](https://azure.microsoft.com/documentation/articles/hdinsight-hadoop-optimize-hive-query/)
* [Řešení potíží s výkonem dotazů na podregistr](https://blogs.msdn.microsoft.com/bigdatasupport/2015/08/13/troubleshooting-hive-query-performance-in-hdinsight-hadoop-cluster/)
* [Ignite hovořit o optimalizaci podregistru v HDInsight](https://channel9.msdn.com/events/Machine-Learning-and-Data-Sciences-Conference/Data-Science-Summit-2016/MSDSS25)
