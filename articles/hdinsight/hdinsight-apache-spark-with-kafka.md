---
title: Apache Spark streamování s Kafka - Azure HDInsight | Microsoft Docs
description: Další informace o použití Spark Apache Spark na datový proud dat do nebo z Apache Kafka pomocí DStreams. V tomto příkladu stream dat pomocí poznámkového bloku Jupyter z Spark v HDInsight.
keywords: Příklad kafka, kafka zookeeper, spark, streamování kafka, například kafka vysílání datového proudu spark
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
ms.assetid: dd8f53c1-bdee-4921-b683-3be4c46c2039
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: ''
ms.topic: conceptual
ms.date: 02/23/2018
ms.author: larryfr
ms.openlocfilehash: a9463b5983b5f41683a5cfe416ca125bf2810062
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
ms.locfileid: "31402375"
---
# <a name="apache-spark-streaming-dstream-example-with-kafka-on-hdinsight"></a>Apache Spark streamování (DStream) příklad s Kafka v HDInsight

Další informace o použití Spark Apache Spark na datový proud dat do nebo z Apache Kafka v HDInsight pomocí DStreams. Tento příklad používá poznámkového bloku Jupyter, která běží na clusteru Spark.

> [!NOTE]
> Kroky v tomto dokumentu vytvořte skupinu prostředků Azure, která obsahuje oba Spark v HDInsight a Kafka na clusteru HDInsight. Tyto clustery jsou obě nachází v rámci virtuální síť Azure, což umožňuje clusteru Spark přímo komunikovat s Kafka clusteru.
>
> Po dokončení kroků v tomto dokumentu, nezapomeňte odstranit clustery nadbytečné náklady.

> [!IMPORTANT]
> Tento příklad používá DStreams, což je technologie streamování starší Spark. Příklad, který používá novější Spark streamování funkce, najdete v článku [strukturovaných streamování Spark s Kafka](hdinsight-apache-kafka-spark-structured-streaming.md) dokumentu.

## <a name="create-the-clusters"></a>Vytváření clusterů

Apache Kafka v HDInsight neposkytuje přístup k zprostředkovatelé Kafka prostřednictvím veřejného Internetu. Všechno, co komunikuje se Kafka musí být ve stejné virtuální síti Azure jako uzly v clusteru Kafka. V tomto příkladu jsou Kafka i Spark clusterů umístěné v virtuální sítě Azure. Následující diagram znázorňuje tok komunikace mezi clustery:

![Diagram clustery Spark a Kafka v virtuální sítě Azure](./media/hdinsight-apache-spark-with-kafka/spark-kafka-vnet.png)

> [!NOTE]
> I když Kafka, samotné je omezený na komunikaci v rámci virtuální sítě, dalších služeb v clusteru, například SSH a Ambari jsou přístupné přes internet. Další informace o veřejné porty, které jsou k dispozici s HDInsight naleznete v tématu [porty a identifikátory URI používají v prostředí HDInsight](hdinsight-hadoop-port-settings-for-services.md).

Když vytvoříte virtuální síť Azure, Kafka, a clustery Spark ručně, je jednodušší použít šablonu Azure Resource Manager. Použijte následující kroky k nasazení virtuální sítě Azure, Kafka a clustery k předplatnému Azure z Spark.

1. Na následující tlačítko použijte pro přihlášení do Azure a otevřete šablonu na portálu Azure.
    
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-linux-based-kafka-spark-cluster-in-vnet-v4.1.json" target="_blank"><img src="./media/hdinsight-apache-spark-with-kafka/deploy-to-azure.png" alt="Deploy to Azure"></a>
    
    Šablona Azure Resource Manager je umístěna ve **https://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-kafka-spark-cluster-in-vnet-v4.1.json**.

    > [!WARNING]
    > Pokud chcete zajistit dostupnost Kafka v HDInsightu, musí cluster obsahovat aspoň tři pracovní uzly. Tato šablona vytvoří cluster Kafka, který obsahuje tři uzly pracovního procesu.

    Tato šablona vytvoří cluster služby HDInsight 3.6 Kafka a Spark.

2. Následující informace slouží k naplnění položek na **vlastní nasazení** části:
   
    ![HDInsight vlastní nasazení](./media/hdinsight-apache-spark-with-kafka/parameters.png)
   
    * **Skupina prostředků**: vytvoření skupiny nebo vyberte nějaký existující. Tato skupina obsahuje clusteru HDInsight.

    * **Umístění**: Vyberte umístění geograficky blízko vás.

    * **Základní název clusteru**: Tato hodnota se používá jako základní název pro Spark a Kafka clusterů. Například zadáním **hdi** vytvoří cluster Spark s názvem __spark hdi__ a Kafka clusteru s názvem **kafka hdi**.

    * **Uživatelské jméno přihlášení clusteru**: uživatelské jméno správce pro clustery Spark a Kafka.

    * **Heslo pro přihlášení clusteru**: uživatelské heslo správce pro clustery Spark a Kafka.

    * **Uživatelské jméno SSH**: SSH, aby uživatel vytvořil pro clustery Spark a Kafka.

    * **Heslo SSH**: heslo pro uživatele SSH pro clustery Spark a Kafka.

3. Pro čtení **podmínky a ujednání**a potom vyberte **souhlasím s podmínkami a ujednáními výše uvedených**.

4. Nakonec zkontrolujte **připnout na řídicí panel** a pak vyberte **nákupu**. Chcete-li vytvořit clustery trvá asi 20 minut.

Po vytvoření prostředky se zobrazí souhrnná stránka.

![Skupina prostředků souhrnu pro virtuální síť a clustery](./media/hdinsight-apache-spark-with-kafka/groupblade.png)

> [!IMPORTANT]
> Všimněte si, že jsou názvy clusterů HDInsight **spark BASENAME** a **kafka BASENAME**, kde BASENAME je jméno, které jste zadali v šabloně. Názvy těchto používat v dalších krocích při připojování k clustery.

## <a name="use-the-notebooks"></a>Použití poznámkových bloků

Kód pro tento příklad popsané v tomto dokumentu je k dispozici na [ https://github.com/Azure-Samples/hdinsight-spark-scala-kafka ](https://github.com/Azure-Samples/hdinsight-spark-scala-kafka).

Abyste mohli dokončit tento příklad, postupujte podle kroků v `README.md`.

## <a name="delete-the-cluster"></a>Odstranění clusteru

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Vzhledem k tomu, že kroky v tomto dokumentu vytvořit oba clustery ve stejné skupině prostředků Azure, můžete odstranit skupinu prostředků na portálu Azure. Odstraňuje se skupina odebere všechny prostředky, které jsou vytvořené pomocí následujících tento dokument, Azure Virtual Network a účet úložiště, které jsou používané clustery.

## <a name="next-steps"></a>Další postup

V tomto příkladu jste zjistili, jak používat Spark ke čtení a zápisu do Kafka. Chcete-li zjistit další způsoby, jak pracovat s Kafka pomocí následujících odkazů:

* [Začínáme s Apache Kafka v HDInsight](kafka/apache-kafka-get-started.md)
* [Vytvoření repliky Kafka ve službě HDInsight pomocí MirrorMakeru](kafka/apache-kafka-mirroring.md)
* [Použití Apache Stormu se systémem Kafka ve službě HDInsight](hdinsight-apache-storm-with-kafka.md)

