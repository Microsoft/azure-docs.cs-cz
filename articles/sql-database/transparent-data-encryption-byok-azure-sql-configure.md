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
ms.date: 12/04/2018
ms.openlocfilehash: b6abfa24d790554de239171ffef1899cdc135d26
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/11/2019
ms.locfileid: "55996249"
---
# <a name="powershell-and-cli-enable-transparent-data-encryption-with-customer-managed-key-from-azure-key-vault"></a>Prostředí PowerShell a rozhraní příkazového řádku: Povolit transparentní šifrování dat s použitím klíče spravovaného zákazníkem ze služby Azure Key Vault

Tento článek vás provede postupem použití klíče ze služby Azure Key Vault pro transparentní šifrování dat (TDE) pro SQL Database nebo datového skladu. Další informace o transparentní šifrování dat díky integraci služby Azure Key Vault – podpora přineste si vlastní klíč (BYOK), najdete v tématu [transparentní šifrování dat pomocí klíčů spravovaných zákazníkem ve službě Azure Key Vault](transparent-data-encryption-byok-azure-sql.md). 

## <a name="prerequisites-for-powershell"></a>Požadavky na prostředí PowerShell

- Musíte mít předplatné Azure a mít oprávnění správce pro toto předplatné.
- [Nepovinné ale doporučeno] Mají modulu hardwarového zabezpečení (HSM) nebo místní klíč úložiště pro vytváření místní kopie klíče ochrana TDE.
- Musíte mít Azure PowerShell verze 4.2.0 nebo novější nainstalován a spuštěn. 
- Vytvoření služby Azure Key Vault a klíče pro TDE.
   - [Pokyny pro PowerShell ze služby Key Vault](../key-vault/key-vault-get-started.md)
   - [Pokyny pro používání modulu hardwarového zabezpečení (HSM) a služby Key Vault](../key-vault/key-vault-get-started.md#HSM)
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
   $server = Set-AzureRmSqlServer `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> `
   -AssignIdentity
   ```

Pokud vytváříte server, použijte [New-AzureRmSqlServer](/powershell/module/azurerm.sql/new-azurermsqlserver) rutiny se značkou-Identity přidat při vytváření serveru Azure AD identity:

   ```powershell
   $server = New-AzureRmSqlServer `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -Location <RegionName> `
   -ServerName <LogicalServerName> `
   -ServerVersion "12.0" `
   -SqlAdministratorCredentials <PSCredential> `
   -AssignIdentity 
   ```

## <a name="step-2-grant-key-vault-permissions-to-your-server"></a>Krok 2. Udělení oprávnění pro Key Vault k serveru

Použití [Set-AzureRmKeyVaultAccessPolicy](/powershell/module/azurerm.keyvault/set-azurermkeyvaultaccesspolicy) rutiny udělit přístup k klíč serveru trezoru před pro transparentní šifrování dat pomocí klíče z něj.

   ```powershell
   Set-AzureRmKeyVaultAccessPolicy  `
   -VaultName <KeyVaultName> `
   -ObjectId $server.Identity.PrincipalId `
   -PermissionsToKeys get, wrapKey, unwrapKey
   ```

## <a name="step-3-add-the-key-vault-key-to-the-server-and-set-the-tde-protector"></a>Krok 3. Přidat klíč služby Key Vault k serveru a nastavit ochrana TDE

- Použití [přidat AzureRmSqlServerKeyVaultKey](/powershell/module/azurerm.sql/add-azurermsqlserverkeyvaultkey) rutiny pro přidání klíče ze služby Key Vault k serveru.
- Použití [rutiny Set-AzureRmSqlServerTransparentDataEncryptionProtector](/powershell/module/azurerm.sql/set-azurermsqlservertransparentdataencryptionprotector) rutina pro nastavení klíče jako ochrana TDE pro všechny prostředky serveru.
- Použití [Get-AzureRmSqlServerTransparentDataEncryptionProtector](/powershell/module/azurerm.sql/get-azurermsqlservertransparentdataencryptionprotector) rutiny a potvrďte, že ochrana TDE byl nakonfigurován tak, jak má.

> [!Note]
> Celková délka pro název trezoru klíčů a název klíče nemůže být delší než 94 znaků.
> 

>[!Tip]
>Příklad ID klíče ze služby Key Vault: https://contosokeyvault.vault.azure.net/keys/Key1/1a1a2b2b3c3c4d4d5e5e6f6f7g7g8h8h
>

   ```powershell
   <# Add the key from Key Vault to the server #>
   Add-AzureRmSqlServerKeyVaultKey `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> `
   -KeyId <KeyVaultKeyId>

   <# Set the key as the TDE protector for all resources under the server #>
   Set-AzureRmSqlServerTransparentDataEncryptionProtector `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> `
   -Type AzureKeyVault `
   -KeyId <KeyVaultKeyId> 

   <# To confirm that the TDE protector was configured as intended: #>
   Get-AzureRmSqlServerTransparentDataEncryptionProtector `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> 
   ```

## <a name="step-4-turn-on-tde"></a>Krok 4. Zapnout transparentní šifrování dat 

Použití [Set-AzureRMSqlDatabaseTransparentDataEncryption](/powershell/module/azurerm.sql/set-azurermsqldatabasetransparentdataencryption) rutiny zapnout TDE.

   ```powershell
   Set-AzureRMSqlDatabaseTransparentDataEncryption `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> `
   -DatabaseName <DatabaseName> `
   -State "Enabled"
   ```

Nyní databázi ani na datový sklad má povoleno pomocí šifrovacího klíče ve službě Key Vault TDE.

## <a name="step-5-check-the-encryption-state-and-encryption-activity"></a>Krok 5. Zkontrolujte stav šifrování a aktivita šifrování

Použití [Get-AzureRMSqlDatabaseTransparentDataEncryption](/powershell/module/azurerm.sql/get-azurermsqldatabasetransparentdataencryption) zobrazíte stav šifrování a [Get-AzureRMSqlDatabaseTransparentDataEncryptionActivity](/powershell/module/azurerm.sql/get-azurermsqldatabasetransparentdataencryptionactivity) ke kontrole průběhu šifrování pro databáze nebo datového skladu.

   ```powershell
   # Get the encryption state
   Get-AzureRMSqlDatabaseTransparentDataEncryption `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> `
   -DatabaseName <DatabaseName> `

   <# Check the encryption progress for a database or data warehouse #>
   Get-AzureRMSqlDatabaseTransparentDataEncryptionActivity `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> `
   -DatabaseName <DatabaseName>  
   ```

## <a name="other-useful-powershell-cmdlets"></a>Další užitečné rutiny prostředí PowerShell

- Použití [Set-AzureRMSqlDatabaseTransparentDataEncryption](/powershell/module/azurerm.sql/set-azurermsqldatabasetransparentdataencryption) rutiny, chcete-li vypnout TDE.

   ```powershell
   Set-AzureRMSqlDatabaseTransparentDataEncryption `
   -ServerName <LogicalServerName> `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -DatabaseName <DatabaseName> `
   -State "Disabled”
   ```
 
- Použití [Get-AzureRmSqlServerKeyVaultKey](/powershell/module/azurerm.sql/get-azurermsqlserverkeyvaultkey) rutina vrátí seznam klíčů služby Key Vault přidána na server.

   ```powershell
   <# KeyId is an optional parameter, to return a specific key version #>
   Get-AzureRmSqlServerKeyVaultKey `
   -ServerName <LogicalServerName> `
   -ResourceGroupName <SQLDatabaseResourceGroupName>
   ```
 
- Použití [odebrat AzureRmSqlServerKeyVaultKey](/powershell/module/azurerm.sql/remove-azurermsqlserverkeyvaultkey) odebrání klíče služby Key Vault ze serveru.

   ```powershell
   <# The key set as the TDE Protector cannot be removed. #>
   Remove-AzureRmSqlServerKeyVaultKey `
   -KeyId <KeyVaultKeyId> `
   -ServerName <LogicalServerName> `
   -ResourceGroupName <SQLDatabaseResourceGroupName>   
   ```
 
## <a name="troubleshooting"></a>Řešení potíží

Pokud dojde k problému zkontrolujte následující:
- Pokud nelze najít trezor klíčů, ujistěte se, že jste pomocí správného předplatného [Get-AzureRmSubscription](/powershell/module/azurerm.profile/get-azurermsubscription) rutiny.

   ```powershell
   Get-AzureRmSubscription `
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
- Rozhraní příkazového řádku verze 2.0 nebo novější. Nainstalujte nejnovější verzi a připojte se ke svému předplatnému Azure najdete v tématu [instalace a konfigurace rozhraní příkazového řádku 2.0 pro různé platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest). 
- Vytvoření služby Azure Key Vault a klíče pro TDE.
   - [Správa služby Key Vault pomocí CLI 2.0](../key-vault/key-vault-manage-with-cli2.md)
   - [Pokyny pro používání modulu hardwarového zabezpečení (HSM) a služby Key Vault](../key-vault/key-vault-get-started.md#HSM)
 - Key vault musí mít následující vlastnost, která má být použit pro transparentní šifrování dat:
   - [soft-delete](../key-vault/key-vault-ovw-soft-delete.md)
   - [Jak používat obnovitelné odstranění Key Vaultu s využitím CLI](../key-vault/key-vault-soft-delete-cli.md) 
- Klíč musí mít následující atributy, které se použije pro transparentní šifrování dat:
   - Žádné datum vypršení platnosti
   - Není zakázáno
   - Může provádět *získat*, *zabalit klíč*, *rozbalit klíč* operace
   
## <a name="step-1-create-a-server-and-assign-an-azure-ad-identity-to-your-server"></a>Krok 1. Vytvoření serveru a přiřazení k vašemu serveru Azure AD identity
      cli
      # create server (with identity) and database
      az sql server create -n "ServerName" -g "ResourceGroupName" -l "westus" -u "cloudsa" -p "YourFavoritePassWord99@34" -i 
      az sql db create -n "DatabaseName" -g "ResourceGroupName" -s "ServerName" 
      

 
## <a name="step-2-grant-key-vault-permissions-to-your-server"></a>Krok 2. Udělení oprávnění pro Key Vault k serveru
      cli
      # create key vault, key and grant permission
      az keyvault create -n "VaultName" -g "ResourceGroupName" 
      az keyvault key create -n myKey -p software --vault-name "VaultName" 
      az keyvault set-policy -n "VaultName" --object-id "ServerIdentityObjectId" -g "ResourceGroupName" --key-permissions wrapKey unwrapKey get list 
      

 
## <a name="step-3-add-the-key-vault-key-to-the-server-and-set-the-tde-protector"></a>Krok 3. Přidat klíč služby Key Vault k serveru a nastavit ochrana TDE
  
     cli
     # add server key and update encryption protector
      az sql server key create -g "ResourceGroupName" -s "ServerName" -t "AzureKeyVault" -u "FullVersionedKeyUri 
      az sql server tde-key update -g "ResourceGroupName" -s "ServerName" -t AzureKeyVault -u "FullVersionedKeyUri" 
      
  
  > [!Note]
> Celková délka pro název trezoru klíčů a název klíče nemůže být delší než 94 znaků.
> 

>[!Tip]
>Příklad ID klíče ze služby Key Vault: https://contosokeyvault.vault.azure.net/keys/Key1/1a1a2b2b3c3c4d4d5e5e6f6f7g7g8h8h
>
  
## <a name="step-4-turn-on-tde"></a>Krok 4. Zapnout transparentní šifrování dat 
      cli
      # enable encryption
      az sql db tde create -n "DatabaseName" -g "ResourceGroupName" -s "ServerName" --status Enabled 
      

Nyní databázi ani na datový sklad má povoleno pomocí šifrovacího klíče ve službě Key Vault TDE.

## <a name="step-5-check-the-encryption-state-and-encryption-activity"></a>Krok 5. Zkontrolujte stav šifrování a aktivita šifrování

     cli
      # get encryption scan progress
      az sql db tde show-activity -n "DatabaseName" -g "ResourceGroupName" -s "ServerName" 

      # get whether encryption is on or off
      az sql db tde show-configuration -n "DatabaseName" -g "ResourceGroupName" -s "ServerName" 

## <a name="sql-cli-references"></a>Reference k rozhraní příkazového řádku SQL

https://docs.microsoft.com/cli/azure/sql?view=azure-cli-latest 

https://docs.microsoft.com/cli/azure/sql/server/key?view=azure-cli-latest 

https://docs.microsoft.com/cli/azure/sql/server/tde-key?view=azure-cli-latest 

https://docs.microsoft.com/cli/azure/sql/db/tde?view=azure-cli-latest 

