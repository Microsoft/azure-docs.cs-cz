---
title: Dotaz Apache Hive s ovladačem ODBC & PowerShell – Azure HDInsight
description: K dotazování Apache Hive clusterů v Azure HDInsight použijte Microsoft podregistr ODBC Driver a PowerShell.
keywords: podregistr, ODBC v podregistru, PowerShell
ms.service: hdinsight
ms.topic: tutorial
ms.date: 06/27/2019
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 34ee7eb25b1d106ea8bb53197d69ca5a9d528773
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "104871601"
---
# <a name="tutorial-query-apache-hive-with-odbc-and-powershell"></a>Kurz: dotazování Apache Hive pomocí rozhraní ODBC a prostředí PowerShell

Ovladače Microsoft ODBC poskytují flexibilní způsob, jak pracovat s různými druhy zdrojů dat, včetně Apache Hive. Můžete psát kód ve skriptovacích jazycích, jako je například prostředí PowerShell, které používá ovladače rozhraní ODBC k otevření připojení k vašemu clusteru podregistru, předat dotaz podle vašeho výběru a zobrazit výsledky.

V tomto kurzu provedete následující úlohy:

> [!div class="checklist"]
> * Stažení a instalace ovladače Microsoft podregistr ODBC Driver
> * Vytvoření zdroje dat Apache Hive ODBC propojeného s vaším clusterem
> * Dotazování ukázkových informací z vašeho clusteru pomocí prostředí PowerShell

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Před zahájením tohoto kurzu musíte mít tyto položky:

* Interaktivní cluster dotazů v HDInsight. Pokud ho chcete vytvořit, přečtěte si téma Začínáme [se službou Azure HDInsight](../hdinsight-hadoop-provision-linux-clusters.md). Jako typ clusteru vyberte **interaktivní dotaz** .

## <a name="install-microsoft-hive-odbc-driver"></a>Nainstalovat ovladač Microsoft podregistr ODBC Driver

