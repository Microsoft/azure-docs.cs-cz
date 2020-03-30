---
title: Streamování Apache Spark pomocí Apache Kafka - Azure HDInsight
description: Naučte se používat Apache Spark k streamování dat do Apache Kafka nebo z Apache Kafka pomocí DStreams. V tomto příkladu budete streamovat data pomocí poznámkového bloku Jupyter ze Sparku ve službě HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 11/21/2019
ms.openlocfilehash: d868cdd346c79cf77d4f8c1ea6e4b20adcd99b6c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74327400"
---
# <a name="apache-spark-streaming-dstream-example-with-apache-kafka-on-hdinsight"></a>Příklad streamování Apache Spark (DStream) s Apache Kafka na HDInsight

Naučte se používat [Apache Spark](https://spark.apache.org/) k streamování dat do apache [kafky](https://kafka.apache.org/) nebo z ní na HDInsight pomocí [DStreams](https://spark.apache.org/docs/latest/api/java/org/apache/spark/streaming/dstream/DStream.html). Tento příklad používá [poznámkový blok Jupyter,](https://jupyter.org/) který běží v clusteru Spark.

> [!NOTE]  
> Pomocí kroků v tomto dokumentu se vytvoří skupina prostředků Azure obsahující cluster Spark ve službě HDInsight i cluster Kafka ve službě HDInsight. Oba tyto clustery se nacházejí ve virtuální síti Azure, což umožňuje přímou komunikaci clusteru Spark s clusterem Kafka.
>
> Jakmile budete hotovi s kroky v tomto dokumentu, nezapomeňte clustery odstranit, abyste se vyhnuli nadbytečným poplatkům.

> [!IMPORTANT]  
> Tento příklad používá DStreams, což je starší technologie streamování Spark. Příklad, který používá novější funkce streamování Spark, najdete v dokumentu [Strukturované streamování Spark s Apache Kafka.](hdinsight-apache-kafka-spark-structured-streaming.md)

## <a name="create-the-clusters"></a>Vytvoření clusterů

Apache Kafka na HDInsight neposkytuje přístup k makléřům Kafka přes veřejný internet. Cokoliv, co mluví s Kafka musí být ve stejné virtuální síti Azure jako uzly v clusteru Kafka. V tomto příkladu clustery Kafka a Spark jsou umístěny ve virtuální síti Azure. Následující diagram znázorňuje, jak komunikace proutí mezi clustery:

![Diagram clusterů Spark a Kafka ve virtuální síti Azure](./media/hdinsight-apache-spark-with-kafka/apache-spark-kafka-vnet.png)

> [!NOTE]  
> Ačkoli kafka sama o sobě je omezena na komunikaci v rámci virtuální sítě, další služby v clusteru, jako je SSH a Ambari lze přistupovat přes internet. Další informace o veřejných portech dostupných ve službě HDInsight najdete v tématu [Porty a identifikátory URI používané službou HDInsight](hdinsight-hadoop-port-settings-for-services.md).

Zatímco virtuální síť Azure, clustery Kafka a Spark můžete vytvořit ručně, je jednodušší použít šablonu Azure Resource Manager. Pomocí následujících kroků nasadit virtuální síť Azure, Kafka a Spark clustery do předplatného Azure.

1. Pomocí následujícího tlačítka se přihlaste do Azure a otevřete šablonu na webu Azure Portal.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-linux-based-kafka-spark-cluster-in-vnet-v4.1.json" target="_blank"><img src="./media/hdinsight-apache-spark-with-kafka/hdi-deploy-to-azure1.png" alt="Deploy to Azure button for new cluster"></a>

    Šablona Azure Resource Manager **https://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-kafka-spark-cluster-in-vnet-v4.1.json**je umístěna na adrese .

    > [!WARNING]  
    > Pokud chcete zajistit dostupnost Kafka v HDInsightu, musí cluster obsahovat aspoň tři pracovní uzly. Tato šablona vytvoří cluster Kafka se třemi pracovními uzly.

    Tato šablona vytvoří cluster HDInsight 3.6 pro Kafka i Spark.

1. K vyplnění položek v části **Vlastní nasazení** použijte následující informace:

    |Vlastnost |Hodnota |
    |---|---|
    |Skupina prostředků|Vytvořte skupinu nebo vyberte existující skupinu.|
    |Umístění|Vyberte umístění geograficky blízko vás.|
    |Název základního clusteru|Tato hodnota se používá jako základní název clusterů Spark a Kafka. Například zadáním **hdistreaming** vytvoří cluster Spark s názvem __spark-hdistreaming__ a cluster Kafka s názvem **kafka-hdistreaming**.|
    |Uživatelské jméno přihlášení clusteru|Uživatelské jméno správce clusterů Spark a Kafka.|
    |Heslo přihlášení clusteru|Heslo uživatele správce pro clustery Spark a Kafka.|
    |Uživatelské jméno SSH|Uživatel SSH vytvořit pro clustery Spark a Kafka.|
    |Heslo SSH|Heslo pro uživatele SSH pro clustery Spark a Kafka.|

    ![Parametry vlastního nasazení HDInsight](./media/hdinsight-apache-spark-with-kafka/hdinsight-parameters.png)

1. Přečtěte si **obchodní podmínky**a pak vyberte Souhlasím s **výše uvedenými podmínkami**.

1. Nakonec vyberte **možnost Koupit**. Vytvoření clusterů trvá přibližně 20 minut.

Po vytvoření prostředků se zobrazí souhrnná stránka.

![Souhrn skupiny prostředků pro virtuální síť a clustery](./media/hdinsight-apache-spark-with-kafka/hdinsight-group-blade.png)

> [!IMPORTANT]  
> Všimněte si, že názvy clusterů HDInsight jsou **spark-BASENAME** a **kafka-BASENAME**, kde BASENAME je název, který jste zadali do šablony. Tyto názvy se používají v pozdějších krocích při připojování ke clusterům.

## <a name="use-the-notebooks"></a>Použití poznámkových bloků

Kód příkladu popsaného v tomto dokumentu [https://github.com/Azure-Samples/hdinsight-spark-scala-kafka](https://github.com/Azure-Samples/hdinsight-spark-scala-kafka)je k dispozici na adrese .

## <a name="delete-the-cluster"></a>Odstranění clusteru

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Vzhledem k tomu, že kroky v tomto dokumentu vytvořit oba clustery ve stejné skupině prostředků Azure, můžete odstranit skupinu prostředků na webu Azure Portal. Odstraněním skupiny odeberete všechny prostředky vytvořené podle tohoto dokumentu, virtuální sítě Azure a účtu úložiště používaného clustery.

## <a name="next-steps"></a>Další kroky

V tomto příkladu jste se naučili používat Spark ke čtení a zápisu do Kafky. Pomocí následujících odkazů můžete zjistit další způsoby práce s Kafkou:

* [Začínáme s Apache Kafka na HDInsightu](kafka/apache-kafka-get-started.md)
* [Pomocí MirrorMakeru vytvořte repliku Apache Kafka na HDInsightu](kafka/apache-kafka-mirroring.md)
* [Použijte Apache Storm s Apache Kafka na HDInsight](hdinsight-apache-storm-with-kafka.md)
