## <a name="next-steps"></a>Další postup

Po povolení Azure Key Vault integrace, můžete povolit šifrování SQL serveru na virtuální počítač SQL. Nejprve musíte vytvořit asymetrický klíč v trezoru klíčů a symetrický klíč v rámci systému SQL Server na vašem virtuálním počítači. Potom bude možné provést T-SQL příkazy pro zapnutí šifrování pro databáze a zálohování.

Existuje několik typů šifrování, které můžete využít výhod:

* [Transparentní šifrování dat (šifrování TDE)](https://msdn.microsoft.com/library/bb934049.aspx)
* [Šifrované zálohování](https://msdn.microsoft.com/library/dn449489.aspx)
* [Šifrování na úrovni sloupce (Vymazat)](https://msdn.microsoft.com/library/ms173744.aspx)

Tyto skripty jazyka Transact-SQL zadejte příklady pro každé z těchto oblastí.

### <a name="prerequisites-for-examples"></a>Předpoklady pro příklady

Každý příklad vychází z dva požadavky: asymetrického klíče z trezoru klíčů názvem **CONTOSO_KEY** pověření vytvořené funkci Integrace se službou AZURE s názvem **Azure_EKM_TDE_cred**. Tyto požadavky pro spuštění příkladů instalačního programu následující příkazy jazyka Transact-SQL.

``` sql
USE master;
GO

--create credential
--The <<SECRET>> here requires the <Application ID> (without hyphens) and <Secret> to be passed together without a space between them.
CREATE CREDENTIAL sysadmin_ekm_cred
    WITH IDENTITY = 'keytestvault', --keyvault
    SECRET = '<<SECRET>>'
FOR CRYPTOGRAPHIC PROVIDER AzureKeyVault_EKM_Prov;


--Map the credential to a SQL login that have sysadmin permissions, this will allows the SQL login to access the key vault when creating the asymmetric key in the next step.
ALTER LOGIN [SQL_Login]
ADD CREDENTIAL sysadmin_ekm_cred;


CREATE ASYMMETRIC KEY CONTOSO_KEY
FROM PROVIDER [AzureKeyVault_EKM_Prov]
WITH PROVIDER_KEY_NAME = 'KeyName_in_KeyVault',  --The key name here requires the key we created in the key vault
CREATION_DISPOSITION = OPEN_EXISTING;
```

### <a name="transparent-data-encryption-tde"></a>Transparentní šifrování dat (šifrování TDE)

1. Vytvořit přihlášení systému SQL Server, který má být používána databázový stroj pro šifrování TDE a pak do ní přidejte přihlašovací údaje.

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

1. Vytvořte šifrovací klíč databáze, který se použije pro šifrování TDE.

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

1. Vytvořit přihlášení systému SQL Server, který má být používána databázový stroj pro šifrování záloh a do něj přidat přihlašovací údaje.

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

1. Zálohování databáze zadat šifrování s asymetrického klíče uložené v trezoru klíčů.

   ``` sql
   USE master;
   BACKUP DATABASE [DATABASE_TO_BACKUP]
   TO DISK = N'[PATH TO BACKUP FILE]'
   WITH FORMAT, INIT, SKIP, NOREWIND, NOUNLOAD,
   ENCRYPTION(ALGORITHM = AES_256, SERVER ASYMMETRIC KEY = [CONTOSO_KEY]);
   GO
   ```

### <a name="column-level-encryption-cle"></a>Šifrování na úrovni sloupce (Vymazat)

Tento skript vytvoří symetrického klíče chráněné pomocí asymetrického klíče v trezoru klíčů a potom pomocí symetrický klíč k šifrování dat v databázi.

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

## <a name="additional-resources"></a>Další materiály

Další informace o tom, jak používat tyto funkce šifrování najdete v tématu [pomocí EKM pomocí funkcí šifrování SQL serveru](https://msdn.microsoft.com/library/dn198405.aspx#UsesOfEKM).

Všimněte si, že postup v tomto článku předpokládá, že už máte SQL Server běžící na virtuálním počítači Azure. Pokud ne, najdete v části [zřízení virtuálního počítače s SQL serverem v Azure](../articles/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision.md). Další pokyny k systémem SQL Server na virtuálních počítačích Azure, najdete v části [SQL Server na virtuálních počítačích Azure přehled](../articles/virtual-machines/windows/sql/virtual-machines-windows-sql-server-iaas-overview.md).
