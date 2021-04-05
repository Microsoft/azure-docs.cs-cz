---
title: Konfigurace podporovaných uzlů Azure HDInsight
description: Seznamte se s minimálními a doporučenými konfiguracemi pro uzly clusteru HDInsight.
keywords: velikosti virtuálních počítačů, velikosti clusterů, konfigurace clusteru
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 05/14/2020
ms.openlocfilehash: d41ee2554d30a56bc2e025bbe2c93aee143d75e8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98931648"
---
# <a name="what-are-the-default-and-recommended-node-configurations-for-azure-hdinsight"></a>Jaké jsou výchozí a doporučené konfigurace uzlů pro Azure HDInsight?

Tento článek popisuje výchozí a doporučené konfigurace uzlů pro clustery Azure HDInsight.

## <a name="default-and-minimum-recommended-node-configuration-and-virtual-machine-sizes-for-clusters"></a>Výchozí a minimální doporučená konfigurace uzlů a velikosti virtuálních počítačů pro clustery

V následujících tabulkách jsou uvedeny výchozí a Doporučené velikosti virtuálních počítačů pro clustery HDInsight.  Tyto informace jsou nezbytné k pochopení velikosti virtuálních počítačů, které se mají použít při vytváření skriptů PowerShellu nebo rozhraní příkazového řádku Azure CLI pro nasazení clusterů HDInsight.

Pokud v clusteru potřebujete víc než 32 uzlů pracovních procesů, vyberte velikost hlavního uzlu s aspoň 8 jádry a 14 GB paměti RAM.

Jedinými typy clusterů, které mají datové disky, jsou Kafka a HBA clustery s povolenou funkcí urychleného zápisu. HDInsight podporuje P30 a S30 velikosti disků v těchto scénářích. Pro všechny ostatní typy clusterů poskytuje HDInsight spravované místo na disku clusteru. Od 11/07/2019 bude velikost spravovaného disku každého uzlu v nově vytvořeném clusteru 128 GB. Tato změna se nedá změnit.

Specifikace všech minimálních doporučených typů virtuálních počítačů použitých v tomto dokumentu jsou shrnuté v následující tabulce.

| Velikost              | Virtuální procesory | Paměť: GiB | Dočasné úložiště (SSD): GiB | Maximální propustnost dočasného úložiště: IOPS / čtení v MB/s / zápis v MB/s | Maximální propustnost datových disků: IOPS | Maximální počet síťových karet/očekávaná šířka pásma sítě (MB/s) |
|-------------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| Standard_D3_v2 | 4    | 14          | 200                    | 12000 / 187 / 93                                           | 16/16x500           | 4 / 3 000                                       |
| Standard_D4_v2 | 8    | 28          | 400                    | 24000 / 375 / 187                                          | 32/32x500           | 8 / 6 000                                       |
| Standard_D5_v2 | 16   | 56          | 800                    | 48000 / 750 / 375                                          | 64/64x500           | 8 / 12000                                    |
| Standard_D12_v2   | 4         | 28          | 200            | 12000 / 187 / 93                                         | 16 / 16×500                         | 4 / 3 000                     |
| Standard_D13_v2   | 8         | 56          | 400            | 24000 / 375 / 187                                        | 32 / 32×500                       | 8 / 6 000                     |
| Standard_D14_v2   | 16        | 112         | 800            | 48000 / 750 / 375                                        | 64/64x500                       | 8 / 12000          |
| Standard_A1_v2  | 1         | 2           | 10             | 1000 / 20 / 10                                           | 2 / 2×500               | 2 / 250                 |
| Standard_A2_v2  | 2         | 4           | 20             | 2000 / 40 / 20                                           | 4 / 4×500               | 2 / 500                 |
| Standard_A4_v2  | 4         | 8           | 40             | 4000 / 80 / 40                                           | 8 / 8×500               | 4 / 1 000                     |

Další informace o specifikacích jednotlivých typů virtuálních počítačů najdete v následujících dokumentech:

* [Velikosti virtuálních počítačů pro obecné účely: Dv2 Series 1-5](../virtual-machines/dv2-dsv2-series.md)
* [Paměťově optimalizované velikosti virtuálních počítačů: Dv2 Series 11-15](../virtual-machines/dv2-dsv2-series-memory.md)
* [Velikosti virtuálních počítačů pro obecné účely: Av2 Series 1-8](../virtual-machines/av2-series.md)

