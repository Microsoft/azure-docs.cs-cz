---
title: Odebrání ochrany TDE (PowerShell & rozhraní příkazového řádku Azure)
titleSuffix: Azure SQL Database & Azure Synapse Analytics
description: Přečtěte si, jak reagovat na potenciálně ohroženou ochranu TDE ochrany proti Azure SQL Database nebo Azure synapse Analytics s využitím TDE s podporou funkce Přineste si vlastní klíč (BYOK).
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: seo-lt-2019 sqldbrb=1, devx-track-azurecli
ms.devlang: ''
ms.topic: how-to
author: jaszymas
ms.author: jaszymas
ms.reviewer: vanto
ms.date: 02/24/2020
ms.openlocfilehash: 31298be4d50c7f562e2e2b9adbf889b165b197e5
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/01/2020
ms.locfileid: "96461858"
---
# <a name="remove-a-transparent-data-encryption-tde-protector-using-powershell"></a>Odebrání ochrany transparentní šifrování dat (TDE) pomocí prostředí PowerShell
[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]


Toto téma popisuje, jak reagovat na potenciálně ohroženou TDE ochranu pro Azure SQL Database nebo Azure synapse Analytics, která používá TDE s klíči spravovanými zákazníky v podpoře Azure Key Vault-Bring Your Own Key (BYOK). Další informace o podpoře BYOK pro TDE najdete na stránce s [přehledem](transparent-data-encryption-byok-overview.md).

> [!CAUTION]
> Postupy popsané v tomto článku by měly být provedeny pouze v extrémních případech nebo v testovacích prostředích. Projděte si pečlivě tyto kroky, protože odstranění aktivně používaných TDE ochran z Azure Key Vault způsobí, že **databáze nebude k dispozici**.

Pokud dojde k ohrožení bezpečnosti nějakého klíče, aby služba nebo uživatel měli k klíči neoprávněný přístup, je nejlepší klíč odstranit.

