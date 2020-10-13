---
title: Migrace instance spravovaného certifikátem TDE
description: Migrace certifikátu ochrany šifrovacího klíče databáze databáze s transparentní šifrování dat do spravované instance Azure SQL
services: sql-database
ms.service: sql-managed-instance
ms.subservice: security
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: MladjoA
ms.author: mlandzic
ms.reviewer: sstein, jovanpop
ms.date: 07/21/2020
ms.openlocfilehash: 08adfd7b69d580f6a231f13f9fb2793d828e16a3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91618135"
---
# <a name="migrate-a-certificate-of-a-tde-protected-database-to-azure-sql-managed-instance"></a>Migrace certifikátu chráněné databáze TDE do spravované instance Azure SQL
[!INCLUDE[appliesto-sqlmi](../includes/appliesto-sqlmi.md)]

Pokud migrujete databázi chráněnou [transparentní šifrování dat (TDE)](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption) do spravované instance Azure SQL pomocí možnosti nativní obnovení, musí být před obnovením databáze migrován odpovídající certifikát z SQL Server instance. Tento článek vás provede procesem ruční migrace certifikátu do spravované instance Azure SQL:

> [!div class="checklist"]
>
> * Exportujte certifikát do souboru Personal Information Exchange (. pfx).
> * Extrahovat certifikát ze souboru do řetězce Base-64
> * Nahrajte ho pomocí rutiny PowerShellu.

Alternativní možnost použití plně spravované služby pro bezproblémové migrace databáze chráněné TDE a odpovídajícího certifikátu najdete v tématu [jak migrovat místní databázi do spravované instance Azure SQL pomocí Azure Database Migration Service](../../dms/tutorial-sql-server-to-managed-instance.md).

> [!IMPORTANT]
> Migrovaný certifikát se používá pouze pro obnovení databáze chráněné TDE. Po dokončení obnovení se migrované certifikát nahradí jiným modulem ochrany, buď certifikátem spravovaným službou, nebo asymetrický klíč z trezoru klíčů, a to v závislosti na typu TDE, který jste pro instanci nastavili.

## <a name="prerequisites"></a>Požadavky

K dokončení kroků v tomto článku budete potřebovat následující:

* Nástroj příkazového řádku [Pvk2pfx](https://docs.microsoft.com/windows-hardware/drivers/devtest/pvk2pfx) nainstalovaný na místním serveru nebo jiném počítači s přístupem k certifikátu, který se exportuje do souboru. Nástroj Pvk2Pfx je součástí sady [Enterprise Driver Kit pro Windows](https://docs.microsoft.com/windows-hardware/drivers/download-the-wdk), která obsahuje samostatné prostředí příkazového řádku.
* [Prostředí Windows PowerShell](/powershell/scripting/install/installing-windows-powershell) nainstalované ve verzi 5.0 nebo vyšší.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Ujistěte se, že máte následující:

* Je [nainstalovaný a aktualizovaný](https://docs.microsoft.com/powershell/azure/install-az-ps)modul Azure PowerShell.
* [AZ. SQL Module](https://www.powershellgallery.com/packages/Az.Sql).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

> [!IMPORTANT]
> Modul PowerShell Azure Resource Manager je stále podporován službou Azure SQL Managed instance, ale všechny budoucí vývojové prostředí jsou pro modul AZ. SQL. Tyto rutiny naleznete v tématu [AzureRM. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Argumenty pro příkazy v modulech AZ a v modulech AzureRM jsou v podstatě identické.

Spuštěním následujících příkazů v PowerShellu nainstalujte/aktualizujte modul:

```azurepowershell
Install-Module -Name Az.Sql
Update-Module -Name Az.Sql
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace rozhraní příkazového řádku Azure CLI](/cli/azure/install-azure-cli).

* * *

## <a name="export-the-tde-certificate-to-a-pfx-file"></a>Exportujte certifikát TDE do souboru. pfx.

Certifikát se dá exportovat přímo ze zdrojové SQL Server instance, nebo z úložiště certifikátů, pokud se tam udržuje.

### <a name="export-the-certificate-from-the-source-sql-server-instance"></a>Exportujte certifikát ze zdrojové SQL Server instance.

Pomocí následujících kroků exportujte certifikát s SQL Server Management Studio a převeďte ho do formátu. pfx. Obecné názvy *TDE_Cert* a *full_path* jsou používány pro certifikáty a názvy souborů a cesty prostřednictvím kroků. Nahraďte je skutečnými názvy.

1. V SSMS otevřete nové okno dotazu a připojte se ke zdrojové instanci SQL Server.

1. Pomocí následujícího skriptu můžete vypsat databáze chráněné TDE a získat název certifikátu, který chrání šifrování databáze, která se má migrovat:

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

   ![Seznam certifikátů TDE](./media/tde-certificate-migrate/onprem-certificate-list.png)

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

   ![Záložní certifikát TDE](./media/tde-certificate-migrate/backup-onprem-certificate.png)

1. Pomocí konzoly PowerShellu zkopírujte informace o certifikátu z páru nově vytvořených souborů do souboru. pfx pomocí nástroje Pvk2Pfx:

   ```cmd
   .\pvk2pfx -pvk c:/full_path/TDE_Cert.pvk  -pi "<SomeStrongPassword>" -spc c:/full_path/TDE_Cert.cer -pfx c:/full_path/TDE_Cert.pfx
   ```

### <a name="export-the-certificate-from-a-certificate-store"></a>Exportujte certifikát z úložiště certifikátů.

Pokud je certifikát uložený v úložišti certifikátů místního počítače SQL Server, můžete ho exportovat pomocí následujících kroků:

1. Otevřete konzolu prostředí PowerShell a spuštěním následujícího příkazu otevřete modul snap-in Certifikáty konzoly Microsoft Management Console:

   ```cmd
   certlm
   ```

2. V modulu snap-in Certifikáty konzoly MMC rozbalte položku cesta osobní > certifikáty, abyste viděli seznam certifikátů.

3. Klikněte na certifikát pravým tlačítkem a klikněte na **exportovat**.

4. Postupujte podle pokynů průvodce a exportujte certifikát a soukromý klíč do formátu. pfx.

## <a name="upload-the-certificate-to-azure-sql-managed-instance-using-an-azure-powershell-cmdlet"></a>Nahrajte certifikát do spravované instance Azure SQL pomocí rutiny Azure PowerShell.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Začněte s přípravnými kroky v prostředí PowerShell:

   ```azurepowershell
   # import the module into the PowerShell session
   Import-Module Az
   # connect to Azure with an interactive dialog for sign-in
   Connect-AzAccount
   # list subscriptions available and copy id of the subscription target the managed instance belongs to
   Get-AzSubscription
   # set subscription for the session
   Select-AzSubscription <subscriptionId>
   ```

2. Po dokončení všech kroků přípravy spusťte následující příkazy, které Nahrajte certifikát s kódováním Base-64 do cílové spravované instance:

   ```azurepowershell
   $fileContentBytes = Get-Content 'C:/full_path/TDE_Cert.pfx' -AsByteStream
   $base64EncodedCert = [System.Convert]::ToBase64String($fileContentBytes)
   $securePrivateBlob = $base64EncodedCert  | ConvertTo-SecureString -AsPlainText -Force
   $password = "<password>"
   $securePassword = $password | ConvertTo-SecureString -AsPlainText -Force
   Add-AzSqlManagedInstanceTransparentDataEncryptionCertificate -ResourceGroupName "<resourceGroupName>" `
       -ManagedInstanceName "<managedInstanceName>" -PrivateBlob $securePrivateBlob -Password $securePassword
   ```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Nejdřív musíte [nastavit Trezor klíčů Azure](/azure/key-vault/key-vault-manage-with-cli2) pomocí souboru *. pfx* .

1. Začněte s přípravnými kroky v prostředí PowerShell:

   ```azurecli
   # connect to Azure with an interactive dialog for sign-in
   az login

   # list subscriptions available and copy id of the subscription target the managed instance belongs to
   az account list

   # set subscription for the session
   az account set --subscription <subscriptionId>
   ```

1. Po dokončení všech kroků přípravy spusťte následující příkazy, které Nahrajte certifikát s kódováním Base-64 do cílové spravované instance:

   ```azurecli
   az sql mi tde-key set --server-key-type AzureKeyVault --kid "<keyVaultId>" `
       --managed-instance "<managedInstanceName>" --resource-group "<resourceGroupName>"
   ```

* * *

Certifikát je teď k dispozici zadané spravované instanci a zálohování odpovídající databáze chráněné TDE se dá úspěšně obnovit.

## <a name="next-steps"></a>Další kroky

V tomto článku jste zjistili, jak migrovat certifikát chránící šifrovací klíč databáze pomocí transparentní šifrování dat, z místní nebo IaaS SQL Server instance do spravované instance Azure SQL.

Informace o tom, jak obnovit zálohu databáze do spravované instance Azure SQL, najdete v tématu [obnovení zálohy databáze do spravované instance Azure SQL](restore-sample-database-quickstart.md) .