### <a name="all-supported-regions-except-brazil-south-and-japan-west"></a>Všechny podporované oblasti kromě Brazílie – jih a Japonsko – západ

> [!Note]
> Pokud chcete získat identifikátor SKU pro použití v PowerShellu a dalších skriptech, přidejte `Standard_` na začátek všech SKU virtuálních počítačů v následujících tabulkách. Například `D12_v2` by se stala `Standard_D12_v2` .

| Typ clusteru | Hadoop | HBase | Interaktivní dotaz | Storm | Spark | ML Server | Kafka |
|---|---|---|---|---|---|---|---|
| Head: výchozí velikost virtuálního počítače | D12_v2 | D12_v2 | D13_v2 | A4_v2 | D12_v2, <br/>D13_v2 * | D12_v2 | D3_v2 |
| Head: Minimální doporučené velikosti virtuálních počítačů | D5_v2 | D3_v2 | D13_v2 | A4_v2 | D12_v2, <br/>D13_v2 * | D12_v2 | D3_v2 |
| Pracovní proces: výchozí velikost virtuálního počítače | D4_v2 | D4_v2 | D14_v2 | D3_v2 | D13_v2 | D4_v2 | 4 D12_v2 s 2 S30 disky na zprostředkovatele |
| Pracovní proces: Minimální doporučené velikosti virtuálních počítačů | D5_v2 | D3_v2 | D13_v2 | D3_v2 | D12_v2 | D4_v2 | D3_v2 |
| ZooKeeper: výchozí velikost virtuálního počítače |  | A4_v2 | A4_v2 | A4_v2 |  | A2_v2 | A4_v2 |
| ZooKeeper: Minimální doporučené velikosti virtuálních počítačů |  | A4_v2 | A4_v2 | A2_v2 |  | A2_v2 | A4_v2 |
| Služby ML: výchozí velikost virtuálního počítače |  |  |  |  |  | D4_v2 |  |
| Služby ML: Minimální doporučená velikost virtuálního počítače |  |  |  |  |  | D4_v2 |  |

\* = Velikosti virtuálních počítačů pro clustery Spark Balíček zabezpečení podniku (ESP)

### <a name="brazil-south-and-japan-west-only"></a>Brazílie – pouze jih a Japonsko – západ

| Typ clusteru | Hadoop | HBase | Interaktivní dotaz | Storm | Spark | Služby ML |
|---|---|---|---|---|---|---|
| Head: výchozí velikost virtuálního počítače | D12 | D12 | D13 | A4_v2 | D12 | D12 |
| Head: Minimální doporučené velikosti virtuálních počítačů | D5_v2 | D3_v2 | D13_v2 | A4_v2 | D12_v2 | D12_v2 |
| Pracovní proces: výchozí velikost virtuálního počítače | 4 | 4 | D14 | D3 | D13 | 4 |
| Pracovní proces: Minimální doporučené velikosti virtuálních počítačů | D5_v2 | D3_v2 | D13_v2 | D3_v2 | D12_v2 | D4_v2 |
| ZooKeeper: výchozí velikost virtuálního počítače |  | A4_v2 | A4_v2 | A4_v2 |  | A2_v2 |
| ZooKeeper: Minimální doporučené velikosti virtuálních počítačů |  | A4_v2 | A4_v2 | A4_v2 |  | A2_v2 |
| Služby ML: výchozí velikosti virtuálních počítačů |  |  |  |  |  | 4 |
| Služby ML: Minimální doporučené velikosti virtuálních počítačů |  |  |  |  |  | D4_v2 |

> [!NOTE]
> - Hlava se označuje jako *Nimbus* pro typ clusteru.
> - Pracovní proces je známý jako *správce* pro typ clusteru.
> - Pracovní proces je známý jako *oblast* pro typ clusteru HBA.

## <a name="next-steps"></a>Další kroky

* [Jaké jsou součásti Apache Hadoop a verze, které jsou k dispozici v HDInsight?](hdinsight-component-versioning.md)
