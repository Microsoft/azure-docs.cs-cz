---
title: Otočení ochrany TDE – PowerShell
description: Naučte se, jak otočit ochranu transparentní šifrování dat (TDE) pro Azure SQL Server.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
author: aliceku
ms.author: aliceku
ms.reviewer: vanto
ms.date: 03/12/2019
ms.openlocfilehash: 5bfcacb9348940e0b36947f6e4e0d27839de35bb
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/08/2019
ms.locfileid: "73824687"
---
# <a name="rotate-the-transparent-data-encryption-tde-protector-using-powershell"></a>Otočení ochrany transparentní šifrování dat (TDE) pomocí prostředí PowerShell

Tento článek popisuje střídání klíčů pro Azure SQL Server pomocí ochrany TDE před Azure Key Vault. Otáčení ochrany TDE serveru Azure SQL Server znamená přechod na nový asymetrický klíč, který chrání databáze na serveru. Střídání klíčů je online operace, kterou je potřeba provést jenom pár sekund, protože se tím dešifruje a znovu zašifruje šifrovací klíč dat databáze, ne celá databáze.

Tato příručka popisuje dvě možnosti, jak na serveru otočit ochranu TDE.

> [!NOTE]
> Před otočením klíče je nutné obnovit pozastavený SQL Data Warehouse.
>

> [!IMPORTANT]
> Po přecházení **neodstraňujte** předchozí verze klíče.  Když se převezmou klíče, některá data se zašifrují s předchozími klíči, jako je třeba starší zálohy databáze. 
>

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Modul PowerShell Azure Resource Manager je stále podporován Azure SQL Database, ale všechny budoucí vývojové prostředí jsou pro modul AZ. SQL. Tyto rutiny naleznete v tématu [AzureRM. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Argumenty pro příkazy v modulech AZ a v modulech AzureRm jsou v podstatě identické.

- V tomto průvodci se předpokládá, že už používáte klíč z Azure Key Vault jako ochranu TDE pro Azure SQL Database nebo datový sklad. Viz [transparentní šifrování dat s podporou BYOK](transparent-data-encryption-byok-azure-sql.md).
- Musíte mít nainstalovanou a spuštěnou Azure PowerShell.
- [Doporučené, ale volitelné] V modulu hardwarového zabezpečení (HSM) nebo v místním úložišti klíčů vytvořte materiál klíče pro ochranu TDE a naimportujte klíč klíče do Azure Key Vault. Pokud se chcete dozvědět víc, postupujte podle [pokynů k použití modulu hardwarového zabezpečení (HSM) a Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-get-started) .

## <a name="manual-key-rotation"></a>Ruční střídání klíčů

Ruční střídání klíčů používá rutiny [Add-AzKeyVaultKey](/powershell/module/az.keyvault/Add-AzKeyVaultKey), [Add-AzSqlServerKeyVaultKey](/powershell/module/az.sql/add-azsqlserverkeyvaultkey)a [set-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) k přidání zcela nového klíče, který může být pod novým názvem klíče nebo dokonce jiným klíčem. hesel. Použití tohoto přístupu podporuje přidávání stejného klíče do různých trezorů klíčů pro podporu scénářů s vysokou dostupností a geografickým zotavením po havárii.

>[!NOTE]
>Celková délka názvu trezoru klíčů a názvu klíče nesmí překročit 94 znaků.

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

- Chcete-li přepnout ochranu TDE ze spravovaného Microsoftem do režimu BYOK, použijte rutinu [set-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) .

   ```powershell
   Set-AzSqlServerTransparentDataEncryptionProtector `
   -Type AzureKeyVault `
   -KeyId <KeyVaultKeyId> `
   -ServerName <LogicalServerName> `
   -ResourceGroup <SQLDatabaseResourceGroupName>
   ```

- Chcete-li přepnout ochranu TDE z režimu BYOK na spravované Microsoftem, použijte rutinu [set-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) .

   ```powershell
   Set-AzSqlServerTransparentDataEncryptionProtector `
   -Type ServiceManaged `
   -ServerName <LogicalServerName> `
   -ResourceGroup <SQLDatabaseResourceGroupName> 
   ``` 

## <a name="next-steps"></a>Další kroky

- V případě bezpečnostního rizika se dozvíte, jak odebrat potenciálně ohrožený TDE ochranu: [Odebrání potenciálně ohroženého klíče](transparent-data-encryption-byok-azure-sql-remove-tde-protector.md) . 

- Začínáme s Azure Key Vault integrací a podporou Bring Your Own Key pro TDE: [Zapnutí TDE pomocí vlastního klíče z Key Vault pomocí PowerShellu](transparent-data-encryption-byok-azure-sql-configure.md)
