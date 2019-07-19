---
title: 'Reference k rozhraní API služby Azure Monitorování stavu v2: Zakázat modul instrumentace | Microsoft Docs'
description: Reference k rozhraní API pro Monitorování stavu v2 Disable-InstrumentationEngine. Monitorujte výkon webu bez nutnosti opětovného nasazení webu. Funguje s ASP.NET webovými aplikacemi hostovanými místně, na virtuálních počítačích nebo v Azure.
services: application-insights
documentationcenter: .net
author: MS-TimothyMothra
manager: alexklim
ms.assetid: 769a5ea4-a8c6-4c18-b46c-657e864e24de
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: tilee
ms.openlocfilehash: 827f8555718a22979617c9b0794dce3bd92207f4
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/18/2019
ms.locfileid: "68326393"
---
# <a name="status-monitor-v2-api-disable-instrumentationengine"></a>Rozhraní API pro Monitorování stavu v2: Disable-InstrumentationEngine

Tento článek popisuje rutinu, která je členem [modulu PowerShellu AZ. ApplicationMonitor](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/).

## <a name="description"></a>Popis
Zakáže modul instrumentace odebráním některých klíčů registru.
Restartujte službu IIS, aby se změny projevily.

> [!IMPORTANT] 
> Tato rutina vyžaduje relaci PowerShellu s oprávněními správce.

## <a name="examples"></a>Příklady

```powershell
PS C:\> Disable-InstrumentationEngine
```

## <a name="parameters"></a>Parametry 

### <a name="-verbose"></a>– Verbose
**Společný parametr** Pomocí tohoto přepínače můžete vyvýstupovat podrobné protokoly.

## <a name="output"></a>Výstup


#### <a name="example-output-from-successfully-disabling-the-instrumentation-engine"></a>Příklad výstupu z úspěšného zakázání modulu instrumentace

```
Configuring IIS Environment for instrumentation engine...
Registry: removing 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\IISADMIN[Environment]'
Registry: removing 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\W3SVC[Environment]'
Registry: removing 'HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\WAS[Environment]'
Configuring registry for instrumentation engine...
```


## <a name="next-steps"></a>Další kroky

 Další informace najdete v Monitorování stavu v2:
 - Použijte náš průvodce k [řešení potíží s](status-monitor-v2-troubleshoot.md) monitorování stavu v2.
