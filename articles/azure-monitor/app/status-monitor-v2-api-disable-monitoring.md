---
title: Reference k rozhraní API agenta Azure Application Insights
description: Reference k rozhraní API agenta Application Insights. Disable-ApplicationInsightsMonitoring. Monitorujte výkon webu bez nutnosti opětovného nasazení webu. Funguje s ASP.NET webovými aplikacemi hostovanými místně, na virtuálních počítačích nebo v Azure.
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 04/23/2019
ms.openlocfilehash: e30b9757600bbc4a9baf3db00534e3457a6574a5
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/24/2019
ms.locfileid: "72899773"
---
# <a name="application-insights-agent-api-disable-applicationinsightsmonitoring"></a>Rozhraní API pro Application Insights agenta: Disable-ApplicationInsightsMonitoring

Tento článek popisuje rutinu, která je členem [modulu PowerShellu AZ. ApplicationMonitor](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/).

## <a name="description"></a>Popis

Zakáže monitorování v cílovém počítači.
Tato rutina odstraní úpravy souboru applicationHost. config služby IIS a odebere klíče registru.

> [!IMPORTANT] 
> Tato rutina vyžaduje relaci PowerShellu s oprávněními správce.

## <a name="examples"></a>Příklady

```powershell
PS C:\> Disable-ApplicationInsightsMonitoring
```

## <a name="parameters"></a>Parametry 

### <a name="-verbose"></a>– Verbose
**Společný parametr** Pomocí tohoto přepínače zobrazíte podrobné protokoly.

## <a name="output"></a>Výstup


#### <a name="example-output-from-successfully-disabling-monitoring"></a>Příklad výstupu z úspěšného zakázání monitorování

```
Initiating Disable Process
Applying transformation to 'C:\Windows\System32\inetsrv\config\applicationHost.config'
'C:\Windows\System32\inetsrv\config\applicationHost.config' backed up to 'C:\Windows\System32\inetsrv\config\applicationHost.config.backup-2019-03-26_08-59-00z'
in :1,237
No element in the source document matches '/configuration/location[@path='']/system.webServer/modules/add[@name='ManagedHttpModuleHelper']'
Not executing RemoveAll (transform line 1, 546)
Transformation to 'C:\Windows\System32\inetsrv\config\applicationHost.config' was successfully applied. Operation: 'disable'
GAC Module will not be removed, since this operation might cause IIS instabilities
Configuring IIS Environment for codeless attach...
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\IISADMIN[Environment]
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\W3SVC[Environment]
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\WAS[Environment]
Configuring IIS Environment for instrumentation engine...
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\IISADMIN[Environment]
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\W3SVC[Environment]
Registry: skipping non-existent 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\WAS[Environment]
Configuring registry for instrumentation engine...
Successfully disabled Application Insights Status Monitor
```


## <a name="next-steps"></a>Další kroky

 Další Application Insights agenta:
 - Pomocí naší příručky můžete [řešit potíže s](status-monitor-v2-troubleshoot.md) agentem Application Insights.
