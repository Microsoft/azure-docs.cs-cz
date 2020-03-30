---
title: 'Ladění výkonu: MapReduce, HDInsight & Azure Data Lake Storage Gen2 | Dokumenty společnosti Microsoft'
description: Azure Data Lake Storage Gen2 MapReduce Performance Tuning Guidelines
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 11/18/2019
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: a3ea6858355d6cb921f629bf36134d96371f6244
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74327926"
---
# <a name="tune-performance-mapreduce-hdinsight--azure-data-lake-storage-gen2"></a>Výkon ladění: MapReduce, HDInsight & Azure Data Lake Storage Gen2

Seznamte se s faktory, které byste měli zvážit při ladění výkonu úloh Omezení mapování. Tento článek popisuje řadu pokynů pro ladění výkonu.

## <a name="prerequisites"></a>Požadavky

* **Předplatné Azure**. Viz [Získání bezplatné zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Účet Azure Data Lake Storage Gen2**. Pokyny k jeho vytvoření najdete v [tématu Úvodní příručka: Vytvoření účtu úložiště Azure Data Lake Storage Gen2](data-lake-storage-quickstart-create-account.md).
* **Cluster Azure HDInsight** s přístupem k účtu Data Lake Storage Gen2. Viz [Použití Azure Data Lake Storage Gen2 s clustery Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2)
* **Pomocí MapReduce na HDInsight**.  Další informace naleznete v tématu [Use MapReduce in Hadoop on HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-use-mapreduce)
* **Pokyny pro ladění výkonu pro úložiště datových jezer Gen2**.  Obecné koncepty výkonu najdete v [tématu Data Lake Storage Gen2 Performance Tuning Guidance](data-lake-storage-performance-tuning-guidance.md)

## <a name="parameters"></a>Parametry

Při spuštění mapreduce úloh, zde jsou parametry, které můžete nakonfigurovat pro zvýšení výkonu na data lake storage Gen2:

* **Mapreduce.map.memory.mb** – množství paměti, které má být přiděleno každému mapovači
* **Mapreduce.job.maps** – počet mapových úloh na úlohu
* **Mapreduce.reduce.memory.mb** – množství paměti přidělit každému reduktoru
* **Mapreduce.job.reduces** – počet snížení úkolů na úlohu

**Mapreduce.map.memory / Mapreduce.reduce.memory** Toto číslo by mělo být upraveno na základě toho, kolik paměti je potřeba pro mapu a/nebo snížit úkol.  Výchozí hodnoty mapreduce.map.memory a mapreduce.reduce.memory lze zobrazit v Ambari pomocí konfigurace příze.  V Ambari přejděte na YARN a zobrazte kartu Configs.  Zobrazí se paměť YARN.  

**Mapreduce.job.maps / Mapreduce.job.reduces** Tím určíte maximální počet mapovačů nebo reduktorů, které mají být vytvořeny.  Počet rozdělení určí, kolik mapovačů bude vytvořeno pro úlohu MapReduce.  Proto můžete získat méně mapovačů, než jste požadovali, pokud existuje méně rozdělení než požadovaný počet mapovačů.       

## <a name="guidance"></a>Doprovodné materiály

> [!NOTE]
> Pokyny v tomto dokumentu předpokládá, že vaše aplikace je jediná aplikace spuštěná v clusteru.

**Krok 1: Určení počtu spuštěných úloh**

Ve výchozím nastavení mapreduce bude používat celý cluster pro vaši úlohu.  Můžete použít méně clusteru pomocí méně mapovačů, než jsou k dispozici kontejnery.        

**Krok 2: Nastavení mapreduce.map.memory/mapreduce.reduce.memory**

Velikost paměti pro mapu a snížení úkolů bude záviset na konkrétní úloze.  Velikost paměti můžete zmenšit, pokud chcete zvýšit souběžnost.  Počet souběžně spuštěných úloh závisí na počtu kontejnerů.  Snížením množství paměti na mapovač nebo reduktor lze vytvořit více kontejnerů, které umožňují souběžné spuštění více mapovačů nebo reduktorů.  Snížení množství paměti příliš mnoho může způsobit, že některé procesy dojdou paměti.  Pokud se při spuštění úlohy zobrazí chyba haldy, měli byste zvýšit paměť na mapovač nebo reduktor.  Měli byste zvážit, že přidání dalšíkontejnery přidá další režii pro každý další kontejner, což může potenciálně snížit výkon.  Další alternativou je získat více paměti pomocí clusteru, který má vyšší množství paměti nebo zvýšení počtu uzlů v clusteru.  Více paměti umožní více kontejnerů, které mají být použity, což znamená více souběžnosti.  

**Krok 3: Určení celkové paměti příze**

Chcete-li optimalizovat mapreduce.job.maps/mapreduce.job.reduces, měli byste zvážit množství celkové paměti YARN, která je k dispozici pro použití.  Tyto informace jsou k dispozici v Ambari.  Přejděte na YARN a zobrazte kartu Configs.  V tomto okně se zobrazí paměť YARN.  Paměť YARN byste měli vynásobit počtem uzlů v clusteru, abyste získali celkovou paměť YARN.

    Total YARN memory = nodes * YARN memory per node

Pokud používáte prázdný cluster, může být paměť celkovou pamětí YARN pro váš cluster.  Pokud paměť používají jiné aplikace, můžete použít pouze část paměti clusteru snížením počtu mapovačů nebo reduktorů na počet kontejnerů, které chcete použít.  

**Krok 4: Výpočet počtu kontejnerů YARN**

Kontejnery YARN určují množství souběžnosti, které jsou k dispozici pro úlohu.  Vezměte celkovou paměť YARN a vydělte ji mapreduce.map.memory.  

    # of YARN containers = total YARN memory / mapreduce.map.memory

**Krok 5: Nastavení mapreduce.job.maps/mapreduce.job.reduces**

Nastavte mapreduce.job.maps/mapreduce.job.reduzuje alespoň počet dostupných kontejnerů.  Můžete dále experimentovat zvýšením počtu mapovačů a reduktorů, abyste zjistili, zda získáte lepší výkon.  Mějte na paměti, že další mapovače budou mít další režii, takže příliš mnoho mapovačů může snížit výkon.  

Plánování procesoru a izolace procesoru jsou ve výchozím nastavení vypnuty, takže počet kontejnerů YARN je omezen pamětí.

## <a name="example-calculation"></a>Příklad výpočtu

Předpokládejme, že máme cluster složený z uzlů 8 D14 a chceme spustit úlohu intenzivní vstupně-operace.  Zde jsou výpočty, které byste měli udělat:

**Krok 1: Určení počtu spuštěných úloh**

V tomto příkladu předpokládejme, že naše úloha je jediná úloha, která je spuštěna.  

**Krok 2: Nastavení mapreduce.map.memory/mapreduce.reduce.memory**

V tomto příkladu spouštějíme vstupně-v., kterou intenzivně provádíme, a rozhodneme, že bude stačit 3 GB paměti pro úlohy mapy.

    mapreduce.map.memory = 3GB

**Krok 3: Určení celkové paměti příze**

    Total memory from the cluster is 8 nodes * 96GB of YARN memory for a D14 = 768GB
**Krok 4: Výpočet počtu kontejnerů YARN**

    # of YARN containers = 768GB of available memory / 3 GB of memory =   256

**Krok 5: Nastavení mapreduce.job.maps/mapreduce.job.reduces**

    mapreduce.map.jobs = 256

## <a name="examples-to-run"></a>Příklady spuštění

Chcete-li ukázat, jak MapReduce běží na Data Lake Storage Gen2, níže je nějaký ukázkový kód, který byl spuštěn v clusteru s následujícím nastavením:

* 16 uzlů D14v2
* Cluster Hadoop se systémem HDI 3.6

Pro výchozí bod zde jsou některé příklady příkazů pro spuštění MapReduce Teragen, Terasort a Teravalidate.  Tyto příkazy můžete upravit na základě vašich prostředků.

**Teragen (Teragen)**

    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar teragen -Dmapreduce.job.maps=2048 -Dmapreduce.map.memory.mb=3072 10000000000 abfs://example/data/1TB-sort-input

**Terasort (Terasort)**

    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar terasort -Dmapreduce.job.maps=2048 -Dmapreduce.map.memory.mb=3072 -Dmapreduce.job.reduces=512 -Dmapreduce.reduce.memory.mb=3072 abfs://example/data/1TB-sort-input abfs://example/data/1TB-sort-output

**Teravalidate**

    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar teravalidate -Dmapreduce.job.maps=512 -Dmapreduce.map.memory.mb=3072 abfs://example/data/1TB-sort-output abfs://example/data/1TB-sort-validate
