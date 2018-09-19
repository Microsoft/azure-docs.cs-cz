---
title: Azure Data Lake Storage Gen1 výkonu Hive pokyny k ladění | Dokumentace Microsoftu
description: Azure Data Lake Storage Gen1 výkonu Hive pokyny k ladění
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
ms.openlocfilehash: e9d0ad0398dfc238d48060247cdb6f29b0f34a60
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/18/2018
ms.locfileid: "46123330"
---
# <a name="performance-tuning-guidance-for-hive-on-hdinsight-and-azure-data-lake-storage-gen1"></a>Průvodce laděním výkonu pro Hive v HDInsight a Azure Data Lake Storage Gen1

Výchozí nastavení jsou nastavené pro zajištění dobrého výkonu mezi mnoho použití v odlišných situacích.  Za dotazy na náročné na vstupně-výstupních operací Hive můžete ladit pro získání lepší výkon s Azure Data Lake Storage Gen1.  

## <a name="prerequisites"></a>Požadavky

* **Předplatné Azure**. Viz [Získání bezplatné zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Účet Data Lake Storage Gen1**. Pokyny k jeho vytvoření najdete v tématu [Začínáme s Azure Data Lake Storage Gen1](data-lake-store-get-started-portal.md)
* **Azure HDInsight cluster** s přístupem k účtu Data Lake Storage Gen1. Zobrazit [vytvoření clusteru HDInsight s Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md). Ujistěte se, že se že povolení vzdálené plochy pro cluster.
* **Spuštění Hive v HDInsight**.  Další informace o spouštění úloh Hive v HDInsight, najdete v článku [používání Hive s HDInsight])https://docs.microsoft.com/azure/hdinsight/hdinsight-use-hive)
* **Pokyny pro Data Lake Storage Gen1 pro optimalizaci výkonu**.  Obecné informace o výkonu koncepty, najdete v části [Data Lake Storage Gen1 ladění Průvodce výkonem](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-performance-tuning-guidance)

## <a name="parameters"></a>Parametry

Tady jsou nejdůležitější nastavení a vylaďte za účelem vylepšení výkonu Gen1 úložiště Data Lake:

* **Hive.tez.Container.size** – množství paměti používané každé úlohy

* **velikost tez.Grouping.min** – minimální velikost každého mapper

* **velikost tez.Grouping.Max** – maximální velikost každého mapper

* **Hive.Exec.reducer.bytes.per.reducer** – velikost každého redukční funkci

**Hive.tez.Container.size** – velikost kontejneru Určuje, kolik paměti je k dispozici pro každý úkol.  Toto je hlavní vstup pro řízení souběžnosti v podregistru.  

**velikost tez.Grouping.min** – tento parametr umožňuje nastavit minimální velikost každého mapper.  Pokud počet mapovačů, které vybere Tez je menší než hodnota tohoto parametru, bude používat Tez nastavená hodnota.

**velikost tez.Grouping.Max** – tento parametr umožňuje nastavit maximální velikost každého mapper.  Pokud počet mapovačů, které vybere Tez je větší než hodnota tohoto parametru, bude používat Tez nastavená hodnota.

**Hive.Exec.reducer.bytes.per.reducer** – tento parametr nastavuje velikost každého redukční funkci.  Ve výchozím nastavení je každý redukční funkci 256MB.  

## <a name="guidance"></a>Doprovodné materiály

**Nastavte hive.exec.reducer.bytes.per.reducer** – výchozí hodnota dobře funguje i při nekomprimovaná data.  Pro data, která je komprimován by měla snížit velikost redukční funkci.  

**Nastavte hive.tez.container.size** – v jednotlivých uzlech, je určená yarn.nodemanager.resource.memory mb paměti a by měl být správně nastavená na clusteru Hdinsight ve výchozím nastavení.  Další informace o nastavení odpovídající paměti YARN, najdete v tomto [příspěvku](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-hive-out-of-memory-error-oom).

Úlohy náročné na vstupně-výstupních operací může přinést více paralelismu snížením velikosti kontejneru Tez. To umožňuje uživateli víc kontejnerů, což zvyšuje souběžnosti.  Ale některé dotazy Hive vyžadovat značné množství paměti (například MapJoin).  Pokud úloha nemá dostatek paměti, zobrazí se nedostatku paměti výjimky za běhu.  Pokud se zobrazí mimo výjimky paměti, měli byste zvýšit paměť.   

Číslo souběžné úkoly spuštěné nebo paralelismu bude ohraničené celkové paměti YARN.  Počet kontejnerů YARN bude určovat, kolik souběžných úloh můžete spustit.  K vyhledání paměti YARN podle počtu uzlů, může použít Ambari.  Přejděte na YARN a zobrazit na kartě konfigurace.  V tomto okně se zobrazí paměti YARN.  

        Total YARN memory = nodes * YARN memory per node
        # of YARN containers = Total YARN memory / Tez container size
Klíčem ke zlepšení výkonu pomocí Data Lake Storage Gen1 je zvýšit souběžnost co největší míře.  Tez automaticky vypočítá počet úkolů, které by měly být vytvořeny, takže není potřeba ho nastavit.   

## <a name="example-calculation"></a>Příklad výpočtu

Řekněme, že máte cluster D14 8 uzlu.  

    Total YARN memory = nodes * YARN memory per node
    Total YARN memory = 8 nodes * 96GB = 768GB
    # of YARN containers = 768GB / 3072MB = 256

## <a name="limitations"></a>Omezení

**Data Lake Storage Gen1 omezování** 

Pokud dosáhnete limitů šířky pásma poskytuje Gen1 úložiště Data Lake, byste začali zobrazíte selhání úkolů. To může být označen sledování chyb omezení v protokolech úloh.  Zvýšením velikosti kontejneru Tez můžete zkrátit paralelismu.  Pokud potřebujete větší souběžnost pro vaši úlohu, kontaktujte nás.

Pokud chcete zkontrolovat, pokud jste se získávání omezují, budete muset povolit protokolování na straně klienta ladění. Zde je, jak můžete to udělat:

1. Vložte následující vlastnost log4j vlastnosti konfigurace Hive. To můžete udělat v zobrazení Ambari: log4j.logger.com.microsoft.azure.datalake.store=DEBUG restartujte všechny uzly a službu konfigurace se projeví.

2. Pokud jste se získávání omezují, zobrazí kód HTTP 429 chyby v souboru protokolu hive. Soubor protokolu hive je v /tmp/&lt;uživatele&gt;/hive.log

## <a name="further-information-on-hive-tuning"></a>Další informace o ladění Hive

Tady je pár blogy, které vám usnadní vyladění vašich dotazů Hive:
* [Optimalizace dotazů Hive pro Hadoop v HDInsight](https://azure.microsoft.com/documentation/articles/hdinsight-hadoop-optimize-hive-query/)
* [Řešení potíží s výkonem dotazů Hive](https://blogs.msdn.microsoft.com/bigdatasupport/2015/08/13/troubleshooting-hive-query-performance-in-hdinsight-hadoop-cluster/)
* [Ignite Přednáška v optimalizaci Hive v HDInsight](https://channel9.msdn.com/events/Machine-Learning-and-Data-Sciences-Conference/Data-Science-Summit-2016/MSDSS25)
