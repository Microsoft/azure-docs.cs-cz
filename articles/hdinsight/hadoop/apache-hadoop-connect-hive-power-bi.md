---
title: Vizualizace dat Apache Hive pomocí Power BI – Azure HDInsight
description: Přečtěte si, jak pomocí Microsoft Power BI vizualizovat data Hive zpracovaná službou Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 03/02/2020
ms.openlocfilehash: d9b64785dbd82842479eb3f313b8394f9f25b40b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79369994"
---
# <a name="visualize-apache-hive-data-with-microsoft-power-bi-using-odbc-in-azure-hdinsight"></a>Vizualizace dat Apache Hive s využitím Microsoft Power BI a ODBC ve službě Azure HDInsight

Přečtěte si, jak připojit Microsoft Power BI Desktop k Azure HDInsight pomocí ROZHRANÍ ODBC a vizualizovat data Apache Hive.

> [!IMPORTANT]
> Ovladač ODBC Hive můžete využít k importu pomocí obecného konektoru ODBC v Power BI Desktopu. Nedoporučuje se však pro úlohy BI dané neinteraktivní povahy dotazovacího stroje Hive. [Konektor interaktivních dotazů HDInsight](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md) a [konektor HDInsight Spark](https://docs.microsoft.com/power-bi/spark-on-hdinsight-with-direct-connect) jsou lepší volbou pro jejich výkon.

V tomto článku načtete `hivesampletable` data z tabulky Hive do Power BI. Tabulka Hive obsahuje některá data o používání mobilního telefonu. Potom vykreslíte údaje o využití na mapě světa:

![HDInsight Power BI sestava mapy](./media/apache-hadoop-connect-hive-power-bi/hdinsight-power-bi-visualization.png)

Tyto informace platí také pro nový typ clusteru [interaktivních dotazů.](../interactive-query/apache-interactive-query-get-started.md) Postup připojení k interaktivnímu dotazu HDInsight pomocí přímého dotazu najdete v [tématu Vizualizace dat interactive query hive pomocí Microsoft Power BI pomocí přímého dotazu v Azure HDInsight](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md).

## <a name="prerequisites"></a>Požadavky

Než si projdete tímto článkem, musíte mít následující položky:

* clusteru HDInsight. Cluster může být cluster HDInsight s Hive nebo nově vydané interaktivní dotaz clusteru. Vytváření clusterů naleznete v [tématu Vytvoření clusteru](apache-hadoop-linux-tutorial-get-started.md).

* [Microsoft Power BI Desktop](https://powerbi.microsoft.com/desktop/). Kopii si můžete stáhnout ze služby [Stažení softwaru](https://www.microsoft.com/download/details.aspx?id=45331).

## <a name="create-hive-odbc-data-source"></a>Vytvoření zdroje dat Hive ODBC

Viz [Vytvoření zdroje dat odhlašovatel odhlašovatelů odhlašovatelů odhlašovat](apache-hadoop-connect-excel-hive-odbc-driver.md#create-apache-hive-odbc-data-source)

## <a name="load-data-from-hdinsight"></a>Načítání dat z HDInsight

Tabulka **Hivesampletable** Hive je dodávána se všemi clustery HDInsight.

1. Spusťte Power BI Desktop.

1. V horní nabídce přejděte na **Home** > **Get Data** > **More...**.

    ![Otevřená data HDInsight Excel Power BI](./media/apache-hadoop-connect-hive-power-bi/hdinsight-power-bi-open-odbc.png)

1. V dialogovém okně **Získat data** vyberte zleva **možnost Jiné,** zprava vyberte **ODBC** a v dolní části vyberte **Připojit.**

1. V dialogovém okně **OdBC** vyberte název zdroje dat, který jste vytvořili v poslední části z rozevíracího seznamu. Pak vyberte **OK**.

1. Při prvním použití se otevře dialogové okno **ovladače ROZHRANÍ ODBC.** V levé nabídce vyberte **Výchozí nebo Vlastní.** Pak vyberte **Připojit,** chcete-li otevřít **navigátor**.

1. V dialogovém okně **Navigátor** rozbalte **možnost ODBC > HIVE > výchozí**, vyberte **hivesampletable**a pak vyberte **Načíst**.

## <a name="visualize-data"></a>Vizualizace dat

Pokračujte od posledního postupu.

1. V podokně Vizualizace vyberte **Mapovat**, je to ikona zeměkoule.

    ![HDInsight Power BI přizpůsobuje sestavu](./media/apache-hadoop-connect-hive-power-bi/hdinsight-power-bi-customize.png)

1. V podokně **Pole** vyberte **zemi** a **zařízení .** Můžete vidět data vykreslená na mapě.

1. Rozbalte mapu.

## <a name="next-steps"></a>Další kroky

V tomto článku jste se dozvěděli, jak vizualizovat data z HDInsightpomocí Power BI.  Další informace naleznete v následujících článcích:

* [Pomocí Apache Zeppelin můžete ve službě Azure HDInsight spouštět dotazy Apache Hive](../interactive-query/hdinsight-connect-hive-zeppelin.md).
* [Připojte Excel k hdinsightu pomocí ovladače Microsoft Hive ODBC .](./apache-hadoop-connect-excel-hive-odbc-driver.md)
* [Připojte Excel k Apache Hadoop pomocí Power Query](apache-hadoop-connect-excel-power-query.md).
* [Připojte se k Azure HDInsight a spouštět dotazy Apache Hive pomocí nástrojů Data Lake Tools pro Visual Studio](apache-hadoop-visual-studio-tools-get-started.md).
* [Použijte nástroj Azure HDInsight pro kód Visual Studia](../hdinsight-for-vscode.md).
* [Nahrajte data do HDInsight](./../hdinsight-upload-data.md).
