---
title: Ochrana Otáčení TDE - PowerShell
description: Přečtěte si, jak otočit ochranu transparentního šifrování dat (TDE) pro server Azure SQL.
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
ms.openlocfilehash: aaed06ac086893f63fde530e46b936b3fb637766
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80067168"
---
# <a name="rotate-the-transparent-data-encryption-tde-protector-using-powershell"></a>Otočení ochrany transparentního šifrování dat (TDE) pomocí prostředí PowerShell

Tento článek popisuje střídání klíčů pro server Azure SQL pomocí ochrany TDE z Trezoru klíčů Azure. Otočení ochrany TDE serveru Azure SQL znamená přepnutí na nový asymetrický klíč, který chrání databáze na serveru. Střídání klíčů je operace online a mělo by trvat pouze několik sekund, protože to pouze dešifruje a znovu šifruje šifrovací klíč databáze, nikoli celou databázi.

Tato příručka popisuje dvě možnosti otočit ochrana TDE na serveru.

> [!NOTE]
> Pozastavený datový sklad SQL musí být obnoven před otočením klíče.

> [!IMPORTANT]
> Neodstraňujte předchozí verze klíče po přechodu. Při přehražení klíčů jsou některá data stále šifrována pomocí předchozích klíčů, například starších záloh databáze.

## <a name="prerequisites"></a>Požadavky

- Tento návod předpokládá, že už používáte klíč z Azure Key Vault jako ochrana TDE pro Azure SQL Database nebo datového skladu. Viz [Transparentní šifrování dat s podporou BYOK](transparent-data-encryption-byok-azure-sql.md).
- Azure PowerShell musí být nainstalovaný a spuštěný.
- [Doporučeno, ale volitelné] Vytvořte materiál klíče pro ochranu TDE v modulu hardwarového zabezpečení (HSM) nebo místní klíč úložiště první a importovat materiál klíče do trezoru klíčů Azure. Další informace najdete v [pokynech pro použití modulu hardwarového zabezpečení (HSM) a Trezoru klíčů.](https://docs.microsoft.com/azure/key-vault/key-vault-get-started)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Pokyny k instalaci modulu Az najdete v tématu věnovaném [instalaci Azure PowerShellu](/powershell/azure/install-az-ps). Konkrétní rutiny najdete v tématu [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/).

> [!IMPORTANT]
> Modul Správce prostředků Azure (RM) prostředí PowerShell je stále podporovaný službou Azure SQL Database, ale veškerý budoucí vývoj je pro modul Az.Sql. Modul AzureRM bude nadále dostávat opravy chyb nejméně do prosince 2020.  Argumenty pro příkazy v modulu Az a v modulech AzureRm jsou v podstatě identické. Další informace o jejich kompatibilitě [najdete v tématu Představení nového modulu Azure PowerShell Az](/powershell/azure/new-azureps-module-az).

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Informace o instalaci najdete [v tématu Instalace příkazového příkazového příkazu k webu Azure](/cli/azure/install-azure-cli).

* * *

## <a name="manual-key-rotation"></a>Ruční střídání klíčů

Ruční střídání klíčů používá následující příkazy k přidání zcela nového klíče, který může být pod novým názvem klíče nebo dokonce jiným trezorem klíčů. Použití tohoto přístupu podporuje přidání stejného klíče do různých trezorů klíčů pro podporu scénářů s vysokou dostupností a geografickou dr.

> [!NOTE]
> Kombinovaná délka názvu trezoru klíčů a názvu klíče nesmí přesáhnout 94 znaků.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Použijte rutiny [Add-AzKeyVaultKey](/powershell/module/az.keyvault/Add-AzKeyVaultKey), [Add-AzSqlServerKeyVaultKey](/powershell/module/az.sql/add-azsqlserverkeyvaultkey)a [Set-AzSqlServerTransparentDataEncryptionProtector.](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector)

```powershell
# add a new key to Key Vault
Add-AzKeyVaultKey -VaultName <keyVaultName> -Name <keyVaultKeyName> -Destination <hardwareOrSoftware>

# add the new key from Key Vault to the server
Add-AzSqlServerKeyVaultKey -KeyId <keyVaultKeyId> -ServerName <logicalServerName> -ResourceGroup <SQLDatabaseResourceGroupName>
  
# set the key as the TDE protector for all resources under the server
Set-AzSqlServerTransparentDataEncryptionProtector -Type AzureKeyVault -KeyId <keyVaultKeyId> `
   -ServerName <logicalServerName> -ResourceGroup <SQLDatabaseResourceGroupName>
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Použijte příkazy [az keyvault vytvořit](/cli/azure/keyvault/key#az-keyvault-key-create), [az sql server klíč vytvořit](/cli/azure/sql/server/key#az-sql-server-key-create)a [az sql server tde-key-key set](/cli/azure/sql/server/tde-key#az-sql-server-tde-key-set) příkazy.

```azurecli
# add a new key to Key Vault
az keyvault key create --name <keyVaultKeyName> --vault-name <keyVaultName> --protection <hsmOrSoftware>

# add the new key from Key Vault to the server
az sql server key create --kid <keyVaultKeyId> --resource-group <SQLDatabaseResourceGroupName> --server <logicalServerName>

# set the key as the TDE protector for all resources under the server
az sql server tde-key set --server-key-type AzureKeyVault --kid <keyVaultKeyId> --resource-group <SQLDatabaseResourceGroupName> --server <logicalServerName>
```

* * *

## <a name="useful-powershell-cmdlets"></a>Užitečné rutiny prostředí PowerShell

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

- Chcete-li přepnout ochranu TDE z režimu Microsoft spravovaného do režimu BYOK, použijte rutinu [Set-AzSqlServerTransparentDataEncryptionProtector.](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector)

   ```powershell
   Set-AzSqlServerTransparentDataEncryptionProtector -Type AzureKeyVault `
       -KeyId <keyVaultKeyId> -ServerName <logicalServerName> -ResourceGroup <SQLDatabaseResourceGroupName>
   ```

- Chcete-li přepnout ochranu TDE z režimu BYOK na spravovanou společností Microsoft, použijte rutinu [Set-AzSqlServerTransparentDataEncryptionProtector.](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector)

   ```powershell
   Set-AzSqlServerTransparentDataEncryptionProtector -Type ServiceManaged `
       -ServerName <logicalServerName> -ResourceGroup <SQLDatabaseResourceGroupName>
   ```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Následující příklady používají [sadu tde-key az sql server](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector).

- Chcete-li přepnutí chrániče TDE z režimu BYOK spravovaného společností Microsoft,

   ```azurecli
   az sql server tde-key set --server-key-type AzureKeyVault --kid <keyVaultKeyId> --resource-group <SQLDatabaseResourceGroupName> --server <logicalServerName>
   ```

- Chcete-li přepnutí chrániče TDE z režimu BYOK do režimu Microsoft

   ```azurecli
   az sql server tde-key set --server-key-type ServiceManaged --resource-group <SQLDatabaseResourceGroupName> --server <logicalServerName>
   ```

* * *

## <a name="next-steps"></a>Další kroky

- V případě bezpečnostního rizika se dozvíte, jak odebrat potenciálně narušený chránič TDE: [Odeberte potenciálně narušený klíč.](transparent-data-encryption-byok-azure-sql-remove-tde-protector.md)

- Začínáme s integrací Azure Key Vault a přineste si vlastní podporu klíče pro TDE: [Zapněte TDE pomocí vlastního klíče z trezoru klíčů pomocí Prostředí PowerShell](transparent-data-encryption-byok-azure-sql-configure.md)
