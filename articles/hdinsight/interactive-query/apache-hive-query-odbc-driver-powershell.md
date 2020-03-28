---
title: Dotaz Apache Hive s ovladačem ODBC & PowerShell - Azure HDInsight
description: Pomocí ovladače Microsoft Hive ODBC a prostředí PowerShell můžete dotazovat clusterů Apache Hive na Azure HDInsight.
keywords: úl,úl odbc,powershell
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: tutorial
ms.date: 06/27/2019
ms.openlocfilehash: f6139bb98fa0272e43c8e180d4ec029f7a7538bb
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "73494326"
---
# <a name="tutorial-query-apache-hive-with-odbc-and-powershell"></a>Kurz: Dotaz apache hive s ROZHRANÍM ODBC a PowerShell

Ovladače ODBC společnosti Microsoft poskytují flexibilní způsob interakce s různými druhy zdrojů dat, včetně Apache Hive. Můžete psát kód ve skriptovacích jazycích, jako je PowerShell, které používají ovladače ODBC k otevření připojení ke clusteru Hive, předání dotazu podle vašeho výběru a zobrazení výsledků.

V tomto kurzu budete dělat následující úkoly:

> [!div class="checklist"]
> * Stažení a instalace ovladače Microsoft Hive ODBC
> * Vytvoření zdroje dat ODBC Apache Hive propojeného s vaším clusterem
> * Dotaz na ukázkové informace z clusteru pomocí PowerShellu

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete.

## <a name="prerequisites"></a>Požadavky

Před zahájením tohoto kurzu musíte mít tyto položky:

* Cluster interaktivních dotazů na hdinsightu. Pokud chcete ho vytvořit, [přečtěte si, že začínáme s Azure HDInsight](../hdinsight-hadoop-provision-linux-clusters.md). Jako typ clusteru vyberte **Interaktivní dotaz.**

## <a name="install-microsoft-hive-odbc-driver"></a>Instalace ovladače Microsoft Hive ODBC

Stáhněte a nainstalujte [ovladač Microsoft Hive ODBC Driver](https://www.microsoft.com/download/details.aspx?id=40886).

## <a name="create-apache-hive-odbc-data-source"></a>Vytvoření zdroje dat Apache Hive ODBC

Následující kroky ukazují, jak vytvořit zdroj dat Apache Hive ODBC.

1. V systému Windows přejděte na **položku Start** > **Windows Administrative Tools** > **ODBC Data Sources (32-bit)/(64-bit)**.  Otevře se okno **Správce zdroje dat rozhraní ODBC.**

    ![Správce zdroje dat OBDC](./media/apache-hive-query-odbc-driver-powershell/hive-odbc-driver-dsn-setup.png "Konfigurace dsn pomocí správce zdroje dat ODBC")

1. Na kartě **DSN uživatele** vyberte **Přidat,** chcete-li otevřít okno **Vytvořit nový zdroj dat.**

1. Vyberte **ovladač Microsoft Hive ODBC Driver**a pak klepnutím na příkaz **Dokončit** otevřete okno **Instalace dsn ovladače Microsoft Hive ODBC.**

1. Zadejte nebo vyberte tyto hodnoty:

   | Vlastnost | Popis |
   | --- | --- |
   |  Název zdroje dat |Zadejte název zdroje dat. |
   |  Hostitelé |Zadejte `CLUSTERNAME.azurehdinsight.net`. Například `myHDICluster.azurehdinsight.net`. |
   |  Port |Použijte **443**.|
   |  Databáze |Použít **výchozí**. |
   |  Mechanismus |Vyberte **službu Windows Azure HDInsight** |
   |  Uživatelské jméno |Zadejte uživatelské jméno HTTP clusteru HDInsight. Výchozí uživatelské jméno **admin**. |
   |  Heslo |Zadejte uživatelské heslo clusteru HDInsight. Zaškrtněte políčko **Uložit heslo (Šifrované).**|

1. Volitelné: Vyberte **upřesnit možnosti**.  

   | Parametr | Popis |
   | --- | --- |
   |  Použití nativního dotazu |Pokud je vybrána, ovladač ODBC se nepokouší převést TSQL do HiveQL. Tuto možnost použijte pouze v případě, že jste si 100% jisti, že odesíláte čisté příkazy HiveQL. Při připojování k SQL Server nebo Azure SQL Database, měli byste nechat nezaškrtnuté. |
   |  Řádky načtené na blok |Při načítání velkého počtu záznamů může být vyžadováno ladění tohoto parametru, aby byl zajištěn optimální výkon. |
   |  Výchozí délka sloupce řetězce, délka binárního sloupce, měřítko desetinného sloupce |Délky a přesnosti datového typu mohou ovlivnit způsob vrácení dat. Způsobit nesprávné informace, které mají být vráceny z důvodu ztráty přesnosti a zkrácení. |

    ![Rozšířené možnosti konfigurace DSN](./media/apache-hive-query-odbc-driver-powershell/odbc-data-source-advanced-options.png "Rozšířené možnosti konfigurace DSN")

1. Chcete-li otestovat zdroj dat, vyberte **možnost Testovat.** Pokud je zdroj dat správně nakonfigurován, výsledek testu ukazuje **ÚSPĚCH**.  

1. Chcete-li zavřít okno Test, vyberte **ok.**  

1. Výběrem **možnosti OK** zavřete okno **Instalace dsn ovladače Microsoft Hive ODBC.**  

1. Chcete-li zavřít okno **Správce zdroje dat ROZHRANÍ ODBC,** vyberte **ok.**  

## <a name="query-data-with-powershell"></a>Dotazovat se na data pomocí PowerShellu

Následující skript prostředí PowerShell je funkce, která ODBC pro dotazování clusteru Hive.

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

Následující fragment kódu používá výše uvedenou funkci k provedení dotazu v clusteru interaktivních dotazů, který jste vytvořili na začátku kurzu. Nahraďte `DATASOURCENAME` **název zdroje dat,** který jste zadali na obrazovce Instalace **dsn ovladače Microsoft Hive ODBC Driver.** Po zobrazení výzvy k zadání pověření zadejte uživatelské jméno a heslo, které jste zadali v části **uživatelské jméno přihlášení clusteru** a **přihlašovací heslo clusteru** při vytváření clusteru.

```powershell

$dsn = "DATASOURCENAME"

$query = "select count(distinct clientid) AS total_clients from hivesampletable"

Get-ODBC-Data -query $query -dsn $dsn
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už nepotřebujete, odstraňte skupinu prostředků, cluster HDInsight a účet úložiště. Chcete-li tak učinit, vyberte skupinu prostředků, ve které byl cluster vytvořen, a klepněte na tlačítko **Odstranit**.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili, jak pomocí ovladače Microsoft Hive ODBC a prostředí PowerShell načíst data z clusteru interaktivních dotazů Azure HDInsight.

> [!div class="nextstepaction"]
> [Připojení Excelu k Apache Hive pomocí rozhraní ODBC](../hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md)
