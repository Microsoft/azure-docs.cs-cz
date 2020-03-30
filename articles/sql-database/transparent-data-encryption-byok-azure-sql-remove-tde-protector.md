---
title: Odebrat ochranu TDE - PowerShell
description: Návod pro reakci na potenciálně ohrožené ochrany TDE pro Azure SQL Database nebo datový sklad pomocí TDE s podporou Bring YOur Own Key (BYOK).
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
author: jaszymas
ms.author: jaszymas
ms.reviewer: vanto
ms.date: 02/24/2020
ms.openlocfilehash: 5a89c3f7d52c5717b902a69e9c64b3fcc422c481
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80067198"
---
# <a name="remove-a-transparent-data-encryption-tde-protector-using-powershell"></a>Odebrání ochrany transparentního šifrování dat (TDE) pomocí prostředí PowerShell

## <a name="prerequisites"></a>Požadavky

- Musíte mít předplatné Azure a být správcem tohoto předplatného
- Azure PowerShell musí být nainstalovaný a spuštěný.
- Tento návod předpokládá, že už používáte klíč z Azure Key Vault jako ochrana TDE pro Azure SQL Database nebo datového skladu. Další informace najdete [v tématu Transparentní šifrování dat pomocí podpory BYOK.](transparent-data-encryption-byok-azure-sql.md)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

 Pokyny k instalaci modulu Az najdete v tématu věnovaném [instalaci Azure PowerShellu](/powershell/azure/install-az-ps). Konkrétní rutiny najdete v tématu [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/).

> [!IMPORTANT]
> Modul Správce prostředků Azure (RM) prostředí PowerShell je stále podporovaný službou Azure SQL Database, ale veškerý budoucí vývoj je pro modul Az.Sql. Modul AzureRM bude nadále dostávat opravy chyb nejméně do prosince 2020.  Argumenty pro příkazy v modulu Az a v modulech AzureRm jsou v podstatě identické. Další informace o jejich kompatibilitě [najdete v tématu Představení nového modulu Azure PowerShell Az](/powershell/azure/new-azureps-module-az).

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Informace o instalaci najdete [v tématu Instalace příkazového příkazového příkazu k webu Azure](/cli/azure/install-azure-cli).

* * *

## <a name="overview"></a>Přehled

Tento návod popisuje, jak reagovat na potenciálně ohroženou ochranu TDE pro Azure SQL Database nebo datový sklad, který používá TDE s klíči spravovanými zákazníky v azure key vaultu – přineste si vlastní klíč (BYOK) podporu. Další informace o podpoře BYOK pro TDE naleznete na [stránce s přehledem](transparent-data-encryption-byok-azure-sql.md).

Následující postupy by měly být prováděny pouze v extrémních případech nebo ve zkušebních prostředích. Pečlivě si přečtěte návod, jak to udělat, protože odstranění aktivně používaných ochrannek TDE z trezoru klíčů Azure vault bude mít za následek **nedostupnost databáze**.

Pokud je někdy podezření na ohrožení zabezpečení klíče, takže služba nebo uživatel měl neoprávněný přístup ke klíči, je nejlepší klíč odstranit.

