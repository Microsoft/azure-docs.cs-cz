---
title: Vizualizace Apache Hive dat pomocí Power BI – Azure HDInsight
description: Naučte se používat Microsoft Power BI k vizualizaci dat podregistru zpracovávaných službou Azure HDInsight.
keywords: HDInsight, Hadoop, podregistr, interaktivní dotaz, interaktivní podregistr, LLAP, ODBC
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,
ms.topic: conceptual
ms.date: 05/21/2019
ms.author: hrasheed
ms.openlocfilehash: 0e8f0e6ff6ba4b280d6174b6cec231ddca782912
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/17/2019
ms.locfileid: "71058605"
---
# <a name="visualize-apache-hive-data-with-microsoft-power-bi-using-odbc-in-azure-hdinsight"></a>Vizualizace Apache Hivech dat s využitím Microsoft Power BI pomocí rozhraní ODBC ve službě Azure HDInsight

Naučte se, jak připojit Microsoft Power BI Desktop k Azure HDInsight pomocí rozhraní ODBC a vizualizovat Apache Hive data.

>[!IMPORTANT]
> Můžete využít ovladač ODBC pro podregistr pro import prostřednictvím obecného konektoru ODBC v Power BI Desktop. Nedoporučuje se ale pro úlohy BI, které neinteraktivní charakterují modul dotazů na podregistr. [Konektor HDInsight Interactive pro dotazy](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md) a [konektor HDInsight Spark](https://docs.microsoft.com/power-bi/spark-on-hdinsight-with-direct-connect) jsou lepším výběrem pro jejich výkon.

V tomto článku načtete data z `hivesampletable` tabulky podregistru do Power BI. Tabulka podregistru obsahuje data o využití mobilních telefonů. Pak vykreslíte data o využití na světové mapě:

![HDInsight Power BI sestavy mapy](./media/apache-hadoop-connect-hive-power-bi/hdinsight-power-bi-visualization.png)

Tyto informace platí také pro nový typ clusteru [interaktivních dotazů](../interactive-query/apache-interactive-query-get-started.md) . Informace o tom, jak se připojit k interaktivnímu dotazu HDInsight pomocí přímých dotazů, najdete v tématu [vizualizace interaktivního dotazování dat v rámci služby Microsoft Power BI pomocí přímého dotazování ve službě Azure HDInsight](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md).

## <a name="prerequisites"></a>Požadavky

Před provedením tohoto článku musíte mít následující položky:

* **Cluster HDInsight**. Cluster může být buď cluster HDInsight s podregistrem, nebo nově vydaným clusterem interaktivních dotazů. Informace o vytváření clusterů najdete v tématu [Vytvoření clusteru](apache-hadoop-linux-tutorial-get-started.md#create-cluster).

* **[Microsoft Power BI Desktop](https://powerbi.microsoft.com/desktop/)** . Kopii si můžete stáhnout z webu [Stažení softwaru](https://www.microsoft.com/download/details.aspx?id=45331).

## <a name="create-hive-odbc-data-source"></a>Vytvoření zdroje dat Hive ODBC

Viz [Vytvoření zdroje dat ODBC pro podregistr](apache-hadoop-connect-excel-hive-odbc-driver.md#create-apache-hive-odbc-data-source).

## <a name="load-data-from-hdinsight"></a>Načtení dat ze služby HDInsight

Tabulka podregistru hivesampletable se dodává se všemi clustery HDInsight.

1. Spusťte Power BI Desktop.

2. V horní nabídce přejděte na **Domů** > **získat data** > **Další..** ..

    ![Aplikace Power BI v HDInsight – otevřít data](./media/apache-hadoop-connect-hive-power-bi/hdinsight-power-bi-open-odbc.png)

3. V dialogu **získat data** vyberte z levé strany možnost **jiné** , v pravém horním rohu vyberte **ODBC** a pak dole vyberte **připojit** .

4. V dialogovém okně **z rozhraní ODBC** vyberte v rozevíracím seznamu název zdroje dat, který jste vytvořili v poslední části, a pak vyberte **OK**.

5. V dialogovém okně **navigátor** rozbalte položku **> > podregistru rozhraní ODBC**, vyberte možnost **hivesampletable**a pak vyberte **načíst**.

6. V dialogovém okně **ovladač ODBC** vyberte **výchozí nebo vlastní**a pak vyberte **připojit**.

## <a name="visualize-data"></a>Vizualizace dat

Pokračujte od posledního postupu.

1. V podokně vizualizace vyberte **Mapa**.  Ikona zeměkoule

    ![Sestava přizpůsobení Power BI HDInsight](./media/apache-hadoop-connect-hive-power-bi/hdinsight-power-bi-customize.png)
2. V podokně **pole** vyberte Country ( **země** ) a **devicemake**(země). Data vykreslená na mapě vidíte.
3. Rozbalte mapu.

## <a name="next-steps"></a>Další postup

V tomto článku jste zjistili, jak vizualizovat data ze služby HDInsight pomocí Power BI.  Další informace najdete v následujících článcích:

* [Použijte Apache Zeppelin ke spouštění dotazů Apache Hive ve službě Azure HDInsight](../interactive-query/hdinsight-connect-hive-zeppelin.md).
* [Připojte Excel k HDInsight pomocí Microsoft Hive ODBC Driver](./apache-hadoop-connect-excel-hive-odbc-driver.md).
* [Připojení Excelu k Apache Hadoop pomocí Power Query](apache-hadoop-connect-excel-power-query.md).
* [Připojte se ke službě Azure HDInsight a spouštějte Apache Hive dotazy pomocí nástrojů Data Lake pro Visual Studio](apache-hadoop-visual-studio-tools-get-started.md).
* [Použijte nástroj Azure HDInsight pro Visual Studio Code](../hdinsight-for-vscode.md).
* [Nahrajte data do HDInsight](./../hdinsight-upload-data.md).
