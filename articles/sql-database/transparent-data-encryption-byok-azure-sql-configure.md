---
title: 'PowerShell a rozhraní příkazového řádku: povolení SQL TDE – pomocí Azure Key Vault-Přineste si vlastní klíč – Azure SQL Database | Microsoft Docs'
description: Naučte se konfigurovat Azure SQL Database a datový sklad, abyste mohli začít používat transparentní šifrování dat (TDE) pro šifrování v klidovém formátu pomocí PowerShellu nebo rozhraní příkazového řádku.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: aliceku
ms.author: aliceku
ms.reviewer: vanto
ms.date: 03/12/2019
ms.openlocfilehash: beeb5fa4f979ac457db8a779dd8f8f2e94ef87f5
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2019
ms.locfileid: "73163848"
---
# <a name="powershell-and-cli-enable-transparent-data-encryption-with-customer-managed-key-from-azure-key-vault"></a>PowerShell a rozhraní příkazového řádku: povolení transparentní šifrování dat s klíčem spravovaným zákazníkem z Azure Key Vault

Tento článek vás seznámí s postupem použití klíče z Azure Key Vault pro transparentní šifrování dat (TDE) na SQL Database nebo na datovém skladu. Další informace o TDE s podporou Azure Key Vault Integration-Bring Your Own Key (BYOK) najdete v části [TDE s klíči spravovanými zákazníky v Azure Key Vault](transparent-data-encryption-byok-azure-sql.md). 

## <a name="prerequisites-for-powershell"></a>Předpoklady pro PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Modul PowerShell Azure Resource Manager je stále podporován Azure SQL Database, ale všechny budoucí vývojové prostředí jsou pro modul AZ. SQL. Tyto rutiny naleznete v tématu [AzureRM. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Argumenty pro příkazy v modulech AZ a v modulech AzureRm jsou v podstatě identické.

- Musíte mít předplatné Azure a mít oprávnění správce k tomuto předplatnému.
- [Doporučené, ale volitelné] Mít modul hardwarového zabezpečení (HSM) nebo místní úložiště klíčů pro vytvoření místní kopie materiálu klíče ochrany TDE.
- Musíte mít nainstalovanou a spuštěnou Azure PowerShell. 
- Vytvořte Azure Key Vault a klíč pro použití pro TDE.
  - [Pokyny pro PowerShell z Key Vault](../key-vault/quick-create-powershell.md)
  - [Pokyny k používání modulu hardwarového zabezpečení (HSM) a Key Vault](../key-vault/key-vault-hsm-protected-keys.md)
    - Trezor klíčů musí mít následující vlastnost, která se má použít pro TDE:
  - [obnovitelné odstraňování](../key-vault/key-vault-ovw-soft-delete.md) a mazání ochrany
  - [Jak používat obnovitelné odstranění Key Vaultu s využitím PowerShellu](../key-vault/key-vault-soft-delete-powershell.md) 
- Klíč musí mít následující atributy, které se mají použít pro TDE:
   - Žádné datum vypršení platnosti
   - Nezakázáno
   - Může provádět operace *Get*, *Wrap Key*, *rozbalení klíčových* operací.

## <a name="step-1-assign-an-azure-ad-identity-to-your-server"></a>Krok 1. Přiřazení identity Azure AD k vašemu serveru 

Pokud máte existující server, přidejte na server identitu Azure AD pomocí následujících kroků:

   ```powershell
   $server = Set-AzSqlServer `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> `
   -AssignIdentity
   ```

Pokud vytváříte server, použijte k přidání identity Azure AD během vytváření serveru rutinu [New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) s ID značky.

   ```powershell
   $server = New-AzSqlServer `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -Location <RegionName> `
   -ServerName <LogicalServerName> `
   -ServerVersion "12.0" `
   -SqlAdministratorCredentials <PSCredential> `
   -AssignIdentity 
   ```

## <a name="step-2-grant-key-vault-permissions-to-your-server"></a>Krok 2. Udělení Key Vault oprávnění vašemu serveru

Pomocí rutiny [set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) udělte vašemu serveru přístup k trezoru klíčů před použitím klíče pro TDE.

   ```powershell
   Set-AzKeyVaultAccessPolicy  `
   -VaultName <KeyVaultName> `
   -ObjectId $server.Identity.PrincipalId `
   -PermissionsToKeys get, wrapKey, unwrapKey
   ```

## <a name="step-3-add-the-key-vault-key-to-the-server-and-set-the-tde-protector"></a>Krok 3. Přidejte Key Vault klíč k serveru a nastavte ochranu TDE.


- Načtení ID klíče z trezoru klíčů pomocí rutiny [Get-AzKeyVaultKey](/powershell/module/az.keyvault/get-azkeyvaultkey?view=azps-2.4.0)
- K přidání klíče z Key Vault na server použijte rutinu [Add-AzSqlServerKeyVaultKey](/powershell/module/az.sql/add-azsqlserverkeyvaultkey) .
- Pomocí rutiny [set-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) nastavte klíč jako ochranu TDE pro všechny prostředky serveru.
- Pomocí rutiny [Get-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/get-azsqlservertransparentdataencryptionprotector) ověřte, že ochrana TDE byla nakonfigurovaná tak, jak má.

