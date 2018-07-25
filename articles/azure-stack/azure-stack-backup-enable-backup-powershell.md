---
title: Povolit zálohování pro Azure Stack pomocí Powershellu | Dokumentace Microsoftu
description: Povolte infrastruktury služby Backup pomocí Windows Powershellu pro Azure Stack je možné obnovit, pokud dojde k selhání.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 5/10/2018
ms.author: mabrigg
ms.reviewer: hectorl
ms.openlocfilehash: 76a24e7096cbc2a9bcea8bf68e2b333345dbff68
ms.sourcegitcommit: d76d9e9d7749849f098b17712f5e327a76f8b95c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/25/2018
ms.locfileid: "39242950"
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
| $key            | Typ **šifrovací klíč** použitý k šifrování každé zálohování. |
| $password       | Typ **heslo** pro daného uživatele. |
| $sharepath      | Zadejte cestu k **umístění úložiště zálohy**. Je nutné použít řetězec konvenci UNC (Universal Naming) pro cestu ke sdílené složce hostované na samostatném zařízení. Řetězec ve formátu UNC Určuje umístění prostředků, jako jsou sdílené soubory nebo zařízení. K zajištění dostupnosti dat zálohy by měla být zařízení v samostatném umístění. |

   ```powershell
    $username = "domain\backupadmin"
   
    $Secure = Read-Host -Prompt ("Password for: " + $username) -AsSecureString
    $Encrypted = ConvertFrom-SecureString -SecureString $Secure
    $password = ConvertTo-SecureString -String $Encrypted
    
    $BackupEncryptionKeyBase64 = ""
    $tempEncryptionKeyString = ""
    foreach($i in 1..64) { $tempEncryptionKeyString += -join ((65..90) + (97..122) | Get-Random | % {[char]$_}) }
    $tempEncryptionKeyBytes = [System.Text.Encoding]::UTF8.GetBytes($tempEncryptionKeyString)
    $BackupEncryptionKeyBase64 = [System.Convert]::ToBase64String($tempEncryptionKeyBytes)
    $BackupEncryptionKeyBase64
    
    $Securekey = ConvertTo-SecureString -String $BackupEncryptionKeyBase64 -AsPlainText -Force
    $Encryptedkey = ConvertFrom-SecureString -SecureString $Securekey
    $key = ConvertTo-SecureString -String $Encryptedkey
    
    $sharepath = "\\serverIP\AzSBackupStore\contoso.com\seattle"

    Set-AzSBackupShare -BackupShare $sharepath -Username $username -Password $password -EncryptionKey $key
   ```
   
##  <a name="confirm-backup-settings"></a>Potvrdit nastavení zálohování

Ve stejné relaci prostředí PowerShell spusťte následující příkazy:

   ```powershell
    Get-AzsBackupLocation | Select-Object -Property Path, UserName, AvailableCapacity
   ```

Výsledek by měl vypadat podobně jako následující výstup:

   ```powershell
    Path                        : \\serverIP\AzSBackupStore\contoso.com\seattle
    UserName                    : domain\backupadmin
    AvailableCapacity           : 60 GB
   ```

## <a name="next-steps"></a>Další postup

 - Zjistěte, jak spustit zálohování, viz [zálohování Azure stacku](azure-stack-backup-back-up-azure-stack.md ).  
 - Zjistěte, jak ověřit, jestli spustila svoji zálohu, najdete v článku [potvrdit zálohování bylo dokončeno v portálu pro správu](azure-stack-backup-back-up-azure-stack.md ).