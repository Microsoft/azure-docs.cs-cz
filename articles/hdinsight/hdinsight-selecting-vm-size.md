---
title: Jak vybrat správnou velikost virtuálního počítače pro cluster Azure HDInsight
description: Naučte se, jak vybrat správnou velikost virtuálního počítače pro cluster HDInsight.
keywords: velikosti virtuálních počítačů, velikosti clusterů, konfigurace clusteru
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 10/09/2019
ms.openlocfilehash: 51043f0a1009994528783a1b56ec5ccec68e99b3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98931783"
---
# <a name="selecting-the-right-vm-size-for-your-azure-hdinsight-cluster"></a>Výběr správné velikosti virtuálního počítače pro cluster Azure HDInsight

Tento článek popisuje, jak vybrat správnou velikost virtuálního počítače pro různé uzly v clusteru HDInsight. 

Začněte tím, že pochopíte, jak vlastnosti virtuálního počítače, jako je zpracování procesoru, velikost paměti RAM a latence sítě, budou mít vliv na zpracování vašich úloh. V dalším kroku si myslíte, jak vaše aplikace odpovídá a jaké jsou pro ně optimalizované různé rodiny virtuálních počítačů. Ujistěte se, že rodina virtuálních počítačů, kterou chcete použít, je kompatibilní s typem clusteru, který plánujete nasadit. Seznam všech podporovaných a doporučených velikostí virtuálních počítačů pro každý typ clusteru najdete v tématu [podporované konfigurace uzlů pro Azure HDInsight](hdinsight-supported-node-configuration.md). Nakonec můžete použít proces srovnávacích testů k testování některých ukázkových úloh a zkontrolovat, kterou SKU v této rodině je pro vás nejvhodnější.

Další informace o plánování dalších aspektů vašeho clusteru, například výběru typu úložiště nebo velikosti clusteru, najdete v tématu [plánování kapacity pro clustery HDInsight](hdinsight-capacity-planning.md).

## <a name="vm-properties-and-big-data-workloads"></a>Vlastnosti virtuálního počítače a úlohy s velkými objemy dat

Velikost a typ virtuálního počítače závisí na výkonu procesoru, velikosti paměti RAM a latenci sítě:

- CPU: velikost virtuálního počítače určuje počet jader. Víc jader, tím větší je stupeň paralelního výpočtu, který každý uzel může dosáhnout. Některé typy virtuálních počítačů navíc mají rychlejší jádra.

- RAM: velikost virtuálního počítače také určí velikost paměti RAM dostupné ve virtuálním počítači. Pro úlohy, které ukládají data do paměti pro zpracování, nikoli čtení z disku, ujistěte se, že vaše pracovní uzly mají dostatek paměti pro přizpůsobení dat.

- Síť: u většiny typů clusterů data zpracovaná clusterem nejsou na místním disku, ale ne v externí službě úložiště, jako je Data Lake Storage nebo Azure Storage. Vezměte v úvahu šířku pásma a propustnost sítě mezi virtuálním počítačem uzlu a službou úložiště. Šířka pásma sítě, která je k dispozici pro virtuální počítač, se obvykle zvětšuje o větší velikost. Podrobnosti najdete v tématu [Přehled velikostí virtuálních počítačů](../virtual-machines/sizes.md).

## <a name="understanding-vm-optimization"></a>Principy optimalizace virtuálních počítačů

Rodiny virtuálních počítačů v Azure jsou optimalizované tak, aby vyhovovaly různým případům použití. V níže uvedené tabulce najdete některé z nejoblíbenějších případů použití a rodin virtuálních počítačů, které se na ně shodují.

| Typ                     | Velikosti           |    Popis       |
|--------------------------|-------------------|------------------------------------------------------------------------------------------------------------------------------------|
| [Vstupní úroveň](../virtual-machines/sizes-general.md)          | A, Av2  | Pro úlohy vstupní úrovně, jako je vývoj a testování, se nejlépe hodí konfigurace výkonu a paměti procesoru. Je to ekonomicky výhodná možnost, která vám umožní začít s Azure. |
| [Obecné účely](../virtual-machines/sizes-general.md)          | D, DSv2, Dv2  | Vyvážený poměr procesorů k paměti. Tato možnost je ideální pro testování a vývoj, malé až střední databáze a webové servery s nízkým až středním provozem. |
| [Optimalizované pro výpočty](../virtual-machines/sizes-compute.md)        | F           | Vysoký poměr procesorů k paměti. Vhodné pro webové servery se středním provozem, síťová zařízení, dávkové procesy a aplikační servery.        |
| [Optimalizované pro paměť](../virtual-machines/sizes-memory.md)         | Esv3, Ev3  | Vysoký poměr paměti k procesoru. Jsou velmi vhodné pro servery s relační databází, střední a velké mezipaměti a analýzu v paměti.                 |

- Informace o cenách dostupných instancí virtuálních počítačů napříč oblastmi podporovanými HDInsight najdete v tématu [ceny služby HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/).

## <a name="cost-saving-vm-types-for-light-workloads"></a>Náklady na úsporu typů virtuálních počítačů pro úlohy s lehkým zatížením

Pokud máte požadavky na zpracování světla, může být [Řada F-Series](https://azure.microsoft.com/blog/f-series-vm-size/) vhodnou volbou pro zahájení práce se službou HDInsight. Za nižší ceníkové sazby za hodinu nabízí řada F-series nejlepší poměr cena–výkon v portfoliu Azure, pokud jde o množství výpočetních jednotek Azure (ACU) na virtuální procesor.

Následující tabulka popisuje typy clusterů a typy uzlů, které lze vytvořit pomocí virtuálních počítačů Fsv2-Series.

| Typ clusteru | Verze | Pracovní uzel | Hlavní uzel | Uzel Zookeeper |
|---|---|---|---|---|
| Spark | Vše | F4 a vyšší | ne | ne |
| Hadoop | Vše | F4 a vyšší | ne | ne |
| Kafka | Vše | F4 a vyšší | ne | ne |
| HBase | Vše | F4 a vyšší | ne | ne |
| LLAP | zakázaný | ne | ne | ne |
| Storm | zakázaný | ne | ne | ne |
| Služba ML | JENOM HDI 3,6 | F4 a vyšší | ne | ne |

Specifikace jednotlivých SKU F-Series najdete v tématu [velikosti virtuálních počítačů řady f-Series](https://azure.microsoft.com/blog/f-series-vm-size/).

## <a name="benchmarking"></a>Srovnávací testy

Srovnávací testy je proces spouštění simulovaných úloh na různých virtuálních počítačích, aby bylo možné měřit, jak dobře budou fungovat pro vaše produkční úlohy. 

Další informace o srovnávacích testech pro SKU virtuálních počítačů a velikosti clusterů najdete v tématu [plánování kapacity clusteru ve službě Azure HDInsight ](hdinsight-capacity-planning.md#choose-the-vm-size-and-type).

## <a name="next-steps"></a>Další kroky

- [Konfigurace podporovaných uzlů Azure HDInsight](hdinsight-supported-node-configuration.md)
- [Sizes for Linux virtual machines in Azure](../virtual-machines/sizes.md)