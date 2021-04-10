---
title: Zobrazení dat podregistru interaktivního dotazu pomocí Power BI ve službě Azure HDInsight
description: Použití Microsoft Power BI k vizualizaci dat podregistru interaktivních dotazů z Azure HDInsight
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: how-to
ms.date: 06/17/2019
ms.openlocfilehash: 05199968339329632c2e68e9604e3f5308e8b12b
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "104871652"
---
# <a name="visualize-interactive-query-apache-hive-data-with-microsoft-power-bi-using-direct-query-in-hdinsight"></a>Vizualizace interaktivních dotazů Apache Hive dat pomocí Microsoft Power BI pomocí přímého dotazování v HDInsight

Tento článek popisuje, jak připojit Microsoft Power BI k clusterům s interaktivními dotazy Azure HDInsight a vizualizovat Apache Hive data pomocí přímých dotazů. Zadaný příklad načte data z `hivesampletable` tabulky podregistru do Power BI. `hivesampletable`Tabulka podregistru obsahuje data o využití mobilních telefonů. Pak vykreslíte data o využití na světové mapě:

:::image type="content" source="./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-power-bi-visualization.png" alt-text="HDInsight Power BI sestavy mapy" border="true":::

Pomocí [ovladače Apache Hive ODBC](../hadoop/apache-hadoop-connect-hive-power-bi.md) můžete provádět import prostřednictvím obecného konektoru odbc v Power BI Desktop. Nedoporučuje se ale pro úlohy BI, které neinteraktivní charakterují modul dotazů na podregistr. Konektor [HDInsight Interactive pro dotazy](./apache-hadoop-connect-hive-power-bi-directquery.md) a [konektor HDInsight Apache Spark](/power-bi/spark-on-hdinsight-with-direct-connect) jsou lepšími možnostmi pro jejich výkon.

## <a name="prerequisites"></a>Požadavky
Před provedením tohoto článku musíte mít následující položky:

* **Cluster HDInsight**. Cluster může být buď cluster HDInsight s Apache Hive, nebo nově vydaným clusterem interaktivních dotazů. Informace o vytváření clusterů najdete v tématu [Vytvoření clusteru](../hadoop/apache-hadoop-linux-tutorial-get-started.md).
* **[Microsoft Power BI Desktop](https://powerbi.microsoft.com/desktop/)**. Kopii si můžete stáhnout z webu [Stažení softwaru](https://www.microsoft.com/download/details.aspx?id=45331).

## <a name="load-data-from-hdinsight"></a>Načtení dat ze služby HDInsight

`hivesampletable`Tabulka podregistr je dodávána se všemi clustery HDInsight.

1. Spusťte Power BI Desktop.

2. V řádku nabídek přejděte na **Domů**  >  **získat data**  >  **Další..**..

    :::image type="content" source="./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-power-bi-open-odbc.png" alt-text="HDInsight Power BI získat data více" border="true":::

3. V okně **získat data** zadejte do vyhledávacího pole **HDInsight** .  

4. Ve výsledcích hledání vyberte možnost **HDInsight Interactive Query** a pak vyberte **připojit**.  Pokud nevidíte **interaktivní dotaz HDInsight**, musíte aktualizovat Power BI Desktop na nejnovější verzi.

5. Vyberte **pokračovat** a zavřete tak dialogové okno **připojení ke službě třetí strany** .

6. V okně **dotaz interaktivního dotazu HDInsight** zadejte následující informace a pak vyberte **OK**:

    |Vlastnost | Hodnota |
    |---|---|
    |Server |Zadejte název clusteru, například *myiqcluster.azurehdinsight.NET*.|
    |databáze |Zadejte **výchozí nastavení** tohoto článku.|
    |Režim připojení dat |Pro tento článek vyberte **DirectQuery** .|

    :::image type="content" source="./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-interactive-query-power-bi-connect.png" alt-text="Interaktivní dotaz HDInsight Power BI DirectQuery Connect" border="true":::

7. Zadejte přihlašovací údaje HTTP a pak vyberte **připojit**. Výchozí uživatelské jméno je **admin**.

8. V okně **navigátor** v levém podokně vyberte **hivesampletale**.

9. V hlavním okně vyberte **načíst** .

    :::image type="content" source="./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-interactive-query-power-bi-hivesampletable.png" alt-text="Interaktivní dotaz HDInsight Power BI hivesampletable" border="true":::

## <a name="visualize-data-on-a-map"></a>Vizualizace dat na mapě

Pokračujte od posledního postupu.

1. V podokně vizualizace vyberte **Mapa**, ikona zeměkoule. V hlavním okně se pak zobrazí obecná mapa.

    :::image type="content" source="./media/apache-hadoop-connect-hive-power-bi-directquery/hdinsight-power-bi-customize.png" alt-text="Sestava přizpůsobení Power BI HDInsight" border="true":::

2. V podokně pole vyberte Country ( **země** ) a **devicemake**(země). Mapa světa s datovými body se po chvíli objeví v hlavním okně.

3. Rozbalte mapu.

## <a name="next-steps"></a>Další kroky
V tomto článku jste zjistili, jak vizualizovat data z HDInsight pomocí Microsoft Power BI.  Další informace o vizualizaci dat najdete v následujících článcích:

* [Vizualizujte Apache Hive data pomocí Microsoft Power BI pomocí ODBC ve službě Azure HDInsight](../hadoop/apache-hadoop-connect-hive-power-bi.md). 
* [Použijte Apache Zeppelin ke spouštění dotazů Apache Hive ve službě Azure HDInsight](../interactive-query/hdinsight-connect-hive-zeppelin.md).
* [Připojte Excel k HDInsight pomocí Microsoft Hive ODBC Driver](../hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md).
* [Připojení Excelu k Apache Hadoop pomocí Power Query](../hadoop/apache-hadoop-connect-excel-power-query.md).
* [Připojte se ke službě Azure HDInsight a spouštějte Apache Hive dotazy pomocí nástrojů Data Lake pro Visual Studio](../hadoop/apache-hadoop-visual-studio-tools-get-started.md).
* [Použijte nástroj Azure HDInsight pro Visual Studio Code](../hdinsight-for-vscode.md).
* [Nahrajte data do HDInsight](./../hdinsight-upload-data.md).