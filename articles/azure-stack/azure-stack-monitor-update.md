---
title: Monitorování aktualizací v zásobníku Azure pomocí privilegované koncový bod | Microsoft Docs
description: Naučte se používat privilegované koncový bod monitorovat stav aktualizace pro Azure zásobníku integrované systémy.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 449ae53e-b951-401a-b2c9-17fee2f491f1
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/18/2017
ms.author: mabrigg
ms.openlocfilehash: 96eebf340f13f2f5e9e922fee8032d04fce1d130
ms.sourcegitcommit: 0e1c4b925c778de4924c4985504a1791b8330c71
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/06/2018
ms.locfileid: "27621857"
---
# <a name="monitor-updates-in-azure-stack-using-the-privileged-endpoint"></a>Monitorování aktualizací v zásobníku Azure pomocí privilegované koncový bod

*Platí pro: Azure zásobníku integrované systémy*

Privilegované koncového bodu můžete monitorovat průběh aktualizace spustit Azure zásobníku a obnovit Chyba aktualizace, spusťte z poslední úspěšné kroku má zásobník Azure portal k dispozici.  Pomocí portálu Azure zásobníku je doporučená metoda ke správě aktualizací v zásobníku Azure.

Následující nové rutiny prostředí PowerShell pro správu aktualizací jsou zahrnuty v aktualizaci 1710 pro Azure zásobníku integrované systémy.

| Rutina  | Popis  |
|---------|---------|
| `Get-AzureStackUpdateStatus` | Vrátí stav aktualizace aktuálně spuštěné, dokončené nebo se nezdařilo. Poskytuje podrobný stav operace aktualizace a dokument XML, která popisuje aktuální krok a odpovídající stavu. |
| `Get-AzureStackUpdateVerboseLog` | Vrátí podrobné protokoly, které jsou generovány aktualizací. |
| `Resume-AzureStackUpdate` | Obnoví Chyba aktualizace v okamžiku, kdy se nezdařilo. V některých scénářích budete muset provést kroky ke zmírnění předtím, než budete pokračovat v aktualizaci.         |
| | |

## <a name="verify-the-cmdlets-are-available"></a>Ověřte, zda že jsou k dispozici rutiny
Vzhledem k tomu, že jsou v balíčku aktualizace 1710 pro Azure zásobníku nové rutiny, je potřeba získat do určité míry předtím, než je k dispozici možnost monitorování procesu 1710 aktualizace. Obvykle jsou k dispozici, pokud stav na portálu správce označuje, že aktualizace 1710 je na rutiny **restartujte úložiště hostitelů** krok. Konkrétně aktualizace rutiny spadá **krok: spuštěním kroku 2.6 - aktualizovat PrivilegedEndpoint povolených**.

