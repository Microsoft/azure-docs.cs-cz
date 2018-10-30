---
author: rothja
ms.service: virtual-machines-sql
ms.topic: include
ms.date: 10/26/2018
ms.author: jroth
ms.openlocfilehash: 22f16a7382cb0fe1f3fe2a6ef5e7c00a6989623c
ms.sourcegitcommit: 6e09760197a91be564ad60ffd3d6f48a241e083b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/29/2018
ms.locfileid: "50226634"
---
## <a name="next-steps"></a>Další postup

Po povolení integrace se službou Azure Key Vault, můžete povolit šifrování SQL serveru na virtuálním počítači SQL. Nejprve je potřeba vytvořit asymetrického klíče v trezoru klíčů a symetrický klíč v rámci SQL serveru na vašem virtuálním počítači. Potom budete moci být prováděny příkazy jazyka T-SQL povolit šifrování pro databáze a zálohy.

Existuje několik typů šifrování, které můžete využít výhod:

* [Transparentní šifrování dat (TDE)](https://msdn.microsoft.com/library/bb934049.aspx)
* [Šifrované zálohování](https://msdn.microsoft.com/library/dn449489.aspx)
* [Šifrování na úrovni sloupce (Vymazat)](https://msdn.microsoft.com/library/ms173744.aspx)

Následující skripty jazyka Transact-SQL najdete příklady pro každý z těchto oblastí.

### <a name="prerequisites-for-examples"></a>Předpoklady pro příklady

Každý příklad je založen na dva požadavky: volá asymetrického klíče z trezoru klíčů **CONTOSO_KEY** a přihlašovací údaje vytvořené funkci Integrace se službou AZURE s názvem **Azure_EKM_TDE_cred**. Tyto požadavky pro spuštění příkladů instalačního programu následující příkazy jazyka Transact-SQL.

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

1. Vytvořit přihlášení systému SQL Server databázového stroje používané pro transparentní šifrování dat a pak přidejte přihlašovací údaje, které k němu.

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

1. Vytvořte šifrovací klíč databáze, který se použije pro TDE.

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

### <a name="encrypted-backups"></a>Šifrované zálohování

1. Vytvořit přihlášení systému SQL Server pro databázový stroj pro šifrování záloh a přidejte přihlašovací údaje, které do ní.

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

1. Zálohování databáze zadat šifrování s asymetrický klíč uložený ve službě key vault.

   ``` sql
   USE master;
   BACKUP DATABASE [DATABASE_TO_BACKUP]
   TO DISK = N'[PATH TO BACKUP FILE]'
   WITH FORMAT, INIT, SKIP, NOREWIND, NOUNLOAD,
   ENCRYPTION(ALGORITHM = AES_256, SERVER ASYMMETRIC KEY = [CONTOSO_KEY]);
   GO
   ```

### <a name="column-level-encryption-cle"></a>Šifrování na úrovni sloupce (Vymazat)

Tento skript vytvoří symetrický klíč chráněný architekturou asymetrického klíče v trezoru klíčů a potom pomocí symetrický klíč k šifrování dat v databázi.

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

## <a name="additional-resources"></a>Další zdroje informací:

Další informace o tom, jak používat tyto funkce šifrování najdete v tématu [pomocí EKM s funkcí SQL Server šifrování](https://msdn.microsoft.com/library/dn198405.aspx#UsesOfEKM).

Všimněte si, že kroky v tomto článku předpokládají, že již máte SQL Server běžící na virtuálním počítači Azure. Pokud ne, přečtěte si téma [zřízení virtuálního počítače s SQL serverem v Azure](../articles/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision.md). Další doprovodné materiály o spouštění systému SQL Server na virtuálních počítačích Azure najdete v části [systému SQL Server na Azure Virtual Machines – přehled](../articles/virtual-machines/windows/sql/virtual-machines-windows-sql-server-iaas-overview.md).
