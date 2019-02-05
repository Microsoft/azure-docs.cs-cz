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
ms.date: 01/29/2019
ms.author: hrasheed
ms.openlocfilehash: 0b4e2d3e7c75e6d25c7d519d4179a635a7833b6b
ms.sourcegitcommit: a65b424bdfa019a42f36f1ce7eee9844e493f293
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/04/2019
ms.locfileid: "55695317"
---
# <a name="connect-excel-to-apache-hadoop-in-azure-hdinsight-with-the-microsoft-hive-odbc-driver"></a>Připojení Excelu k systému Apache Hadoop v Azure HDInsight pomocí ovladače Microsoft Hive ODBC

[!INCLUDE [ODBC-JDBC-selector](../../../includes/hdinsight-selector-odbc-jdbc.md)]

Řešení Microsoftu pro velké objemy dat se integruje s clustery systému Apache Hadoop, které jsou nasazené v Azure HDInsight komponentami Microsoft Business Intelligence (BI). Příklad této integrace je schopnost připojení Excelu k datovému skladu Hive clusteru Hadoop v HDInsight pomocí ovladače Microsoft Hive Open Database Connectivity (ODBC).

Je také možné připojit data související s clusterem HDInsight a zdrojů dat, včetně jiných clusterů Hadoop (mimo HDInsight) z aplikace Excel pomocí doplňku Microsoft Power Query pro Excel. Informace o instalaci a pomocí Power Query, naleznete v tématu [připojení Excelu k HDInsight pomocí Power Query][hdinsight-power-query].


## <a name="prerequisites"></a>Požadavky

Před zahájením tohoto článku, musíte mít následující položky:

* Cluster HDInsight Hadoop. Pokud chcete jeden vytvořit, přečtěte si téma [Začínáme s Azure HDInsight](apache-hadoop-linux-tutorial-get-started.md).
* Pracovní stanice s Office 2010 Professional Plus nebo novější, nebo aplikaci Excel 2010 nebo novější.

## <a name="install-microsoft-hive-odbc-driver"></a>Instalace ovladače Microsoft Hive ODBC
Stáhněte a nainstalujte [ovladače Microsoft Hive ODBC Driver] [ hive-odbc-driver-download] verzi, která odpovídá verzi aplikace, kde bude pomocí ovladače ODBC.  V tomto kurzu se používá ovladače pro Office Excel.

## <a name="create-apache-hive-odbc-data-source"></a>Vytvoření zdroje dat Apache Hive ODBC
Následující kroky ukazují, jak vytvořit zdroje dat Hive ODBC.

1. Z Windows, přejděte na začátek > Nástroje pro správu Windows > zdroje dat ODBC (32-bit)/(64-bit).  Tím se otevře **správce zdrojů dat ODBC** okna.
   
    ![Správce zdrojů dat ODBC](./media/apache-hadoop-connect-excel-hive-odbc-driver/HDI.SimbaHiveOdbc.DataSourceAdmin1.png "nakonfigurovat pomocí Správce zdrojů dat ODBC DSN")

2. Z **uživatelské DSN** kartu, vyberte možnost **přidat** otevřít **vytvořit nový zdroj dat** okna.

3. Vyberte **ovladače Microsoft Hive ODBC Driver**a pak vyberte **Dokončit** otevřít **Microsoft Hive ODBC DSN instalace ovladačů** okna.

4. Zadejte nebo vyberte tyto hodnoty:
   
   | Vlastnost | Popis |
   | --- | --- |
   |  Název zdroje dat |Zadejte název zdroje dat. |
   |  Hostitele |Zadejte &lt;Název_clusteru_hdinsight&gt;. azurehdinsight.net. Například mujHDICluster.azurehdinsight.net. |
   |  Port |Použijte <strong>443</strong>. (Tento port se změnil z 563 na 443.) |
   |  Databáze |Použití <strong>výchozí</strong>. |
   |  Mechanismus |Vyberte <strong>Služba Azure HDInsight</strong> |
   |  Uživatelské jméno |Zadejte uživatelské jméno uživatele, HTTP clusteru HDInsight. Výchozí uživatelské jméno <strong>admin</strong>. |
   |  Heslo |Zadejte heslo uživatele clusteru HDInsight. |

   