> [!Note]
> Celková délka názvu trezoru klíčů a názvu klíče nesmí překročit 94 znaků.
> 

>[!Tip]
>Příklad KeyId z Key Vault: https://contosokeyvault.vault.azure.net/keys/Key1/1a1a2b2b3c3c4d4d5e5e6f6f7g7g8h8h
>

   ```powershell
   <# Add the key from Key Vault to the server #>
   Add-AzSqlServerKeyVaultKey `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> `
   -KeyId <KeyVaultKeyId>

   <# Set the key as the TDE protector for all resources under the server #>
   Set-AzSqlServerTransparentDataEncryptionProtector `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> `
   -Type AzureKeyVault `
   -KeyId <KeyVaultKeyId> 

   <# To confirm that the TDE protector was configured as intended: #>
   Get-AzSqlServerTransparentDataEncryptionProtector `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> 
   ```

## <a name="step-4-turn-on-tde"></a>Krok 4. Zapnout TDE 

Zapněte TDE pomocí rutiny [set-AzSqlDatabaseTransparentDataEncryption](/powershell/module/az.sql/set-azsqldatabasetransparentdataencryption) .

   ```powershell
   Set-AzSqlDatabaseTransparentDataEncryption `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> `
   -DatabaseName <DatabaseName> `
   -State "Enabled"
   ```

Databáze nebo datový sklad teď má TDE povolený šifrovací klíč v Key Vault.

## <a name="step-5-check-the-encryption-state-and-encryption-activity"></a>Krok 5. Zkontroluje stav šifrování a aktivitu šifrování.

Použijte [příkaz Get-AzSqlDatabaseTransparentDataEncryption](/powershell/module/az.sql/get-azsqldatabasetransparentdataencryption) k získání stavu šifrování a [Get-AzSqlDatabaseTransparentDataEncryptionActivity](/powershell/module/az.sql/get-azsqldatabasetransparentdataencryptionactivity) pro kontrolu průběhu šifrování databáze nebo datového skladu.

   ```powershell
   # Get the encryption state
   Get-AzSqlDatabaseTransparentDataEncryption `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> `
   -DatabaseName <DatabaseName> `

   <# Check the encryption progress for a database or data warehouse #>
   Get-AzSqlDatabaseTransparentDataEncryptionActivity `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> `
   -DatabaseName <DatabaseName>  
   ```

## <a name="other-useful-powershell-cmdlets"></a>Další užitečné rutiny prostředí PowerShell

- Pro vypnutí TDE použijte rutinu [set-AzSqlDatabaseTransparentDataEncryption](/powershell/module/az.sql/set-azsqldatabasetransparentdataencryption) .

   ```powershell
   Set-AzSqlDatabaseTransparentDataEncryption `
   -ServerName <LogicalServerName> `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -DatabaseName <DatabaseName> `
   -State "Disabled"
   ```
 
- Pomocí rutiny [Get-AzSqlServerKeyVaultKey](/powershell/module/az.sql/get-azsqlserverkeyvaultkey) vraťte seznam Key Vault klíčů přidaných na server.

   ```powershell
   <# KeyId is an optional parameter, to return a specific key version #>
   Get-AzSqlServerKeyVaultKey `
   -ServerName <LogicalServerName> `
   -ResourceGroupName <SQLDatabaseResourceGroupName>
   ```
 
- K odebrání Key Vault klíče ze serveru použijte [příkaz Remove-AzSqlServerKeyVaultKey](/powershell/module/az.sql/remove-azsqlserverkeyvaultkey) .

   ```powershell
   <# The key set as the TDE Protector cannot be removed. #>
   Remove-AzSqlServerKeyVaultKey `
   -KeyId <KeyVaultKeyId> `
   -ServerName <LogicalServerName> `
   -ResourceGroupName <SQLDatabaseResourceGroupName>   
   ```
 
## <a name="troubleshooting"></a>Řešení potíží

