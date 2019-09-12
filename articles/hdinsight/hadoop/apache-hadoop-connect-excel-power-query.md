---
title: Připojení Excelu k Apache Hadoop s využitím Power Query-Azure HDInsight
description: Naučte se využívat výhod business intelligencech komponent a používat Power Query pro Excel k přístupu k datům uloženým v Hadoop v HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/16/2018
ms.openlocfilehash: de72daa6d34ea54517d5a21d7467a62d8097581c
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/11/2019
ms.locfileid: "70882683"
---
# <a name="connect-excel-to-apache-hadoop-by-using-power-query"></a>Připojení Excelu k Apache Hadoop pomocí Power Query
Jedním z klíčových funkcí řešení pro velké objemy dat společnosti Microsoft je integrace součástí Microsoft business intelligence (BI) s Apache Hadoop clustery ve službě Azure HDInsight. Primárním příkladem je možnost připojení Excelu k účtu Azure Storage, který obsahuje data přidružená k vašemu clusteru Hadoop pomocí doplňku Microsoft Power Query pro Excel. Tento článek vás seznámí s postupem nastavení a použití Power Query k dotazování na data přidružená k clusteru Hadoop spravovanému pomocí HDInsight.

### <a name="prerequisites"></a>Požadavky
Než začnete tento článek, musíte mít následující položky:

* **Cluster an HDInsight**. Pokud ho chcete nakonfigurovat, přečtěte si téma Začínáme [se službou Azure HDInsight](./apache-hadoop-linux-tutorial-get-started.md).
* **Pracovní stanice** se systémem Windows 7, windows Server 2008 R2 nebo novějším operačním systémem.
* Office **2016, office 2013 Professional Plus, office 365 ProPlus, Excel 2013 Standalone nebo office 2010 Professional Plus**.

## <a name="install-power-query"></a>Nainstalovat Power Query
Power Query může importovat data, která byla vygenerována pomocí úlohy Hadoop spuštěné v clusteru HDInsight.

V aplikaci Excel 2016 byla Power Query integrována do pásu karet data v sekci získat & transformaci. Pro starší verze Excelu Stáhněte Microsoft Power Query pro Excel z webu [Microsoft Download Center](https://go.microsoft.com/fwlink/?LinkID=286689) a nainstalujte ho.

## <a name="import-hdinsight-data-into-excel"></a>Import dat HDInsight do Excelu
Doplněk Power Query pro Excel usnadňuje import dat z clusteru HDInsight do Excelu, kde nástroje pro BI, jako je PowerPivot a Power Map, se dají použít ke kontrole, analýze a prezentaci dat.

**Import dat z clusteru HDInsight**

1. Otevřete aplikaci Excel.
2. Vytvoří nový prázdný sešit.
3. Proveďte následující kroky v závislosti na verzi aplikace Excel:

   - Excel 2016

     - Klikněte na nabídku **data** , klikněte na možnost **získat data** na pásu karet **získat & transformovat data** , klikněte na možnost **z Azure**a pak klikněte **ze služby Azure HDInsight (HDFS)** .

       ![HDI. PowerQuery. SelectHdiSource. 2016](./media/apache-hadoop-connect-excel-power-query/powerquery-selecthdisource-excel2016.png)

   - Excel 2013/2010

     - Klikněte na nabídku **Power Query** , klikněte na možnost **z Azure**a pak klikněte na **z Microsoft Azure HDInsight**.
   
       ![HDI.PowerQuery.SelectHdiSource](./media/apache-hadoop-connect-excel-power-query/powerquery-selecthdisource.png)
       
       **Poznámka:** Pokud nevidíte nabídku **Power Query** , přejděte na**Možnosti** >  **souborů** > **Doplňky**a v rozevíracím seznamu **Spravovat** v dolní části stránky vyberte **Doplňky modelu COM** . Vyberte tlačítko **Přejít do...** a ověřte, že je zaškrtnuté políčko pro doplněk Power Query pro Excel.
       
       **Poznámka:** Power Query také umožňuje importovat data ze HDFS kliknutím na **jiné zdroje**.
4. Do pole **název účtu**zadejte název účtu úložiště objektů BLOB v Azure, který je přidružený k vašemu clusteru, a pak klikněte na **OK**. Tento účet může být výchozí účet úložiště nebo propojený účet úložiště.  Formát je *https://&lt;StorageAccountName >. blob. Core. Windows. NET/* .
5. V poli **klíč účtu**zadejte klíč pro účet úložiště objektů BLOB a pak klikněte na **Uložit**. (Musíte zadat informace o účtu jenom při prvním přístupu k tomuto úložišti.)
6. V podokně **navigátor** na levé straně editoru dotazů poklikejte na název kontejneru úložiště objektů BLOB. Ve výchozím nastavení má název kontejneru stejný název jako název clusteru.
7. Ve sloupci **název** vyhledejte **HiveSampleData. txt** (cesta ke složce je **.. /Hive/Warehouse/hivesampletable/** ) a pak klikněte na **binární soubor** na levé straně souboru HiveSampleData. txt. HiveSampleData. txt se dodává se všemi clustery. Volitelně můžete použít vlastní soubor.
   
    ![HDI.PowerQuery.ImportData](./media/apache-hadoop-connect-excel-power-query/powerquery-importdata.png)

8. Pokud chcete, můžete názvy sloupců přejmenovat. Až budete připraveni, klikněte na **zavřít & načíst**.  Data se načetla do vašeho sešitu:
   
    ![HDI.PowerQuery.ImportedTable](./media/apache-hadoop-connect-excel-power-query/powerquery-importedtable.png)

## <a name="next-steps"></a>Další kroky
V tomto článku jste zjistili, jak pomocí Power Query načíst data z HDInsight do Excelu. Podobně můžete data z HDInsight načíst do Azure SQL Database. Data je také možné odeslat do HDInsight. Další informace najdete v následujících článcích:

* [Vizualizujte Apache Hive data pomocí Microsoft Power BI ve službě Azure HDInsight](apache-hadoop-connect-hive-power-bi.md).
* [Vizualizovat data podregistru interaktivního dotazu pomocí Power BI ve službě Azure HDInsight](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md).
* [Použijte Apache Zeppelin ke spouštění dotazů Apache Hive ve službě Azure HDInsight](../interactive-query/hdinsight-connect-hive-zeppelin.md).
* [Připojte Excel k HDInsight pomocí Microsoft Hive ODBC Driver](apache-hadoop-connect-excel-hive-odbc-driver.md).
* [Připojte se ke službě Azure HDInsight a spouštějte Apache Hive dotazy pomocí nástrojů Data Lake pro Visual Studio](apache-hadoop-visual-studio-tools-get-started.md).
* [Použijte nástroj Azure HDInsight pro Visual Studio Code](../hdinsight-for-vscode.md).
* [Nahrajte data do HDInsight](./../hdinsight-upload-data.md).
