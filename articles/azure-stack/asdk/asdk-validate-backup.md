---
title: Ověření zálohování Azure stacku pomocí ASDK | Dokumentace Microsoftu
description: Jak ověřit zálohu integrované systémy Azure Stack pomocí ASDK.
services: azure-stack
author: jeffgilb
manager: femila
cloud: azure-stack
ms.service: azure-stack
ms.topic: article
ms.date: 02/15/2019
ms.author: jeffgilb
ms.reviewer: hectorl
ms.lastreviewed: 02/15/2019
ms.openlocfilehash: 31c5d068c8fcd0b6edea7cff63098131d848a14e
ms.sourcegitcommit: 79038221c1d2172c0677e25a1e479e04f470c567
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/19/2019
ms.locfileid: "56416374"
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

### <a name="prereqs"></a>Požadavky na obnovení cloudu
Před zahájením nasazení cloudu pro obnovení ASDK, ujistěte se, že máte následující informace:

**Požadavky instalačního programu uživatelského rozhraní**

*Aktuální instalační program uživatelského rozhraní podporuje pouze šifrovacího klíče*

|Požadavek|Popis|
|-----|-----|
|Cesta ke sdílené složce záloh|Cesty souboru UNC sdílené složky z poslední zálohy Azure Stack, který se použije k obnovení informací infrastruktury Azure stacku. Tato místní sdílená složka se vytvoří během procesu nasazení cloudu pro obnovení.|
|ID zálohy k obnovení|ID zálohy ve formuláři alfanumerické "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx", který identifikuje zálohy obnovit během obnovení cloudu.|
|IP adresa serveru čas|Platný čas adresu IP serveru, jako je například 132.163.97.2, je vyžadován pro nasazení Azure Stack.|
|Externí certifikát heslo|Heslo pro externí certifikát používaný službou Azure Stack. Zálohování certifikační Autority obsahuje externí certifikáty, které je nutné obnovit s toto heslo.|
|Zálohování šifrovacího klíče|Vyžaduje-li upgradováni na verzi služby Azure Stack 1901 nebo novější a zálohování nastavení je stále probíhá konfigurace pomocí šifrovacího klíče. Šifrovací klíč je zastaralý, počínaje 1901. Instalační program bude podporovat šifrovací klíč ve zpětné režim kompatibility pro alespoň 3 verze. Po aktualizaci nastavení zálohování k používání certifikátu naleznete v další tabulce pro požadované informace.|

|     |     | 

**Požadavky instalačního programu Powershellu**

*Aktuální instalačního programu Powershellu podporuje šifrovací klíč nebo dešifrovací certifikát*

|Požadavek|Popis|
|-----|-----|
|Cesta ke sdílené složce záloh|Cesty souboru UNC sdílené složky z poslední zálohy Azure Stack, který se použije k obnovení informací infrastruktury Azure stacku. Tato místní sdílená složka se vytvoří během procesu nasazení cloudu pro obnovení.|
|ID zálohy k obnovení|ID zálohy ve formuláři alfanumerické "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx", který identifikuje zálohy obnovit během obnovení cloudu.|
|IP adresa serveru čas|Platný čas adresu IP serveru, jako je například 132.163.97.2, je vyžadován pro nasazení Azure Stack.|
|Externí certifikát heslo|Heslo pro externí certifikát používaný službou Azure Stack. Zálohování certifikační Autority obsahuje externí certifikáty, které je nutné obnovit s toto heslo.|
|Dešifrovací heslo certifikace|Volitelné. Vyžaduje se, jenom Pokud zálohování šifrována pomocí certifikátu. Heslo je pro držitele certifikátu (.pfx), který obsahuje privátní klíč, který je nutný k dešifrování dat záloh.|
|Zálohování šifrovacího klíče|Volitelné. Vyžaduje-li upgradováni na verzi služby Azure Stack 1901 nebo novější a zálohování nastavení je stále probíhá konfigurace pomocí šifrovacího klíče. Instalační program bude podporovat šifrovací klíč ve zpětné režim kompatibility pro alespoň 3 verze. Po aktualizaci nastavení zálohování pro použití certifikátu se musí zadat heslo k certifikátu dešifrování.|
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

