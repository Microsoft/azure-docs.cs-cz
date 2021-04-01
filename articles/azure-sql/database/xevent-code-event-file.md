---
title: Kód souboru události XEvent
description: Poskytuje prostředí PowerShell a Transact-SQL pro ukázku kódu, který ukazuje cíl souboru události v rozšířené události na Azure SQL Database. Azure Storage je požadovaná součást tohoto scénáře.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: sqldbrb=1
ms.devlang: PowerShell
ms.topic: sample
author: WilliamDAssafMSFT
ms.author: wiassaf
ms.reviewer: sstein
ms.date: 06/06/2020
ms.openlocfilehash: d7a57f98551cf91ed87858caba0907471bcf6b12
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "96501222"
---
# <a name="event-file-target-code-for-extended-events-in-azure-sql-database"></a>Cílový kód souboru události pro rozšířené události v Azure SQL Database
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

[!INCLUDE [sql-database-xevents-selectors-1-include](../../../includes/sql-database-xevents-selectors-1-include.md)]

Potřebujete kompletní ukázku kódu pro robustní způsob, jak zachytit a ohlásit informace pro rozšířenou událost.

V Microsoft SQL Server se [cíl souboru události](/previous-versions/sql/sql-server-2016/ff878115(v=sql.130)) používá k ukládání výstupů událostí do souboru místního pevného disku. Tyto soubory ale nejsou k dispozici pro Azure SQL Database. Místo toho používáme službu Azure Storage k podpoře cíle souboru události.

V tomto tématu se zobrazuje příklad dvoufázové fáze kódu:

- PowerShell, chcete-li vytvořit kontejner Azure Storage v cloudu.
- Transact-SQL:
  - Chcete-li přiřadit kontejner Azure Storage k cíli souboru události.
  - Chcete-li vytvořit a spustit relaci události a tak dále.

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

> [!IMPORTANT]
> Modul PowerShell Azure Resource Manager je stále podporován Azure SQL Database, ale všechny budoucí vývojové prostředí jsou pro modul AZ. SQL. Tyto rutiny naleznete v tématu [AzureRM. SQL](/powershell/module/AzureRM.Sql/). Argumenty pro příkazy v modulech AZ a v modulech AzureRm jsou v podstatě identické.

- Účet a předplatné Azure. Můžete si zaregistrovat i [bezplatnou zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).
- Všechny databáze, na kterých můžete vytvořit tabulku.
  
  - Volitelně můžete [vytvořit ukázkovou databázi **AdventureWorksLT**](single-database-create-quickstart.md) během několika minut.

