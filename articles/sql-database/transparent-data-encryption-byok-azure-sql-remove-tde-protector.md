---
title: -Odebrat ochrana TDE – prostředí PowerShell Azure SQL Database | Dokumentace Microsoftu
description: Průvodce pro reakce na potenciálně ohroženými ochrana TDE pro služby Azure SQL Database a Data Warehouse s využitím transparentní šifrování dat s podporou přineste si vlastní klíč (BYOK).
keywords: ''
services: sql-database
documentationcenter: ''
author: becczhang
manager: craigg
ms.prod: ''
ms.reviewer: vanto
ms.suite: sql
ms.prod_service: sql-database, sql-data-warehouse
ms.service: sql-database
ms.custom: ''
ms.tgt_pltfrm: ''
ms.topic: conceptual
ms.date: 08/07/2017
ms.author: rebeccaz
monikerRange: = azuresqldb-current || = azure-sqldw-latest || = sqlallproducts-allversions
ms.openlocfilehash: c7d7c9ae3a53783a9f1f7dcef2275e74814d07f3
ms.sourcegitcommit: f983187566d165bc8540fdec5650edcc51a6350a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/13/2018
ms.locfileid: "45543924"
---
# <a name="remove-a-transparent-data-encryption-tde-protector-using-powershell"></a>Odebrat ochranného transparentní šifrování dat (TDE) pomocí Powershellu
## <a name="prerequisites"></a>Požadavky
- Musíte mít předplatné Azure a mít oprávnění správce pro toto předplatné
- Musíte mít Azure PowerShell verze 4.2.0 nebo novější nainstalován a spuštěn. 
- Tato příručka předpokládá, že už používáte klíče z trezoru klíčů Azure jako ochrana TDE pro služby Azure SQL Database a Data Warehouse. Zobrazit [transparentního šifrování dat s podporou modelu BYOK](transparent-data-encryption-byok-azure-sql.md) Další informace.

## <a name="overview"></a>Přehled
Tato příručka popisuje, jak reagovat na potenciálně ohroženými ochrana TDE pro služby Azure SQL Database a datový sklad, který používá transparentní šifrování dat s podporou přineste si vlastní klíč (BYOK). Další informace o podpoře funkce BYOK pro transparentní šifrování dat, najdete v článku [stránka s přehledem](transparent-data-encryption-byok-azure-sql.md). 

Následující postupy lze provádět pouze ve výjimečných případech nebo v testovacích prostředích. Pečlivě zkontrolujte průvodce, protože odstranění aktivně používanými TDE ochrany z Azure Key Vault může vést k **ztrátě dat**. 

Pokud klíč je někdy podezření u něho ohrožena bezpečnost, tak, aby se služba nebo uživatel měl neoprávněný přístup ke klíči, je vhodné odstranit klíč.

Mějte na paměti, která jednou ochrana TDE je odstraněn ve službě Key Vault **všechna připojení k šifrovaným databázím na serveru jsou blokovány, a tyto databáze přejdou do režimu offline a během 24 hodin**. Starší zálohy šifrované pomocí ohrožený klíč už nejsou dostupné.

Tato příručka prochází přes dva přístupy v závislosti na požadovaný výsledek po reakce na incidenty:
- Zachovat Azure SQL Database / Data Warehouses **přístupné**
- Aby Azure SQL Database / Data Warehouses **nedostupný**

