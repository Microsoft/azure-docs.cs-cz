---
title: Instalace publikované aplikace – H2O šumivého horních - Azure HDInsight | Microsoft Docs
description: Nainstalovat a používat aplikace Hadoop H2O šumivého horních třetích stran.
services: hdinsight
documentationcenter: ''
author: ashishthaps
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: ''
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.date: 01/10/2018
ms.author: ashish
ms.openlocfilehash: 9a03588b3327c3ab231f5c2cae17488f4d63bde7
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
ms.locfileid: "31402103"
---
# <a name="install-published-application---h2o-sparkling-water"></a>Nainstalujte publikované aplikace – H2O šumivého horních

Tento článek popisuje, jak nainstalovat a spustit [H20 šumivého horních](http://www.h2o.ai/) publikovaných aplikací Hadoop v Azure HDInsight. Přehled platformy aplikace HDInsight a seznam z dostupných nezávislého výrobce softwaru (ISV) publikovaných aplikací, najdete v části [instalovat aplikace jiných výrobců Hadoop](hdinsight-apps-install-applications.md). Pokyny pro instalaci vašich vlastních aplikací najdete v článku [Instalace vlastních aplikací HDInsight](hdinsight-apps-install-custom-applications.md).

## <a name="about-h2o-sparkling-water"></a>O H2O šumivého horních

H2O šumivého horních je typu open source, platformy learning plně distribuované v paměti počítače s lineární škálovatelnost. H2O šumivého horních umožňují kombinovat rychlá a spolehlivá algoritmů strojového učení H2O s funkcemi Spark. S šumivého horních můžete uživatelé jednotka výpočtu z Scala, R a Python pomocí uživatelského rozhraní H2O toku.

H2O šumivého horních poskytuje:

* **Snadné použití WebUI a známých rozhraní** – nastavte až a začít rychle buď H2O intuitivní webová toku grafickém uživatelském rozhraní nebo programování prostředích, jako je R, Python, Java, Scala, JSON a H2O rozhraní API.
* **Podpora vázané na data pro všechny běžné typy databáze a souboru** – snadno prozkoumat a model Big Data přímo z aplikace Microsoft Excel, R Studio, Tableau a další. Připojte se k datům ze zdroje dat pro HDFS, S3, SQL a NoSQL.
* **Nesmírně škálovatelná munging velkých objemů dat a analýzu** – velký spojení H2O umožňuje provádět 7 x rychlejší než operace data.table R a lineárně škálovat miliardy 10 x 10 miliardy řádků spojení.
* **Data v reálném čase vyhodnocování** – rychle nasadit modely na produkčním prostředí pomocí prostý old Java objekty (POJO), modelu optimalizované objekty Java (MOJO), nebo H2O REST API.

### <a name="resource-links"></a>Odkazy na zdroje

* [Plán H2O.AI inženýrství](https://jira.h2o.ai/)
* [H2O.AI domovské](http://www.h2o.ai/)
* [H2O.AI dokumentace](http://docs.h2o.ai/)
* [Podpora H2O.ai](https://support.h2o.ai/)
* [Codebase H2O.AI Open Source](https://github.com/h2oai/)

## <a name="prerequisites"></a>Požadavky

K instalaci této aplikace na novém clusteru HDInsight nebo stávajícího clusteru, musíte mít následující konfiguraci:

* Cluster tier(s): Standard nebo Premium
* Typ clusteru: Spark
* Verze clusteru: 3.5 nebo 3.6

## <a name="install-the-h2o-sparkling-water-published-application"></a>Instalace horních šumivého H2O publikované aplikace

Podrobné pokyny k instalaci to a dalších dostupných aplikací ISV, najdete v tématu [instalovat aplikace jiných výrobců Hadoop](hdinsight-apps-install-applications.md).

## <a name="launch-h2o-sparkling-water"></a>Spusťte H2O šumivého horních

1. Po instalaci, můžete začít používat H2O šumivého horních (h2o-sparklingwater) z clusteru na portálu Azure otevřením poznámkové bloky Jupyter (`https://<ClusterName>.azurehdinsight.net/jupyter`). Získáte také k Jupyter výběrem **řídicí panel clusteru** z vašeho clusteru podokně na portálu, pak výběrem **Poznámkový blok Jupyter**. Zobrazí se výzva k zadání přihlašovacích údajů. Zadejte přihlašovací údaje do clusteru Hadoop uvedeného na vytváření clusteru.

2. V Jupyter, nezobrazí se tři složky: H2O. PySparkling příklady, příklady PySpark a Scala příklady. Vyberte **H2O. PySparkling příklady** složky.

    ![Domácí poznámkové bloky Jupyter](./media/hdinsight-apps-install-h2o/jupyter-home.png)

3. Konfigurace prostředí Spark je prvním krokem při vytváření nového poznámkového bloku. Tato informace je obsažena v **Sentiment_analysis_with_Sparkling_Water** příklad. Při konfiguraci prostředí Spark, je nutné použít správné jar a zadejte IP adresu poskytované výstup první buňky.

    ![Domácí poznámkové bloky Jupyter](./media/hdinsight-apps-install-h2o/spark-config.png)

4. Spusťte H2O clusteru.

    ![Spuštění clusteru](./media/hdinsight-apps-install-h2o/start-cluster.png)

5. Jakmile je Cluster H2O spuštěná, otevřete H2O toku přechodem na **`https://<ClusterName>-h2o.apps.azurehdinsight.net:443`**.

    > [!NOTE]
    > Pokud nelze otevřít H2O toku, vymažte mezipaměť prohlížeče. Pokud stále možné vás zastihnout na jeho, pravděpodobně nemáte dostatek prostředků v clusteru. Pokuste se zvýšit počet uzlů pracovního procesu v rámci **škálování clusteru** možnost v podokně pro cluster.

    ![Tok H2O řídicí panel](./media/hdinsight-apps-install-h2o/h2o-flow.png)

6. Vyberte **Million_Songs.flow** příklad z nabídky na pravé straně. Po zobrazení výzvy s upozorněním, klikněte na tlačítko **zatížení Poznámkový blok**. Tato ukázka je určená ke spuštění za pár minut pomocí skutečná data. Cílem je k předvídání z dat, zda byl skladbu vydané před nebo po 2004 pomocí binární klasifikace.

    ![Vyberte Million_Songs.flow](./media/hdinsight-apps-install-h2o/million-songs.png)

7. Najít cestu obsahující **milsongs. specifikací cls train.csv.gz**a nahraďte celou cestu s **https://h2o-public-test-data.s3.amazonaws.com/bigdata/laptop/milsongs/milsongs-cls-train.csv.gz**.

8. Najít cestu obsahující **milsongs. specifikací cls test.csv.gz** a nahraďte ho **https://h2o-public-test-data.s3.amazonaws.com/bigdata/laptop/milsongs/milsongs-cls-test.csv.gz**.

9. Chcete-li provést všechny příkazy v rámci Poznámkový blok buněk, vyberte **spustit všechny** tlačítka na panelu nástrojů.

    ![Spustit všechny](./media/hdinsight-apps-install-h2o/run-all.png)

10. Po několika minutách měli byste vidět výstup podobný následujícímu.

    ![Výstup](./media/hdinsight-apps-install-h2o/output.png)

A to je vše! Jste orientovat umělé intelligence ve Sparku v rámci několika minut. Nyní můžete prozkoumat další příklady v toku H2O, která ukazují různé typy algoritmů strojového učení.

## <a name="next-steps"></a>Další postup

* [H2O dokumentace](http://docs.h2o.ai/h2o/latest-stable/h2o-docs/index.html)
* [Instalace vlastních aplikací HDInsight](hdinsight-apps-install-custom-applications.md): naučit se nasazovat nepublikované aplikace HDInsight do HDInsight.
* [Publikování aplikací HDInsight](hdinsight-apps-publish-applications.md): Zjistěte, jak publikovat vlastní aplikace HDInsight do obchodu Azure Marketplace.
* [MSDN: Instalace aplikace HDInsight](https://msdn.microsoft.com/library/mt706515.aspx): Další informace jak definovat aplikace HDInsight.
* [Přizpůsobení clusterů HDInsight se systémem Linux pomocí akce skriptu](hdinsight-hadoop-customize-cluster-linux.md): Další informace o použití akce skriptu k instalaci dalších aplikací.
* [Použít prázdný edge uzly v HDInsight](hdinsight-apps-use-edge-node.md): Naučte se používat prázdný hraniční uzel pro přístup ke clusterům HDInsight a pro účely testování a hostování aplikace HDInsight.
