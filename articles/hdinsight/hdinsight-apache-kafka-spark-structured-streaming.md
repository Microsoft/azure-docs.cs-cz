---
title: Strukturované streamování Apache Sparku s využitím systému Kafka – Azure HDInsight | Microsoft Docs
description: Zjistěte, jak pomocí streamování Apache Sparku (DStream) přenášet data do nebo ze systému Apache Kafka. V tomto příkladu budete streamovat data pomocí poznámkového bloku Jupyter ze Sparku ve službě HDInsight.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: cgronlun
editor: cgronlun
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: ''
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 04/04/2018
ms.author: larryfr
ms.openlocfilehash: 49c13bbea537d7de60ecf509bc28675191c0b34d
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2018
---
# <a name="use-spark-structured-streaming-with-kafka-on-hdinsight"></a>Použití strukturovaného streamování Sparku se systémem Kafka ve službě HDInsight

Zjistěte, jak pomocí strukturovaného streamování Sparku číst data ze systému Apache Kafka ve službě Azure HDInsight.

Strukturované streamování Sparku je modul pro zpracování datových proudů založený na Spark SQL. Umožňuje zrychlit streamované i dávkové výpočty se statickými daty. Další informace o strukturovaném streamování najdete v [průvodci programováním pro strukturované streamování](http://spark.apache.org/docs/2.2.0/structured-streaming-programming-guide.html) na webu Apache.org.

> [!IMPORTANT]
> V tomto příkladu se používá Spark 2.2 ve službě HDInsight 3.6.
>
> Pomocí kroků v tomto dokumentu se vytvoří skupina prostředků Azure obsahující cluster Spark ve službě HDInsight i cluster Kafka ve službě HDInsight. Oba tyto clustery se nacházejí ve virtuální síti Azure, což umožňuje přímou komunikaci clusteru Spark s clusterem Kafka.
>
> Jakmile budete hotovi s kroky v tomto dokumentu, nezapomeňte clustery odstranit, abyste se vyhnuli nadbytečným poplatkům.

## <a name="create-the-clusters"></a>Vytvoření clusterů

Apache Kafka ve službě HDInsight neposkytuje přístup ke zprostředkovatelům systému Kafka přes veřejný internet. Cokoli, co využívá systém Kafka, musí být ve stejné virtuální síti Azure. V tomto kurzu se clustery Kafka i Spark nacházejí ve stejné virtuální síti Azure. 

Následující diagram znázorňuje tok komunikace mezi Sparkem a systémem Kafka:

![Diagram clusterů Spark a Kafka ve virtuální síti Azure](./media/hdinsight-apache-spark-with-kafka/spark-kafka-vnet.png)

> [!NOTE]
> Komunikace služby Kafka je omezená na virtuální síť. Další služby v clusteru, jako jsou SSH a Ambari, jsou přístupné přes internet. Další informace o veřejných portech dostupných ve službě HDInsight najdete v tématu [Porty a identifikátory URI používané službou HDInsight](hdinsight-hadoop-port-settings-for-services.md).

Pro usnadnění práce se v následujících krocích k vytvoření clusterů Kafka a Spark ve virtuální síti používá šablona Azure Resource Manageru.

1. Pomocí následujícího tlačítka se přihlaste do Azure a otevřete šablonu na webu Azure Portal.
    
    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure-Samples%2Fhdinsight-spark-kafka-structured-streaming%2Fmaster%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-apache-spark-with-kafka/deploy-to-azure.png" alt="Deploy to Azure"></a>
    
    Šablona Azure Resource Manageru se nachází na adrese **https://raw.githubusercontent.com/Azure-Samples/hdinsight-spark-kafka-structured-streaming/master/azuredeploy.json**.

    Tato šablona vytvoří následující prostředky:

    * Cluster Kafka ve službě HDInsight 3.6.
    * Cluster Spark 2.2.0 ve službě HDInsight 3.6.
    * Virtuální síť Azure obsahující clustery HDInsight.

    > [!IMPORTANT]
    > Poznámkový blok strukturovaného streamování použitý v tomto příkladu vyžaduje Spark ve službě HDInsight 3.6. Pokud používáte starší verzi Sparku ve službě HDInsight, při použití poznámkového bloku se zobrazí chyby.

2. Pomocí následujících informací vyplňte položky v části **Přizpůsobená šablona**:

    | Nastavení | Hodnota |
    | --- | --- |
    | Předplatné | Vaše předplatné Azure |
    | Skupina prostředků | Skupina prostředků obsahující prostředky. |
    | Umístění | Oblast Azure, ve které se prostředky vytvoří. |
    | Název clusteru Spark | Název clusteru Spark. |
    | Název clusteru Kafka | Název clusteru Kafka. |
    | Uživatelské jméno přihlášení clusteru | Uživatelské jméno správce clusterů. |
    | Heslo přihlášení clusteru | Heslo správce clusterů. |
    | Uživatelské jméno SSH | Uživatel SSH, který se má pro clustery vytvořit. |
    | Heslo SSH | Heslo uživatele SSH. |
   
    ![Snímek obrazovky přizpůsobené šablony](./media/hdinsight-apache-kafka-spark-structured-streaming/spark-kafka-template.png)

4. Nakonec zaškrtněte políčko **Připnout na řídicí panel** a vyberte **Koupit**. 

> [!NOTE]
> Vytvoření clusterů může trvat až 20 minut.

## <a name="get-the-notebook"></a>Získání poznámkového bloku

Kód příkladu popisovaného v tomto dokumentu je dostupný na adrese [https://github.com/Azure-Samples/hdinsight-spark-kafka-structured-streaming](https://github.com/Azure-Samples/hdinsight-spark-kafka-structured-streaming).

## <a name="upload-the-notebooks"></a>Nahrání poznámkových bloků

Pokud chcete nahrát poznámkový blok z projektu do clusteru Spark ve službě HDInsight, postupujte následovně:

1. Ve webovém prohlížeči se připojte k poznámkovému bloku Jupyter na svém clusteru Spark. V následující adrese URL nahraďte `CLUSTERNAME` názvem svého clusteru __Spark__:

        https://CLUSTERNAME.azurehdinsight.net/jupyter

    Po zobrazení výzvy zadejte přihlašovací jméno (správce) a heslo clusteru, které jste použili při vytváření clusteru.

2. Pomocí tlačítka __Upload__ (Nahrát) v pravém horním rohu stránky nahrajte do clusteru soubor __spark-structured-streaming-kafka.ipynb__. Vyberte __Otevřít__ a zahajte nahrávání.

    ![Použití tlačítka Upload (Nahrát) k výběru a nahrání poznámkového bloku](./media/hdinsight-apache-kafka-spark-structured-streaming/upload-button.png)

    ![Výběr souboru KafkaStreaming.ipynb](./media/hdinsight-apache-kafka-spark-structured-streaming/select-notebook.png)

3. V seznamu poznámkových bloků vyhledejte položku __spark-structured-streaming-kafka.ipynb__ a vyberte tlačítko __Upload__ (Nahrát) vedle ní.

    ![Nahrání poznámkového bloku pomocí tlačítka Upload (Nahrát) vedle položky KafkaStreaming.ipynb](./media/hdinsight-apache-kafka-spark-structured-streaming/upload-notebook.png)


## <a name="use-the-notebook"></a>Použití poznámkového bloku

Po nahrání souborů výběrem položky __spark-structured-streaming-kafka.ipynb__ otevřete poznámkový blok. Informace o použití strukturovaného streamování Sparku se systémem Kafka ve službě HDInsight najdete v pokynech v poznámkovém bloku.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete vyčistit prostředky vytvořené v tomto kurzu, můžete odstranit skupinu prostředků. Odstraněním skupiny prostředků odstraníte také přidružený cluster HDInsight a všechny další prostředky, které jsou k příslušné skupině prostředků přidružené.

Odebrání skupiny prostředků pomocí webu Azure Portal:

1. Na webu Azure Portal rozbalením nabídky na levé straně otevřete nabídku služeb a pak zvolte __Skupiny prostředků__. Zobrazí se seznam skupin prostředků.
2. Vyhledejte skupinu prostředků, kterou chcete odstranit, a klikněte pravým tlačítkem na tlačítko __Další__ (...) na pravé straně seznamu.
3. Vyberte __Odstranit skupinu prostředků__ a potvrďte tuto akci.

> [!WARNING]
> Účtování clusteru HDInsight začne vytvořením clusteru a skončí jeho odstraněním. Účtuje se poměrnou částí po minutách, takže byste cluster měli odstranit vždy, když už se nepoužívá.
> 
> Odstraněním clusteru Kafka ve službě HDInsight odstraníte také všechna data uložená v systému Kafka.

## <a name="next-steps"></a>Další kroky

Když už víte, jak používat strukturované streamování Sparku, přečtěte si následující dokumenty, kde najdete další informace o práci se Sparkem a systémem Kafka:

* [Jak používat streamování Sparku (DStream) s využitím systému Kafka](hdinsight-apache-spark-with-kafka.md)
* [Začínáme s poznámkovými bloky Jupyter a Sparkem ve službě HDInsight](spark/apache-spark-jupyter-spark-sql.md)