## <a name="to-keep-the-encrypted-resources-accessible"></a>Zachovat šifrovaná prostředky dostupné
1. Vytvoření [nový klíč ve službě Key Vault](https://docs.microsoft.com/powershell/module/azurerm.keyvault/add-azurekeyvaultkey?view=azurermps-4.1.0). Ujistěte se, že tento nový klíč je vytvořen v samostatné služby key vault z potenciálně napadeného ochrana TDE, protože je zřízený řízení přístupu na úrovni trezoru. 
2. Přidat nový klíč k serveru pomocí [přidat AzureRmSqlServerKeyVaultKey](/powershell/module/azurerm.sql/add-azurermsqlserverkeyvaultkey) a [rutiny Set-AzureRmSqlServerTransparentDataEncryptionProtector](/powershell/module/azurerm.sql/set-azurermsqlservertransparentdataencryptionprotector) rutiny a aktualizovat ho jako novou ochrana TDE serveru.

   ```powershell
   # Add the key from Key Vault to the server  
   Add-AzureRmSqlServerKeyVaultKey `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> `
   -KeyId <KeyVaultKeyId>
   
   # Set the key as the TDE protector for all resources under the server
   Set-AzureRmSqlServerTransparentDataEncryptionProtector `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> `
   -Type AzureKeyVault -KeyId <KeyVaultKeyId> 
   ```

3. Ujistěte se, že server a všechny repliky aktualizovaly na nový ochrana TDE pomocí [Get-AzureRmSqlServerTransparentDataEncryptionProtector](/powershell/module/azurerm.sql/get-azurermsqlservertransparentdataencryptionprotector) rutiny. 

   >[!NOTE]
   > Může trvat několik minut, než se nové ochrana TDE rozšíří do všech databází a sekundární databáze v rámci serveru.
   >

   ```powershell
   Get-AzureRmSqlServerTransparentDataEncryptionProtector `
   -ServerName <LogicalServerName> `
   -ResourceGroupName <SQLDatabaseResourceGroupName>
   ```

4. Přijmout [zálohování nového klíče](/powershell/module/azurerm.keyvault/backup-azurekeyvaultkey) ve službě Key Vault.

   ```powershell
   <# -OutputFile parameter is optional; 
   if removed, a file name is automatically generated. #>
   Backup-AzureKeyVaultKey `
   -VaultName <KeyVaultName> `
   -Name <KeyVaultKeyName> `
   -OutputFile <DesiredBackupFilePath>
   ```
 
5. Odstranit ohrožený klíč ze služby Key Vault pomocí [Remove-AzureKeyVaultKey](/powershell/module/azurerm.keyvault/remove-azurekeyvaultkey) rutiny. 

   ```powershell
   Remove-AzureKeyVaultKey `
   -VaultName <KeyVaultName> `
   -Name <KeyVaultKeyName>
   ```
 
6. Obnovení klíče do služby Key Vault v budoucnu pomocí [Restore-AzureKeyVaultKey](/powershell/module/azurerm.keyvault/restore-azurekeyvaultkey) rutiny:
   ```powershell
   Restore-AzureKeyVaultKey `
   -VaultName <KeyVaultName> `
   -InputFile <BackupFilePath>
   ```
 
## <a name="to-make-the-encrypted-resources-inaccessible"></a>Aby se mohly šifrované prostředky nedostupný
1. Databáze, které jsou šifrované pomocí klíče potenciálně ohroženými vyřaďte.
Soubory databáze a protokolu se automaticky zálohují, tak v okamžiku obnovení databáze lze provést v libovolném okamžiku (za předpokladu, můžete zadat klíč). Databáze musíte vyřadit před odstraněním ze active ochrana TDE, aby se zabránilo potenciální ztráty dat posledních transakcí až 10 minut. 
2. Zálohování klíče ochrana TDE ve službě Key Vault.
3. Odebrání potenciálně ohrožený klíč ze služby Key Vault

## <a name="next-steps"></a>Další postup

- Zjistěte, jak otočit ochrana TDE serveru k zajištění souladu s požadavky na zabezpečení: [otočit transparentního šifrování dat ochranu pomocí Powershellu](transparent-data-encryption-byok-azure-sql-key-rotation.md)

- Začínáme s přineste si vlastní klíč podpory pro transparentní šifrování dat: [zapnout transparentní šifrování dat pomocí vlastní klíče ze služby Key Vault pomocí Powershellu](transparent-data-encryption-byok-azure-sql-configure.md)
