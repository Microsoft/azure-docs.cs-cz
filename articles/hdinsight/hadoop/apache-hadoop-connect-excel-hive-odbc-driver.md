---
title: Aplikace Excel & Apache Hadoop s ovladačem rozhraní ODBC (Open Database Connectivity) – Azure HDInsight
description: Naučte se, jak nastavit a použít ovladač ODBC pro podregistr Microsoft pro Excel k dotazování na data v clusterech HDInsight z Microsoft Excelu.
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,hdiseo17may2017,seoapr2020
ms.date: 04/22/2020
ms.openlocfilehash: c7125e25c9716a9934c97f9f8f85666d264d5f89
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "104866943"
---
# <a name="connect-excel-to-apache-hadoop-in-azure-hdinsight-with-the-microsoft-hive-odbc-driver"></a>Připojení aplikace Excel k Apache Hadoopu ve službě Azure HDInsight pomocí konektoru Microsoft Hive ODBC Driver

[!INCLUDE [ODBC-JDBC-selector](../../../includes/hdinsight-selector-odbc-jdbc.md)]

Řešení pro velké objemy dat od Microsoftu integruje komponenty Microsoft Business Intelligence (BI) s Apache Hadoop clustery nasazenými v HDInsight. Příkladem je možnost připojení aplikace Excel k datovému skladu podregistru clusteru Hadoop. Připojte se pomocí ovladače ODBC (Open Database Connectivity) pro podregistr společnosti Microsoft.

Data přidružená k clusteru HDInsight můžete připojit z Excelu k Microsoft Power Query doplňku pro Excel. Další informace najdete v tématu [připojení Excelu k HDInsight pomocí Power Query](./apache-hadoop-connect-excel-power-query.md).

## <a name="prerequisites"></a>Požadavky

Než začnete tento článek, musíte mít následující položky:

* An HDInsight clusteru Hadoop. Pokud ho chcete vytvořit, přečtěte si téma Začínáme [se službou Azure HDInsight](apache-hadoop-linux-tutorial-get-started.md).
* Pracovní stanice s Office 2010 Professional Plus nebo novější nebo Excel 2010 nebo novější.

## <a name="install-microsoft-hive-odbc-driver"></a>Nainstalovat ovladač Microsoft podregistr ODBC Driver

