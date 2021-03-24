---
title: Apache Kafka zvýšení měřítka – Azure HDInsight
description: Zjistěte, jak nakonfigurovat spravované disky pro cluster Apache Kafka v prostředí Azure HDInsight, které zvýší škálovatelnost.
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/09/2019
ms.openlocfilehash: 9aa11be42aca59458fea0462a90b6aeb70df893d
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2021
ms.locfileid: "104863135"
---
# <a name="configure-storage-and-scalability-for-apache-kafka-on-hdinsight"></a>Konfigurace úložiště a škálovatelnosti pro platformu Apache Kafka v prostředí HDInsight

Naučte se konfigurovat počet spravovaných disků používaných [Apache Kafka](https://kafka.apache.org/) ve službě HDInsight.

Platforma Kafka ve službě HDInsight používá místní disky virtuálních počítačů v clusteru HDInsight. Protože je platforma Kafka náročná na V/V prostředky, k zajištění vysoké propustnosti a vyšší kapacity úložiště na každý uzel se využívají [Spravované disky Azure](../../virtual-machines/managed-disks-overview.md). Kdyby platforma Kafka využívala tradiční virtuální pevné disky (VHD), každý uzel by byl omezený na 1 TB. Díky spravovaným diskům můžete používat více disků, se kterými každý uzel v clusteru nabídne kapacitu až 16 TB.

Následující diagram porovnává platformu Kafka ve službě HDInsight před použitím spravovaných disků a s nimi:

:::image type="content" source="./media/apache-kafka-scalability/kafka-with-managed-disks-architecture.png" alt-text="Architektura Kafka se spravovanými disky" border="false":::

## <a name="configure-managed-disks-azure-portal"></a>Konfigurace spravovaných disků: portál Azure Portal

1. Postupujte podle kroků v tématu [Vytvoření clusteru HDInsight](../hdinsight-hadoop-create-linux-clusters-portal.md), kde se dozvíte, jaký je obecný postup pro vytvoření clusteru pomocí portálu. Nedokončujte proces vytváření portálu.

2. V části **Configuration & Price** nakonfigurujte počet disků pomocí pole __počet uzlů__ .

    > [!NOTE]  
    > Typ spravovaného disku může být buď __Standardní__ (HDD), nebo __Prémiový__ (SSD). Prémiové disky se používají u virtuálních počítačů řady DS a GS. Všechny ostatní typy virtuálních počítačů používají standardní disky.

    :::image type="content" source="./media/apache-kafka-scalability/azure-portal-cluster-configuration-pricing-kafka-disks.png" alt-text="oddíl velikost clusteru se zvýrazněnými disky na pracovní uzel" border="true":::

## <a name="configure-managed-disks-resource-manager-template"></a>Konfigurace spravovaných disků: šablony Resource Manageru

Pokud chcete nastavit počet disků, které využívají pracovní uzly v clusteru Kafka, použijte následující část šablony:

```json
"dataDisksGroups": [
    {
        "disksPerNode": "[variables('disksPerWorkerNode')]"
    }
    ],
```

Úplnou šablonu, která ukazuje, jak konfigurovat spravované disky, najdete na adrese [https://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-kafka-mirror-cluster-in-vnet-v2.1.json](https://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-kafka-mirror-cluster-in-vnet-v2.1.json) .

## <a name="next-steps"></a>Další kroky

Další informace o práci s Apache Kafka v HDInsight najdete v následujících dokumentech:

* [Vytvoření repliky Apache Kafka v HDInsight pomocí nástroje MirrorMaker](apache-kafka-mirroring.md)
* [Použití Apache Storm s Apache Kafka v HDInsight](../hdinsight-apache-storm-with-kafka.md)
* [Použití Apache Spark s Apache Kafka v HDInsight](../hdinsight-apache-spark-with-kafka.md)
* [Připojení k Apache Kafka prostřednictvím Azure Virtual Network](apache-kafka-connect-vpn-gateway.md)

* [Blog HDInsight na spravovaných discích s Apache Kafka](https://azure.microsoft.com/blog/announcing-public-preview-of-apache-kafka-on-hdinsight-with-azure-managed-disks/)
