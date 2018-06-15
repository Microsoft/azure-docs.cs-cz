---
title: Instalace publikované aplikace – kolekcí dat StreamSets - Azure HDInsight | Microsoft Docs
description: Nainstalovat a používat aplikace Hadoop kolekcí dat StreamSets třetích stran.
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
ms.openlocfilehash: e433de82576f8b943988881ed0b6673c0dccd77e
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
ms.locfileid: "31401019"
---
# <a name="install-published-application---streamsets-data-collector"></a>Nainstalujte publikovanou aplikaci - StreamSets kolekcí dat

Tento článek popisuje, jak nainstalovat a spustit [StreamSets kolekce dat pro HDInsight](https://streamsets.com/) publikovaných aplikací Hadoop v Azure HDInsight. Přehled platformy aplikace HDInsight a seznam z dostupných nezávislého výrobce softwaru (ISV) publikovaných aplikací, najdete v části [instalovat aplikace jiných výrobců Hadoop](hdinsight-apps-install-applications.md). Pokyny pro instalaci vašich vlastních aplikací najdete v článku [Instalace vlastních aplikací HDInsight](hdinsight-apps-install-custom-applications.md).

## <a name="about-streamsets-data-collector"></a>O kolekcí StreamSets dat

Kolekce dat StreamSets nasadí nad aplikace Azure HDInsight. Sběrač dat StreamSets poskytuje plné integrované vývojové prostředí (IDE), umožňuje návrh, testování, nasazení a správě any-to-any ingestování kanály. Tyto kanály můžete mřížku datového proudu a dávky dat a zahrnují řadu ve datového proudu transformace všechny bez nutnosti psát vlastní kód.

Kolekce dat StreamSets vám umožní pomocí mnoha velkých objemů dat komponenty, například HDFS, Kafka, Solr, Hive, HBASE a Kudu sestavení datové toky. Jakmile StreamSets kolekcí dat běží na hraniční server nebo v clusteru Hadoop, získáte sledování toku operací pro anomálií data a data v reálném čase. Toto monitorování zahrnuje na základě prahové hodnoty výstrahy, detekce anomálií a automatické nápravy chyba záznamů.

Sběrač dat StreamSets je určena pro logicky izolovat každá fáze v kanálu, tak, aby splňujete nové požadavky na obchodní umístěním v nové procesory a konektory bez kódování a s minimálními výpadky.

### <a name="streamsets-resource-links"></a>Odkazy na zdroje StreamSets

* [Dokumentace](https://streamsets.com/documentation/datacollector/latest/help/#Getting_Started/GettingStarted_Title.html)
* [Blog](https://streamsets.com/blog/)
* [Kurzy](https://github.com/streamsets/tutorials)
* [Fórum podpory pro vývojáře](https://groups.google.com/a/streamsets.com/forum/#!forum/sdc-user)
* [Slack veřejné StreamSets kanálu](https://streamsetters.slack.com/)
* [Zdrojový kód](https://github.com/streamsets)

## <a name="prerequisites"></a>Požadavky

K instalaci této aplikace na novém clusteru HDInsight nebo stávajícího clusteru, musíte mít následující konfiguraci:

* Cluster tier(s): Standard nebo Premium
* Verze clusteru: 3.5 a vyšší

## <a name="install-the-streamsets-data-collector-published-application"></a>Instalace kolekce dat StreamSets publikované aplikace

Podrobné pokyny k instalaci to a dalších dostupných aplikací ISV, najdete v tématu [instalovat aplikace jiných výrobců Hadoop](hdinsight-apps-install-applications.md).

## <a name="launch-streamsets-data-collector"></a>Spusťte kolekcí StreamSets dat

1. Po instalaci můžete spustit StreamSets z clusteru na portálu Azure přejděte **nastavení** podokně zaškrtnutím **aplikace** v části **Obecné** kategorie. V podokně nainstalované aplikace obsahuje seznam nainstalovaných aplikací.

    ![Aplikace nainstalované StreamSets](./media/hdinsight-apps-install-streamsets/streamsets.png)

2. Když vyberete StreamSets kolekcí dat, zobrazí se odkaz na webovou stránku a cestu ke koncovému bodu SSH. Vyberte odkaz webové stránky.

3. V dialogovém okně přihlášení, použijte následující pověření k přihlášení: `admin` a `admin`.

4. Na stránce Začínáme, klikněte na **vytvořit nový kanál**.

    ![Vytvořit nový kanál](./media/hdinsight-apps-install-streamsets/get-started.png)

5. V okně Nový kanál, zadejte název kanálu ("Hello, World"), volitelně zadejte popis a vyberte **Uložit**.

6. Sběrač dat konzoly se zobrazí. Vlastnosti panelu zobrazí vlastnosti kanálu.
 
    ![Konzole kolekce dat](./media/hdinsight-apps-install-streamsets/pipeline-canvas.png)

7. Nyní jste připraveni použít postup popsaný [StreamSets kurzu](https://streamsets.com/documentation/datacollector/latest/help/#Tutorial/Tutorial-title.html). Tento kurz poskytuje podrobné pokyny pro vytváření svůj první kanál.

## <a name="next-steps"></a>Další postup

* [Sběrač dat StreamSets dokumentaci](https://streamsets.com/documentation/datacollector/latest/help/#Getting_Started/GettingStarted_Title.html#concept_htw_ghg_jq).
* [Instalace vlastních aplikací HDInsight](hdinsight-apps-install-custom-applications.md): naučit se nasazovat nepublikované aplikace HDInsight do HDInsight.
* [Publikování aplikací HDInsight](hdinsight-apps-publish-applications.md): Zjistěte, jak publikovat vlastní aplikace HDInsight do obchodu Azure Marketplace.
* [MSDN: Instalace aplikace HDInsight](https://msdn.microsoft.com/library/mt706515.aspx): Další informace jak definovat aplikace HDInsight.
* [Přizpůsobení clusterů HDInsight se systémem Linux pomocí akce skriptu](hdinsight-hadoop-customize-cluster-linux.md): Další informace o použití akce skriptu k instalaci dalších aplikací.
* [Použít prázdný edge uzly v HDInsight](hdinsight-apps-use-edge-node.md): Naučte se používat prázdný hraniční uzel pro přístup ke clusterům HDInsight a pro účely testování a hostování aplikace HDInsight.
