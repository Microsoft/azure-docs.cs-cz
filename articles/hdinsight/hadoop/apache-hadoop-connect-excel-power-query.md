---
title: Připojení aplikace Excel k systému Hadoop pomocí Power Query - Azure HDInsight | Microsoft Docs
description: Zjistěte, jak využít výhod komponenty business intelligence a použijte Power Query pro Excel přístup k datům uloženým v Hadoop v HDInsight.
services: hdinsight
documentationcenter: ''
tags: azure-portal
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: 01ad2f90-7520-44d9-8c16-4d936faaff9b
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 05/16/2018
ms.author: jgao
ms.openlocfilehash: be2b6e27e097ea5206e1faceed75ec212406d330
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
ms.locfileid: "34202124"
---
# <a name="connect-excel-to-hadoop-by-using-power-query"></a>Připojení aplikace Excel k systému Hadoop pomocí Power Query
Klíčovou vlastností řešení velkých objemů dat společnosti Microsoft je integrace komponenty Microsoft business intelligence (BI) s clustery systému Hadoop v prostředí Azure HDInsight. Primární příkladem je možnost připojit Excel k účtu úložiště Azure, který obsahuje data přidruženého k vašemu clusteru Hadoop pomocí Microsoft Power Query pro doplněk aplikace Excel. Tento článek vás provede procesem nastavení a použití doplňku Power Query k dotazování na data související s clusterem Hadoop spravované s HDInsight.

### <a name="prerequisites"></a>Požadavky
Před zahájením tohoto článku, musíte mít následující položky:

* **Cluster služby HDInsight**. Nakonfigurujte jeden, najdete v tématu [Začínáme s Azure HDInsight] [hdinsight-get-started].
* **Pracovní stanice** se systémem Windows 7, Windows Server 2008 R2 nebo novějším operačním systémem.
* **Office 2016, Office Professional 2013 Plus, Office 365 ProPlus, samostatné aplikace Excel 2013 nebo Office 2010 Professional Plus**.

## <a name="install-power-query"></a>Instalace doplňku Power Query
Power Query můžete importovat data, která má výstup, nebo které byla vygenerována v rámci Hadoop úlohy spuštěné v clusteru služby HDInsight.

V aplikaci Excel 2016 Power Query integrovány do dat pásu karet v části Get & transformace. Starší verze Excelu, stáhněte si Microsoft Power Query pro Excel z [Microsoft Download Center] [ powerquery-download] a nainstalujte ji.

## <a name="import-hdinsight-data-into-excel"></a>Importovat HDInsight data do aplikace Excel
Doplněk Power Query pro Excel lze snadno importovat data z clusteru HDInsight do Excelu, kde BI nástroje, jako je doplňku PowerPivot a mapa Power slouží ke kontrole, analyzovat a data k dispozici.

**Při importu dat z clusteru služby HDInsight**

1. Otevřete aplikaci Excel.
2. Vytvoření nového prázdného sešitu.
3. Proveďte následující kroky založeny na verzi aplikace Excel:

    - Excel 2016

        - Klikněte na tlačítko **Data** nabídky, klikněte na tlačítko **načíst Data** z **Get & transformace dat** pásu karet, klikněte na tlačítko **z Azure**a potom klikněte na **z Azure HDInsight(HDFS)**.

        ![HDI.PowerQuery.SelectHdiSource](./media/apache-hadoop-connect-excel-power-query/hdi.powerquery.selecthdisource.excel2016.png)

    - Excel 2013/2010

        - Klikněte na tlačítko **Power Query** nabídky, klikněte na tlačítko **z Azure**a potom klikněte na **z Microsoft Azure HDInsight**.
   
        ![HDI.PowerQuery.SelectHdiSource][image-hdi-powerquery-hdi-source]
       
        **Poznámka:** Pokud nevidíte **Power Query** nabídky, přejděte na **soubor** > **možnosti** > **doplňky**a vyberte **COM Doplňky** z rozevíracího seznamu **spravovat** pole v dolní části stránky. Vyberte **přejděte...**  tlačítko a ověřte, že bylo zaškrtnuté políčko pro Power Query pro doplněk aplikace Excel.
       
        **Poznámka:** Power Query taky umožňuje importovat data z HDFS kliknutím **z jiných zdrojů**.
4. Pro **název účtu**, zadejte název účtu úložiště objektů Blob Azure přidruženého k vašemu clusteru a pak klikněte na tlačítko **OK**. Tento účet může být [výchozí účet úložiště](../hdinsight-administer-use-management-portal.md#find-the-default-storage-account) nebo propojený účet úložiště.  Formát je *https://&lt;StorageAccountName >.blob.core.windows.net/*.
5. Pro **klíč účtu**, zadejte klíč pro účet úložiště Blob a pak klikněte na tlačítko **Uložit**. (Potřebujete zadejte čas informace pouze první účet přístupu k tomuto úložišti.)
6. V **Navigátor** podokna na levé straně editoru dotazů, dvakrát klikněte na název kontejneru úložiště objektů Blob. Ve výchozím nastavení je název kontejneru stejný název jako název clusteru.
7. Vyhledejte **HiveSampleData.txt** v **název** sloupce (je cesta ke složce **... / hive/skladu/hivesampletable/**) a potom klikněte na **binární** na levé straně HiveSampleData.txt. HiveSampleData.txt obsahuje všechny clusteru. Volitelně můžete vytvořit svůj vlastní soubor.
   
    ![HDI. PowerQuery.ImportData][image-hdi-powerquery-importdata]
8. Pokud chcete, můžete přejmenovat názvy sloupců. Až budete připravení, klikněte na tlačítko **zavřete & načíst**.  Data se načetl do vašeho sešitu:
   
    ![HDI. PowerQuery.ImportedTable][image-hdi-powerquery-imported-table]

## <a name="next-steps"></a>Další postup
V tomto článku jste zjistili, jak k získání dat z prostředí HDInsight do aplikace Excel pomocí doplňku Power Query. Podobně můžete data načíst z prostředí HDInsight do Azure SQL Database. Je také možné nahrát data do HDInsight. Další informace naleznete v následujících článcích:

* [Vizualizovat data Hive s Microsoft Power BI v Azure HDInsight](apache-hadoop-connect-hive-power-bi.md).
* [Vizualizace interaktivní dotazu Hive pomocí Power BI v Azure HDInsight](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md).
* [Použití Zeppelin ke spouštění dotazů Hive v Azure HDInsight ](./../hdinsight-connect-hive-zeppelin.md).
* [Připojení aplikace Excel do HDInsight pomocí ovladače ODBC Microsoft Hive](apache-hadoop-connect-excel-hive-odbc-driver.md).
* [Připojení k Azure HDInsight a spouštět dotazy Hive pomocí nástrojů Data Lake pro Visual Studio](apache-hadoop-visual-studio-tools-get-started.md).
* [Pomocí nástroje Azure HDInsight pro Visual Studio Code](../hdinsight-for-vscode.md).
* [Nahrání dat do HDInsight](./../hdinsight-upload-data.md).

[image-hdi-powerquery-hdi-source]: ./media/apache-hadoop-connect-excel-power-query/hdi.powerquery.selecthdisource.png
[image-hdi-powerquery-importdata]: ./media/apache-hadoop-connect-excel-power-query/hdi.powerquery.importdata.png
[image-hdi-powerquery-imported-table]: ./media/apache-hadoop-connect-excel-power-query/hdi.powerquery.importedtable.PNG

[powerquery-download]: http://go.microsoft.com/fwlink/?LinkID=286689
