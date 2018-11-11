---
title: Vizualizace dat Interactive Query Hive pomocí Power BI v Azure HDInsight
description: Použít Microsoft Power BI k vizualizaci dat Interactive Query Hive z Azure HDInsight
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/06/2018
ms.openlocfilehash: 46b5b3436a33c3f83d85cfb02028759d53d214af
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2018
ms.locfileid: "51245269"
---
# <a name="visualize-interactive-query-hive-data-with-microsoft-power-bi-using-direct-query-in-azure-hdinsight"></a>Vizualizace dat Interactive Query Hive pomocí Microsoft Power BI pomocí přímých dotazů v Azure HDInsight

Tento článek popisuje, jak připojit Microsoft Power BI do clusterů Azure HDInsight Interactive Query a vizualizovat data Apache Hive pomocí přímých dotazů. Ukázkový text do Power BI načte data z tabulky Hive hivesampletable. Tabulka Hive hivesampletable obsahuje data o využití některých mobilního telefonu. Potom vykreslení dat o využití na mapu světa:

![HDInsight Power BI sestavy mapy](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-power-bi-visualization.png)

Můžete využít [ovladače Hive ODBC](../hadoop/apache-hadoop-connect-hive-power-bi.md) import prostřednictvím konektoru obecná rozhraní ODBC v Power BI Desktopu. Však není doporučeno pro BI úlohy uvedené jako neinteraktivní povaze modul dotazu Hive. [Konektor HDInsight Interactive Query](./apache-hadoop-connect-hive-power-bi-directquery.md) a [konektor HDInsight Spark](https://docs.microsoft.com/power-bi/spark-on-hdinsight-with-direct-connect) jsou lepší možnosti pro jejich výkon.

## <a name="prerequisites"></a>Požadavky
Před provedením tohoto článku, musíte mít následující položky:

* **HDInsight cluster**. Cluster může být v clusteru služby HDInsight pomocí Hive nebo nově vydané clusteru Interactive Query. Vytváření clusterů najdete v části [vytvořit cluster](../hadoop/apache-hadoop-linux-tutorial-get-started.md#create-cluster).
* **[Microsoft Power BI Desktopu](https://powerbi.microsoft.com/desktop/)**. Můžete stáhnout kopii z [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=45331).

## <a name="load-data-from-hdinsight"></a>Načtení dat z HDInsight

Hivesampletable tabulka Hive obsahuje všechny clustery HDInsight.

1. Přihlaste se k Power BI Desktopu.

2. Klikněte na tlačítko **Domů** klikněte na tlačítko **získat Data** z **externí data** pásu karet a potom vyberte **více...** .

    ![Otevřít dat HDInsight Power BI](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-power-bi-open-odbc.png)
    
3. Z **získat Data** podokně, zadejte **hdinsight** do vyhledávacího pole. Pokud nevidíte **HDInsight Interactive Query (beta verze)**, je potřeba aktualizovat na nejnovější verzi vaší Power BI Desktopu.

4. Vyberte **HDInsight Interactive Query (beta verze)** a pak vyberte **připojit**.

5. Vyberte **pokračovat** zavřete **Předběžná verze konektoru** dialog s upozorněním.

6. Z **HDInsight Interactive Query**, vyberte nebo zadejte následující informace:

    - **Server**: Zadejte název clusteru Interactive Query, například *myiqcluster.azurehdinsight.net*.

    - **Databáze**: pro účely tohoto kurzu zadejte **výchozí**.
    
    - **Režim připojení dat**: pro účely tohoto kurzu vyberte **DirectQuery**.

    ![Připojení k HDInsight interactive query Power BI DirectQuery](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-interactive-query-power-bi-connect.png)

7. Klikněte na **OK**.

8. Zadejte přihlašovací údaje uživatele protokolu HTTP a potom klikněte na tlačítko **OK**. Výchozí uživatelské jméno **správce**

9. V levém podokně vyberte **hivesampletale**a potom klikněte na tlačítko **zatížení**.

    ![HDInsight interactive query Power BI hivesampletable](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-interactive-query-power-bi-hivesampletable.png)

## <a name="visualize-data-on-a-map"></a>Vizualizace dat na mapě

Pokračujte v posledním postupu.

1. V podokně vizualizace vyberte **mapy**.  Ikona glóbu je.

    ![HDInsight Power BI přizpůsobí sestavy](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-power-bi-customize.png)
    
2. V podokně pole vyberte **země** a **devicemake**. Zobrazí se dat zobrazovaných na mapě.

3. Rozbalte mapu.

## <a name="next-steps"></a>Další postup
V tomto článku jste zjistili, jak k vizualizaci dat z HDInsight pomocí Power BI.  Další informace o vizualizaci dat najdete v následujících článcích:

* [Vizualizace dat Hive pomocí Microsoft Power BI v Azure HDInsight pomocí ovladače ODBC](../hadoop/apache-hadoop-connect-hive-power-bi.md). 
* [Použití Zeppelinu ke spouštění dotazů Hive v HDInsight Azure](./../hdinsight-connect-hive-zeppelin.md).
* [Připojení Excelu k HDInsight pomocí ovladače ODBC Microsoft Hivu](../hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md).
* [Připojení Excelu k Hadoopu pomocí Power Query](../hadoop/apache-hadoop-connect-excel-power-query.md).
* [Připojení k Azure HDInsight a spouštění dotazů Hive pomocí nástrojů Data Lake pro Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md).
* [Pomocí nástroje Azure HDInsight pro Visual Studio Code](../hdinsight-for-vscode.md).
* [Nahrání dat do HDInsight](./../hdinsight-upload-data.md).
