---
title: Plánování kapacity clusteru v Azure HDInsight
description: Identifikujte klíčové otázky pro plánování kapacity a výkonu clusteru Azure HDInsight.
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 05/07/2020
ms.openlocfilehash: 45cfa90f2156dba87cbec2b6313bc24e5d030572
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98933225"
---
# <a name="capacity-planning-for-hdinsight-clusters"></a>Plánování kapacity pro clustery HDInsight

Před nasazením clusteru HDInsight si naplánujte zamýšlenou kapacitu clusteru určením potřebného výkonu a škálování. Toto plánování pomáhá optimalizovat použitelnost i náklady. Některá rozhodnutí o kapacitě clusteru se po nasazení nedají změnit. Pokud se změní parametry výkonu, cluster se dá deinstalovat a znovu vytvořit, aniž by došlo ke ztrátě uložených dat.

Klíčovým dotazem pro plánování kapacity jsou:

* V jakých geografických oblastech byste měli cluster nasazovat?
* Jak velké úložiště potřebujete?
* Jaký typ clusteru byste měli nasadit?
* Jaké velikosti a typy virtuálních počítačů by měly uzly clusteru používat?
* Kolik pracovních uzlů má váš cluster?

## <a name="choose-an-azure-region"></a>Zvolit oblast Azure

Oblast Azure určuje, kde se cluster fyzicky zřídí. Aby se minimalizovala latence čtení a zápisu, měl by cluster být blízko vašich dat.

