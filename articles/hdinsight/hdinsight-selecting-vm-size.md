---
title: Jak vybrat správnou velikost virtuálního počítače pro váš cluster Azure HDInsight
description: Přečtěte si, jak vybrat správnou velikost virtuálního počítače pro váš cluster HDInsight.
keywords: velikosti virtuálních počítačů, velikosti clusteru, konfigurace clusteru
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 10/09/2019
ms.openlocfilehash: a21e8d6c76c93b3084619c09f6a7664a25c1929c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73682208"
---
# <a name="selecting-the-right-vm-size-for-your-azure-hdinsight-cluster"></a>Výběr správné velikosti virtuálního počítače pro váš cluster Azure HDInsight

Tento článek popisuje, jak vybrat správnou velikost virtuálního počítače pro různé uzly v clusteru HDInsight. 

Začněte tím, že pochopíte, jak vlastnosti virtuálního počítače, jako je zpracování procesoru, velikost paměti RAM a latence sítě, ovlivní zpracování vašich úloh. Dále přemýšlejte o vaší aplikaci a o tom, jak odpovídá různým rodinám virtuálních zařízení, pro které jsou optimalizované. Ujistěte se, že rodina virtuálních počítačů, kterou chcete použít, je kompatibilní s typem clusteru, který chcete nasadit. Seznam všech podporovaných a doporučených velikostí virtuálních počítačů pro každý typ clusteru najdete v tématu [Konfigurace uzlů podporovaných Azure HDInsight](hdinsight-supported-node-configuration.md). Nakonec můžete použít proces srovnávání k testování některých ukázkových úloh a ke kontrole, která skladová položka v rámci této rodiny je pro vás ta pravá.

Další informace o plánování dalších aspektů clusteru, jako je výběr typu úložiště nebo velikosti clusteru, naleznete v [tématu Plánování kapacity pro clustery HDInsight](hdinsight-capacity-planning.md).

## <a name="vm-properties-and-big-data-workloads"></a>Vlastnosti virtuálních počítače a úlohy velkých objemů dat

Velikost a typ virtuálního počítače je určen výkonem procesoru, velikostí paměti RAM a latencí sítě:

- CPU: Velikost virtuálního počítače určuje počet jader. Čím více jader, tím větší je stupeň paralelního výpočtu, které může každý uzel dosáhnout. Některé typy virtuálních počítačů mají také rychlejší jádra.

- RAM: Velikost virtuálního počítače také určuje množství paměti RAM, které jsou k dispozici ve virtuálním počítače. Pro úlohy, které ukládají data v paměti pro zpracování, nikoli čtení z disku, ujistěte se, že pracovní uzly mají dostatek paměti, aby se vešly data.

- Síť: U většiny typů clusterů nejsou data zpracovaná clusterem na místním disku, ale spíše v externí službě úložiště, jako je úložiště datových jezer nebo Azure Storage. Zvažte šířku pásma sítě a propustnost mezi virtuálním virtuálním proudem uzlu a službou úložiště. Šířka pásma sítě, která je k dispozici pro virtuální počítač, se obvykle zvyšuje s většími velikostmi. Podrobnosti najdete v tématu [Přehled velikostí virtuálních počítače](https://docs.microsoft.com/azure/virtual-machines/linux/sizes).

## <a name="understanding-vm-optimization"></a>Principy optimalizace virtuálních mís

Rodiny virtuálních strojů v Azure jsou optimalizované tak, aby vyhovovaly různým případům použití. V následující tabulce najdete některé z nejoblíbenějších případů použití a rodiny virtuálních knih, které se jim shodují.

| Typ                     | Velikosti           |    Popis       |
|--------------------------|-------------------|------------------------------------------------------------------------------------------------------------------------------------|
| [Entry-level](../virtual-machines/linux/sizes-general.md)          | A, Av2  | Mějkonfiguraci výkonu procesoru a paměti, která nejlépe vyhovuje úlohám vstupní úrovně, jako je vývoj a testování. Jsou ekonomické a poskytují nízkonákladovou možnost, jak začít s Azure. |
| [Obecné účely](../virtual-machines/linux/sizes-general.md)          | D, DSv2, Dv2  | Vyvážený poměr procesoru k paměti. Tato možnost je ideální pro testování a vývoj, malé až střední databáze a webové servery s nízkým až středním provozem. |
| [Optimalizované z hlediska výpočetních služeb](../virtual-machines/linux/sizes-compute.md)        | F           | Vysoký poměr procesoru k paměti. Vhodné pro webové servery se středním provozem, síťová zařízení, dávkové procesy a aplikační servery.        |
| [Optimalizované z hlediska paměti](../virtual-machines/linux/sizes-memory.md)         | Esv3, Ev3  | Vysoký poměr paměti k procesoru. Jsou velmi vhodné pro servery s relační databází, střední a velké mezipaměti a analýzu v paměti.                 |

- Informace o cenách dostupných instancí virtuálních montovek v podporovaných oblastech HDInsightu najdete v [tématu CENY HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).

## <a name="cost-saving-vm-types-for-light-workloads"></a>Úsporné typy virtuálních počítačů pro lehké úlohy

Pokud máte požadavky na lehké zpracování, [řada F](https://azure.microsoft.com/blog/f-series-vm-size/) může být dobrou volbou pro zahájení s HDInsight. Za nižší ceníkové sazby za hodinu nabízí řada F-series nejlepší poměr cena–výkon v portfoliu Azure, pokud jde o množství výpočetních jednotek Azure (ACU) na virtuální procesor.

Následující tabulka popisuje typy clusterů a typy uzlů, které lze vytvořit pomocí virtuálních počítačů řady Fsv2.

| Typ clusteru | Version | Uzel pracovníka | Hlavní uzel | Zookeeper uzel |
|---|---|---|---|---|
| Spark | Všechny | F4 a vyšší | ne | ne |
| Hadoop | Všechny | F4 a vyšší | ne | ne |
| Kafka | Všechny | F4 a vyšší | ne | ne |
| HBase | Všechny | F4 a vyšší | ne | ne |
| LLAP | zakázaný | ne | ne | ne |
| Storm | zakázaný | ne | ne | ne |
| Služba ML | POUZE HDI 3.6 | F4 a vyšší | ne | ne |

Informace o specifikacích jednotlivých skladových míst řady F naleznete v [tématu Velikosti virtuálních počítače řady F](https://azure.microsoft.com/blog/f-series-vm-size/).

## <a name="benchmarking"></a>Srovnávání

Benchmarking je proces spouštění simulovaných úloh na různých virtuálních počítačích, aby se změřilo, jak dobře budou fungovat pro vaše produkční úlohy. 

Další informace o benchmarkingu pro virtuální počítače a velikostclusteru najdete [v tématu Plánování kapacity clusteru v Azure HDInsight ](hdinsight-capacity-planning.md#choose-the-vm-size-and-type).

## <a name="next-steps"></a>Další kroky

- [Podporované konfigurace uzlů Azure HDInsight](hdinsight-supported-node-configuration.md)
- [Velikosti virtuálních počítačů s Linuxem v Azure](../virtual-machines/linux/sizes.md)
