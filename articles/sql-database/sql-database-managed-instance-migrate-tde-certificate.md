---
title: Migrace certifikátu TDE – spravovaná instance
description: Migrace certifikátu chránícího databázový šifrovací klíč databáze pomocí transparentního šifrování dat do spravované instance Azure SQL Database
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MladjoA
ms.author: mlandzic
ms.reviewer: carlrab, jovanpop
ms.date: 04/25/2019
ms.openlocfilehash: 0f6e379287323d9353acd887cf30d5c9c0065959
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74555389"
---
# <a name="migrate-certificate-of-tde-protected-database-to-azure-sql-database-managed-instance"></a>Migrace certifikátu databáze chráněné transparentním šifrováním dat do spravované instance Azure SQL

Při migraci databáze chráněné [transparentní šifrování dat](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption) do Azure SQL Database Spravované instance pomocí možnosti nativní obnovení, odpovídající certifikát z místního nebo IaaS SQL Server je třeba migrovat před obnovením databáze. Tento článek vás provede ručním procesem migrace certifikátu do spravované instance Azure SQL Database:

> [!div class="checklist"]
> * Export certifikátu do souboru Personal Information Exchange (.pfx)
> * Extrakce certifikátu ze souboru do řetězce base-64
> * Nahrání certifikátu pomocí rutiny PowerShell

Alternativní možnost migrace databáze s ochranou TDE i odpovídajícího certifikátu pomocí plně spravované služby najdete v článku [Jak migrovat místní databázi do spravované instance pomocí služby Azure Database Migration Service](../dms/tutorial-sql-server-to-managed-instance.md).

> [!IMPORTANT]
> Migrovaný certifikát se použije pouze k obnovení databáze chráněné transparentním šifrováním dat. Brzy po obnovení se migrovaný certifikát nahradí jiným nástrojem ochrany, certifikátem spravovatým službou nebo asymetrickým klíčem z trezoru klíčů, v závislosti na typu transparentního šifrování dat, které najdete v instanci.

## <a name="prerequisites"></a>Požadavky

K dokončení kroků v tomto článku budete potřebovat následující:

- Nástroj příkazového řádku [Pvk2pfx](https://docs.microsoft.com/windows-hardware/drivers/devtest/pvk2pfx) nainstalovaný na místním serveru nebo jiném počítači s přístupem k certifikátu, který se exportuje do souboru. Nástroj Pvk2pfx je součástí [Enterprise Windows Driver Kit](https://docs.microsoft.com/windows-hardware/drivers/download-the-wdk), samostatného a nezávislého prostředí příkazového řádku.
- [Prostředí Windows PowerShell](/powershell/scripting/install/installing-windows-powershell) nainstalované ve verzi 5.0 nebo vyšší.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Ujistěte se, že máte následující:

- Nainstalovaný [a aktualizovaný](https://docs.microsoft.com/powershell/azure/install-az-ps)modul Azure PowerShellu .
- [Modul Az.Sql](https://www.powershellgallery.com/packages/Az.Sql).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

> [!IMPORTANT]
> Modul PowerShell Azure Resource Manager je stále podporovaný službou Azure SQL Database, ale veškerý budoucí vývoj je pro modul Az.Sql. Tyto rutiny naleznete v tématu [AzureRM.Sql](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Argumenty pro příkazy v modulu Az a v modulech AzureRm jsou v podstatě identické.

Spusťte v PowerShellu následující příkazy k instalaci nebo aktualizaci modulu:

```azurepowershell
Install-Module -Name Az.Sql
Update-Module -Name Az.Sql
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace rozhraní příkazového řádku Azure CLI](/cli/azure/install-azure-cli).

* * *

## <a name="export-tde-certificate-to-a-personal-information-exchange-pfx-file"></a>Export certifikátu TDE do souboru Personal Information Exchange (.pfx)

Certifikát můžete exportovat přímo ze zdrojového systému SQL Server, nebo z úložiště certifikátů, pokud je v něm uložený.

### <a name="export-certificate-from-the-source-sql-server"></a>Export certifikátu ze zdrojového systému SQL Server

Následujícím postupem vyexportujete certifikát pomocí SQL Server Management Studia a převedete ho do formátu pfx. V postupu se používají zástupná jména *TDE_Cert* a *full_path* pro název soubor a certifikátu a pro cestu. Nahraďte je skutečnými názvy.

1. V SQL Server Management Studiu otevřete nové okno dotazu a připojte se ke zdrojovému systému SQL Server.

1. Pomocí následujícího skriptu vypište seznam databází chráněných pomocí TDE a zjistěte název certifikátu, kterým je zašifrovaná migrovaná databáze:

   ```sql
   USE master
   GO
   SELECT db.name as [database_name], cer.name as [certificate_name]
   FROM sys.dm_database_encryption_keys dek
   LEFT JOIN sys.certificates cer
   ON dek.encryptor_thumbprint = cer.thumbprint
   INNER JOIN sys.databases db
   ON dek.database_id = db.database_id
   WHERE dek.encryption_state = 3
   ```

   ![seznam certifikátů TDE](./media/sql-database-managed-instance-migrate-tde-certificate/onprem-certificate-list.png)

1. Následujícím skriptem exportujte certifikát do dvojice souborů (.cer a .pvk), které obsahují veřejný a privátní klíč:

   ```sql
   USE master
   GO
   BACKUP CERTIFICATE TDE_Cert
   TO FILE = 'c:\full_path\TDE_Cert.cer'
   WITH PRIVATE KEY (
     FILE = 'c:\full_path\TDE_Cert.pvk',
     ENCRYPTION BY PASSWORD = '<SomeStrongPassword>'
   )
   ```

   ![certifikát TDE zálohy](./media/sql-database-managed-instance-migrate-tde-certificate/backup-onprem-certificate.png)

1. V konzole PowerShell zkopírujte data certifikátu z dvojice nově vytvořených souborů do souboru Personal Information Exchange (.pfx) pomocí nástroje Pvk2Pfx:

   ```cmd
   .\pvk2pfx -pvk c:/full_path/TDE_Cert.pvk  -pi "<SomeStrongPassword>" -spc c:/full_path/TDE_Cert.cer -pfx c:/full_path/TDE_Cert.pfx
   ```

### <a name="export-certificate-from-certificate-store"></a>Export certifikátu z úložiště certifikátů

Pokud je certifikát v úložišti certifikátů místního počítače systému SQL Server, můžete ho exportovat následujícím postupem:

1. Otevřete konzolu PowerShell a následujícím příkazem otevřete modul snap-in konzoly pro správu Certifikáty:

   ```cmd
   certlm
   ```

2. V modulu snap-in Certifikáty konzoly MMC rozbalte podstrom Osobní -> Certifikáty a zobrazte seznam certifikátů

3. Klikněte pravým tlačítkem na certifikát a pak vyberte Exportovat…

4. Postupujte podle pokynů průvodce exportem certifikátu a privátní klíč exportujte do formátu Personal Information Exchange

## <a name="upload-certificate-to-azure-sql-database-managed-instance-using-azure-powershell-cmdlet"></a>Nahrání certifikátu do spravované instance Azure SQL Database pomocí rutiny Azure PowerShell

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Začněte s přípravnými kroky v prostředí PowerShell:

   ```azurepowershell
   # import the module into the PowerShell session
   Import-Module Az
   # connect to Azure with an interactive dialog for sign-in
   Connect-AzAccount
   # list subscriptions available and copy id of the subscription target Managed Instance belongs to
   Get-AzSubscription
   # set subscription for the session
   Select-AzSubscription <subscriptionId>
   ```

2. Jakmile bude vše připraveno, spuštěním následujících příkazů nahrajte certifikát v kódování base-64 do cílové spravované instance:

   ```azurepowershell
   $fileContentBytes = Get-Content 'C:/full_path/TDE_Cert.pfx' -Encoding Byte
   $base64EncodedCert = [System.Convert]::ToBase64String($fileContentBytes)
   $securePrivateBlob = $base64EncodedCert  | ConvertTo-SecureString -AsPlainText -Force
   $password = "<password>"
   $securePassword = $password | ConvertTo-SecureString -AsPlainText -Force
   Add-AzSqlManagedInstanceTransparentDataEncryptionCertificate -ResourceGroupName "<resourceGroupName>" `
       -ManagedInstanceName "<managedInstanceName>" -PrivateBlob $securePrivateBlob -Password $securePassword
   ```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Je třeba [nejprve nastavit Azure Key Vault](/azure/key-vault/key-vault-manage-with-cli2) se souborem *.pfx.*

1. Začněte s přípravnými kroky v prostředí PowerShell:

   ```azurecli
   # connect to Azure with an interactive dialog for sign-in
   az login

   # list subscriptions available and copy id of the subscription target Managed Instance belongs to
   az account list

   # set subscription for the session
   az account set --subscription <subscriptionId>
   ```

1. Jakmile bude vše připraveno, spuštěním následujících příkazů nahrajte certifikát v kódování base-64 do cílové spravované instance:

   ```azurecli
   az sql mi tde-key set --server-key-type AzureKeyVault --kid "<keyVaultId>" `
       --managed-instance "<managedInstanceName>" --resource-group "<resourceGroupName>"
   ```

* * *

Certifikát je nyní k dispozici pro zadanou spravovanou instanci a bude v ní možné obnovit zálohu databáze chráněné transparentním šifrováním dat.

## <a name="next-steps"></a>Další kroky

V tomto článku jste se dozvěděli, jak migrovat certifikát chránící šifrovací klíč databáze pomocí transparentního šifrování dat z místního serveru nebo serveru IaaS SQL Server do spravované instance Azure SQL Database.

V kurzu [Obnovení zálohy databáze do spravované instance Azure SQL Database](sql-database-managed-instance-get-started-restore.md) se naučíte, jak obnovit zálohu databáze do spravované instance Azure SQL Database.
