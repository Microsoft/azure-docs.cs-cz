---
title: Modul AADCloudSyncTools PowerShell pro synchronizaci cloudu Azure AD Connect
description: Tento článek popisuje, jak nainstalovat agenta pro zřizování cloudu Azure AD Connect.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 11/30/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: aa358b0c9d7747584deabe761160d3bcbcde8feb
ms.sourcegitcommit: f377ba5ebd431e8c3579445ff588da664b00b36b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/05/2021
ms.locfileid: "99593176"
---
# <a name="aadcloudsynctools-powershell-module-for-azure-ad-connect-cloud-sync"></a>Modul AADCloudSyncTools PowerShell pro synchronizaci cloudu Azure AD Connect

Modul AADCloudSyncTools poskytuje sadu užitečných nástrojů, které vám pomůžou se správou nasazení cloudových synchronizací Azure AD Connect.

## <a name="pre-requisites"></a>Požadavky
Vyžadují se tyto požadavky:

- Všechny požadavky pro tento modul se dají automaticky nainstalovat pomocí nástroje. `Install-AADCloudSyncToolsPrerequisites`
- Tento modul používá ověřování MSAL, takže vyžaduje nainstalovaný modul MSAL.PS. Chcete-li ověřit, v okně PowerShellu spusťte `Get-module MSAL.PS -ListAvailable` . Pokud je modul nainstalovaný správně, zobrazí se odpověď. `Install-AADCloudSyncToolsPrerequisites`K instalaci nejnovější verze nástroje MSAL.PS můžete použít.
- I když modul prostředí PowerShell AzureAD není nezbytným předpokladem pro žádnou funkci tohoto modulu, je užitečné ho také automaticky nainstalovat pomocí nástroje `Install-AADCloudSyncToolsPrerequisites` .
- Ruční instalace modulů z PowerShellu vyžaduje vynucení TLS 1,2. Abyste se ujistili, že je možné nainstalovat moduly, nastavte v relaci PowerShellu před použitím možnost následující.
  ```
   Install-Module:
  [Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12 
  ```


## <a name="install-the-aadcloudsynctools-powershell-module"></a>Instalace modulu PowerShellu pro AADCloudSyncTools
K instalaci a použití modulu AADCloudSyncTools použijte následující postup:

1. Otevřete prostředí Windows PowerShell s oprávněními správce.
2. Zadejte nebo zkopírujte a vložte následující text: `Import-module -Name "C:\Program Files\Microsoft Azure AD Connect Provisioning Agent\Utility\AADCloudSyncTools"`
3. Stiskněte ENTER.
4. Pokud chcete ověřit, že byl modul importován, zadejte nebo zkopírujte a vložte následující: `Get-module AADCloudSyncTools`
5. Nyní byste měli vidět informace o modulu.
6. V dalším kroku nainstalujete AADCloudSyncTools modulu s požadavky pro spuštění: `Install-AADCloudSyncToolsPrerequisites`
7. Při prvním spuštění se modul PoweShellGet nainstaluje, pokud není k dispozici. Pokud chcete načíst nový modul PowershellGet, zavřete okno PowerShellu a otevřete novou relaci PowerShellu s oprávněními správce. 
8. Importujte modul znovu pomocí kroku 3.
9. Spusťte `Install-AADCloudSyncToolsPrerequisites` pro instalaci modulů MSAL a AzureAD.
11. Všechny před reqs by se měly úspěšně nainstalovat ![ modul instalace.](media/reference-powershell/install-1.png)


## <a name="aadcloudsynctools--cmdlets"></a>Rutiny AADCloudSyncTools
### <a name="connect-aadcloudsynctools"></a>Connect-AADCloudSyncTools
Pomocí modulu MSAL.PS vyžádá token pro správce Azure AD pro přístup k Microsoft Graph 


### <a name="export-aadcloudsynctoolslogs"></a>Export-AADCloudSyncToolsLogs
Exportuje a zabalí všechna data pro řešení potíží v komprimovaném souboru následujícím způsobem:
 1. Spustí podrobné trasování pomocí Start-AADCloudSyncToolsVerboseLogs.  Tyto protokoly trasování najdete ve složce C:\ProgramData\Microsoft\Azure AD Connect Provisioning Agent\Trace.
 2. Shromažďuje protokol trasování po dobu 3 minut.
   Můžete zadat jiný čas s-TracingDurationMins nebo přeskočit podrobné trasování s-SkipVerboseTrace.
 3. Zastaví podrobné trasování pomocí Stop-AADCloudSyncToolsVerboseLogs
 4. Shromažďuje protokoly Prohlížeč událostí za posledních 24 hodin.
 5. Komprimuje všechny protokoly agentů, podrobné protokoly a protokoly prohlížeče událostí do komprimovaného souboru ZIP ve složce Dokumenty uživatele. 
 </br>Můžete zadat jinou výstupní složku s-OutputPath. \<folder path\>

