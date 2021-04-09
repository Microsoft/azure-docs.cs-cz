---
title: Apache Spark streamování s Apache Kafka – Azure HDInsight
description: Naučte se používat Apache Spark ke streamování dat do Apache Kafka pomocí DStreams. V tomto příkladu můžete streamovat data pomocí Jupyter Notebook ze Sparku ve službě HDInsight.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 11/21/2019
ms.openlocfilehash: 6ef11e9c7907f57b3b8de0a042e1035bce638cf4
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "104863271"
---
# <a name="apache-spark-streaming-dstream-example-with-apache-kafka-on-hdinsight"></a>Příklad streamování Apache Spark (DStream) s Apache Kafka v HDInsight

Naučte se používat [Apache Spark](https://spark.apache.org/) ke streamování dat do nebo z [Apache Kafka](https://kafka.apache.org/) v HDInsight pomocí [DStreams](https://spark.apache.org/docs/latest/api/java/org/apache/spark/streaming/dstream/DStream.html). V tomto příkladu se používá [Jupyter notebook](https://jupyter.org/) , který běží v clusteru Spark.

> [!NOTE]  
> Pomocí kroků v tomto dokumentu se vytvoří skupina prostředků Azure obsahující cluster Spark ve službě HDInsight i cluster Kafka ve službě HDInsight. Oba tyto clustery se nacházejí ve virtuální síti Azure, což umožňuje přímou komunikaci clusteru Spark s clusterem Kafka.
>
> Jakmile budete hotovi s kroky v tomto dokumentu, nezapomeňte clustery odstranit, abyste se vyhnuli nadbytečným poplatkům.

> [!IMPORTANT]  
> V tomto příkladu se používá DStreams, což je starší technologie pro streamování Sparku. Příklad, který používá novější funkce streamování Sparku, najdete v tématu [strukturované streamování Sparku s Apache Kafka](hdinsight-apache-kafka-spark-structured-streaming.md) dokumentem.

## <a name="create-the-clusters"></a>Vytvoření clusterů

Apache Kafka v HDInsight neposkytuje přístup ke zprostředkovatelům Kafka prostřednictvím veřejného Internetu. Cokoli, co se mluví do Kafka, musí být ve stejné virtuální síti Azure jako uzly v clusteru Kafka. V tomto příkladu se clustery Kafka a Spark nacházejí ve službě Azure Virtual Network. Následující diagram znázorňuje komunikaci mezi clustery:

:::image type="content" source="./media/hdinsight-apache-spark-with-kafka/apache-spark-kafka-vnet.png" alt-text="Diagram clusterů Spark a Kafka ve virtuální síti Azure" border="false":::

> [!NOTE]  
> I když je Kafka sám o komunikaci v rámci virtuální sítě, další služby v clusteru, jako jsou SSH a Ambari, jsou dostupné přes Internet. Další informace o veřejných portech dostupných ve službě HDInsight najdete v tématu [Porty a identifikátory URI používané službou HDInsight](hdinsight-hadoop-port-settings-for-services.md).

I když můžete vytvořit clustery Azure Virtual Network, Kafka a Spark ručně, je snazší použít šablonu Azure Resource Manager. Pomocí následujících kroků nasadíte clustery Azure Virtual Network, Kafka a Spark do svého předplatného Azure.

1. Pomocí následujícího tlačítka se přihlaste do Azure a otevřete šablonu na webu Azure Portal.

   <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-linux-based-kafka-spark-cluster-in-vnet-v4.1.json" target="_blank"><img src="./media/hdinsight-apache-spark-with-kafka/hdi-deploy-to-azure1.png" alt="Deploy to Azure button for new cluster"></a>

   Šablona Azure Resource Manager se nachází na adrese **https://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-kafka-spark-cluster-in-vnet-v4.1.json** .

   > [!WARNING]
   > Pokud chcete zajistit dostupnost Kafka v HDInsightu, musí cluster obsahovat aspoň tři pracovní uzly. Tato šablona vytvoří cluster Kafka se třemi pracovními uzly.

   Tato šablona vytvoří cluster HDInsight 3,6 pro Kafka i Spark.

1. K naplnění položek v části **vlastní nasazení** použijte následující informace:

   |Vlastnost |Hodnota |
   |---|---|
   |Skupina prostředků|Vytvořte skupinu nebo vyberte některou existující.|
   |Umístění|Vyberte umístění geograficky blízko vás.|
   |Základní název clusteru|Tato hodnota se používá jako základní název pro clustery Spark a Kafka. Například zadáním **hdistreaming** se vytvoří cluster Spark s názvem __Spark-Hdistreaming__ a cluster Kafka s názvem **Kafka-hdistreaming**.|
   |Uživatelské jméno přihlášení clusteru|Uživatelské jméno správce pro clustery Spark a Kafka.|
   |Heslo přihlášení clusteru|Heslo uživatele správce pro clustery Spark a Kafka.|
   |Uživatelské jméno SSH|Uživatel SSH, který se má vytvořit pro clustery Sparku a Kafka.|
   |Heslo SSH|Heslo pro uživatele SSH pro clustery Spark a Kafka|

   :::image type="content" source="./media/hdinsight-apache-spark-with-kafka/hdinsight-parameters.png" alt-text="Parametry vlastního nasazení HDInsight":::

1. Přečtěte si **podmínky a ujednání** a potom vyberte Souhlasím **s výše uvedenými podmínkami a ujednáními**.

1. Nakonec vyberte **koupit**. Vytvoření clusteru trvá přibližně 20 minut.

Po vytvoření prostředků se zobrazí stránka Souhrn.

:::image type="content" source="./media/hdinsight-apache-spark-with-kafka/hdinsight-group-blade.png" alt-text="Shrnutí skupiny prostředků pro virtuální síť a clustery":::

> [!IMPORTANT]  
> Všimněte si, že názvy clusterů HDInsight jsou **Spark-Base** a **Kafka-BASE**, kde Base je název, který jste zadali pro šablonu. Tyto názvy použijete v pozdějších krocích při připojování ke clusterům.

## <a name="use-the-notebooks"></a>Použití poznámkových bloků

Kód pro příklad popsaný v tomto dokumentu je k dispozici na adrese [https://github.com/Azure-Samples/hdinsight-spark-scala-kafka](https://github.com/Azure-Samples/hdinsight-spark-scala-kafka) .

## <a name="delete-the-cluster"></a>Odstranění clusteru

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Vzhledem k tomu, že kroky v tomto dokumentu vytvoří oba clustery ve stejné skupině prostředků Azure, můžete odstranit skupinu prostředků v Azure Portal. Odstraněním skupiny se odstraní všechny prostředky vytvořené pomocí tohoto dokumentu, Virtual Network Azure a účtu úložiště používaného clustery.

## <a name="next-steps"></a>Další kroky

V tomto příkladu jste zjistili, jak pomocí Sparku číst a zapisovat do Kafka. Pomocí následujících odkazů můžete zjistit další způsoby práce s Kafka:

* [Začínáme s Apache Kafka v HDInsight](kafka/apache-kafka-get-started.md)
* [Vytvoření repliky Apache Kafka v HDInsight pomocí nástroje MirrorMaker](kafka/apache-kafka-mirroring.md)
* [Použití Apache Storm s Apache Kafka v HDInsight](hdinsight-apache-storm-with-kafka.md)
