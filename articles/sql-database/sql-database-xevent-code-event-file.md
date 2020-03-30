---
title: Kód souboru události XEvent
description: Poskytuje Prostředí PowerShell a Transact-SQL pro ukázku dvoufázového kódu, která demonstruje cíl souboru událostí v rozšířené události v Azure SQL Database. Azure Storage je povinnou součástí tohoto scénáře.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: ''
ms.devlang: PowerShell
ms.topic: conceptual
author: MightyPen
ms.author: genemi
ms.reviewer: jrasnik
ms.date: 03/12/2019
ms.openlocfilehash: a9bf28fb1b3c5278d25b417fc646d2ad3d6f1abc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79213979"
---
# <a name="event-file-target-code-for-extended-events-in-sql-database"></a>Cílový kód souboru událostí pro rozšířené události v databázi SQL

[!INCLUDE [sql-database-xevents-selectors-1-include](../../includes/sql-database-xevents-selectors-1-include.md)]

Chcete kompletní ukázku kódu pro robustní způsob, jak zachytit a sestavy informace pro rozšířené události.

V microsoft sql serveru se [cíl soubor událostí](https://msdn.microsoft.com/library/ff878115.aspx) používá k ukládání výstupů událostí do souboru místního pevného disku. Ale tyto soubory nejsou k dispozici pro Azure SQL Database. Místo toho používáme službu Azure Storage pro podporu cíle souboru událostí.

Toto téma představuje ukázku dvoufázového kódu:

- Prostředí PowerShell k vytvoření kontejneru úložiště Azure v cloudu.
- Transact-SQL:
  
  - Přiřazení kontejneru úložiště Azure k cíli souboru událostí.
  - Chcete-li vytvořit a spustit relaci události a tak dále.

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

> [!IMPORTANT]
> Modul PowerShell Azure Resource Manager je stále podporovaný službou Azure SQL Database, ale veškerý budoucí vývoj je pro modul Az.Sql. Tyto rutiny naleznete v tématu [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Argumenty pro příkazy v modulu Az a v modulech AzureRm jsou v podstatě identické.

- Účet a předplatné Azure. Můžete si zaregistrovat i [bezplatnou zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).
- Libovolná databáze, ve které můžete vytvořit tabulku.
  
  - Volitelně můžete [vytvořit demonstrační databázi **AdventureWorksLT** ](sql-database-get-started.md) během několika minut.

- SQL Server Management Studio (ssms.exe), v ideálním případě jeho nejnovější měsíční aktualizace verze.
  Zde si můžete stáhnout nejnovější ssms.exe z:
  
  - Téma s názvem [Download SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).
  - [Přímý odkaz na stažení.](https://go.microsoft.com/fwlink/?linkid=616025)

- Musíte mít nainstalované [moduly Azure PowerShellu.](https://go.microsoft.com/?linkid=9811175)

  - Moduly poskytují příkazy, jako je například **- New-AzStorageAccount**.

## <a name="phase-1-powershell-code-for-azure-storage-container"></a>Fáze 1: Kód Prostředí PowerShell pro kontejner úložiště Azure

Toto prostředí PowerShell je fáze 1 ukázky dvoufázového kódu.

Skript začíná příkazy vyčistit po možném předchozím spuštění a je rerunnable.

1. Vložte skript prostředí PowerShell do jednoduchého textového editoru, například Notepad.exe, a uložte jej jako soubor s příponou **PS1**.
2. Spusťte prostředí PowerShell ISE jako správce.
3. Na výzvu zadejte<br/>`Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope CurrentUser`<br/>a pak stiskněte Enter.
4. V prostředí PowerShell ISE otevřete soubor **PS1.** Spusťte skript.
5. Skript nejprve spustí nové okno, ve kterém se přihlásíte do Azure.

   - Pokud skript znovu spustíte bez narušení relace, máte pohodlnou možnost komentovat příkaz **Add-AzureAccount.**

![PowerShell ISE s nainstalovaným modulem Azure připravený ke spuštění skriptu.][30_powershell_ise]

### <a name="powershell-code"></a>Kód Prostředí PowerShell

Tento skript prostředí PowerShell předpokládá, že jste již nainstalovali modul Az. Další informace najdete [v tématu Instalace modulu Azure PowerShell](/powershell/azure/install-Az-ps).

```powershell
## TODO: Before running, find all 'TODO' and make each edit!!

cls;

#--------------- 1 -----------------------

'Script assumes you have already logged your PowerShell session into Azure.
But if not, run  Connect-AzAccount (or  Connect-AzAccount), just one time.';
#Connect-AzAccount;   # Same as  Connect-AzAccount.

#-------------- 2 ------------------------

'
TODO: Edit the values assigned to these variables, especially the first few!
';

# Ensure the current date is between
# the Expiry and Start time values that you edit here.

$subscriptionName    = 'YOUR_SUBSCRIPTION_NAME';
$resourceGroupName   = 'YOUR_RESOURCE-GROUP-NAME';

$policySasExpiryTime = '2018-08-28T23:44:56Z';
$policySasStartTime  = '2017-10-01';

$storageAccountLocation = 'YOUR_STORAGE_ACCOUNT_LOCATION';
$storageAccountName     = 'YOUR_STORAGE_ACCOUNT_NAME';
$contextName            = 'YOUR_CONTEXT_NAME';
$containerName          = 'YOUR_CONTAINER_NAME';
$policySasToken         = ' ? ';

$policySasPermission = 'rwl';  # Leave this value alone, as 'rwl'.

#--------------- 3 -----------------------

# The ending display lists your Azure subscriptions.
# One should match the $subscriptionName value you assigned
#   earlier in this PowerShell script. 

'Choose an existing subscription for the current PowerShell environment.';

Select-AzSubscription -Subscription $subscriptionName;

#-------------- 4 ------------------------

'
Clean up the old Azure Storage Account after any previous run, 
before continuing this new run.';

if ($storageAccountName) {
    Remove-AzStorageAccount `
        -Name              $storageAccountName `
        -ResourceGroupName $resourceGroupName;
}

#--------------- 5 -----------------------

[System.DateTime]::Now.ToString();

'
Create a storage account. 
This might take several minutes, will beep when ready.
  ...PLEASE WAIT...';

New-AzStorageAccount `
    -Name              $storageAccountName `
    -Location          $storageAccountLocation `
    -ResourceGroupName $resourceGroupName `
    -SkuName           'Standard_LRS';

[System.DateTime]::Now.ToString();
[System.Media.SystemSounds]::Beep.Play();

'
Get the access key for your storage account.
';

$accessKey_ForStorageAccount = `
    (Get-AzStorageAccountKey `
        -Name              $storageAccountName `
        -ResourceGroupName $resourceGroupName
        ).Value[0];

"`$accessKey_ForStorageAccount = $accessKey_ForStorageAccount";

'Azure Storage Account cmdlet completed.
Remainder of PowerShell .ps1 script continues.
';

#--------------- 6 -----------------------

# The context will be needed to create a container within the storage account.

'Create a context object from the storage account and its primary access key.
';

$context = New-AzStorageContext `
    -StorageAccountName $storageAccountName `
    -StorageAccountKey  $accessKey_ForStorageAccount;

'Create a container within the storage account.
';

$containerObjectInStorageAccount = New-AzStorageContainer `
    -Name    $containerName `
    -Context $context;

'Create a security policy to be applied to the SAS token.
';

New-AzStorageContainerStoredAccessPolicy `
    -Container  $containerName `
    -Context    $context `
    -Policy     $policySasToken `
    -Permission $policySasPermission `
    -ExpiryTime $policySasExpiryTime `
    -StartTime  $policySasStartTime;

'
Generate a SAS token for the container.
';
try {
    $sasTokenWithPolicy = New-AzStorageContainerSASToken `
        -Name    $containerName `
        -Context $context `
        -Policy  $policySasToken;
}
catch {
    $Error[0].Exception.ToString();
}

#-------------- 7 ------------------------

'Display the values that YOU must edit into the Transact-SQL script next!:
';

"storageAccountName: $storageAccountName";
"containerName:      $containerName";
"sasTokenWithPolicy: $sasTokenWithPolicy";

'
REMINDER: sasTokenWithPolicy here might start with "?" character, which you must exclude from Transact-SQL.
';

'
(Later, return here to delete your Azure Storage account. See the preceding  Remove-AzStorageAccount -Name $storageAccountName)';

'
Now shift to the Transact-SQL portion of the two-part code sample!';

# EOFile
```

Poznamenejte si několik pojmenovaných hodnot, které skript Prostředí PowerShell vytiskne po jeho ukončení. Tyto hodnoty je nutné upravit do skriptu Transact-SQL, který následuje jako fáze 2.

## <a name="phase-2-transact-sql-code-that-uses-azure-storage-container"></a>Fáze 2: Kód Transact-SQL, který používá kontejner úložiště Azure

- Ve fázi 1 této ukázky kódu jste spustili skript Prostředí PowerShell k vytvoření kontejneru úložiště Azure.
- Další ve fázi 2 následující Skript Transact-SQL musí použít kontejner.

Skript začíná příkazy vyčistit po možném předchozím spuštění a je rerunnable.

Skript prostředí PowerShell vytiskl několik pojmenovaných hodnot, když skončil. Chcete-li tyto hodnoty použít, je nutné upravit skript Transact-SQL. Najít **TODO** ve skriptu Transact-SQL najít body úprav.

1. Otevřete sql server management studio (ssms.exe).
2. Připojte se k databázi Azure SQL Database.
3. Klepnutím otevřete nové podokno dotazů.
4. Vložte do podokna dotazů následující skript Transact-SQL.
5. Najděte **každý todo** ve skriptu a proveďte příslušné úpravy.
6. Uložte a spusťte skript.

> [!WARNING]
> Hodnota klíče SAS generovaná předchozím skriptem prostředí PowerShell může začínat na "?" (otazník). Při použití klíče SAS v následujícím skriptu T-SQL je nutné *odebrat úvodní tlačítko ?.* V opačném případě může být vaše úsilí blokováno zabezpečením.

### <a name="transact-sql-code"></a>Kód Transact-SQL

```sql
---- TODO: First, run the earlier PowerShell portion of this two-part code sample.
---- TODO: Second, find every 'TODO' in this Transact-SQL file, and edit each.

---- Transact-SQL code for Event File target on Azure SQL Database.

SET NOCOUNT ON;
GO

----  Step 1.  Establish one little table, and  ---------
----  insert one row of data.

IF EXISTS
    (SELECT * FROM sys.objects
        WHERE type = 'U' and name = 'gmTabEmployee')
BEGIN
    DROP TABLE gmTabEmployee;
END
GO

CREATE TABLE gmTabEmployee
(
    EmployeeGuid         uniqueIdentifier   not null  default newid()  primary key,
    EmployeeId           int                not null  identity(1,1),
    EmployeeKudosCount   int                not null  default 0,
    EmployeeDescr        nvarchar(256)          null
);
GO

INSERT INTO gmTabEmployee ( EmployeeDescr )
    VALUES ( 'Jane Doe' );
GO

------  Step 2.  Create key, and  ------------
------  Create credential (your Azure Storage container must already exist).

IF NOT EXISTS
    (SELECT * FROM sys.symmetric_keys
        WHERE symmetric_key_id = 101)
BEGIN
    CREATE MASTER KEY ENCRYPTION BY PASSWORD = '0C34C960-6621-4682-A123-C7EA08E3FC46' -- Or any newid().
END
GO

IF EXISTS
    (SELECT * FROM sys.database_scoped_credentials
        -- TODO: Assign AzureStorageAccount name, and the associated Container name.
        WHERE name = 'https://gmstorageaccountxevent.blob.core.windows.net/gmcontainerxevent')
BEGIN
    DROP DATABASE SCOPED CREDENTIAL
        -- TODO: Assign AzureStorageAccount name, and the associated Container name.
        [https://gmstorageaccountxevent.blob.core.windows.net/gmcontainerxevent] ;
END
GO

CREATE
    DATABASE SCOPED
    CREDENTIAL
        -- use '.blob.',   and not '.queue.' or '.table.' etc.
        -- TODO: Assign AzureStorageAccount name, and the associated Container name.
        [https://gmstorageaccountxevent.blob.core.windows.net/gmcontainerxevent]
    WITH
        IDENTITY = 'SHARED ACCESS SIGNATURE',  -- "SAS" token.
        -- TODO: Paste in the long SasToken string here for Secret, but exclude any leading '?'.
        SECRET = 'sv=2014-02-14&sr=c&si=gmpolicysastoken&sig=EjAqjo6Nu5xMLEZEkMkLbeF7TD9v1J8DNB2t8gOKTts%3D'
    ;
GO

------  Step 3.  Create (define) an event session.  --------
------  The event session has an event with an action,
------  and a has a target.

IF EXISTS
    (SELECT * from sys.database_event_sessions
        WHERE name = 'gmeventsessionname240b')
BEGIN
    DROP
        EVENT SESSION
            gmeventsessionname240b
        ON DATABASE;
END
GO

CREATE
    EVENT SESSION
        gmeventsessionname240b
    ON DATABASE

    ADD EVENT
        sqlserver.sql_statement_starting
            (
            ACTION (sqlserver.sql_text)
            WHERE statement LIKE 'UPDATE gmTabEmployee%'
            )
    ADD TARGET
        package0.event_file
            (
            -- TODO: Assign AzureStorageAccount name, and the associated Container name.
            -- Also, tweak the .xel file name at end, if you like.
            SET filename =
                'https://gmstorageaccountxevent.blob.core.windows.net/gmcontainerxevent/anyfilenamexel242b.xel'
            )
    WITH
        (MAX_MEMORY = 10 MB,
        MAX_DISPATCH_LATENCY = 3 SECONDS)
    ;
GO

------  Step 4.  Start the event session.  ----------------
------  Issue the SQL Update statements that will be traced.
------  Then stop the session.

------  Note: If the target fails to attach,
------  the session must be stopped and restarted.

ALTER
    EVENT SESSION
        gmeventsessionname240b
    ON DATABASE
    STATE = START;
GO

SELECT 'BEFORE_Updates', EmployeeKudosCount, * FROM gmTabEmployee;

UPDATE gmTabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 2
    WHERE EmployeeDescr = 'Jane Doe';

UPDATE gmTabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 13
    WHERE EmployeeDescr = 'Jane Doe';

SELECT 'AFTER__Updates', EmployeeKudosCount, * FROM gmTabEmployee;
GO

ALTER
    EVENT SESSION
        gmeventsessionname240b
    ON DATABASE
    STATE = STOP;
GO

-------------- Step 5.  Select the results. ----------

SELECT
        *, 'CLICK_NEXT_CELL_TO_BROWSE_ITS_RESULTS!' as [CLICK_NEXT_CELL_TO_BROWSE_ITS_RESULTS],
        CAST(event_data AS XML) AS [event_data_XML]  -- TODO: In ssms.exe results grid, double-click this cell!
    FROM
        sys.fn_xe_file_target_read_file
            (
                -- TODO: Fill in Storage Account name, and the associated Container name.
                -- TODO: The name of the .xel file needs to be an exact match to the files in the storage account Container (You can use Storage Account explorer from the portal to find out the exact file names or you can retrieve the name using the following DMV-query: select target_data from sys.dm_xe_database_session_targets. The 3rd xml-node, "File name", contains the name of the file currently written to.)
                'https://gmstorageaccountxevent.blob.core.windows.net/gmcontainerxevent/anyfilenamexel242b',
                null, null, null
            );
GO

-------------- Step 6.  Clean up. ----------

DROP
    EVENT SESSION
        gmeventsessionname240b
    ON DATABASE;
GO

DROP DATABASE SCOPED CREDENTIAL
    -- TODO: Assign AzureStorageAccount name, and the associated Container name.
    [https://gmstorageaccountxevent.blob.core.windows.net/gmcontainerxevent]
    ;
GO

DROP TABLE gmTabEmployee;
GO

PRINT 'Use PowerShell Remove-AzStorageAccount to delete your Azure Storage account!';
GO
```

Pokud se cíl při spuštění nepodaří připojit, je nutné zastavit a restartovat relaci události:

```sql
ALTER EVENT SESSION ... STATE = STOP;
GO
ALTER EVENT SESSION ... STATE = START;
GO
```

## <a name="output"></a>Výstup

Po dokončení skriptu Transact-SQL klikněte na buňku pod záhlavím **sloupce event_data_XML.** Zobrazí ** \<** se jeden prvek>události, který zobrazuje jeden příkaz UPDATE.

Zde je ** \<** jedna událost>prvek, který byl generován během testování:

```xml
<event name="sql_statement_starting" package="sqlserver" timestamp="2015-09-22T19:18:45.420Z">
  <data name="state">
    <value>0</value>
    <text>Normal</text>
  </data>
  <data name="line_number">
    <value>5</value>
  </data>
  <data name="offset">
    <value>148</value>
  </data>
  <data name="offset_end">
    <value>368</value>
  </data>
  <data name="statement">
    <value>UPDATE gmTabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 2
    WHERE EmployeeDescr = 'Jane Doe'</value>
  </data>
  <action name="sql_text" package="sqlserver">
    <value>

SELECT 'BEFORE_Updates', EmployeeKudosCount, * FROM gmTabEmployee;

UPDATE gmTabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 2
    WHERE EmployeeDescr = 'Jane Doe';

UPDATE gmTabEmployee
    SET EmployeeKudosCount = EmployeeKudosCount + 13
    WHERE EmployeeDescr = 'Jane Doe';

SELECT 'AFTER__Updates', EmployeeKudosCount, * FROM gmTabEmployee;
</value>
  </action>
</event>
```

Předchozí skript Transact-SQL použil ke čtení event_file následující systémovou funkci:

- [sys.fn_xe_file_target_read_file (Transact-SQL)](https://msdn.microsoft.com/library/cc280743.aspx)

Vysvětlení pokročilých možností pro zobrazení dat z rozšířených událostí je k dispozici na adrese:

- [Pokročilé zobrazení cílových dat z rozšířených událostí](https://msdn.microsoft.com/library/mt752502.aspx)

## <a name="converting-the-code-sample-to-run-on-sql-server"></a>Převod ukázky kódu pro spuštění na serveru SQL Server

Předpokládejme, že jste chtěli spustit předchozí ukázku Transact-SQL na serveru Microsoft SQL Server.

- Pro jednoduchost byste chtěli zcela nahradit použití kontejneru úložiště Azure jednoduchým souborem, jako je *C:\myeventdata.xel*. Soubor by byl zapsán na místní pevný disk počítače, který je hostitelem serveru SQL Server.
- Pro **příkazy CREATE MASTER KEY** a **CREATE CREDENTIAL**byste nepotřebovali žádné příkazy Transact-SQL .
- V **příkazu CREATE EVENT SESSION** byste v klauzuli **ADD TARGET** nahradili hodnotu Http přiřazenou **filename=** úplným řetězcem cesty, jako je *C:\myfile.xel*.
  
  - Není potřeba se zapojit žádný účet Azure Storage.

## <a name="more-information"></a>Další informace

Další informace o účtech a kontejnerech ve službě Azure Storage najdete v tématu:

- [Použití úložiště objektů Blob z rozhraní .NET](../storage/blobs/storage-dotnet-how-to-use-blobs.md)
- [Pojmenování a odkazování na kontejnery, objekty BLOB a metadata](https://msdn.microsoft.com/library/azure/dd135715.aspx)
- [Práce s kořenovým kontejnerem](https://msdn.microsoft.com/library/azure/ee395424.aspx)
- [Lekce 1: Vytvoření zásady uloženého přístupu a sdíleného přístupového podpisu v kontejneru Azure](https://msdn.microsoft.com/library/dn466430.aspx)
  - [Lekce 2: Vytvoření pověření serveru SQL Server pomocí sdíleného přístupového podpisu](https://msdn.microsoft.com/library/dn466435.aspx)
- [Rozšířené události pro microsoft SQL Server](https://docs.microsoft.com/sql/relational-databases/extended-events/extended-events)

<!-- Image references. -->
[30_powershell_ise]: ./media/sql-database-xevent-code-event-file/event-file-powershell-ise-b30.png
