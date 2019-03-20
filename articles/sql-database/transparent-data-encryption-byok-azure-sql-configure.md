---
title: 'Prostředí PowerShell a rozhraní příkazového řádku: Povolení šifrování TDE SQL - pomocí služby Azure Key Vault – přineste si vlastní klíč – Azure SQL Database | Dokumentace Microsoftu'
description: Další informace o konfiguraci Azure SQL Database a Data Warehouse, pokud chcete začít používat transparentní šifrování dat (TDE) pro šifrování v klidovém stavu pomocí Powershellu nebo rozhraní příkazového řádku.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: aliceku
ms.author: aliceku
ms.reviewer: vanto
manager: craigg
ms.date: 03/12/2019
ms.openlocfilehash: c42c6175512105de38a29be260c370851e152137
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/18/2019
ms.locfileid: "57871634"
---
# <a name="powershell-and-cli-enable-transparent-data-encryption-with-customer-managed-key-from-azure-key-vault"></a>Prostředí PowerShell a rozhraní příkazového řádku: Povolit transparentní šifrování dat s použitím klíče spravovaného zákazníkem ze služby Azure Key Vault

Tento článek vás provede postupem použití klíče ze služby Azure Key Vault pro transparentní šifrování dat (TDE) pro SQL Database nebo datového skladu. Další informace o transparentní šifrování dat díky integraci služby Azure Key Vault – podpora přineste si vlastní klíč (BYOK), najdete v tématu [transparentní šifrování dat pomocí klíčů spravovaných zákazníkem ve službě Azure Key Vault](transparent-data-encryption-byok-azure-sql.md). 

## <a name="prerequisites-for-powershell"></a>Požadavky na prostředí PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Modul Azure PowerShell – Resource Manager je stále podporuje Azure SQL Database, ale všechny budoucí vývoj je Az.Sql modulu. Tyto rutiny najdete v části [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Argumenty pro příkazy v modulu Az a moduly AzureRm podstatně totožné.

- Musíte mít předplatné Azure a mít oprávnění správce pro toto předplatné.
- [Nepovinné ale doporučeno] Mají modulu hardwarového zabezpečení (HSM) nebo místní klíč úložiště pro vytváření místní kopie klíče ochrana TDE.
- Musíte mít Azure PowerShell nainstalovaný a spuštěný. 
- Vytvoření služby Azure Key Vault a klíče pro TDE.
  - [Pokyny pro PowerShell ze služby Key Vault](../key-vault/key-vault-overview.md)
  - [Pokyny pro používání modulu hardwarového zabezpečení (HSM) a služby Key Vault](../key-vault/key-vault-hsm-protected-keys.md)
    - Key vault musí mít následující vlastnost, která má být použit pro transparentní šifrování dat:
  - [soft-delete](../key-vault/key-vault-ovw-soft-delete.md)
  - [Jak používat obnovitelné odstranění Key Vaultu s využitím PowerShellu](../key-vault/key-vault-soft-delete-powershell.md) 
- Klíč musí mít následující atributy, které se použije pro transparentní šifrování dat:
   - Žádné datum vypršení platnosti
   - Není zakázáno
   - Může provádět *získat*, *zabalit klíč*, *rozbalit klíč* operace

## <a name="step-1-assign-an-azure-ad-identity-to-your-server"></a>Krok 1. Přiřazení k vašemu serveru Azure AD identity 

Pokud máte existující server, použijte následující identity Azure AD přidat k vašemu serveru:

   ```powershell
   $server = Set-AzSqlServer `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> `
   -AssignIdentity
   ```

Při vytváření serveru, použijte [New-AzSqlServer](/powershell/module/az.sql/new-azsqlserver) rutiny se značkou-Identity přidat při vytváření serveru Azure AD identity:

   ```powershell
   $server = New-AzSqlServer `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -Location <RegionName> `
   -ServerName <LogicalServerName> `
   -ServerVersion "12.0" `
   -SqlAdministratorCredentials <PSCredential> `
   -AssignIdentity 
   ```

## <a name="step-2-grant-key-vault-permissions-to-your-server"></a>Krok 2. Udělení oprávnění pro Key Vault k serveru

Použití [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) rutiny udělit přístup k klíč serveru trezoru před pro transparentní šifrování dat pomocí klíče z něj.

   ```powershell
   Set-AzKeyVaultAccessPolicy  `
   -VaultName <KeyVaultName> `
   -ObjectId $server.Identity.PrincipalId `
   -PermissionsToKeys get, wrapKey, unwrapKey
   ```