Můžete také určit, zda k dispozici jsou rutiny prostřednictvím kódu programu dotazováním seznamu příkazů z privilegovaných koncového bodu. Chcete-li to provést, spusťte následující příkazy z hostitele životního cyklu hardware nebo z pracovní stanice privilegovaný přístup. Také zajistěte, aby byl privilegované koncový bod důvěryhodného hostitele. Další informace najdete v části Krok 1 ze [přístup privilegované koncový bod](azure-stack-privileged-endpoint.md#access-the-privileged-endpoint). 

1. Vytvoření relace prostředí PowerShell na všech virtuálních počítačů ERCS ve vašem prostředí Azure zásobníku (*předpony*-ERCS01, *předpony*-ERCS02, nebo *předpony*-ERCS03). Nahraďte *předponu* řetězec předpony virtuální počítač, který je specifický pro vaše prostředí.

   ```powershell
   $cred = Get-Credential

   $pepSession = New-PSSession -ComputerName <Prefix>-ercs01 -Credential $cred -ConfigurationName PrivilegedEndpoint 
   ```
   Pokud budete vyzváni k zadání pověření, použijte &lt; *zásobník Azure domény*&gt;\cloudadmin účet nebo účet, který je členem skupiny CloudAdmins. Pro účet CloudAdmin zadejte stejné heslo, které jste zadali během instalace pro účet správce domény AzureStackAdmin.

2. Získáte úplný seznam příkazů, které jsou k dispozici v privilegované koncový bod. 

   ```powershell
   $commands = Invoke-Command -Session $pepSession -ScriptBlock { Get-Command } 
   ```
3. Zjistit, pokud byl aktualizován privilegované koncový bod.

   ```powershell
   $updateManagementModuleName = "Microsoft.Azurestack.UpdateManagement"
    if (($commands | ? Source -eq $updateManagementModuleName)) {
   Write-Host "Privileged endpoint was updated to support update monitoring tools."
    } else {
   Write-Host "Privileged endpoint has not been updated yet. Please try again later."
    } 
   ```

4. Seznam příkazů, které jsou specifické pro modul Microsoft.AzureStack.UpdateManagement.

   ```powershell
   $commands | ? Source -eq $updateManagementModuleName 
   ```
   Příklad:
   ```powershell
   $commands | ? Source -eq $updateManagementModuleName
   
   CommandType     Name                                               Version    Source                                                  PSComputerName
    -----------     ----                                               -------    ------                                                  --------------
   Function        Get-AzureStackUpdateStatus                         0.0        Microsoft.Azurestack.UpdateManagement                   Contoso-ercs01
   Function        Get-AzureStackUpdateVerboseLog                     0.0        Microsoft.Azurestack.UpdateManagement                   Contoso-ercs01
   Function        Resume-AzureStackUpdate                            0.0        Microsoft.Azurestack.UpdateManagement                   Contoso-ercs01
   ``` 

## <a name="use-the-update-management-cmdlets"></a>Pomocí rutin správy aktualizace

> [!NOTE]
> Spusťte následující příkazy z hostitele životního cyklu hardware nebo z pracovní stanice privilegovaný přístup. Také zajistěte, aby byl privilegované koncový bod důvěryhodného hostitele. Další informace najdete v části Krok 1 ze [přístup privilegované koncový bod](azure-stack-privileged-endpoint.md#access-the-privileged-endpoint).

### <a name="connect-to-the-privileged-endpoint-and-assign-session-variable"></a>Připojte k privilegované koncový bod a přiřaďte proměnné relace

Spusťte následující příkazy pro vytvoření relace prostředí PowerShell na všech virtuálních počítačů ERCS ve vašem prostředí Azure zásobníku (*předpony*-ERCS01, *předpony*-ERCS02, nebo *předpony*-ERCS03) a k přiřazení proměnné relace.

```powershell
$cred = Get-Credential

$pepSession = New-PSSession -ComputerName <Prefix>-ercs01 -Credential $cred -ConfigurationName PrivilegedEndpoint 
```
 Pokud budete vyzváni k zadání pověření, použijte &lt; *zásobník Azure domény*&gt;\cloudadmin účet nebo účet, který je členem skupiny CloudAdmins. Pro účet CloudAdmin zadejte stejné heslo, které jste zadali během instalace pro účet správce domény AzureStackAdmin.

### <a name="get-high-level-status-of-the-current-update-run"></a>Získat podrobný stav aktuální spuštění aktualizace 

Chcete-li získat podrobný stav aktuální spuštění, aktualizace, spusťte následující příkazy: 

```powershell
$statusString = Invoke-Command -Session $pepSession -ScriptBlock { Get-AzureStackUpdateStatus -StatusOnly }

$statusString.Value 
```

Možné hodnoty:

- Běží
- Dokončeno
- Neúspěch 
- Zrušeno

Můžete spustit tyto příkazy opakovaně chcete zobrazit aktuální stav. Není nutné znovu vytvořit připojení ke znovu zkontrolujte.

### <a name="get-the-full-update-run-status-with-details"></a>Získat úplné aktualizace spustit s podrobnostmi o stavu 

Můžete získat úplné aktualizace spustit souhrn jako řetězec v kódu XML. Můžete napsat řetězec do souboru pro prozkoumání, nebo ho převést na dokument XML a analyzovat ho pomocí prostředí PowerShell. Následující příkaz analyzuje XML hierarchické seznam aktuálně spuštěných kroky.

```powershell
[xml]$updateStatus = Invoke-Command -Session $pepSession -ScriptBlock { Get-AzureStackUpdateStatus }

$updateStatus.SelectNodes("//Step[@Status='InProgress']")
```

V následujícím příkladu má nejvyšší úrovně krok (cloudu aktualizace) podřízené plán aktualizovat a restartujte úložiště hostitelů. Zobrazuje, že plán restartujte úložiště hostitelů aktualizuje službu úložiště objektů Blob na jednom z hostitelů.

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

### <a name="get-the-verbose-progress-log"></a>Získejte podrobné průběh protokolu

Protokol může zapisovat do souboru pro prozkoumání. To může pomoci diagnostikovat selhání aktualizace.

```powershell
$log = Invoke-Command -Session $pepSession -ScriptBlock { Get-AzureStackUpdateVerboseLog }

$log > ".\UpdateVerboseLog.txt" 
```

### <a name="actively-view-the-verbose-logging"></a>Aktivně zobrazit podrobné protokolování

K aktivně zobrazení podrobného protokolování během aktualizace spustit a přejít na poslední položky, spusťte následující příkazy zadejte relace v interaktivním režimu a zobrazit v protokolu:

```powershell
Enter-PSSession -Session $pepSession 

Get-AzureStackUpdateVerboseLog -Wait 
```
Protokol aktualizuje každých 60 sekund a nový obsah (Pokud je k dispozici) je zapsán do konzoly. 

Během dlouho běžící procesy na pozadí nemusí být zapsán výstup konzoly ke konzole pro nějakou dobu. Pokud chcete zrušit interaktivní výstup, stiskněte kombinaci kláves Ctrl + C. 

### <a name="resume-a-failed-update-operation"></a>Aktualizace se nezdařila operace obnovení

Pokud se aktualizace nezdaří, můžete obnovit aktualizace, spusťte, kde bylo přerušeno.

```powershell
Invoke-Command -Session $pepSession -ScriptBlock { Resume-AzureStackUpdate } 
```

## <a name="troubleshoot"></a>Řešení potíží

Privilegované koncový bod je k dispozici na všechny virtuální počítače ERCS v prostředí Azure zásobníku. Protože připojení není navázat na koncový bod vysoce dostupný, může dojít příležitostně přerušení, upozornění nebo chybové zprávy. Tyto zprávy může znamenat, že relace byla odpojena, nebo že došlo k chybě při komunikaci se službou ECE. Toto chování je očekávané. Můžete operaci za několik minut nebo vytvořit novou relaci privilegované koncový bod na jednom z jiných virtuálních počítačů ERCS. 

## <a name="next-steps"></a>Další postup

- [Správa aktualizací v Azure zásobníku](azure-stack-updates.md) 


