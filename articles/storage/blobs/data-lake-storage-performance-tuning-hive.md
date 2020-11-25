---
title: 'Ladění výkonu: podregistr, & HDInsight Azure Data Lake Storage Gen2 | Microsoft Docs'
description: Seznamte se s pokyny pro ladění náročných na vstupně-výstupní dotazy pomocí podregistru, HDInsight a Azure Data Lake Storage Gen2.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: how-to
ms.date: 11/18/2019
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: 4b1e5dd3c72122ade2fd4d4092bb18a7acf215f5
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "95912939"
---
# <a name="tune-performance-hive-hdinsight--azure-data-lake-storage-gen2"></a>Ladění výkonu: podregistr, & HDInsight Azure Data Lake Storage Gen2

Výchozí nastavení byla nastavena tak, aby poskytovala dobrý výkon v mnoha různých případech použití.  U dotazů náročných na vstupně-výstupní operace může být podregistr vyladěn, aby bylo možné dosáhnout vyššího výkonu Azure Data Lake Storage Gen2.  

## <a name="prerequisites"></a>Požadavky

* **Předplatné Azure**. Viz [Získání bezplatné zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Účet Data Lake Storage Gen2**. Pokyny, jak ho vytvořit, najdete v tématu [rychlý Start: vytvoření účtu úložiště Azure Data Lake Storage Gen2.](../common/storage-account-create.md)
* **Cluster Azure HDInsight** s přístupem k účtu Data Lake Storage Gen2. Viz [použití Azure Data Lake Storage Gen2 s clustery Azure HDInsight](../../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2.md) .
* **Spuštění podregistru v HDInsight**.  Další informace o spouštění úloh podregistru v HDInsight najdete v tématu [použití podregistru v HDInsight](../../hdinsight/hadoop/hdinsight-use-hive.md) .
* **Pokyny k ladění výkonu na data Lake Storage Gen2**.  Obecné koncepty výkonu najdete v tématu [Data Lake Storage Gen2 pokyny k ladění výkonu](data-lake-storage-performance-tuning-guidance.md) .

## <a name="parameters"></a>Parametry

Tady je nejdůležitější nastavení pro optimalizaci pro zlepšení výkonu Data Lake Storage Gen2:

* **podregistr. TEZ. Container. Size** – množství paměti využité jednotlivými úkoly

* **TEZ. Grouping. min-Size** – minimální velikost každého mapování

* **TEZ. Grouping. Max-Size** – maximální velikost každého mapování

* **hive.exec. snižovalo. bajtů. per.** – velikost každého zmenšení

**podregistr. TEZ. Container. Size** – velikost kontejneru určuje, kolik paměti je k dispozici pro každý úkol.  Toto je hlavní vstup pro řízení souběžnosti v podregistru.  

**TEZ. Grouping. min-Size** – tento parametr umožňuje nastavit minimální velikost každého mapovače.  Pokud počet mapovačů, které tez zvolí, je menší než hodnota tohoto parametru, pak tez použije nastavenou hodnotu.

**TEZ. Grouping. Max-Size** – parametr umožňuje nastavit maximální velikost každého mapovače.  Pokud počet mapovačů, které tez zvolí, je větší než hodnota tohoto parametru, pak tez použije nastavenou hodnotu.

**hive.exec. snižoval. bytes. per.** – tento parametr nastaví velikost každého zmenšení.  Ve výchozím nastavení má každý zpomalení 256 MB.  

## <a name="guidance"></a>Pokyny

**Nastavit hive.exec.. bytes. per.** – výchozí hodnota je vhodná, pokud jsou data nekomprimovaná.  Pro komprimovaná data byste měli zmenšit velikost zmenšení.  

**Nastavení podregistru. TEZ. Container. Size** – v každém uzlu je paměť určena pomocí příze. NodeManager. Resource. Memory-MB a měla by být ve výchozím nastavení správně nastavena v clusteru HDI.  Další informace o nastavení vhodné paměti v PŘÍZi najdete v tomto [příspěvku](../../hdinsight/hdinsight-hadoop-hive-out-of-memory-error-oom.md).

Úlohy náročné na vstupně-výstupní operace můžou těžit z více paralelismu díky snížení velikosti kontejneru TEZ. Tím se uživateli poskytne více kontejnerů, které zvyšují souběžnost.  Některé dotazy podregistru ale vyžadují značné množství paměti (např. MapJoin).  Pokud úloha nemá dostatek paměti, během běhu se zobrazí výjimka z důvodu nedostatku paměti.  Pokud se dostanou výjimky z paměti, měli byste zvětšit paměť.   

Souběžný počet probíhajících úloh nebo paralelismu bude svázán s celkovou pamětí PŘÍZe.  Počet kontejnerů PŘÍZe určí, kolik souběžných úloh může být spuštěno.  Pro nalezení paměti PŘÍZe na uzel můžete přejít na Ambari.  Přejděte na PŘÍZe a zobrazte kartu konfigurace.  V tomto okně se zobrazí paměť PŘÍZe.  

- Celková paměť PŘÍZ = uzly * PŘÍZe paměť na uzel
- \# kontejnerů PŘÍZ = celková paměť PŘÍZe/velikost kontejneru tez

Klíčem ke zvýšení výkonu pomocí Data Lake Storage Gen2 je co nejvíc zvýšit souběžnost.  Tez automaticky vypočítá počet úloh, které by se měly vytvořit, takže je nemusíte nastavovat.   

## <a name="example-calculation"></a>Příklad výpočtu

Řekněme, že máte cluster D14 s 8 uzly.  

- Celková paměť PŘÍZ = uzly * PŘÍZe paměť na uzel
- Celková paměť PŘÍZ = 8 uzlů * 96GB = 768GB
- \# kontejnerů PŘÍZ = 768GB/3072MB = 256

## <a name="further-information-on-hive-tuning"></a>Další informace o optimalizaci podregistru

Tady je několik blogů, které vám pomůžou při optimalizaci dotazů na podregistr:
* [Optimalizace dotazů na podregistr pro Hadoop v HDInsight](../../hdinsight/hdinsight-hadoop-optimize-hive-query.md)
* [Optimalizace dotazů Apache Hivu v Azure HDInsightu](../../hdinsight/hdinsight-hadoop-optimize-hive-query.md)
* [Ignite hovořit o optimalizaci podregistru v HDInsight](https://channel9.msdn.com/events/Machine-Learning-and-Data-Sciences-Conference/Data-Science-Summit-2016/MSDSS25)