Pokud dojde k problému, podívejte se na následující:
- Pokud se Trezor klíčů nepovedlo najít, ujistěte se, že jste ve správném předplatném pomocí rutiny [Get-AzSubscription](/powershell/module/az.accounts/get-azsubscription) .

   ```powershell
   Get-AzSubscription `
   -SubscriptionId <SubscriptionId>
   ```

- Pokud nový klíč nelze přidat na server nebo nový klíč nelze aktualizovat jako ochranu TDE, podívejte se na následující:
   - Klíč by neměl mít datum vypršení platnosti.
   - Klíč musí mít povolené operace *Get*, *Wrap Key*a *Unwrap Key* .

## <a name="next-steps"></a>Další kroky

- Naučte se, jak otočit ochranu TDE serveru tak, aby splňovala požadavky na zabezpečení: [otočení transparentní šifrování dat ochrany pomocí PowerShellu](transparent-data-encryption-byok-azure-sql-key-rotation.md).
- V případě bezpečnostního rizika se dozvíte, jak odebrat potenciálně ohrožený TDE ochranu: [Odebrání potenciálně ohroženého klíče](transparent-data-encryption-byok-azure-sql-remove-tde-protector.md). 

## <a name="prerequisites-for-cli"></a>Předpoklady pro rozhraní příkazového řádku

- Musíte mít předplatné Azure a mít oprávnění správce k tomuto předplatnému.
- [Doporučené, ale volitelné] Mít modul hardwarového zabezpečení (HSM) nebo místní úložiště klíčů pro vytvoření místní kopie materiálu klíče ochrany TDE.
- Rozhraní příkazového řádku verze 2,0 nebo novější. Pokud chcete nainstalovat nejnovější verzi a připojit se k předplatnému Azure, přečtěte si téma [instalace a konfigurace 2,0 rozhraní příkazového řádku Azure pro více platforem](https://docs.microsoft.com/cli/azure/install-azure-cli). 
- Vytvořte Azure Key Vault a klíč pro použití pro TDE.
  - [Správa Key Vault pomocí CLI 2,0](../key-vault/key-vault-manage-with-cli2.md)
  - [Pokyny k používání modulu hardwarového zabezpečení (HSM) a Key Vault](../key-vault/key-vault-hsm-protected-keys.md)
    - Trezor klíčů musí mít následující vlastnost, která se má použít pro TDE:
  - [Obnovitelné odstranění](../key-vault/key-vault-ovw-soft-delete.md)
  - [Jak používat obnovitelné odstranění Key Vaultu s využitím CLI](../key-vault/key-vault-soft-delete-cli.md) 
- Klíč musí mít následující atributy, které se mají použít pro TDE:
   - Žádné datum vypršení platnosti
   - Nezakázáno
   - Může provádět operace *Get*, *Wrap Key*, *rozbalení klíčových* operací.
   
## <a name="step-1-create-a-server-with-an-azure-ad-identity"></a>Krok 1. Vytvoření serveru s identitou Azure AD
      cli
      # create server (with identity) and database
      az sql server create --name <servername> --resource-group <rgname>  --location <location> --admin-user <user> --admin-password <password> --assign-identity
      az sql db create --name <dbname> --server <servername> --resource-group <rgname>  
 
 
>[!Tip]
>Ponechte "principalID" z vytváření serveru, jedná se o ID objektu používané k přiřazení oprávnění trezoru klíčů v dalším kroku.
>
 
## <a name="step-2-grant-key-vault-permissions-to-the-logical-sql-server"></a>Krok 2. Udělení oprávnění Key Vault logickému SQL serveru
      cli
      # create key vault, key and grant permission
       az keyvault create --name <kvname> --resource-group <rgname> --location <location> --enable-soft-delete true
       az keyvault key create --name <keyname> --vault-name <kvname> --protection software
       az keyvault set-policy --name <kvname>  --object-id <objectid> --resource-group <rgname> --key-permissions wrapKey unwrapKey get 


>[!Tip]
>Ponechte klíč URI nebo keyID nového klíče pro další krok, například: https://contosokeyvault.vault.azure.net/keys/Key1/1a1a2b2b3c3c4d4d5e5e6f6f7g7g8h8h
>
 
       
## <a name="step-3-add-the-key-vault-key-to-the-server-and-set-the-tde-protector"></a>Krok 3. Přidejte Key Vault klíč k serveru a nastavte ochranu TDE.
  
     cli
     # add server key and update encryption protector
     az sql server key create --server <servername> --resource-group <rgname> --kid <keyID>
     az sql server tde-key set --server <servername> --server-key-type AzureKeyVault  --resource-group <rgname> --kid <keyID>

        
  > [!Note]
> Celková délka názvu trezoru klíčů a názvu klíče nesmí překročit 94 znaků.
> 

  
## <a name="step-4-turn-on-tde"></a>Krok 4. Zapnout TDE 
      cli
      # enable encryption
      az sql db tde set --database <dbname> --server <servername> --resource-group <rgname> --status Enabled 
      

Databáze nebo datový sklad teď má TDE povolený šifrovací klíč spravovaný zákazníkem v Azure Key Vault.

## <a name="step-5-check-the-encryption-state-and-encryption-activity"></a>Krok 5. Zkontroluje stav šifrování a aktivitu šifrování.

     cli
      # get encryption scan progress
      az sql db tde list-activity --database <dbname> --server <servername> --resource-group <rgname>  

      # get whether encryption is on or off
      az sql db tde show --database <dbname> --server <servername> --resource-group <rgname> 

## <a name="sql-cli-references"></a>Reference k rozhraní příkazového řádku SQL

https://docs.microsoft.com/cli/azure/sql 

https://docs.microsoft.com/cli/azure/sql/server/key 

https://docs.microsoft.com/cli/azure/sql/server/tde-key 

https://docs.microsoft.com/cli/azure/sql/db/tde 

