---
title: Dotaz Apache Hive pomocí ovladače ODBC a prostředí PowerShell – Azure HDInsight
description: Dotazy Apache Hive clustery v Azure HDInsight pomocí ovladače Microsoft Hive ODBC a prostředí PowerShell.
keywords: Hive, hive odbc, prostředí powershell
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: tutorial
ms.date: 06/27/2019
ms.author: hrasheed
ms.openlocfilehash: b02c865e953861b5ac396538fdd0f0623b0e5428
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/01/2019
ms.locfileid: "67486099"
---
# <a name="tutorial-query-apache-hive-with-odbc-and-powershell"></a>Kurz: Apache Hive dotaz s rozhraním ODBC a prostředí PowerShell

Ovladače Microsoft ODBC poskytují flexibilní způsob, jak pracovat s různými druhy datových zdrojů, včetně Apache Hive. Můžete napsat kód v skriptovací jazyky, jako je PowerShell, které používají ovladače rozhraní ODBC k otevření připojení ke clusteru Hive, předejte dotaz podle svého výběru a zobrazit výsledky.

V tomto kurzu budete provádět následující úlohy:

> [!div class="checklist"]
> * Stáhnout a nainstalovat ovladače Microsoft Hive ODBC
> * Vytvoření zdroje dat Apache Hive ODBC propojené ke svému clusteru
> * Ukázkový dotaz na informace z clusteru pomocí Powershellu

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Před zahájením tohoto kurzu musíte mít tyto položky:

* Cluster Interactive Query v HDInsight. Pokud chcete jeden vytvořit, přečtěte si téma [Začínáme s Azure HDInsight](../hdinsight-hadoop-provision-linux-clusters.md). Vyberte **Interactive Query** jako typ clusteru.

## <a name="install-microsoft-hive-odbc-driver"></a>Instalace ovladače Microsoft Hive ODBC

Stáhněte a nainstalujte [ovladače Microsoft Hive ODBC Driver](https://go.microsoft.com/fwlink/?LinkID=286698).

## <a name="create-apache-hive-odbc-data-source"></a>Vytvoření zdroje dat Apache Hive ODBC

Následující kroky ukazují, jak vytvořit zdroj dat Apache Hive ODBC.

1. Z Windows, přejděte na **Start** > **nástroje pro správu Windows** > **zdroje dat ODBC (32-bit)/(64-bit)** .  **Správce zdrojů dat ODBC** otevře se okno.

    ![Správce zdrojů dat ODBC](./media/apache-hive-query-odbc-driver-powershell/hive-odbc-driver-dsn-setup.png "nakonfigurovat pomocí Správce zdrojů dat ODBC DSN")

1. Z **uživatelské DSN** kartu, vyberte možnost **přidat** otevřít **vytvořit nový zdroj dat** okna.

1. Vyberte **ovladače Microsoft Hive ODBC Driver**a pak vyberte **Dokončit** otevřít **Microsoft Hive ODBC DSN instalace ovladačů** okna.

1. Zadejte nebo vyberte tyto hodnoty:

   | Vlastnost | Popis |
   | --- | --- |
   |  Název zdroje dat |Zadejte název zdroje dat. |
   |  Hostitele |Zadejte `CLUSTERNAME.azurehdinsight.net`. Například `myHDICluster.azurehdinsight.net`. |
   |  Port |Použijte **443**.|
   |  Databáze |Použití **výchozí**. |
   |  Mechanismus |Vyberte **služby Windows Azure HDInsight** |
   |  Uživatelské jméno |Zadejte uživatelské jméno uživatele, HTTP clusteru HDInsight. Výchozí uživatelské jméno **admin**. |
   |  Heslo |Zadejte heslo uživatele clusteru HDInsight. Zaškrtněte políčko **uložit hesla (šifrované)** .|

1. Volitelné: Vyberte **pokročilé možnosti**.  

   | Parametr | Popis |
   | --- | --- |
   |  Použít nativní dotaz |Pokud je vybrána, ovladač ODBC se nepokusí se převést TSQL HiveQL. Tuto možnost použijte jenom v případě, že 100 % jistotu, že odešlete čistě příkazy HiveQL. Při připojování k serveru SQL Server nebo databázi SQL Azure, měli nechat nezaškrtnuté. |
   |  Řádků načtených na blok |Při načítání velké množství záznamů, optimalizace pro tento parametr může být nutné zajistit optimální přínos. |
   |  Výchozí délka sloupce řetězec, délka sloupce binární, Decimal sloupce stupnice |Datový typ délky a přesnosti může ovlivnit, jak se data vrací. Mohou způsobit nesprávné informace, který se má vrátit z důvodu ztráty přesnosti a zkrácení. |

    ![Pokročilá nastavení](./media/apache-hive-query-odbc-driver-powershell/odbc-data-source-advanced-options.png "DSN rozšířené možnosti konfigurace")

1. Vyberte **testování** pro zdroj dat otestovat. Pokud zdroj dat správně nakonfigurovaný, výsledek testu zobrazí **úspěch**.  

1. Vyberte **OK** zavřete okno Test.  

1. Vyberte **OK** zavřete **Microsoft Hive ODBC DSN instalace ovladačů** okna.  

1. Vyberte **OK** zavřete **správce zdrojů dat ODBC** okna.  

## <a name="query-data-with-powershell"></a>Dotazování dat pomocí Powershellu

Následující příkaz powershellu je funkce tohoto rozhraní ODBC pro dotaz Hive clusteru.

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

Následující fragment kódu používá ke spouštění dotazu na clusteru Interactive Query, který jste vytvořili na začátku kurzu výše uvedené funkce. Nahraďte `DATASOURCENAME` s **název zdroje dat** , který jste zadali na **Microsoft Hive ODBC DSN instalace ovladačů** obrazovky. Po zobrazení výzvy k zadání pověření zadejte uživatelské jméno a heslo, které jste zadali v rámci **uživatelské jméno přihlášení clusteru** a **přihlašovací heslo clusteru** při vytváření clusteru.

```powershell

$dsn = "DATASOURCENAME"

$query = "select count(distinct clientid) AS total_clients from hivesampletable"

Get-ODBC-Data -query $query -dsn $dsn
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, odstraňte skupinu prostředků, HDInsight cluster a účet úložiště. Uděláte to tak, vyberte skupinu prostředků, ve kterém byl vytvořen cluster a klikněte na tlačítko **odstranit**.

## <a name="next-steps"></a>Další postup

V tomto kurzu jste zjistili, jak pomocí ovladače Microsoft Hive ODBC a prostředí PowerShell k načtení dat z vašeho clusteru Azure HDInsight Interactive Query.

> [!div class="nextstepaction"]
> [Připojení Excelu k Apache Hive pomocí ovladače ODBC](../hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md)
