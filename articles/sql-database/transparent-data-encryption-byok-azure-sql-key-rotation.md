---
title: PowerShell – ochrana otočit TDE – Azure SQL Database | Dokumentace Microsoftu
description: Zjistěte, jak otočit ochrana transparentní šifrování dat (TDE) pro server Azure SQL.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: aliceku
ms.author: aliceku
ms.reviewer: vanto
manager: jhubbard
ms.date: 08/07/2017
ms.openlocfilehash: fe8872e0db9f596ca7c58b087b1b061ae91f7384
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/06/2018
ms.locfileid: "52957551"
---
# <a name="rotate-the-transparent-data-encryption-tde-protector-using-powershell"></a>Otočit ochrana transparentní šifrování dat (TDE) pomocí Powershellu 

Tento článek popisuje obměna klíčů pro server Azure SQL pomocí ochrana TDE ze služby Azure Key Vault. Otáčení znamená ochranu transparentní šifrování dat serveru Azure SQL přepnutí na novou asymetrický klíč, který chrání databáze na serveru. Obměna klíčů je online operace a zabere jenom pár sekund dokončit, protože to pouze dešifruje a znovu zašifruje databáze šifrovacího klíče dat, ne celou databázi.

Tato příručka popisuje dvě možnosti, jak otočit ochrana TDE serveru.

> [!NOTE]
> Pozastavený SQL Data Warehouse musí obnovit před obměny klíče.
>

> [!IMPORTANT]
> **Provést odstranění** předchozí verze klíče po efekt přechodu.  Když jsou jednotlivě klíče, některá data stále šifrován dřívější klíče, jako je například starší zálohy databáze. 
>

## <a name="prerequisites"></a>Požadavky

- Tato příručka předpokládá, že už používáte klíče z trezoru klíčů Azure jako ochrana TDE pro služby Azure SQL Database a Data Warehouse. Zobrazit [transparentní šifrování dat s podporou modelu BYOK](transparent-data-encryption-byok-azure-sql.md).
- Musíte mít Azure PowerShell verze 3.7.0 nebo novější nainstalován a spuštěn. 
- [Nepovinné ale doporučeno] Vytvoření klíče materiál pro ochrana TDE v modulu hardwarového zabezpečení (HSM) nebo ukládání první místní klíč a importovat materiál klíče do služby Azure Key Vault. Postupujte podle [pokyny, jak pomocí modulu hardwarového zabezpečení (HSM) a služby Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-get-started) Další informace.

## <a name="option-1-auto-rotation"></a>Možnost 1: Automatické rotace

Generujte novou verzi existujícího ochranné zařízení klíče transparentní šifrování dat ve službě Key Vault za stejný název klíče služby key vault. Spuštění služby Azure SQL pomocí této nové verzi během 24 hodin. 

Vytvořit novou verzi transparentní šifrování dat pomocí ochrany [Add-AzureKeyVaultKey](/powershell/module/azurerm.keyvault/add-azurekeyvaultkey) rutiny:

   ```powershell
   Add-AzureKeyVaultKey `
   -VaultName <KeyVaultName> `
   -Name <KeyVaultKeyName> `
   -Destination <HardwareOrSoftware>
   ```

## <a name="option-2-manual-rotation"></a>Možnost 2: Ruční otáčení

Tato možnost používá [Add-AzureKeyVaultKey](/powershell/module/azurerm.keyvault/add-azurekeyvaultkey), [přidat AzureRmSqlServerKeyVaultKey](/powershell/module/azurerm.sql/add-azurermsqlserverkeyvaultkey), a [rutiny Set-AzureRmSqlServerTransparentDataEncryptionProtector](/powershell/module/azurerm.sql/set-azurermsqlservertransparentdataencryptionprotector) rutiny pro přidání úplně nový klíč, které by mohly být v rámci nového názvu klíče nebo dokonce pro jiný trezor klíčů. 

>[!NOTE]
>Celková délka pro název trezoru klíčů a název klíče nemůže být delší než 94 znaků.
>

   ```powershell
   # Add a new key to Key Vault
   Add-AzureKeyVaultKey `
   -VaultName <KeyVaultName> `
   -Name <KeyVaultKeyName> `
   -Destination <HardwareOrSoftware>

   # Add the new key from Key Vault to the server
   Add-AzureRmSqlServerKeyVaultKey `
   -KeyId <KeyVaultKeyId> `
   -ServerName <LogicalServerName> `
   -ResourceGroup <SQLDatabaseResourceGroupName>   
  
   <# Set the key as the TDE protector for all resources 
   under the server #>
   Set-AzureRmSqlServerTransparentDataEncryptionProtector `
   -Type AzureKeyVault `
   -KeyId <KeyVaultKeyId> `
   -ServerName <LogicalServerName> `
   -ResourceGroup <SQLDatabaseResourceGroupName>
   ```
  
## <a name="other-useful-powershell-cmdlets"></a>Další užitečné rutiny prostředí PowerShell

- Chcete-li přepnout ochrana TDE ze spravovaných do režimu BYOK, použijte [rutiny Set-AzureRmSqlServerTransparentDataEncryptionProtector](/powershell/module/azurerm.sql/set-azurermsqlservertransparentdataencryptionprotector) rutiny.

   ```powershell
   Set-AzureRmSqlServerTransparentDataEncryptionProtector `
   -Type AzureKeyVault `
   -KeyId <KeyVaultKeyId> `
   -ServerName <LogicalServerName> `
   -ResourceGroup <SQLDatabaseResourceGroupName>
   ```

- Chcete-li přepnout ochrana TDE z režimu BYOK pro spravovaná Microsoftem, použijte [rutiny Set-AzureRmSqlServerTransparentDataEncryptionProtector](/powershell/module/azurerm.sql/set-azurermsqlservertransparentdataencryptionprotector) rutiny.

   ```powershell
   Set-AzureRmSqlServerTransparentDataEncryptionProtector `
   -Type ServiceManaged `
   -ServerName <LogicalServerName> `
   -ResourceGroup <SQLDatabaseResourceGroupName> 
   ``` 

## <a name="next-steps"></a>Další postup

- V případě ohrožení zabezpečení, přečtěte si, jak odebrat potenciálně ohroženými ochrana TDE: [odebrání potenciálně ohroženými klíče](transparent-data-encryption-byok-azure-sql-remove-tde-protector.md) 

- Začínáme s přineste si vlastní klíč podpory pro transparentní šifrování dat: [zapnout transparentní šifrování dat pomocí vlastní klíče ze služby Key Vault pomocí Powershellu](transparent-data-encryption-byok-azure-sql-configure.md)