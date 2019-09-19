---
title: Apache Kafka zvýšení měřítka – Azure HDInsight
description: Zjistěte, jak nakonfigurovat spravované disky pro cluster Apache Kafka v prostředí Azure HDInsight, které zvýší škálovatelnost.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/30/2018
ms.openlocfilehash: ef32a5d3a1439cded6cd092f076e7b26c178cb7a
ms.sourcegitcommit: fad368d47a83dadc85523d86126941c1250b14e2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/19/2019
ms.locfileid: "71122106"
---
# <a name="configure-storage-and-scalability-for-apache-kafka-on-hdinsight"></a>Konfigurace úložiště a škálovatelnosti pro platformu Apache Kafka v prostředí HDInsight

Naučte se konfigurovat počet spravovaných disků používaných [Apache Kafka](https://kafka.apache.org/) ve službě HDInsight.

Platforma Kafka ve službě HDInsight používá místní disky virtuálních počítačů v clusteru HDInsight. Protože je platforma Kafka náročná na V/V prostředky, k zajištění vysoké propustnosti a vyšší kapacity úložiště na každý uzel se využívají [Spravované disky Azure](../../virtual-machines/windows/managed-disks-overview.md). Kdyby platforma Kafka využívala tradiční virtuální pevné disky (VHD), každý uzel by byl omezený na 1 TB. Díky spravovaným diskům můžete používat více disků, se kterými každý uzel v clusteru nabídne kapacitu až 16 TB.

Následující diagram porovnává platformu Kafka ve službě HDInsight před použitím spravovaných disků a s nimi:

![Architektura Kafka se spravovanými disky](./media/apache-kafka-scalability/kafka-with-managed-disks-architecture.png)

## <a name="configure-managed-disks-azure-portal"></a>Konfigurace spravovaných disků: portál Azure

1. Postupujte podle kroků v tématu [Vytvoření clusteru HDInsight](../hdinsight-hadoop-create-linux-clusters-portal.md), kde se dozvíte, jaký je obecný postup pro vytvoření clusteru pomocí portálu. Proces vytvoření clusteru pomocí portálu nedokončujte.

2. V části __Velikost clusteru__ pomocí pole __Počet disků v pracovním uzlu__ nakonfigurujte počet disků.

    > [!NOTE]  
    > Typ spravovaného disku může být buď __Standardní__ (HDD), nebo __Prémiový__ (SSD). Prémiové disky se používají u virtuálních počítačů řady DS a GS. Všechny ostatní typy virtuálních počítačů používají standardní disky.

    ![oddíl velikost clusteru se zvýrazněnými disky na pracovní uzel](./media/apache-kafka-scalability/set-managed-disks-portal.png)

## <a name="configure-managed-disks-resource-manager-template"></a>Konfigurace spravovaných disků: Šablona Resource Manageru

Pokud chcete nastavit počet disků, které využívají pracovní uzly v clusteru Kafka, použijte následující část šablony:

```json
"dataDisksGroups": [
    {
        "disksPerNode": "[variables('disksPerWorkerNode')]"
    }
    ],
```

Úplnou šablonu, která ukazuje, jak konfigurovat spravované disky, najdete na adrese [https://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-kafka-mirror-cluster-in-vnet-v2.1.json](https://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-kafka-mirror-cluster-in-vnet-v2.1.json).

## <a name="next-steps"></a>Další kroky

Další informace o práci s Apache Kafka v HDInsight najdete v následujících dokumentech:

* [Vytvoření repliky Apache Kafka v HDInsight pomocí nástroje MirrorMaker](apache-kafka-mirroring.md)
* [Použití Apache Storm s Apache Kafka v HDInsight](../hdinsight-apache-storm-with-kafka.md)
* [Použití Apache Spark s Apache Kafka v HDInsight](../hdinsight-apache-spark-with-kafka.md)
* [Připojení k Apache Kafka prostřednictvím Azure Virtual Network](apache-kafka-connect-vpn-gateway.md)

* [Blog HDInsight na spravovaných discích s Apache Kafka](https://azure.microsoft.com/blog/announcing-public-preview-of-apache-kafka-on-hdinsight-with-azure-managed-disks/)
