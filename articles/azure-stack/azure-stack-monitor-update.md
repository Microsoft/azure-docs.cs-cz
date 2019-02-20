---
title: Monitorování aktualizací ve službě Azure Stack pomocí privilegovaných koncového bodu | Dokumentace Microsoftu
description: Další informace o použití privilegovaných koncový bod monitorování stavu aktualizace pro integrované systémy Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/19/2019
ms.author: mabrigg
ms.reviewer: fiseraci
ms.lastreviewed: 11/05/2018
ms.openlocfilehash: c62ed7c0fe556c50d3171045321596536d8b7098
ms.sourcegitcommit: 9aa9552c4ae8635e97bdec78fccbb989b1587548
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/20/2019
ms.locfileid: "56427969"
---
# <a name="monitor-updates-in-azure-stack-using-the-privileged-endpoint"></a>Monitorování aktualizací ve službě Azure Stack pomocí privilegovaných koncového bodu

*Platí pro: Integrované systémy Azure Stack*

Můžete použít [privilegovaných koncový bod](azure-stack-privileged-endpoint.md) můžete sledovat průběh služby Azure Stack Hromadná postupná aktualizace, a pokud chcete obnovit aktualizace se nezdařila z poslední úspěšné kroku spuštění by měl na portálu Azure Stack přestanou být dostupné.  Pomocí portálu Azure Stack je doporučená metoda ke správě aktualizací ve službě Azure Stack.

Následující nové rutiny Powershellu pro správu aktualizací jsou součástí aktualizace 1710 pro integrované systémy Azure Stack.

| Rutina  | Popis  |
|---------|---------|
| `Get-AzureStackUpdateStatus` | Vrátí stav aktuálně spuštěné, Dokončeno nebo neúspěšné aktualizace. Poskytuje podrobný stav operace aktualizace a dokument XML, který popisuje aktuální krok a odpovídající stavu. |
| `Resume-AzureStackUpdate` | Obnoví aktualizace se nezdařila v místě, kde se nezdařilo. V některých scénářích budete muset dokončit kroky pro zmírnění rizika, než budete pokračovat v aktualizaci.         |
| | |

## <a name="verify-the-cmdlets-are-available"></a>Ověřte, zda že jsou k dispozici rutiny
Protože rutiny jsou nové v balíčku aktualizace 1710 pro službu Azure Stack, proces aktualizace 1710 musí dát do určitého bodu předtím, než je k dispozici možnost monitorování. Obvykle jsou k dispozici, pokud se stav na portálu správce označuje, že aktualizace 1710 je na rutiny **restartování hostitele úložiště** kroku. Konkrétně, rutina aktualizace spadá **kroku: Provádí se krok 2.6 – PrivilegedEndpoint aktualizace seznamu povolených IP adres**.

