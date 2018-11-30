---
title: Streamování s využitím Apache Kafka – Azure HDInsight Apache Sparku
description: Další informace o použití Apache Spark pro streamování dat do nebo ze systému Apache Kafka pomocí diskretizovanými streamy. V tomto příkladu budete streamovat data pomocí poznámkového bloku Jupyter ze Sparku ve službě HDInsight.
keywords: Příklad kafka, kafka zookeeper, spark, datové proudy kafka, spark, kafka příklad streamování
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/06/2018
ms.author: hrasheed
ms.openlocfilehash: 110357a7a84aa8cc6bd7dcb5bbef9ca02879a9cb
ms.sourcegitcommit: 345b96d564256bcd3115910e93220c4e4cf827b3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/28/2018
ms.locfileid: "52494288"
---
# <a name="apache-spark-streaming-dstream-example-with-apache-kafka-on-hdinsight"></a>Příklad (DStream) s Apache Kafka v HDInsight streamování Apache Sparku

Další informace o použití [Apache Spark](https://spark.apache.org/) pro streamování dat do nebo z celkového počtu [Apache Kafka](https://kafka.apache.org/) v HDInsight pomocí [diskretizovanými streamy](https://spark.apache.org/docs/latest/api/java/org/apache/spark/streaming/dstream/DStream.html). Tento příklad používá [Poznámkový blok Jupyter](https://jupyter.org/) , který běží na clusteru Spark.

> [!NOTE]
> Pomocí kroků v tomto dokumentu se vytvoří skupina prostředků Azure obsahující cluster Spark ve službě HDInsight i cluster Kafka ve službě HDInsight. Oba tyto clustery se nacházejí ve virtuální síti Azure, což umožňuje přímou komunikaci clusteru Spark s clusterem Kafka.
>
> Jakmile budete hotovi s kroky v tomto dokumentu, nezapomeňte clustery odstranit, abyste se vyhnuli nadbytečným poplatkům.

> [!IMPORTANT]
> Tento příklad používá diskretizovanými streamy, která je starší technologie streamování Sparku. Příklad použití novější Spark streaming funkce, najdete v článku [strukturovaného streamování Sparku se systémem Kafka](hdinsight-apache-kafka-spark-structured-streaming.md) dokumentu.

## <a name="create-the-clusters"></a>Vytvoření clusterů

Apache Kafka ve službě HDInsight neposkytuje přístup ke zprostředkovatelům systému Kafka přes veřejný internet. Nic, která komunikuje Kafka musí být ve stejné virtuální síti Azure jako uzly v clusteru Kafka. V tomto příkladu clustery Kafka i Spark nacházejí ve službě Azure virtual network. Následující diagram znázorňuje tok komunikace mezi clustery:

![Diagram clusterů Spark a Kafka ve virtuální síti Azure](./media/hdinsight-apache-spark-with-kafka/spark-kafka-vnet.png)

> [!NOTE]
> I když Kafka samotného je omezený na komunikaci v rámci virtuální sítě, další služby v clusteru, jako jsou SSH a Ambari je možný přes internet. Další informace o veřejných portech dostupných ve službě HDInsight najdete v tématu [Porty a identifikátory URI používané službou HDInsight](hdinsight-hadoop-port-settings-for-services.md).

I když můžete vytvořit virtuální síť Azure, Kafka, a clustery Spark ručně, je jednodušší použít šablonu Azure Resource Manageru. V následujícím návodu pro nasazení virtuální sítě Azure, Kafka a clustery se svým předplatným Azure Spark.

1. Pomocí následujícího tlačítka se přihlaste do Azure a otevřete šablonu na webu Azure Portal.
    
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-linux-based-kafka-spark-cluster-in-vnet-v4.1.json" target="_blank"><img src="./media/hdinsight-apache-spark-with-kafka/deploy-to-azure.png" alt="Deploy to Azure"></a>
    
    Šablona Azure Resource Manageru se nachází na adrese **https://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-kafka-spark-cluster-in-vnet-v4.1.json**.

    > [!WARNING]
    > Pokud chcete zajistit dostupnost Kafka v HDInsightu, musí cluster obsahovat aspoň tři pracovní uzly. Tato šablona vytvoří cluster Kafka se třemi pracovními uzly.

    Tato šablona vytvoří cluster HDInsight 3.6 Kafka a Spark.

2. Pomocí následujících informací vyplňte položky na **vlastní nasazení** části:
   
    ![HDInsight vlastního nasazení](./media/hdinsight-apache-spark-with-kafka/parameters.png)
   
    * **Skupina prostředků**: Vytvořte skupinu nebo vyberte existující. Tato skupina obsahuje HDInsight cluster.

    * **Umístění**: Vyberte umístění geograficky blízko vás.

    * **Základní název clusteru**: Tato hodnota se používá jako základní název pro Spark a clusterů Kafka. Například zadání **hdistreaming** vytvoří Spark cluster __spark hdistreaming__ a cluster Kafka s názvem **kafka hdistreaming**.

    * **Uživatelské jméno přihlášení clusteru**: uživatelské jméno správce clusterů Spark a Kafka.

    * **Heslo přihlášení clusteru**: heslo správce pro clusterů Spark a Kafka.

    * **Uživatelské jméno SSH**: SSH uživatele k vytvoření clusterů Spark a Kafka.

    * **Heslo SSH**: heslo pro uživatele SSH pro clusterů Spark a Kafka.

3. Přečtěte si **Podmínky a ujednání** a pak vyberte **Souhlasím s podmínkami a ujednáními uvedenými nahoře**.

4. Nakonec vyberte **nákupní**. Vytvoření clusteru trvá přibližně 20 minut.

Po vytvoření prostředků se zobrazí stránce souhrnu.

![Souhrn pro virtuální síť a clustery skupiny prostředků](./media/hdinsight-apache-spark-with-kafka/groupblade.png)

> [!IMPORTANT]
> Všimněte si, že jsou názvy clusterů HDInsight **spark BASENAME** a **kafka BASENAME**, kde BASENAME je název, který jste zadali v šabloně. Tyto názvy používat v dalších krocích při připojování ke clusteru.

## <a name="use-the-notebooks"></a>Použití poznámkových bloků

Kód příkladu popisovaného v tomto dokumentu je dostupný na adrese [https://github.com/Azure-Samples/hdinsight-spark-scala-kafka](https://github.com/Azure-Samples/hdinsight-spark-scala-kafka).

Abyste mohli absolvovat tento příklad, postupujte podle kroků v `README.md`.

## <a name="delete-the-cluster"></a>Odstranění clusteru

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Vzhledem k tomu, že kroky v tomto dokumentu vytvořte obou clusterech ve stejné skupině prostředků Azure, můžete odstranit skupinu prostředků na webu Azure Portal. Odstraňuje se skupina se odstraní všechny prostředky vytvořené podle tohoto dokumentu, Azure Virtual Network a účet úložiště používané clustery.

## <a name="next-steps"></a>Další postup

V tomto příkladu jste zjistili, jak se sparkem ke čtení a zápis do systému Kafka. Zjistit další způsoby, jak pracovat s využitím Kafka pomocí následujících odkazů:

* [Začínáme s Apache Kafka v HDInsight](kafka/apache-kafka-get-started.md)
* [Vytvoření repliky Apache Kafka v HDInsight pomocí Mirrormakeru](kafka/apache-kafka-mirroring.md)
* [Použití Apache Stormu s Apache Kafka v HDInsight](hdinsight-apache-storm-with-kafka.md)

