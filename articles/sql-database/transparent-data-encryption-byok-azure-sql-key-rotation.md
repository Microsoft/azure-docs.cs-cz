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
ms.date: 03/12/2019
ms.openlocfilehash: c3e982c0fc46ea72692d5fa7f27e14b88c6383df
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/20/2019
ms.locfileid: "67274267"
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

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Modul Azure PowerShell – Resource Manager je stále podporuje Azure SQL Database, ale všechny budoucí vývoj je Az.Sql modulu. Tyto rutiny najdete v části [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Argumenty pro příkazy v modulu Az a moduly AzureRm podstatně totožné.

- Tato příručka předpokládá, že už používáte klíče z trezoru klíčů Azure jako ochrana TDE pro služby Azure SQL Database a Data Warehouse. Zobrazit [transparentní šifrování dat s podporou modelu BYOK](transparent-data-encryption-byok-azure-sql.md).
- Musíte mít Azure PowerShell nainstalovaný a spuštěný.
- [Nepovinné ale doporučeno] Vytvoření klíče materiál pro ochrana TDE v modulu hardwarového zabezpečení (HSM) nebo ukládání první místní klíč a importovat materiál klíče do služby Azure Key Vault. Postupujte podle [pokyny, jak pomocí modulu hardwarového zabezpečení (HSM) a služby Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-get-started) Další informace.

## <a name="manual-key-rotation"></a>Ruční obměna klíčů

Ruční obměna klíčů používá [AzKeyVaultKey přidat](/powershell/module/az.keyvault/Add-AzKeyVaultKey), [AzSqlServerKeyVaultKey přidat](/powershell/module/az.sql/add-azsqlserverkeyvaultkey), a [Set-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) rutiny pro přidání úplně nový klíč, který může být v rámci nového názvu klíče nebo dokonce pro jiný trezor klíčů. Tento přístup podporuje přidávání stejný klíč do jiných trezorech klíčů pro zajištění podpory scénářů vysoké dostupnosti a geo-dr.

>[!NOTE]
>Celková délka pro název trezoru klíčů a název klíče nemůže být delší než 94 znaků.

   ```powershell
   # Add a new key to Key Vault
   Add-AzKeyVaultKey `
   -VaultName <KeyVaultName> `
   -Name <KeyVaultKeyName> `
   -Destination <HardwareOrSoftware>

   # Add the new key from Key Vault to the server
   Add-AzSqlServerKeyVaultKey `
   -KeyId <KeyVaultKeyId> `
   -ServerName <LogicalServerName> `
   -ResourceGroup <SQLDatabaseResourceGroupName>
  
   <# Set the key as the TDE protector for all resources under the server #>
   Set-AzSqlServerTransparentDataEncryptionProtector `
   -Type AzureKeyVault `
   -KeyId <KeyVaultKeyId> `
   -ServerName <LogicalServerName> `
   -ResourceGroup <SQLDatabaseResourceGroupName>
   ```


## <a name="other-useful-powershell-cmdlets"></a>Další užitečné rutiny prostředí PowerShell

- Chcete-li přepnout ochrana TDE ze spravovaných do režimu BYOK, použijte [Set-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) rutiny.

   ```powershell
   Set-AzSqlServerTransparentDataEncryptionProtector `
   -Type AzureKeyVault `
   -KeyId <KeyVaultKeyId> `
   -ServerName <LogicalServerName> `
   -ResourceGroup <SQLDatabaseResourceGroupName>
   ```

- Chcete-li přepnout ochrana TDE z režimu BYOK pro spravovaná Microsoftem, použijte [Set-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) rutiny.

   ```powershell
   Set-AzSqlServerTransparentDataEncryptionProtector `
   -Type ServiceManaged `
   -ServerName <LogicalServerName> `
   -ResourceGroup <SQLDatabaseResourceGroupName> 
   ``` 

## <a name="next-steps"></a>Další postup

- V případě ohrožení zabezpečení zjistěte, jak odebrat potenciálně ohroženými ochrana TDE: [Odebrání potenciálně ohroženými klíče](transparent-data-encryption-byok-azure-sql-remove-tde-protector.md) 

- Začínáme s Azure Key Vault integrace a podpora vlastního klíče pro transparentní šifrování dat: [Zapnout transparentní šifrování dat pomocí vlastní klíče ze služby Key Vault pomocí Powershellu](transparent-data-encryption-byok-azure-sql-configure.md)
