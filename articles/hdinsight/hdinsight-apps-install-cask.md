---
title: Instalace publikované aplikace – Datameer - Azure HDInsight | Microsoft Docs
description: Nainstalovat a používat aplikace Hadoop Datameer třetích stran.
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
ms.openlocfilehash: 9eef1760b7cee3bbdf33122514669b38b0b4d9db
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="install-published-application---cask-data-application-platform-cdap"></a>Nainstalujte publikované aplikace – obalového souboru dat aplikace platformy (CDAP)

Tento článek popisuje, jak nainstalovat a spustit [CDAP](http://cask.co/products/cdap/) publikovaných aplikací Hadoop v Azure HDInsight. Přehled platformy aplikace HDInsight a seznam z dostupných nezávislého výrobce softwaru (ISV) publikovaných aplikací, najdete v části [instalovat aplikace jiných výrobců Hadoop](hdinsight-apps-install-applications.md). Pokyny pro instalaci vašich vlastních aplikací najdete v článku [Instalace vlastních aplikací HDInsight](hdinsight-apps-install-custom-applications.md).

## <a name="about-cdap"></a>O CDAP

Vývoj aplikací v Hadoop může být náročné.  Existuje velký počet rostoucí Hadoop technologie rozšíření, která může trvat nějakou dobu integrovat. Tok dat monitorování a shromažďování metrik může vyžadovat vytvoření samostatné řešení.

### <a name="how-does-cdap-help"></a>Jak funguje CDAP?

Platforma pro aplikaci Data obalového souboru (CDAP) je platforma integrace pro velká Data. CDAP vám umožní zaměřit se na vytváření aplikací, ne na odpovídající infrastruktury.

CDAP používá základními koncepty a abstrakce, které jsou pro vývojáře. Tato abstrakce skrýt složitosti interní systémů a podporuje – opětovné použití řešení.

Rozšíření CDAP voláno [Hydrator obalového souboru](http://cask.co/products/hydrator/) poskytuje uživatelské rozhraní pro vývoj a správu datových kanálů. Datový kanál se skládá z různých * modulů plug-in, které provádějí úlohy, jako získávání dat, transformace, analýzy a po spuštění operace.

Každý modul plug-in CDAP má dobře definované rozhraní tak, aby vyhodnocení různých technologií stačí nahrazení jeden modul plug-in jiným certifikátem, aniž by museli touch zbývající aplikace.

CDAP *kanály* poskytují souhrnné obrazové toku dat ve vaší aplikaci. Tuto vizualizaci zobrazuje začátku do konce tok dat z přijímání prostřednictvím transformace dat a analýz a nakonec na externí data uložit.

Následující příklad datovém kanálu ingestuje twitter data v reálném čase a potom filtruje některé tweetů na základě předem definovaných kritérií. Datový kanál transformuje tweet nezpracovaná data a ukládat projekty, pak skupiny tweetů podle sadu hodnot data do čitelnějšího formátu a zapisuje výsledky do HBase.

![CDAP kanálu](./media/hdinsight-apps-install-cask/pipeline.png)

Tento kanál začátku do konce je sestaven pomocí **uživatelského rozhraní Hydrator obalového souboru**, pomocí jeho funkce modulu plug-in rozhraní a přetažení myší vytvořit připojení mezi každé fáze. Můžete izolovat a změnit tak funkce modulu plug-in nezávisle. Pomocí CDAP, podobně jako kanály můžete být vytvořené a ověřit v hodinách. V typické world Hadoop vytváření takové řešení může trvat několik dní.

CDAP také poskytuje rozšíření názvem [sledovací modul obalového souboru](http://cask.co/products/tracker/) vizuálně trasování data jak toků prostřednictvím aplikace. Přidá sledovací modul obalového souboru *řízení dat* systému tak, aby datové prostředky se spravují oficiálně v celé aplikaci. Můžete sledovat rodokmenu každý datový bod, shromažďovat relevantní metriky a záznam dat v celém procesu pro audit.

Tady je ilustraci, jak je v výše kanálu toku dat:

![CDAP sledovací modul](./media/hdinsight-apps-install-cask/tracker.png)

## <a name="prerequisites"></a>Požadavky

K instalaci této aplikace na novém clusteru HDInsight nebo stávajícího clusteru, musíte mít následující konfiguraci:

* Vrstvy clusteru: standardní
* Typ clusteru: HBase
* Verze clusteru: 3,4, 3.5

## <a name="install-the-cdap-published-application"></a>Instalace CDAP publikované aplikace

Podrobné pokyny k instalaci to a dalších dostupných aplikací ISV, najdete v tématu [instalovat aplikace jiných výrobců Hadoop](hdinsight-apps-install-applications.md).

## <a name="launch-cdap"></a>Spusťte CDAP

1. Po instalaci zahájit CDAP z clusteru na portálu Azure tak, že přejdete na **nastavení** podokně zaškrtnutím **aplikace** pod **Obecné** kategorie. V podokně nainstalované aplikace zobrazí všechny nainstalované aplikace.

    ![Aplikace nainstalované CDAP](./media/hdinsight-apps-install-cask/cdap-app.png)

2. Když vyberete CDAP, zobrazí se odkaz na webovou stránku a koncový bod protokolu HTTP a také cesta koncový bod SSH. Vyberte odkaz webové stránky.

3. Po zobrazení výzvy zadejte přihlašovací údaje Správce clusteru.

    ![Authentication](./media/hdinsight-apps-install-cask/auth.png)

4. Po přihlášení zobrazí obalového souboru CDAP grafickém uživatelském rozhraní domovské stránky.

    ![Obalového souboru grafickém uživatelském rozhraní domovské stránky](./media/hdinsight-apps-install-cask/gui.png)

5. Pokud chcete prozkoumat rozhraní CDAP, klikněte **obalového souboru trhu** odkaz nabídky na stránku.

    ![Odkaz trhu obalového souboru](./media/hdinsight-apps-install-cask/cask-market.png)

6. Vyberte **Access protokolu ukázka** ze seznamu.

    ![Ukázka přístup protokolu](./media/hdinsight-apps-install-cask/market-log-sample.png)

7. Klikněte na tlačítko **zatížení** k potvrzení.

    ![Klikněte na tlačítko zatížení](./media/hdinsight-apps-install-cask/market-load.png)

8. Zobrazí se zobrazení zahrnuté ukázkových dat. Vyberte **Další**.

    ![Přístup k protokolu ukázka - zobrazení dat](./media/hdinsight-apps-install-cask/market-view-data.png)

9. Vyberte **datového proudu** jako cílový typ, zadejte název cílového a potom vyberte **Dokončit**.

    ![Přístup k protokolu ukázka - vybrat cílový](./media/hdinsight-apps-install-cask/market-destination.png)

10. Když datapack byla úspěšně načtena, vyberte **zobrazení podrobností datového proudu**.

    ![Datapack úspěšně nahrál.](./media/hdinsight-apps-install-cask/market-view-details.png)

11. Chcete-li metadata pro obor názvů, vyberte **povolit** na kartě využití na stránce s podrobnostmi o přístup protokolu.

    ![Povolit přístup k protokolu ukázka - byla načtena – metadat](./media/hdinsight-apps-install-cask/log-loaded.png)

12. Po povolení metadata zobrazí diagram zobrazující informace zprávy auditu.

    ![Přístup k protokolu ukázka - Metadata povoleno](./media/hdinsight-apps-install-cask/log-metadata.png)

13. Chcete-li prozkoumat data protokolu, vyberte **prozkoumat** ikonu na stránce.

    ![Přístup k protokolu ukázkové – prozkoumat](./media/hdinsight-apps-install-cask/log-explore.png)

14. Zobrazí ukázkový dotaz SQL. Libosti změnit jej jako požadovaný a pak vyberte **Execute**.

    ![Přístup k protokolu ukázka – prozkoumat datovou sadu s dotazem](./media/hdinsight-apps-install-cask/log-query.png)

15. Po dokončení dotazu, vyberte **zobrazení** ikony ve sloupci Akce.

    ![Přístup k protokolu ukázka - dotaz zobrazení dokončena](./media/hdinsight-apps-install-cask/log-query-view.png)

16. Zobrazí výsledky dotazu.

    ![Přístup k protokolu ukázka - výsledky dotazu](./media/hdinsight-apps-install-cask/log-query-results.png)

## <a name="next-steps"></a>Další postup

* [Dokumentace obalového souboru](http://cask.co/resources/documentation/).
* [Instalace vlastních aplikací HDInsight](hdinsight-apps-install-custom-applications.md): naučit se nasazovat nepublikované aplikace HDInsight do HDInsight.
* [Publikování aplikací HDInsight](hdinsight-apps-publish-applications.md): Zjistěte, jak publikovat vlastní aplikace HDInsight do obchodu Azure Marketplace.
* [MSDN: Instalace aplikace HDInsight](https://msdn.microsoft.com/library/mt706515.aspx): Další informace jak definovat aplikace HDInsight.
* [Přizpůsobení clusterů HDInsight se systémem Linux pomocí akce skriptu](hdinsight-hadoop-customize-cluster-linux.md): Další informace o použití akce skriptu k instalaci dalších aplikací.
* [Použít prázdný edge uzly v HDInsight](hdinsight-apps-use-edge-node.md): Naučte se používat prázdný hraniční uzel pro přístup ke clusterům HDInsight a pro účely testování a hostování aplikace HDInsight.
