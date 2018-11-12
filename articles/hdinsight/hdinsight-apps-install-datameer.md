---
title: Instalace publikované aplikace – Datameer – Azure HDInsight
description: Nainstalovat a používat aplikace Datameer třetích stran Apache Hadoop.
services: hdinsight
author: ashishthaps
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: ashish
ms.openlocfilehash: 97d99aa59c490cf2dcdd4a69f32411a051942d36
ms.sourcegitcommit: f0c2758fb8ccfaba76ce0b17833ca019a8a09d46
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/06/2018
ms.locfileid: "51037800"
---
# <a name="install-published-application---datameer"></a>Instalace publikované aplikace – Datameer

Tento článek popisuje, jak nainstalovat a spustit [Datameer](https://www.datameer.com/) publikovat aplikace Apache Hadoop v Azure HDInsight. Přehled aplikační platforma HDInsight a seznam z dostupných nezávislý výrobce softwaru (ISV) publikované aplikace, najdete v části [instalace aplikací Hadoop jiných výrobců](hdinsight-apps-install-applications.md). Pokyny pro instalaci vašich vlastních aplikací najdete v článku [Instalace vlastních aplikací HDInsight](hdinsight-apps-install-custom-applications.md).

## <a name="about-datameer"></a>O Datameer

Aplikace Datameer je nativní aplikace pro Hadoop platformy, rozšíření stávajících funkcí Azure HDInsight a poskytuje rychlou integraci, přípravy a analýzy strukturovanými i nestrukturovanými daty. Datameer mají přístup k více než 70 zdrojům a formáty: strukturovaných, částečně strukturovaných a nestrukturovaných. Můžete přímo nahrát data nebo použijte jejich jedinečných dat odkazy k získání dat na vyžádání. Datameer pro samoobslužné funkce a rozhraní strukturu tabulky snižuje složitost technologií pro velké objemy dat a zrychluje čas insight. Rozhraní tabulka poskytuje jednoduchý mechanismus pro zadávání deklarativní vzorců, které jsou následně převedeny na optimalizované úlohy systému Hadoop. Datameer a business intelligence (BI) a znalosti Excelu můžete pomocí Hadoopu v cloudu rychle. Další informace najdete v tématu [Datameer dokumentaci](http://www.datameer.com/documentation/display/DAS50/Home?ls=Partners&lsd=Microsoft&c=Partners&cd=Microsoft).

## <a name="prerequisites"></a>Požadavky

K instalaci této aplikace na nový cluster HDInsight nebo stávajícího clusteru, musíte mít následující konfiguraci:

* Úroveň clusteru: Standard
* Typ clusteru: Hadoop
* Verze clusteru: 3.4

## <a name="install-the-datameer-published-application"></a>Instalace Datameer publikované aplikace

Podrobné pokyny k instalaci Tato a další dostupné aplikace nezávislých výrobců softwaru, přečtěte si [instalace aplikací Hadoop jiných výrobců](hdinsight-apps-install-applications.md).

## <a name="launch-datameer"></a>Spuštění Datameer

1. Po dokončení instalace můžete spustit Datameer z váš cluster na webu Azure portal tak, že přejdete **nastavení** podokno, pak levým na **aplikací** pod **Obecné** kategorie . V podokně nainstalované aplikace uvádí nainstalované aplikace.

    ![Datameer nainstalované aplikace](./media/hdinsight-apps-install-datameer/datameer-app.png)

2. Když vyberete Datameer, uvidíte odkaz na webovou stránku a cestu koncového bodu SSH. Vyberte odkaz webové stránky.

3. Při prvním spuštění, existují dvě možnosti licence: buď bezplatnou zkušební za 14 dní nebo aktivovat stávající licenci.

    ![Možnosti licencí](./media/hdinsight-apps-install-datameer/license.png)

4. Po dokončení vaší vybraných možností se zobrazí přihlašovací formulář. Zadejte přihlašovací údaje výchozí zobrazí před přihlašovací formulář. Po přihlášení, přijměte podmínky smlouvy software pokračujte.

    ![Přihlásit](./media/hdinsight-apps-install-datameer/login.png)

Následující kroky ukazují ukázku "Hello World".

1. [Stáhněte si ukázky sdíleného svazku clusteru](https://datameer.box.com/s/wzzw27za3agic4yjj8zrn6vfrph0ppnf).

2. Klikněte na tlačítko **+** podepsat nad Datameer řídicího panelu a klikněte na tlačítko **odeslat soubor**.

    ![Nahrání souboru](./media/hdinsight-apps-install-datameer/upload.png)

3. V dialogovém okně nahrávání Procházet a vybrat **Hello World.csv** jste stáhli. Ujistěte se, že **typ souboru** nastavit do sdíleného svazku clusteru / TSV. Vyberte **Další**. Dál klikat **Další** dokud se nedostanete konci průvodce.

    ![Nahrání souboru](./media/hdinsight-apps-install-datameer/upload-browse.png)

4. Název souboru **Hello World** pod novou složku. Přejmenujte novou složku jako "Ukázka". Vyberte **Uložit**.

    ![Uložení](./media/hdinsight-apps-install-datameer/save.png)

5. Klikněte na tlačítko **+** přihlásit jednou a vyberte **sešitu** vytvořte nový sešit pro data.

    ![Přidat sešitu](./media/hdinsight-apps-install-datameer/add-workbook.png)

6. Rozbalte **Data** složce **FileUploads**, pak bude **ukázka** složky, které jste vytvořili při ukládání souboru "Hello World". Vyberte **Hello World** formuláře seznam souborů a pak klikněte na **přidat Data**.

    ![Uložení](./media/hdinsight-apps-install-datameer/select-file.png)

7. Uvidíte data načíst do tabulky rozhraní. Chcete-li vybrat podmnožinu dat, vyberte **filtr** tlačítko na panelu nástrojů.

    ![Tlačítko Filtrovat](./media/hdinsight-apps-install-datameer/filter-button.png)

8. V dialogovém okně použít filtr, vyberte **Město** sloupci **rovná** operátoru a typu **Chicago** do filtru textového pole. Zkontrolujte **vytvořit filtr v nový list** zaškrtávací políčko, pak vyberte **vytvořit filtr**.

    ![Použít filtr](./media/hdinsight-apps-install-datameer/apply-filter.png)

9. Kliknutím na Uložit sešit **souboru**, pak **Uložit**. Zadejte název, jako je například "Hello World sešit".

10. Zobrazí se možnosti pro jak a kdy se má spustit v sešitu. Prozatím se všechny možnosti ponechte jejich výchozí hodnoty a pak zkontrolujte **zahájení výpočtu procesu okamžitě po uložení**a vyberte **Uložit**.

    ![Uložení sešitu](./media/hdinsight-apps-install-datameer/save-workbook.png)

11. Datameer poskytuje nástroje, výkonné vizualizace. Zobrazit data, vytvořte Infografiku. Vyberte **+** přihlásit na řídicím panelu a pak vyberte **Infografika**.

    ![Přidat informační grafiky](./media/hdinsight-apps-install-datameer/infographic-button.png)

12. Přetáhněte widgetu pruhový graf ze seznamu ovládacích prvků na levé straně, jak je znázorněno v kroku 1 v následujícím diagramu. V dalším kroku procházet Data ve složce data prohlížeče na pravé straně, rozbalte sešit, pak do listu, který jste přidali pomocí filtru (krok 2). Přetáhněte **název** sloupec přes horní pruhový graf a drop do **popisek** cílového nastavení v sešitu název sloupce jako pole popisku grafu (krok 3).

    ![Infografika](./media/hdinsight-apps-install-datameer/infographic.png)

13. Chcete-li nastavit stáří jako osa Y grafu, přetáhněte **stáří** sloupce do grafu **Data** pole.

    ![Infografika](./media/hdinsight-apps-install-datameer/infographic-age.png)

Blahopřejeme! Vytvořili jste vizualizace vašich dat bez psaní kódu. Nyní můžete prozkoumat odchylky a dalších vizualizací.

## <a name="next-steps"></a>Další postup

* [Dokumentace ke službě Datameer](http://www.datameer.com/documentation/display/DAS50/Home?ls=Partners&lsd=Microsoft&c=Partners&cd=Microsoft).
* [Instalace vlastních aplikací HDInsight](hdinsight-apps-install-custom-applications.md): Naučte se nasazovat nepublikované aplikace HDInsight do HDInsight.
* [Publikování aplikací HDInsight](hdinsight-apps-publish-applications.md): Zjistěte, jak publikovat vlastní aplikace HDInsight do obchodu Azure Marketplace.
* [MSDN: Instalace aplikace HDInsight](https://msdn.microsoft.com/library/mt706515.aspx): Další informace jak definovat aplikace HDInsight.
* [Přizpůsobení clusterů HDInsight se systémem Linux pomocí akce skriptu](hdinsight-hadoop-customize-cluster-linux.md): Další informace o použití akce skriptu k instalaci dalších aplikací.
* [Použití prázdných hraničních uzlů v HDInsight](hdinsight-apps-use-edge-node.md): Další informace o použití prázdných hraničních uzlů pro přístup ke clusterům HDInsight a pro účely testování a hostování aplikací HDInsight.
