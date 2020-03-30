---
title: Plánování kapacity clusteru v Azure HDInsight
description: Identifikujte klíčové otázky pro plánování kapacity a výkonu clusteru Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 10/15/2019
ms.openlocfilehash: 69627c961d9224a124fda09f40901f837d627281
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79272640"
---
# <a name="capacity-planning-for-hdinsight-clusters"></a>Plánování kapacity pro clustery HDInsight

Před nasazením clusteru HDInsight naplánujte požadovanou kapacitu clusteru určením potřebného výkonu a škálování. Toto plánování pomáhá optimalizovat použitelnost i náklady. Některá rozhodnutí o kapacitě clusteru nelze po nasazení změnit. Pokud se změní parametry výkonu, cluster lze demontovat a znovu vytvořit bez ztráty uložených dat.

Klíčové otázky, které je třeba položit pro plánování kapacity, jsou:

* Ve které geografické oblasti byste měli nasadit cluster?
* Kolik úložného prostoru potřebujete?
* Jaký typ clusteru byste měli nasadit?
* Jakou velikost a typ virtuálního počítače (VM) by měl y otomek clusteru používat?
* Kolik pracovních uzlů by měl mít cluster?

## <a name="choose-an-azure-region"></a>Výběr oblasti Azure

Oblast Azure určuje, kde je váš cluster fyzicky zřízena. Chcete-li minimalizovat latenci čtení a zápisů, cluster by měl být v blízkosti dat.

