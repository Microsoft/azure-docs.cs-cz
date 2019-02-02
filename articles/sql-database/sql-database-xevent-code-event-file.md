---
title: Kód souboru události XEvent pro SQL Database | Dokumentace Microsoftu
description: Příklad dvoufázového kódu, který ukazuje cílový soubor událostí v rozšířené události pro Azure SQL Database poskytuje prostředí PowerShell a příkazů jazyka Transact-SQL. Úložiště Azure je požadovanou součástí tohoto scénáře.
services: sql-database
ms.service: sql-database
ms.subservice: monitor
ms.custom: ''
ms.devlang: PowerShell
ms.topic: conceptual
author: MightyPen
ms.author: genemi
ms.reviewer: jrasnik
manager: craigg
ms.date: 12/19/2018
ms.openlocfilehash: 035d2731a78ad6216f56255fb87e32444366ea97
ms.sourcegitcommit: ba035bfe9fab85dd1e6134a98af1ad7cf6891033
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/01/2019
ms.locfileid: "55563487"
---
# <a name="event-file-target-code-for-extended-events-in-sql-database"></a>Cílový kód souboru události pro rozšířené události ve službě SQL Database

[!INCLUDE [sql-database-xevents-selectors-1-include](../../includes/sql-database-xevents-selectors-1-include.md)]

Chcete ukázku dokončení kódu pro robustní způsob, jak informace sběr dat a sestavy pro rozšířené události.

