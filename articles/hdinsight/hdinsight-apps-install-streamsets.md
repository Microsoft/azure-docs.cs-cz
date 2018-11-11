---
title: Instalace publikované aplikace – StreamSets Data Collector – Azure HDInsight
description: Nainstalovat a používat aplikaci Apache Hadoop jiných výrobců StreamSets Data Collector.
services: hdinsight
author: ashishthaps
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: ashish
ms.openlocfilehash: f963ae53e1396b1ef6279f2bd6502e5ab0cd23a1
ms.sourcegitcommit: f0c2758fb8ccfaba76ce0b17833ca019a8a09d46
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/06/2018
ms.locfileid: "51034553"
---
# <a name="install-published-application---streamsets-data-collector"></a>Instalace publikované aplikace – StreamSets Data Collector

Tento článek popisuje, jak nainstalovat a spustit [kolekce dat StreamSets pro HDInsight](https://streamsets.com/) publikovat aplikace Apache Hadoop v Azure HDInsight. Přehled aplikační platforma HDInsight a seznam z dostupných nezávislý výrobce softwaru (ISV) publikované aplikace, najdete v části [instalace aplikací Hadoop jiných výrobců](hdinsight-apps-install-applications.md). Pokyny pro instalaci vašich vlastních aplikací najdete v článku [Instalace vlastních aplikací HDInsight](hdinsight-apps-install-custom-applications.md).

## <a name="about-streamsets-data-collector"></a>O StreamSets Data Collector

StreamSets Data Collector se nasazuje nad aplikaci HDInsight v Azure. StreamSets Data Collector poskytuje plně vybavené integrované vývojové prostředí (IDE), že kanály ingestace umožňuje navrhnout, testovat, nasazovat a spravovat any-to-any. Tyto kanály můžete sítě datového proudu data a data dávek a zahrnují různé transformace ve streamu vše bez nutnosti psát vlastní kód.

StreamSets Data Collector vám umožní pomocí mnoha součástmi velké objemy dat, jako je HDFS, Kafka, Solr, Hive, HBASE a Kudu sestavení datové toky. Jakmile StreamSets Data Collector je spuštěná na hraničním serveru nebo v clusteru Hadoop, získáte v reálném čase, monitorování pro anomálie data i data toku provozu. Toto monitorování zahrnuje založené na prahových hodnotách výstrahy, detekci anomálií a automatické nápravy záznamy o chybách.

StreamSets Data Collector slouží k logické izolaci každá fáze v kanálu, tak nové obchodní požadavky lze splnit vyřazením nové procesory a konektory bez psaní kódu a s minimálními výpadky.

### <a name="streamsets-resource-links"></a>Odkazy na materiály StreamSets

* [Dokumentace ke službě](https://streamsets.com/documentation/datacollector/latest/help/#Getting_Started/GettingStarted_Title.html)
* [Blog](https://streamsets.com/blog/)
* [Kurzy](https://github.com/streamsets/tutorials)
* [Fórum podpory služby pro vývojáře](https://groups.google.com/a/streamsets.com/forum/#!forum/sdc-user)
* [Kanál StreamSets veřejného Slack](https://streamsetters.slack.com/)
* [Zdrojový kód](https://github.com/streamsets)

## <a name="prerequisites"></a>Požadavky

K instalaci této aplikace na nový cluster HDInsight nebo stávajícího clusteru, musíte mít následující konfiguraci:

* Cluster úrovně: Standard nebo Premium
* Verze clusteru: 3.5 a vyšší

## <a name="install-the-streamsets-data-collector-published-application"></a>Instalace kolekce dat StreamSets publikované aplikace

Podrobné pokyny k instalaci Tato a další dostupné aplikace nezávislých výrobců softwaru, přečtěte si [instalace aplikací Hadoop jiných výrobců](hdinsight-apps-install-applications.md).

## <a name="launch-streamsets-data-collector"></a>Spusťte kolekce dat StreamSets

1. Po dokončení instalace můžete spustit StreamSets z váš cluster na webu Azure portal tak, že přejdete **nastavení** podokně vyberete **aplikací** pod **Obecné** kategorie. V podokně nainstalované aplikace uvádí nainstalované aplikace.

    ![Aplikace nainstalované StreamSets](./media/hdinsight-apps-install-streamsets/streamsets.png)

2. Když vyberete StreamSets Data Collector, uvidíte odkaz na webovou stránku a cestu koncového bodu SSH. Vyberte odkaz webové stránky.

3. V dialogovém okně přihlášení, použijte následující pověření k přihlášení: `admin` a `admin`.

4. Na stránce Začínáme klikněte na tlačítko **vytvořit nový kanál**.

    ![Vytvořit nový kanál](./media/hdinsight-apps-install-streamsets/get-started.png)

5. V okně Nový kanál, zadejte název pro kanál ("Hello World"), volitelně zadejte popis a vyberte **Uložit**.

6. Kolektor dat konzoly se zobrazí. Vlastnosti panelu se zobrazí vlastnosti kanálu.
 
    ![Konzola Kolektoru dat](./media/hdinsight-apps-install-streamsets/pipeline-canvas.png)

7. Nyní jste připraveni sledovat [StreamSets kurzu](https://streamsets.com/documentation/datacollector/latest/help/#Tutorial/Tutorial-title.html). Tento kurz obsahuje podrobné pokyny pro vytvoření prvního kanálu.

## <a name="next-steps"></a>Další postup

* [Dokumentace ke službě kolekce dat StreamSets](https://streamsets.com/documentation/datacollector/latest/help/#Getting_Started/GettingStarted_Title.html#concept_htw_ghg_jq).
* [Instalace vlastních aplikací HDInsight](hdinsight-apps-install-custom-applications.md): Naučte se nasazovat nepublikované aplikace HDInsight do HDInsight.
* [Publikování aplikací HDInsight](hdinsight-apps-publish-applications.md): Zjistěte, jak publikovat vlastní aplikace HDInsight do obchodu Azure Marketplace.
* [MSDN: Instalace aplikace HDInsight](https://msdn.microsoft.com/library/mt706515.aspx): Další informace jak definovat aplikace HDInsight.
* [Přizpůsobení clusterů HDInsight se systémem Linux pomocí akce skriptu](hdinsight-hadoop-customize-cluster-linux.md): Další informace o použití akce skriptu k instalaci dalších aplikací.
* [Použití prázdných hraničních uzlů v HDInsight](hdinsight-apps-use-edge-node.md): Další informace o použití prázdných hraničních uzlů pro přístup ke clusterům HDInsight a pro účely testování a hostování aplikací HDInsight.