Nakonec zkopírujte do adresáře certifikát dešifrování certifikátu (.pfx): `C:\CloudDeployment\Setup\Certificates\` a přejmenujte soubor na `BackupDecryptionCert.pfx`.

## <a name="deploy-the-asdk-in-cloud-recovery-mode"></a>Nasazení ASDK v režimu obnovení cloudu

> [!IMPORTANT]
> 1. Aktuální instalační program uživatelského rozhraní podporuje pouze šifrovacího klíče. Můžete pouze ověření zálohy ze systémů, které dál používat šifrovací klíč. Pokud záloha byla zašifrována na integrovaný systém nebo ASDK pomocí certifikátu, je nutné použít instalační program prostředí PowerShell (**InstallAzureStackPOC.ps1**). 
> 2. Instalační služba prostředí PowerShell (**InstallAzureStackPOC.ps1**) podporuje šifrovací klíč nebo certifikát.
> 3. Instalace ASDK podporuje přesně jednu síťovou kartu (NIC) sítě. Pokud máte více síťových adaptérů, ujistěte se, že je povolená jenom jedna (a všechny ostatní jsou zakázané) před spuštěním skriptu nasazení.

### <a name="use-the-installer-ui-to-deploy-the-asdk-in-recovery-mode"></a>Použijte instalační program uživatelského rozhraní pro nasazení ASDK v režimu obnovení
Kroky v této části ukazují, jak nasadit ASDK pomocí grafického uživatelského rozhraní (GUI) poskytované stáhnete a nainstalujete **asdk installer.ps1** skript prostředí PowerShell.

> [!NOTE]
> Uživatelské rozhraní instalačního programu pro Azure Stack Development Kit je skriptu open source na základě WCF a prostředí PowerShell.

> [!IMPORTANT]
> Aktuální instalační program uživatelského rozhraní podporuje pouze šifrovacího klíče.

1. Po hostitelský počítač úspěšně spustí do bitové kopie CloudBuilder.vhdx, přihlášení pomocí přihlašovacích údajů správce zadán při vám [připravili development kit hostitelský počítač](asdk-prepare-host.md) ASDK instalace. To by měla být stejná jako přihlašovací údaje development kit hostitele místního správce.
2. Otevřete konzolu Powershellu se zvýšenými oprávněními a spusťte  **&lt;písmeno jednotky > \AzureStack_Installer\asdk-installer.ps1** skript prostředí PowerShell. Skript může být nyní na jinou jednotku než C:\ CloudBuilder.vhdx obrázku. Klikněte na tlačítko **obnovit**.

    ![Skript instalačního programu ASDK](media/asdk-validate-backup/1.PNG) 

3. Zadejte údaje adresáře Azure AD (volitelné) a heslo místního správce pro ASDK hostitelského počítače na stránce zprostředkovatele a přihlašovací údaje identity. Klikněte na **Další**.

    ![Stránka identity a přihlašovací údaje](media/asdk-validate-backup/2.PNG) 

4. Vyberte síťový adaptér použije ASDK hostitelský počítač a klikněte na tlačítko **Další**. Během instalace ASDK vypne všechna další síťová rozhraní. 

    ![Síťového adaptéru rozhraní](media/asdk-validate-backup/3.PNG) 

5. Na stránce konfigurace sítě zadejte platný čas serveru a předávání IP adres DNS. Klikněte na **Další**.

    ![Stránka Konfigurace sítě](media/asdk-validate-backup/4.PNG) 

6. Pokud byly ověřeny vlastnosti karty síťového rozhraní, klikněte na tlačítko **Další**. 

    ![Ověření nastavení síťové karty](media/asdk-validate-backup/5.PNG) 

7. Zadejte požadované informace popsané výše v [části s předpoklady](#prereqs) na stránce nastavení zálohování a uživatelské jméno a heslo pro přístup ke sdílené složce. Klikněte na tlačítko **Další**: 

   ![Stránka nastavení zálohování](media/asdk-validate-backup/6.PNG) 

8. Skript nasazení použije pro nasazení ASDK na stránce Souhrn zkontrolujte. Klikněte na tlačítko **nasadit** zahájíte nasazení. 

    ![Stránka souhrnu](media/asdk-validate-backup/7.PNG) 


### <a name="use-powershell-to-deploy-the-asdk-in-recovery-mode"></a>Použití Powershellu k nasazení ASDK v režimu obnovení

Upravte následující příkazy Powershellu pro vaše prostředí a spustit nasazení ASDK v režimu obnovení cloudu:

**Pomocí skriptu InstallAzureStackPOC.ps1 zahájíte cloudu pro obnovení pomocí šifrovacího klíče.**

```powershell
cd C:\CloudDeployment\Setup     
$adminpass = Read-Host -AsSecureString -Prompt "Local Administrator password"
$certPass = Read-Host -AsSecureString -Prompt "Password for the external certificate"
$backupstorecredential = Read-Host -AsSecureString -Prompt "Credential for backup share"
$key = Read-Host -AsSecureString -Prompt "Your backup encryption key"

