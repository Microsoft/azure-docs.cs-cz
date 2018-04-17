---
title: Instalace publikované aplikace – Dataiku DDS - Azure HDInsight | Microsoft Docs
description: Nainstalovat a používat aplikace Hadoop Dataiku DDS třetích stran.
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
ms.openlocfilehash: facee9187528fa0c6a74f71ee73636dc23ca35ba
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="install-published-application---dataiku-dds"></a>Nainstalujte publikovanou aplikaci - Dataiku DDS

Tento článek popisuje, jak nainstalovat a spustit [Dataiku DDS](https://www.dataiku.com/) publikovaných aplikací Hadoop v Azure HDInsight. Přehled platformy aplikace HDInsight a seznam z dostupných nezávislého výrobce softwaru (ISV) publikovaných aplikací, najdete v části [instalovat aplikace jiných výrobců Hadoop](hdinsight-apps-install-applications.md). Pokyny pro instalaci vašich vlastních aplikací najdete v článku [Instalace vlastních aplikací HDInsight](hdinsight-apps-install-custom-applications.md).

## <a name="about-dataiku-dss"></a>O Dataiku DSS

Dataiku [datové vědy Studio (DSS)](https://www.dataiku.com/dss/features/connectivity/), je platforma vědecké účely spolupráce data, která umožňuje datových vědců sestavení a poskytovat analytická řešení. Nabídka DSS jako HDInsight aplikace vám umožní používat vědecké zpracování dat pro sestavení řešení pro velká Data a jejich spuštění na podnikové úrovni a škálování.

DSS můžete použít k implementaci kompletního analytická řešení, počínaje přijímání dat, přípravy a zpracování. DSS řešení může také obsahovat školení a používá modely machine learning, vizualizace a pak zprovozňování.

DSS můžete nainstalovat v HDInsight pomocí clusterů Hadoop nebo Spark. DSS můžete nainstalovat na existující clustery spuštěná nebo při vytváření nových clusterů. DSS podporuje také pro čtení dat pomocí úložiště objektů Blob v Azure jako spojnice.

DSS můžete použít k vytvoření projektů, které pak mohou generovat úloh MapReduce nebo Spark. Tyto úlohy jsou spouštěny jako pravidelných úloh MapReduce nebo Spark v HDInsight, takže je možné škálovat clusteru na vyžádání.

## <a name="prerequisites"></a>Požadavky

K instalaci této aplikace na novém clusteru HDInsight nebo stávajícího clusteru, musíte mít následující konfiguraci:

* Cluster tier(s): standardní, Premium
* Cluster typu (typů): Hadoop, Spark
* Verze clusteru: 3,4, 3.5

## <a name="install-the-dataiku-dss-published-application"></a>Instalace Dataiku DSS publikované aplikace

Podrobné pokyny k instalaci to a dalších dostupných aplikací ISV, najdete v tématu [instalovat aplikace jiných výrobců Hadoop](hdinsight-apps-install-applications.md).

## <a name="launch-dataiku-dss"></a>Spusťte Dataiku DSS

1. Po instalaci můžete spustit DSS z clusteru na portálu Azure přejděte **nastavení** podokně, klikněte na **aplikace** v části **Obecné** kategorie. V podokně nainstalované aplikace obsahuje seznam nainstalovaných aplikací.

    ![Aplikace nainstalované Dataiku DSS](./media/hdinsight-apps-install-dataiku/app.png)

2. Když vyberete DSS v HDInsight, zobrazí se odkaz na webovou stránku a cestu ke koncovému bodu SSH. Vyberte odkaz webové stránky.

3. Při prvním spuštění se zobrazí s formuláři zdarma vytvořit nový účet Dataiku a přihlaste se k existujícímu účtu. Máte také možnost spustit bezplatnou zkušební verzi 2týden [Enterprise Edition](https://www.dataiku.com/dss/editions/). Z tohoto bodu máte možnost pokračujte v zadávání licenční klíč pro Enterprise Edition, nebo pomocí edice Community.

4. Po dokončení vaší licencí možnost, zobrazí se přihlašovací formulář. Zadejte výchozí pověření zobrazí před přihlašovací formulář.

Následující kroky poskytují jednoduché ukázka.

1. [Stáhnout ukázkové objednávky CSV](https://doc.dataiku.com/tutorials/data/101/haiku_shirt_sales.csv).

2. Na řídicím panelu DSS, vyberte **+** (nový projekt) odkaz v levé nabídce na vytvoření nového projektu.

    ![Nové propojení projektu](./media/hdinsight-apps-install-dataiku/new-project.png)

3. Ve formuláři, nový projekt, zadejte **název**. **Projektu klíč** je automaticky vyplněno navrhované hodnoty. V takovém případě zadejte "Objednávky". Klikněte na tlačítko **vytvořit**.

    ![Formulář nové projektu](./media/hdinsight-apps-install-dataiku/new-project-form.png)

4. Vyberte **+ IMPORT vaše první datovou sadu** v nové stránky projektu.

    ![Nahrání souboru](./media/hdinsight-apps-install-dataiku/import-dataset.png)

5. Vyberte **nahrát soubory** pod **soubory** seznamu datovou sadu. Zobrazí se dialogové okno nahrání. Klikněte na Přidat soubor, vyberte `haiku_shirt_sales.csv` soubor můžete stáhnout a ověřit.

6. Soubor je nahrán do DSS. Zkontrolujte, pokud DSS zjistil formát CSV správně kliknutím na tlačítko Náhled.

    ![Nahrání souboru](./media/hdinsight-apps-install-dataiku/preview.png)

7. Import je téměř úplně bez chyby. Soubor CSV je pomocí karty oddělovače. Uvidíte, že data jsou ve formátu tabulky se sloupci volat funkce a řádky, které představují připomínky. Jednu chybu je, že zjevně soubor obsahoval prázdný řádek mezi hlavičku a data. Chcete-li tuto chybu opravit, zadejte `1` v **přeskočit další řádky** pole.

    ![Uložení](./media/hdinsight-apps-install-dataiku/skip-lines.png)

8. Zadejte název nové datové sady. Zadejte **haiku_shirt_sales** v poli na obrazovce, pak vyberte **vytvořit** tlačítko.

9. Zobrazí tabulkové zobrazení dat, kde můžete začít vyhledávat ho. Pro každý sloupec byste měli vidět, že Dataiku vědecké účely Studio zjistila datového typu, v _blue_ – v tomto případu, Text, číslo nebo datum (nezpracované). Měřidlo Určuje poměr sloupce, pro kterou hodnoty nejsou schopné odesílat shodovat s typem (červeně), nebo chybí (prázdný). V této datové sadě příklad oddělení má prázdné hodnoty a neplatná data.

    ![Tabulkové zobrazení](./media/hdinsight-apps-install-dataiku/viewing-dataset.png)

## <a name="data-manipulation"></a>Manipulace s daty

Skutečná data jsou téměř vždy komplikované a zřídka se přehledně zabalený. Vyčištění dat obvykle vyžaduje řetězec skripty a přidružené obchodní logiku. Poskytuje vyhrazená Dataiku DSS **testovacím** nástroj k usnadnění této činnosti přívětivější.

1. Klikněte na **testovacího prostředí** v pravém horním rohu.

    ![Tlačítko testovacího prostředí](./media/hdinsight-apps-install-dataiku/lab-button.png)

2. Otevře se okno testovacího prostředí. Testovací prostředí je, kde můžete interaktivně pracovat na datovou sadu získat další do ní. Tento kurz představuje aspekt Visual analýzy. Vyberte **nový** tlačítko pod Visual analýzy. Zobrazí se výzva k zadání názvu pro analýzy. Ponechte výchozí název teď a potom klikněte na **vytvořit**.

    ![Vytvoření testovacího prostředí](./media/hdinsight-apps-install-dataiku/create-lab.png)

3. Vyberte **rychlé sloupce statistiky** tlačítko v pravém horním rohu stránky.

    ![Rychlé sloupce statistiky](./media/hdinsight-apps-install-dataiku/quick-column-stats.png)

4. Zobrazí statistiku pro datové typy a hodnoty zobrazené na založené na časové ose grafy v části **sloupce rychlý přehled** podokně.

    ![Rychlý přehled sloupců](./media/hdinsight-apps-install-dataiku/columns-quick-view.png)

Nyní můžete prozkoumat pomocí ukázkových dat DSS. Můžete vyčistit a práce s daty a vytvořit nové vizualizace.

Podrobné kurzy, najdete v tématu [další DSS Dataiku](https://www.dataiku.com/learn/).

## <a name="next-steps"></a>Další postup

* [Dokumentace Dataiku DSS](https://doc.dataiku.com/dss/latest/).
* [Instalace vlastních aplikací HDInsight](hdinsight-apps-install-custom-applications.md): naučit se nasazovat nepublikované aplikace HDInsight do HDInsight.
* [Publikování aplikací HDInsight](hdinsight-apps-publish-applications.md): Zjistěte, jak publikovat vlastní aplikace HDInsight do obchodu Azure Marketplace.
* [MSDN: Instalace aplikace HDInsight](https://msdn.microsoft.com/library/mt706515.aspx): Další informace jak definovat aplikace HDInsight.
* [Přizpůsobení clusterů HDInsight se systémem Linux pomocí akce skriptu](hdinsight-hadoop-customize-cluster-linux.md): Další informace o použití akce skriptu k instalaci dalších aplikací.
* [Použít prázdný edge uzly v HDInsight](hdinsight-apps-use-edge-node.md): Naučte se používat prázdný hraniční uzel pro přístup ke clusterům HDInsight a pro účely testování a hostování aplikace HDInsight.
