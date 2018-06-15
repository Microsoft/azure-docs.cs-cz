---
title: Ladění pravidla výkonu Hive Azure Data Lake Store | Microsoft Docs
description: Ladění pravidla výkonu Hive Azure Data Lake Store
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
ms.openlocfilehash: c46eb1b2da62d70337e60066ed0706c3a4fdedcf
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
ms.locfileid: "34198965"
---
# <a name="performance-tuning-guidance-for-hive-on-hdinsight-and-azure-data-lake-store"></a>Pokyny pro Hive v HDInsight a Azure Data Lake Store optimalizace výkonu

K zajištění dobrý výkon v řadě případů použití v odlišných byly nastaveny výchozí nastavení.  Pro dotazy náročné na vstupně-výstupních operací lze získat lepší výkon s ADLS ladit Hive.  

## <a name="prerequisites"></a>Požadavky

* **Předplatné Azure**. Viz [Získání bezplatné zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Účet Azure Data Lake Store**. Pokyny o tom, jak vytvořit najdete v tématu [Začínáme s Azure Data Lake Store](data-lake-store-get-started-portal.md)
* **Azure HDInsight cluster** s přístupem k účtu Data Lake Store. V tématu [vytvoření clusteru HDInsight s Data Lake Store](data-lake-store-hdinsight-hadoop-use-portal.md). Ujistěte se, že povolení vzdálené plochy pro cluster.
* **Spuštění Hive v HDInsight**.  Další informace o probíhajících úloh Hive v HDInsight naleznete v tématu [použití Hive v HDInsight] (https://docs.microsoft.com/azure/hdinsight/hdinsight-use-hive)
* **Ladění pokyny na ADLS výkonu**.  Obecný výkon koncepty, najdete v části [Data Lake Store výkonu ladění pokyny](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-performance-tuning-guidance)

## <a name="parameters"></a>Parametry

Zde jsou nejdůležitější nastavení a vylaďte pro zlepšení výkonu ADLS:

* **Hive.tez.Container.size** – množství paměti, které každý úlohy

* **velikost tez.Grouping.min** – minimální velikost každé mapper

* **velikost tez.Grouping.Max** – maximální velikost každé mapper

* **Hive.Exec.reducer.bytes.per.reducer** – velikost každé reduktorem

**Hive.tez.Container.size** – velikost kontejneru Určuje, kolik paměti je k dispozici pro každou úlohu.  Toto je hlavní vstup pro řízení souběžnost v Hive.  

**velikost tez.Grouping.min** – tento parametr můžete nastavit minimální velikost každé mapper.  Pokud počet mappers, které vybral Tez je menší než hodnota tohoto parametru, Tez použije nastavená hodnota.

**velikost tez.Grouping.Max** – parametr můžete nastavit maximální velikost každé mapper.  Pokud počet mappers, které vybral Tez je větší než hodnota tohoto parametru, Tez použije nastavená hodnota.

**Hive.Exec.reducer.bytes.per.reducer** – tento parametr nastaví velikost každé reduktorem.  Ve výchozím nastavení je každý reduktorem 256MB.  

## <a name="guidance"></a>Doprovodné materiály

**Nastavit hive.exec.reducer.bytes.per.reducer** – výchozí hodnota funguje dobře, když nekomprimované data.  Pro data, která je komprimován by měl zmenšete velikost reduktorem.  

**Nastavit hive.tez.container.size** – v každém uzlu, je zadána yarn.nodemanager.resource.memory mb paměti a musí být správně v clusteru HDI ve výchozím nastavení.  Další informace o nastavení odpovídající paměti v YARN najdete [post](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-hive-out-of-memory-error-oom).

Zatížení s intenzivním vstupně-výstupních operací můžete těžit z další paralelismus snížením velikosti kontejneru Tez. To umožňuje uživateli víc kontejnerů, což zvyšuje souběžnosti.  Ale některé dotazy Hive vyžadovat značné množství paměti (např. MapJoin).  Pokud úloha nemá dostatek paměti, zobrazí se nedostatku paměti výjimky za běhu.  Pokud se zobrazí nedostatek paměti výjimky, měli byste zvýšit velikost paměti.   

Souběžné počet úloh spuštěných nebo paralelismus bude možné ohraničené celkovou velikost paměti YARN.  Počet kontejnerů YARN se určují, jak velký počet souběžných úloh můžete spustit.  Pokud chcete najít YARN paměti na jeden uzel, můžete přejít na Ambari.  Přejděte do YARN a zobrazit kartu konfigurací.  V tomto okně se zobrazí YARN paměti.  

        Total YARN memory = nodes * YARN memory per node
        # of YARN containers = Total YARN memory / Tez container size
Klíč k zlepšení výkonu pomocí ADLS je zvýšit souběžnost co nejvíc.  Tez automaticky vypočítá počet úloh, které mají být vytvořeny, takže není potřeba ho nastavit.   

## <a name="example-calculation"></a>Příklad výpočtu

Řekněme, že máte cluster D14 8 uzlu.  

    Total YARN memory = nodes * YARN memory per node
    Total YARN memory = 8 nodes * 96GB = 768GB
    # of YARN containers = 768GB / 3072MB = 256

## <a name="limitations"></a>Omezení

**ADLS omezení** 

UIf dosáhl omezení šířky pásma poskytované ADLS, byste začali zobrazíte selhání úkolů. To může být identifikován sledování omezení chyby v protokolech úloh.  Paralelismus může snížit zvýšením velikosti kontejneru Tez.  Pokud potřebujete další souběžnosti pro úlohu, kontaktujte nás.

Pokud chcete zkontrolovat, pokud jste jsou získávání omezené, musíte povolit ladění na straně klienta protokolování. Zde je, jak můžete to udělat:

1. Uveďte následující vlastnost ve vlastnostech log4j v konfiguračním Hive. To lze provést ze zobrazení Ambari: log4j.logger.com.microsoft.azure.datalake.store=DEBUG restartovat všechny uzly nebo služby, konfigurace se projeví.

2. Pokud jste jsou získávání omezené, se zobrazí kód HTTP 429 chyby v souboru protokolu hive. Soubor protokolu hive je v /tmp/&lt;uživatele&gt;/hive.log

## <a name="further-information-on-hive-tuning"></a>Další informace o ladění Hive

Tady je několik blogy, které vám pomůžou optimalizovat dotazy Hive:
* [Optimalizace dotazů Hive pro Hadoop v HDInsight](https://azure.microsoft.com/documentation/articles/hdinsight-hadoop-optimize-hive-query/)
* [Řešení potíží s výkon dotazů Hive](https://blogs.msdn.microsoft.com/bigdatasupport/2015/08/13/troubleshooting-hive-query-performance-in-hdinsight-hadoop-cluster/)
* [Ignite obraťte na optimalizaci Hive v HDInsight](https://channel9.msdn.com/events/Machine-Learning-and-Data-Sciences-Conference/Data-Science-Summit-2016/MSDSS25)