.\InstallAzureStackPOC.ps1 -AdminPassword $adminpass `
 -BackupStorePath ("\\" + $env:COMPUTERNAME + "\AzSBackups") `
 -BackupEncryptionKeyBase64 $key `
 -BackupStoreCredential $backupstorecredential `
 -BackupId "<Backup ID to restore>" `
 -TimeServer "<Valid time server IP>" -ExternalCertPassword $certPass
```

**Pomocí skriptu InstallAzureStackPOC.ps1 zahájíte cloudu pro obnovení pomocí certifikátu dešifrování.**

```powershell
cd C:\CloudDeployment\Setup     
$adminpass = Read-Host -AsSecureString -Prompt "Local Administrator password"
$certPass = Read-Host -AsSecureString -Prompt "Password for the external certificate"
$backupstorecredential = Read-Host -AsSecureString -Prompt "Credential for backup share"
$decryptioncertpassword  = Read-Host -AsSecureString -Prompt "Password for the decryption certificate"

.\InstallAzureStackPOC.ps1 -AdminPassword $adminpass `
 -BackupStorePath ("\\" + $env:COMPUTERNAME + "\AzSBackups") `
 -BackupDecryptionCertPassword $decryptioncertpassword `
 -BackupStoreCredential $backupstorecredential `
 -BackupId "<Backup ID to restore>" `
 -TimeServer "<Valid time server IP>" -ExternalCertPassword $certPass
```

## <a name="complete-cloud-recovery"></a>Kompletní cloud pro obnovení 
Po úspěšné cloudového nasazení pro obnovení, které potřebujete k dokončení obnovení pomocí **obnovení AzureStack** rutiny. 

Po přihlášení jako operátory Azure stacku [instalaci Azure Stack Powershellu](asdk-post-deploy.md#install-azure-stack-powershell) a spusťte následující příkazy a zadat certifikát a heslo, které se použije při obnovování ze zálohy:

**Režim obnovení pomocí souboru certifikátu**

> [!NOTE] 
> Nasazení Azure Stack není zachována dešifrovací certifikát z bezpečnostních důvodů. Je potřeba zadat certifikát dešifrování a přiřazené heslo znovu.

```powershell
$decryptioncertpassword = Read-Host -AsSecureString -Prompt "Password for the decryption certificate"
Restore-AzsBackup -ResourceId "<BackupID>" `
 -DecryptionCertPath "<path to decryption certificate with file name (.pfx)>" `
 -DecryptionCertPassword $decryptioncertpassword
```

**Režim obnovení pomocí šifrovacího klíče**
```powershell
$decryptioncertpassword = Read-Host -AsSecureString -Prompt "Password for the decryption certificate"
Restore-AzsBackup -ResourceId "<BackupID>"
```

Počkejte 60 minut po volání tuto rutinu spustit ověřovací data záloh do cloudu ASDK obnovení.

## <a name="next-steps"></a>Další postup
[Registrace Azure Stack](asdk-register.md)