### <a name="get-aadcloudsynctoolsinfo"></a>Get-AADCloudSyncToolsInfo
Zobrazuje podrobnosti o Tenantovi Azure AD a stav interních proměnných.

### <a name="get-aadcloudsynctoolsjob"></a>Get-AADCloudSyncToolsJob
Pomocí grafu získá objekty služby AD2AAD a vrátí informace o úloze synchronizace.
Může být také volána pomocí konkrétního ID úlohy synchronizace jako parametr.

### <a name="get-aadcloudsynctoolsjobschedule"></a>Get-AADCloudSyncToolsJobSchedule
Pomocí grafu získá objekty služby AD2AAD a vrátí plán úlohy synchronizace.
Může být také volána pomocí konkrétního ID úlohy synchronizace jako parametr.

### <a name="get-aadcloudsynctoolsjobschema"></a>Get-AADCloudSyncToolsJobSchema
Pomocí grafu získá objekty služby AD2AAD a vrátí schéma úlohy synchronizace.

### <a name="get-aadcloudsynctoolsjobscope"></a>Get-AADCloudSyncToolsJobScope
Pomocí grafu Získá schéma úlohy synchronizace pro zadané ID úlohy synchronizace a vypíše všechny rozsahy skupin filtru.

### <a name="get-aadcloudsynctoolsjobsettings"></a>Get-AADCloudSyncToolsJobSettings
Pomocí grafu získá objekty služby AD2AAD a vrátí nastavení úlohy synchronizace.
Může být také volána pomocí konkrétního ID úlohy synchronizace jako parametr.

### <a name="get-aadcloudsynctoolsjobstatus"></a>Get-AADCloudSyncToolsJobStatus
Pomocí grafu získá objekty služby AD2AAD a vrátí stav úlohy synchronizace.
Může být také volána pomocí konkrétního ID úlohy synchronizace jako parametr.

### <a name="get-aadcloudsynctoolsserviceprincipal"></a>Get-AADCloudSyncToolsServicePrincipal
Pomocí grafu získá objekty služby pro AD2AAD a/nebo SyncFabric.
Bez parametrů budou vracet pouze objekty (AD2AAD) služby.

### <a name="install-aadcloudsynctoolsprerequisites"></a>Install-AADCloudSyncToolsPrerequisites
Zkontroluje přítomnost modulů PowerShellGet v 2.2.4.1 nebo novějších a Azure AD a MSAL.PS a nainstaluje je, pokud chybí.

### <a name="invoke-aadcloudsynctoolsgraphquery"></a>Invoke-AADCloudSyncToolsGraphQuery
Vyvolá webovou žádost pro identifikátor URI, metodu a tělo, které jsou zadané jako parametry.

### <a name="repair-aadcloudsynctoolsaccount"></a>Repair-AADCloudSyncToolsAccount
Pomocí Azure AD PowerShellu odstraní aktuální účet (Pokud je k dispozici) a obnoví ověřování účtu synchronizace pomocí nového účtu synchronizace ve službě Azure AD.

### <a name="restart-aadcloudsynctoolsjob"></a>Restart-AADCloudSyncToolsJob
Restartuje úplnou synchronizaci.

### <a name="resume-aadcloudsynctoolsjob"></a>Resume-AADCloudSyncToolsJob
Pokračuje v synchronizaci z předchozí meze.

### <a name="start-aadcloudsynctoolsverboselogs"></a>Start-AADCloudSyncToolsVerboseLogs
Upraví AADConnectProvisioningAgent.exe.config, aby se povolilo podrobné trasování, a restartuje službu AADConnectProvisioningAgent. můžete použít SkipServiceRestart a zabránit tak restartování služby, ale změny konfigurace se projeví.  Tyto protokoly trasování najdete ve složce C:\ProgramData\Microsoft\Azure AD Connect Provisioning Agent\Trace.

### <a name="stop-aadcloudsynctoolsverboselogs"></a>Stop-AADCloudSyncToolsVerboseLogs
Upraví ' AADConnectProvisioningAgent.exe.config ' pro vypnutí podrobného trasování a restartuje službu AADConnectProvisioningAgent. Pomocí-SkipServiceRestart můžete zabránit restartování služby, ale všechny změny konfigurace se neprojeví.

### <a name="suspend-aadcloudsynctoolsjob"></a>Suspend-AADCloudSyncToolsJob
Pozastaví synchronizaci.

## <a name="next-steps"></a>Další kroky 

- [Co je zřizování?](what-is-provisioning.md)
- [Co je Azure AD Connect synchronizace cloudu?](what-is-cloud-sync.md)

