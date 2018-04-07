---
title: Kód soubor události XEvent pro SQL Database | Microsoft Docs
description: Příklad dvoufázového kód, který ukazuje soubor událostí cíl v rozšířené události v databázi SQL Azure poskytuje prostředí PowerShell a Transact-SQL. Azure Storage je požadovaných součástí tohoto scénáře.
services: sql-database
author: MightyPen
manager: craigg
ms.service: sql-database
ms.custom: monitor & tune
ms.topic: article
ms.date: 04/01/2018
ms.author: genemi
ms.openlocfilehash: f13ac366a1c382e955db23f3bcefb8f31c89fcb9
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2018
---
# <a name="event-file-target-code-for-extended-events-in-sql-database"></a>Kód cílový soubor události pro rozšířené události v databázi SQL

[!INCLUDE [sql-database-xevents-selectors-1-include](../../includes/sql-database-xevents-selectors-1-include.md)]

Chcete úplného příkladu kódu pro robustní způsob, jak informace o zachycení a sestav pro rozšířené události.

V systému Microsoft SQL Server [cílový soubor událostí](http://msdn.microsoft.com/library/ff878115.aspx) se používá k ukládání výstupy události do souboru pevného disku. Ale tyto soubory nejsou k dispozici pro Azure SQL Database. Místo toho jsme používat službu Azure Storage pro podporu cílový soubor událostí.

Toto téma představuje ukázka dvoufázového kódu:

* Prostředí PowerShell, chcete-li vytvořit kontejner Azure Storage v cloudu.
* Transact-SQL:
  
  * Kontejner úložiště Azure přiřadit cíl soubor událostí.
  * Chcete-li vytvořit a zahájit relaci události a tak dále.

## <a name="prerequisites"></a>Požadavky

* Účet a předplatné Azure. Můžete si zaregistrovat i [bezplatnou zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).
* Všechny databáze, které je možné vytvořit tabulku v.
  
  * Volitelně můžete [vytvořit **AdventureWorksLT** ukázkovou databázi](sql-database-get-started.md) v minutách.
* SQL Server Management Studio (ssms.exe), v ideálním případě její nejnovější měsíční aktualizace verzi. 
  Si můžete stáhnout nejnovější ssms.exe z:
  
  * Téma s názvem [stáhnout SQL Server Management Studio](http://msdn.microsoft.com/library/mt238290.aspx).
  * [Přímý odkaz na stažení.](http://go.microsoft.com/fwlink/?linkid=616025)
* Musíte mít [modulů prostředí Azure PowerShell](http://go.microsoft.com/?linkid=9811175) nainstalována.
  
  * Moduly zadejte příkazy, jako třeba - **New-AzureStorageAccount**.

## <a name="phase-1-powershell-code-for-azure-storage-container"></a>Fáze 1: Kód prostředí PowerShell pro Azure Storage kontejneru

Toto prostředí PowerShell je fáze 1 dvoufázového příklad.

Skript se spustí pomocí příkazů odstraňování až po předchozí možného spustit a je rerunnable.

1. Vložte skript prostředí PowerShell do jednoduchého textového editoru, například Notepad.exe a uložíte skript jako soubor s příponou **.ps1**.
2. Spusťte prostředí PowerShell ISE jako správce.
3. Na příkazovém řádku zadejte<br/>`Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope CurrentUser`<br/>a stiskněte klávesu Enter.
4. V prostředí PowerShell ISE otevřete váš **.ps1** souboru. Spusťte skript.
5. Skript spustí nejprve nové okno, ve kterém se přihlásit k Azure.
   
   * Pokud skript bez nutnosti přerušení vaší relace, máte vhodnou možnost komentování se **Add-AzureAccount** příkaz.

![Prostředí PowerShell ISE s Azure modul nainstalovaný, připraven ke spuštění skriptu.][30_powershell_ise]

### <a name="powershell-code"></a>Prostředí PowerShell kódu

Tento skript prostředí PowerShell předpokládá, že jste již spustili rutiny Import-Module AzureRm modulu. Referenční dokumentaci najdete v tématu [prohlížeče modulu prostředí PowerShell](https://docs.microsoft.com/powershell/module/).

```powershell
## TODO: Before running, find all 'TODO' and make each edit!!

cls;

#--------------- 1 -----------------------

'Script assumes you have already logged your PowerShell session into Azure.
But if not, run  Add-AzureRmAccount (or  Login-AzureRmAccount), just one time.';
#Add-AzureRmAccount;   # Same as  Login-AzureRmAccount.

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


Poznamenejte si několik pojmenovaných hodnot, které skript prostředí PowerShell vytiskne při jeho ukončení. Je nutné upravit tyto hodnoty do skriptu jazyka Transact-SQL, který následuje fáze 2.

## <a name="phase-2-transact-sql-code-that-uses-azure-storage-container"></a>Fáze 2: Kód Transact-SQL, který používá kontejner úložiště Azure

* V této ukázce kódu fáze 1 jste spustili skript prostředí PowerShell vytvořit kontejner úložiště Azure.
* Další ve fázi 2, musíte použít následující skript jazyka Transact-SQL kontejneru.

Skript se spustí pomocí příkazů odstraňování až po předchozí možného spustit a je rerunnable.

Skript prostředí PowerShell vytištěny několik pojmenovaných hodnot, kdy se dokončila. Je nutné upravit skript Transact-SQL pro použití těchto hodnot. Najít **TODO** ve skriptu jazyka Transact-SQL pro vyhledání bodů upravit.

1. Spusťte aplikaci SQL Server Management Studio (ssms.exe).
2. Připojení k vaší databázi Azure SQL Database.
3. Kliknutím otevřete nové podokno dotazu.
4. Vložte následující skript jazyka Transact-SQL do podokna dotazu.
5. Najít každých **TODO** ve skriptu a proveďte příslušné úpravy.
6. Uložte a pak spusťte skript.


> [!WARNING]
> Hodnotu klíče SAS generované předchozí skript prostředí PowerShell může začínat '?' (otazník). Pokud použijete klíč SAS v následujícím skriptu T-SQL, musíte *odebrat úvodního '?'* . V opačném případě mohou vaše úsilí blokovány zabezpečení.


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


Pokud cílový se nepodaří připojit při spuštění, musíte zastavit a restartovat relaci události:

```sql
ALTER EVENT SESSION ... STATE = STOP;
GO
ALTER EVENT SESSION ... STATE = START;
GO
```


## <a name="output"></a>Výstup

Po dokončení skriptu jazyka Transact-SQL, klikněte na buňku v části **event_data_XML** záhlaví sloupce. Jeden **<event>** element se zobrazuje, který zobrazuje jeden příkaz aktualizace.

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


Uvedený skript Transact-SQL použít následující funkce systému ke čtení event_file:

* [sys.fn_xe_file_target_read_file (Transact-SQL)](http://msdn.microsoft.com/library/cc280743.aspx)

Vysvětlení rozšířené možnosti pro zobrazení dat z rozšířených událostí je k dispozici na:

* [Rozšířené zobrazení cílová Data z rozšířených událostí](http://msdn.microsoft.com/library/mt752502.aspx)


## <a name="converting-the-code-sample-to-run-on-sql-server"></a>Převádění ukázkový kód pro spuštění na serveru SQL

Předpokládejme, že chcete spustit v předchozím příkladu Transact-SQL na serveru Microsoft SQL Server.

* Pro jednoduchost, by chcete úplně nahradit použití kontejner úložiště Azure jednoduchého souboru, jako **C:\myeventdata.xel**. Soubor by byla zapsána na místní pevný disk počítače, který je hostitelem systému SQL Server.
* Nebude potřeba jakýkoli druh příkazy jazyka Transact-SQL pro **CREATE MASTER KEY** a **vytvoření pověření**.
* V **vytvořit událost relace** příkaz v jeho **přidat cíl** klauzule, měli byste nahradit Http hodnotu přiřazenou provedené **filename =** řetězcem úplnou cestu jako **C:\myfile.xel**.
  
  * Žádný účet úložiště Azure musí být zahrnut.

## <a name="more-information"></a>Další informace

Další informace o účtech a kontejnerů ve službě Azure Storage najdete v tématu:

* [Používání úložiště Blob z rozhraní .NET](../storage/blobs/storage-dotnet-how-to-use-blobs.md)
* [Názvy kontejnerů, objektů blob a metadat a odkazování na ně](http://msdn.microsoft.com/library/azure/dd135715.aspx)
* [Práce s Kořenový kontejner](http://msdn.microsoft.com/library/azure/ee395424.aspx)
* [Lekce 1: Vytvoření zásady uložené přístup a sdílený přístupový podpis na kontejner Azure](http://msdn.microsoft.com/library/dn466430.aspx)
  * [Lekce 2: Vytvoření přihlašovacích údajů systému SQL Server pomocí sdíleného přístupového podpisu](http://msdn.microsoft.com/library/dn466435.aspx)
* [Rozšířené události pro Microsoft SQL Server](https://docs.microsoft.com/sql/relational-databases/extended-events/extended-events)

<!--
Image references.
-->

[30_powershell_ise]: ./media/sql-database-xevent-code-event-file/event-file-powershell-ise-b30.png

