---
title: Azure Data Lake Storage Gen1 MapReduce výkonu pokyny k ladění | Dokumentace Microsoftu
description: Azure Data Lake Storage Gen1 MapReduce výkonu pokyny k ladění
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
ms.openlocfilehash: b661499786057a3083f79684dfd12c85266b7b5c
ms.sourcegitcommit: f10653b10c2ad745f446b54a31664b7d9f9253fe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/18/2018
ms.locfileid: "46128787"
---
# <a name="performance-tuning-guidance-for-mapreduce-on-hdinsight-and-azure-data-lake-storage-gen1"></a>Průvodce laděním výkonu pro MapReduce na HDInsight a Azure Data Lake Storage Gen1

## <a name="prerequisites"></a>Požadavky

* **Předplatné Azure**. Viz [Získání bezplatné zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Účet Azure Data Lake Storage Gen1**. Pokyny k jeho vytvoření najdete v tématu [Začínáme s Azure Data Lake Storage Gen1](data-lake-store-get-started-portal.md)
* **Azure HDInsight cluster** s přístupem k účtu Data Lake Storage Gen1. Zobrazit [vytvoření clusteru HDInsight s Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md). Ujistěte se, že se že povolení vzdálené plochy pro cluster.
* **Použití prostředí MapReduce na HDInsight**.  Další informace najdete v tématu [použití MapReduce se v clusteru Hadoop v HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-use-mapreduce)
* **Pokyny pro Data Lake Storage Gen1 pro optimalizaci výkonu**.  Obecné informace o výkonu koncepty, najdete v části [Data Lake Storage Gen1 ladění Průvodce výkonem](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-performance-tuning-guidance)

## <a name="parameters"></a>Parametry

Při spuštění úlohy mapreduce je možné, jsou zde nejdůležitějších parametrů, jejichž konfigurací můžete zvýšit výkon v Data Lake Storage Gen1:

* **Mapreduce.map.Memory.MB** – množství paměti pro každé mapování
* **Mapreduce.job.Maps** – počet úkolů mapování na úlohu
* **Mapreduce.reduce.Memory.MB** – množství paměti pro každou redukční funkci
* **Mapreduce.job.reduces** – počet úkolů zmenšit na úlohu

**Mapreduce.map.Memory / Mapreduce.reduce.memory** toto číslo by mělo být upraveno v závislosti na tom, kolik paměti je potřeba pro mapy a/nebo snižte úloh.  Výchozí hodnoty mapreduce.map.memory a mapreduce.reduce.memory lze zobrazit v Ambari prostřednictvím konfigurace Yarn.  V Ambari přejděte na YARN a zobrazit na kartě konfigurace.  Zobrazí se paměti YARN.  

**Mapreduce.job.Maps / Mapreduce.job.reduces** tak bude určit maximální počet mapovačů a reduktorů vytvořit.  Počet rozdělí určí, kolik mapovačů se vytvoří pro úlohu MapReduce.  Proto může získat méně mapovačů, než je požadováno, pokud jsou méně rozdělí, než je počet elementů mapovačů požadovaný.       

## <a name="guidance"></a>Doprovodné materiály

**Krok 1: Určení počtu úloh spuštěných** -MapReduce ve výchozím nastavení, bude používat celý cluster pro vaši úlohu.  Můžete použít menší clusteru s použitím méně mapovačů, než je k dispozici kontejnerů.  Pokyny v tomto dokumentu předpokládá, že vaše aplikace je pouze aplikace běžící v clusteru.      

**Krok 2: Nastavení mapreduce.map.memory/mapreduce.reduce.memory** – velikost paměti pro mapy a snížit úkoly budou závislé na konkrétní úlohy.  Pokud chcete zvýšit souběžnost, můžete zmenšit velikost paměti.  Počet současně spuštěných úloh závisí na počtu kontejnerů.  Snížením množství paměti za mapovací a redukční funkci víc kontejnerů mohou být vytvořeny, které povolují více mapovačů a reduktorů jak souběžně spustit.  Příliš mnoho snížení množství paměti může způsobit, že některé procesy mít nedostatek paměti.  Pokud při spuštění vaší úlohy se zobrazí chyba haldy, měli byste zvýšit paměti za mapovací a redukční funkci.  Měli byste zvážit, že přidáte víc kontejnerů přidá další režie pro každý další kontejner, který může potenciálně dojít ke snížení výkonu.  Další možností je načíst větší množství paměti pomocí funkce clusteru, který má větší objem paměti a zvýšením počtu uzlů ve vašem clusteru.  Větší množství paměti umožní víc kontejnerů mají být použity, což znamená, že větší souběžnost.  

**Krok 3: Určení celkového YARN paměti** – Pokud chcete ladit mapreduce.job.maps/mapreduce.job.reduces, měli byste zvážit množství celkové paměti YARN k dispozici pro použití.  Tyto informace jsou k dispozici v Ambari.  Přejděte na YARN a zobrazit na kartě konfigurace.  V tomto okně se zobrazí paměti YARN.  By měl vynásobit paměti YARN s počtem uzlů v clusteru a k získání celkové paměti YARN.

    Total YARN memory = nodes * YARN memory per node
Pokud používáte cluster prázdný, paměť, může být celkové paměti YARN pro váš cluster.  Pokud jiné aplikace používají paměti, je možné použít pouze část paměti vašeho clusteru snížením počtu mapovačů a reduktorů počtu kontejnerů, které chcete použít.  

**Krok 4: Výpočet počet kontejnery YARN** – kontejnery YARN diktovat množství souběžnosti, které jsou k dispozici pro konkrétní úlohu.  Využijte celkové paměti YARN a dělení, který mapreduce.map.memory.  

    # of YARN containers = total YARN memory / mapreduce.map.memory

**Krok 5: Nastavení mapreduce.job.maps/mapreduce.job.reduces** nastavit mapreduce.job.maps/mapreduce.job.reduces alespoň na počet dostupných kontejnerů.  Můžete experimentovat další zvýšením počtu mapovačů a reduktorů zobrazíte, pokud dosahovat vyšších výkonů.  Uvědomte si, že více mapovačů bude mít další režie, takže máte příliš mnoho mapovačů může snížit výkon.  

Procesor plánování a izolaci procesoru jsou ve výchozím nastavení vypnuta, paměti, takže je omezen počet kontejnery YARN.

## <a name="example-calculation"></a>Příklad výpočtu

Řekněme, že máte aktuálně cluster skládá z 8 D14 uzly a vy chcete spuštění úlohy náročné na vstupně-výstupních operací.  Tady jsou výpočty, které byste měli dělat:

**Krok 1: Určení počtu úloh spuštěných** – v našem příkladu předpokládáme, že naše úlohy je jen jeden spuštěný.  

**Krok 2: Nastavení mapreduce.map.memory/mapreduce.reduce.memory** – v našem příkladu jsou spuštěny úlohy náročné na vstupně-výstupní operace a rozhodnout, že bude stačit 3 GB paměti pro mapování úlohy.

    mapreduce.map.memory = 3GB
**Krok 3: Určení celkového YARN paměti**

    total memory from the cluster is 8 nodes * 96GB of YARN memory for a D14 = 768GB
**Krok 4: Výpočet # kontejnery YARN**

    # of YARN containers = 768GB of available memory / 3 GB of memory =   256

**Krok 5: Nastavení mapreduce.job.maps/mapreduce.job.reduces**

    mapreduce.map.jobs = 256

## <a name="limitations"></a>Omezení

**Data Lake Storage Gen1 omezování**

Jako víceklientská služba Data Lake Storage Gen1 nastaví omezení šířky pásma úrovně účtu.  Pokud tato omezení dosáhnete, začnete naleznete v tématu selhání úkolů. Tím lze identifikovat podle sledování chyb omezení v protokolech úloh.  Pokud potřebujete větší šířku pásma pro vaši úlohu, kontaktujte nás.   

Pokud chcete zkontrolovat, pokud jste se získávání omezují, budete muset povolit protokolování na straně klienta ladění. Zde je, jak můžete to udělat:

1. Vložte následující vlastnost ve vlastnostech log4j v Ambari > YARN > Konfigurace > Upřesnit yarn log4j: log4j.logger.com.microsoft.azure.datalake.store=DEBUG

2. Restartujte všechny uzly a službu konfigurace se projeví.

3. Pokud jste se získávání omezují, zobrazí kód HTTP 429 chyby v souboru protokolu YARN. Protokol YARN je v /tmp/&lt;uživatele&gt;/yarn.log

## <a name="examples-to-run"></a>Příklady pro spuštění

Abychom si předvedli, jak běží MapReduce v Data Lake Storage Gen1, tady je ukázkový kód, který byl spuštěn na cluster s následujícími nastaveními:

* 16 uzlů D14v2
* Cluster Hadoop s HDI 3.6

Výchozí bod tady jsou některé příklady příkazů spustit MapReduce Teragen Terasort a Teravalidate.  Můžete upravit tyto příkazy na základě vašich prostředků.

**Teragen**

    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar teragen -Dmapreduce.job.maps=2048 -Dmapreduce.map.memory.mb=3072 10000000000 adl://example/data/1TB-sort-input

**Terasort**

    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar terasort -Dmapreduce.job.maps=2048 -Dmapreduce.map.memory.mb=3072 -Dmapreduce.job.reduces=512 -Dmapreduce.reduce.memory.mb=3072 adl://example/data/1TB-sort-input adl://example/data/1TB-sort-output

**Teravalidate**

    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar teravalidate -Dmapreduce.job.maps=512 -Dmapreduce.map.memory.mb=3072 adl://example/data/1TB-sort-output adl://example/data/1TB-sort-validate
