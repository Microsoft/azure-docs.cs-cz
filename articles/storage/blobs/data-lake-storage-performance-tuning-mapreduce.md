---
title: 'Ladění výkonu: MapReduce, HDInsight & Azure Data Lake Storage Gen2 | Microsoft Docs'
description: Seznamte se s pokyny pro optimalizaci výkonu mapy zmenšování úloh na Azure Data Lake Storage Gen2.
author: normesta
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: how-to
ms.date: 11/18/2019
ms.author: normesta
ms.reviewer: stewu
ms.openlocfilehash: b95d37e1725940799750dbd3c29174d9855390d6
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "95912922"
---
# <a name="tune-performance-mapreduce-hdinsight--azure-data-lake-storage-gen2"></a>Ladění výkonu: MapReduce, HDInsight & Azure Data Lake Storage Gen2

Seznamte se s faktory, které byste měli zvážit při ladění výkonu mapy omezení úloh. Tento článek popisuje řadu pokynů pro ladění výkonu.

## <a name="prerequisites"></a>Požadavky

* **Předplatné Azure**. Viz [Získání bezplatné zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Účet Azure Data Lake Storage Gen2**. Pokyny, jak ho vytvořit, najdete v tématu [rychlý Start: vytvoření účtu úložiště Azure Data Lake Storage Gen2](../common/storage-account-create.md).
* **Cluster Azure HDInsight** s přístupem k účtu Data Lake Storage Gen2. Viz [použití Azure Data Lake Storage Gen2 s clustery Azure HDInsight](../../hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2.md) .
* **Použití MapReduce ve službě HDInsight**.  Další informace najdete v tématu [použití MapReduce v Hadoop ve službě HDInsight](../../hdinsight/hadoop/hdinsight-use-mapreduce.md) .
* **Pokyny k ladění výkonu na data Lake Storage Gen2**.  Obecné koncepty výkonu najdete v tématu [Data Lake Storage Gen2 pokyny k ladění výkonu](data-lake-storage-performance-tuning-guidance.md) .

## <a name="parameters"></a>Parametry

Když spouštíte úlohy MapReduce, tady jsou parametry, které můžete nakonfigurovat, aby se zvýšil výkon Data Lake Storage Gen2:

* **MapReduce. map. Memory. MB** – množství paměti, které se má přidělit každému mapovači
* **MapReduce. job. Maps** – počet úloh mapy na úlohu
* **MapReduce. zmenšení. Memory. MB** – množství paměti, které se má přidělit každému snížení
* **MapReduce. job. redukujes** – počet úloh s omezením na úlohu

**MapReduce. map. Memory/MapReduce. zmenšení. Memory** Toto číslo by mělo být upraveno na základě toho, kolik paměti je potřeba pro mapu nebo zmenšení úlohy.  Výchozí hodnoty MapReduce. map. Memory a MapReduce. zmenšení. paměť lze zobrazit v Ambari prostřednictvím konfigurace příze.  V Ambari přejděte do PŘÍZe a zobrazte kartu konfigurace.  Zobrazí se paměť PŘÍZe.  

**MapReduce. job. map/MapReduce. job. redukuje** Tím se určí maximální počet mapovačů nebo reduktorůů, které se mají vytvořit.  Počet rozdělení určuje, kolik mapovačů se vytvoří pro úlohu MapReduce.  Proto můžete získat méně mapovačů, než kolik požadujete, pokud je méně rozděleno, než je počet požadovaných mapovačů.       

## <a name="guidance"></a>Pokyny

> [!NOTE]
> Pokyny v tomto dokumentu předpokládají, že vaše aplikace je jediná aplikace spuštěná v clusteru.

**Krok 1: určení počtu spuštěných úloh**

Ve výchozím nastavení bude MapReduce používat celý cluster pro vaši úlohu.  Méně než tento cluster můžete použít k menšímu počtu mapovačů, než je dostupných kontejnerů.        

**Krok 2: nastavte MapReduce. map. Memory/MapReduce. zmenšení. Memory**

Velikost paměti pro mapu a omezení úloh bude závislá na konkrétní úloze.  Velikost paměti můžete snížit, pokud chcete zvýšit souběžnost.  Počet souběžně spuštěných úloh závisí na počtu kontejnerů.  Tím, že se zmenší množství paměti na jedno nebo více kontejnerů, je možné vytvořit více kontejnerů, které umožní souběžně běžet více mapovačů nebo reduktorů.  Zmenšení množství paměti může způsobit nedostatek paměti z některých procesů.  Pokud při spuštění úlohy dojde k chybě haldy, měli byste zvětšit paměť na Mapovač nebo na zmenšení.  Měli byste zvážit, že přidání dalších kontejnerů přidá další režii pro každý další kontejner, což může způsobit snížení výkonu.  Další možností je získat více paměti pomocí clusteru s větším množstvím paměti nebo zvýšením počtu uzlů v clusteru.  Více paměti umožní použít více kontejnerů, což znamená více souběžnosti.  

**Krok 3: určení celkové paměti PŘÍZe**

Pro účely ladění MapReduce. job. map/MapReduce. job. redukuje byste měli zvážit množství celkové dostupné paměti PŘÍZe k použití.  Tyto informace jsou k dispozici v Ambari.  Přejděte na PŘÍZe a zobrazte kartu konfigurace.  V tomto okně se zobrazí paměť PŘÍZe.  Paměť PŘÍZe byste měli vynásobit počtem uzlů v clusteru, aby se získala celková paměť PŘÍZe.

Celková paměť PŘÍZ = uzly * PŘÍZe paměť na uzel

Pokud používáte prázdný cluster, může být paměť celkovou paměti PŘÍZí pro váš cluster.  Pokud jiné aplikace používají paměť, můžete zvolit, že se má použít jenom část paměti clusteru, a to snížením počtu mapovačů nebo reduktorů počtu kontejnerů, které chcete použít.  

**Krok 4: výpočet počtu kontejnerů PŘÍZe**

Kontejnery PŘÍZe určují množství souběžnosti, které je k dispozici pro úlohu.  Využijte celkovou paměť PŘÍZe a rozdělte ji pomocí MapReduce. map. Memory.  

\# kontejnery PŘÍZ = celková paměť PŘÍZe/MapReduce. map. Memory

**Krok 5: nastavte MapReduce. job. Maps/MapReduce. job. redukujes**

Nastavte MapReduce. job. Maps/MapReduce. job. zmenšuje alespoň na počet dostupných kontejnerů.  Můžete ještě experimentovat zvýšením počtu mapovačů a reduktorů, abyste viděli, jestli získáte lepší výkon.  Mějte na paměti, že další mapovače budou mít další režii, takže příliš mnoho mapovačů může snížit výkon.  

Plánování procesoru a izolace procesoru jsou ve výchozím nastavení vypnuté, takže počet kontejnerů PŘÍZ je omezený pamětí.

## <a name="example-calculation"></a>Příklad výpočtu

Předpokládejme, že máme cluster tvořený 8 D14 uzly a chceme spustit úlohu náročné na vstupně-výstupní operace.  Tady jsou výpočty, které byste měli provést:

**Krok 1: určení počtu spuštěných úloh**

V tomto příkladu předpokládáme, že naše úloha je jedinou úlohou, která je spuštěná.  

**Krok 2: nastavte MapReduce. map. Memory/MapReduce. zmenšení. Memory**

V tomto příkladu je spuštěná úloha náročné na vstupně-výstupní operace a rozhodnete, že povolenou paměti pro úlohy mapy budou dostačující.

MapReduce. map. Memory = povolenou

**Krok 3: určení celkové paměti PŘÍZe**

Celková paměť z clusteru je 8 uzlů * 96GB paměti PŘÍZe D14 = 768GB

**Krok 4: výpočet počtu kontejnerů PŘÍZe**

\# kontejnerů PŘÍZ = 768GB dostupné paměti/3 GB paměti = 256

**Krok 5: nastavte MapReduce. job. Maps/MapReduce. job. redukujes**

mapreduce.map.jobs = 256

## <a name="examples-to-run"></a>Příklady spuštění

Ukázka, jak MapReduce běží na Data Lake Storage Gen2 níže je ukázkový kód, který byl spuštěn v clusteru s následujícím nastavením:

* D14v2 se 16 uzly
* Cluster Hadoop se systémem HDI 3,6

Tady je několik ukázkových příkazů pro výchozí bod, které spouští MapReduce Teragen, Terasort a Teravalidate.  Tyto příkazy můžete upravit v závislosti na svých prostředcích.

**Teragen**

```cmd
yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar teragen -Dmapreduce.job.maps=2048 -Dmapreduce.map.memory.mb=3072 10000000000 abfs://example/data/1TB-sort-input
```

**Terasort**

```cmd
yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar terasort -Dmapreduce.job.maps=2048 -Dmapreduce.map.memory.mb=3072 -Dmapreduce.job.reduces=512 -Dmapreduce.reduce.memory.mb=3072 abfs://example/data/1TB-sort-input abfs://example/data/1TB-sort-output
```

**Teravalidate**

```cmd
yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar teravalidate -Dmapreduce.job.maps=512 -Dmapreduce.map.memory.mb=3072 abfs://example/data/1TB-sort-output abfs://example/data/1TB-sort-validate
```