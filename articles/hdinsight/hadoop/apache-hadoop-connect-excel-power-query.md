---
title: Připojení Excelu k Apache Hadoop s využitím Power Query-Azure HDInsight
description: Naučte se využívat výhod business intelligencech komponent a používat Power Query pro Excel k přístupu k datům uloženým v Hadoop v HDInsight.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 12/17/2019
ms.openlocfilehash: bcca511bcb61ceab8395ee56b377efa99f095586
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98946702"
---
# <a name="connect-excel-to-apache-hadoop-by-using-power-query"></a>Připojení Excelu k Apache Hadoop pomocí Power Query

Jedním z klíčových funkcí řešení pro velké objemy dat společnosti Microsoft je integrace součástí Microsoft business intelligence (BI) s Apache Hadoop clustery ve službě Azure HDInsight. Primárním příkladem je možnost připojení Excelu k účtu Azure Storage, který obsahuje data přidružená k vašemu clusteru Hadoop pomocí doplňku Microsoft Power Query pro Excel. Tento článek vás seznámí s postupem nastavení a použití Power Query k dotazování na data přidružená k clusteru Hadoop spravovanému pomocí HDInsight.

## <a name="prerequisites"></a>Předpoklady

* Cluster Apache Hadoop v HDInsight. Viz Začínáme [se službou HDInsight v systému Linux](./apache-hadoop-linux-tutorial-get-started.md).
* Pracovní stanice se systémem Windows 10, 7, Windows Server 2008 R2 nebo novějším operačním systémem.
* Microsoft 365 aplikace pro podniky, Office 2016, Office 2013 Professional Plus, Excel 2013 Standalone nebo Office 2010 Professional Plus.

## <a name="install-microsoft-power-query"></a>Nainstalovat Microsoft Power Query

Power Query může importovat data, která byla vygenerována pomocí úlohy Hadoop spuštěné v clusteru HDInsight.

V aplikaci Excel 2016 byla Power Query integrována do pásu karet data v sekci získat & transformaci. Pro starší verze Excelu Stáhněte Microsoft Power Query pro Excel z webu [Microsoft Download Center](https://go.microsoft.com/fwlink/?LinkID=286689) a nainstalujte ho.

## <a name="import-hdinsight-data-into-excel"></a>Import dat HDInsight do Excelu

Doplněk Power Query pro Excel usnadňuje import dat z clusteru HDInsight do Excelu, kde nástroje pro BI, jako je PowerPivot a Power Map, se dají použít ke kontrole, analýze a prezentaci dat.

1. Spusťte aplikaci Excel.

1. Vytvoří nový prázdný sešit.

1. Proveďte následující kroky v závislosti na verzi aplikace Excel:

   * Excel 2016

     * Vyberte > **data**  >  **získat data**  >  **z Azure**  >  **z Azure HDInsight (HDFS)**.

       ![HDI. PowerQuery. SelectHdiSource. 2016](./media/apache-hadoop-connect-excel-power-query/powerquery-selecthdisource-excel2016.png)

   * Excel 2013/2010

     * Z Microsoft Azure HDInsight vyberte **Power Query**  >  **z Azure**  >  .

       ![HDI. PowerQuery. SelectHdiSource](./media/apache-hadoop-connect-excel-power-query/powerquery-selecthdisource.png)

       **Poznámka:** Pokud nevidíte nabídku **Power Query** , přejděte na možnosti **souborů**  >    >  **Doplňky** a v rozevíracím seznamu **Spravovat** v dolní části stránky vyberte **Doplňky modelu COM** . Vyberte tlačítko **Přejít do...** a ověřte, že je zaškrtnuté políčko pro doplněk Power Query pro Excel.

       **Poznámka:** Power Query také umožňuje importovat data ze HDFS výběrem **z jiných zdrojů**.

1. V dialogovém okně **Azure HDInsight (HDFS)** zadejte do textového pole **název účtu nebo adresa URL** název účtu úložiště objektů BLOB v Azure, který je přidružený k vašemu clusteru. Pak vyberte **OK**. Tento účet může být výchozí účet úložiště nebo propojený účet úložiště.  Formát je `https://StorageAccountName.blob.core.windows.net/` .

1. V poli **klíč účtu** zadejte klíč pro účet úložiště objektů BLOB a pak vyberte **připojit**. (Musíte zadat informace o účtu jenom při prvním přístupu k tomuto úložišti.)

1. V podokně **navigátor** na levé straně editoru dotazů poklikejte na název kontejneru úložiště objektů BLOB přidružený k vašemu clusteru. Ve výchozím nastavení má název kontejneru stejný název jako název clusteru.

1. Vyhledejte **HiveSampleData.txt** ve sloupci **název** (cesta ke složce je **.. /Hive/Warehouse/hivesampletable/**) a potom vyberte **binární soubor** vlevo od HiveSampleData.txt. HiveSampleData.txt se dodává se všemi clustery. Volitelně můžete použít vlastní soubor.

    ![Data importování Power Query v HDI Excelu](./media/apache-hadoop-connect-excel-power-query/powerquery-importdata.png)

1. Pokud chcete, můžete názvy sloupců přejmenovat. Až budete připraveni, vyberte **zavřít & načíst**.  Data se načetla do vašeho sešitu:

    ![Importovaná tabulka Power Query v HDI Excelu](./media/apache-hadoop-connect-excel-power-query/powerquery-importedtable.png)

## <a name="next-steps"></a>Další kroky

V tomto článku jste zjistili, jak pomocí Power Query načíst data z HDInsight do Excelu. Podobně můžete data z HDInsight načíst do Azure SQL Database. Je také možné nahrávat data do HDInsight. Další informace najdete v těchto článcích:

* [Vizualizujte Apache Hive data pomocí Microsoft Power BI ve službě Azure HDInsight](apache-hadoop-connect-hive-power-bi.md).
* [Vizualizovat data podregistru interaktivního dotazu pomocí Power BI ve službě Azure HDInsight](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md).
* [Použijte Apache Zeppelin ke spouštění dotazů Apache Hive ve službě Azure HDInsight](../interactive-query/hdinsight-connect-hive-zeppelin.md).
* [Připojte Excel k HDInsight pomocí Microsoft Hive ODBC Driver](apache-hadoop-connect-excel-hive-odbc-driver.md).
* [Připojte se ke službě Azure HDInsight a spouštějte Apache Hive dotazy pomocí nástrojů Data Lake pro Visual Studio](apache-hadoop-visual-studio-tools-get-started.md).
* [Použijte nástroj Azure HDInsight pro Visual Studio Code](../hdinsight-for-vscode.md).
* [Nahrajte data do HDInsight](./../hdinsight-upload-data.md).
