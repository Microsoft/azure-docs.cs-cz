---
title: Instalace publikované aplikace – Datameer – Azure HDInsight
description: Nainstalovat a používat aplikace Hadoop jiných výrobců Datameer.
services: hdinsight
author: ashishthaps
editor: jasonwhowell
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: ashish
ms.openlocfilehash: a8961da0a62815ce60a6ee694d57f3172b81718b
ms.sourcegitcommit: 1f0587f29dc1e5aef1502f4f15d5a2079d7683e9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/07/2018
ms.locfileid: "39592172"
---
# <a name="install-published-application---cask-data-application-platform-cdap"></a>Instalace publikované aplikace – Cask Data Application Platform (CDAP)

Tento článek popisuje, jak nainstalovat a spustit [CDAP](http://cask.co/products/cdap/) publikovaných aplikací Hadoop v Azure HDInsight. Přehled aplikační platforma HDInsight a seznam z dostupných nezávislý výrobce softwaru (ISV) publikované aplikace, najdete v části [instalace aplikací Hadoop jiných výrobců](hdinsight-apps-install-applications.md). Pokyny pro instalaci vašich vlastních aplikací najdete v článku [Instalace vlastních aplikací HDInsight](hdinsight-apps-install-custom-applications.md).

## <a name="about-cdap"></a>O CDAP

Vývoj aplikací v systému Hadoop, může být náročné.  Existuje velké a rostoucí počet rozšíření technologie Hadoop, což může trvat nějakou dobu pro integraci. Tok dat monitorování a shromažďování metrik může vyžadovat sestavení do samostatného řešení.

### <a name="how-does-cdap-help"></a>Jak mi pomůže CDAP?

Cask Data Application Platform CDAP () je integrační platformu pro velké objemy dat. CDAP vám umožní zaměřit se na vytváření aplikací, spíše než v základní infrastruktuře.

CDAP používá základními koncepty a abstrakce, které jsou pro vývojáře srozumitelná. Tato abstrakce skrýt složitosti interních systémů a podporovat opětovné použití řešení.

Rozšíření CDAP voláno [Cask Hydrator](http://cask.co/products/hydrator/) poskytuje uživatelské rozhraní pro vývoj a spravovat datové kanály. Datový kanál se skládá z různých * získávání dat, transformace, analýzy a po spuštění operace, jako jsou moduly plug-in, které provádějí úlohy.

Každý modul plug-in CDAP má dobře definované rozhraní tak, aby vaše rozhodnutí vyzkoušet různé technologie stačí jenom jeden modul plug-in nahrazení jiný, aniž byste museli touch zbývajících částí aplikace.

CDAP *kanály* poskytují základní vypravovat tok dat v aplikaci. Tuto vizualizaci zobrazuje-kompletního toku dat z ingestování, transformace dat a analýz a nakonec na externí data ukládat.

Následující příklad datového kanálu ingestuje data twitteru v reálném čase a pak filtruje některé tweety na základě předem definovaných kritérií. Datový kanál transformuje data nezpracovaná tweet a ukládání projektů, že data do čitelnějšího formátu, pak skupiny tweety podle sadu hodnot a zapisuje výsledky do HBase.

![CDAP kanálu](./media/hdinsight-apps-install-cask/pipeline.png)

Tento kanál začátku do konce se vytvořil pomocí **Cask Hydrator UI**, pomocí jeho funkce modulu plug-in rozhraní a přetažením myší vytvořit připojení mezi jednotlivým fázím. Můžete izolovat a změnit tak funkce modulu plug-in nezávisle na sobě. Pomocí CDAP, podobně jako kanály dají vytvořit a ověřit v hodinách. V typické Hadoop world vytváření takové řešení může trvat několik dní.

CDAP také poskytuje rozšíření volá [Cask sledování](http://cask.co/products/tracker/) pro vizuální trasování dat při prochází přes aplikaci. Přidá cask sledování *datový dozor nad* systému tak, aby datové assety jsou formálně spravovat v celé aplikaci. Můžete sledovat rodokmenu datových bodů, shromažďovat relevantní metriky a záznam pro data v celém procesu audit.

Tady je ilustraci, jak se data přenášejí do výše uvedené kanálu:

![Sledování CDAP](./media/hdinsight-apps-install-cask/tracker.png)

## <a name="prerequisites"></a>Požadavky

K instalaci této aplikace na nový cluster HDInsight nebo stávajícího clusteru, musíte mít následující konfiguraci:

* Úroveň clusteru: Standard
* Typ clusteru: HBase
* Verze clusteru: 3.4, 3.5

## <a name="install-the-cdap-published-application"></a>Instalace CDAP publikované aplikace

Podrobné pokyny k instalaci Tato a další dostupné aplikace nezávislých výrobců softwaru, přečtěte si [instalace aplikací Hadoop jiných výrobců](hdinsight-apps-install-applications.md).

## <a name="launch-cdap"></a>Spuštění CDAP

1. Po dokončení instalace spusťte CDAP z váš cluster na webu Azure portal tak, že přejdete **nastavení** podokně vyberete **aplikací** pod **Obecné** kategorie. V podokně nainstalované aplikace uvádí všechny nainstalované aplikace.

    ![Aplikace nainstalované CDAP](./media/hdinsight-apps-install-cask/cdap-app.png)

2. Když vyberete CDAP, uvidíte odkaz na webovou stránku a koncový bod protokolu HTTP a také cestu koncového bodu SSH. Vyberte odkaz webové stránky.

3. Po zobrazení výzvy zadejte svoje přihlašovací údaje Správce clusteru.

    ![Authentication](./media/hdinsight-apps-install-cask/auth.png)

4. Po přihlášení zobrazí domovská stránka Cask CDAP grafického uživatelského rozhraní.

    ![Cask grafickém uživatelském rozhraní domovské stránky](./media/hdinsight-apps-install-cask/gui.png)

5. Prozkoumat rozhraní CDAP, klikněte na tlačítko **Cask trhu** nabídky odkazu na stránce.

    ![Odkaz na trhu cask](./media/hdinsight-apps-install-cask/cask-market.png)

6. Vyberte **přístup protokolu ukázka** ze seznamu.

    ![Ukázka protokolu přístup](./media/hdinsight-apps-install-cask/market-log-sample.png)

7. Klikněte na tlačítko **zatížení** potvrďte.

    ![Klikněte na načíst](./media/hdinsight-apps-install-cask/market-load.png)

8. Zobrazí se zobrazení součástí ukázková data. Vyberte **Další**.

    ![Přístup k protokolu ukázkový – zobrazení dat](./media/hdinsight-apps-install-cask/market-view-data.png)

9. Vyberte **Stream** jako cílový typ, zadejte název cíle a pak vyberte **Dokončit**.

    ![Přístup k protokolu ukázkový – vyberte cílovou](./media/hdinsight-apps-install-cask/market-destination.png)

10. Po úspěšném načtení datapack vyberte **Stream zobrazit podrobnosti o**.

    ![Datapack byl úspěšně nahrán.](./media/hdinsight-apps-install-cask/market-view-details.png)

11. Chcete-li metadata pro obor názvů, vyberte **povolit** na kartě využití na stránce s podrobnostmi přístup k protokolu.

    ![Ukázka protokolu přístup – byla načtena – povolení metadat](./media/hdinsight-apps-install-cask/log-loaded.png)

12. Po povolení metadat, uvidíte v zobrazení informací o zprávy auditu graf.

    ![Přístup k protokolu ukázkový – povolení metadat](./media/hdinsight-apps-install-cask/log-metadata.png)

13. Chcete-li prozkoumat data protokolu, vyberte **prozkoumat** ikonu na stránce.

    ![Přístup k protokolu ukázkový – prozkoumejte](./media/hdinsight-apps-install-cask/log-explore.png)

14. Zobrazí ukázkového dotazu SQL. Můžete bez obav ho jako požadovaný a pak vyberte Upravit **Execute**.

    ![Přístup k protokolu ukázkové – prozkoumejte datovou sadu pomocí dotazu](./media/hdinsight-apps-install-cask/log-query.png)

15. Po dokončení dotazu, vyberte **zobrazení** ikony ve sloupci Akce.

    ![Ukázkový protokol přístupu – zobrazit dokončené dotazu](./media/hdinsight-apps-install-cask/log-query-view.png)

16. Zobrazí výsledky dotazu.

    ![Ukázkový protokol přístupu – výsledky dotazu](./media/hdinsight-apps-install-cask/log-query-results.png)

## <a name="next-steps"></a>Další postup

* [Dokumentace ke službě cask](http://cask.co/resources/documentation/).
* [Instalace vlastních aplikací HDInsight](hdinsight-apps-install-custom-applications.md): Naučte se nasazovat nepublikované aplikace HDInsight do HDInsight.
* [Publikování aplikací HDInsight](hdinsight-apps-publish-applications.md): Zjistěte, jak publikovat vlastní aplikace HDInsight do obchodu Azure Marketplace.
* [MSDN: Instalace aplikace HDInsight](https://msdn.microsoft.com/library/mt706515.aspx): Další informace jak definovat aplikace HDInsight.
* [Přizpůsobení clusterů HDInsight se systémem Linux pomocí akce skriptu](hdinsight-hadoop-customize-cluster-linux.md): Další informace o použití akce skriptu k instalaci dalších aplikací.
* [Použití prázdných hraničních uzlů v HDInsight](hdinsight-apps-use-edge-node.md): Další informace o použití prázdných hraničních uzlů pro přístup ke clusterům HDInsight a pro účely testování a hostování aplikací HDInsight.