- SQL Server Management Studio (ssms.exe), v ideálním případě podle nejnovější měsíční verze aktualizace.
  Nejnovější ssms.exe si můžete stáhnout z těchto:
  
  - Téma s názvem [stažení SQL Server Management Studio](/sql/ssms/download-sql-server-management-studio-ssms).
  - [Přímý odkaz na stažení.](https://go.microsoft.com/fwlink/?linkid=616025)

- Musíte mít nainstalované [Azure PowerShell moduly](https://go.microsoft.com/?linkid=9811175) .

  - Moduly poskytují příkazy, jako je například- **New-AzStorageAccount**.

## <a name="phase-1-powershell-code-for-azure-storage-container"></a>Fáze 1: kód PowerShellu pro kontejner Azure Storage

Toto prostředí PowerShell je fáze 1 ukázky kódu ve dvou fázích.

Skript začíná příkazy pro vyčištění po možném předchozím spuštění a je rerunnable.

1. Vložte skript prostředí PowerShell do jednoduchého textového editoru, jako je například Notepad.exe, a tento skript uložte jako soubor s příponou **. ps1**.
2. Spusťte PowerShell ISE jako správce.
3. Na příkazovém řádku zadejte<br/>`Set-ExecutionPolicy -ExecutionPolicy Unrestricted -Scope CurrentUser`<br/>a potom stiskněte klávesu ENTER.
4. V prostředí PowerShell ISE otevřete soubor **. ps1** . Spusťte skript.
5. Skript nejprve spustí nové okno, ve kterém se přihlašujete k Azure.

   - Pokud znovu spustíte skript bez přerušení relace, máte pohodlný možnost komentovat příkaz **Add-AzureAccount** .

![Prostředí PowerShell ISE s nainstalovaným modulem Azure je připravené ke spuštění skriptu.](./media/xevent-code-event-file/event-file-powershell-ise-b30.png)

### <a name="powershell-code"></a>Kód PowerShellu

Tento skript PowerShellu předpokládá, že jste už nainstalovali modul AZ Module. Informace najdete v tématu [Instalace modulu Azure PowerShell](/powershell/azure/install-Az-ps).

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

Poznamenejte si několik pojmenovaných hodnot, které skript PowerShellu vytiskne, když skončí. Tyto hodnoty je nutné upravit do skriptu Transact-SQL, který následuje jako fáze 2.

<!--
TODO:   Consider whether the preceding PowerShell code example deserves to be updated to the latest package (AzureRM.SQL?).
2020/June/06   Adding the !NOTE below about "ADLS Gen2 storage accounts".
Related to   https://github.com/MicrosoftDocs/azure-docs/issues/56520
-->

> [!NOTE]
> V předchozím příkladu kódu PowerShellu nejsou rozšířené události SQL kompatibilní s účty úložiště ADLS Gen2.

## <a name="phase-2-transact-sql-code-that-uses-azure-storage-container"></a>Fáze 2: kód Transact-SQL, který používá kontejner Azure Storage

- Ve fázi 1 této ukázky kódu jste spustili skript prostředí PowerShell pro vytvoření kontejneru Azure Storage.
- V dalším kroku 2 musí následující skript Transact-SQL použít kontejner.

Skript začíná příkazy pro vyčištění po možném předchozím spuštění a je rerunnable.

Skript prostředí PowerShell vytiskl po ukončení několik pojmenovaných hodnot. Chcete-li použít tyto hodnoty, je nutné upravit skript Transact-SQL. Vyhledá **body úprav ve skriptu** Transact-SQL.

1. Otevřete SQL Server Management Studio (ssms.exe).
2. Připojte se k databázi v Azure SQL Database.
3. Kliknutím otevřete nové podokno dotazu.
4. Do podokna dotazu vložte následující skript Transact-SQL.
5. Vyhledá všechny **TODO** ve skriptu a provede příslušné úpravy.
6. Uložte a spusťte skript.

> [!WARNING]
> Hodnota klíče SAS generovaná předchozím skriptem PowerShellu může začínat znakem "?". (otazník). Při použití klíče SAS v následujícím skriptu T-SQL je nutné *Odebrat úvodní znak "?"*. V opačném případě může být vaše úsilí blokováno zabezpečením.

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

Pokud se cíl nepovede připojit při spuštění, musíte zastavit a znovu spustit relaci události:

```sql
ALTER EVENT SESSION ... STATE = STOP;
GO
ALTER EVENT SESSION ... STATE = START;
GO
```

## <a name="output"></a>Výstup

Až se skript Transact-SQL dokončí, klikněte na buňku pod záhlavím **event_data_XML** sloupce. **\<event>** Zobrazí se jeden prvek, který ukazuje jeden příkaz Update.

Zde je jeden **\<event>** prvek, který byl vygenerován při testování:

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

Předchozí skript Transact-SQL použil následující systémovou funkci ke čtení event_file:

- [sys.fn_xe_file_target_read_file (Transact-SQL)](/sql/relational-databases/system-functions/sys-fn-xe-file-target-read-file-transact-sql)

Vysvětlení pokročilých možností zobrazení dat z rozšířených událostí je k dispozici na adrese:

- [Rozšířené zobrazení cílových dat z rozšířených událostí](/sql/relational-databases/extended-events/advanced-viewing-of-target-data-from-extended-events-in-sql-server)

## <a name="converting-the-code-sample-to-run-on-sql-server"></a>Převod ukázky kódu pro spuštění v SQL Server

Předpokládejme, že jste chtěli spustit předchozí ukázku jazyka Transact-SQL v Microsoft SQL Server.

- Pro jednoduchost byste chtěli zcela nahradit použití kontejneru Azure Storage jednoduchým souborem, jako je například *C:\myeventdata.XEL*. Soubor se zapíše na místní pevný disk počítače, který je hostitelem SQL Server.
- Pro **Vytvoření hlavního klíče** a **Vytvoření přihlašovacích údajů** nepotřebujete žádný druh příkazů jazyka Transact-SQL.
- V příkazu **vytvořit relaci události** v klauzuli **Add Target** byste nahradili hodnotu http přiřazenou **souboru filename =** řetězcem s úplnými cestami, jako je *C:\myfile.XEL*.
  
  - Nemusíte mít žádný účet Azure Storage.

## <a name="more-information"></a>Další informace

Další informace o účtech a kontejnerech ve službě Azure Storage najdete v tématech:

- [Jak používat úložiště objektů BLOB z .NET](../../storage/blobs/storage-quickstart-blobs-dotnet.md)
- [Pojmenování kontejnerů, objektů BLOB a metadat a odkazování na ně](/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata)
- [Práce s kořenovým kontejnerem](/rest/api/storageservices/Working-with-the-Root-Container)
- [Lekce 1: Vytvoření zásad uloženého přístupu a sdíleného přístupového podpisu na kontejneru Azure](/sql/relational-databases/tutorial-use-azure-blob-storage-service-with-sql-server-2016#1---create-stored-access-policy-and-shared-access-storage)
  - [Lekce 2: vytvoření přihlašovacích údajů pro SQL Server pomocí sdíleného přístupového podpisu](/sql/relational-databases/tutorial-use-azure-blob-storage-service-with-sql-server-2016#2---create-a-sql-server-credential-using-a-shared-access-signature)
- [Rozšířené události pro Microsoft SQL Server](/sql/relational-databases/extended-events/extended-events)