HDInsight je k dispozici v mnoha oblastech Azure. Pokud chcete najít nejbližší oblast, přečtěte si téma [Dostupné produkty v jednotlivých oblastech](https://azure.microsoft.com/global-infrastructure/services/?products=hdinsight).

## <a name="choose-storage-location-and-size"></a>Zvolit umístění a velikost úložiště

### <a name="location-of-default-storage"></a>Umístění výchozího úložiště

Výchozí úložiště, buď účet Azure Storage, nebo Azure Data Lake Storage, musí být ve stejném umístění jako váš cluster. Azure Storage je k dispozici ve všech umístěních. Data Lake Storage Gen1 je k dispozici v některých oblastech – viz aktuální [dostupnost Data Lake Storage](https://azure.microsoft.com/global-infrastructure/services/?products=storage).

### <a name="location-of-existing-data"></a>Umístění stávajících dat

Pokud chcete použít existující účet úložiště nebo Data Lake Storage jako výchozí úložiště clusteru, musíte nasadit svůj cluster na stejném místě.

### <a name="storage-size"></a>Velikost úložiště

V nasazeném clusteru můžete připojit další účty Azure Storage nebo získat přístup k jiným Data Lake Storage. Všechny vaše účty úložiště musí být živé ve stejném umístění jako váš cluster. Data Lake Storage může být v jiném umístění, i když Skvělé vzdálenosti můžou způsobit určitou latenci.

Azure Storage má některá [omezení kapacity](../azure-resource-manager/management/azure-subscription-service-limits.md#storage-limits), zatímco Data Lake Storage Gen1 je skoro neomezené.

Cluster má přístup k kombinaci různých účtů úložiště. Mezi typické příklady patří:

* V případě, že množství dat může překročit kapacitu úložiště jednoho kontejneru úložiště objektů BLOB.
* V případě, že frekvence přístupu k kontejneru objektů BLOB může překročit prahovou hodnotu, pokud dojde k omezování.
* Když chcete data udělat, už jste nahráli do kontejneru objektů blob, který je k dispozici pro cluster.
* Pokud chcete izolovat různé části úložiště z důvodů zabezpečení nebo zjednodušit správu.

Pro lepší výkon používejte jenom jeden kontejner na účet úložiště.

## <a name="choose-a-cluster-type"></a>Zvolit typ clusteru

Typ clusteru určuje zatížení, ke kterému má být cluster HDInsight nakonfigurovaný. Mezi typy patří [Apache Hadoop](./hadoop/apache-hadoop-introduction.md), [Apache Storm](./storm/apache-storm-overview.md), [Apache Kafka](./kafka/apache-kafka-introduction.md)nebo [Apache Spark](./spark/apache-spark-overview.md). Podrobný popis dostupných typů clusterů najdete v tématu [Úvod do Azure HDInsight](hdinsight-overview.md#cluster-types-in-hdinsight). Každý typ clusteru má konkrétní topologii nasazení, která zahrnuje požadavky na velikost a počet uzlů.

## <a name="choose-the-vm-size-and-type"></a>Zvolit velikost a typ virtuálního počítače

Každý typ clusteru má sadu typů uzlů a každý typ uzlu má konkrétní možnosti pro velikost a typ virtuálního počítače.

Chcete-li určit optimální velikost clusteru pro vaši aplikaci, můžete otestovat kapacitu clusteru benchmark a zvětšit uvedenou velikost. Můžete například použít simulované úlohy nebo *testovací dotaz*. Spusťte simulované úlohy v různých velikostních clusterech. Postupně zvyšujte velikost, dokud se nedosáhne zamýšleného výkonu. Do ostatních provozních dotazů se dá pravidelně vkládat dotaz, který ukazuje, jestli cluster má dostatek prostředků.

Další informace o tom, jak zvolit správnou rodinu virtuálních počítačů pro vaše zatížení, najdete v tématu [Výběr správné velikosti virtuálních počítačů pro váš cluster](hdinsight-selecting-vm-size.md).

## <a name="choose-the-cluster-scale"></a>Výběr škálování clusteru

Velikost clusteru se určuje podle počtu jeho uzlů virtuálních počítačů. Pro všechny typy clusterů existují typy uzlů, které mají konkrétní měřítko, a typy uzlů, které podporují škálování na více instancí. Cluster může například vyžadovat přesně tři [Apache Zookeeper](https://zookeeper.apache.org/) uzly nebo dva hlavní uzly. Pracovní uzly, které zpracovávají data distribuovaným způsobem, využívají další pracovní uzly.

V závislosti na typu clusteru zvyšuje počet pracovních uzlů další výpočetní kapacitu (například více jader). Další uzly zvýší celkovou paměť potřebnou pro celý cluster, aby bylo možné podporovat úložiště zpracovávaných dat v paměti. Stejně jako u možnosti velikost a typ virtuálního počítače se obvykle dosahuje i správného škálování clusteru. Používejte simulované úlohy nebo Kanárské dotazy.

Kapacitu clusteru můžete škálovat tak, aby splňovala požadavky na zatížení ve špičce. Pokud už tyto dodatečné uzly nepotřebujete, můžete je znovu škálovat. [Funkce automatického škálování](hdinsight-autoscale-clusters.md) umožňuje automaticky škálovat cluster na základě předem určených metrik a časování. Další informace o ručním škálování clusterů najdete v tématu věnovaném [škálování clusterů HDInsight](hdinsight-scaling-best-practices.md).

### <a name="cluster-lifecycle"></a>Životní cyklus clusteru

Účtuje se vám doba života clusteru. V případě, že cluster potřebuje jenom určité časy, [Vytvořte clustery na vyžádání pomocí Azure Data Factory](hdinsight-hadoop-create-linux-clusters-adf.md). Můžete také vytvořit skripty PowerShellu, které zřídí a odstraní cluster, a pak tyto skripty naplánujete pomocí [Azure Automation](https://azure.microsoft.com/services/automation/).

> [!NOTE]  
> Při odstranění clusteru se odstraní i jeho výchozí metastore Hive. Pokud chcete zachovat metastore pro další opětovné vytvoření clusteru, použijte externí úložiště metadat, jako je Azure Database nebo [Apache Oozie](https://oozie.apache.org/).

### <a name="isolate-cluster-job-errors"></a>Izolace chyb úloh clusteru

K chybám může dojít, pokud dojde k paralelnímu spuštění více map a omezení součástí clusteru s více uzly. Pro snazší izolaci problému zkuste použít distribuované testování. Spusťte souběžné více úloh v jednom clusteru pracovního uzlu. Pak tento přístup rozbalíte, pokud chcete spustit více úloh současně na clusterech, které obsahují více než jeden uzel. Pokud chcete v Azure vytvořit cluster HDInsight s jedním uzlem, použijte *`Custom(size, settings, apps)`* možnost a při zřizování nového clusteru na portálu použijte hodnotu 1 pro *počet uzlů pracovního procesu* v části **Velikost clusteru** .

## <a name="quotas"></a>Kvóty

Další informace o správě kvót předplatného najdete v tématu [vyžádání zvýšení kvóty](quota-increase-request.md).

## <a name="next-steps"></a>Další kroky

* [Nastavení clusterů ve službě HDInsight pomocí Apache Hadoop, Spark, Kafka a dalších](hdinsight-hadoop-provision-linux-clusters.md): Naučte se, jak nastavit a nakonfigurovat clustery v HDInsight.
* [Monitorování výkonu clusteru](hdinsight-key-scenarios-to-monitor.md): Přečtěte si o klíčových scénářích, jak monitorovat cluster HDInsight, který může mít vliv na kapacitu clusteru.
