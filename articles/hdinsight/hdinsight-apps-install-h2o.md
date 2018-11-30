---
title: Instalace publikované aplikace – H2O Sparkling Water – Azure HDInsight
description: Nainstalovat a používat aplikace Hadoop jiných výrobců H2O Sparkling Water.
services: hdinsight
author: ashishthaps
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: ashish
ms.openlocfilehash: d2aeb41c2fcc11c37ec56707932eabcf63e026a0
ms.sourcegitcommit: 345b96d564256bcd3115910e93220c4e4cf827b3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/28/2018
ms.locfileid: "52497209"
---
# <a name="install-published-application---h2o-sparkling-water"></a>Instalace publikované aplikace – H2O Sparkling Water

Tento článek popisuje, jak nainstalovat a spustit [H20 Sparkling Water](http://www.h2o.ai/) publikované [Apache Hadoop](https://hadoop.apache.org/) aplikaci v Azure HDInsight. Přehled aplikační platforma HDInsight a seznam z dostupných nezávislý výrobce softwaru (ISV) publikované aplikace, najdete v části [instalace aplikací Hadoop jiných výrobců](hdinsight-apps-install-applications.md). Pokyny pro instalaci vašich vlastních aplikací najdete v článku [Instalace vlastních aplikací HDInsight](hdinsight-apps-install-custom-applications.md).

## <a name="about-h2o-sparkling-water"></a>O H2O Sparkling Water

H2O Sparkling Water je open source platforma pro distribuované plně v paměti strojové učení s lineární škálovatelnost. H2O Sparkling Water umožňuje kombinovat rychlé a škálovatelné algoritmy machine learningu H2O se schopnostmi [Apache Spark](https://spark.apache.org/). S Sparkling Water, můžete uživatele přimět výpočtu z [Scala](https://www.scala-lang.org/), R a Python s pomocí uživatelského rozhraní H2O Flow.

H2O Sparkling Water umožňuje:

* **Snadno použitelné WebUI a známých rozhraní** – při nastavení nahoru a můžete začít rychle používat buď H2O intuitivní webový tok grafického uživatelského rozhraní nebo programovací prostředí, jako je R, Python, Java, Scala, JSON a H2O rozhraní API.
* **Data bez ohledu na podporu pro všechny běžné typy databáze a souboru** – snadné zkoumání a modelování velkých objemů dat z Microsoft Excelu, R Studio, Tableau a další. Připojení k datům z HDFS, S3, SQL a NoSQL daty.
* **Masivně škálovatelné nedefinované transformace velkých objemů dat a analýzu** – H2O velká spojení můžete provádět 7 x rychlejší než operace data.table R a lineárně škálovat na 10 miliard x 10 miliard řádků spojení.
* **Vyhodnocení dat v reálném čase** – rychle nasadit modely do produkčního prostředí pomocí objekty prostý old Java (POJO), modelu paměťově optimalizované objekty Java (MOJO), nebo rozhraní REST API H2O.

### <a name="resource-links"></a>Odkazy na materiály

* [Plán H2O.AI Engineering](http://jira.h2o.ai/)
* [Domovská stránka H2O.ai](http://www.h2o.ai/)
* [Dokumentace ke službě H2O.ai](http://docs.h2o.ai/)
* [Podpora H2O.ai](https://support.h2o.ai/)
* [Open Source H2O.AI základu kódu](https://github.com/h2oai/)

## <a name="prerequisites"></a>Požadavky

K instalaci této aplikace na nový cluster HDInsight nebo stávajícího clusteru, musíte mít následující konfiguraci:

* Cluster úrovně: Standard nebo Premium
* Typ clusteru: Spark
* Verze clusteru: 3.5 a 3.6

## <a name="install-the-h2o-sparkling-water-published-application"></a>Instalace H2O Sparkling Water publikované aplikace

Podrobné pokyny k instalaci Tato a další dostupné aplikace nezávislých výrobců softwaru, přečtěte si [instalovat aplikace jiných výrobců Apache Hadoop](hdinsight-apps-install-applications.md).

## <a name="launch-h2o-sparkling-water"></a>Spusťte H2O Sparkling Water

1. Po dokončení instalace můžete začít používat H2O Sparkling Water (h2o sparklingwater) z váš cluster na webu Azure portal tak, že otevřete [poznámkové bloky Jupyter](https://jupyter.org/) (`https://<ClusterName>.azurehdinsight.net/jupyter`). Získáte taky pro Jupyter tak, že vyberete **řídicí panel clusteru** z podokna clusteru na portálu, pak vyberete **Poznámkový blok Jupyter**. Zobrazí se výzva k zadání přihlašovacích údajů. Zadejte přihlašovací údaje clusteru Hadoop je definován na vytváření clusteru.

2. V Jupyter, uvidíte tři složky: H2O. PySparkling příklady, příklady PySpark a Scala příklady. Vyberte **H2O. PySparkling příklady** složky.

    ![Domácí poznámkové bloky Jupyter](./media/hdinsight-apps-install-h2o/jupyter-home.png)

3. Prvním krokem při vytváření nového poznámkového bloku je konfigurace prostředí Spark. Tyto informace jsou uvedeny v **Sentiment_analysis_with_Sparkling_Water** příklad. Při konfiguraci prostředí Spark, nezapomeňte použít správný soubor jar a zadejte IP adresu, poskytuje výstup první buňky.

    ![Domácí poznámkové bloky Jupyter](./media/hdinsight-apps-install-h2o/spark-config.png)

4. Spusťte H2O Cluster.

    ![Spustit cluster](./media/hdinsight-apps-install-h2o/start-cluster.png)

5. Po zprovoznění clusteru H2O se otevřete H2O tok tak, že přejdete do **`https://<ClusterName>-h2o.apps.azurehdinsight.net:443`**.

    > [!NOTE]
    > Pokud nemůžete otevřít tok H2O, vymažte mezipaměť prohlížeče. Pokud stále nelze k němu přistoupit, pravděpodobně nemáte dostatek prostředků ve vašem clusteru. Zkuste zvýšit počet pracovních uzlů v rámci **škálování clusteru** možnost v podokně vašeho clusteru.

    ![Tok H2O řídicí panel](./media/hdinsight-apps-install-h2o/h2o-flow.png)

6. Vyberte **Million_Songs.flow** příklad z nabídky na pravé straně. Po zobrazení výzvy s upozorněním, klikněte na tlačítko **Poznámkový blok zatížení**. V této ukázce je navržen pro spouštění pomocí reálná data za několik minut. Cílem je možnost předvídat z dat, zda skladby byla vydána před nebo po 2004 pomocí binární klasifikace.

    ![Vyberte Million_Songs.flow](./media/hdinsight-apps-install-h2o/million-songs.png)

7. Najít cestu obsahující **milsongs. kompatibilní se specifikací train.csv.gz**a nahraďte celou cestu s **https://h2o-public-test-data.s3.amazonaws.com/bigdata/laptop/milsongs/milsongs-cls-train.csv.gz**.

8. Najít cestu obsahující **milsongs. kompatibilní se specifikací test.csv.gz** a nahraďte ho hodnotou **https://h2o-public-test-data.s3.amazonaws.com/bigdata/laptop/milsongs/milsongs-cls-test.csv.gz**.

9. Ke spuštění všech příkazů v buňkách Poznámkový blok, vyberte **spustit všechny** tlačítko na panelu nástrojů.

    ![Spustit všechny](./media/hdinsight-apps-install-h2o/run-all.png)

10. Po několika minutách byste měli vidět výstup podobný následujícímu.

    ![Výstup](./media/hdinsight-apps-install-h2o/output.png)

A to je vše! Jste využily umělou inteligenci ve Sparku v řádu minut. Teď si můžete projít Další příklady v H2O tok, které ukazují různé typy algoritmů strojového učení.

## <a name="next-steps"></a>Další postup

* [Dokumentace ke službě H2O](http://docs.h2o.ai/h2o/latest-stable/h2o-docs/index.html)
* [Instalace vlastních aplikací HDInsight](hdinsight-apps-install-custom-applications.md): Naučte se nasazovat nepublikované aplikace HDInsight do HDInsight.
* [Publikování aplikací HDInsight](hdinsight-apps-publish-applications.md): Zjistěte, jak publikovat vlastní aplikace HDInsight do obchodu Azure Marketplace.
* [MSDN: Instalace aplikace HDInsight](https://msdn.microsoft.com/library/mt706515.aspx): Další informace jak definovat aplikace HDInsight.
* [Přizpůsobení clusterů HDInsight se systémem Linux pomocí akce skriptu](hdinsight-hadoop-customize-cluster-linux.md): Další informace o použití akce skriptu k instalaci dalších aplikací.
* [Použití prázdných hraničních uzlů v HDInsight](hdinsight-apps-use-edge-node.md): Další informace o použití prázdných hraničních uzlů pro přístup ke clusterům HDInsight a pro účely testování a hostování aplikací HDInsight.
