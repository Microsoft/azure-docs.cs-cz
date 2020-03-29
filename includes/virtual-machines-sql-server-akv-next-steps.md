---
author: rothja
ms.service: virtual-machines-sql
ms.topic: include
ms.date: 10/26/2018
ms.author: jroth
ms.openlocfilehash: 22f16a7382cb0fe1f3fe2a6ef5e7c00a6989623c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "67174957"
---
## <a name="next-steps"></a>Další kroky

Po povolení integrace úložiště klíčů Azure můžete povolit šifrování serveru SQL Server na vašem virtuálním počítači SQL. Nejprve budete muset vytvořit asymetrický klíč uvnitř trezoru klíčů a symetrický klíč v rámci SQL Server na vašem virtuálním počítači. Potom budete moci spouštět příkazy T-SQL, abyste povolili šifrování pro vaše databáze a zálohy.

Existuje několik forem šifrování, které můžete využít:

* [Transparentní šifrování dat (TDE)](https://msdn.microsoft.com/library/bb934049.aspx)
* [Šifrované zálohy](https://msdn.microsoft.com/library/dn449489.aspx)
* [Šifrování na úrovni sloupce (CLE)](https://msdn.microsoft.com/library/ms173744.aspx)

Následující skripty Transact-SQL poskytují příklady pro každou z těchto oblastí.

### <a name="prerequisites-for-examples"></a>Předpoklady pro příklady

Každý příklad je založen na dvou požadavcích: asymetrický klíč z trezoru klíčů s názvem **CONTOSO_KEY** a pověření vytvořené funkcí integrace AKV s názvem **Azure_EKM_TDE_cred**. Následující příkazy Transact-SQL nastavit tyto předpoklady pro spuštění příklady.

``` sql
USE master;
GO

--create credential
--The <<SECRET>> here requires the <Application ID> (without hyphens) and <Secret> to be passed together without a space between them.
CREATE CREDENTIAL sysadmin_ekm_cred
    WITH IDENTITY = 'keytestvault', --keyvault
    SECRET = '<<SECRET>>'
FOR CRYPTOGRAPHIC PROVIDER AzureKeyVault_EKM_Prov;


--Map the credential to a SQL login that has sysadmin permissions. This allows the SQL login to access the key vault when creating the asymmetric key in the next step.
ALTER LOGIN [SQL_Login]
ADD CREDENTIAL sysadmin_ekm_cred;


CREATE ASYMMETRIC KEY CONTOSO_KEY
FROM PROVIDER [AzureKeyVault_EKM_Prov]
WITH PROVIDER_KEY_NAME = 'KeyName_in_KeyVault',  --The key name here requires the key we created in the key vault
CREATION_DISPOSITION = OPEN_EXISTING;
```

### <a name="transparent-data-encryption-tde"></a>Transparentní šifrování dat (TDE)

1. Vytvořte sql server přihlášení pro použití databázového stroje pro TDE a přidejte pověření k němu.

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

1. Vytvořte šifrovací klíč databáze, který bude použit pro TDE.

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

### <a name="encrypted-backups"></a>Šifrované zálohy

1. Vytvořte sql server přihlášení, které má být použito databázový stroj pro šifrování záloh a přidejte pověření k němu.

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

1. Zálohujte databázi a zadejte šifrování pomocí asymetrického klíče uloženého v trezoru klíčů.

   ``` sql
   USE master;
   BACKUP DATABASE [DATABASE_TO_BACKUP]
   TO DISK = N'[PATH TO BACKUP FILE]'
   WITH FORMAT, INIT, SKIP, NOREWIND, NOUNLOAD,
   ENCRYPTION(ALGORITHM = AES_256, SERVER ASYMMETRIC KEY = [CONTOSO_KEY]);
   GO
   ```

### <a name="column-level-encryption-cle"></a>Šifrování na úrovni sloupce (CLE)

Tento skript vytvoří symetrický klíč chráněný asymetrickým klíčem v trezoru klíčů a potom použije symetrický klíč k šifrování dat v databázi.

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

Další informace o použití těchto funkcí šifrování naleznete [v tématu Použití funkce EKM s funkcemi šifrování serveru SQL Server](https://msdn.microsoft.com/library/dn198405.aspx#UsesOfEKM).

Všimněte si, že kroky v tomto článku předpokládat, že už máte SQL Server spuštěný na virtuálním počítači Azure. Pokud ne, přečtěte si informace [o zřízení virtuálního počítače SQL Server v Azure](../articles/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision.md). Další pokyny ke spuštění SQL Serveru na virtuálních počítačích Azure najdete v tématu [SQL Server ve virtuálních počítačích Azure přehled](../articles/virtual-machines/windows/sql/virtual-machines-windows-sql-server-iaas-overview.md).
