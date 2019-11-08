---
title: Odebrání ochrany TDE – PowerShell
description: Návod, jak reagovat na potenciálně ohrožené ochrany TDE pro Azure SQL Database nebo datový sklad pomocí TDE s podporou BYOK (Přineste si vlastní klíč).
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
ms.openlocfilehash: df1bf5a53cd5c49465acbe363c71a4a316cd2cc9
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/08/2019
ms.locfileid: "73820779"
---
# <a name="remove-a-transparent-data-encryption-tde-protector-using-powershell"></a>Odebrání ochrany transparentní šifrování dat (TDE) pomocí prostředí PowerShell

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]
> [!IMPORTANT]
> Modul PowerShell Azure Resource Manager je stále podporován Azure SQL Database, ale všechny budoucí vývojové prostředí jsou pro modul AZ. SQL. Tyto rutiny naleznete v tématu [AzureRM. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Argumenty pro příkazy v modulech AZ a v modulech AzureRm jsou v podstatě identické.

- Musíte mít předplatné Azure a mít oprávnění správce k tomuto předplatnému.
- Musíte mít nainstalovanou a spuštěnou Azure PowerShell. 
- V tomto průvodci se předpokládá, že už používáte klíč z Azure Key Vault jako ochranu TDE pro Azure SQL Database nebo datový sklad. Další informace najdete v tématu [transparentní šifrování dat s podporou BYOK](transparent-data-encryption-byok-azure-sql.md) .

## <a name="overview"></a>Přehled

Tato příručka popisuje, jak reagovat na potenciálně ohrožené ochrany TDE pro Azure SQL Database nebo datový sklad, který používá TDE s klíči spravovanými zákazníky v podpoře BYOK (Azure Key Vault-Bring Your Own Key). Další informace o podpoře BYOK pro TDE najdete na stránce s [přehledem](transparent-data-encryption-byok-azure-sql.md). 

Následující postupy by měly být provedeny pouze v extrémních případech nebo v testovacích prostředích. Projděte si příručku návody, protože odstranění aktivně používaných TDE ochran z Azure Key Vault může způsobit **ztrátu dat**. 

Pokud dojde k ohrožení bezpečnosti nějakého klíče, aby služba nebo uživatel měli k klíči neoprávněný přístup, je nejlepší klíč odstranit.

Mějte na paměti, že jakmile se ochrana TDE v Key Vault odstraní, **všechna připojení k šifrovaným databázím na serveru jsou blokovaná a tyto databáze budou přecházet do režimu offline a budou se zahodit do 24 hodin**. Staré zálohy šifrované s ohroženým klíčem již nejsou přístupné.

Následující kroky popisují, jak kontrolovat kryptografické otisky ochrany TDE, které pořád používají virtuální soubory protokolů (VLF) dané databáze. Kryptografický otisk aktuálního TDE ochrany databáze a ID databáze lze najít spuštěním: SELECT [database_id],       [encryption_state], [encryptor_type],/*asymetrický klíč znamená integrace, certifikát znamená klíče spravované službou*/[ encryptor_thumbprint], z [sys]. [dm_database_encryption_keys] 
 
Následující dotaz vrátí VLFs a příslušné kryptografické otisky používané pro šifrování. Každý jiný kryptografický otisk odkazuje na jiný klíč v Azure Key Vault (integrace): SELECT * FROM sys. dm_db_log_info (database_id) 

Příkaz PowerShellu Get-AzureRmSqlServerKeyVaultKey poskytuje kryptografický otisk ochrany TDE, která se používá v dotazu, abyste viděli, které klíče se mají zachovat a které klíče se mají odstranit v integrace. Z Azure Key Vault můžete bezpečně odstranit jenom klíče, které už nepoužívá databáze.

Tato příručka přechází přes dvě přístupy v závislosti na požadovaném výsledku po reakci na incident:

- Zachování **přístupu** k databázím SQL Azure a datovým skladům
- **Přístup** k databázím SQL Azure a datovým skladům

## <a name="to-keep-the-encrypted-resources-accessible"></a>Zachování přístupných šifrovaných prostředků

1. Vytvořte [nový klíč v Key Vault](/powershell/module/az.keyvault/add-azkeyvaultkey). Zajistěte, aby byl tento nový klíč vytvořen v jiném trezoru klíčů od potenciálně ohroženého ochrany TDE, protože řízení přístupu je zřízené na úrovni trezoru.
2. Přidejte nový klíč na server pomocí rutin [Add-AzSqlServerKeyVaultKey](/powershell/module/az.sql/add-azsqlserverkeyvaultkey) a [set-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/set-azsqlservertransparentdataencryptionprotector) a aktualizujte ho jako nový TDE ochrany serveru.

   ```powershell
   # Add the key from Key Vault to the server  
   Add-AzSqlServerKeyVaultKey `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> `
   -KeyId <KeyVaultKeyId>

   # Set the key as the TDE protector for all resources under the server
   Set-AzSqlServerTransparentDataEncryptionProtector `
   -ResourceGroupName <SQLDatabaseResourceGroupName> `
   -ServerName <LogicalServerName> `
   -Type AzureKeyVault -KeyId <KeyVaultKeyId> 
   ```

3. Zajistěte, aby byl server a všechny repliky v nové ochraně TDE pomocí rutiny [Get-AzSqlServerTransparentDataEncryptionProtector](/powershell/module/az.sql/get-azsqlservertransparentdataencryptionprotector) . 

   >[!NOTE]
   > Novému stráži TDE může trvat několik minut, než se došíří do všech databází a sekundárních databází v rámci serveru.

   ```powershell
   Get-AzSqlServerTransparentDataEncryptionProtector `
   -ServerName <LogicalServerName> `
   -ResourceGroupName <SQLDatabaseResourceGroupName>
   ```

4. Proveďte [zálohu nového klíče](/powershell/module/az.keyvault/backup-azkeyvaultkey) v Key Vault.

   ```powershell
   <# -OutputFile parameter is optional; 
   if removed, a file name is automatically generated. #>
   Backup-AzKeyVaultKey `
   -VaultName <KeyVaultName> `
   -Name <KeyVaultKeyName> `
   -OutputFile <DesiredBackupFilePath>
   ```
 
5. Odstraňte ohrožený klíč z Key Vault pomocí rutiny [Remove-AzKeyVaultKey](/powershell/module/az.keyvault/remove-azkeyvaultkey) . 

   ```powershell
   Remove-AzKeyVaultKey `
   -VaultName <KeyVaultName> `
   -Name <KeyVaultKeyName>
   ```
 
6. Postup obnovení klíče pro Key Vault v budoucnu pomocí rutiny [Restore-AzKeyVaultKey](/powershell/module/az.keyvault/restore-azkeyvaultkey) :
   ```powershell
   Restore-AzKeyVaultKey `
   -VaultName <KeyVaultName> `
   -InputFile <BackupFilePath>
   ```

## <a name="to-make-the-encrypted-resources-inaccessible"></a>Zpřístupnění šifrovaných prostředků jako nepřístupné

1. Vyřaďte databáze šifrované potenciálně ohroženým klíčem.

   Databáze a soubory protokolu jsou automaticky zálohovány, takže obnovení databáze v určitém bodě v čase lze provést kdykoli (Pokud klíč zadáte). Databáze musí být před odstraněním ochrany aktivních TDE vyřazeny, aby se předešlo potenciální ztrátě dat až do 10 minut od poslední transakce. 
2. Zazálohujte klíčový materiál ochrany TDE v Key Vault.
3. Odebrat potenciálně ohrožený klíč z Key Vault

## <a name="next-steps"></a>Další kroky

- Naučte se, jak otočit ochranu TDE serveru tak, aby splňovala požadavky na zabezpečení: [otočení transparentní šifrování dat ochrany pomocí PowerShellu](transparent-data-encryption-byok-azure-sql-key-rotation.md)
- Začínáme s podporou Bring Your Own Key TDE: [Zapnutí TDE pomocí vlastního klíče z Key Vault pomocí PowerShellu](transparent-data-encryption-byok-azure-sql-configure.md)
