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
ms.openlocfilehash: 17b68de4766aa8f995a88bd583a7a84e646b9325
ms.sourcegitcommit: f29fec8ec945921cc3a89a6e7086127cc1bc1759
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/17/2019
ms.locfileid: "72529168"
---
# <a name="capacity-planning-for-hdinsight-clusters"></a>Plánování kapacity pro clustery HDInsight

Než nasadíte cluster HDInsight, naplánujte požadovanou kapacitu clusteru určením potřebného výkonu a škálování. Toto plánování pomáhá optimalizovat použitelnost i náklady. Některá rozhodnutí o kapacitě clusteru se po nasazení nedají změnit. Pokud se změní parametry výkonu, cluster se dá deinstalovat a znovu vytvořit, aniž by došlo ke ztrátě uložených dat.

Klíčovým dotazem pro plánování kapacity jsou:

* V jakých geografických oblastech byste měli cluster nasazovat?
* Jak velké úložiště potřebujete?
* Jaký typ clusteru byste měli nasadit?
* Jaké velikosti a typy virtuálních počítačů by měly uzly clusteru používat?
* Kolik pracovních uzlů má váš cluster?

## <a name="choose-an-azure-region"></a>Zvolit oblast Azure

Oblast Azure určuje, kde se cluster fyzicky zřídí. Aby se minimalizovala latence čtení a zápisu, měl by cluster být blízko vašich dat.

