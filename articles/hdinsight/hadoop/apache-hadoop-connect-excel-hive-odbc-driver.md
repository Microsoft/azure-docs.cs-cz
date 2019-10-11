---
title: Připojení Excelu k Apache Hadoop pomocí ovladače rozhraní ODBC pro podregistr – Azure HDInsight
description: Naučte se, jak nastavit a použít ovladač ODBC pro podregistr Microsoft pro Excel k dotazování na data v clusterech HDInsight z Microsoft Excelu.
keywords: Hadoop Excel, excelový podregistr, podregistr ODBC
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 10/08/2019
ms.openlocfilehash: 7451eaf56a2466bbb02fa879008b4a9787f6c2f5
ms.sourcegitcommit: b4665f444dcafccd74415fb6cc3d3b65746a1a31
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2019
ms.locfileid: "72264622"
---
# <a name="connect-excel-to-apache-hadoop-in-azure-hdinsight-with-the-microsoft-hive-odbc-driver"></a>Připojení Excelu k Apache Hadoop v Azure HDInsight pomocí ovladače Microsoft podregistr ODBC Driver

[!INCLUDE [ODBC-JDBC-selector](../../../includes/hdinsight-selector-odbc-jdbc.md)]

Řešení pro velké objemy dat od Microsoftu integruje komponenty Microsoft Business Intelligence (BI) s Apache Hadoop clustery nasazenými ve službě Azure HDInsight. Příkladem této integrace je možnost propojení Excelu s datovým skladem clusteru Hadoop v HDInsight pomocí ovladače Microsoft Open Database Connectivity (ODBC).

Také je možné připojit data přidružená k clusteru HDInsight a dalším zdrojům dat, včetně jiných clusterů Hadoop (mimo HDInsight), z Excelu pomocí doplňku Microsoft Power Query pro Excel. Informace o instalaci a použití Power Query najdete v tématu [připojení Excelu k HDInsight pomocí Power Query](../hdinsight-connect-excel-power-query.md).

## <a name="prerequisites"></a>Požadavky

Než začnete tento článek, musíte mít následující položky:

* An HDInsight clusteru Hadoop. Pokud ho chcete vytvořit, přečtěte si téma Začínáme [se službou Azure HDInsight](apache-hadoop-linux-tutorial-get-started.md).
* Pracovní stanice s Office 2010 Professional Plus nebo novější nebo Excel 2010 nebo novější.

## <a name="install-microsoft-hive-odbc-driver"></a>Nainstalovat ovladač Microsoft podregistr ODBC Driver