Stáhněte a nainstalujte [Microsoft Hive ODBC Driver](https://www.microsoft.com/download/details.aspx?id=40886).

## <a name="create-apache-hive-odbc-data-source"></a>Vytvoření zdroje dat Apache Hive ODBC

Následující kroky ukazují, jak vytvořit zdroj dat Apache Hive ODBC.

1. V systému Windows přejděte k   >  **Možnosti spustit nástroje pro správu systému Windows**  >  **ODBC zdroje dat (32bitová verze)/(64bitová verze)**.  Otevře se okno **Správce zdrojů dat ODBC** .

    :::image type="content" source="./media/apache-hive-query-odbc-driver-powershell/hive-odbc-driver-dsn-setup.png " alt-text="Správce zdroje dat OBDC" border="true":::

1. Na kartě **uživatelský název DSN** vyberte **Přidat** a otevřete tak okno **vytvořit nový zdroj dat** .

1. Vyberte **Microsoft Hive ODBC Driver** a pak vyberte **Dokončit** a otevřete tak okno **nastavení Microsoft Hive ODBC Driver DSN** .

1. Zadejte nebo vyberte tyto hodnoty:

   | Vlastnost | Popis |
   | --- | --- |
   |  Název zdroje dat |Zadejte název zdroje dat. |
   |  Hostitelé: |Zadejte `CLUSTERNAME.azurehdinsight.net`. Například `myHDICluster.azurehdinsight.net`. |
   |  Port |Použijte **443**.|
   |  databáze |Použijte **výchozí nastavení**. |
   |  Mechanismus |Výběr **služby Windows Azure HDInsight** |
   |  Uživatelské jméno |Zadejte uživatelské jméno uživatele clusteru HDInsight HTTP. Výchozí uživatelské jméno **admin**. |
   |  Heslo |Zadejte uživatelské heslo pro cluster HDInsight. Zaškrtněte políčko **Uložit heslo (šifrované)**.|

1. Volitelné: vyberte **Rozšířené možnosti**.  

   | Parametr | Popis |
   | --- | --- |
   |  Použít nativní dotaz |Když je vybraná, ovladač ODBC se nepokusí převést TSQL na HiveQL. Tuto možnost použijte jenom v případě, že máte 100%, že posíláte čisté příkazy HiveQL. Při připojování k SQL Server nebo Azure SQL Database byste ji měli nechat nezaškrtnutou. |
   |  Počet načtených řádků na blok |Při načítání velkého počtu záznamů může být nutné vyladit tento parametr, aby bylo zajištěno optimální výkon. |
   |  Výchozí délka sloupce řetězce, délka binárního sloupce, škála desetinných sloupců |Délky datových typů a jejich přesnost mohou ovlivnit způsob, jakým jsou vrácena data. Způsobují nesprávné informace, které mají být vráceny z důvodu ztráty přesnosti a zkrácení. |

    :::image type="content" source="./media/apache-hive-query-odbc-driver-powershell/odbc-data-source-advanced-options.png " alt-text="Rozšířené možnosti konfigurace DSN" border="true":::

1. Vyberte **test** pro otestování zdroje dat. Pokud je zdroj dat správně nakonfigurován, výsledek testu zobrazí **úspěch**.  

1. Kliknutím na **tlačítko OK** zavřete okno testu.  

1. Výběrem **OK** zavřete okno **Microsoft Hive ODBC Driver nastavení DSN** .  

1. Kliknutím na **tlačítko OK** zavřete okno **Správce zdrojů dat ODBC** .  

## <a name="query-data-with-powershell"></a>Dotazování dat pomocí PowerShellu

Následující skript prostředí PowerShell je funkce, kterou rozhraní ODBC slouží k dotazování clusteru podregistru.

```powershell
function Get-ODBC-Data {

   param(
   [string]$query=$(throw 'query is required.'),
   [string]$dsn,  
   [PSCredential] $cred = (Get-Credential)  
   )

   $conn = New-Object System.Data.Odbc.OdbcConnection
   $uname = $cred.UserName

   $pswd = (New-Object System.Net.NetworkCredential -ArgumentList "", $cred.Password).Password
   $conn.ConnectionString = "DSN=$dsn;Uid=$uname;Pwd=$pswd;"
   $conn.open()
   $cmd = New-object System.Data.Odbc.OdbcCommand($query,$conn)

   $ds = New-Object system.Data.DataSet

   (New-Object system.Data.odbc.odbcDataAdapter($cmd)).fill($ds) #| out-null
   $conn.close()
   $ds.Tables
}
```

Následující fragment kódu používá výše uvedenou funkci ke spuštění dotazu na interaktivním clusteru dotazů, který jste vytvořili na začátku tohoto kurzu. Nahraďte `DATASOURCENAME` **názvem zdroje dat** , který jste zadali na obrazovce **nastavení Microsoft Hive ODBC Driver DSN** . Po zobrazení výzvy k zadání přihlašovacích údajů zadejte uživatelské jméno a heslo, které jste zadali v části **uživatelské jméno přihlášení clusteru** a **heslo přihlášení clusteru** při vytváření clusteru.

```powershell

$dsn = "DATASOURCENAME"

$query = "select count(distinct clientid) AS total_clients from hivesampletable"

Get-ODBC-Data -query $query -dsn $dsn
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, odstraňte skupinu prostředků, cluster HDInsight a účet úložiště. Provedete to tak, že vyberete skupinu prostředků, ve které se cluster vytvořil, a kliknete na **Odstranit**.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste zjistili, jak pomocí ovladače Microsoft podregistr ODBC a prostředí PowerShell načíst data z vašeho clusteru Azure HDInsight Interactive Query.

> [!div class="nextstepaction"]
> [Připojení Excelu k Apache Hive pomocí rozhraní ODBC](../hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md)
