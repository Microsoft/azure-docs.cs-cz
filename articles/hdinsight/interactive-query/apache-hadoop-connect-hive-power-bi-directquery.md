---
title: Podívejte se na interaktivní data hive dotazů s Power BI v Azure HDInsight
description: Vizualizace dat interactive Query Hive z Azure HDInsight pomocí Microsoft Power BI
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/17/2019
ms.openlocfilehash: adcd4d9e81eecad9540a4ef1be5e675f940ffb8d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79367988"
---
# <a name="visualize-interactive-query-apache-hive-data-with-microsoft-power-bi-using-direct-query-in-hdinsight"></a>Vizualizace dat Apache Hive interaktivního dotazu pomocí Microsoft Power BI pomocí přímého dotazu v HDInsightu

Tento článek popisuje, jak připojit Microsoft Power BI ke clusterům interaktivních dotazů Azure HDInsight a vizualizovat data Apache Hive pomocí přímého dotazu. Zadaný příklad načte data `hivesampletable` z tabulky Hive do Power BI. Tabulka `hivesampletable` Hive obsahuje některá data o používání mobilního telefonu. Potom vykreslíte údaje o využití na mapě světa:

![HDInsight Power BI sestava mapy](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-power-bi-visualization.png)

[Ovladač ODBC Apache Hive](../hadoop/apache-hadoop-connect-hive-power-bi.md) můžete využít k importu přes obecný konektor ODBC v Power BI Desktopu. Nedoporučuje se však pro úlohy BI dané neinteraktivní povahy dotazovacího stroje Hive. [Konektor interaktivních dotazů HDInsight](./apache-hadoop-connect-hive-power-bi-directquery.md) a [konektor HDInsight Apache Spark](https://docs.microsoft.com/power-bi/spark-on-hdinsight-with-direct-connect) jsou lepší volbou pro jejich výkon.

## <a name="prerequisites"></a>Požadavky
Než si projdete tímto článkem, musíte mít následující položky:

* **cluster HDInsight**. Cluster může být buď cluster HDInsight s Apache Hive nebo nově vydané interaktivní dotaz clusteru. Vytváření clusterů naleznete v [tématu Vytvoření clusteru](../hadoop/apache-hadoop-linux-tutorial-get-started.md).
* **[Microsoft Power BI Desktop](https://powerbi.microsoft.com/desktop/)**. Kopii si můžete stáhnout ze služby [Stažení softwaru](https://www.microsoft.com/download/details.aspx?id=45331).

## <a name="load-data-from-hdinsight"></a>Načítání dat z HDInsight

Tabulka `hivesampletable` Hive je dodávána se všemi clustery HDInsight.

1. Spusťte Power BI Desktop.

2. Z řádku nabídek přejděte na **Domovzískat** > **data** > **více...**.

    ![HDInsight Power BI získejte další data](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-power-bi-open-odbc.png)

3. V okně **Získat data** zadejte **hdinsight** do vyhledávacího pole.  

4. Ve výsledcích hledání vyberte **HDInsight Interactive Query**a pak vyberte **Připojit**.  Pokud interaktivní dotaz **HDInsight**nevidíte , je potřeba aktualizovat Power BI Desktop na nejnovější verzi.

5. Vyberte **Pokračovat,** chcete-li zavřít dialogové **okno Připojení ke službě jiného výrobce.**

6. V okně **Interaktivní dotaz HDInsight** zadejte následující informace a pak vyberte **OK**:

    |Vlastnost | Hodnota |
    |---|---|
    |Server |Zadejte název clusteru, například *myiqcluster.azurehdinsight.net*.|
    |Databáze |Zadejte **výchozí hodnotu** pro tento článek.|
    |Režim připojení dat |Vyberte **DirectQuery** pro tento článek.|

    ![Interaktivní dotaz HDInsight Připojení Power BI DirectQuery](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-interactive-query-power-bi-connect.png)

7. Zadejte pověření HTTP a vyberte **Připojit**. Výchozí uživatelské jméno je **admin**.

8. V okně **Navigátor** v levém podokně vyberte **možnost hivesampletale**.

9. V hlavním okně vyberte **Načíst.**

    ![Interaktivní dotaz HDInsight, který je zdrojem vzorku hivehi Power BI](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-interactive-query-power-bi-hivesampletable.png)

## <a name="visualize-data-on-a-map"></a>Vizualizace dat na mapě

Pokračujte od posledního postupu.

1. V podokně Vizualizace vyberte **Mapovat**, ikonu zeměkoule. V hlavním okně se pak zobrazí obecná mapa.

    ![HDInsight Power BI přizpůsobuje sestavu](./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-power-bi-customize.png)

2. V podokně Pole vyberte **zemi** a **zařízení .** Mapa světa s datovými body se po několika okamžicích zobrazí v hlavním okně.

3. Rozbalte mapu.

## <a name="next-steps"></a>Další kroky
V tomto článku jste se dozvěděli, jak vizualizovat data z HDInsight pomocí Microsoft Power BI.  Další informace o vizualizaci dat naleznete v následujících článcích:

* [Vizualizujte data Apache Hive s Microsoft Power BI pomocí ROZHRANÍ ODBC v Azure HDInsight](../hadoop/apache-hadoop-connect-hive-power-bi.md). 
* [Pomocí Apache Zeppelin můžete ve službě Azure HDInsight spouštět dotazy Apache Hive](../interactive-query/hdinsight-connect-hive-zeppelin.md).
* [Připojte Excel k hdinsightu pomocí ovladače Microsoft Hive ODBC .](../hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md)
* [Připojte Excel k Apache Hadoop pomocí Power Query](../hadoop/apache-hadoop-connect-excel-power-query.md).
* [Připojte se k Azure HDInsight a spouštět dotazy Apache Hive pomocí nástrojů Data Lake Tools pro Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md).
* [Použijte nástroj Azure HDInsight pro kód Visual Studia](../hdinsight-for-vscode.md).
* [Nahrajte data do HDInsight](./../hdinsight-upload-data.md).
