---
title: Otočení TDE ochrany (PowerShell & rozhraní příkazového řádku Azure)
titleSuffix: Azure SQL Database & Azure Synapse Analytics
description: Naučte se, jak otočit ochranu transparentní šifrování dat (TDE) pro server v Azure, který používají Azure SQL Database a Azure synapse Analytics pomocí PowerShellu a rozhraní příkazového řádku Azure.
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: seo-lt-2019 sqldbrb=1, devx-track-azurecli
ms.devlang: ''
ms.topic: how-to
author: shohamMSFT
ms.author: shohamd
ms.reviewer: vanto
ms.date: 03/12/2019
ms.openlocfilehash: 4bcfc0c7e24c83f5388e49af397d66f2bad7aa9f
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/13/2021
ms.locfileid: "107310933"
---
# <a name="rotate-the-transparent-data-encryption-tde-protector"></a>Otočení ochrany transparentní šifrování dat (TDE)
[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]


Tento článek popisuje střídání klíčů pro [Server](logical-servers.md) pomocí ochrany TDE před Azure Key Vault. Rotace ochrany logických TDE pro server znamená přechod na nový asymetrický klíč, který chrání databáze na serveru. Střídání klíčů je online operace, kterou je potřeba provést jenom pár sekund, protože se tím dešifruje a znovu zašifruje šifrovací klíč dat databáze, ne celá databáze.

Tato příručka popisuje dvě možnosti, jak na serveru otočit ochranu TDE.

> [!NOTE]
> Pozastavený vyhrazený fond SQL ve službě Azure synapse Analytics se musí před otočením klíčů obnovit.

> [!IMPORTANT]
> Po přecházení neodstraňujte předchozí verze klíče. Když se převezmou klíče, některá data se zašifrují s předchozími klíči, jako je třeba starší zálohy databáze.

## <a name="prerequisites"></a>Požadavky

- V tomto průvodci se předpokládá, že už používáte klíč z Azure Key Vault jako ochranu TDE pro Azure SQL Database nebo Azure synapse Analytics. Viz [transparentní šifrování dat s podporou BYOK](transparent-data-encryption-byok-overview.md).
- Musíte mít nainstalovanou a spuštěnou Azure PowerShell.
- [Doporučené, ale volitelné] V modulu hardwarového zabezpečení (HSM) nebo v místním úložišti klíčů vytvořte materiál klíče pro ochranu TDE a naimportujte klíč klíče do Azure Key Vault. Pokud se chcete dozvědět víc, postupujte podle [pokynů k použití modulu hardwarového zabezpečení (HSM) a Key Vault](../../key-vault/general/overview.md) .

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Pokyny k instalaci modulu Az najdete v tématu věnovaném [instalaci Azure PowerShellu](/powershell/azure/install-az-ps). Konkrétní rutiny naleznete v tématu [AzureRM. SQL](/powershell/module/AzureRM.Sql/).

> [!IMPORTANT]
> Modul Azure Resource Manager PowerShellu (RM) je stále podporován, ale všechny budoucí vývojové prostředí jsou pro modul AZ. SQL. V modulu AzureRM bude i nadále docházet k opravám chyb až do prosince 2020.  Argumenty pro příkazy v modulech AZ a v modulech AzureRm jsou v podstatě identické. Další informace o kompatibilitě najdete v tématu [představení nového Azure PowerShell AZ Module](/powershell/azure/new-azureps-module-az).

# <a name="the-azure-cli"></a>[Azure CLI](#tab/azure-cli)

Informace o instalaci najdete v tématu [instalace rozhraní příkazového řádku Azure CLI](/cli/azure/install-azure-cli).

* * *

## <a name="manual-key-rotation"></a>Ruční střídání klíčů

Ruční střídání klíčů používá následující příkazy k přidání zcela nového klíče, který může být pod novým názvem klíče nebo i jiným trezorem klíčů. Použití tohoto přístupu podporuje přidávání stejného klíče do různých trezorů klíčů pro podporu scénářů s vysokou dostupností a geografickým zotavením po havárii.