Mějte na paměti, že jakmile je ochrana TDE odstraněna v trezoru klíčů, za 10 minut všechny šifrované databáze začnou odepřít všechna připojení s odpovídající chybovou zprávou a změnit jeho stav na [Nepřístupný](https://docs.microsoft.com/azure/sql-database/transparent-data-encryption-byok-azure-sql#inaccessible-tde-protector).

Následující kroky popisují, jak zkontrolovat kryptografické otisky Ochrany TDE, které jsou stále používány virtuálními soubory protokolu (VLF) dané databáze.
Kryptografický otisk aktuální ochrany TDE databáze a ID databáze lze nalézt spuštěním:

```sql
SELECT [database_id],
       [encryption_state],
       [encryptor_type], /*asymmetric key means AKV, certificate means service-managed keys*/
       [encryptor_thumbprint],
 FROM [sys].[dm_database_encryption_keys]
```

Následující dotaz vrátí VLFs a encryptor příslušné kryptografické otisky v provozu. Každý jiný kryptografický otisk odkazuje na jiný klíč v trezoru klíčů Azure (AKV):

```sql
SELECT * FROM sys.dm_db_log_info (database_id)
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Příkaz PowerShell **Get-AzureRmSqlServerKeyVaultKey** poskytuje kryptografický otisk ochrany TDE použité v dotazu, takže můžete vidět, které klíče chcete zachovat a které klíče odstranit v AKV. Z úložiště klíčů Azure Key Vault lze bezpečně odstranit pouze klíče, které databáze nepoužívá.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

PowerShell příkaz **az sql server klíč show** poskytuje kryptografický otisk TDE Protector použité v dotazu, takže můžete vidět, které klíče zachovat a které klíče odstranit v AKV. Z úložiště klíčů Azure Key Vault lze bezpečně odstranit pouze klíče, které databáze nepoužívá.

* * *

Tento návod přejde přes dva přístupy v závislosti na požadovaném výsledku po reakci na incident:

- Pokud je databáze Azure SQL / Datové sklady **přístupné**
- **Znepřístupnění** databází Azure SQL / datových skladů

## <a name="to-keep-the-encrypted-resources-accessible"></a>Chcete-li zachovat šifrované prostředky přístupné

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Vytvořte [nový klíč v trezoru klíčů](/powershell/module/az.keyvault/add-azkeyvaultkey). Ujistěte se, že tento nový klíč je vytvořen v samostatnétrezoru klíčů z potenciálně ohrožené ochrany TDE, protože řízení přístupu je zřízena na úrovni trezoru.

2. Přidejte nový klíč na server pomocí rutin [Add-AzSqlServerKeyVaultKey](/powershell/module/az.sql/add-azsqlserverkeyvaultkey) a [Set-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) a aktualizujte jej jako novou ochranu TDE serveru.

   ```powershell
   # add the key from Key Vault to the server  
   Add-AzSqlServerKeyVaultKey -ResourceGroupName <SQLDatabaseResourceGroupName> -ServerName <LogicalServerName> -KeyId <KeyVaultKeyId>

   # set the key as the TDE protector for all resources under the server
   Set-AzSqlServerTransparentDataEncryptionProtector -ResourceGroupName <SQLDatabaseResourceGroupName> `
       -ServerName <LogicalServerName> -Type AzureKeyVault -KeyId <KeyVaultKeyId>
   ```

3. Ujistěte se, že server a všechny repliky byly aktualizovány na nové ochrany TDE pomocí [Rutina Get-AzSqlServerTransparentDataEncryptionProtector.](/powershell/module/az.sql/get-azsqlservertransparentdataencryptionprotector)

   > [!NOTE]
   > Může trvat několik minut pro nové ochrany TDE šířit do všech databází a sekundární databáze pod serverem.

   ```powershell
   Get-AzSqlServerTransparentDataEncryptionProtector -ServerName <LogicalServerName> -ResourceGroupName <SQLDatabaseResourceGroupName>
   ```

4. Záloha [nového klíče](/powershell/module/az.keyvault/backup-azkeyvaultkey) v trezoru klíčů.

   ```powershell
   # -OutputFile parameter is optional; if removed, a file name is automatically generated.
   Backup-AzKeyVaultKey -VaultName <KeyVaultName> -Name <KeyVaultKeyName> -OutputFile <DesiredBackupFilePath>
   ```

5. Odstraňte kompromitovaný klíč z trezoru klíčů pomocí rutiny [Remove-AzKeyVaultKey.](/powershell/module/az.keyvault/remove-azkeyvaultkey)

   ```powershell
   Remove-AzKeyVaultKey -VaultName <KeyVaultName> -Name <KeyVaultKeyName>
   ```

6. Chcete-li obnovit klíč k trezoru klíčů v budoucnu pomocí [rutiny Obnovit azKeyVaultKey:](/powershell/module/az.keyvault/restore-azkeyvaultkey)

   ```powershell
   Restore-AzKeyVaultKey -VaultName <KeyVaultName> -InputFile <BackupFilePath>
   ```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Odkaz na příkaz najdete v tématu [Azure CLI keyvault](/cli/azure/keyvault/key).

1. Vytvořte [nový klíč v trezoru klíčů](/cli/azure/keyvault/key#az-keyvault-key-create). Ujistěte se, že tento nový klíč je vytvořen v samostatnétrezoru klíčů z potenciálně ohrožené ochrany TDE, protože řízení přístupu je zřízena na úrovni trezoru.

2. Přidejte nový klíč na server a aktualizujte jej jako nový server TDE chránič.

   ```azurecli
   # add the key from Key Vault to the server  
   az sql server key create --kid <KeyVaultKeyId> --resource-group <SQLDatabaseResourceGroupName> --server <LogicalServerName>

   # set the key as the TDE protector for all resources under the server
   az sql server tde-key set --server-key-type AzureKeyVault --kid <KeyVaultKeyId> --resource-group <SQLDatabaseResourceGroupName> --server <LogicalServerName>
   ```

3. Ujistěte se, že server a všechny repliky byly aktualizovány na novou ochranu TDE.

   > [!NOTE]
   > Může trvat několik minut pro nové ochrany TDE šířit do všech databází a sekundární databáze pod serverem.

   ```azurecli
   az sql server tde-key show --resource-group <SQLDatabaseResourceGroupName> --server <LogicalServerName>
   ```

4. Záloha nového klíče v trezoru klíčů.

   ```azurecli
   # --file parameter is optional; if removed, a file name is automatically generated.
   az keyvault key backup --file <DesiredBackupFilePath> --name <KeyVaultKeyName> --vault-name <KeyVaultName>
   ```

5. Odstraňte kompromitovaný klíč z trezoru klíčů.

   ```azurecli
   az keyvault key delete --name <KeyVaultKeyName> --vault-name <KeyVaultName>
   ```

6. Chcete-li obnovit klíč k trezoru klíčů v budoucnu.

   ```azurecli
   az keyvault key restore --file <BackupFilePath> --vault-name <KeyVaultName>
   ```

* * *

## <a name="to-make-the-encrypted-resources-inaccessible"></a>Znepřístupnění šifrovaných prostředků

1. Přetáhněte databáze, které jsou šifrovány potenciálně ohrožený klíč.

   Soubory databáze a protokolu jsou automaticky zálohovány, takže obnovení databáze v daném okamžiku lze provést v libovolném bodě (pokud zadáte klíč). Databáze musí být vynechány před odstraněním aktivní ochrany TDE zabránit potenciální ztrátě dat až 10 minut nejnovější transakce.

2. Záloha je klíčovým materiálem ochrany TDE v trezoru klíčů.
3. Odebrání potenciálně ohroženého klíče z trezoru klíčů

[!INCLUDE [sql-database-akv-permission-delay](includes/sql-database-akv-permission-delay.md)]

## <a name="next-steps"></a>Další kroky

- Naučte se otáčet ochranu TDE serveru tak, aby splňovala požadavky na zabezpečení: [Otočení ochrany transparentního šifrování dat pomocí prostředí PowerShell](transparent-data-encryption-byok-azure-sql-key-rotation.md)
- Začínáme s podporou Přineste si vlastní klíč pro TDE: [Zapněte TDE pomocí vlastního klíče z trezoru klíčů pomocí prostředí PowerShell](transparent-data-encryption-byok-azure-sql-configure.md)