## <a name="step-3-add-the-key-vault-key-to-the-server-and-set-the-tde-protector"></a>Krok 3. Přidat klíč služby Key Vault k serveru a nastavit ochrana TDE

- Použití [přidat AzSqlServerKeyVaultKey](/powershell/module/az.sql/add-azsqlserverkeyvaultkey) rutiny pro přidání klíče ze služby Key Vault k serveru.
- Použití [Set-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) rutina pro nastavení klíče jako ochrana TDE pro všechny prostředky serveru.
- Použití [Get-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/get-azsqlservertransparentdataencryptionprotector) rutiny a potvrďte, že ochrana TDE byl nakonfigurován tak, jak má.

> [!Note]
> Celková délka pro název trezoru klíčů a název klíče nemůže být delší než 94 znaků.
> 

>[!Tip]
>Příklad ID klíče ze služby Key Vault: https://contosokeyvault.vault.azure.net/keys/Key1/1a1a2b2b3c3c4d4d5e5e6f6f7g7g8h8h
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

## <a name="step-4-turn-on-tde"></a>Krok 4. Zapnout transparentní šifrování dat 

Použití [Set-AzSqlDatabaseTransparentDataEncryption](/powershell/module/az.sql/set-azsqldatabasetransparentdataencryption) rutiny zapnout TDE.

   ```powershell
   Set-AzSqlDatabaseTransparentDataEncryption `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> `
   -DatabaseName <DatabaseName> `
   -State "Enabled"
   ```

Nyní databázi ani na datový sklad má povoleno pomocí šifrovacího klíče ve službě Key Vault TDE.

## <a name="step-5-check-the-encryption-state-and-encryption-activity"></a>Krok 5. Zkontrolujte stav šifrování a aktivita šifrování

Použití [Get-AzSqlDatabaseTransparentDataEncryption](/powershell/module/az.sql/get-azsqldatabasetransparentdataencryption) zobrazíte stav šifrování a [Get-AzSqlDatabaseTransparentDataEncryptionActivity](/powershell/module/az.sql/get-azsqldatabasetransparentdataencryptionactivity) ke kontrole průběhu šifrování pro databázi nebo datový sklad.

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

- Použití [Set-AzSqlDatabaseTransparentDataEncryption](/powershell/module/az.sql/set-azsqldatabasetransparentdataencryption) rutiny, chcete-li vypnout TDE.

   ```powershell
   Set-AzSqlDatabaseTransparentDataEncryption `
   -ServerName <LogicalServerName> `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -DatabaseName <DatabaseName> `
   -State "Disabled”
   ```
 
- Použití [Get-AzSqlServerKeyVaultKey](/powershell/module/az.sql/get-azsqlserverkeyvaultkey) rutina vrátí seznam klíčů služby Key Vault přidána na server.

   ```powershell
   <# KeyId is an optional parameter, to return a specific key version #>
   Get-AzSqlServerKeyVaultKey `
   -ServerName <LogicalServerName> `
   -ResourceGroupName <SQLDatabaseResourceGroupName>
   ```
 
- Použití [odebrat AzSqlServerKeyVaultKey](/powershell/module/az.sql/remove-azsqlserverkeyvaultkey) odebrání klíče služby Key Vault ze serveru.

   ```powershell
   <# The key set as the TDE Protector cannot be removed. #>
   Remove-AzSqlServerKeyVaultKey `
   -KeyId <KeyVaultKeyId> `
   -ServerName <LogicalServerName> `
   -ResourceGroupName <SQLDatabaseResourceGroupName>   
   ```
 
## <a name="troubleshooting"></a>Řešení potíží

Pokud dojde k problému zkontrolujte následující:
- Pokud nelze najít trezor klíčů, ujistěte se, že jste pomocí správného předplatného [Get-AzSubscription](/powershell/module/az.accounts/get-azsubscription) rutiny.

   ```powershell
   Get-AzSubscription `
   -SubscriptionId <SubscriptionId>
   ```

- Pokud se k serveru nelze přidat nový klíč, nebo nový klíč nelze aktualizovat jako ochrana TDE, zkontrolujte následující:
   - Klíč by neměl mít datum vypršení platnosti
   - Klíč musí mít *získat*, *zabalit klíč*, a *rozbalit klíč* operace povolena.

## <a name="next-steps"></a>Další postup

