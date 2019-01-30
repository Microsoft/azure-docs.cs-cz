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
ms.date: 08/16/2018
ms.author: jeffgilb
ms.reviewer: hectorl
ms.lastreviewed: 08/16/2018
ms.openlocfilehash: 10d7303c4323305e177cf006b9a259a817dc695e
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/30/2019
ms.locfileid: "55247472"
---
# <a name="enable-backup-for-azure-stack-with-powershell"></a>Povolit zálohování pro Azure Stack pomocí Powershellu

*Platí pro: Azure Stack integrované systémy a Azure Stack Development Kit*

Povolit službu Backup infrastruktury v prostředí Windows PowerShell proveďte tak pravidelné zálohy:
 - Interní identity služby a kořenový certifikát
 - Uživatelské plány, nabídky, předplatná
 - Tajné kódy Keyvault
 - Uživatelské role RBAC a zásady

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
|     |     |

   ```powershell
    # Example username:
    $username = "domain\backupadmin"
    # Example share path:
    $sharepath = "\\serverIP\AzSBackupStore\contoso.com\seattle"
   
    $password = Read-Host -Prompt ("Password for: " + $username) -AsSecureString
    
    # The encryption key is generated using the New-AzsEncryptionKeyBase64 cmdlet provided in Azure Stack PowerShell.
    # Make sure to store your encryption key in a secure location after it is generated.
    $Encryptionkey = New-AzsEncryptionKeyBase64
    $key = ConvertTo-SecureString -String ($Encryptionkey) -AsPlainText -Force

    Set-AzsBackupShare -BackupShare $sharepath -Username $username -Password $password -EncryptionKey $key
   ```
   
##  <a name="confirm-backup-settings"></a>Potvrdit nastavení zálohování

Ve stejné relaci prostředí PowerShell spusťte následující příkazy:

   ```powershell
    Get-AzsBackupLocation | Select-Object -Property Path, UserName
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

    Set-AzsBackupShare -BackupFrequencyInHours $frequencyInHours -BackupRetentionPeriodInDays $retentionPeriodInDays
    Get-AzsBackupLocation | Select-Object -Property Path, UserName, AvailableCapacity, BackupFrequencyInHours, BackupRetentionPeriodInDays
   ```

Výsledek by měl vypadat jako následující příklad výstupu:

   ```powershell
    Path                        : \\serverIP\AzsBackupStore\contoso.com\seattle
    UserName                    : domain\backupadmin
    AvailableCapacity           : 60 GB
    BackupFrequencyInHours      : 10
    BackupRetentionPeriodInDays : 5
   ```

## <a name="next-steps"></a>Další postup

 - Zjistěte, jak spustit zálohování, viz [zálohování Azure stacku](azure-stack-backup-back-up-azure-stack.md ).  
 - Zjistěte, jak ověřit, jestli spustila svoji zálohu, najdete v článku [potvrdit zálohování bylo dokončeno v portálu pro správu](azure-stack-backup-back-up-azure-stack.md ).
