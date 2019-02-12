---
title: Povolit zálohování pro Azure Stack pomocí Powershellu | Dokumentace Microsoftu
description: Povolte infrastruktury služby Backup pomocí Windows Powershellu pro Azure Stack je možné obnovit, pokud dojde k selhání.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/08/2019
ms.author: jeffgilb
ms.reviewer: hectorl
ms.lastreviewed: 02/08/2019
ms.openlocfilehash: 280a811e943c2e81a96875e3c8ba8efdb86fbf2a
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/11/2019
ms.locfileid: "56004821"
---
# <a name="enable-backup-for-azure-stack-with-powershell"></a>Povolit zálohování pro Azure Stack pomocí Powershellu

*Platí pro: Azure Stack integrované systémy a Azure Stack Development Kit*

Povolit službu Backup infrastruktury v prostředí Windows PowerShell proveďte tak pravidelné zálohy:
 - Interní identity služby a kořenový certifikát
 - Uživatelské plány, nabídky, předplatná
 - Pro výpočty, úložiště a síťové kvóty uživatele
 - Tajné klíče uživatelů Key vault
 - Uživatelské role RBAC a zásady
 - Uživatelské účty úložiště

Rutiny Powershellu k povolení zálohování, spustit zálohování a získat informace o zálohování přes koncový bod správy operátor můžete přistupovat.

## <a name="prepare-powershell-environment"></a>Příprava prostředí PowerShell

Pokyny ke konfiguraci prostředí PowerShell najdete v tématu [instalace Powershellu pro Azure Stack ](azure-stack-powershell-install.md). Přihlaste se ke službě Azure Stack, najdete v článku [nakonfigurovat prostředí operátor a přihlaste se ke službě Azure Stack](azure-stack-powershell-configure-admin.md).

## <a name="provide-the-backup-share-credentials-and-encryption-key-to-enable-backup"></a>Zadejte zálohování sdílené složky, přihlašovací údaje a šifrovací klíč k povolení zálohování

Ve stejné relaci prostředí PowerShell upravte následující skript prostředí PowerShell tak, že přidáte proměnné pro vaše prostředí. Spusťte skript aktualizace zálohování sdílené složky, přihlašovací údaje a šifrovací klíč poskytovat infrastrukturu služby zálohování.

| Proměnná        | Popis   |
|---              |---                                        |
| $username       | Typ **uživatelské jméno** pomocí doména a uživatelské jméno pro umístění sdíleného disku s dostatečný přístup pro čtení a zápis souborů. Například, `Contoso\backupshareuser`. |
| $password       | Typ **heslo** pro daného uživatele. |
| $sharepath      | Zadejte cestu k **umístění úložiště zálohy**. Je nutné použít řetězec konvenci UNC (Universal Naming) pro cestu ke sdílené složce hostované na samostatném zařízení. Řetězec ve formátu UNC Určuje umístění prostředků, jako jsou sdílené soubory nebo zařízení. K zajištění dostupnosti dat zálohy by měla být zařízení v samostatném umístění. |
| $frequencyInHours | Určuje četnost v hodinách, jak často se vytvoří zálohy. Výchozí hodnota je 12. Scheduler podporuje maximálně 12 a minimálně 4.|
| $retentionPeriodInDays | Doba uchování ve dnech Určuje, kolik dní záloh, které jsou uvedeny v externím místě. Výchozí hodnota je 7. Scheduler podporuje maximálně 14 a minimálně 2. Starší než doba uchování zálohy se automaticky odstraní z externího umístění.|
| $encryptioncertpath | Cesta k certifikátu šifrování Určuje cesta k souboru. Soubor CER pomocí veřejného klíče pro šifrování dat |
|     |     |

```powershell
    # Example username:
    $username = "domain\backupadmin"
 
    # Example share path:
    $sharepath = "\\serverIP\AzSBackupStore\contoso.com\seattle"

    $password = Read-Host -Prompt ("Password for: " + $username) -AsSecureString

    # Create a self-signed certificate using New-SelfSignedCertificate, export the public key portion and save it locally.

    $cert = New-SelfSignedCertificate `
        -DnsName "www.contoso.com" `
        -CertStoreLocation "cert:\LocalMachine\My" 

    New-Item -Path "C:\" -Name "Certs" -ItemType "Directory" 

    #make sure to export the PFX format of the certificate with the public and private keys and then delete the certifcate from the local certificate store of the machine where you created the certificate
    
    Export-Certificate `
        -Cert $cert `
        -FilePath c:\certs\AzSIBCCert.cer 

    # Set the backup settings with the name, password, share, and CER certificate file.
    Set-AzsBackupConfiguration -BackupShare $sharepath -Username $username -Password $password -EncryptionCertPath "c:\temp\cert.cer"
```
   
##  <a name="confirm-backup-settings"></a>Potvrdit nastavení zálohování

Ve stejné relaci prostředí PowerShell spusťte následující příkazy:

   ```powershell
    Get-AzsBackupConfiguration | Select-Object -Property Path, UserName
   ```

Výsledek by měl vypadat jako následující příklad výstupu:

   ```powershell
    Path                        : \\serverIP\AzsBackupStore\contoso.com\seattle
    UserName                    : domain\backupadmin
   ```

## <a name="update-backup-settings"></a>Aktualizovat nastavení zálohování
Ve stejné relaci prostředí PowerShell můžete aktualizovat výchozí hodnoty pro dobu uchování a četnost zálohování. 

   ```powershell
    #Set the backup frequency and retention period values.
    $frequencyInHours = 10
    $retentionPeriodInDays = 5

    Set-AzsBackupConfiguration -BackupFrequencyInHours $frequencyInHours -BackupRetentionPeriodInDays $retentionPeriodInDays

    Get-AzsBackupConfiguration | Select-Object -Property Path, UserName, AvailableCapacity, BackupFrequencyInHours, BackupRetentionPeriodInDays
   ```

Výsledek by měl vypadat jako následující příklad výstupu:

   ```powershell
    Path                        : \\serverIP\AzsBackupStore\contoso.com\seattle
    UserName                    : domain\backupadmin
    AvailableCapacity           : 60 GB
    BackupFrequencyInHours      : 10
    BackupRetentionPeriodInDays : 5
   ```

###<a name="azure-stack-powershell"></a>Azure Stack PowerShell 
Rutiny Powershellu pro konfiguraci zálohování infrastruktury je sada AzsBackupConfiguration. V předchozích verzích byla rutinu Set-AzsBackupShare. Tato rutina vyžaduje poskytnutí certifikátu. Pokud infrastruktura zálohování je nakonfigurovaný pomocí šifrovacího klíče, nejde aktualizovat šifrovací klíč nebo vlastnost zobrazit. Je potřeba použít verzi 1.6 Powershellu pro správu. 

Infrastruktura zálohování se nakonfigurovalo před aktualizací na 1901, můžete nastavit a zobrazit šifrovací klíč verze 1.6 Powershellu pro správu. Verze 1.6 nebude možné aktualizovat z šifrovací klíč do souboru certifikátu.
Odkazovat na [instalace Azure Stack Powershellu](azure-stack-powershell-install.md) Další informace o instalaci správné verze modulu. 


## <a name="next-steps"></a>Další postup

Zjistěte, jak spustit zálohování, viz [zálohování Azure stacku](azure-stack-backup-back-up-azure-stack.md)

Zjistěte, jak ověřit, jestli spustila svoji zálohu, najdete v článku [potvrdit zálohování bylo dokončeno v portálu pro správu](azure-stack-backup-back-up-azure-stack.md)
