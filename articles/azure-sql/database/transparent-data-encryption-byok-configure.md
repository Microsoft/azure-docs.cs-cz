---
title: Povolit SQL TDE s Azure Key Vault
titleSuffix: Azure SQL Database & SQL Managed Instance & Azure Synapse Analytics
description: Naučte se konfigurovat Azure SQL Database a Azure synapse Analytics, abyste mohli začít používat transparentní šifrování dat (TDE) pro šifrování v klidovém prostředí pomocí PowerShellu nebo rozhraní příkazového řádku Azure CLI.
services: sql-database
ms.service: sql-db-mi
ms.subservice: security
ms.custom: seo-lt-2019 sqldbrb=1, devx-track-azurecli
ms.devlang: ''
ms.topic: how-to
author: jaszymas
ms.author: jaszymas
ms.reviewer: vanto
ms.date: 03/12/2019
ms.openlocfilehash: 610f0a9692c18afbd7bb446959b09bac14d6f629
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "102507615"
---
# <a name="powershell-and-the-azure-cli-enable-transparent-data-encryption-with-customer-managed-key-from-azure-key-vault"></a>PowerShell a rozhraní příkazového řádku Azure CLI: povolení transparentní šifrování dat s klíčem spravovaným zákazníkem z Azure Key Vault
[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

Tento článek vás seznámí s postupem použití klíče z Azure Key Vault pro transparentní šifrování dat (TDE) v Azure SQL Database nebo ve službě Azure synapse Analytics. Další informace o TDE s podporou Azure Key Vault Integration-Bring Your Own Key (BYOK) najdete v části [TDE s klíči spravovanými zákazníky v Azure Key Vault](transparent-data-encryption-byok-overview.md).

> [!NOTE] 
> Azure SQL teď podporuje použití klíče RSA uloženého ve spravovaném HSM jako ochrany TDE. Tato funkce je ve **verzi Public Preview**. Azure Key Vault spravovaný modul HSM je plně spravovaná cloudová služba s vysokou dostupností, která vyhovuje standardům, která vám umožní chránit kryptografické klíče pro vaše cloudové aplikace, a to pomocí ověřené HSM úrovně 3 ve standardu FIPS 140-2. Přečtěte si další informace o [spravovaných HSM](../../key-vault/managed-hsm/index.yml).

## <a name="prerequisites-for-powershell"></a>Předpoklady pro PowerShell

- Musíte mít předplatné Azure a mít oprávnění správce k tomuto předplatnému.
- [Doporučené, ale volitelné] Mít modul hardwarového zabezpečení (HSM) nebo místní úložiště klíčů pro vytvoření místní kopie materiálu klíče ochrany TDE.
- Musíte mít nainstalovanou a spuštěnou Azure PowerShell.
- Vytvořte Azure Key Vault a klíč pro použití pro TDE.
  - [Pokyny k používání modulu hardwarového zabezpečení (HSM) a Key Vault](../../key-vault/keys/hsm-protected-keys.md)
    - Trezor klíčů musí mít následující vlastnost, která se má použít pro TDE:
  - [obnovitelné odstraňování](../../key-vault/general/soft-delete-overview.md) a mazání ochrany
- Klíč musí mít následující atributy, které se mají použít pro TDE:
  - Nesmí mít žádné datum ukončení platnosti.
  - Nesmí být zakázaný.
  - Může provádět operace *Get*, *Wrap Key*, *rozbalení klíčových* operací.
- **(Ve verzi Preview)** Pokud chcete použít spravovaný klíč HSM, postupujte podle pokynů k [Vytvoření a aktivaci spravovaného modulu HSM pomocí Azure CLI](../../key-vault/managed-hsm/quick-create-cli.md) .

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Pokyny k instalaci modulu Az najdete v tématu věnovaném [instalaci Azure PowerShellu](/powershell/azure/install-az-ps). Konkrétní rutiny naleznete v tématu [AzureRM. SQL](/powershell/module/AzureRM.Sql/).

Konkrétní informace o Key Vault najdete v tématu [pokyny pro PowerShell z Key Vault](../../key-vault/secrets/quick-create-powershell.md) a [Jak používat Key Vault obnovitelného odstranění pomocí PowerShellu](../../key-vault/general/key-vault-recovery.md).

> [!IMPORTANT]
> Modul Azure Resource Manager PowerShellu (RM) je stále podporován, ale všechny budoucí vývojové prostředí jsou pro modul AZ. SQL. V modulu AzureRM bude i nadále docházet k opravám chyb až do prosince 2020.  Argumenty pro příkazy v modulech AZ a v modulech AzureRm jsou v podstatě identické. Další informace o kompatibilitě najdete v tématu [představení nového Azure PowerShell AZ Module](/powershell/azure/new-azureps-module-az).

## <a name="assign-an-azure-active-directory-azure-ad-identity-to-your-server"></a>Přiřazení identity Azure Active Directory (Azure AD) vašemu serveru

Pokud máte existující [Server](logical-servers.md), použijte následující postup k přidání identity Azure Active Directory (Azure AD) na server:

   ```powershell
   $server = Set-AzSqlServer -ResourceGroupName <SQLDatabaseResourceGroupName> -ServerName <LogicalServerName> -AssignIdentity
   ```

Pokud vytváříte server, použijte k přidání identity Azure AD během vytváření serveru rutinu [New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) s ID značky.

   ```powershell
   $server = New-AzSqlServer -ResourceGroupName <SQLDatabaseResourceGroupName> -Location <RegionName> `
       -ServerName <LogicalServerName> -ServerVersion "12.0" -SqlAdministratorCredentials <PSCredential> -AssignIdentity
   ```

## <a name="grant-key-vault-permissions-to-your-server"></a>Udělení Key Vault oprávnění vašemu serveru

Pomocí rutiny [set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) udělte vašemu serveru přístup k trezoru klíčů před použitím klíče pro TDE.

   ```powershell
   Set-AzKeyVaultAccessPolicy -VaultName <KeyVaultName> `
       -ObjectId $server.Identity.PrincipalId -PermissionsToKeys get, wrapKey, unwrapKey
   ```
Pokud chcete na server přidat oprávnění na spravovaném modulu HSM, přidejte na server místní roli RBAC šifrovací služby HSM. Tím umožníte, aby server prováděl operace získat, zabalit klíč a rozbalit klíče na klíče ve spravovaném modulu HSM.
[Pokyny k zřízení přístupu serveru na spravovaném modulu HSM](../../key-vault/managed-hsm/role-management.md)

## <a name="add-the-key-vault-key-to-the-server-and-set-the-tde-protector"></a>Přidejte Key Vault klíč k serveru a nastavte ochranu TDE.

- Načtení ID klíče z trezoru klíčů pomocí rutiny [Get-AzKeyVaultKey](/powershell/module/az.keyvault/get-azkeyvaultkey)
- K přidání klíče z Key Vault na server použijte rutinu [Add-AzSqlServerKeyVaultKey](/powershell/module/az.sql/add-azsqlserverkeyvaultkey) .
- Pomocí rutiny [set-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) nastavte klíč jako ochranu TDE pro všechny prostředky serveru.
- Pomocí rutiny [Get-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/get-azsqlservertransparentdataencryptionprotector) ověřte, že ochrana TDE byla nakonfigurovaná tak, jak má.

> [!NOTE]
> **(Ve verzi Preview)** U spravovaných klíčů HSM použijte příkaz AZ. SQL 2.11.1 verze prostředí PowerShell.

> [!NOTE]
> Celková délka názvu trezoru klíčů a názvu klíče nesmí překročit 94 znaků.

> [!TIP]
> Příklad KeyId z Key Vault:<br/>https://contosokeyvault.vault.azure.net/keys/Key1/1a1a2b2b3c3c4d4d5e5e6f6f7g7g8h8h
>
> Příklad KeyId ze spravovaného HSM:<br/>https://contosoMHSM.managedhsm.azure.net/keys/myrsakey

```powershell
# add the key from Key Vault to the server
Add-AzSqlServerKeyVaultKey -ResourceGroupName <SQLDatabaseResourceGroupName> -ServerName <LogicalServerName> -KeyId <KeyVaultKeyId>

# set the key as the TDE protector for all resources under the server
Set-AzSqlServerTransparentDataEncryptionProtector -ResourceGroupName <SQLDatabaseResourceGroupName> -ServerName <LogicalServerName> `
   -Type AzureKeyVault -KeyId <KeyVaultKeyId>

# confirm the TDE protector was configured as intended
Get-AzSqlServerTransparentDataEncryptionProtector -ResourceGroupName <SQLDatabaseResourceGroupName> -ServerName <LogicalServerName>
```

## <a name="turn-on-tde"></a>Zapnout TDE

Zapněte TDE pomocí rutiny [set-AzSqlDatabaseTransparentDataEncryption](/powershell/module/az.sql/set-azsqldatabasetransparentdataencryption) .

```powershell
Set-AzSqlDatabaseTransparentDataEncryption -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> -DatabaseName <DatabaseName> -State "Enabled"
```

Databáze nebo datový sklad teď má TDE povolený šifrovací klíč v Key Vault.

## <a name="check-the-encryption-state-and-encryption-activity"></a>Zkontroluje stav šifrování a aktivitu šifrování.

Použijte [příkaz Get-AzSqlDatabaseTransparentDataEncryption](/powershell/module/az.sql/get-azsqldatabasetransparentdataencryption) k získání stavu šifrování a [Get-AzSqlDatabaseTransparentDataEncryptionActivity](/powershell/module/az.sql/get-azsqldatabasetransparentdataencryptionactivity) pro kontrolu průběhu šifrování databáze nebo datového skladu.

```powershell
# get the encryption state
Get-AzSqlDatabaseTransparentDataEncryption -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> -DatabaseName <DatabaseName> `

# check the encryption progress for a database or data warehouse
Get-AzSqlDatabaseTransparentDataEncryptionActivity -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> -DatabaseName <DatabaseName>  
```

# <a name="the-azure-cli"></a>[Azure CLI](#tab/azure-cli)

Pokud chcete nainstalovat požadovanou verzi rozhraní příkazového řádku Azure CLI (verze 2,0 nebo novější) a připojit se k předplatnému Azure, přečtěte si téma [instalace a konfigurace Azure pro více platforem Command-Line rozhraní 2,0](/cli/azure/install-azure-cli).

Konkrétní informace o Key Vault najdete v tématech [správa Key Vault pomocí rozhraní příkazového řádku (cli 2,0](../../key-vault/general/manage-with-cli2.md) ) a [Jak používat Key Vault obnovitelného odstranění pomocí rozhraní](../../key-vault/general/key-vault-recovery.md)příkazového řádku.

## <a name="assign-an-azure-ad-identity-to-your-server"></a>Přiřazení identity Azure AD k vašemu serveru

```azurecli
# create server (with identity) and database
az sql server create --name <servername> --resource-group <rgname>  --location <location> --admin-user <user> --admin-password <password> --assign-identity
az sql db create --name <dbname> --server <servername> --resource-group <rgname>
```

> [!TIP]
> Ponechte "principalID" z vytváření serveru, jedná se o ID objektu používané k přiřazení oprávnění trezoru klíčů v dalším kroku.

## <a name="grant-key-vault-permissions-to-your-server"></a>Udělení Key Vault oprávnění vašemu serveru

```azurecli
# create key vault, key and grant permission
az keyvault create --name <kvname> --resource-group <rgname> --location <location> --enable-soft-delete true
az keyvault key create --name <keyname> --vault-name <kvname> --protection software
az keyvault set-policy --name <kvname>  --object-id <objectid> --resource-group <rgname> --key-permissions wrapKey unwrapKey get
```

> [!TIP]
> Ponechte klíč URI nebo keyID nového klíče pro další krok, například: https://contosokeyvault.vault.azure.net/keys/Key1/1a1a2b2b3c3c4d4d5e5e6f6f7g7g8h8h

## <a name="add-the-key-vault-key-to-the-server-and-set-the-tde-protector"></a>Přidejte Key Vault klíč k serveru a nastavte ochranu TDE.

```azurecli
# add server key and update encryption protector
az sql server key create --server <servername> --resource-group <rgname> --kid <keyID>
az sql server tde-key set --server <servername> --server-key-type AzureKeyVault  --resource-group <rgname> --kid <keyID>
```

> [!NOTE]
> Celková délka názvu trezoru klíčů a názvu klíče nesmí překročit 94 znaků.

## <a name="turn-on-tde"></a>Zapnout TDE

```azurecli
# enable encryption
az sql db tde set --database <dbname> --server <servername> --resource-group <rgname> --status Enabled
```

Databáze nebo datový sklad teď má TDE povolený šifrovací klíč spravovaný zákazníkem v Azure Key Vault.

## <a name="check-the-encryption-state-and-encryption-activity"></a>Zkontroluje stav šifrování a aktivitu šifrování.

```azurecli
# get encryption scan progress
az sql db tde list-activity --database <dbname> --server <servername> --resource-group <rgname>  

# get whether encryption is on or off
az sql db tde show --database <dbname> --server <servername> --resource-group <rgname>
```

* * *

## <a name="useful-powershell-cmdlets"></a>Užitečné rutiny PowerShellu

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

- Pro vypnutí TDE použijte rutinu [set-AzSqlDatabaseTransparentDataEncryption](/powershell/module/az.sql/set-azsqldatabasetransparentdataencryption) .

   ```powershell
   Set-AzSqlDatabaseTransparentDataEncryption -ServerName <LogicalServerName> -ResourceGroupName <SQLDatabaseResourceGroupName> `
      -DatabaseName <DatabaseName> -State "Disabled"
   ```

- Pomocí rutiny [Get-AzSqlServerKeyVaultKey](/powershell/module/az.sql/get-azsqlserverkeyvaultkey) vraťte seznam Key Vault klíčů přidaných na server.

   ```powershell
   # KeyId is an optional parameter, to return a specific key version
   Get-AzSqlServerKeyVaultKey -ServerName <LogicalServerName> -ResourceGroupName <SQLDatabaseResourceGroupName>
   ```

- K odebrání Key Vault klíče ze serveru použijte [příkaz Remove-AzSqlServerKeyVaultKey](/powershell/module/az.sql/remove-azsqlserverkeyvaultkey) .

   ```powershell
   # the key set as the TDE Protector cannot be removed
   Remove-AzSqlServerKeyVaultKey -KeyId <KeyVaultKeyId> -ServerName <LogicalServerName> -ResourceGroupName <SQLDatabaseResourceGroupName>
   ```

# <a name="the-azure-cli"></a>[Azure CLI](#tab/azure-cli)

- Obecné nastavení databáze najdete v tématu [AZ SQL](/cli/azure/sql).

- Nastavení klíče trezoru najdete v tématu [AZ SQL Server Key](/cli/azure/sql/server/key).

- Nastavení TDE najdete v tématu [AZ SQL Server TDE-Key](/cli/azure/sql/server/tde-key) a [AZ SQL DB TDE](/cli/azure/sql/db/tde).

* * *

## <a name="troubleshooting"></a>Řešení potíží

Pokud dojde k problému, podívejte se na následující:

- Pokud se Trezor klíčů nenajde, ujistěte se, že jste ve správném předplatném.

   # <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

   ```powershell
   Get-AzSubscription -SubscriptionId <SubscriptionId>
   ```

   # <a name="the-azure-cli"></a>[Azure CLI](#tab/azure-cli)

   ```powershell
   az account show - s <SubscriptionId>
   ```

   * * *

- Pokud nový klíč nelze přidat na server nebo nový klíč nelze aktualizovat jako ochranu TDE, podívejte se na následující:
   - Klíč by neměl mít datum vypršení platnosti.
   - Klíč musí mít povolené operace *Get*, *Wrap Key* a *Unwrap Key* .

## <a name="next-steps"></a>Další kroky

- Naučte se, jak otočit ochranu TDE serveru tak, aby splňovala požadavky na zabezpečení: [otočení transparentní šifrování dat ochrany pomocí PowerShellu](transparent-data-encryption-byok-key-rotation.md).
- V případě bezpečnostního rizika se dozvíte, jak odebrat potenciálně ohrožený TDE ochranu: [Odebrání potenciálně ohroženého klíče](transparent-data-encryption-byok-remove-tde-protector.md).
