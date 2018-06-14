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
ms.openlocfilehash: 5008056ae2274d058706649f286b91b71feadc27
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
ms.locfileid: "31401305"
---
# <a name="install-published-application---datameer"></a>Nainstalujte publikované aplikace – Datameer

Tento článek popisuje, jak nainstalovat a spustit [Datameer](https://www.datameer.com/) publikovaných aplikací Hadoop v Azure HDInsight. Přehled platformy aplikace HDInsight a seznam z dostupných nezávislého výrobce softwaru (ISV) publikovaných aplikací, najdete v části [instalovat aplikace jiných výrobců Hadoop](hdinsight-apps-install-applications.md). Pokyny pro instalaci vašich vlastních aplikací najdete v článku [Instalace vlastních aplikací HDInsight](hdinsight-apps-install-custom-applications.md).

## <a name="about-datameer"></a>O Datameer

Datameer je nativní aplikace pro Hadoop platformy, rozšíření stávající funkce Azure HDInsight a rychlé integrace, přípravy a analýzu strukturovaná i nestrukturovaná data. Datameer mají přístup k více než 70 zdroje a formáty: strukturovaná, částečně strukturovaná i nestrukturovaná. Můžete přímo nahrání dat nebo použít jejich jedinečným datům odkazy k získání dat na vyžádání. Na Datameer samoobslužných funkcí a rozhraní strukturu tabulky snižuje složitost technologie velkých objemů dat a zrychluje čas pro náhled. Rozhraní tabulky poskytuje jednoduché mechanismus pro zadávání deklarativní vzorců, které jsou pak převedeny na optimalizované úloh Hadoop. Datameer a business intelligence (BI) a znalosti Excelu můžete pomocí Hadoop v cloudu rychle. Další informace najdete v tématu [Datameer dokumentaci](http://www.datameer.com/documentation/display/DAS50/Home?ls=Partners&lsd=Microsoft&c=Partners&cd=Microsoft).

## <a name="prerequisites"></a>Požadavky

K instalaci této aplikace na novém clusteru HDInsight nebo stávajícího clusteru, musíte mít následující konfiguraci:

* Vrstvy clusteru: standardní
* Typ clusteru: Hadoop
* Verze clusteru: 3.4

## <a name="install-the-datameer-published-application"></a>Instalace Datameer publikované aplikace

Podrobné pokyny k instalaci to a dalších dostupných aplikací ISV, najdete v tématu [instalovat aplikace jiných výrobců Hadoop](hdinsight-apps-install-applications.md).

## <a name="launch-datameer"></a>Spusťte Datameer

1. Po instalaci můžete spustit Datameer z clusteru na portálu Azure přejděte **nastavení** podokně, klikněte na **aplikace** v části **Obecné** kategorie . V podokně nainstalované aplikace obsahuje seznam nainstalovaných aplikací.

    ![Aplikace nainstalované Datameer](./media/hdinsight-apps-install-datameer/datameer-app.png)

2. Když vyberete Datameer, zobrazí se odkaz na webovou stránku a cestu ke koncovému bodu SSH. Vyberte odkaz webové stránky.

3. Při prvním spuštění, existují dvě možnosti licence: buď bezplatnou zkušební 14 dnů nebo aktivovat stávající licence.

    ![Možnosti licencí](./media/hdinsight-apps-install-datameer/license.png)

4. Po dokončení vaší licencí možnost, zobrazí se uvedené s přihlašovací formulář. Zadejte výchozí pověření zobrazí před přihlašovací formulář. Po přihlášení přijměte smlouvu softwaru pokračujte.

    ![Přihlásit](./media/hdinsight-apps-install-datameer/login.png)

Následující kroky ukazují ukázka "Hello World".

1. [Stažení ukázky CSV](https://datameer.box.com/s/wzzw27za3agic4yjj8zrn6vfrph0ppnf).

2. Klikněte **+** přihlásit na řídicím panelu Datameer a klikněte na **nahrát soubor**.

    ![Nahrání souboru](./media/hdinsight-apps-install-datameer/upload.png)

3. V dialogovém okně nahrávání Procházet a vybrat **Hello World.csv** jste stáhli. Zajistěte, aby **typ souboru** je nastaven na sdílený svazek clusteru / TSV. Vyberte **Next** (Další). Zachovat kliknutím na **Další** dokud nepřejdete na konci průvodce.

    ![Nahrání souboru](./media/hdinsight-apps-install-datameer/upload-browse.png)

4. Název souboru **Hello, World** pod novou složku. Přejmenujte do nové složky jako "Ukázkový". Vyberte **Uložit**.

    ![Uložení](./media/hdinsight-apps-install-datameer/save.png)

5. Klikněte **+** přihlásit jednou a vybrat **sešitu** k vytvoření nového sešitu pro data.

    ![Přidat sešitu](./media/hdinsight-apps-install-datameer/add-workbook.png)

6. Rozbalte **Data** složky, **FileUploads**, pak se **ukázku** složky, které jste vytvořili při ukládání souboru "Hello World". Vyberte **Hello, World** formuláři seznam souborů a pak klikněte na **přidat Data**.

    ![Uložení](./media/hdinsight-apps-install-datameer/select-file.png)

7. Zobrazit data načíst v rozhraní tabulky. Chcete-li vybrat podmnožinu dat, vyberte **filtru** tlačítka na panelu nástrojů.

    ![Tlačítko Filtr](./media/hdinsight-apps-install-datameer/filter-button.png)

8. V dialogovém okně použít filtr, vyberte **města** sloupce **rovná** operátor a typ **Chicagu** do textového pole filtru. Zkontrolujte **vytvořit filtr v novém listu** zaškrtávací políčko, pak vyberte **vytvořit filtr**.

    ![Použít filtr](./media/hdinsight-apps-install-datameer/apply-filter.png)

9. Sešit uložit kliknutím **soubor**, pak **Uložit**. Zadejte název, jako je například "Hello World sešit".

10. Zobrazí se možnosti, jak a kdy se má spustit sešitu. Prozatím se všechny možnosti ponechte výchozí hodnoty a potom zkontrolujte **okamžitě po uložení procesu spuštění výpočtu**a vyberte **Uložit**.

    ![Sešit uložit](./media/hdinsight-apps-install-datameer/save-workbook.png)

11. Datameer poskytuje výkonné vizualizace nástroje. Pokud chcete zobrazit data, vytvořte Infografice. Vyberte **+** přihlásit na řídicím panelu a pak vyberte **Infografice**.

    ![Přidat Infografice](./media/hdinsight-apps-install-datameer/infographic-button.png)

12. Přetáhněte modul widget pruhový graf ze seznamu pomůcky na levé straně, jak je znázorněno v kroku 1 v následujícím diagramu. Dále procházet složky dat v prohlížeči data na pravé straně, rozbalte sešit, potom na listu, který jste přidali pomocí filtru (krok 2). Přetáhněte **název** sloupce v horní části pruhový graf a drop do **popisek** cíl nastavit název sloupce sešitu jako pole popisku grafu (krok 3).

    ![Infografika](./media/hdinsight-apps-install-datameer/infographic.png)

13. Chcete-li nastavit stáří jako osu Y grafu, přetáhněte **stáří** sloupce do grafu **Data** pole.

    ![Infografika](./media/hdinsight-apps-install-datameer/infographic-age.png)

Blahopřejeme! Vytvoření zobrazení dat. bez psaní jakéhokoli kódu. Nyní můžete prozkoumat rozdíly a další vizualizace.

## <a name="next-steps"></a>Další postup

* [Dokumentace Datameer](http://www.datameer.com/documentation/display/DAS50/Home?ls=Partners&lsd=Microsoft&c=Partners&cd=Microsoft).
* [Instalace vlastních aplikací HDInsight](hdinsight-apps-install-custom-applications.md): naučit se nasazovat nepublikované aplikace HDInsight do HDInsight.
* [Publikování aplikací HDInsight](hdinsight-apps-publish-applications.md): Zjistěte, jak publikovat vlastní aplikace HDInsight do obchodu Azure Marketplace.
* [MSDN: Instalace aplikace HDInsight](https://msdn.microsoft.com/library/mt706515.aspx): Další informace jak definovat aplikace HDInsight.
* [Přizpůsobení clusterů HDInsight se systémem Linux pomocí akce skriptu](hdinsight-hadoop-customize-cluster-linux.md): Další informace o použití akce skriptu k instalaci dalších aplikací.
* [Použít prázdný edge uzly v HDInsight](hdinsight-apps-use-edge-node.md): Naučte se používat prázdný hraniční uzel pro přístup ke clusterům HDInsight a pro účely testování a hostování aplikace HDInsight.
