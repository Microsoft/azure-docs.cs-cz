---
title: Automatizace ověření služby Azure Stack pomocí Powershellu | Dokumentace Microsoftu
description: Ověření služby Azure Stack pomocí Powershellu, můžete automatizovat.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 07/24/2018
ms.author: mabrigg
ms.reviewer: johnhas
ms.openlocfilehash: 1cb4b1a7cfd72ea302676244a53af58e77215aa9
ms.sourcegitcommit: 3f8f973f095f6f878aa3e2383db0d296365a4b18
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/20/2018
ms.locfileid: "40235252"
---
# <a name="automate-azure-stack-validation-with-powershell"></a>Automatizace ověření služby Azure Stack pomocí Powershellu 

Ověření jako služba (VaaS) poskytuje možnost automatizovat spouštění testů pomocí **LaunchVaaSTests.ps1** skriptu.

Pro následující pracovní postup můžete použít PowerShell:

- Průchod testu pracovního postupu

Tento skript zahrnuje čtyři prvky z pracovního postupu:

- Nainstaluje požadavky.
- Instalace a spouštění místního agenta.
- Spustí kategorie testů, jako je integrace, funkční, spolehlivost.
- Sestavy, každý test předat výsledek pro monitorování a sestavy souboru generace.

## <a name="launch-the-test-pass-workflow"></a>Spuštění pracovního postupu testování průchodu

1. Otevřete řádku Powershellu se zvýšenými oprávněními.

2. Spusťte následující skript ke stažení skriptu pro automatizaci:

    ````PowerShell  
    New-Item -ItemType Directory -Path <VaaSLaunchDirectory>
    Set-Location <VaaSLaunchDirectory>
    Invoke-WebRequest -Uri https://vaastestpacksprodeastus.blob.core.windows.net/packages/Microsoft.VaaS.Scripts.3.0.0.nupkg -OutFile "LaunchVaaS.zip"
    Expand-Archive -Path ".\LaunchVaaS.zip" -DestinationPath .\ -Force
    ````

3. S hodnotami, spusťte následující skript:

    ````PowerShell  
    $VaaSAccountCreds = New-Object System.Management.Automation.PSCredential "<VaaSUserId>", (ConvertTo-SecureString "<VaaSUserPassword>"  -AsPlainText -Force)
    $ServiceAdminCreds = New-Object System.Management.Automation.PSCredential "<ServiceAdminUser>", (ConvertTo-SecureString "<ServiceAdminPassword>" -AsPlainText -Force)
    $TenantAdminCreds = New-Object System.Management.Automation.PSCredential "<TenantAdminUser>", (ConvertTo-SecureString "<TenantAdminPassword>" -AsPlainText -Force)
    .\LaunchVaaSTests.ps1 -VaaSAccountCreds $VaaSAccountCreds `
        -VaaSAccountTenantId <VaaSAccountTenantId> `
        -VaaSSolutionName <VaaSSolutionName> `
        -VaaSTestPassName <VaaSTestPassName> `
        -VaaSTestCategories Integration,Functional `
        -MaxScriptWaitTimeInHours 12 `
        -ServiceAdminCreds $ServiceAdminCreds `
    ````

    **Parametry**

    | Parametr | Popis |
    | --- | --- |
    | VaaSUserld | Vaše ID uživatele VaaS. | 
    | VaaSUserPassword | VaaS heslo. |
    | ServiceAdminUser | Účet správce služby Azure Stack.  |
    | ServiceAdminPassword | Heslo služby Azure Stack.  |
    | TenantAdminUser | Správce pro primární tenant.  |
    | TenantAdminPassword | Heslo pro primární tenant.  |
    | FunctionalCategory| Integrace, funkční, nebo. Spolehlivost. Pokud používáte více hodnot, jednotlivé hodnoty oddělte je čárkami.  |

4. Zkontrolujte výsledky testu. Informace o čtení výsledků testu naleznete v tématu [monitorování testů](azure-stack-vaas-monitor-test.md).

## <a name="next-steps"></a>Další postup

 - Další informace o [ověřování služby Azure Stack jako služba](https://docs.microsoft.com/azure/azure-stack/partner).
 - Další informace o prostředí PowerShell ve službě Azure Stack, najdete v článku [modulem pro službu Azure Stack](https://docs.microsoft.com/powershell/azure/azure-stack/overview?view=azurestackps-1.3.0) odkaz.