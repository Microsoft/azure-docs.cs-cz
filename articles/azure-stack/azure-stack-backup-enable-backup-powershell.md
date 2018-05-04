---
title: Povolit zálohování pro Azure zásobníku pomocí prostředí PowerShell | Microsoft Docs
description: Povolte službu infrastruktura zálohování pomocí prostředí Windows PowerShell, aby zásobník Azure můžete obnovit, pokud dojde k selhání.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 7DFEFEBE-D6B7-4BE0-ADC1-1C01FB7E81A6
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/15/2017
ms.author: mabrigg
ms.openlocfilehash: 6fbd82c3d49a4d64523bf0e10b67ce3aabe96de2
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
---
# <a name="enable-backup-for-azure-stack-with-powershell"></a>Povolit zálohování pro Azure zásobníku pomocí prostředí PowerShell

*Platí pro: Azure zásobníku integrované systémy a Azure zásobníku Development Kit*

Povolte službu infrastruktura zálohování pomocí prostředí Windows PowerShell, aby zásobník Azure můžete obnovit, pokud dojde k selhání. Rutiny prostředí PowerShell pro povolení zálohování, spustit zálohování a získat informace o zálohování přes koncový bod správy operátor můžete přistupovat.

## <a name="download-azure-stack-tools"></a>Stažení nástroje Azure zásobníku

Instalace a nakonfigurované prostředí PowerShell pro Azure zásobníku a nástroje Azure zásobníku. V tématu [zprovoznění pomocí prostředí PowerShell v zásobníku Azure](https://docs.microsoft.com/azure/azure-stack/azure-stack-powershell-configure-quickstart).

##  <a name="load-the-connect-and-infrastructure-modules"></a>Načtení modulů připojit a infrastruktury

Otevřete prostředí Windows PowerShell s řádku se zvýšenými oprávněními a spusťte následující příkazy:

   ```powershell
    cd C:\tools\AzureStack-Tools-master\Connect
    Import-Module .\AzureStack.Connect.psm1
    
    cd C:\tools\AzureStack-Tools-master\Infrastructure
    Import-Module .\AzureStack.Infra.psm1 
    
   ```

##  <a name="setup-rm-environment-and-log-into-the-operator-management-endpoint"></a>Instalační program Rm prostředí a protokolu do koncového bodu správy – operátor

Přidáním proměnné prostředí ve stejné relaci prostředí PowerShell, upravte následující skript prostředí PowerShell. Spusťte skript aktualizované nastavení prostředí RM a přihlaste se k koncový bod správy operátor.

| Proměnná    | Popis |
|---          |---          |
| $TenantName | Název klienta Azure Active Directory. |
| Název účtu – operátor        | Název účtu operátor zásobník Azure. |
| Koncový bod Azure Resource Manager | Adresa URL pro Azure Resource Manager. |

   ```powershell
   # Specify Azure Active Directory tenant name
    $TenantName = "contoso.onmicrosoft.com"
    
    # Set the module repository and the execution policy
    Set-PSRepository `
      -Name "PSGallery" `
      -InstallationPolicy Trusted
    
    Set-ExecutionPolicy RemoteSigned `
      -force
    
    # Configure the Azure Stack operator’s PowerShell environment.
    Add-AzureRMEnvironment `
      -Name "AzureStackAdmin" `
      -ArmEndpoint "https://adminmanagement.seattle.contoso.com"
    
    Set-AzureRmEnvironment `
      -Name "AzureStackAdmin" `
      -GraphAudience "https://graph.windows.net/"
    
    $TenantID = Get-AzsDirectoryTenantId `
      -AADTenantName $TenantName `
      -EnvironmentName AzureStackAdmin
    
    # Sign-in to the operator's console.
    Add-AzureRmAccount -EnvironmentName "AzureStackAdmin" -TenantId $TenantID 
    
   ```
## <a name="generate-a-new-encryption-key"></a>Vygenerujte nový šifrovací klíč

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
   Get-AzsBackupLocation | Select-Object -Property Path, UserName, Password | ConvertTo-Json 
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
