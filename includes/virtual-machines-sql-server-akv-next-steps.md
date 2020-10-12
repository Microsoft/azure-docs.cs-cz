---
author: rothja
ms.service: virtual-machines-sql
ms.topic: include
ms.date: 10/26/2018
ms.author: jroth
ms.openlocfilehash: e0ff4e91ed55a37e710a5655e7da9ec76b7d1dd5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "84014688"
---
## <a name="next-steps"></a>Další kroky

Po povolení Integrace Azure Key Vault můžete povolit SQL Server šifrování na svém VIRTUÁLNÍm počítači s SQL. Nejdřív budete muset vytvořit asymetrický klíč v trezoru klíčů a symetrický klíč v rámci SQL Server na svém VIRTUÁLNÍm počítači. Pak budete moci spustit příkazy T-SQL, které umožní šifrování pro databáze a zálohy.

Existuje několik forem šifrování, které můžete využít k těmto akcím:

* [Transparentní šifrování dat](/sql/relational-databases/security/encryption/transparent-data-encryption)
* [Šifrovaná zálohování](/sql/relational-databases/backup-restore/backup-encryption)
* [Šifrování na úrovni sloupce (CLE)](/sql/t-sql/functions/cryptographic-functions-transact-sql)

Následující skripty Transact-SQL poskytují příklady pro každou z těchto oblastí.

### <a name="prerequisites-for-examples"></a>Předpoklady pro příklady

Každý příklad je založený na těchto dvou požadavcích: asymetrický klíč z vašeho trezoru klíčů s názvem **CONTOSO_KEY** a přihlašovací údaje vytvořené funkcí integrace integrace s názvem **Azure_EKM_cred**. Následující příkazy jazyka Transact-SQL nastavte pro spuštění příkladů tyto požadavky.

``` sql
USE master;
GO

--create credential
--The <<SECRET>> here requires the <Application ID> (without hyphens) and <Secret> to be passed together without a space between them.
CREATE CREDENTIAL Azure_EKM_cred
    WITH IDENTITY = 'keytestvault', --keyvault
    SECRET = '<<SECRET>>'
FOR CRYPTOGRAPHIC PROVIDER AzureKeyVault_EKM_Prov;


--Map the credential to a SQL login that has sysadmin permissions. This allows the SQL login to access the key vault when creating the asymmetric key in the next step.
ALTER LOGIN [SQL_Login]
ADD CREDENTIAL Azure_EKM_cred;


CREATE ASYMMETRIC KEY CONTOSO_KEY
FROM PROVIDER [AzureKeyVault_EKM_Prov]
WITH PROVIDER_KEY_NAME = 'KeyName_in_KeyVault',  --The key name here requires the key we created in the key vault
CREATION_DISPOSITION = OPEN_EXISTING;
```

### <a name="transparent-data-encryption-tde"></a>Transparentní šifrování dat (TDE)

1. Vytvořte SQL Server přihlašovací jméno, které bude používat databázový stroj pro TDE, a pak do něj přidejte přihlašovací údaje.

   ``` sql
   USE master;
   -- Create a SQL Server login associated with the asymmetric key
   -- for the Database engine to use when it loads a database
   -- encrypted by TDE.
   CREATE LOGIN EKM_Login
   FROM ASYMMETRIC KEY CONTOSO_KEY;
   GO

   -- Alter the TDE Login to add the credential for use by the
   -- Database Engine to access the key vault
   ALTER LOGIN EKM_Login
   ADD CREDENTIAL Azure_EKM_cred;
   GO
   ```

1. Vytvořte šifrovací klíč databáze, který se bude používat pro TDE.

   ``` sql
   USE ContosoDatabase;
   GO

   CREATE DATABASE ENCRYPTION KEY 
   WITH ALGORITHM = AES_128 
   ENCRYPTION BY SERVER ASYMMETRIC KEY CONTOSO_KEY;
   GO

   -- Alter the database to enable transparent data encryption.
   ALTER DATABASE ContosoDatabase
   SET ENCRYPTION ON;
   GO
   ```

### <a name="encrypted-backups"></a>Šifrovaná zálohování

1. Vytvořte SQL Server přihlašovací jméno, které má databázový stroj použít k šifrování záloh, a přidejte do něj přihlašovací údaje.

   ``` sql
   USE master;
   -- Create a SQL Server login associated with the asymmetric key
   -- for the Database engine to use when it is encrypting the backup.
   CREATE LOGIN EKM_Login
   FROM ASYMMETRIC KEY CONTOSO_KEY;
   GO

   -- Alter the Encrypted Backup Login to add the credential for use by
   -- the Database Engine to access the key vault
   ALTER LOGIN EKM_Login
   ADD CREDENTIAL Azure_EKM_cred ;
   GO
   ```

1. Zálohuje databázi, která určuje šifrování, s asymetrickým klíčem uloženým v trezoru klíčů.

   ``` sql
   USE master;
   BACKUP DATABASE [DATABASE_TO_BACKUP]
   TO DISK = N'[PATH TO BACKUP FILE]'
   WITH FORMAT, INIT, SKIP, NOREWIND, NOUNLOAD,
   ENCRYPTION(ALGORITHM = AES_256, SERVER ASYMMETRIC KEY = [CONTOSO_KEY]);
   GO
   ```

### <a name="column-level-encryption-cle"></a>Šifrování na úrovni sloupce (CLE)

Tento skript vytvoří symetrický klíč chráněný pomocí asymetrického klíče v trezoru klíčů a potom použije symetrický klíč k šifrování dat v databázi.

``` sql
CREATE SYMMETRIC KEY DATA_ENCRYPTION_KEY
WITH ALGORITHM=AES_256
ENCRYPTION BY ASYMMETRIC KEY CONTOSO_KEY;

DECLARE @DATA VARBINARY(MAX);

--Open the symmetric key for use in this session
OPEN SYMMETRIC KEY DATA_ENCRYPTION_KEY
DECRYPTION BY ASYMMETRIC KEY CONTOSO_KEY;

--Encrypt syntax
SELECT @DATA = ENCRYPTBYKEY(KEY_GUID('DATA_ENCRYPTION_KEY'), CONVERT(VARBINARY,'Plain text data to encrypt'));

-- Decrypt syntax
SELECT CONVERT(VARCHAR, DECRYPTBYKEY(@DATA));

--Close the symmetric key
CLOSE SYMMETRIC KEY DATA_ENCRYPTION_KEY;
```

## <a name="additional-resources"></a>Další zdroje

Další informace o tom, jak tyto funkce šifrování používat, najdete v tématu [použití funkce EKM s SQL Server funkcemi šifrování](https://msdn.microsoft.com/library/dn198405.aspx#UsesOfEKM).

Všimněte si, že kroky v tomto článku předpokládají, že už máte SQL Server spuštěnou na virtuálním počítači Azure. Pokud ne, přečtěte si téma [zřízení virtuálního počítače s SQL Server v Azure](../articles/azure-sql/virtual-machines/windows/create-sql-vm-portal.md). Další pokyny týkající se spuštění SQL Server na virtuálních počítačích Azure najdete v tématu [SQL Server na azure Virtual Machines přehled](../articles/azure-sql/virtual-machines/windows/sql-server-on-azure-vm-iaas-what-is-overview.md).
