---
title: Instalace publikované aplikace – Dataiku DDS – Azure HDInsight
description: Nainstalovat a používat aplikace Hadoop jiných výrobců Dataiku DDS.
services: hdinsight
author: ashishthaps
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: ashish
ms.openlocfilehash: 64a6f393498ca90675712747afc8f9befc4b932f
ms.sourcegitcommit: f6e2a03076679d53b550a24828141c4fb978dcf9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/27/2018
ms.locfileid: "43105165"
---
# <a name="install-published-application---dataiku-dds"></a>Instalace publikované aplikace – Dataiku DDS

Tento článek popisuje, jak nainstalovat a spustit [Dataiku DDS](https://www.dataiku.com/) publikovaných aplikací Hadoop v Azure HDInsight. Přehled aplikační platforma HDInsight a seznam z dostupných nezávislý výrobce softwaru (ISV) publikované aplikace, najdete v části [instalace aplikací Hadoop jiných výrobců](hdinsight-apps-install-applications.md). Pokyny pro instalaci vašich vlastních aplikací najdete v článku [Instalace vlastních aplikací HDInsight](hdinsight-apps-install-custom-applications.md).

## <a name="about-dataiku-dss"></a>O Dataiku DSS

Dataiku [Data Science Studio (DSS)](https://www.dataiku.com/dss/features/connectivity/), je spolupráci platforma pro datovou vědu, které umožňuje datovým vědcům vytvářet a dodávat analytická řešení. Nabídka DSS jako HDInsight aplikace vám umožní používat pro datové vědy pro sestavení řešení pro velké objemy dat a spusťte je v podnikové a škálování.

DSS můžete použít k implementaci kompletního analytická řešení, počínaje ingestování, Příprava a zpracování. DSS řešení může obsahovat také trénovací a použití modelů strojového učení, vizualizace a pak až po zprovoznění.

DSS můžete nainstalovat na HDInsight clusterů Hadoop nebo Spark. DSS můžete nainstalovat na existující spuštěné clustery nebo při vytváření nových clusterů. Pro načtení dat pomocí úložiště objektů Blob v Azure jako konektor podporuje také DSS.

DSS můžete použít k sestavení projektů, které pak můžete vygenerovat úlohy MapReduce nebo Sparku. Tyto úlohy jsou spouštěny jako pravidelné úlohy MapReduce nebo Spark v HDInsight, takže při škálování clusteru na vyžádání.

## <a name="prerequisites"></a>Požadavky

K instalaci této aplikace na nový cluster HDInsight nebo stávajícího clusteru, musíte mít následující konfiguraci:

* Cluster úrovně: Standard, Premium
* Typy clusteru: Hadoop, Spark
* Verze clusteru: 3.4, 3.5

## <a name="install-the-dataiku-dss-published-application"></a>Instalace Dataiku DSS publikované aplikace

Podrobné pokyny k instalaci Tato a další dostupné aplikace nezávislých výrobců softwaru, přečtěte si [instalace aplikací Hadoop jiných výrobců](hdinsight-apps-install-applications.md).

## <a name="launch-dataiku-dss"></a>Spuštění Dataiku DSS

1. Po dokončení instalace můžete spustit DSS z váš cluster na webu Azure portal tak, že přejdete **nastavení** podokno, pak levým na **aplikací** pod **Obecné** kategorie. V podokně nainstalované aplikace uvádí nainstalované aplikace.

    ![Aplikace nainstalované Dataiku DSS](./media/hdinsight-apps-install-dataiku/app.png)

2. Když vyberete DSS na HDInsight, uvidíte odkaz na webovou stránku a cestu koncového bodu SSH. Vyberte odkaz webové stránky.

3. Při prvním spuštění zobrazí se formulář vám zadarmo vytvoří nový účet Dataiku nebo se přihlásit k existujícímu účtu. Máte také možnost spustit bezplatnou zkušební verzi 2 týden [Enterprise Edition](https://www.dataiku.com/dss/editions/). Od této chvíle máte možnost zadat licenční klíč pro Enterprise Edition, nebo pomocí Community Edition.

4. Po dokončení vaší vybraných možností, zobrazí se přihlašovací formulář. Zadejte přihlašovací údaje výchozí zobrazí před přihlašovací formulář.

Následující kroky obsahují jednoduché ukázku.

1. [Stáhnout ukázkový objednávky sdíleného svazku clusteru](https://doc.dataiku.com/tutorials/data/101/haiku_shirt_sales.csv).

2. Na řídicím panelu DSS vyberte **+** (nový projekt) odkaz v nabídce vlevo na vytvoření nového projektu.

    ![Nový odkaz projektu](./media/hdinsight-apps-install-dataiku/new-project.png)

3. Ve formuláři nový projekt, zadejte **název**. **Klíč projektu** je navrhovaná hodnota automaticky vyplněna. V takovém případě zadejte "Orders". Klikněte na tlačítko **vytvořit**.

    ![Nový formulář projektu](./media/hdinsight-apps-install-dataiku/new-project-form.png)

4. Vyberte **+ IMPORTOVAT vaše první datovou sadu** v nová stránka projektu.

    ![Nahrání souboru](./media/hdinsight-apps-install-dataiku/import-dataset.png)

5. Vyberte **nahrát soubory** pod **soubory** seznamu datovou sadu. Zobrazí se dialogové okno nahrát. Klikněte na Přidat soubor, vyberte `haiku_shirt_sales.csv` soubor jste stáhli a ověřit.

6. Soubor nahraje do DSS. Zaškrtněte, pokud DSS rozpoznán formát CSV správně po kliknutí na tlačítko ve verzi Preview.

    ![Nahrání souboru](./media/hdinsight-apps-install-dataiku/preview.png)

7. Import je téměř dokonalé. Soubor CSV je pomocí karty oddělovače. Uvidíte, že data jsou ve formátu tabulky, sloupce s názvy funkcí a řádky, které představují pozorování. Jednu chybu je, že zjevně soubor obsahuje prázdný řádek mezi záhlavím a data. Chcete-li tuto chybu vyřešit, zadejte `1` v **přeskočit další řádky** pole.

    ![Uložení](./media/hdinsight-apps-install-dataiku/skip-lines.png)

8. Zadejte název nové datové sady. Zadejte **haiku_shirt_sales** pole na obrazovce, vyberte **vytvořit** tlačítko.

9. Zobrazí v tabulkovém zobrazení vašich dat, kde můžete začít prozkoumávat ho. Pro každý sloupec, měli byste vidět, že Dataiku Science Studio byl nalezen typ dat v _modré_ – v tomto případě, Text, číslo nebo datum (nezpracované). Ukazatel Určuje poměr sloupec, pro kterou hodnoty pravděpodobně tak, aby odpovídaly typu (červeně), nebo chybí (prázdné). V této datové sadě příklad oddělení má prázdné hodnoty a neplatná data.

    ![Tabulkové zobrazení](./media/hdinsight-apps-install-dataiku/viewing-dataset.png)

## <a name="data-manipulation"></a>Manipulace s daty

Je téměř vždy neuspořádaných dat z reálného světa a zřídka se elegantně zabaleno. Vyčištění dat obvykle vyžaduje řetězec skripty a přidružené obchodní logiku. Dataiku DSS nabízí vyhrazené **Lab** nástroj provést tento úkol přívětivější.

1. Klikněte na **Lab** v pravém horním rohu.

    ![Tlačítko testovacího prostředí](./media/hdinsight-apps-install-dataiku/lab-button.png)

2. Otevře se okno testovacího prostředí. Testovací prostředí je místo, kde můžete interaktivně pracovat na vaší datové sadě do toho pustíme Další. Tento kurz ukazuje aspekt vizuální analýzu. Vyberte **nový** pod vizuální analýzu. Zobrazí se výzva k zadání názvu vaší analýzy. Ponechat výchozí název pro tuto chvíli a pak klikněte na **vytvořit**.

    ![Vytvoření testovacího prostředí](./media/hdinsight-apps-install-dataiku/create-lab.png)

3. Vyberte **rychlé sloupce statistiky** tlačítko v pravém horním rohu stránky.

    ![Statistika rychlé sloupce](./media/hdinsight-apps-install-dataiku/quick-column-stats.png)

4. Zobrazí statistické údaje o datové typy a hodnoty zobrazené v založené na časové ose grafů **sloupce rychlý přehled** podokně.

    ![Rychlé zobrazení sloupců](./media/hdinsight-apps-install-dataiku/columns-quick-view.png)

Nyní můžete prozkoumat pomocí ukázkových dat DSS. Můžete vyčistit a pracovat s daty a vytvářet nové vizualizace.

Podrobné kurzy, najdete v článku [další Dataiku DSS](https://www.dataiku.com/learn/).

## <a name="next-steps"></a>Další postup

* [Dokumentace ke službě Dataiku DSS](https://doc.dataiku.com/dss/latest/).
* [Instalace vlastních aplikací HDInsight](hdinsight-apps-install-custom-applications.md): Naučte se nasazovat nepublikované aplikace HDInsight do HDInsight.
* [Publikování aplikací HDInsight](hdinsight-apps-publish-applications.md): Zjistěte, jak publikovat vlastní aplikace HDInsight do obchodu Azure Marketplace.
* [MSDN: Instalace aplikace HDInsight](https://msdn.microsoft.com/library/mt706515.aspx): Další informace jak definovat aplikace HDInsight.
* [Přizpůsobení clusterů HDInsight se systémem Linux pomocí akce skriptu](hdinsight-hadoop-customize-cluster-linux.md): Další informace o použití akce skriptu k instalaci dalších aplikací.
* [Použití prázdných hraničních uzlů v HDInsight](hdinsight-apps-use-edge-node.md): Další informace o použití prázdných hraničních uzlů pro přístup ke clusterům HDInsight a pro účely testování a hostování aplikací HDInsight.
