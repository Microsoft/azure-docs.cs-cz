---
title: Připojení Excelu k Apache Hadoop pomocí Power Query – Azure HDInsight
description: Přečtěte si, jak využít komponenty business intelligence a jak pomocí Power Query pro Excel přistupovat k datům uloženým v Hadoopu na HDInsightu.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/17/2019
ms.openlocfilehash: e643c7fe7b18eed30843e7cab3977036435d2112
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75435805"
---
# <a name="connect-excel-to-apache-hadoop-by-using-power-query"></a>Připojení Excelu k Apache Hadoop pomocí Power Query

Jednou z klíčových vlastností řešení microsoftu pro velká data je integrace komponent Microsoft business intelligence (BI) s clustery Apache Hadoop v Azure HDInsight. Primárním příkladem je možnost připojit Excel k účtu Azure Storage, který obsahuje data přidružená k vašemu clusteru Hadoop pomocí doplňku Microsoft Power Query pro Excel. Tento článek vás provede nastavením a použitím Power Query k dotazování dat přidružených ke clusteru Hadoop spravovanému pomocí HDInsightu.

## <a name="prerequisites"></a>Požadavky

* Cluster Apache Hadoop na HDInsight. Viz [Začínáme s HDInsight na Linuxu](./apache-hadoop-linux-tutorial-get-started.md).
* Pracovní stanice se systémem Windows 10, 7, Windows Server 2008 R2 nebo novějším operačním systémem.
* Office 2016, Office 2013 Professional Plus, Office 365 ProPlus, Excel 2013 Samostatný nebo Office 2010 Professional Plus.

## <a name="install-microsoft-power-query"></a>Instalace Microsoft Power Query

Power Query můžete importovat data, která byla výstup nebo které byly generovány úlohou Hadoop spuštěné v clusteru HDInsight.

V Excelu 2016 byl Power Query integrovaný do pásu karet Data v části Získat & transformace. U starších verzí aplikace Excel stáhněte Microsoft Power Query pro Excel ze [služby Stažení softwaru a](https://go.microsoft.com/fwlink/?LinkID=286689) nainstalujte jej.

## <a name="import-hdinsight-data-into-excel"></a>Import dat HDInsight do Excelu

Doplněk Power Query pro Excel usnadňuje import dat z clusteru HDInsight do Excelu, kde lze k kontrole, analýze a prezentaci dat použít nástroje BI, jako je PowerPivot a Power Map.

1. Spusťte Excel.

1. Vytvořte nový prázdný sešit.

1. Proveďte následující kroky na základě verze aplikace Excel:

   * Excel 2016

     * Vyberte > **data** > **získat data** > **z Azure** > **z Azure HDInsight (HDFS)**.

       ![Hdi. PowerQuery.SelectHdiSource.2016](./media/apache-hadoop-connect-excel-power-query/powerquery-selecthdisource-excel2016.png)

   * Excel 2013/2010

     * Vyberte **Power Query** > **z Azure** > **z Microsoft Azure HDInsight**.

       ![Hdi. PowerQuery.SelectHdiSource](./media/apache-hadoop-connect-excel-power-query/powerquery-selecthdisource.png)

       **Poznámka:** Pokud nabídku Power **Query** nevidíte, přejděte na**Doplňky****S možností** >  **souborů** > a v rozevíracím poli **Správa** v dolní části stránky vyberte **doplňky com.** Vyberte tlačítko **Přejít...** a ověřte, jestli bylo zaškrtnuté políčko doplňku Power Query pro Excel.

       **Poznámka:** Power Query také umožňuje importovat data z HDFS výběrem **z jiných zdrojů**.

1. V dialogovém okně **Azure HDInsight (HDFS)** zadejte do textového pole **Název účtu nebo URL** název účtu úložiště objektů blob Azure přidruženého k vašemu clusteru. Pak vyberte **OK**. Tento účet může být výchozí účet úložiště nebo účet propojeného úložiště.  Formát je `https://StorageAccountName.blob.core.windows.net/`.

1. V **části Klíč účtu**zadejte klíč pro účet úložiště objektů Blob a pak vyberte **Připojit**. (Informace o účtu je třeba zadat pouze při prvním přístupu do tohoto obchodu.)

1. V podokně **Navigátor** nalevé straně Editoru dotazů poklikejte na název kontejneru úložiště objektů Blob přidružený k vašemu clusteru. Ve výchozím nastavení je název kontejneru stejný jako název clusteru.

1. Vyhledejte **soubor HiveSampleData.txt** ve sloupci **Název** (cesta ke složce je **.. /hive/warehouse/hivesampletable/**) a potom vyberte **binární** na levé straně HiveSampleData.txt. Soubor HiveSampleData.txt je dodáván se všemi clustery. Volitelně můžete použít vlastní soubor.

    ![Data importu power query HDI Excel](./media/apache-hadoop-connect-excel-power-query/powerquery-importdata.png)

1. Pokud chcete, můžete názvy sloupců přejmenovat. Až budete připraveni, vyberte **Zavřít & načíst**.  Data byla načtena do sešitu:

    ![Importovaná tabulka importované hodovacího dotazu na HDI Excel](./media/apache-hadoop-connect-excel-power-query/powerquery-importedtable.png)

## <a name="next-steps"></a>Další kroky

V tomto článku jste se dozvěděli, jak pomocí Power Query načíst data z HDInsightu do Excelu. Podobně můžete načíst data z HDInsight do Azure SQL Database. Je také možné nahrát data do HDInsight. Další informace naleznete v následujících článcích:

* [Vizualizujte data Apache Hive pomocí Microsoft Power BI v Azure HDInsight](apache-hadoop-connect-hive-power-bi.md).
* [Vizualizujte data interactive query hive pomocí Power BI v Azure HDInsight](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md).
* [Pomocí Apache Zeppelin můžete ve službě Azure HDInsight spouštět dotazy Apache Hive](../interactive-query/hdinsight-connect-hive-zeppelin.md).
* [Připojte Excel k hdinsightu pomocí ovladače Microsoft Hive ODBC .](apache-hadoop-connect-excel-hive-odbc-driver.md)
* [Připojte se k Azure HDInsight a spouštět dotazy Apache Hive pomocí nástrojů Data Lake Tools pro Visual Studio](apache-hadoop-visual-studio-tools-get-started.md).
* [Použijte nástroj Azure HDInsight pro kód Visual Studia](../hdinsight-for-vscode.md).
* [Nahrajte data do HDInsight](./../hdinsight-upload-data.md).
