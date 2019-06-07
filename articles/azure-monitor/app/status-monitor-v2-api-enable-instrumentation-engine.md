---
title: 'Azure Monitor stavu v2 reference k rozhraní API: Povolit instrumentaci modulu | Dokumentace Microsoftu'
description: Odkaz na stav monitorování v2 API. Enable-InstrumentationEngine. Sledování výkonu webu bez opětovného nasazení webu. Funguje s webové aplikace ASP.NET hostované místně, na virtuálních počítačích nebo v Azure.
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
ms.openlocfilehash: 1e30490dbd51f541afd0b7036769cfc638a75877
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/04/2019
ms.locfileid: "66514371"
---
# <a name="status-monitor-v2-api-enable-instrumentationengine-v021-alpha"></a>Rozhraní API v2 monitorování stavu: Enable-InstrumentationEngine (v0.2.1 alfa)

Tento článek popisuje rutiny, která je členem skupiny [modulu Az.ApplicationMonitor PowerShell](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/).

> [!IMPORTANT]
> Stav monitorování v2 je aktuálně ve verzi public preview.
> Tato verze preview je k dispozici bez smlouvu o úrovni služeb, a to nedoporučujeme pro produkční úlohy. Některé funkce nemusí být podporované a některé můžou mít omezené možnosti.
> Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="description"></a>Popis

Umožňuje modulu instrumentace nastavením některé klíče registru.
Restartujte službu IIS se změny projevily.

Modul instrumentace můžete doplnit data shromážděná pomocí sady .NET SDK.
Shromažďuje události a zprávy, které popisují spuštění spravovaného procesu. Tyto události a zprávy zahrnují kódy výsledků závislostí, příkazy HTTP a text příkazu SQL.

Povolte modul instrumentace, pokud:
- Už jsme povolili monitorování pomocí rutiny Enable ale nepovolili modul instrumentace.
- Ručně instrumentovali vaší aplikace spolu se sadami SDK pro .NET a chcete shromažďovat další telemetrické údaje.

> [!IMPORTANT] 
> Tato rutina vyžaduje relaci Powershellu s oprávněními správce.

> [!NOTE] 
> - Tato rutina vyžaduje, přečtěte si a přijměte naše prohlášení licence a ochrana osobních údajů.
> - Instrumentace stroj přidá další režii a je vypnuto ve výchozím nastavení.

## <a name="examples"></a>Příklady

```powershell
PS C:\> Enable-InstrumentationEngine
```

## <a name="parameters"></a>Parametry

### <a name="-acceptlicense"></a>-AcceptLicense
**Volitelné.** Použijte tento přepínač tak, aby přijímal příkaz licence a ochrana osobních údajů v bezobslužné instalace.

### <a name="-verbose"></a>-Verbose
**Společný parametr.** Tento přepínač použijte na výstup podrobných protokolů.

## <a name="output"></a>Výstup


#### <a name="example-output-from-successfully-enabling-the-instrumentation-engine"></a>Příklad výstupu z úspěšném povolení modul instrumentace

```
Configuring IIS Environment for instrumentation engine...
Configuring registry for instrumentation engine...
```

## <a name="next-steps"></a>Další postup

  Zobrazení telemetrických dat:
 - [Zkoumání metrik](../../azure-monitor/app/metrics-explorer.md) pro monitorování výkonu a využití.
- [Prohledávejte události a protokoly](../../azure-monitor/app/diagnostic-search.md) k diagnostice problémů.
- Použití [analytics](../../azure-monitor/app/analytics.md) pro pokročilejší dotazy.
- [Vytváření řídicích panelů](../../azure-monitor/app/overview-dashboard.md).
 
 Přidání další telemetrie:
 - [Vytvářejte webové testy](monitor-web-app-availability.md) k Ujistěte se, že web zůstává živý.
- [Přidání telemetrie webového klienta](../../azure-monitor/app/javascript.md) pro zobrazení výjimek z kódu webové stránky a povolit trasování volání.
- [Přidejte Application Insights SDK do kódu](../../azure-monitor/app/asp-net.md) abyste mohli vložit trasování a protokolování volání.
 
 Lepší využití v2 monitorování stavu:
 - Pomocí naší příručce ke [Poradce při potížích s](status-monitor-v2-troubleshoot.md) v2 monitorování stavu.
 - [Získání konfigurace](status-monitor-v2-api-get-config.md) potvrďte, že nastavení byly zaznamenány správně.
 - [Načíst stav](status-monitor-v2-api-get-status.md) ke kontrole monitorování.
