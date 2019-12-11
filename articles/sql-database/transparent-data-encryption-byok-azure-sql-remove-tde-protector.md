---
title: Odebrání ochrany TDE – PowerShell
description: Návod, jak reagovat na potenciálně ohrožené ochrany TDE pro Azure SQL Database nebo datový sklad pomocí TDE s podporou BYOK (Přineste si vlastní klíč).
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
author: jaszymas
ms.author: jaszymas
ms.reviewer: vanto
ms.date: 03/12/2019
ms.openlocfilehash: d4c9b926afe93f52946c5f1adf40835f72812f2a
ms.sourcegitcommit: d614a9fc1cc044ff8ba898297aad638858504efa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/10/2019
ms.locfileid: "74995818"
---
# <a name="remove-a-transparent-data-encryption-tde-protector-using-powershell"></a>Odebrání ochrany transparentní šifrování dat (TDE) pomocí prostředí PowerShell

## <a name="prerequisites"></a>Předpoklady

- Musíte mít předplatné Azure a mít oprávnění správce k tomuto předplatnému.
- Musíte mít nainstalovanou a spuštěnou Azure PowerShell.
- V tomto průvodci se předpokládá, že už používáte klíč z Azure Key Vault jako ochranu TDE pro Azure SQL Database nebo datový sklad. Další informace najdete v tématu [transparentní šifrování dat s podporou BYOK](transparent-data-encryption-byok-azure-sql.md) .

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

 Pokyny k instalaci modulu Az najdete v tématu věnovaném [instalaci Azure PowerShellu](/powershell/azure/install-az-ps). Konkrétní rutiny naleznete v tématu [AzureRM. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/).

> [!IMPORTANT]
> Modul Azure Resource Manager PowerShellu (RM) je stále podporován Azure SQL Database, ale všechny budoucí vývojové prostředí jsou k dispozici pro modul AZ. SQL. V modulu AzureRM bude i nadále docházet k opravám chyb až do prosince 2020.  Argumenty pro příkazy v modulech AZ a v modulech AzureRm jsou v podstatě identické. Další informace o kompatibilitě najdete v tématu [představení nového Azure PowerShell AZ Module](/powershell/azure/new-azureps-module-az).

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

Informace o instalaci najdete v tématu Instalace rozhraní příkazového [řádku Azure CLI](/cli/azure/install-azure-cli).

* * *

## <a name="overview"></a>Přehled

Tato příručka popisuje, jak reagovat na potenciálně ohrožené ochrany TDE pro Azure SQL Database nebo datový sklad, který používá TDE s klíči spravovanými zákazníky v podpoře BYOK (Azure Key Vault-Bring Your Own Key). Další informace o podpoře BYOK pro TDE najdete na stránce s [přehledem](transparent-data-encryption-byok-azure-sql.md).

Následující postupy by měly být provedeny pouze v extrémních případech nebo v testovacích prostředích. Projděte si příručku návody, protože odstranění aktivně používaných TDE ochran z Azure Key Vault může způsobit **ztrátu dat**.

Pokud dojde k ohrožení bezpečnosti nějakého klíče, aby služba nebo uživatel měli k klíči neoprávněný přístup, je nejlepší klíč odstranit.

Mějte na paměti, že jakmile se ochrana TDE v Key Vault odstraní, **všechna připojení k šifrovaným databázím na serveru jsou blokovaná a tyto databáze budou přecházet do režimu offline a budou se zahodit do 24 hodin**. Staré zálohy šifrované s ohroženým klíčem již nejsou přístupné.

Následující kroky popisují, jak kontrolovat kryptografické otisky ochrany TDE, které pořád používají virtuální soubory protokolů (VLF) dané databáze.
Kryptografický otisk aktuálního TDE ochrany databáze a ID databáze lze najít spuštěním:

```sql
SELECT [database_id], 
       [encryption_state], 
       [encryptor_type], /*asymmetric key means AKV, certificate means service-managed keys*/ 
       [encryptor_thumbprint], 
 FROM [sys].[dm_database_encryption_keys]
```

Následující dotaz vrátí VLFs a příslušné kryptografické otisky používané pro šifrování. Každý jiný kryptografický otisk odkazuje na jiný klíč v Azure Key Vault (integrace):

```sql
SELECT * FROM sys.dm_db_log_info (database_id)
```

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Příkaz PowerShellu **Get-AzureRmSqlServerKeyVaultKey** poskytuje kryptografický otisk ochrany TDE, která se používá v dotazu, abyste viděli, které klíče se mají zachovat a které klíče se mají odstranit v integrace. Z Azure Key Vault můžete bezpečně odstranit jenom klíče, které už nepoužívá databáze.

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

Příkaz PowerShellu **AZ SQL Server Key show** poskytuje kryptografický otisk ochrany TDE, která se používá v dotazu, abyste viděli, které klíče se mají zachovat a které klíče se mají odstranit v integrace. Z Azure Key Vault můžete bezpečně odstranit jenom klíče, které už nepoužívá databáze.

* * *

Tato příručka přechází přes dvě přístupy v závislosti na požadovaném výsledku po reakci na incident:

- Zachování **přístupu** k databázím SQL Azure a datovým skladům
- **Přístup** k databázím SQL Azure a datovým skladům

## <a name="to-keep-the-encrypted-resources-accessible"></a>Zachování přístupných šifrovaných prostředků

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

1. Vytvořte [nový klíč v Key Vault](/powershell/module/az.keyvault/add-azkeyvaultkey). Zajistěte, aby byl tento nový klíč vytvořen v jiném trezoru klíčů od potenciálně ohroženého ochrany TDE, protože řízení přístupu je zřízené na úrovni trezoru.

