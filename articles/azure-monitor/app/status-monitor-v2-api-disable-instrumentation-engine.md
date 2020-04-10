---
title: Azure Application Insights Agent Disable-InstrumentationEngine
description: Odkaz na rozhraní API agenta Application Insights. Zakázat instrumentaceMotor. Sledujte výkon webových stránek bez opětovného nasazení webu. Funguje s ASP.NET webových aplikací hostovaných místně, ve virtuálních počítačích nebo v Azure.
ms.topic: conceptual
author: TimothyMothra
ms.author: tilee
ms.date: 04/23/2019
ms.openlocfilehash: 6c52d429b527c437db5bb8aaf7e3cc1f1c52af57
ms.sourcegitcommit: 25490467e43cbc3139a0df60125687e2b1c73c09
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/09/2020
ms.locfileid: "80998374"
---
# <a name="application-insights-agent-api-disable-instrumentationengine"></a>Rozhraní API agenta application insights: Disable-InstrumentationEngine

Tento článek popisuje rutinu, která je členem [modulu Az.ApplicationMonitor PowerShell](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/).

## <a name="description"></a>Popis
Zakáže instrumentační modul odebráním některých klíčů registru.
Změny se projeví až po restartování služby IIS.

> [!IMPORTANT] 
> Tato rutina vyžaduje relaci prostředí PowerShell s oprávněními správce.

## <a name="examples"></a>Příklady

```powershell
PS C:\> Disable-InstrumentationEngine
```

## <a name="parameters"></a>Parametry 

### <a name="-verbose"></a>-Podrobné
**Společný parametr.** Tento přepínač použijte k výstupu podrobných protokolů.

## <a name="output"></a>Výstup


#### <a name="example-output-from-successfully-disabling-the-instrumentation-engine"></a>Příklad výstupu z úspěšného vypnutí instrumentačního stroje

```
Configuring IIS Environment for instrumentation engine...
Registry: removing 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\IISADMIN[Environment]'
Registry: removing 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\W3SVC[Environment]'
Registry: removing 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\WAS[Environment]'
Configuring registry for instrumentation engine...
```


## <a name="next-steps"></a>Další kroky

 S agentem Application Insights toho zvládnete víc:
 - Použijte náš průvodce [k řešení potíží s](status-monitor-v2-troubleshoot.md) agentem Application Insights.
