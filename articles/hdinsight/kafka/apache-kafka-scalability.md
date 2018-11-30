---
title: Apache Kafka zvýšit škálování – Azure HDInsight
description: Zjistěte, jak nakonfigurovat spravované disky pro cluster Apache Kafka v prostředí Azure HDInsight, které zvýší škálovatelnost.
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/30/2018
ms.openlocfilehash: 1df23a35e16d4ba3562ebbc9d89a7a8003afc784
ms.sourcegitcommit: 345b96d564256bcd3115910e93220c4e4cf827b3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/28/2018
ms.locfileid: "52498943"
---
# <a name="configure-storage-and-scalability-for-apache-kafka-on-hdinsight"></a>Konfigurace úložiště a škálovatelnosti pro platformu Apache Kafka v prostředí HDInsight

Naučte se konfigurovat spravované disky používané systémem [Apache Kafka](https://kafka.apache.org/) v HDInsight.

Platforma Kafka ve službě HDInsight používá místní disky virtuálních počítačů v clusteru HDInsight. Protože je platforma Kafka náročná na V/V prostředky, k zajištění vysoké propustnosti a vyšší kapacity úložiště na každý uzel se využívá služba [Azure Managed Disks](../../virtual-machines/windows/managed-disks-overview.md). Kdyby platforma Kafka využívala tradiční virtuální pevné disky (VHD), každý uzel by byl omezený na 1 TB. Díky službě Managed Disks můžete používat více disků, se kterými každý uzel v clusteru nabídne kapacitu až 16 TB.

Následující diagram porovnává platformu Kafka ve službě HDInsight před použitím spravovaných disků a s nimi:

![Diagram zobrazující platformu Kafka ve službě HDInsight při použití jednoho virtuálního pevného disku a při použití několika spravovaných disků v každém virtuálním počítači](./media/apache-kafka-scalability/kafka-with-managed-disks-architecture.png)

## <a name="configure-managed-disks-azure-portal"></a>Konfigurace spravovaných disků: portál Azure Portal

1. Postupujte podle kroků v tématu [Vytvoření clusteru HDInsight](../hdinsight-hadoop-create-linux-clusters-portal.md), kde se dozvíte, jaký je obecný postup pro vytvoření clusteru pomocí portálu. Proces vytvoření clusteru pomocí portálu nedokončujte.

2. V části __Velikost clusteru__ pomocí pole __Počet disků v pracovním uzlu__ nakonfigurujte počet disků.

    > [!NOTE]
    > Typ spravovaného disku může být buď __Standardní__ (HDD), nebo __Prémiový__ (SSD). Prémiové disky se používají u virtuálních počítačů řady DS a GS. Všechny ostatní typy virtuálních počítačů používají standardní disky.

    ![Obrázek části Velikost clusteru se zvýrazněným počtem disků v pracovním uzlu](./media/apache-kafka-scalability/set-managed-disks-portal.png)

## <a name="configure-managed-disks-resource-manager-template"></a>Konfigurace spravovaných disků: šablony Resource Manageru

Pokud chcete nastavit počet disků, které využívají pracovní uzly v clusteru Kafka, použijte následující část šablony:

```json
"dataDisksGroups": [
    {
        "disksPerNode": "[variables('disksPerWorkerNode')]"
    }
    ],
```

Můžete najít úplnou šablonu, která ukazuje, jak nakonfigurovat spravované disky na [ https://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-kafka-mirror-cluster-in-vnet-v2.1.json ](https://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-kafka-mirror-cluster-in-vnet-v2.1.json).

## <a name="next-steps"></a>Další postup

Další informace o práci s Apache Kafka v HDInsight najdete v následujících dokumentech:

* [Vytvoření repliky Apache Kafka v HDInsight pomocí Mirrormakeru](apache-kafka-mirroring.md)
* [Použití Apache Stormu s Apache Kafka v HDInsight](../hdinsight-apache-storm-with-kafka.md)
* [Použití Apache Sparku s využitím Apache Kafka v HDInsight](../hdinsight-apache-spark-with-kafka.md)
* [Připojení k Apache Kafka přes virtuální síť Azure](apache-kafka-connect-vpn-gateway.md)

* [Blogu HDInsight o spravovaných disků s využitím Apache Kafka](https://azure.microsoft.com/blog/announcing-public-preview-of-apache-kafka-on-hdinsight-with-azure-managed-disks/)
