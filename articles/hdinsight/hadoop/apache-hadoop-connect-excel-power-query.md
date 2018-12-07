---
title: Připojení Excelu k systému Apache Hadoop pomocí Power Query – Azure HDInsight
description: Zjistěte, jak využít výhod komponenty business intelligence a přístup k datům uloženým v Hadoop v HDInsight pomocí Power Query pro Excel.
services: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/16/2018
ms.openlocfilehash: d852919b762dd7350b74372acbbbcaa31f0a8f90
ms.sourcegitcommit: 698ba3e88adc357b8bd6178a7b2b1121cb8da797
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/07/2018
ms.locfileid: "53016670"
---
# <a name="connect-excel-to-apache-hadoop-by-using-power-query"></a>Připojení Excelu k systému Apache Hadoop pomocí doplňku Power Query
Jednu klíčovou funkcí řešení pro velké objemy dat společnosti Microsoft je integrační komponenty Microsoft business intelligence (BI) s clustery Apache Hadoop v Azure HDInsight. Primární příkladem je schopnost připojit Excel k účtu služby Azure Storage, který obsahuje data spojená s vaším clusterem Hadoop pomocí Microsoft Power Query pro Excel add-in. Tento článek vás provede jak nastavit a provádět dotazy na data související s clusterem Hadoop spravované pomocí HDInsight pomocí Power Query.

### <a name="prerequisites"></a>Požadavky
Před zahájením tohoto článku, musíte mít následující položky:

* **HDInsight cluster**. Nakonfigurujte jeden, najdete v tématu [Začínáme s Azure HDInsight] [hdinsight-get-started].
* **Pracovní stanice** , na kterém je spuštěn Windows 7, Windows Server 2008 R2 nebo novějším operačním systémem.
* **Office 2016, Office 2013 Professional Plus, Office 365 ProPlus, Excel 2013 Standalone nebo Office 2010 Professional Plus**.

## <a name="install-power-query"></a>Instalace doplňku Power Query
Power Query můžete importovat data, která má výstup, který byl vytvořen pomocí Hadoop úloha spuštěná na clusteru HDInsight.

V Excelu 2016 doplňku Power Query integroval do dat pásu karet v části načíst a transformovat. Starší verze Excelu, stáhněte si Microsoft Power Query pro Excel z [Microsoft Download Center] [ powerquery-download] a nainstalujte ho.

## <a name="import-hdinsight-data-into-excel"></a>Importovat HDInsight data do Excelu
Doplněk Power Query pro Excel umožňuje snadno importovat data z vašeho clusteru HDInsight do Excelu, kde nástroje BI, jako je doplňku PowerPivot a doplňkem Power Map lze použít ke kontrole, analyzovat a prezentovat data.

**Importovat data z clusteru služby HDInsight**

1. Otevřete aplikaci Excel.
2. Vytvořte nový prázdný sešit.
3. Proveďte následující kroky na základě verze Excelu:

    - Excel 2016

        - Klikněte na tlačítko **Data** nabídky, klikněte na tlačítko **získat Data** z **načíst a transformovat Data** pásu karet, klikněte na tlačítko **z Azure**a potom klikněte na tlačítko **Z Azure HDInsight(HDFS)**.

        ![HDI.PowerQuery.SelectHdiSource](./media/apache-hadoop-connect-excel-power-query/hdi.powerquery.selecthdisource.excel2016.png)

    - Excel 2013/2010

        - Klikněte na tlačítko **Power Query** nabídky, klikněte na tlačítko **z Azure**a potom klikněte na tlačítko **z Microsoft Azure HDInsight**.
   
        ![HDI.PowerQuery.SelectHdiSource][image-hdi-powerquery-hdi-source]
       
        **Poznámka:** Pokud nevidíte **Power Query** nabídky, přejděte na **souboru** > **možnosti** > **Add-Ins**a vyberte **doplňky modelu COM** z rozevíracího seznamu **spravovat** pole v dolní části stránky. Vyberte **přejít...**  tlačítko a ověřte, že je zaškrtnuto políčko pro Power Query pro Excel add-in.
       
        **Poznámka:** Power Query také umožňuje importovat data z HDFS kliknutím **z jiných zdrojů**.
4. Pro **název účtu**, zadejte název účtu služby Azure Blob storage spojené s vaším clusterem a potom klikněte na tlačítko **OK**. Tento účet může být [výchozí účet úložiště](../hdinsight-administer-use-management-portal.md#find-the-default-storage-account) nebo propojený účet úložiště.  Formát je *https://&lt;StorageAccountName >.blob.core.windows.net/*.
5. Pro **klíč účtu**, zadejte klíč účtu úložiště objektů Blob a pak klikněte na tlačítko **Uložit**. (Budete muset zadat čas informace pouze první účet přístupu k tomuto úložišti.)
6. V **Navigátor** podokna na levé straně editoru dotazů, dvakrát klikněte na název kontejneru úložiště objektů Blob. Ve výchozím nastavení je název kontejneru stejný název jako název clusteru.
7. Vyhledejte **HiveSampleData.txt** v **název** sloupec (cesta ke složce je **... / hive/warehouse/hivesampletable/**) a potom klikněte na tlačítko **binární** na levé straně HiveSampleData.txt. HiveSampleData.txt obsahuje všechny clusteru. Volitelně můžete použít vlastní soubor.
   
    ![HDI. PowerQuery.ImportData][image-hdi-powerquery-importdata]
8. Pokud chcete, můžete přejmenovat názvy sloupců. Až budete připravení, klikněte na tlačítko **zavřít a načíst**.  Data se načetl do sešitu:
   
    ![HDI. PowerQuery.ImportedTable][image-hdi-powerquery-imported-table]

## <a name="next-steps"></a>Další postup
V tomto článku jste zjistili, jak pomocí Power Query k načtení dat z HDInsight do Excelu. Podobně může načítat data z HDInsight do Azure SQL Database. Je také možné nahrát data do HDInsight. Další informace naleznete v následujících článcích:

* [Vizualizace dat Hive pomocí Microsoft Power BI v Azure HDInsight](apache-hadoop-connect-hive-power-bi.md).
* [Vizualizace dat Interactive Query Hive pomocí Power BI v Azure HDInsight](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md).
* [Použití Zeppelinu ke spouštění dotazů Hive v HDInsight Azure ](./../hdinsight-connect-hive-zeppelin.md).
* [Připojení Excelu k HDInsight pomocí ovladače ODBC Microsoft Hivu](apache-hadoop-connect-excel-hive-odbc-driver.md).
* [Připojení k Azure HDInsight a spouštění dotazů Hive pomocí nástrojů Data Lake pro Visual Studio](apache-hadoop-visual-studio-tools-get-started.md).
* [Pomocí nástroje Azure HDInsight pro Visual Studio Code](../hdinsight-for-vscode.md).
* [Nahrání dat do HDInsight](./../hdinsight-upload-data.md).

[image-hdi-powerquery-hdi-source]: ./media/apache-hadoop-connect-excel-power-query/hdi.powerquery.selecthdisource.png
[image-hdi-powerquery-importdata]: ./media/apache-hadoop-connect-excel-power-query/hdi.powerquery.importdata.png
[image-hdi-powerquery-imported-table]: ./media/apache-hadoop-connect-excel-power-query/hdi.powerquery.importedtable.PNG

[powerquery-download]: https://go.microsoft.com/fwlink/?LinkID=286689