> [!NOTE]
> Celková délka názvu trezoru klíčů a názvu klíče nesmí překročit 94 znaků.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Použijte rutiny [Add-AzKeyVaultKey](/powershell/module/az.keyvault/Add-AzKeyVaultKey), [Add-AzSqlServerKeyVaultKey](/powershell/module/az.sql/add-azsqlserverkeyvaultkey)a [set-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) .

```powershell
# add a new key to Key Vault
Add-AzKeyVaultKey -VaultName <keyVaultName> -Name <keyVaultKeyName> -Destination <hardwareOrSoftware>

# add the new key from Key Vault to the server
Add-AzSqlServerKeyVaultKey -KeyId <keyVaultKeyId> -ServerName <logicalServerName> -ResourceGroup <SQLDatabaseResourceGroupName>
  
# set the key as the TDE protector for all resources under the server
Set-AzSqlServerTransparentDataEncryptionProtector -Type AzureKeyVault -KeyId <keyVaultKeyId> `
   -ServerName <logicalServerName> -ResourceGroup <SQLDatabaseResourceGroupName>
```

# <a name="the-azure-cli"></a>[Azure CLI](#tab/azure-cli)

Použijte příkaz [AZ Key trezor Create](/cli/azure/keyvault/key#az-keyvault-key-create), [AZ SQL Server Key Create](/cli/azure/sql/server/key#az-sql-server-key-create)a [AZ SQL Server TDE-Key set](/cli/azure/sql/server/tde-key#az-sql-server-tde-key-set) Commands.

```azurecli
# add a new key to Key Vault
az keyvault key create --name <keyVaultKeyName> --vault-name <keyVaultName> --protection <hsmOrSoftware>

# add the new key from Key Vault to the server
az sql server key create --kid <keyVaultKeyId> --resource-group <SQLDatabaseResourceGroupName> --server <logicalServerName>

# set the key as the TDE protector for all resources under the server
az sql server tde-key set --server-key-type AzureKeyVault --kid <keyVaultKeyId> --resource-group <SQLDatabaseResourceGroupName> --server <logicalServerName>
```

* * *

## <a name="switch-tde-protector-mode"></a>Přepnout režim ochrany TDE

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

- Chcete-li přepnout ochranu TDE ze spravovaného Microsoftem do režimu BYOK, použijte rutinu [set-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) .

   ```powershell
   Set-AzSqlServerTransparentDataEncryptionProtector -Type AzureKeyVault `
       -KeyId <keyVaultKeyId> -ServerName <logicalServerName> -ResourceGroup <SQLDatabaseResourceGroupName>
   ```

- Chcete-li přepnout ochranu TDE z režimu BYOK na spravované Microsoftem, použijte rutinu [set-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) .

   ```powershell
   Set-AzSqlServerTransparentDataEncryptionProtector -Type ServiceManaged `
       -ServerName <logicalServerName> -ResourceGroup <SQLDatabaseResourceGroupName>
   ```

# <a name="the-azure-cli"></a>[Azure CLI](#tab/azure-cli)

V následujících příkladech je použit [příkaz AZ SQL Server TDE-Key set](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector).

- Pokud chcete přepnout ochranu TDE ze spravovaného Microsoftem na režim BYOK,

   ```azurecli
   az sql server tde-key set --server-key-type AzureKeyVault --kid <keyVaultKeyId> --resource-group <SQLDatabaseResourceGroupName> --server <logicalServerName>
   ```

- Pokud chcete přepnout ochranu TDE z režimu BYOK na spravované Microsoftem,

   ```azurecli
   az sql server tde-key set --server-key-type ServiceManaged --resource-group <SQLDatabaseResourceGroupName> --server <logicalServerName>
   ```

* * *

## <a name="next-steps"></a>Další kroky

- V případě bezpečnostního rizika se dozvíte, jak odebrat potenciálně ohrožený TDE ochranu: [Odebrání potenciálně ohroženého klíče](transparent-data-encryption-byok-remove-tde-protector.md).

- Začínáme s Azure Key Vault integrací a podporou Bring Your Own Key pro TDE: [zapněte TDE pomocí vlastního klíče z Key Vault pomocí PowerShellu](transparent-data-encryption-byok-configure.md).