5. Volitelné: Vyberte **rozšířené možnosti...**  
   
   | Parametr | Popis |
   | --- | --- |
   |  Použít nativní dotaz |Pokud je vybrána, ovladač ODBC se nepokusí se převést TSQL HiveQL. Se použije pouze v případě, že jsou 100 % jistotu, že jste odeslali čistě příkazy HiveQL. Při připojování k serveru SQL Server nebo databázi SQL Azure, měli nechat nezaškrtnuté. |
   |  Řádků načtených na blok |Při načítání velké množství záznamů, optimalizace pro tento parametr může být nutné zajistit optimální přínos. |
   |  Výchozí délka sloupce řetězec, délka sloupce binární, Decimal sloupce stupnice |Datový typ délky a přesnosti může ovlivnit, jak se data vrací. Mohou způsobit nesprávné informace, který se má vrátit z důvodu ztráty přesnosti a/nebo zkrácení. |

    ![Pokročilá nastavení](./media/apache-hadoop-connect-excel-hive-odbc-driver/HDI.HiveOdbc.DataSource.AdvancedOptions1.png "DSN rozšířené možnosti konfigurace")

5. Vyberte **testování** pro zdroj dat otestovat. Pokud zdroj dat správně nakonfigurovaný, výsledek testu zobrazí **úspěch!**.  

6. Vyberte **OK** zavřete okno Test.  

7. Vyberte **OK** zavřete **Microsoft Hive ODBC DSN instalace ovladačů** okna.  

8. Vyberte **OK** zavřete **správce zdrojů dat ODBC** okna.  

## <a name="import-data-into-excel-from-hdinsight"></a>Import dat do Excelu ze služby HDInsight
Následující kroky popisují způsob, jak importovat data z tabulky Hive do sešitu aplikace Excel pomocí zdroje dat ODBC, který jste vytvořili v předchozí části.

1. V Excelu otevřete nový nebo existující sešit.

2. Z **Data** kartu, přejděte na **získat Data** > **z jiných zdrojů** > **z rozhraní ODBC** spusťte **z rozhraní ODBC** okna.
   
    ![Otevřete Průvodce připojením dat](./media/apache-hadoop-connect-excel-hive-odbc-driver/HDI.SimbaHiveOdbc.Excel.DataConnection1.png "otevřete Průvodce připojením dat")

3. Z rozevíracího seznamu vyberte název zdroje dat, který jste vytvořili v předchozí části a pak vyberte **OK**.

4. Zadejte uživatelské jméno Hadoop (výchozí název je správce) a heslo a pak vyberte **připojit** otevřít **Navigátor** okna.

5. Z **Navigátor**, přejděte na **HIVE** > **výchozí** > **hivesampletable**a pak vyberte  **Zatížení**. Trvá několik minut, než import dat do Excelu.

    ![Navigátor HDInsight Hive ODBC](./media/apache-hadoop-connect-excel-hive-odbc-driver/hdinsight.hive.odbc.navigator.png "otevřete Průvodce připojením dat")

## <a name="next-steps"></a>Další postup
V tomto článku jste zjistili, jak načíst data ze služby HDInsight do Excelu pomocí ovladače Microsoft Hive ODBC. Podobně může načítat data ze služby HDInsight do služby SQL Database. Je také možné nahrát data do služby HDInsight. Další informace naleznete v tématu:

* [Vizualizace dat pomocí Microsoft Power BI v Azure HDInsight s Apache Hive](apache-hadoop-connect-hive-power-bi.md).
* [Vizualizace dat Interactive Query Hive pomocí Power BI v Azure HDInsight](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md).
* [Použití Apache Zeppelinu ke spuštění dotazy Apache Hive v HDInsight Azure ](./../hdinsight-connect-hive-zeppelin.md).
* [Připojení Excelu k systému Apache Hadoop pomocí doplňku Power Query](apache-hadoop-connect-excel-power-query.md).
* [Připojení k Azure HDInsight a spouštět dotazy Apache Hive pomocí nástrojů Data Lake pro Visual Studio](apache-hadoop-visual-studio-tools-get-started.md).
* [Pomocí nástroje Azure HDInsight pro Visual Studio Code](../hdinsight-for-vscode.md).
* [Nahrání dat do HDInsight](./../hdinsight-upload-data.md).

[hdinsight-use-sqoop]:hdinsight-use-sqoop.md
[hdinsight-analyze-flight-data]: hdinsight-analyze-flight-delay-data.md
[hdinsight-use-hive]:hdinsight-use-hive.md
[hdinsight-upload-data]: ../hdinsight-upload-data.md
[hdinsight-power-query]: ../hdinsight-connect-excel-power-query.md
[hive-odbc-driver-download]: https://go.microsoft.com/fwlink/?LinkID=286698


