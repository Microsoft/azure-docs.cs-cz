---
title: Připojení Excelu k systému Apache Hadoop pomocí ovladače ODBC Hive – Azure HDInsight
description: Zjistěte, jak nastavit a používat pro aplikaci Excel ovladače Microsoft Hive ODBC k dotazování dat v clusterech HDInsight z Microsoft Excelu.
keywords: excel hadoop, hive aplikace excel, hive odbc
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 05/16/2018
ms.author: hrasheed
ms.openlocfilehash: ace12e9bd5cb1b9f6e38f57ca42faa6c6870af87
ms.sourcegitcommit: efcd039e5e3de3149c9de7296c57566e0f88b106
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/10/2018
ms.locfileid: "53162541"
---
# <a name="connect-excel-to-apache-hadoop-in-azure-hdinsight-with-the-microsoft-hive-odbc-driver"></a>Připojení Excelu k systému Apache Hadoop v Azure HDInsight pomocí ovladače Microsoft Hive ODBC

[!INCLUDE [ODBC-JDBC-selector](../../../includes/hdinsight-selector-odbc-jdbc.md)]

Řešení Microsoftu pro velké objemy dat se integruje s clustery systému Apache Hadoop, které jsou nasazené Azure HDInsight komponentami Microsoft Business Intelligence (BI). Příklad této integrace je schopnost připojení Excelu k datovému skladu Hive clusteru Hadoop v HDInsight pomocí ovladače Microsoft Hive Open Database Connectivity (ODBC).

Je také možné připojit data související s clusterem HDInsight a zdrojů dat, včetně jiných clusterů Hadoop (mimo HDInsight) z aplikace Excel pomocí doplňku Microsoft Power Query pro Excel. Informace o instalaci a pomocí Power Query, naleznete v tématu [připojení Excelu k HDInsight pomocí Power Query][hdinsight-power-query].



**Požadavky**:

Před zahájením tohoto článku, musíte mít následující položky:

* **HDInsight cluster**. Pokud chcete jeden vytvořit, přečtěte si téma [Začínáme s Azure HDInsight](apache-hadoop-linux-tutorial-get-started.md).
* **Pracovní stanice** s Office 2013 Professional Plus, Office 365 Pro Plus, Excel 2013 Standalone nebo Office 2010 Professional Plus.

## <a name="install-microsoft-hive-odbc-driver"></a>Instalace ovladače Microsoft Hive ODBC
Stáhnout a nainstalovat ovladače Microsoft Hive ODBC Driver z [Download Center][hive-odbc-driver-download].

Tento ovladač je nainstalovat na 32bitové nebo 64bitové verze Windows 7, Windows 8, Windows 10, Windows Server 2008 R2 a Windows Server 2012. Ovladač umožňuje připojení k Azure HDInsight. Nainstalujete verzi, která odpovídá verzi aplikace, kde používáte ovladač ODBC. V tomto kurzu se používá ovladač z aplikace Office Excel.

## <a name="create-apache-hive-odbc-data-source"></a>Vytvoření zdroje dat Apache Hive ODBC
Následující kroky ukazují, jak vytvořit zdroje dat Hive ODBC.

1. Z Windows 8 nebo Windows 10, stiskněte klávesu Windows a otevřete tak obrazovku Start a potom zadejte **zdroje dat**.
2. Klikněte na tlačítko **nastavení zdroje dat ODBC (32bitová verze)** nebo **nastavení zdroje dat ODBC (64-bit)** v závislosti na vaší verzi Office. Pokud používáte Windows 7, zvolte **zdroje dat ODBC (32 bitů)** nebo **zdroje dat ODBC (64bitová verze)** z **nástroje pro správu**. Měly by se zobrazit **správce zdrojů dat ODBC** dialogového okna.
   
    ![Správce zdrojů dat ODBC](./media/apache-hadoop-connect-excel-hive-odbc-driver/HDI.SimbaHiveOdbc.DataSourceAdmin1.png "nakonfigurovat pomocí Správce zdrojů dat ODBC DSN")