2. Přidejte nový klíč na server pomocí rutin [Add-AzSqlServerKeyVaultKey](/powershell/module/az.sql/add-azsqlserverkeyvaultkey) a [set-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) a aktualizujte ho jako nový TDE ochrany serveru.

   ```powershell
   # add the key from Key Vault to the server  
   Add-AzSqlServerKeyVaultKey -ResourceGroupName <SQLDatabaseResourceGroupName> -ServerName <LogicalServerName> -KeyId <KeyVaultKeyId>

   # set the key as the TDE protector for all resources under the server
   Set-AzSqlServerTransparentDataEncryptionProtector -ResourceGroupName <SQLDatabaseResourceGroupName> `
       -ServerName <LogicalServerName> -Type AzureKeyVault -KeyId <KeyVaultKeyId>
   ```

3. Zajistěte, aby byl server a všechny repliky v nové ochraně TDE pomocí rutiny [Get-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/get-azsqlservertransparentdataencryptionprotector) .

   > [!NOTE]
   > Novému stráži TDE může trvat několik minut, než se došíří do všech databází a sekundárních databází v rámci serveru.

   ```powershell
   Get-AzSqlServerTransparentDataEncryptionProtector -ServerName <LogicalServerName> -ResourceGroupName <SQLDatabaseResourceGroupName>
   ```

4. Proveďte [zálohu nového klíče](/powershell/module/az.keyvault/backup-azkeyvaultkey) v Key Vault.

   ```powershell
   # -OutputFile parameter is optional; if removed, a file name is automatically generated.
   Backup-AzKeyVaultKey -VaultName <KeyVaultName> -Name <KeyVaultKeyName> -OutputFile <DesiredBackupFilePath>
   ```

5. Odstraňte ohrožený klíč z Key Vault pomocí rutiny [Remove-AzKeyVaultKey](/powershell/module/az.keyvault/remove-azkeyvaultkey) .

   ```powershell
   Remove-AzKeyVaultKey -VaultName <KeyVaultName> -Name <KeyVaultKeyName>
   ```

6. Postup obnovení klíče pro Key Vault v budoucnu pomocí rutiny [Restore-AzKeyVaultKey](/powershell/module/az.keyvault/restore-azkeyvaultkey) :

   ```powershell
   Restore-AzKeyVaultKey -VaultName <KeyVaultName> -InputFile <BackupFilePath>
   ```

# <a name="azure-clitabazure-cli"></a>[Azure CLI](#tab/azure-cli)

Referenční informace k příkazům najdete v [trezoru klíčů Azure CLI](/cli/azure/keyvault/key).

1. Vytvořte [nový klíč v Key Vault](/cli/azure/keyvault/key#az-keyvault-key-create). Zajistěte, aby byl tento nový klíč vytvořen v jiném trezoru klíčů od potenciálně ohroženého ochrany TDE, protože řízení přístupu je zřízené na úrovni trezoru.

2. Přidejte nový klíč na server a aktualizujte ho jako nové ochranu TDE serveru.

   ```powershell
   # add the key from Key Vault to the server  
   az sql server key create --kid <KeyVaultKeyId> --resource-group <SQLDatabaseResourceGroupName> --server <LogicalServerName>

   # set the key as the TDE protector for all resources under the server
   az sql server tde-key set --server-key-type AzureKeyVault --kid <KeyVaultKeyId> --resource-group <SQLDatabaseResourceGroupName> --server <LogicalServerName>
   ```

3. Ujistěte se, že se server a všechny repliky aktualizovaly do nového ochrany TDE.

   > [!NOTE]
   > Novému stráži TDE může trvat několik minut, než se došíří do všech databází a sekundárních databází v rámci serveru.

   ```powershell
   az sql server tde-key show --resource-group <SQLDatabaseResourceGroupName> --server <LogicalServerName>
   ```

4. Proveďte zálohu nového klíče v Key Vault.

   ```powershell
   # --file parameter is optional; if removed, a file name is automatically generated.
   az keyvault key backup --file <DesiredBackupFilePath> --name <KeyVaultKeyName> --vault-name <KeyVaultName>
   ```

5. Odstraňte ohrožený klíč z Key Vault.

   ```powershell
   az keyvault key delete --name <KeyVaultKeyName> --vault-name <KeyVaultName>
   ```

6. K obnovení klíče pro Key Vault v budoucnu.

   ```powershell
   az keyvault key restore --file <BackupFilePath> --vault-name <KeyVaultName>
   ```

* * *

## <a name="to-make-the-encrypted-resources-inaccessible"></a>Zpřístupnění šifrovaných prostředků jako nepřístupné

1. Vyřaďte databáze šifrované potenciálně ohroženým klíčem.

   Databáze a soubory protokolu jsou automaticky zálohovány, takže obnovení databáze v určitém bodě v čase lze provést kdykoli (Pokud klíč zadáte). Databáze musí být před odstraněním ochrany aktivních TDE vyřazeny, aby se předešlo potenciální ztrátě dat až do 10 minut od poslední transakce.

2. Zazálohujte klíčový materiál ochrany TDE v Key Vault.
3. Odebrat potenciálně ohrožený klíč z Key Vault

## <a name="next-steps"></a>Další kroky

- Naučte se, jak otočit ochranu TDE serveru tak, aby splňovala požadavky na zabezpečení: [otočení transparentní šifrování dat ochrany pomocí PowerShellu](transparent-data-encryption-byok-azure-sql-key-rotation.md)
- Začínáme s podporou Bring Your Own Key TDE: [Zapnutí TDE pomocí vlastního klíče z Key Vault pomocí PowerShellu](transparent-data-encryption-byok-azure-sql-configure.md)
