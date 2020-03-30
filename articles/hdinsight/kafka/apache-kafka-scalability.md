---
title: Apache Kafka zvýšit měřítko - Azure HDInsight
description: Zjistěte, jak nakonfigurovat spravované disky pro cluster Apache Kafka v prostředí Azure HDInsight, které zvýší škálovatelnost.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/09/2019
ms.openlocfilehash: 56c25b7c77809a5cb7f4e539cff8e1815cd9976f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77031697"
---
# <a name="configure-storage-and-scalability-for-apache-kafka-on-hdinsight"></a>Konfigurace úložiště a škálovatelnosti pro platformu Apache Kafka v prostředí HDInsight

Přečtěte si, jak nakonfigurovat počet spravovaných disků používaných [Apache Kafka](https://kafka.apache.org/) na HDInsight.

Platforma Kafka ve službě HDInsight používá místní disky virtuálních počítačů v clusteru HDInsight. Protože je platforma Kafka náročná na V/V prostředky, k zajištění vysoké propustnosti a vyšší kapacity úložiště na každý uzel se využívají [Spravované disky Azure](../../virtual-machines/windows/managed-disks-overview.md). Kdyby platforma Kafka využívala tradiční virtuální pevné disky (VHD), každý uzel by byl omezený na 1 TB. Díky spravovaným diskům můžete používat více disků, se kterými každý uzel v clusteru nabídne kapacitu až 16 TB.

Následující diagram porovnává platformu Kafka ve službě HDInsight před použitím spravovaných disků a s nimi:

![kafka s architekturou spravovaných disků](./media/apache-kafka-scalability/kafka-with-managed-disks-architecture.png)

## <a name="configure-managed-disks-azure-portal"></a>Konfigurace spravovaných disků: portál Azure Portal

1. Postupujte podle kroků v tématu [Vytvoření clusteru HDInsight](../hdinsight-hadoop-create-linux-clusters-portal.md), kde se dozvíte, jaký je obecný postup pro vytvoření clusteru pomocí portálu. Nedokončujte proces vytváření portálu.

2. V části **Konfigurace & Ceny** použijte pole Počet __uzlů__ ke konfiguraci počtu disků.

    > [!NOTE]  
    > Typ spravovaného disku může být buď __Standardní__ (HDD), nebo __Prémiový__ (SSD). Prémiové disky se používají u virtuálních počítačů řady DS a GS. Všechny ostatní typy virtuálních počítačů používají standardní disky.

    ![Oddíl velikosti clusteru se zvýrazněnými disky na pracovní uzel](./media/apache-kafka-scalability/azure-portal-cluster-configuration-pricing-kafka-disks.png)

## <a name="configure-managed-disks-resource-manager-template"></a>Konfigurace spravovaných disků: šablony Resource Manageru

Pokud chcete nastavit počet disků, které využívají pracovní uzly v clusteru Kafka, použijte následující část šablony:

```json
"dataDisksGroups": [
    {
        "disksPerNode": "[variables('disksPerWorkerNode')]"
    }
    ],
```

Kompletní šablonu, která ukazuje, jak konfigurovat spravované [https://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-kafka-mirror-cluster-in-vnet-v2.1.json](https://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-kafka-mirror-cluster-in-vnet-v2.1.json)disky, naleznete na adrese .

## <a name="next-steps"></a>Další kroky

Další informace o práci s Apache Kafka na HDInsight, naleznete v následujících dokumentech:

* [Pomocí MirrorMakeru vytvořte repliku Apache Kafka na HDInsightu](apache-kafka-mirroring.md)
* [Použijte Apache Storm s Apache Kafka na HDInsight](../hdinsight-apache-storm-with-kafka.md)
* [Používejte Apache Spark s Apache Kafka na HDInsight](../hdinsight-apache-spark-with-kafka.md)
* [Připojení k Apache Kafka prostřednictvím virtuální sítě Azure](apache-kafka-connect-vpn-gateway.md)

* [HDInsight blog na spravovaných discích s Apache Kafka](https://azure.microsoft.com/blog/announcing-public-preview-of-apache-kafka-on-hdinsight-with-azure-managed-disks/)
