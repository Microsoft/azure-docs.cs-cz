---
title: Povolení SQL TDE pomocí trezoru klíčů Azure
description: Zjistěte, jak nakonfigurovat Azure SQL Database a datový sklad tak, aby začaly používat transparentní šifrování dat (TDE) pro šifrování v klidovém stavu pomocí PowerShellu nebo CLI.
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
ms.openlocfilehash: 5b1c985eeec9af25ec576f4e2375c417dc376f95
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81452753"
---
# <a name="powershell-and-cli-enable-transparent-data-encryption-with-customer-managed-key-from-azure-key-vault"></a>PowerShell a CLI: Povolení transparentního šifrování dat pomocí klíče spravovaného zákazníkem z trezoru klíčů Azure

Tento článek vás provede, jak použít klíč z trezoru klíčů Azure pro transparentní šifrování dat (TDE) v databázi SQL nebo datovém skladu. Další informace o tde s azure key vault integrace – přineste si vlastní klíč (BYOK) podpora, navštivte [TDE s klientem spravované klíče v Trezoru klíčů Azure](transparent-data-encryption-byok-azure-sql.md). 

## <a name="prerequisites-for-powershell"></a>Požadavky pro Prostředí PowerShell

- Musíte mít předplatné Azure a být správcem tohoto předplatného.
- [Doporučeno, ale volitelné] Mít modul hardwarového zabezpečení (HSM) nebo místní úložiště klíčů pro vytvoření místní kopie materiálu klíče TDE Protector.
- Azure PowerShell musí být nainstalovaný a spuštěný.
- Vytvořte trezor klíčů Azure a klíč pro TDE.
  - [Pokyny pro použití modulu hardwarového zabezpečení (HSM) a trezoru klíčů](../key-vault/keys/hsm-protected-keys.md)
    - Trezor klíčů musí mít následující vlastnost, která má být použita pro TDE:
  - [ochrana proti odstranění a](../key-vault/general/overview-soft-delete.md) vymazání
- Klíč musí mít následující atributy, které mají být použity pro TDE:
   - Bez data vypršení platnosti
   - Není zakázáno
   - Schopen provádět *get*, *obtékání klíč*, *rozbalit klíčové* operace

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Pokyny k instalaci modulu Az najdete v tématu věnovaném [instalaci Azure PowerShellu](/powershell/azure/install-az-ps). Konkrétní rutiny najdete v tématu [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/).

Podrobnosti o trezoru klíčů najdete v [pokynech prostředí PowerShell z trezoru klíčů](../key-vault/secrets/quick-create-powershell.md) a [jak používat obnovitelné odstranění trezoru klíčů pomocí prostředí PowerShell](../key-vault/general/soft-delete-powershell.md).

> [!IMPORTANT]
> Modul Správce prostředků Azure (RM) prostředí PowerShell je stále podporovaný službou Azure SQL Database, ale veškerý budoucí vývoj je pro modul Az.Sql. Modul AzureRM bude nadále dostávat opravy chyb nejméně do prosince 2020.  Argumenty pro příkazy v modulu Az a v modulech AzureRm jsou v podstatě identické. Další informace o jejich kompatibilitě [najdete v tématu Představení nového modulu Azure PowerShell Az](/powershell/azure/new-azureps-module-az).

## <a name="assign-an-azure-ad-identity-to-your-server"></a>Přiřazení identity Azure AD k serveru

Pokud máte existující server, přidejte na server identitu Azure AD pomocí následujícího:

   ```powershell
   $server = Set-AzSqlServer -ResourceGroupName <SQLDatabaseResourceGroupName> -ServerName <LogicalServerName> -AssignIdentity
   ```

Pokud vytváříte server, použijte rutinu [New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) se značkou -Identity k přidání identity Azure AD během vytváření serveru:

   ```powershell
   $server = New-AzSqlServer -ResourceGroupName <SQLDatabaseResourceGroupName> -Location <RegionName> `
       -ServerName <LogicalServerName> -ServerVersion "12.0" -SqlAdministratorCredentials <PSCredential> -AssignIdentity
   ```

## <a name="grant-key-vault-permissions-to-your-server"></a>Udělení oprávnění trezoru klíčů serveru

Pomocí rutiny [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) udělte serveru přístup k trezoru klíčů před použitím klíče z ní pro TDE.

   ```powershell
   Set-AzKeyVaultAccessPolicy -VaultName <KeyVaultName> `
       -ObjectId $server.Identity.PrincipalId -PermissionsToKeys get, wrapKey, unwrapKey
   ```

## <a name="add-the-key-vault-key-to-the-server-and-set-the-tde-protector"></a>Přidání klíče trezoru klíčů na server a nastavení ochrany TDE