3. Uživatel DNS, klikněte na **přidat** otevřít **vytvořit nový zdroj dat** průvodce.
4. Vyberte **ovladače Microsoft Hive ODBC Driver**a potom klikněte na tlačítko **Dokončit**. Měly by se zobrazit **Microsoft Hive ODBC Driver nastavením DNS** dialogového okna.
5. Zadejte nebo vyberte tyto hodnoty:
   
   | Vlastnost | Popis |
   | --- | --- |
   |  Název zdroje dat |Zadejte název zdroje dat. |
   |  Hostitel |Zadejte &lt;název_clusteru_HDInsight>.azurehdinsight.net. Například mujHDICluster.azurehdinsight.net. |
   |  Port |Použijte <strong>443</strong>. (Tento port se změnil z 563 na 443.) |
   |  Databáze |Použijte <strong>Výchozí</strong>. |
   |  Mechanismus |Vyberte <strong>Služba Azure HDInsight</strong> |
   |  Uživatelské jméno |Zadejte uživatelské jméno uživatele, HTTP clusteru HDInsight. Výchozí uživatelské jméno <strong>admin</strong>. |
   |  Heslo |Zadejte heslo uživatele clusteru HDInsight. |
   
    </table>
   
    Existují některé důležité parametry budou vědět, když kliknete **rozšířené možnosti**:
   
   | Parametr | Popis |
   | --- | --- |
   |  Použít nativní dotaz |Pokud je vybrána, ovladač ODBC se nepokusí se převést TSQL HiveQL. Se použije pouze v případě, že jsou 100 % jistotu, že jste odeslali čistě příkazy HiveQL. Při připojování k serveru SQL Server nebo databázi SQL Azure, měli nechat nezaškrtnuté. |
   |  Řádků načtených na blok |Při načítání velké množství záznamů, optimalizace pro tento parametr může být nutné zajistit optimální přínos. |
   |  Výchozí délka sloupce řetězec, délka sloupce binární, Decimal sloupce stupnice |Datový typ délky a přesnosti může ovlivnit, jak se data vrací. Mohou způsobit nesprávné informace, který se má vrátit z důvodu ztráty přesnosti a/nebo zkrácení. |

    ![Pokročilá nastavení](./media/apache-hadoop-connect-excel-hive-odbc-driver/HDI.HiveOdbc.DataSource.AdvancedOptions1.png "DSN rozšířené možnosti konfigurace")

1. Klikněte na tlačítko **testování** pro zdroj dat otestovat. Zdroj dat správně nakonfigurovaný, ukazuje *testů byla úspěšně DOKONČENA.!*.
2. Klikněte na tlačítko **OK** testovací dialogové okno zavřete. Nový zdroj dat musí být uvedena na **správce zdrojů dat ODBC**.
3. Klikněte na tlačítko **OK** ukončíte průvodce.

## <a name="import-data-into-excel-from-hdinsight"></a>Import dat do Excelu ze služby HDInsight
Následující kroky popisují způsob, jak importovat data z tabulky Hive do sešitu aplikace Excel pomocí zdroje dat ODBC, který jste vytvořili v předchozí části.

1. V Excelu otevřete nový nebo existující sešit.
2. Z **Data** klikněte na tlačítko **získat Data**, klikněte na tlačítko **z jiných zdrojů**a potom klikněte na tlačítko **z rozhraní ODBC** ke spuštění **dat Průvodce připojením**.
   
    ![Otevřete Průvodce připojením dat](./media/apache-hadoop-connect-excel-hive-odbc-driver/HDI.SimbaHiveOdbc.Excel.DataConnection1.png "otevřete Průvodce připojením dat")
4. Vyberte název zdroje dat, který jste vytvořili v předchozí části a potom klikněte na tlačítko **OK**.
5. Zadejte uživatelské jméno Hadoop (výchozí název je správce) a heslo a potom klikněte na tlačítko **připojit**.
6. V části Navigátor rozbalte **HIVE**, rozbalte **výchozí**, klikněte na tlačítko **hivesampletable**a potom klikněte na **zatížení**. Import dat do Excelu trvá několik sekund.

    ![Navigátor HDInsight Hive ODBC](./media/apache-hadoop-connect-excel-hive-odbc-driver/hdinsight.hive.odbc.navigator.png "otevřete Průvodce připojením dat")


## <a name="next-steps"></a>Další postup
V tomto článku jste zjistili, jak načíst data ze služby HDInsight do Excelu pomocí ovladače Microsoft Hive ODBC. Podobně může načítat data ze služby HDInsight do služby SQL Database. Je také možné nahrát data do služby HDInsight. Další informace naleznete v tématu:

* [Vizualizace dat Hive pomocí Microsoft Power BI v Azure HDInsight](apache-hadoop-connect-hive-power-bi.md).
* [Vizualizace dat Interactive Query Hive pomocí Power BI v Azure HDInsight](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md).
* [Použití Zeppelinu ke spouštění dotazů Hive v HDInsight Azure ](./../hdinsight-connect-hive-zeppelin.md).
* [Připojení Excelu k Hadoopu pomocí Power Query](apache-hadoop-connect-excel-power-query.md).
* [Připojení k Azure HDInsight a spouštění dotazů Hive pomocí nástrojů Data Lake pro Visual Studio](apache-hadoop-visual-studio-tools-get-started.md).
* [Pomocí nástroje Azure HDInsight pro Visual Studio Code](../hdinsight-for-vscode.md).
* [Nahrání dat do HDInsight](./../hdinsight-upload-data.md).

[hdinsight-use-sqoop]:hdinsight-use-sqoop.md
[hdinsight-analyze-flight-data]: hdinsight-analyze-flight-delay-data.md
[hdinsight-use-hive]:hdinsight-use-hive.md
[hdinsight-upload-data]: ../hdinsight-upload-data.md
[hdinsight-power-query]: ../hdinsight-connect-excel-power-query.md
[hive-odbc-driver-download]: https://go.microsoft.com/fwlink/?LinkID=286698