Stáhněte a nainstalujte [Microsoft Hive ODBC Driver](https://go.microsoft.com/fwlink/?LinkID=286698) verzi, která odpovídá verzi aplikace, ve které budete používat ovladač ODBC.  Pro tento článek se používá ovladač pro Office Excel.

## <a name="create-apache-hive-odbc-data-source"></a>Vytvoření zdroje dat Apache Hive ODBC

Následující kroky ukazují, jak vytvořit zdroj dat pro podregistr ODBC.

1. V systému Windows přejděte na začátek > Nástroje pro správu systému Windows > zdroje dat ODBC (32bitová verze)/(64 bitů).  Otevře se okno **Správce zdrojů dat ODBC** .

    ![Správce zdroje dat OBDC]–(./media/apache-hadoop-connect-excel-hive-odbc-driver/simbahiveodbc-datasourceadmin1.png "Konfigurace DSN pomocí Správce zdrojů dat ODBC")

1. Na kartě **uživatelský název DSN** vyberte **Přidat** a otevřete tak okno **vytvořit nový zdroj dat** .

1. Vyberte **Microsoft Hive ODBC Driver**a pak vyberte **Dokončit** a otevřete tak okno **nastavení Microsoft Hive ODBC Driver DSN** .

1. Zadejte nebo vyberte následující hodnoty:

   | Vlastnost | Popis |
   | --- | --- |
   |  Název zdroje dat |Zadejte název zdroje dat. |
   |  Hostitelé: |Zadejte `HDInsightClusterName.azurehdinsight.net`. Třeba `myHDICluster.azurehdinsight.net`. |
   |  Přístavní |Použijte **443**. (Tento port byl změněn z 563 na 443.) |
   |  Databáze |Použijte **výchozí nastavení**. |
   |  Mechanismy |Výběr **služby Windows Azure HDInsight** |
   |  Uživatelské jméno |Zadejte uživatelské jméno uživatele clusteru HDInsight HTTP. Výchozí uživatelské jméno je **admin**. |
   |  Heslo |Zadejte uživatelské heslo pro cluster HDInsight. Zaškrtněte políčko **Uložit heslo (šifrované)** .|

1. Volitelné: vyberte **Rozšířené možnosti...**  

   | Parametr | Popis |
   | --- | --- |
   |  Použít nativní dotaz |Když je vybraná, ovladač ODBC se nepokusí převést TSQL na HiveQL. Použijete ji jenom v případě, že jste 100%, abyste odeslali čistě příkazy HiveQL. Při připojování k SQL Server nebo Azure SQL Database byste ji měli nechat nezaškrtnutou. |
   |  Počet načtených řádků na blok |Při načítání velkého počtu záznamů může být nutné vyladit tento parametr, aby bylo zajištěno optimální výkon. |
   |  Výchozí délka sloupce řetězce, délka binárního sloupce, škála desetinných sloupců |Délky datových typů a jejich přesnost mohou ovlivnit způsob, jakým jsou vrácena data. Způsobují nesprávné informace, které mají být vráceny z důvodu ztráty přesnosti nebo zkrácení. |

    ![Rozšířené možnosti konfigurace DSN](./media/apache-hadoop-connect-excel-hive-odbc-driver/hiveodbc-datasource-advancedoptions1.png "Upřesnit možnosti konfigurace DSN")

1. Vyberte **test** pro otestování zdroje dat. Pokud je zdroj dat správně nakonfigurován, výsledek testu zobrazí hodnotu **úspěch!** .  

1. Kliknutím na **tlačítko OK** zavřete okno testu.  

1. Výběrem **OK** zavřete okno **Microsoft Hive ODBC Driver nastavení DSN** .  

1. Kliknutím na **tlačítko OK** zavřete okno **Správce zdrojů dat ODBC** .  

## <a name="import-data-into-excel-from-hdinsight"></a>Import dat do Excelu z HDInsight

Následující kroky popisují způsob, jak importovat data z tabulky podregistru do sešitu aplikace Excel pomocí zdroje dat ODBC, který jste vytvořili v předchozí části.

1. Otevřete v Excelu nový nebo existující sešit.

2. Na kartě **data** přejděte na příkaz **získat data** > **z jiných zdrojů** > **z rozhraní ODBC** a spusťte z okna **rozhraní ODBC** .

    Průvodce ![otevřením datového připojení aplikace Excel](./media/apache-hadoop-connect-excel-hive-odbc-driver/simbahiveodbc-excel-dataconnection1.png "otevřít Průvodce připojením dat aplikace Excel")

3. V rozevíracím seznamu vyberte název zdroje dat, který jste vytvořili v poslední části, a pak vyberte **OK**.

4. Pro první použití se otevře dialogové okno **ovladače ODBC** . V nabídce vlevo vyberte **Windows** . Pak vyberte **připojit** a otevřete okno **navigátor** .

5. Z **navigátoru**přejděte na **podregistr** > **výchozí** > **hivesampletable**a pak vyberte **načíst**. Před importováním dat do Excelu trvá chvíli chvilku.

    Rozhraní ![HDInsight Excel pro PODREGISTR ODBC navigátor]pro rozhraní ODBC navigátor(./media/apache-hadoop-connect-excel-hive-odbc-driver/hdinsight-hive-odbc-navigator.png "HDInsight")

## <a name="next-steps"></a>Další kroky

V tomto článku jste zjistili, jak pomocí ovladače Microsoft podregistr ODBC načíst data ze služby HDInsight do Excelu. Podobně můžete data ze služby HDInsight načíst do SQL Database. Data je také možné odeslat do služby HDInsight. Další informace naleznete v tématu:

* [Vizualizujte Apache Hive data pomocí Microsoft Power BI ve službě Azure HDInsight](apache-hadoop-connect-hive-power-bi.md).
* [Vizualizovat data podregistru interaktivního dotazu pomocí Power BI ve službě Azure HDInsight](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md).
* [Použijte Apache Zeppelin ke spouštění dotazů Apache Hive ve službě Azure HDInsight](../interactive-query/hdinsight-connect-hive-zeppelin.md).
* [Připojení Excelu k Apache Hadoop pomocí Power Query](apache-hadoop-connect-excel-power-query.md).
* [Připojte se ke službě Azure HDInsight a spouštějte Apache Hive dotazy pomocí nástrojů Data Lake pro Visual Studio](apache-hadoop-visual-studio-tools-get-started.md).
* [Použijte nástroj Azure HDInsight pro Visual Studio Code](../hdinsight-for-vscode.md).
* [Nahrajte data do HDInsight](./../hdinsight-upload-data.md).