- Použití rutiny [Get-AzKeyVaultKey](/powershell/module/az.keyvault/get-azkeyvaultkey?view=azps-2.4.0) k načtení ID klíče z trezoru klíčů
- Pomocí rutiny [Add-AzSqlServerKeyVaultKey](/powershell/module/az.sql/add-azsqlserverkeyvaultkey) přidejte klíč z trezoru klíčů na server.
- Pomocí rutiny [Set-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) nastavte klíč jako ochranu TDE pro všechny prostředky serveru.
- Pomocí rutiny [Get-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/get-azsqlservertransparentdataencryptionprotector) potvrďte, že ochrana TDE byla nakonfigurována tak, jak bylo zamýšleno.

> [!NOTE]
> Kombinovaná délka názvu trezoru klíčů a názvu klíče nesmí přesáhnout 94 znaků.

> [!TIP]
> Příklad KeyId z trezoru klíčů:https://contosokeyvault.vault.azure.net/keys/Key1/1a1a2b2b3c3c4d4d5e5e6f6f7g7g8h8h

```powershell
# add the key from Key Vault to the server
Add-AzSqlServerKeyVaultKey -ResourceGroupName <SQLDatabaseResourceGroupName> -ServerName <LogicalServerName> -KeyId <KeyVaultKeyId>

# set the key as the TDE protector for all resources under the server
Set-AzSqlServerTransparentDataEncryptionProtector -ResourceGroupName <SQLDatabaseResourceGroupName> -ServerName <LogicalServerName> `
   -Type AzureKeyVault -KeyId <KeyVaultKeyId>

# confirm the TDE protector was configured as intended
Get-AzSqlServerTransparentDataEncryptionProtector -ResourceGroupName <SQLDatabaseResourceGroupName> -ServerName <LogicalServerName>
```

## <a name="turn-on-tde"></a>Zapnutí TDE

Pomocí rutiny [Set-AzSqlDatabaseTransparentDataEncryption](/powershell/module/az.sql/set-azsqldatabasetransparentdataencryption) zapněte TDE.

```powershell
Set-AzSqlDatabaseTransparentDataEncryption -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> -DatabaseName <DatabaseName> -State "Enabled"
```

Nyní databáze nebo datový sklad má TDE povoleno s šifrovacíklíč v trezoru klíčů.

## <a name="check-the-encryption-state-and-encryption-activity"></a>Kontrola stavu šifrování a aktivity šifrování

Pomocí [Get-AzSqlDatabaseTransparentDataEncryption](/powershell/module/az.sql/get-azsqldatabasetransparentdataencryption) získat stav šifrování a [Get-AzSqlDatabaseTransparentDataEncryptionActivity](/powershell/module/az.sql/get-azsqldatabasetransparentdataencryptionactivity) ke kontrole průběhu šifrování pro databázi nebo datový sklad.

```powershell
# get the encryption state
Get-AzSqlDatabaseTransparentDataEncryption -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> -DatabaseName <DatabaseName> `

# check the encryption progress for a database or data warehouse
Get-AzSqlDatabaseTransparentDataEncryptionActivity -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> -DatabaseName <DatabaseName>  
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Pokud chcete nainstalovat požadované rozhraní příkazového řádku verze 2.0 nebo novější a připojit se k předplatnému Azure, přečtěte si informace [o instalaci a konfiguraci rozhraní příkazového řádku Azure pro více platforem 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli).

Podrobnosti o trezoru klíčů najdete v [tématech Správa trezoru klíčů pomocí příkazového příkazu CLI 2.0](../key-vault/general/manage-with-cli2.md) a [Jak používat obnovitelné odstranění trezoru klíčů pomocí příkazového příkazu CLI](../key-vault/general/soft-delete-cli.md).

## <a name="assign-an-azure-ad-identity-to-your-server"></a>Přiřazení identity Azure AD k serveru

```azurecli
# create server (with identity) and database
az sql server create --name <servername> --resource-group <rgname>  --location <location> --admin-user <user> --admin-password <password> --assign-identity
az sql db create --name <dbname> --server <servername> --resource-group <rgname>
```

> [!TIP]
> Zachovávejte "principalID" od vytvoření serveru, je to id objektu, který slouží k přiřazení oprávnění trezoru klíčů v dalším kroku

## <a name="grant-key-vault-permissions-to-your-server"></a>Udělení oprávnění trezoru klíčů serveru

```azurecli
# create key vault, key and grant permission
az keyvault create --name <kvname> --resource-group <rgname> --location <location> --enable-soft-delete true
az keyvault key create --name <keyname> --vault-name <kvname> --protection software
az keyvault set-policy --name <kvname>  --object-id <objectid> --resource-group <rgname> --key-permissions wrapKey unwrapKey get
```

> [!TIP]
> Zachovat identifikátor URI klíče nebo keyID nového klíče pro další krok, například:https://contosokeyvault.vault.azure.net/keys/Key1/1a1a2b2b3c3c4d4d5e5e6f6f7g7g8h8h

## <a name="add-the-key-vault-key-to-the-server-and-set-the-tde-protector"></a>Přidání klíče trezoru klíčů na server a nastavení ochrany TDE

```azurecli
# add server key and update encryption protector
az sql server key create --server <servername> --resource-group <rgname> --kid <keyID>
az sql server tde-key set --server <servername> --server-key-type AzureKeyVault  --resource-group <rgname> --kid <keyID>
```

> [!NOTE]
> Kombinovaná délka názvu trezoru klíčů a názvu klíče nesmí přesáhnout 94 znaků.

## <a name="turn-on-tde"></a>Zapnutí TDE

```azurecli
# enable encryption
az sql db tde set --database <dbname> --server <servername> --resource-group <rgname> --status Enabled
```

Nyní databáze nebo datový sklad má TDE povoleno s zákazníkem spravovaného šifrovacíklíč v Azure Key Vault.

## <a name="check-the-encryption-state-and-encryption-activity"></a>Kontrola stavu šifrování a aktivity šifrování

```azurecli
# get encryption scan progress
az sql db tde list-activity --database <dbname> --server <servername> --resource-group <rgname>  