Mějte na paměti, že jakmile se ochrana TDE odstraní v Key Vault až do 10 minut, začnou všechny šifrované databáze zamítnout všechna připojení k příslušné chybové zprávě a změnit její stav na [nepřístupný](./transparent-data-encryption-byok-overview.md#inaccessible-tde-protector).

Tato příručka přechází přes dvě přístupy v závislosti na požadovaném výsledku po napadené reakci na incidenty:

- Aby databáze v Azure SQL Database/Azure synapse Analytics byly **nedostupné**.
- Aby databáze v Azure SQL Database nebo Azure Azure synapse Analytics **nepřístupné**.

## <a name="prerequisites"></a>Předpoklady

- Musíte mít předplatné Azure a mít oprávnění správce k tomuto předplatnému.
- Musíte mít nainstalovanou a spuštěnou Azure PowerShell.
- V tomto průvodci se předpokládá, že už používáte klíč z Azure Key Vault jako ochranu TDE pro Azure SQL Database nebo Azure synapse. Další informace najdete v tématu [transparentní šifrování dat s podporou BYOK](transparent-data-encryption-byok-overview.md) .

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

 Pokyny k instalaci modulu Az najdete v tématu věnovaném [instalaci Azure PowerShellu](/powershell/azure/install-az-ps). Konkrétní rutiny naleznete v tématu [AzureRM. SQL](/powershell/module/AzureRM.Sql/).

> [!IMPORTANT]
> Modul Azure Resource Manager PowerShellu (RM) se pořád podporuje, ale všechny budoucí vývojové prostředí jsou k dispozici pro modul AZ. SQL. V modulu AzureRM bude i nadále docházet k opravám chyb až do prosince 2020.  Argumenty pro příkazy v modulech AZ a v modulech AzureRm jsou v podstatě identické. Další informace o kompatibilitě najdete v tématu [představení nového Azure PowerShell AZ Module](/powershell/azure/new-azureps-module-az).

# <a name="the-azure-cli"></a>[Azure CLI](#tab/azure-cli)

Informace o instalaci najdete v tématu [instalace rozhraní příkazového řádku Azure CLI](/cli/azure/install-azure-cli).

* * *

## <a name="check-tde-protector-thumbprints"></a>Kontrolovat kryptografické otisky ochrany TDE

Následující kroky popisují, jak kontrolovat kryptografické otisky ochrany TDE, které pořád používají virtuální soubory protokolů (VLF) dané databáze.
Kryptografický otisk aktuálního TDE ochrany databáze a ID databáze lze najít spuštěním:

```sql
SELECT [database_id],
       [encryption_state],
       [encryptor_type], /*asymmetric key means AKV, certificate means service-managed keys*/
       [encryptor_thumbprint]
 FROM [sys].[dm_database_encryption_keys]
```

Následující dotaz vrátí VLFs a používané kryptografické otisky TDE ochrany. Každý jiný kryptografický otisk odkazuje na jiný klíč v Azure Key Vault (integrace):

```sql
SELECT * FROM sys.dm_db_log_info (database_id)
```

Alternativně můžete použít PowerShell nebo rozhraní příkazového řádku Azure:

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Příkaz PowerShellu **Get-AzureRmSqlServerKeyVaultKey**   poskytuje kryptografický otisk ochrany TDE, která se používá v dotazu, abyste viděli, které klíče se mají zachovat a které klíče se mají odstranit v integrace. Z Azure Key Vault můžete bezpečně odstranit jenom klíče, které už nepoužívá databáze.

# <a name="the-azure-cli"></a>[Azure CLI](#tab/azure-cli)

Příkaz PowerShellu **AZ SQL Server Key show**   poskytuje kryptografický otisk ochrany TDE, která se používá v dotazu, abyste viděli, které klíče se mají zachovat a které klíče se mají odstranit v integrace. Z Azure Key Vault můžete bezpečně odstranit jenom klíče, které už nepoužívá databáze.

* * *

## <a name="keep-encrypted-resources-accessible"></a>Zachovat přístup k šifrovaným prostředkům

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

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

# <a name="the-azure-cli"></a>[Azure CLI](#tab/azure-cli)

Referenční informace k příkazům najdete v [trezoru klíčů Azure CLI](/cli/azure/keyvault/key).

1. Vytvořte [nový klíč v Key Vault](/cli/azure/keyvault/key#az-keyvault-key-create). Zajistěte, aby byl tento nový klíč vytvořen v jiném trezoru klíčů od potenciálně ohroženého ochrany TDE, protože řízení přístupu je zřízené na úrovni trezoru.

2. Přidejte nový klíč na server a aktualizujte ho jako nový ochrana TDE serveru.

   ```azurecli
   # add the key from Key Vault to the server  
   az sql server key create --kid <KeyVaultKeyId> --resource-group <SQLDatabaseResourceGroupName> --server <LogicalServerName>

   # set the key as the TDE protector for all resources under the server
   az sql server tde-key set --server-key-type AzureKeyVault --kid <KeyVaultKeyId> --resource-group <SQLDatabaseResourceGroupName> --server <LogicalServerName>
   ```

3. Ujistěte se, že se server a všechny repliky aktualizovaly do nového ochrany TDE.

   > [!NOTE]
   > Novému stráži TDE může trvat několik minut, než se došíří do všech databází a sekundárních databází v rámci serveru.

   ```azurecli
   az sql server tde-key show --resource-group <SQLDatabaseResourceGroupName> --server <LogicalServerName>
   ```

4. Proveďte zálohu nového klíče v Key Vault.

   ```azurecli
   # --file parameter is optional; if removed, a file name is automatically generated.
   az keyvault key backup --file <DesiredBackupFilePath> --name <KeyVaultKeyName> --vault-name <KeyVaultName>
   ```

5. Odstraňte ohrožený klíč z Key Vault.

   ```azurecli
   az keyvault key delete --name <KeyVaultKeyName> --vault-name <KeyVaultName>
   ```

6. K obnovení klíče pro Key Vault v budoucnu.

   ```azurecli
   az keyvault key restore --file <BackupFilePath> --vault-name <KeyVaultName>
   ```

* * *

## <a name="make-encrypted-resources-inaccessible"></a>Předávat šifrované prostředky jako nedostupné

1. Vyřaďte databáze šifrované potenciálně ohroženým klíčem.

   Databáze a soubory protokolu jsou automaticky zálohovány, takže obnovení databáze v určitém bodě v čase lze provést kdykoli (Pokud klíč zadáte). Databáze musí být před odstraněním ochrany aktivních TDE vyřazeny, aby se předešlo potenciální ztrátě dat až do 10 minut od poslední transakce.

2. Zazálohujte klíčový materiál ochrany TDE v Key Vault.
3. Odebrat potenciálně ohrožený klíč z Key Vault

[!INCLUDE [sql-database-akv-permission-delay](../includes/sql-database-akv-permission-delay.md)]

## <a name="next-steps"></a>Další kroky

- Naučte se, jak otočit ochranu TDE serveru tak, aby splňovala požadavky na zabezpečení: [otočení transparentní šifrování dat ochrany pomocí PowerShellu](transparent-data-encryption-byok-key-rotation.md)
- Začínáme s podporou Bring Your Own Key TDE: [Zapnutí TDE pomocí vlastního klíče z Key Vault pomocí PowerShellu](transparent-data-encryption-byok-configure.md)