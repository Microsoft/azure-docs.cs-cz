---
title: Migrace certifikátu TDE do spravované instance Azure SQL Database | Microsoft Docs
description: Přečtěte si, jak migrovat certifikát obsahující šifrovací klíč databáze chráněné transparentním šifrováním dat (TDE) do spravované instance Azure SQL
keywords: sql database tutorial, sql database managed instance, migrate TDE certificate
services: sql-database
author: MladjoA
ms.reviewer: carlrab, jovanpop
ms.service: sql-database
ms.custom: managed instance
ms.topic: tutorial
ms.date: 08/09/2018
ms.author: mlandzic
manager: craigg
ms.openlocfilehash: 73990d6feeed56114bc3c66164bbb53c093bbe21
ms.sourcegitcommit: ebd06cee3e78674ba9e6764ddc889fc5948060c4
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/07/2018
ms.locfileid: "44050607"
---
# <a name="migrate-certificate-of-tde-protected-database-to-azure-sql-managed-instance"></a>Migrace certifikátu databáze s ochranou TDE do spravované instance Azure SQL

Při migraci databáze chráněné [transparentním šifrováním dat](https://docs.microsoft.com/sql/relational-databases/security/encryption/transparent-data-encryption) (TDE) do spravované instance Azure SQL pomocí nativní možnosti obnovení je třeba před obnovením databáze migrovat odpovídající certifikát z místního nebo IaaS systému SQL Server. Tento článek vás provede ručním procesem migrace certifikátu do spravované instance Azure SQL Database:

> [!div class="checklist"]
> * Export certifikátu do souboru Personal Information Exchange (.pfx)
> * Extrakce certifikátu ze souboru do řetězce base-64
> * Nahrání certifikátu pomocí rutiny PowerShell

Alternativní možnost migrace databáze s ochranou TDE i odpovídajícího certifikátu pomocí plně spravované služby najdete v článku [Jak migrovat místní databázi do spravované instance pomocí služby Azure Database Migration Service](../dms/tutorial-sql-server-to-managed-instance.md).

> [!IMPORTANT]
> Transparentní šifrování dat funguje ve spravované instanci SQL Azure v režimu spravovaném službou. Migrovaný certifikát se použije pouze k obnovení databáze chráněné transparentním šifrováním dat. Brzy po dokončení obnovení se migrovaný certifikát nahradí jiným certifikátem spravovaným systémem.

## <a name="prerequisites"></a>Požadavky

K dokončení kroků v tomto článku budete potřebovat následující:

- Nástroj příkazového řádku [Pvk2pfx](https://docs.microsoft.com/windows-hardware/drivers/devtest/pvk2pfx) nainstalovaný na místním serveru nebo jiném počítači s přístupem k certifikátu, který se exportuje do souboru. Nástroj Pvk2pfx je součástí [Enterprise Windows Driver Kit](https://docs.microsoft.com/windows-hardware/drivers/download-the-wdk), samostatného a nezávislého prostředí příkazového řádku.
- [Prostředí Windows PowerShell](https://docs.microsoft.com/powershell/scripting/setup/installing-windows-powershell) nainstalované ve verzi 5.0 nebo vyšší.
- [Nainstalovaný a aktualizovaný](https://docs.microsoft.com/powershell/azure/install-azurerm-ps) modul AzureRM PowerShellu.
- [Modul AzureRM.Sql](https://www.powershellgallery.com/packages/AzureRM.Sql) verze 4.10.0 nebo novější.
  Modul PowerShell nainstalujte spuštěním následujících příkazů v prostředí PowerShell:

   ```powershell
   Install-Module -Name AzureRM.Sql
   Update-Module -Name AzureRM.Sql
   ```

## <a name="export-tde-certificate-to-a-personal-information-exchange-pfx-file"></a>Export certifikátu TDE do souboru Personal Information Exchange (.pfx)

Certifikát můžete exportovat přímo ze zdrojového systému SQL Server, nebo z úložiště certifikátů, pokud je v něm uložený.

### <a name="export-certificate-from-the-source-sql-server"></a>Export certifikátu ze zdrojového systému SQL Server

Následujícím postupem vyexportujete certifikát pomocí SQL Server Management Studia a převedete ho do formátu pfx. V postupu se používají zástupná jména *TDE_Cert* a *full_path* pro název soubor a certifikátu a pro cestu. Nahraďte je skutečnými názvy.

1. V SQL Server Management Studiu otevřete nové okno dotazu a připojte se ke zdrojovému systému SQL Server.
2. Pomocí následujícího skriptu vypište seznam databází chráněných pomocí TDE a zjistěte název certifikátu, kterým je zašifrovaná migrovaná databáze:

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

3. Následujícím skriptem exportujte certifikát do dvojice souborů (.cer a .pvk), které obsahují veřejný a privátní klíč:

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

4. V konzole PowerShell zkopírujte data certifikátu z dvojice nově vytvořených souborů do souboru Personal Information Exchange (.pfx) pomocí nástroje Pvk2Pfx:

   ```powershell
   .\pvk2pfx -pvk c:/full_path/TDE_Cert.pvk  -pi "<SomeStrongPassword>" -spc c:/full_path/TDE_Cert.cer -pfx c:/full_path/TDE_Cert.pfx
   ```

### <a name="export-certificate-from-certificate-store"></a>Export certifikátu z úložiště certifikátů

Pokud je certifikát v úložišti certifikátů místního počítače systému SQL Server, můžete ho exportovat následujícím postupem:

1. Otevřete konzolu PowerShell a následujícím příkazem otevřete modul snap-in konzoly pro správu Certifikáty:

   ```powershell
   certlm
   ```

2. V modulu snap-in Certifikáty konzoly MMC rozbalte podstrom Osobní -> Certifikáty a zobrazte seznam certifikátů

3. Klikněte pravým tlačítkem na certifikát a pak vyberte Exportovat…

4. Postupujte podle pokynů průvodce exportem certifikátu a privátní klíč exportujte do formátu Personal Information Exchange

## <a name="upload-certificate-to-azure-sql-managed-instance-using-azure-powershell-cmdlet"></a>Nahrání certifikátu do spravované instance Azure SQL pomocí rutiny Azure PowerShellu

1. Začněte s přípravnými kroky v prostředí PowerShell:

   ```powershell
   # Import the module into the PowerShell session
   Import-Module AzureRM
   # Connect to Azure with an interactive dialog for sign-in
   Connect-AzureRmAccount
   # List subscriptions available and copy id of the subscription target Managed Instance belongs to
   Get-AzureRmSubscription
   # Set subscription for the session (replace Guid_Subscription_Id with actual subscription id)
   Select-AzureRmSubscription Guid_Subscription_Id
   ```

2. Jakmile bude vše připraveno, spuštěním následujících příkazů nahrajte certifikát v kódování base-64 do cílové spravované instance:

   ```powershell
   $fileContentBytes = Get-Content 'C:/full_path/TDE_Cert.pfx' -Encoding Byte
   $base64EncodedCert = [System.Convert]::ToBase64String($fileContentBytes)
   $securePrivateBlob = $base64EncodedCert  | ConvertTo-SecureString -AsPlainText -Force
   $password = "SomeStrongPassword"
   $securePassword = $password | ConvertTo-SecureString -AsPlainText -Force
   Add-AzureRmSqlManagedInstanceTransparentDataEncryptionCertificate -ResourceGroupName "<ResourceGroupName>" -ManagedInstanceName "<ManagedInstanceName>" -PrivateBlob $securePrivateBlob -Password $securePassword
   ```

Certifikát je nyní k dispozici pro zadanou spravovanou instanci a bude v ní možné obnovit zálohu databáze chráněné transparentním šifrováním dat.

## <a name="next-steps"></a>Další kroky

V tomto článku jste zjistili, jak migrovat certifikát s šifrovacím klíčem databáze chráněné transparentním šifrováním dat z místních nebo IaaS systémů SQL Server do spravované instance Azure SQL.

V kurzu [Obnovení zálohy databáze do spravované instance Azure SQL Database](sql-database-managed-instance-get-started-restore.md) se naučíte, jak obnovit zálohu databáze do spravované instance Azure SQL Database.