# get whether encryption is on or off
az sql db tde show --database <dbname> --server <servername> --resource-group <rgname>
```

* * *

## <a name="useful-powershell-cmdlets"></a>Užitečné rutiny prostředí PowerShell

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

- Pomocí rutiny [Set-AzSqlDatabaseTransparentDataEncryption](/powershell/module/az.sql/set-azsqldatabasetransparentdataencryption) vypněte TDE.

   ```powershell
   Set-AzSqlDatabaseTransparentDataEncryption -ServerName <LogicalServerName> -ResourceGroupName <SQLDatabaseResourceGroupName> `
      -DatabaseName <DatabaseName> -State "Disabled"
   ```

- Pomocí rutiny [Get-AzSqlServerKeyVaultKey](/powershell/module/az.sql/get-azsqlserverkeyvaultkey) vrátíte seznam klíčů trezoru klíčů přidaných na server.

   ```powershell
   # KeyId is an optional parameter, to return a specific key version
   Get-AzSqlServerKeyVaultKey -ServerName <LogicalServerName> -ResourceGroupName <SQLDatabaseResourceGroupName>
   ```

- Pomocí [klávesy Remove-AzSqlServerKeyVaultKey Key](/powershell/module/az.sql/remove-azsqlserverkeyvaultkey) odeberte klíč trezoru klíčů ze serveru.

   ```powershell
   # the key set as the TDE Protector cannot be removed
   Remove-AzSqlServerKeyVaultKey -KeyId <KeyVaultKeyId> -ServerName <LogicalServerName> -ResourceGroupName <SQLDatabaseResourceGroupName>
   ```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

- Obecné nastavení databáze naleznete [v tématu az sql](/cli/azure/sql).

- Nastavení klíčů trezoru naleznete [v tématu az sql server key](/cli/azure/sql/server/key).

- Nastavení TDE naleznete [v tématech tde-key a](/cli/azure/sql/server/tde-key) [az sql db tde](/cli/azure/sql/db/tde).

* * *

## <a name="troubleshooting"></a>Řešení potíží

Pokud dojde k problému, zkontrolujte následující:

- Pokud trezor klíčů nelze najít, ujistěte se, že jste ve správném předplatném.

   # <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

   ```powershell
   Get-AzSubscription -SubscriptionId <SubscriptionId>
   ```

   # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

   ```powershell
   az account show - s <SubscriptionId>
   ```

   * * *

- Pokud nový klíč nelze přidat na server nebo nový klíč nelze aktualizovat jako protektor TDE, zkontrolujte následující:
   - Klíč by neměl mít datum vypršení platnosti.
   - Klíč musí mít *klíč get*, wrap *klíč*a *rozbalit* operace klíče povolena.

## <a name="next-steps"></a>Další kroky

- Naučte se otáčet ochranu TDE serveru tak, aby splňovala požadavky na zabezpečení: [Otočte ochranu transparentního šifrování dat pomocí prostředí PowerShell](transparent-data-encryption-byok-azure-sql-key-rotation.md).
- V případě bezpečnostního rizika se dozvíte, jak odebrat potenciálně narušený chránič TDE: [Odeberte potenciálně ohrožený klíč](transparent-data-encryption-byok-azure-sql-remove-tde-protector.md).
