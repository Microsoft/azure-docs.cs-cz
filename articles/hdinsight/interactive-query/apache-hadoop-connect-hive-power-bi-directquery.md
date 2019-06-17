---
title: Vizualizace dat Interactive Query Hive pomocí Power BI v Azure HDInsight
description: Použít Microsoft Power BI k vizualizaci dat Interactive Query Hive z Azure HDInsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/25/2018
ms.openlocfilehash: fb4e16c8be5344c5b9947758b6a09845b470196d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "65800993"
---
# <a name="visualize-interactive-query-apache-hive-data-with-microsoft-power-bi-using-direct-query-in-azure-hdinsight"></a>Vizualizace dat Interactive Query Apache Hive pomocí Microsoft Power BI pomocí přímých dotazů v Azure HDInsight

Tento článek popisuje, jak připojit Microsoft Power BI do clusterů Azure HDInsight Interactive Query a vizualizovat data Apache Hive pomocí přímých dotazů. Poskytnutý příklad načte data z `hivesampletable` tabulky Hive do Power BI. `hivesampletable` Tabulka Hive obsahuje data o využití některých mobilního telefonu. Potom vykreslení dat o využití na mapu světa:

![HDInsight Power BI sestavy mapy](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-power-bi-visualization.png)

Můžete využít [ovladače Apache Hive ODBC](../hadoop/apache-hadoop-connect-hive-power-bi.md) import prostřednictvím konektoru obecná rozhraní ODBC v Power BI Desktopu. Však není doporučeno pro BI úlohy uvedené jako neinteraktivní povaze modul dotazu Hive. [Konektor HDInsight Interactive Query](./apache-hadoop-connect-hive-power-bi-directquery.md) a [konektor HDInsight Apache Spark](https://docs.microsoft.com/power-bi/spark-on-hdinsight-with-direct-connect) jsou lepší možnosti pro jejich výkon.

## <a name="prerequisites"></a>Požadavky
Před provedením tohoto článku, musíte mít následující položky:

* **HDInsight cluster**. Cluster může být buď clusteru služby HDInsight s Apache Hive nebo nově vydané clusteru Interactive Query. Vytváření clusterů najdete v části [vytvořit cluster](../hadoop/apache-hadoop-linux-tutorial-get-started.md#create-cluster).
* **[Microsoft Power BI Desktopu](https://powerbi.microsoft.com/desktop/)** . Můžete stáhnout kopii z [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=45331).

## <a name="load-data-from-hdinsight"></a>Načtení dat z HDInsight

`hivesampletable` Tabulka Hive obsahuje všechny clustery HDInsight.

1. Spuštění Power BI Desktopu.

2. V řádku nabídek, přejděte na **Domů** > **získat Data** > **více...** .

    ![Otevřít dat HDInsight Power BI](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-power-bi-open-odbc.png)

3. Z **získat Data** okno, zadejte **hdinsight** do vyhledávacího pole.  

4. Ve výsledcích hledání vyberte **HDInsight Interactive Query**a pak vyberte **připojit**.  Pokud nevidíte **HDInsight Interactive Query**, je potřeba aktualizovat na nejnovější verzi vaší Power BI Desktopu.

5. Vyberte **pokračovat** zavřete **připojí ke službě třetí strany** dialogového okna.

6. V **HDInsight Interactive Query** okno, zadejte následující informace a pak vyberte **OK**:

    |Vlastnost | Hodnota |
    |---|---|
    |Server |Zadejte název clusteru, například *myiqcluster.azurehdinsight.net*.|
    |Databáze |Zadejte **výchozí** pro účely tohoto článku.|
    |Režim připojení dat |Vyberte **DirectQuery** pro účely tohoto článku.|

    ![Připojení k HDInsight interactive query Power BI DirectQuery](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-interactive-query-power-bi-connect.png)

7. Zadejte přihlašovací údaje protokolu HTTP a pak vyberte **připojit**. Výchozí uživatelské jméno je **správce**.

8. Z **Navigátor** okna v levém podokně vyberte **hivesampletale**.

9. Vyberte **zatížení** z hlavního okna.

    ![HDInsight interactive query Power BI hivesampletable](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-interactive-query-power-bi-hivesampletable.png)

## <a name="visualize-data-on-a-map"></a>Vizualizace dat na mapě

Pokračujte v posledním postupu.

1. V podokně vizualizace vyberte **mapy**, Ikona glóbu. Obecné mapování se pak objeví v hlavním okně.

    ![HDInsight Power BI přizpůsobí sestavy](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-power-bi-customize.png)

2. V podokně pole vyberte **země** a **devicemake**. Mapu světa s datovými body se zobrazí v hlavním okně za chvíli.

3. Rozbalte mapu.

## <a name="next-steps"></a>Další postup
V tomto článku jste zjistili, jak k vizualizaci dat z HDInsight pomocí Microsoft Power BI.  Další informace o vizualizaci dat najdete v následujících článcích:

* [Vizualizace dat Apache Hive pomocí Microsoft Power BI v Azure HDInsight pomocí ovladače ODBC](../hadoop/apache-hadoop-connect-hive-power-bi.md). 
* [Použití Apache Zeppelinu ke spuštění dotazy Apache Hive v HDInsight Azure](../interactive-query/hdinsight-connect-hive-zeppelin.md).
* [Připojení Excelu k HDInsight pomocí ovladače ODBC Microsoft Hivu](../hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md).
* [Připojení Excelu k systému Apache Hadoop pomocí doplňku Power Query](../hadoop/apache-hadoop-connect-excel-power-query.md).
* [Připojení k Azure HDInsight a spouštět dotazy Apache Hive pomocí nástrojů Data Lake pro Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md).
* [Pomocí nástroje Azure HDInsight pro Visual Studio Code](../hdinsight-for-vscode.md).
* [Nahrání dat do HDInsight](./../hdinsight-upload-data.md).
