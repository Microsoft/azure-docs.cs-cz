---
title: Povolit zálohování pro Azure zásobníku pomocí prostředí PowerShell | Microsoft Docs
description: Povolte službu infrastruktura zálohování pomocí prostředí Windows PowerShell, aby zásobník Azure můžete obnovit, pokud dojde k selhání.
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
ms.openlocfilehash: 4faa6930c37f9d491a3efa4b34519dbb13761a9d
ms.sourcegitcommit: fc64acba9d9b9784e3662327414e5fe7bd3e972e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/12/2018
---
# <a name="enable-backup-for-azure-stack-with-powershell"></a>Povolit zálohování pro Azure zásobníku pomocí prostředí PowerShell

*Platí pro: Azure zásobníku integrované systémy a Azure zásobníku Development Kit*

Povolit službu infrastruktura zálohování pomocí prostředí Windows PowerShell tak trvat pravidelné zálohy:
 - Interní identity služby a kořenový certifikát
 - Plány uživatele, nabídky, odběry
 - Tajné klíče Keyvault
 - Role RBAC uživatelů a zásady

Rutiny prostředí PowerShell pro povolení zálohování, spustit zálohování a získat informace o zálohování přes koncový bod správy operátor můžete přistupovat.

## <a name="prepare-powershell-environment"></a>Příprava prostředí PowerShell

Pokyny ke konfiguraci prostředí PowerShell najdete v tématu [instalaci prostředí PowerShell pro Azure zásobníku ](azure-stack-powershell-install.md).

## <a name="generate-a-new-encryption-key"></a>Vygenerujte nový šifrovací klíč

Instalace a nakonfigurované prostředí PowerShell pro Azure zásobníku a nástroje Azure zásobníku.
 - V tématu [zprovoznění pomocí prostředí PowerShell v zásobníku Azure](https://docs.microsoft.com/azure/azure-stack/azure-stack-powershell-configure-quickstart).
 - V tématu [nástroje zásobník Azure stáhnout z webu GitHub](azure-stack-powershell-download.md)

Otevřete prostředí Windows PowerShell s řádku se zvýšenými oprávněními a spusťte následující příkazy:
   
   ```powershell
    cd C:\tools\AzureStack-Tools-master\Infrastructure
    Import-Module .\AzureStack.Infra.psm1 
   ```
   
Ve stejné relaci prostředí PowerShell spusťte následující příkazy:

   ```powershell
   $encryptionkey = New-EncryptionKeyBase64
   ```

> [!Warning]  
> Nástroje AzureStack musíte použít ke generování klíče.

## <a name="provide-the-backup-share-credentials-and-encryption-key-to-enable-backup"></a>Zadejte zálohování sdílené složky, pověření a šifrovací klíč k povolení zálohování

Přidáním proměnné prostředí ve stejné relaci prostředí PowerShell, upravte následující skript prostředí PowerShell. Spusťte skript aktualizované zajistit zálohování sdílené složky, přihlašovací údaje a šifrovací klíče ke službě zálohování infrastruktury.

| Proměnná        | Popis   |
|---              |---                                        |
| $username       | Typ **uživatelské jméno** pomocí domény a uživatelské jméno pro umístění sdíleného disku. Například, `Contoso\administrator`. |
| $password       | Typ **heslo** pro uživatele. |
| $sharepath      | Zadejte cestu ke **umístění úložiště zálohy**. Je nutné použít řetězec Universal Naming Convention (UNC) pro cestu ke sdílené složce hostované na samostatných zařízení. Řetězec UNC Určuje umístění prostředků, jako jsou sdílené soubory nebo zařízení. K zajištění dostupnosti zálohovaných dat, musí být zařízení v samostatných umístění. |

   ```powershell
    $username = "domain\backupoadmin"
    $password = "password"
    $credential = New-Object System.Management.Automation.PSCredential($username, ($password| ConvertTo-SecureString -asPlainText -Force))  
    $location = Get-AzsLocation
    $sharepath = "\\serverIP\AzSBackupStore\contoso.com\seattle"
    
    Set-AzSBackupShare -Location $location.Name -Path $sharepath -UserName $credential.UserName -Password $credential.GetNetworkCredential().password -EncryptionKey $encryptionkey
   ```
   
##  <a name="confirm-backup-settings"></a>Potvrďte nastavení zálohování

Ve stejné relaci prostředí PowerShell spusťte následující příkazy:

   ```powershell
   Get-AzsBackupLocation | Select-Object -ExpandProperty externalStoreDefault | Select-Object -Property Path, UserName, Password | ConvertTo-Json
   ```

Výsledek by měl vypadat podobně jako následující výstup JSON:

   ```json
      {
    "ExternalStoreDefault":  {
        "Path":  "\\\\serverIP\\AzSBackupStore\\contoso.com\\seattle",
        "UserName":  "domain\backupoadmin",
        "Password":  null
       }
   } 
   ```

## <a name="next-steps"></a>Další postup

 - Další informace ke spuštění zálohování, naleznete v [zálohování Azure zásobníku](azure-stack-backup-back-up-azure-stack.md ).  
 - Naučte se ověřit, jestli vaše zálohování spustila najdete v tématu [zálohování potvrzení byla dokončena v portálu pro správu](azure-stack-backup-back-up-azure-stack.md ).