Můžete také určit, jestli rutiny jsou dostupné prostřednictvím kódu programu pomocí dotazu na seznam příkazů z privileged koncového bodu. Chcete-li to provést, spusťte následující příkazy z hostitele životního cyklu hardware nebo z pracovní stanice Privileged Access. Také ujistěte se, že privilegovaný koncový bod důvěryhodného hostitele. Další informace najdete v kroku 1 postupu [přístup k privilegovaným koncový bod](azure-stack-privileged-endpoint.md#access-the-privileged-endpoint). 

1. Vytvoření relace prostředí PowerShell na žádném ERCS virtuálních počítačů ve vašem prostředí Azure Stack (*předpony*-ERCS01, *předpony*-ERCS02, nebo *předpony*-ERCS03). Nahraďte *předponu* předponu řetězcem virtuálního počítače, které jsou specifické pro vaše prostředí.

   ```powershell
   $cred = Get-Credential

   $pepSession = New-PSSession -ComputerName <Prefix>-ercs01 -Credential $cred -ConfigurationName PrivilegedEndpoint 
   ```
   Po zobrazení výzvy k zadání pověření, použijte &lt; *doméně služby Azure Stack*&gt;\cloudadmin účet nebo účet, který je členem skupiny CloudAdmins. Pro účet CloudAdmin zadejte stejné heslo, které jste zadali během instalace pro účet správce domény AzureStackAdmin.

2. Získejte úplný seznam příkazů, které jsou k dispozici v privilegovaných koncový bod. 

   ```powershell
   $commands = Invoke-Command -Session $pepSession -ScriptBlock { Get-Command } 
   ```
3. Zjistit, pokud byl aktualizován na privilegované koncový bod.

   ```powershell
   $updateManagementModuleName = "Microsoft.Azurestack.UpdateManagement"
    if (($commands | ? Source -eq $updateManagementModuleName)) {
   Write-Host "Privileged endpoint was updated to support update monitoring tools."
    } else {
   Write-Host "Privileged endpoint has not been updated yet. Please try again later."
    } 
   ```

4. Seznam příkazů specifických pro modul Microsoft.AzureStack.UpdateManagement.

   ```powershell
   $commands | ? Source -eq $updateManagementModuleName 
   ```
   Příklad:
   ```powershell
   $commands | ? Source -eq $updateManagementModuleName
   
   CommandType     Name                                               Version    Source                                                  PSComputerName
    -----------     ----                                               -------    ------                                                  --------------
   Function        Get-AzureStackUpdateStatus                         0.0        Microsoft.Azurestack.UpdateManagement                   Contoso-ercs01
   Function        Resume-AzureStackUpdate                            0.0        Microsoft.Azurestack.UpdateManagement                   Contoso-ercs01
   ``` 

## <a name="use-the-update-management-cmdlets"></a>Pomocí rutin správy aktualizací

> [!NOTE]
> Spusťte následující příkazy z hostitele životního cyklu hardware nebo z pracovní stanice privilegovaný přístup. Také ujistěte se, že privilegovaný koncový bod důvěryhodného hostitele. Další informace najdete v kroku 1 postupu [přístup k privilegovaným koncový bod](azure-stack-privileged-endpoint.md#access-the-privileged-endpoint).

### <a name="connect-to-the-privileged-endpoint-and-assign-session-variable"></a>Připojení ke koncovému bodu privilegovaných a přiřadit proměnné relace

Spuštěním následujících příkazů vytvořte relaci prostředí PowerShell na žádném ERCS virtuálních počítačů ve vašem prostředí Azure Stack (*předpony*-ERCS01, *předpony*-ERCS02, nebo *předpony*-ERCS03) a přiřadit proměnné relace.

```powershell
$cred = Get-Credential

$pepSession = New-PSSession -ComputerName <Prefix>-ercs01 -Credential $cred -ConfigurationName PrivilegedEndpoint 
```
 Po zobrazení výzvy k zadání pověření, použijte &lt; *doméně služby Azure Stack*&gt;\cloudadmin účet nebo účet, který je členem skupiny CloudAdmins. Pro účet CloudAdmin zadejte stejné heslo, které jste zadali během instalace pro účet správce domény AzureStackAdmin.

### <a name="get-high-level-status-of-the-current-update-run"></a>Získat podrobný stav aktuálního spuštění aktualizace 

Pokud chcete získat podrobný stav aktuálního spuštění aktualizace, spusťte následující příkazy: 

```powershell
$statusString = Invoke-Command -Session $pepSession -ScriptBlock { Get-AzureStackUpdateStatus -StatusOnly }

$statusString.Value 
```

Možné hodnoty:

- Spuštěno
- Dokončeno
- Selhalo 
- Zrušeno

Můžete spustit tyto příkazy opakovaně zobrazíte nejnovější stav. Není nutné znovu navázat připojení k zkontrolovat znovu.

### <a name="get-the-full-update-run-status-with-details"></a>Získat úplnou aktualizaci stavu spuštění s podrobnostmi 

Můžete získat úplnou aktualizaci spustit souhrn jako řetězec XML. Můžete zápisu řetězce do souboru pro zkoumání, nebo ji převést do dokumentu XML a analyzuje pomocí prostředí PowerShell. Následující příkaz analyzuje XML k získání hierarchického seznamu aktuálně spuštěné kroků.

```powershell
[xml]$updateStatus = Invoke-Command -Session $pepSession -ScriptBlock { Get-AzureStackUpdateStatus }

$updateStatus.SelectNodes("//Step[@Status='InProgress']")
```

V následujícím příkladu má nejvyšší úrovně krok (Cloud aktualizace) podřízené plán aktualizovat a restartovat úložiště hostitele. Ukazuje, že plán restartování hostitele úložiště se aktualizuje ve službě Blob Storage na jednom z hostitelů.

```powershell
[xml]$updateStatus = Invoke-Command -Session $pepSession -ScriptBlock { Get-AzureStackUpdateStatus }

$updateStatus.SelectNodes("//Step[@Status='InProgress']") 

    FullStepIndex : 2
    Index         : 2
    Name          : Cloud Update
    Description   : Perform cloud update.
    StartTimeUtc  : 2017-10-13T12:50:39.9020351Z
    Status        : InProgress
    Task          : Task
    
    FullStepIndex  : 2.9
    Index          : 9
    Name           : Restart Storage Hosts
    Description    : Restart Storage Hosts.
    EceErrorAction : Stop
    StartTimeUtc   : 2017-10-13T15:44:06.7431447Z
    Status         : InProgress
    Task           : Task
    
    FullStepIndex : 2.9.2
    Index         : 2
    Name          : PreUpdate ACS Blob Service
    Description   : Check function level, update deployment artifacts, configure Blob service settings
    StartTimeUtc  : 2017-10-13T15:44:26.0708525Z
    Status        : InProgress
    Task          : Task
```

### <a name="resume-a-failed-update-operation"></a>Operace aktualizace se nezdařila

Pokud se aktualizace nezdaří, můžete pokračovat tam, kde skončila postupné aktualizace.

```powershell
Invoke-Command -Session $pepSession -ScriptBlock { Resume-AzureStackUpdate } 
```

## <a name="troubleshoot"></a>Řešení potíží

Privilegované koncový bod je k dispozici na všech ERCS virtuálních počítačů v prostředí Azure Stack. Vzhledem k tomu, že nebude připojení na koncový bod s vysokou dostupností, může dojít k občasnému přerušení, upozornění nebo chybové zprávy. Tyto zprávy může znamenat, že relace byla odpojená, nebo že došlo k chybě při komunikaci se službou OSN. Jde o očekávané chování. Můžete zkuste operaci zopakovat za několik minut nebo vytvořit novou relaci privileged koncový bod v jednom z jiné virtuální počítače ERCS. 

## <a name="next-steps"></a>Další postup

- [Správa aktualizací ve službě Azure Stack](azure-stack-updates.md) 


