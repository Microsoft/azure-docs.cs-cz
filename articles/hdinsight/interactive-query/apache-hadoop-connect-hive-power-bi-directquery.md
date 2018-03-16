---
title: "Vizualizace interaktivní dotazu Hive pomocí Power BI v Azure HDInsight | Microsoft Docs"
description: "Další informace o použití Microsoft Power BI můžete vizualizovat interaktivní dotaz Hive data zpracovaná službou Azure HDInsight."
keywords: "hdinsight hadoop, hive, interaktivní dotaz directquery interaktivní hive, LLAP,"
services: hdinsight
documentationcenter: 
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: 
ms.service: hdinsight
ms.custom: hdinsightactive,
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/14/2018
ms.author: jgao
ms.openlocfilehash: c74e02ea32a5aacf1a5208c0e194a25b0b6bcb48
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/16/2018
---
# <a name="visualize-interactive-query-hive-data-with-microsoft-power-bi-using-direct-query-in-azure-hdinsight"></a>Vizualizovat data interaktivní Hive dotaz s Microsoft Power BI pomocí přímých dotazů v Azure HDInsight

Zjistěte, jak se připojit k Azure HDInsight interaktivní dotazu clustery Microsoft Power BI a vizualizovat data Hive pomocí přímý dotaz. V tomto kurzu můžete načíst data z tabulky Hive hivesampletable do Power BI. Tabulka Hive obsahuje některé údaje o používání mobilního telefonu. Potom vykreslení dat o využití na mapě world:

![HDInsight Power BI sestavy mapy](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-power-bi-visualization.png)

Můžete využít [ovladače Hive ODBC](../hadoop/apache-hadoop-connect-hive-power-bi.md) pro import prostřednictvím konektoru obecné ODBC v Power BI Desktop. Nedoporučuje se ale pro úlohy BI zadané neinteraktivní povaha modul dotazů Hive. [Konektor interaktivní dotazu HDInsight](./apache-hadoop-connect-hive-power-bi-directquery.md) a [HDInsight Spark konektor](https://docs.microsoft.com/power-bi/spark-on-hdinsight-with-direct-connect) jsou lepší možnosti výkonu.

## <a name="prerequisites"></a>Požadavky
Před zahájením tohoto článku, musíte mít následující položky:

* **HDInsight cluster**. Cluster může být clusteru HDInsight bez Hive nebo clusteru s podporou nově vydané interaktivní dotazu. Vytváření clusterů, najdete v části [vytvořit cluster](../hadoop/apache-hadoop-linux-tutorial-get-started.md#create-cluster).
* **[Microsoft Power BI Desktop](https://powerbi.microsoft.com/desktop/)**. Můžete si stáhnout kopii z [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=45331).

## <a name="load-data-from-hdinsight"></a>Načtení dat z HDInsight

Tabulka hivesampletable Hive obsahuje všechny clustery HDInsight.

1. Přihlaste se k Power BI Desktop.
2. Klikněte na tlačítko **Domů** , klikněte na **načíst Data** z **externích dat** pásu karet a potom vyberte **Další**.

    ![Otevřete data HDInsight Power BI](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-power-bi-open-odbc.png)
3. Z **načíst Data** podokně, typ **hdinsight** do vyhledávacího pole. Pokud nevidíte **HDInsight interaktivní dotazu (Beta)**, budete muset aktualizovat vaše Power BI Desktop na nejnovější verzi.
4. Klikněte na tlačítko **HDInsight interaktivní dotazu (Beta)**a potom klikněte na **Connect**.
5. Klikněte na tlačítko **pokračovat** zavřete **Preview konektor** dialogové okno s upozorněním.
6. Z **HDInsight interaktivní dotazu**, vyberte nebo zadejte následující informace:

    - **Server**: Zadejte název clusteru interaktivní dotazu, například *myiqcluster.azurehdinsight.net*.
    - **Databáze**: v tomto kurzu zadejte **výchozí**.
    - **Režim připojení dat**: v tomto kurzu vyberte **DirectQuery**.

    ![HDInsight interaktivní dotazu power bi directquery připojení](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-interactive-query-power-bi-connect.png)
7. Klikněte na **OK**.
8. Zadejte přihlašovací údaje uživatele HTTP a pak klikněte na **OK**.  Výchozí uživatelské jméno **správce**
9. V levém podokně vyberte **hivesampletale**a potom klikněte na **zatížení**.

    ![HDInsight interaktivní dotazu power bi hivesampletable](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-interactive-query-power-bi-hivesampletable.png)

## <a name="visualize-data"></a>Vizualizaci dat

Pokračujte v posledním postupu.

1. V podokně vizualizace vyberte **mapy**.  Je ikona glóbu.

    ![HDInsight Power BI přizpůsobí sestavy](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-power-bi-customize.png)
2. V podokně polí, vyberte **země** a **devicemake**. Uvidíte dat zobrazovaných na mapě.
3. Rozbalte mapy.

## <a name="next-steps"></a>Další postup
V tomto článku jste zjistili, jak k vizualizaci dat z prostředí HDInsight pomocí Power BI.  Další informace naleznete v následujících článcích:

* [Vizualizovat Hive data s Microsoft Power BI pomocí rozhraní ODBC v Azure HDInsight](../hadoop/apache-hadoop-connect-hive-power-bi.md). 
* [Použití Zeppelin ke spouštění dotazů Hive v Azure HDInsight](./../hdinsight-connect-hive-zeppelin.md).
* [Připojení aplikace Excel do HDInsight pomocí ovladače ODBC Microsoft Hive](../hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md).
* [Připojení aplikace Excel k systému Hadoop pomocí Power Query](../hadoop/apache-hadoop-connect-excel-power-query.md).
* [Připojení k Azure HDInsight a spouštět dotazy Hive pomocí nástrojů Data Lake pro Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md).
* [Pomocí nástroje Azure HDInsight pro Visual Studio Code](../hdinsight-for-vscode.md).
* [Nahrání dat do HDInsight](./../hdinsight-upload-data.md).