HDInsight je dostupný v mnoha oblastech Azure. Chcete-li najít nejbližší oblast, naleznete [v tématu Produkty dostupné podle oblasti](https://azure.microsoft.com/global-infrastructure/services/?products=hdinsight).

## <a name="choose-storage-location-and-size"></a>Zvolte umístění a velikost úložiště

### <a name="location-of-default-storage"></a>Umístění výchozího úložiště

Výchozí úložiště, buď účet Azure Storage nebo Azure Data Lake Storage, musí být ve stejném umístění jako váš cluster. Azure Storage je k dispozici na všech místech. V některých oblastech je k dispozici úložiště datových jezer Gen1 – podívejte se na aktuální [dostupnost úložiště datových jezer](https://azure.microsoft.com/global-infrastructure/services/?products=storage).

### <a name="location-of-existing-data"></a>Umístění stávajících údajů

Pokud už máte účet úložiště nebo úložiště datového jezera obsahující vaše data a chcete toto úložiště použít jako výchozí úložiště clusteru, musíte cluster nasadit ve stejném umístění.

### <a name="storage-size"></a>Velikost úložiště

Po nasazení clusteru HDInsight můžete připojit další účty Azure Storage nebo získat přístup k jinému úložišti Data Lake Storage. Všechny účty úložiště musí být umístěny ve stejném umístění jako cluster. Úložiště datového jezera může být v jiném umístění, i když to může zavést některé latence čtení a zápisu dat.

Azure Storage má určitá [omezení kapacity](../azure-resource-manager/management/azure-subscription-service-limits.md#storage-limits), zatímco Data Lake Storage Gen1 je prakticky neomezená.

Cluster má přístup ke kombinaci různých účtů úložiště. Typickými příklady jsou:

* Pokud je pravděpodobné, že množství dat překročí kapacitu úložiště jednoho kontejneru úložiště objektů blob.
* Pokud rychlost přístupu k kontejneru objektů blob může překročit prahovou hodnotu, kde dochází k omezení.
* Pokud chcete zpřístupnit data, už jste pro cluster nahráli do kontejneru objektů blob.
* Pokud chcete izolovat různé části úložiště z důvodu zabezpečení nebo zjednodušit správu.

Pro lepší výkon použijte pouze jeden kontejner na účet úložiště.

## <a name="choose-a-cluster-type"></a>Volba typu clusteru

Typ clusteru určuje úlohu, kterou je cluster HDInsight nakonfigurován tak, aby se spouštěl, například [Apache Hadoop](https://hadoop.apache.org/), [Apache Storm](https://storm.apache.org/), [Apache Kafka](https://kafka.apache.org/)nebo [Apache Spark](https://spark.apache.org/). Podrobný popis dostupných typů clusterů najdete [v tématu Úvod do Azure HDInsight](hdinsight-overview.md#cluster-types-in-hdinsight). Každý typ clusteru má specifickou topologii nasazení, která zahrnuje požadavky na velikost a počet uzlů.

## <a name="choose-the-vm-size-and-type"></a>Volba velikosti a typu virtuálního počítače

Každý typ clusteru má sadu typů uzlů a každý typ uzlu má specifické možnosti pro jejich velikost a typ virtuálního počítače.

Chcete-li určit optimální velikost clusteru pro vaši aplikaci, můžete srovnávací kapacitu clusteru a zvětšit velikost, jak je uvedeno. Můžete například použít simulované zatížení nebo *kanárský dotaz*. Se simulovaným zatížením spustíte očekávané úlohy v clusterech různých velikostí a postupně se zvětšujete, dokud nebude dosaženo požadovaného výkonu. Kanárský dotaz lze pravidelně vkládat mezi ostatní produkční dotazy, aby se zjistilo, zda má cluster dostatek prostředků.

Další informace o tom, jak vybrat správnou rodinu virtuálních počítačů pro vaše úlohy, najdete [v tématu výběr správné velikosti virtuálního počítače pro váš cluster](hdinsight-selecting-vm-size.md).

## <a name="choose-the-cluster-scale"></a>Volba měřítka clusteru

Měřítko clusteru je určeno množstvím uzlů virtuálních virtuálních mk. Pro všechny typy clusterů existují typy uzlů, které mají určité škálování a typy uzlů, které podporují horizontální navýšení kapacity. Cluster může například vyžadovat přesně tři uzly [Apache ZooKeeper](https://zookeeper.apache.org/) nebo dva uzly Head. Pracovní uzly, které provádějí zpracování dat distribuovaným způsobem, mohou mít prospěch z horizontálního navýšení kapacity přidáním dalších pracovních uzlů.

V závislosti na typu clusteru zvýšení počtu pracovních uzlů přidá další výpočetní kapacitu (například více jader), ale může také přidat k celkovému množství paměti potřebné pro celý cluster pro podporu ukládání zpracovávaných dat v paměti. Stejně jako u volby velikosti a typu virtuálního počítače je výběr správného škálování clusteru obvykle dosaženo empiricky pomocí simulovaných úloh nebo kanárských dotazů.

Můžete horizontální navýšení kapacity clusteru tak, aby splňovaly požadavky na zatížení ve špičce, a pak škálovat zpět dolů, když tyto další uzly již nejsou potřeba. [Funkce Automatické škálování](hdinsight-autoscale-clusters.md) umožňuje automaticky škálovat cluster na základě předem určených metrik a časování. Další informace o ručním škálování clusterů naleznete v [tématu Škálování clusterů HDInsight](hdinsight-scaling-best-practices.md).

### <a name="cluster-lifecycle"></a>Životní cyklus clusteru

Účtuje se vám za život clusteru. Pokud jsou pouze konkrétní časy, které potřebujete clusteru zprovozněn, můžete [vytvořit clustery na vyžádání pomocí Azure Data Factory](hdinsight-hadoop-create-linux-clusters-adf.md). Můžete také vytvořit skripty prostředí PowerShell, které zřazují a odstraňují váš cluster, a pak tyto skripty naplánovat pomocí [Azure Automation](https://azure.microsoft.com/services/automation/).

> [!NOTE]  
> Při odstranění clusteru je odstraněn o jeho výchozí Metastore Hive také. Chcete-li zachovat metastore pro další opětovné vytvoření clusteru, použijte externí úložiště metadat, jako je Azure Database nebo [Apache Oozie](https://oozie.apache.org/).
<!-- see [Using external metadata stores](hdinsight-using-external-metadata-stores.md). -->

### <a name="isolate-cluster-job-errors"></a>Izolovat chyby úloh y clusteru

Někdy může dojít k chybám z důvodu paralelního spuštění více map a snížení komponent v clusteru s více uzlovými počítači. Chcete-li problém izolovat, zkuste distribuované testování spuštěním souběžných více úloh v clusteru jednoho pracovního uzlu a potom rozbalte tento přístup tak, aby spouštěl více úloh současně v clusterech obsahujících více než jeden uzel. Chcete-li vytvořit cluster HDInsight s jedním uzlem v Azure, použijte možnost *Vlastní (velikost, nastavení, aplikace)* a při zřizování nového clusteru na portálu použijte hodnotu 1 pro *počet pracovních uzlů* v části Velikost **clusteru.**

## <a name="quotas"></a>Kvóty

Po určení velikosti, škálování a typu cílového clusteru zkontrolujte aktuální limity kapacity kvót vašeho předplatného. Když dosáhnete limitu kvóty, nemusí být možné nasadit nové clustery nebo horizontální navýšení kapacity existujících clusterů přidáním dalších pracovních uzlů. Jediným limitem kvóty je kvóta jádr procesoru, která existuje na úrovni oblasti pro každé předplatné. Vaše předplatné může mít například 30 základních limitů v oblasti USA – východ. 

Chcete-li zkontrolovat dostupná jádra, postupujte takto:

1. Přihlaste se k [portálu Azure](https://portal.azure.com/).
2. Přejděte na stránku **Přehled** pro cluster HDInsight. 
3. V levé nabídce klepněte na **položku Omezení kvót**.

   Na stránce se zobrazí počet používáných jader, počet dostupných jader a celkový počet jader.

Pokud potřebujete požádat o zvýšení kvóty, postupujte takto:

1. Přihlaste se k [portálu Azure](https://portal.azure.com/).
1. V levé dolní části stránky vyberte **Nápověda + podpora.**
1. Vyberte **nový požadavek na podporu**.
1. Na stránce **Nová žádost o podporu** vyberte v části **Základy** následující možnosti:

   - **Typ problému**: **Omezení služeb a předplatného (kvóty)**
   - **Předplatné**: předplatné, které chcete upravit
   - **Typ kvóty**: **HDInsight**

     ![Vytvoření žádosti o podporu pro zvýšení kvóty jádra HDInsight](./media/hdinsight-capacity-planning/hdinsight-quota-support-request.png)

1. Vyberte **další: Řešení >>**.
1. Na stránce **Podrobnosti** zadejte popis problému, vyberte závažnost problému, upřednostňovanou metodu kontaktu a další povinná pole.
1. Vyberte **další: Kontrola + vytvoření >>**.
1. Na kartě **Revize + vytvoření** vyberte **Vytvořit**.

> [!NOTE]  
> Pokud potřebujete zvýšit kvótu jádra HDInsight v privátní oblasti, [odešlete požadavek na seznam povolených položek](https://aka.ms/canaryintwhitelist).

Můžete [se obrátit na podporu a požádat o zvýšení kvóty](https://docs.microsoft.com/azure/azure-portal/supportability/resource-manager-core-quotas-request).

Existují však některé pevné kvóty, například jedno předplatné Azure může mít maximálně 10 000 jader. Podrobnosti o těchto omezeních najdete v [tématu limity předplatného azure a služby, kvóty a omezení](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits).

## <a name="next-steps"></a>Další kroky

* [Nastavení clusterů v HDInsightu pomocí Apache Hadoop, Spark, Kafka a dalších :](hdinsight-hadoop-provision-linux-clusters.md)Naučte se nastavit a nakonfigurovat clustery v HDInsight u Apache Hadoop, Spark, Kafka, Interactive Hive, HBase, ML Services nebo Storm.
* [Sledování výkonu clusteru](hdinsight-key-scenarios-to-monitor.md): Informace o klíčových scénářích, které je třeba sledovat pro váš cluster HDInsight, které mohou ovlivnit kapacitu vašeho clusteru.