V systému Microsoft SQL Server [cíle souboru událostí](https://msdn.microsoft.com/library/ff878115.aspx) se používá k ukládání výstupů události do souboru místním pevném disku. Ale tyto soubory nejsou k dispozici ke službě Azure SQL Database. Místo toho jsme pomocí služby Azure Storage podporují cíle souboru událostí.

Toto téma představuje ukázku kódu dvoufázového:

* Prostředí PowerShell k vytvoření kontejneru služby Azure Storage v cloudu.
* Transact-SQL:
  
  * Kontejner Azure Storage přiřadit cíl soubor událostí.
  * K vytvoření a spuštění relace událostí a tak dále.

## <a name="prerequisites"></a>Požadavky

* Účet a předplatné Azure. Můžete si zaregistrovat i [bezplatnou zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).
* Všechny databáze, kterou vytvoříte tabulku v.
  
  * Volitelně můžete [vytvořit **AdventureWorksLT** ukázkovou databázi](sql-database-get-started.md) během několika minut.
* SQL Server Management Studio (ssms.exe), v ideálním případě jeho nejnovější měsíční aktualizovanou verzi. 
  Si můžete stáhnout nejnovější ssms.exe od:
  
  * Téma s názvem [stažení aplikace SQL Server Management Studio](https://msdn.microsoft.com/library/mt238290.aspx).
  * [Přímý odkaz na stažení.](https://go.microsoft.com/fwlink/?linkid=616025)
* Musíte mít [moduly Azure Powershellu](https://go.microsoft.com/?linkid=9811175) nainstalované.
  
  * Moduly zadat příkazy, jako - **New-AzureStorageAccount**.

## <a name="phase-1-powershell-code-for-azure-storage-container"></a>Fáze 1: Kód Powershellu pro kontejner Azure Storage

Toto prostředí PowerShell je fáze 1 dvoufázového kódu ukázky.

Pomocí příkazů pro vyčištění po předchozí možné spustit a rerunnable spustí skript.

1. Vložte skript prostředí PowerShell do jednoduchého textového editoru, například Notepad.exe a uložte skript jako soubor s příponou **.ps1**.
2. Spusťte prostředí PowerShell ISE jako správce.
3. Zadejte do příkazového řádku<br/>`Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope CurrentUser`<br/>a potom stiskněte klávesu Enter.
4. V prostředí PowerShell ISE, otevřete vaši **.ps1** souboru. Spusťte skript.
5. Skript poprvé spustí nové okno, ve kterém jste přihlášení k Azure.
   
   * Pokud znovu spustíte skript bez přerušení relace, máte praktické možnost okomentováním odpovídajícího **Add-AzureAccount** příkazu.

![Prostředí PowerShell ISE, pomocí modulu Azure nainstalovaný, připravené ke spuštění skriptu.][30_powershell_ise]

### <a name="powershell-code"></a>Kód Powershellu

Tento skript Powershellu se předpokládá, že jste již spustili rutiny Import-Module pro modul AzureRm. Odkaz na dokumentaci najdete v tématu [prohlížeče modulu prostředí PowerShell](https://docs.microsoft.com/powershell/module/).

```powershell
## TODO: Before running, find all 'TODO' and make each edit!!

cls;

#--------------- 1 -----------------------

'Script assumes you have already logged your PowerShell session into Azure.
But if not, run  Connect-AzureRmAccount (or  Connect-AzureRmAccount), just one time.';
#Connect-AzureRmAccount;   # Same as  Connect-AzureRmAccount.

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

$storageAccountLocation = 'West US';
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

Select-AzureRmSubscription -Subscription $subscriptionName;

#-------------- 4 ------------------------

'
Clean up the old Azure Storage Account after any previous run, 
before continuing this new run.';

If ($storageAccountName)
{
    Remove-AzureRmStorageAccount `
        -Name              $storageAccountName `
        -ResourceGroupName $resourceGroupName;
}

#--------------- 5 -----------------------

[System.DateTime]::Now.ToString();

'
Create a storage account. 
This might take several minutes, will beep when ready.
  ...PLEASE WAIT...';

New-AzureRmStorageAccount `
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
    (Get-AzureRmStorageAccountKey `
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

$context = New-AzureStorageContext `
    -StorageAccountName $storageAccountName `
    -StorageAccountKey  $accessKey_ForStorageAccount;

'Create a container within the storage account.
';

$containerObjectInStorageAccount = New-AzureStorageContainer `
    -Name    $containerName `
    -Context $context;

'Create a security policy to be applied to the SAS token.
';

New-AzureStorageContainerStoredAccessPolicy `
    -Container  $containerName `
    -Context    $context `
    -Policy     $policySasToken `
    -Permission $policySasPermission `
    -ExpiryTime $policySasExpiryTime `
    -StartTime  $policySasStartTime;

'
Generate a SAS token for the container.
';
Try
{
    $sasTokenWithPolicy = New-AzureStorageContainerSASToken `
        -Name    $containerName `
        -Context $context `
        -Policy  $policySasToken;
}
Catch 
{
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
(Later, return here to delete your Azure Storage account. See the preceding  Remove-AzureRmStorageAccount -Name $storageAccountName)';

'
Now shift to the Transact-SQL portion of the two-part code sample!';

# EOFile
```


Poznamenejte si několik pojmenovaných hodnot, které skript prostředí PowerShell vytiskne při jeho ukončení. Je nutné upravit tyto hodnoty do skriptu jazyka Transact-SQL, který následuje za fáze 2.

## <a name="phase-2-transact-sql-code-that-uses-azure-storage-container"></a>Fáze 2: Kód jazyka Transact-SQL, který používá kontejner Azure Storage

* Ve fázi 1 Tento vzorový kód jste spustili skript Powershellu pro vytvoření kontejneru služby Azure Storage.
* Dále ve fázi 2, musíte použít následující skript jazyka Transact-SQL kontejneru.

Pomocí příkazů pro vyčištění po předchozí možné spustit a rerunnable spustí skript.

Skript prostředí PowerShell vytisknout několik pojmenovaných hodnot, kdy se dokončila. Je nutné upravit skript Transact-SQL, který chcete použít tyto hodnoty. Najít **TODO** v skript Transact-SQL, aby vyhledal body upravit.

1. Otevřete SQL Server Management Studio (ssms.exe).
2. Připojení k databázi Azure SQL Database.
3. Kliknutím otevřete nové podokno dotaz.
4. Vložte následující skript jazyka Transact-SQL do podokna dotazu.
5. Najít všechny **TODO** ve skriptu a proveďte příslušné změny.
6. Uložte a spusťte skript.


> [!WARNING]
> Hodnota klíče SAS vygenerovaný předchozí skript prostředí PowerShell může začínat "?" (otazník). Pokud použijete klíč SAS v následujícím skriptu T-SQL, je nutné *odebrat úvodního "?"* . Jinak vaše úsilí může blokovat zabezpečení.


### <a name="transact-sql-code"></a>Kód jazyka Transact-SQL

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

PRINT 'Use PowerShell Remove-AzureStorageAccount to delete your Azure Storage account!';
GO
```


Pokud cíl se nepodaří připojit při spuštění, musíte zastavit a restartovat relaci události:

```sql
ALTER EVENT SESSION ... STATE = STOP;
GO
ALTER EVENT SESSION ... STATE = START;
GO
```


## <a name="output"></a>Výstup

Po dokončení skriptu jazyka Transact-SQL, klikněte na buňku pod **event_data_XML** záhlaví sloupce. Jeden **<event>** element se zobrazuje, které zobrazuje jeden příkaz UPDATE.

Tady je jeden **<event>** element, který byl vygenerován při testování:


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


Předchozí skript Transact-SQL použít následující funkce systému ke čtení event_file:

* [sys.fn_xe_file_target_read_file (Transact-SQL)](https://msdn.microsoft.com/library/cc280743.aspx)

Vysvětlení rozšířené možnosti pro zobrazení dat z rozšířených událostí je k dispozici na:

* [Rozšířené zobrazení cíl dat z rozšířené události](https://msdn.microsoft.com/library/mt752502.aspx)


## <a name="converting-the-code-sample-to-run-on-sql-server"></a>Převod vzorový kód pro spuštění na SQL serveru

Předpokládejme, že chcete spustit v předchozím příkladu příkazů jazyka Transact-SQL na serveru Microsoft SQL Server.

* Pro zjednodušení k úplnému nahrazení použití kontejneru služby Azure Storage pomocí jednoduchého souboru, jako byste **C:\myeventdata.xel**. Soubor by byla zapsána na místní pevný disk počítače, který je hostitelem systému SQL Server.
* Nebude potřeba libovolných příkazů jazyka Transact-SQL pro **CREATE MASTER KEY** a **CREATE CREDENTIAL**.
* V **vytvořit událost relace** příkaz v jeho **přidat cíl** klauzule, by byly nahrazeny Http hodnota přiřazená provedené **filename =** řetězcem úplnou cestu jako **C:\myfile.xel**.
  
  * Žádný účet úložiště Azure potřebovat nepodílí.

## <a name="more-information"></a>Další informace

Další informace o účtech a kontejnerů ve službě Azure Storage najdete v tématu:

* [Použití Blob storage pomocí technologie .NET](../storage/blobs/storage-dotnet-how-to-use-blobs.md)
* [Názvy kontejnerů, objektů blob a metadat a odkazování na ně](https://msdn.microsoft.com/library/azure/dd135715.aspx)
* [Práce s Kořenový kontejner](https://msdn.microsoft.com/library/azure/ee395424.aspx)
* [Lesson 1: Vytvoření uložené zásady přístupu a sdílený přístupový podpis v kontejneru Azure](https://msdn.microsoft.com/library/dn466430.aspx)
  * [Lekce 2: Vytvoření přihlašovacích údajů systému SQL Server pomocí sdíleného přístupového podpisu](https://msdn.microsoft.com/library/dn466435.aspx)
* [Rozšířené události pro Microsoft SQL Server](https://docs.microsoft.com/sql/relational-databases/extended-events/extended-events)

<!--
Image references.
-->

[30_powershell_ise]: ./media/sql-database-xevent-code-event-file/event-file-powershell-ise-b30.png

