---
title: Ověření zálohování Azure stacku pomocí ASDK | Dokumentace Microsoftu
description: Jak ověřit zálohu integrované systémy Azure Stack pomocí ASDK.
services: azure-stack
author: jeffgilb
manager: femila
cloud: azure-stack
ms.service: azure-stack
ms.topic: article
ms.date: 09/05/2018
ms.author: jeffgilb
ms.reviewer: hectorl
ms.lastreviewed: 09/05/2018
ms.openlocfilehash: 027d4a9f93032bfdd0f4cda96df74c92b5679540
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/30/2019
ms.locfileid: "55251567"
---
# <a name="use-the-asdk-to-validate-an-azure-stack-backup"></a>Použít ASDK k ověření zálohování Azure stacku
Po nasazení služby Azure Stack a zřizování uživatelů prostředky, jako jsou nabídky, plány, kvót a předplatných, měli byste [povolit zálohování infrastruktury Azure stacku](../azure-stack-backup-enable-backup-console.md). Plánování a spouštění pravidelných infrastruktura zálohování se zajistí, že infrastruktura správy nedojde ke ztrátě dat při katastrofických hardwaru nebo Chyba služby.

> [!TIP]
> Doporučujeme, který jste [spustit zálohování na vyžádání](../azure-stack-backup-back-up-azure-stack.md) před zahájením tohoto postupu k zajištění, že máte kopii nejnovější data infrastruktury k dispozici. Ujistěte se, že k zaznamenání ID zálohy, až se zálohování úspěšně dokončí. Toto ID se bude vyžadovat během obnovení cloudu. 

Zálohování infrastruktury Azure stacku obsahují důležitá data o vašem cloudu, které je možné obnovit během opětovné nasazení Azure stacku. ASDK můžete použít k ověření tyto zálohy bez dopadu na produkční cloudu. 

Ověřování v ASDK zálohování je podporováno v následujících scénářích:

|Scénář|Účel|
|-----|-----|
|Ověření integrované řešení zálohování infrastruktury.|Krátký povahy ověření, že data v záloze nejsou platné.|
|Přečtěte si začátku do konce obnovení pracovního postupu.|Pomocí ASDK ověření celou zálohu a obnovení prostředí.|
|     |     |

Následující scénář **není** podporované při ověřování v ASDK zálohování:

|Scénář|Účel|
|-----|-----|
|ASDK od sestavení k sestavení zálohování a obnovení.|Obnovení zálohovaných dat z předchozí verze ASDK na novější verzi.|
|     |     |


## <a name="cloud-recovery-deployment"></a>Nasazení v cloudu pro obnovení
Zálohování infrastruktury z integrované systémy pro nasazení může být ověřen pomocí provádí cloudového obnovení nasazení ASDK. V tomto typu nasazení konkrétní službu obnovení dat ze zálohy po instalaci ASDK v hostitelském počítači.



### <a name="cloud-recovery-prerequisites"></a>Požadavky na obnovení cloudu
Před zahájením nasazení cloudu pro obnovení ASDK, ujistěte se, že máte následující informace:

|Požadavek|Popis|
|-----|-----|
|Cesta ke sdílené složce záloh.|Cesty souboru UNC sdílené složky z poslední zálohy Azure Stack, který se použije k obnovení informací infrastruktury Azure stacku. Tato místní sdílená složka se vytvoří během procesu nasazení cloudu pro obnovení.|
|Zálohování šifrovacího klíče.|Šifrovací klíč použitý k plánování infrastruktury zálohování pro spuštění pomocí portálu pro správu služby Azure Stack.|
|ID zálohy k obnovení.|ID zálohy ve formuláři alfanumerické "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx", který identifikuje zálohy obnovit během obnovení cloudu.|
|IP adresa serveru čas.|Platný čas adresu IP serveru, jako je například 132.163.97.2, je vyžadován pro nasazení Azure Stack.|
|Externí certifikát heslo.|Heslo pro externí certifikát používaný službou Azure Stack. Zálohování certifikační Autority obsahuje externí certifikáty, které je nutné obnovit s toto heslo.|
|     |     | 

## <a name="prepare-the-host-computer"></a>Příprava hostitelském počítači 
Stejně jako v normálním ASDK nasazení musí být připravené prostředí ASDK hostitele systému pro instalaci. Když development kit hostitelský počítač připravený, se spustí z pevného disku virtuálního počítače CloudBuilder.vhdx zahájíte ASDK nasazení.

Na hostitelském počítači ASDK, stáhněte si nový cloudbuilder.vhdx odpovídající na stejnou verzi služby Azure Stack, která byla zálohována a postupujte podle pokynů pro [Příprava hostitelském počítači ASDK](asdk-prepare-host.md).

Po restartování serveru hostitele z cloudbuilder.vhdx, musíte vytvořit sdílenou složku a kopírovat data záloh do. Sdílené složky musí být přístupný pro účet, který spouští instalační program; Správce tyto příklady příkazů prostředí PowerShell: 

```powershell
$shares = New-Item -Path "c:\" -Name "Shares" -ItemType "directory"
$azsbackupshare = New-Item -Path $shares.FullName -Name "AzSBackups" -ItemType "directory"
New-SmbShare -Path $azsbackupshare.FullName -FullAccess ($env:computername + "\Administrator")  -Name "AzSBackups"
```

V dalším kroku zkopírujte nejnovější záložní soubory Azure Stack do nově vytvořené sdílené složky. Struktura složky ve sdílené složce by měla být: `\\<ComputerName>\AzSBackups\MASBackup\<BackupID>\`.

## <a name="deploy-the-asdk-in-cloud-recovery-mode"></a>Nasazení ASDK v režimu obnovení cloudu
**InstallAzureStackPOC.ps1** skript slouží k zahájení obnovení cloudu. 

> [!IMPORTANT]
> Instalace ASDK podporuje přesně jednu síťovou kartu (NIC) sítě. Pokud máte více síťových adaptérů, ujistěte se, že je povolená jenom jedna (a všechny ostatní jsou zakázané) před spuštěním skriptu nasazení.

Upravte následující příkazy Powershellu pro vaše prostředí a spustit nasazení ASDK v režimu obnovení cloudu:

```powershell
cd C:\CloudDeployment\Setup     
$adminPass = Get-Credential Administrator
$key = ConvertTo-SecureString "<Your backup encryption key>" -AsPlainText -Force ` 
$certPass = Read-Host -AsSecureString  

.\InstallAzureStackPOC.ps1 -AdminPassword $adminpass.Password -BackupStorePath ("\\" + $env:COMPUTERNAME + "\AzSBackups") `
-BackupEncryptionKeyBase64 $key -BackupStoreCredential $adminPass -BackupId "<Backup ID to restore>" `
-TimeServer "<Valid time server IP>" -ExternalCertPassword $certPass
```

## <a name="restore-infrastructure-data-from-backup"></a>Infrastruktura data obnovit ze zálohy
Po úspěšné cloudového nasazení pro obnovení, které potřebujete k dokončení obnovení pomocí **obnovení AzureStack** rutiny. 

Po přihlášení jako operátory Azure stacku [instalaci Azure Stack Powershellu](asdk-post-deploy.md#install-azure-stack-powershell) a potom nahraďte ID zálohování `Name` parametru, spusťte následující příkaz:

```powershell
Restore-AzsBackup -Name "<BackupID>"
```
Počkejte 60 minut po volání tuto rutinu spustit ověřovací data záloh do cloudu ASDK obnovení.

## <a name="next-steps"></a>Další postup
[Registrace Azure Stack](asdk-register.md)

