---
title: Azure Data Lake Storage Gen2 MapReduce výkonu pokyny k ladění | Dokumentace Microsoftu
description: Azure Data Lake Storage Gen2 MapReduce výkonu pokyny k ladění
services: storage
author: swums
ms.subservice: data-lake-storage-gen2
ms.service: storage
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: stewu
ms.openlocfilehash: 1df7765f4f006d3d79f867fbf23929f6b9b0cf08
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55884388"
---
# <a name="performance-tuning-guidance-for-mapreduce-on-hdinsight-and-azure-data-lake-storage-gen2"></a>Průvodce laděním výkonu pro MapReduce na HDInsight a Azure Data Lake Storage Gen2

Seznamte se s faktory, které byste měli zvážit při optimalizaci výkonu Map Reduce úlohy. Tento článek popisuje celou řadu pokyny pro optimalizaci výkonu.

## <a name="prerequisites"></a>Požadavky

* **Předplatné Azure**. Viz [Získání bezplatné zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Účet Azure Data Lake Storage Gen2**. Pokyny k jeho vytvoření najdete v tématu [rychlý start: Vytvoření účtu úložiště Azure Data Lake Storage Gen2](data-lake-storage-quickstart-create-account.md).
* **Azure HDInsight cluster** s přístupem k účtu Data Lake Storage Gen2. Zobrazit [clusterů pomocí Azure Data Lake Storage Gen2 s Azure HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-hadoop-use-data-lake-storage-gen2)
* **Použití prostředí MapReduce na HDInsight**.  Další informace najdete v tématu [použití MapReduce se v clusteru Hadoop v HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-use-mapreduce)
* **Pokyny pro Data Lake Storage Gen2 pro optimalizaci výkonu**.  Obecné informace o výkonu koncepty, najdete v části [Data Lake Storage Gen2 ladění Průvodce výkonem](data-lake-storage-performance-tuning-guidance.md)

## <a name="parameters"></a>Parametry

Při spuštění úlohy mapreduce je možné, tady jsou parametry, jejichž konfigurací můžete zvýšit výkon při Gen2 úložiště Data Lake:

* **Mapreduce.map.Memory.MB** – množství paměti pro každé mapování
* **Mapreduce.job.Maps** – počet úkolů mapování na úlohu
* **Mapreduce.reduce.Memory.MB** – množství paměti pro každou redukční funkci
* **Mapreduce.job.reduces** – počet úkolů zmenšit na úlohu

**Mapreduce.map.Memory / Mapreduce.reduce.memory** toto číslo by mělo být upraveno v závislosti na tom, kolik paměti je potřeba pro mapy a/nebo snižte úloh.  Výchozí hodnoty mapreduce.map.memory a mapreduce.reduce.memory lze zobrazit v Ambari prostřednictvím konfigurace Yarn.  V Ambari přejděte na YARN a zobrazit na kartě konfigurace.  Zobrazí se paměti YARN.  

**Mapreduce.job.Maps / Mapreduce.job.reduces** tak bude určit maximální počet mapovačů a reduktorů vytvořit.  Počet rozdělí určí, kolik mapovačů se vytvoří pro úlohu MapReduce.  Proto může získat méně mapovačů, než je požadováno, pokud jsou méně rozdělí, než je počet elementů mapovačů požadovaný.       

## <a name="guidance"></a>Doprovodné materiály

> [!NOTE]
> Pokyny v tomto dokumentu předpokládá, že vaše aplikace je pouze aplikace běžící v clusteru.

**Krok 1: Určení počtu spuštěné úlohy**

Ve výchozím nastavení použije MapReduce celý cluster pro vaši úlohu.  Můžete použít menší clusteru s použitím méně mapovačů, než je k dispozici kontejnerů.        

**Krok 2: Nastavte mapreduce.map.memory/mapreduce.reduce.memory**

Velikost paměti pro mapy a snížit úkoly budou závislé na konkrétní úlohy.  Pokud chcete zvýšit souběžnost, můžete zmenšit velikost paměti.  Počet současně spuštěných úloh závisí na počtu kontejnerů.  Snížením množství paměti za mapovací a redukční funkci víc kontejnerů mohou být vytvořeny, které povolují více mapovačů a reduktorů jak souběžně spustit.  Příliš mnoho snížení množství paměti může způsobit, že některé procesy mít nedostatek paměti.  Pokud při spuštění vaší úlohy se zobrazí chyba haldy, měli byste zvýšit paměti za mapovací a redukční funkci.  Měli byste zvážit, že přidáte víc kontejnerů přidá další režie pro každý další kontejner, který může potenciálně dojít ke snížení výkonu.  Další možností je načíst větší množství paměti pomocí funkce clusteru, který má větší objem paměti a zvýšením počtu uzlů ve vašem clusteru.  Větší množství paměti umožní víc kontejnerů mají být použity, což znamená, že větší souběžnost.  

**Krok 3: Určení celkového YARN paměti**

K vyladění mapreduce.job.maps/mapreduce.job.reduces, měli byste zvážit množství celkové paměti YARN k dispozici pro použití.  Tyto informace jsou k dispozici v Ambari.  Přejděte na YARN a zobrazit na kartě konfigurace.  V tomto okně se zobrazí paměti YARN.  By měl vynásobit paměti YARN s počtem uzlů v clusteru a k získání celkové paměti YARN.

    Total YARN memory = nodes * YARN memory per node

Pokud používáte cluster prázdný, paměť, může být celkové paměti YARN pro váš cluster.  Pokud jiné aplikace používají paměti, je možné použít pouze část paměti vašeho clusteru snížením počtu mapovačů a reduktorů počtu kontejnerů, které chcete použít.  

**Krok 4: Vypočítat počet kontejnery YARN**

Kontejnery YARN diktovat množství souběžnosti, které jsou k dispozici pro konkrétní úlohu.  Využijte celkové paměti YARN a dělení, který mapreduce.map.memory.  

    # of YARN containers = total YARN memory / mapreduce.map.memory

**Krok 5: Nastavte mapreduce.job.maps/mapreduce.job.reduces**

Nastavte mapreduce.job.maps/mapreduce.job.reduces alespoň na počet dostupných kontejnerů.  Můžete experimentovat další zvýšením počtu mapovačů a reduktorů zobrazíte, pokud dosahovat vyšších výkonů.  Uvědomte si, že více mapovačů bude mít další režie, takže máte příliš mnoho mapovačů může snížit výkon.  

Procesor plánování a izolaci procesoru jsou ve výchozím nastavení vypnuta, paměti, takže je omezen počet kontejnery YARN.

## <a name="example-calculation"></a>Příklad výpočtu

Předpokládejme, že máme skládá z 8 D14 uzly clusteru, a chceme se spuštění úlohy náročné na vstupně-výstupních operací.  Tady jsou výpočty, které byste měli dělat:

**Krok 1: Určení počtu spuštěné úlohy**

V tomto příkladu předpokládejme, že naším úkolem je jenom úlohy, na kterém běží.  

**Krok 2: Nastavte mapreduce.map.memory/mapreduce.reduce.memory**

V tomto příkladu jsme jsou spuštěny úlohy náročné na vstupně-výstupní operace a rozhodnout, že bude stačit 3GB paměti pro mapování úlohy.

    mapreduce.map.memory = 3GB

**Krok 3: Určení celkového YARN paměti**

    Total memory from the cluster is 8 nodes * 96GB of YARN memory for a D14 = 768GB
**Krok 4: Vypočítat počet kontejnery YARN**

    # of YARN containers = 768GB of available memory / 3 GB of memory =   256

**Krok 5: Nastavte mapreduce.job.maps/mapreduce.job.reduces**

    mapreduce.map.jobs = 256

## <a name="examples-to-run"></a>Příklady pro spuštění

Abychom si předvedli, jak běží MapReduce v Data Lake Storage Gen2, tady je ukázkový kód, který byl spuštěn na cluster s následujícími nastaveními:

* 16 uzlů D14v2
* Cluster Hadoop s HDI 3.6

Výchozí bod tady jsou některé příklady příkazů spustit MapReduce Teragen Terasort a Teravalidate.  Můžete upravit tyto příkazy na základě vašich prostředků.

**Teragen**

    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar teragen -Dmapreduce.job.maps=2048 -Dmapreduce.map.memory.mb=3072 10000000000 abfs://example/data/1TB-sort-input

**Terasort**

    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar terasort -Dmapreduce.job.maps=2048 -Dmapreduce.map.memory.mb=3072 -Dmapreduce.job.reduces=512 -Dmapreduce.reduce.memory.mb=3072 abfs://example/data/1TB-sort-input abfs://example/data/1TB-sort-output

**Teravalidate**

    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar teravalidate -Dmapreduce.job.maps=512 -Dmapreduce.map.memory.mb=3072 abfs://example/data/1TB-sort-output abfs://example/data/1TB-sort-validate
