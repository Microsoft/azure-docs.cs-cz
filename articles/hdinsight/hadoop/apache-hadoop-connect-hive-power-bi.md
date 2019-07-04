---
title: Vizualizace dat pomocí Power BI – Azure HDInsight s Apache Hive
description: Zjistěte, jak použít Microsoft Power BI k vizualizaci dat Hive zpracovává Azure HDInsight.
keywords: hdinsight hadoop, hive, interaktivní dotazy, interaktivní hive, LLAP, odbc
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,
ms.topic: conceptual
ms.date: 05/21/2019
ms.author: hrasheed
ms.openlocfilehash: 69353968f6b38f0d16b68c58b9b00c3e6d45850b
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/28/2019
ms.locfileid: "67446874"
---
# <a name="visualize-apache-hive-data-with-microsoft-power-bi-using-odbc-in-azure-hdinsight"></a>Vizualizace dat Apache Hive pomocí Microsoft Power BI v Azure HDInsight pomocí ovladače ODBC

Zjistěte, jak se připojit k Azure HDInsight pomocí ovladače ODBC Microsoft Power BI Desktopu a vizualizace dat Apache Hive.

>[!IMPORTANT]
> Můžete využít ovladače Hive ODBC pro import prostřednictvím konektoru obecná rozhraní ODBC v Power BI Desktopu. Však není doporučeno pro BI úlohy uvedené jako neinteraktivní povaze modul dotazu Hive. [Konektor HDInsight Interactive Query](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md) a [konektor HDInsight Spark](https://docs.microsoft.com/power-bi/spark-on-hdinsight-with-direct-connect) jsou lepší možnosti pro jejich výkon.

V tomto článku se načtení dat z `hivesampletable` tabulky Hive do Power BI. Tabulka Hive obsahuje data o využití některých mobilního telefonu. Potom vykreslení dat o využití na mapu světa:

![HDInsight Power BI sestavy mapy](./media/apache-hadoop-connect-hive-power-bi/hdinsight-power-bi-visualization.png)

Informace se vztahují taky na nové [Interactive Query](../interactive-query/apache-interactive-query-get-started.md) typ clusteru. Informace o připojení k HDInsight Interactive Query pomocí přímých dotazů najdete v tématu [data vizualizovat Interactive Query Hive pomocí Microsoft Power BI pomocí přímých dotazů v Azure HDInsight](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md).

## <a name="prerequisites"></a>Požadavky

Před provedením tohoto článku, musíte mít následující položky:

* **HDInsight cluster**. Cluster může být v clusteru HDInsight pomocí Hive nebo nově vydané clusteru Interactive Query. Vytváření clusterů najdete v části [vytvořit cluster](apache-hadoop-linux-tutorial-get-started.md#create-cluster).

* **[Microsoft Power BI Desktopu](https://powerbi.microsoft.com/desktop/)** . Můžete stáhnout kopii z [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=45331).

## <a name="create-hive-odbc-data-source"></a>Vytvoření zdroje dat Hive ODBC

Zobrazit [zdroje dat vytvořit Hive ODBC](apache-hadoop-connect-excel-hive-odbc-driver.md#create-apache-hive-odbc-data-source).

## <a name="load-data-from-hdinsight"></a>Načtení dat z HDInsight

Hivesampletable tabulka Hive obsahuje všechny clustery HDInsight.

1. Spuštění Power BI Desktopu.

2. V horní nabídce přejděte na **Domů** > **získat Data** > **více...** .

    ![Otevřít dat HDInsight Power BI](./media/apache-hadoop-connect-hive-power-bi/hdinsight-power-bi-open-odbc.png)

3. Z **získat Data** dialogového okna, vyberte **jiných** na levé straně vyberte **ODBC** zprava a pak vyberte **připojit** v dolní části.

4. Z **z rozhraní ODBC** dialogovém okně vyberte jste vytvořili v předchozí části, z rozevíracího seznamu název zdroje dat a pak vyberte **OK**.

5. Z **Navigátor** dialogového okna, rozbalte **ODBC > HIVE > výchozí**vyberte **hivesampletable**a pak vyberte **zatížení**.

6. Z **ovladač ODBC** dialogového okna, vyberte **výchozí nebo vlastní**a pak vyberte **připojit**.

## <a name="visualize-data"></a>Vizualizace dat

Pokračujte v posledním postupu.

1. V podokně vizualizace vyberte **mapy**.  Ikona glóbu je.

    ![HDInsight Power BI přizpůsobí sestavy](./media/apache-hadoop-connect-hive-power-bi/hdinsight-power-bi-customize.png)
2. Z **pole** vyberte **země** a **devicemake**. Zobrazí se dat zobrazovaných na mapě.
3. Rozbalte mapu.

## <a name="next-steps"></a>Další postup

V tomto článku jste zjistili, jak k vizualizaci dat z HDInsight pomocí Power BI.  Další informace naleznete v následujících článcích:

* [Použití Apache Zeppelinu ke spuštění dotazy Apache Hive v HDInsight Azure](../interactive-query/hdinsight-connect-hive-zeppelin.md).
* [Připojení Excelu k HDInsight pomocí ovladače ODBC Microsoft Hivu](./apache-hadoop-connect-excel-hive-odbc-driver.md).
* [Připojení Excelu k systému Apache Hadoop pomocí doplňku Power Query](apache-hadoop-connect-excel-power-query.md).
* [Připojení k Azure HDInsight a spouštět dotazy Apache Hive pomocí nástrojů Data Lake pro Visual Studio](apache-hadoop-visual-studio-tools-get-started.md).
* [Pomocí nástroje Azure HDInsight pro Visual Studio Code](../hdinsight-for-vscode.md).
* [Nahrání dat do HDInsight](./../hdinsight-upload-data.md).
