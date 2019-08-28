---
title: Konfigurace podporovaných uzlů Azure HDInsight
description: Seznamte se s minimálními a doporučenými konfiguracemi pro uzly clusteru HDInsight.
keywords: velikosti virtuálních počítačů, velikosti clusterů, konfigurace clusteru
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 08/26/2019
ms.openlocfilehash: e482cf9b5367beba00784e69c5bad88142df5225
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2019
ms.locfileid: "70076209"
---
# <a name="what-are-the-default-and-recommended-node-configurations-for-azure-hdinsight"></a>Jaké jsou výchozí a doporučené konfigurace uzlů pro Azure HDInsight?

Tento článek popisuje výchozí a doporučené konfigurace uzlů pro clustery Azure HDInsight.

## <a name="default-and-recommended-node-configuration-and-virtual-machine-sizes-for-clusters"></a>Výchozí a doporučená konfigurace uzlů a velikosti virtuálních počítačů pro clustery

V následujících tabulkách jsou uvedeny výchozí a Doporučené velikosti virtuálních počítačů pro clustery HDInsight.  Tyto informace jsou nezbytné k pochopení velikosti virtuálních počítačů, které se mají použít při vytváření skriptů PowerShellu nebo rozhraní příkazového řádku Azure CLI pro nasazení clusterů HDInsight. 

Pokud v clusteru potřebujete víc než 32 uzlů pracovních procesů, vyberte velikost hlavního uzlu s aspoň 8 jádry a 14 GB paměti RAM. 

Jedinými typy clusterů, které mají datové disky, jsou Kafka a HBA clustery s povolenou funkcí urychleného zápisu. HDInsight podporuje P30 a S30 velikosti disků v těchto scénářích.

Další informace o specifikaci každého typu virtuálního počítače najdete v následujících dokumentech:

* [Velikosti virtuálních počítačů pro obecné účely: Dv2 Series 1-5](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-general#dv2-series)
* [Velikosti virtuálních počítačů optimalizovaných pro paměť: Dv2 Series 11-15](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-memory#dv2-series-11-15)
* [Velikosti virtuálních počítačů pro obecné účely: Av2 Series 1-8](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-general#av2-series)

### <a name="all-supported-regions-except-brazil-south-and-japan-west"></a>Všechny podporované oblasti kromě Brazílie – jih a Japonsko – západ

> [!Note]
> Pokud chcete získat identifikátor SKU pro použití v PowerShellu a dalších skriptech `Standard_` , přidejte na začátek všech SKU virtuálních počítačů v následujících tabulkách. Například `D12_v2` by se stala `Standard_D12_v2`.

| Typ clusteru | Hadoop | HBase | Interaktivní dotaz | Storm | Spark | ML Server | Kafka |
|---|---|---|---|---|---|---|---|
| Head: výchozí velikost virtuálního počítače | D12_v2 | D12_v2 | D13_v2 | A4_v2 | D12_v2 | D12_v2 | D3_v2 |
| Head: Doporučené velikosti virtuálních počítačů | D13_v2,<br/>D14_v2,<br/>D5_v2 | D3_v2,<br/>D4_v2,<br/>D12_v2 | D13_v2,<br/>D14_v2 | A4_v2,<br/>A8_v2 | D12_v2,<br/>D13_v2,<br/>D14_v2 | D12_v2,<br/>D13_v2,<br/>D14_v2 | D3_v2,<br/>D4_v2,<br/>D12_v2 |
| Pracovní proces: výchozí velikost virtuálního počítače | D4_v2 | D4_v2 | D14_v2 | D3_v2 | D13_v2 | D4_v2 | 4 D12_v2 s 2 S30 disky na zprostředkovatele |
| Pracovní proces: Doporučené velikosti virtuálních počítačů | D5_v2,<br>D12_v2,<br/>D13_v2 | D3_v2,<br/>D4_v2,<br/>D13_v2 | D13_v2,<br/>D14_v2 | D3_v2<br/>D4_v2,<br/>D12_v2 | D12_v2,<br>D13_v2,<br>D14_v2 | D4_v2,<br/>D12_v2,<br>D13_v2,<br>D14_v2 | D3_v2,<br/>D4_v2,<br/>DS3_v2,<br/>DS4_v2 |
| ZooKeeper: výchozí velikost virtuálního počítače |  | A4_v2 | A4_v2 | A4_v2 |  | A2_v2 | A4_v2 |
| ZooKeeper: Doporučené velikosti virtuálních počítačů |  | A4_v2, <br/>A8_v2, <br/>A2m_v2 | A4_v2,<br/>A8_v2,<br/>A2m_v2 | A4_v2,<br/>A2_v2,<br/>A8_v2 |  | A2_v2 | A4_v2,<br/> A8_v2,<br/>A2m_v2 |
| Služby ML: výchozí velikost virtuálního počítače |  |  |  |  |  | D4_v2 |  |
| Služby ML: Doporučená velikost virtuálního počítače |  |  |  |  |  | D4_v2,<br/> D12_v2,<br/> D13_v2,<br/>D14_v2 |  |

### <a name="brazil-south-and-japan-west-only"></a>Brazílie – pouze jih a Japonsko – západ

| Typ clusteru | Hadoop | HBase | Interaktivní dotaz | Storm | Spark | ML Services |
|---|---|---|---|---|---|---|
| Head: výchozí velikost virtuálního počítače | D12 | D12 | D13 | A4_v2 | D12 | D12 |
| Head: Doporučené velikosti virtuálních počítačů | D5_v2,<br/> D13_v2,<br/> D14_v2 | D3_v2,<br/> D4_v2,<br/> D12_v2 | D13_v2,<br/> D14_v2 | A4_v2,<br/> A8_v2 | D12_v2,<br/> D13_v2,<br/> D14_v2 | D12_v2,<br/> D13_v2,<br/> D14_v2 |
| Pracovní proces: výchozí velikost virtuálního počítače | D4 | D4 | D14 | D3 | D13 | D4 |
| Pracovní proces: Doporučené velikosti virtuálních počítačů | D5_v2,<br/> D12_v2,<br/> D13_v2 | D3_v2,<br/> D4_v2,<br/> D13_v2 | D13_v2,<br/> D14_v2 | D3_v2,<br/> D4_v2,<br/> D12_v2 | D12_v2,<br/> D13_v2,<br/> D14_v2 | D4_v2,<br/> D12_v2,<br/> D13_v2,<br/> D14_v2 |
| ZooKeeper: výchozí velikost virtuálního počítače |  | A4_v2 | A4_v2 | A4_v2 |  | A2_v2 |
| ZooKeeper: Doporučené velikosti virtuálních počítačů |  | A4_v2,<br/> A8_v2,<br/> A2m_v2 | A4_v2,<br/> A8_v2,<br/> A2m_v2 | A4_v2,<br/> A8_v2 |  | A2_v2 |
| Služby ML: výchozí velikosti virtuálních počítačů |  |  |  |  |  | D4 |
| Služby ML: Doporučené velikosti virtuálních počítačů |  |  |  |  |  | D4_v2,<br/> D12_v2,<br/> D13_v2,<br/> D14_v2 |

> [!NOTE]
> - Hlava se označuje jako *Nimbus* pro typ clusteru.
> - Pracovní proces je známý jako *správce* pro typ clusteru.
> - Pracovní proces je známý jako *oblast* pro typ clusteru HBA.

## <a name="next-steps"></a>Další postup

* [Co je Apache Hadoop komponenty a verze, které jsou k dispozici s HDInsight?](hdinsight-component-versioning.md)