Stáhněte a nainstalujte [Microsoft Hive ODBC Driver](https://www.microsoft.com/download/details.aspx?id=40886). Vyberte verzi, která odpovídá verzi aplikace, ve které budete používat ovladač ODBC.  Pro tento článek se používá ovladač pro Office Excel.

## <a name="create-apache-hive-odbc-data-source"></a>Vytvoření zdroje dat Apache Hive ODBC

Následující kroky ukazují, jak vytvořit zdroj dat pro podregistr ODBC.

1. V systému Windows přejděte na **začátek > nástroje pro správu systému windows > zdroje dat ODBC (32bitová verze)/(64 bitů)**.  Tato akce otevře okno **Správce zdrojů dat ODBC** .

   :::image type="content" source="./media/apache-hadoop-connect-excel-hive-odbc-driver/simbahiveodbc-datasourceadmin1.png" alt-text="Správce zdroje dat OBDC" border="true":::

1. Na kartě **uživatelský název DSN** vyberte **Přidat** a otevřete tak okno **vytvořit nový zdroj dat** .

1. Vyberte **Microsoft Hive ODBC Driver** a pak vyberte **Dokončit** a otevřete tak okno **nastavení Microsoft Hive ODBC Driver DSN** .

1. Zadejte nebo vyberte tyto hodnoty:

   | Vlastnost | Popis |
   | --- | --- |
   |  Název zdroje dat |Zadejte název zdroje dat. |
   |  Hostitelé: |Zadejte `HDInsightClusterName.azurehdinsight.net`. Například, `myHDICluster.azurehdinsight.net`. Poznámka: `HDInsightClusterName-int.azurehdinsight.net` podporuje se, pokud je klientský virtuální počítač v partnerském vztahu ke stejné virtuální síti. |
   |  Port |Použijte **443**. (Tento port se změnil z 563 na 443.) |
   |  databáze |Použijte **výchozí nastavení**. |
   |  Mechanismus |Výběr **služby Windows Azure HDInsight** |
   |  Uživatelské jméno |Zadejte uživatelské jméno uživatele clusteru HDInsight HTTP. Výchozí uživatelské jméno **admin**. |
   |  Heslo |Zadejte uživatelské heslo pro cluster HDInsight. Zaškrtněte políčko **Uložit heslo (šifrované)**.|

1. Volitelné: vyberte **Rozšířené možnosti...**  

   | Parametr | Popis |
   | --- | --- |
   |  Použít nativní dotaz |Když je vybraná, ovladač ODBC se nepokusí převést TSQL na HiveQL. Použijete ji jenom v případě, že jste 100%, abyste odeslali čistě příkazy HiveQL. Při připojování k SQL Server nebo Azure SQL Database byste ji měli nechat nezaškrtnutou. |
   |  Počet načtených řádků na blok |Při načítání velkého počtu záznamů může být nutné vyladit tento parametr, aby bylo zajištěno optimální výkon. |
   |  Výchozí délka sloupce řetězce, délka binárního sloupce, škála desetinných sloupců |Délky datových typů a jejich přesnost mohou ovlivnit způsob, jakým jsou vrácena data. Způsobují nesprávné informace, které mají být vráceny z důvodu ztráty přesnosti a nebo zkrácení. |

    :::image type="content" source="./media/apache-hadoop-connect-excel-hive-odbc-driver/hiveodbc-datasource-advancedoptions1.png" alt-text="Rozšířené možnosti konfigurace DSN" border="true":::

1. Vyberte **test** pro otestování zdroje dat. Pokud je zdroj dat správně nakonfigurovaný, výsledek testu se zobrazí **po úspěšném dokončení.**

1. Kliknutím na **tlačítko OK** zavřete okno testu.  

1. Výběrem **OK** zavřete okno **Microsoft Hive ODBC Driver nastavení DSN** .  

1. Kliknutím na **tlačítko OK** zavřete okno **Správce zdrojů dat ODBC** .  

## <a name="import-data-into-excel-from-hdinsight"></a>Import dat do Excelu ze služby HDInsight

Následující kroky popisují způsob, jak importovat data z tabulky podregistru do sešitu aplikace Excel pomocí zdroje dat ODBC, který jste vytvořili v předchozí části.

1. V Excelu otevřete nový nebo existující sešit.

2. Na kartě **data** přejděte k části **získání dat**  >  **z jiných zdrojů**  >  **z rozhraní ODBC** a spusťte **z okna rozhraní ODBC** .

   :::image type="content" source="./media/apache-hadoop-connect-excel-hive-odbc-driver/simbahiveodbc-excel-dataconnection1.png" alt-text="Průvodce otevřením datového připojení aplikace Excel" border="true":::

3. V rozevíracím seznamu vyberte název zdroje dat, který jste vytvořili v poslední části, a pak vyberte **OK**.

4. Pro první použití se otevře dialogové okno **ovladače ODBC** . V nabídce vlevo vyberte **Windows** . Pak vyberte **připojit** a otevřete okno **navigátor** .

5. Z **navigátoru** přejděte do **podregistru**  >  **Default**  >  **hivesampletable** a pak vyberte **načíst**. Před importováním dat do Excelu trvá chvíli chvilku.

   :::image type="content" source="./media/apache-hadoop-connect-excel-hive-odbc-driver/hdinsight-hive-odbc-navigator.png" alt-text="Navigátor aplikace ODBC pro rozhraní HDInsight v Excelu" border="true":::

## <a name="next-steps"></a>Další kroky

V tomto článku jste zjistili, jak pomocí ovladače Microsoft podregistr ODBC načíst data ze služby HDInsight do Excelu. Podobně můžete data ze služby HDInsight načíst do SQL Database. Data je také možné odeslat do služby HDInsight. Další informace najdete v následujících tématech:

* [Vizualizujte Apache Hive data pomocí Microsoft Power BI ve službě Azure HDInsight](apache-hadoop-connect-hive-power-bi.md).
* [Vizualizovat data podregistru interaktivního dotazu pomocí Power BI ve službě Azure HDInsight](../interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md).
* [Připojení Excelu k Apache Hadoop pomocí Power Query](apache-hadoop-connect-excel-power-query.md).
* [Připojte se ke službě Azure HDInsight a spouštějte Apache Hive dotazy pomocí nástrojů Data Lake pro Visual Studio](apache-hadoop-visual-studio-tools-get-started.md).