- Zjistěte, jak otočit ochrana TDE serveru k zajištění souladu s požadavky na zabezpečení: [Otočit pomocí Powershellu ochrany transparentního šifrování dat](transparent-data-encryption-byok-azure-sql-key-rotation.md).
- V případě ohrožení zabezpečení zjistěte, jak odebrat potenciálně ohroženými ochrana TDE: [Odebrání potenciálně ohrožený klíč](transparent-data-encryption-byok-azure-sql-remove-tde-protector.md). 

## <a name="prerequisites-for-cli"></a>Požadavky pro rozhraní příkazového řádku

- Musíte mít předplatné Azure a mít oprávnění správce pro toto předplatné.
- [Nepovinné ale doporučeno] Mají modulu hardwarového zabezpečení (HSM) nebo místní klíč úložiště pro vytváření místní kopie klíče ochrana TDE.
- Rozhraní příkazového řádku verze 2.0 nebo novější. Nainstalujte nejnovější verzi a připojte se ke svému předplatnému Azure najdete v tématu [instalace a konfigurace rozhraní příkazového řádku 2.0 pro různé platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli). 
- Vytvoření služby Azure Key Vault a klíče pro TDE.
  - [Správa služby Key Vault pomocí CLI 2.0](../key-vault/key-vault-manage-with-cli2.md)
  - [Pokyny pro používání modulu hardwarového zabezpečení (HSM) a služby Key Vault](../key-vault/key-vault-hsm-protected-keys.md)
    - Key vault musí mít následující vlastnost, která má být použit pro transparentní šifrování dat:
  - [soft-delete](../key-vault/key-vault-ovw-soft-delete.md)
  - [Jak používat obnovitelné odstranění Key Vaultu s využitím CLI](../key-vault/key-vault-soft-delete-cli.md) 
- Klíč musí mít následující atributy, které se použije pro transparentní šifrování dat:
   - Žádné datum vypršení platnosti
   - Není zakázáno
   - Může provádět *získat*, *zabalit klíč*, *rozbalit klíč* operace
   
## <a name="step-1-create-a-server-with-an-azure-ad-identity"></a>Krok 1. Vytvoření serveru s identitou služby Azure AD
      cli
      # create server (with identity) and database
      az sql server create --name <servername> --resource-group <rgname>  --location <location> --admin-user <user> --admin-password <password> --assign-identity
      az sql db create --name <dbname> --server <servername> --resource-group <rgname>  
 
 
>[!Tip]
>Zabránit "principalID" Vytvoření serveru, je použita k přiřazení trezoru klíčů oprávnění v dalším kroku id objektu
>
 
## <a name="step-2-grant-key-vault-permissions-to-the-logical-sql-server"></a>Krok 2. Udělení oprávnění pro Key Vault pro logický sql server
      cli
      # create key vault, key and grant permission
       az keyvault create --name <kvname> --resource-group <rgname> --location <location> --enable-soft-delete true
       az keyvault key create --name <keyname> --vault-name <kvname> --protection software
       az keyvault set-policy --name <kvname>  --object-id <objectid> --resource-group <rgname> --key-permissions wrapKey unwrapKey get 


>[!Tip]
>Zachovat klíče identifikátor URI nebo keyID nový klíč na další krok, například: https://contosokeyvault.vault.azure.net/keys/Key1/1a1a2b2b3c3c4d4d5e5e6f6f7g7g8h8h
>
 
       
## <a name="step-3-add-the-key-vault-key-to-the-server-and-set-the-tde-protector"></a>Krok 3. Přidat klíč služby Key Vault k serveru a nastavit ochrana TDE
  
     cli
     # add server key and update encryption protector
     az sql server key create --server <servername> --resource-group <rgname> --kid <keyID>
     az sql server tde-key set --server <servername> --server-key-type AzureKeyVault  --resource-group <rgname> --kid <keyID>

        
  > [!Note]
> Celková délka pro název trezoru klíčů a název klíče nemůže být delší než 94 znaků.
> 

  
## <a name="step-4-turn-on-tde"></a>Krok 4. Zapnout transparentní šifrování dat 
      cli
      # enable encryption
      az sql db tde set --database <dbname> --server <servername> --resource-group <rgname> --status Enabled 
      

Nyní databázi ani na datový sklad má povoleno pomocí klíče spravovaného zákazníkem šifrování ve službě Azure Key Vault TDE.

## <a name="step-5-check-the-encryption-state-and-encryption-activity"></a>Krok 5. Zkontrolujte stav šifrování a aktivita šifrování

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