HDInsight je k dispozici v mnoha oblastech Azure. Pokud chcete najít nejbližší oblast, přečtěte si téma [Dostupné produkty v jednotlivých oblastech](https://azure.microsoft.com/en-us/global-infrastructure/services/?products=hdinsight/).

## <a name="choose-storage-location-and-size"></a>Zvolit umístění a velikost úložiště

### <a name="location-of-default-storage"></a>Umístění výchozího úložiště

Výchozí úložiště, buď účet Azure Storage, nebo Azure Data Lake Storage, musí být ve stejném umístění jako váš cluster. Azure Storage je k dispozici ve všech umístěních. Data Lake Storage Gen1 je k dispozici v některých oblastech – viz aktuální [dostupnost Data Lake Storage](https://azure.microsoft.com/en-us/global-infrastructure/services/?products=storage).

### <a name="location-of-existing-data"></a>Umístění stávajících dat

Pokud už máte účet úložiště nebo Data Lake Storage obsahující vaše data a chcete toto úložiště používat jako výchozí úložiště clusteru, musíte nasadit cluster na stejném místě.

### <a name="storage-size"></a>Velikost úložiště

Po nasazení clusteru HDInsight můžete připojit další účty Azure Storage nebo získat přístup k jiným Data Lake Storage. Všechny vaše účty úložiště se musí nacházet ve stejném umístění jako váš cluster. Data Lake Storage může být v jiném umístění, i když to může způsobit latenci čtení a zápisu dat.

Azure Storage má některá [omezení kapacity](../azure-subscription-service-limits.md#storage-limits), zatímco Data Lake Storage Gen1 je prakticky neomezené.

Cluster má přístup k kombinaci různých účtů úložiště. Mezi typické příklady patří:

* V případě, že množství dat může překročit kapacitu úložiště jednoho kontejneru úložiště objektů BLOB.
* V případě, že frekvence přístupu k kontejneru objektů BLOB může překročit prahovou hodnotu, pokud dojde k omezování.
* Když chcete data udělat, už jste nahráli do kontejneru objektů blob, který je k dispozici pro cluster.
* Pokud chcete izolovat různé části úložiště z důvodů zabezpečení nebo zjednodušit správu.

Pro cluster 48 doporučujeme 4 až 8 účtů úložiště. I když již může existovat dostatečná celková velikost úložiště, každý účet úložiště poskytuje pro výpočetní uzly další šířku pásma sítě. Pokud máte více účtů úložiště, použijte pro každý účet úložiště náhodný název bez předpony. Účelem náhodného pojmenování je snížit pravděpodobnost kritických bodů úložiště (omezování) nebo selhání v běžném režimu napříč všemi účty. Pro lepší výkon používejte jenom jeden kontejner na účet úložiště.

## <a name="choose-a-cluster-type"></a>Zvolit typ clusteru

Typ clusteru určuje zatížení, které je nakonfigurováno pro spuštění clusteru HDInsight, například [Apache Hadoop](https://hadoop.apache.org/), [Apache Storm](https://storm.apache.org/), [Apache Kafka](https://kafka.apache.org/)nebo [Apache Spark](https://spark.apache.org/). Podrobný popis dostupných typů clusterů najdete v tématu [Úvod do Azure HDInsight](hdinsight-overview.md#cluster-types-in-hdinsight). Každý typ clusteru má konkrétní topologii nasazení, která zahrnuje požadavky na velikost a počet uzlů.

## <a name="choose-the-vm-size-and-type"></a>Zvolit velikost a typ virtuálního počítače

Každý typ clusteru má sadu typů uzlů a každý typ uzlu má konkrétní možnosti pro velikost a typ virtuálního počítače.

Chcete-li určit optimální velikost clusteru pro vaši aplikaci, můžete otestovat kapacitu clusteru benchmark a zvětšit uvedenou velikost. Můžete například použít simulované úlohy nebo *testovací dotaz*. Díky simulovaným úlohám spouštíte očekávané úlohy v různých velikostních clusterech a postupně zvyšují velikost, dokud se nedosáhne požadovaného výkonu. Do ostatních provozních dotazů se dá pravidelně vkládat dotaz, který ukazuje, jestli cluster má dostatek prostředků.

Velikost a typ virtuálního počítače závisí na výkonu procesoru, velikosti paměti RAM a latenci sítě:

* CPU: velikost virtuálního počítače určuje počet jader. Víc jader, tím větší je stupeň paralelního výpočtu, který každý uzel může dosáhnout. Některé typy virtuálních počítačů navíc mají rychlejší jádra.

* RAM: velikost virtuálního počítače také určí velikost paměti RAM dostupné ve virtuálním počítači. Pro úlohy, které ukládají data do paměti pro zpracování, nikoli čtení z disku, ujistěte se, že vaše pracovní uzly mají dostatek paměti pro přizpůsobení dat.

* Síť: u většiny typů clusterů data zpracovaná clusterem nejsou na místním disku, ale ne v externí službě úložiště, jako je Data Lake Storage nebo Azure Storage. Vezměte v úvahu šířku pásma a propustnost sítě mezi virtuálním počítačem uzlu a službou úložiště. Šířka pásma sítě, která je k dispozici pro virtuální počítač, se obvykle zvětšuje o větší velikost. Podrobnosti najdete v tématu [Přehled velikostí virtuálních počítačů](https://docs.microsoft.com/azure/virtual-machines/linux/sizes).

## <a name="choose-the-cluster-scale"></a>Výběr škálování clusteru

Velikost clusteru se určuje podle počtu jeho uzlů virtuálních počítačů. Pro všechny typy clusterů existují typy uzlů, které mají konkrétní měřítko, a typy uzlů, které podporují škálování na více instancí. Cluster může například vyžadovat přesně tři [Apache Zookeeper](https://zookeeper.apache.org/) uzly nebo dva hlavní uzly. Pracovní uzly, které zpracovávají data distribuovaným způsobem, můžou využít výhod rozšiřování kapacity přidáním dalších pracovních uzlů.

V závislosti na typu clusteru zvyšuje počet pracovních uzlů další výpočetní kapacitu (například více jader), ale může také přidat do celkového množství paměti, které je nutné, aby celý cluster podporoval ukládání zpracovaných dat v paměti. Stejně jako u zvolené velikosti a typu virtuálních počítačů se při výběru správného měřítka clusteru obvykle dosáhne empirické využití simulovaných úloh nebo Kanárských dotazů.

Můžete škálovat cluster tak, aby splňoval požadavky na nejvyšší zatížení, a pak ho škálovat zpátky, když už tyto dodatečné uzly nepotřebujete. [Funkce automatického škálování](hdinsight-autoscale-clusters.md) umožňuje automaticky škálovat cluster na základě předem určených metrik a časování. Další informace o ručním škálování clusterů najdete v tématu věnovaném [škálování clusterů HDInsight](hdinsight-scaling-best-practices.md).

### <a name="cluster-lifecycle"></a>Životní cyklus clusteru

Účtují se vám poplatky za dobu života clusteru. Pokud máte ke svému clusteru jenom určité časy, které potřebujete, můžete [vytvořit clustery na vyžádání pomocí Azure Data Factory](hdinsight-hadoop-create-linux-clusters-adf.md). Můžete také vytvořit skripty PowerShellu, které zřídí a odstraní cluster, a pak tyto skripty naplánujete pomocí [Azure Automation](https://azure.microsoft.com/services/automation/).

> [!NOTE]  
> Při odstranění clusteru se odstraní i jeho výchozí metastore Hive. Pokud chcete zachovat metastore pro další opětovné vytvoření clusteru, použijte externí úložiště metadat, jako je Azure Database nebo [Apache Oozie](https://oozie.apache.org/).
<!-- see [Using external metadata stores](hdinsight-using-external-metadata-stores.md). -->

### <a name="isolate-cluster-job-errors"></a>Izolace chyb úloh clusteru

K chybám může dojít v důsledku paralelního spouštění více map a snížení počtu komponent v clusteru s více uzly. Chcete-li tento problém izolovat, zkuste distribuované testování spuštěním souběžných úloh v jednom clusteru pracovních uzlů a pak tento přístup rozbalte, pokud chcete spustit více úloh současně na clusterech, které obsahují více než jeden uzel. Pokud chcete v Azure vytvořit cluster HDInsight s jedním uzlem, použijte možnost *vlastní (velikost, nastavení, aplikace)* a při zřizování nového clusteru na portálu použijte hodnotu 1 pro *počet uzlů pracovního procesu* v části **Velikost clusteru** .

## <a name="quotas"></a>Kvóty

Po určení velikosti virtuálního počítače clusteru, škálování a typu ověřte aktuální limity kapacity vašeho předplatného. Pokud dosáhnete limitu kvóty, možná nebudete moct nasadit nové clustery nebo škálovat existující clustery přidáním dalších pracovních uzlů. Jedinou omezenou kvótou je kvóta PROCESORových jader, která existuje na úrovni oblasti pro každé předplatné. Například vaše předplatné může mít v Východní USA oblasti 30 jader omezení. Pokud potřebujete požádat o zvýšení kvóty, proveďte následující kroky:

1. Přihlaste se na web [Azure Portal](https://portal.azure.com/).
1. V levé dolní části stránky vyberte **help + support** .
1. Vyberte **Nová žádost o podporu**.
1. Na stránce **Nová žádost o podporu** na kartě **základy** vyberte následující možnosti:
   - **Typ problému**: **omezení služby a předplatného (kvóty)**
   - **Předplatné**: předplatné, které chcete upravit.
   - **Typ kvóty**: **HDInsight**
    
     ![Vytvoření žádosti o podporu pro zvýšení kvóty jádra HDInsight](./media/hdinsight-capacity-planning/hdinsight-quota-support-request.png)

1. Vyberte **Další: řešení > >** .
1. Na stránce **Podrobnosti** zadejte popis problému, vyberte závažnost problému, upřednostňovanou metodu kontaktu a další povinná pole.
1. Vyberte **Další: zkontrolovat + vytvořit > >** .
1. Na kartě **Revize + vytvořit** vyberte **vytvořit**.

> [!NOTE]  
> Pokud potřebujete zvýšit kvótu jádra HDInsight v soukromé oblasti, [odešlete žádost o seznam povolených](https://aka.ms/canaryintwhitelist).

Můžete [kontaktovat podporu a požádat o zvýšení kvóty](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request).

Existují však omezení pevné kvóty, například jedno předplatné Azure může mít maximálně 10 000 jader. Podrobnosti o těchto omezeních najdete v tématu [limity, kvóty a omezení předplatného a služeb Azure](https://docs.microsoft.com/azure/azure-subscription-service-limits).

## <a name="next-steps"></a>Další kroky

* [Nastavení clusterů ve službě HDInsight pomocí Apache Hadoop, Spark, Kafka a dalších](hdinsight-hadoop-provision-linux-clusters.md): Naučte se, jak nastavit a nakonfigurovat clustery v hdinsight pomocí Apache Hadoop, Spark, Kafka, interaktivního podregistru, HBA, služeb ml nebo přetvoření.
* [Monitorování výkonu clusteru](hdinsight-key-scenarios-to-monitor.md): Přečtěte si o klíčových scénářích, jak monitorovat cluster HDInsight, který může mít vliv na kapacitu clusteru.
