---
title: 'Stav služby Azure Monitor v2 Reference k rozhraní API: Povolit instrumentaci modulu | Dokumentace Microsoftu'
description: Stav monitorování v2 API odkaz Enable-InstrumentationEngine. Sledování výkonu webu bez opětovného nasazení webu. Funguje s místně hostovanými webovými aplikacemi v ASP.NET, na virtuálních počítačích nebo v Azure.
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
ms.openlocfilehash: e993378634262de25449975431c0a9e3145ca9fb
ms.sourcegitcommit: 25a60179840b30706429c397991157f27de9e886
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/28/2019
ms.locfileid: "66255248"
---
# <a name="status-monitor-v2-api-enable-instrumentationengine-v021-alpha"></a>Rozhraní API v2 monitorování stavu: Enable-InstrumentationEngine (v0.2.1 alfa)

Tento dokument popisuje rutiny, který je dodáván jako člen [modulu Az.ApplicationMonitor PowerShell](https://www.powershellgallery.com/packages/Az.ApplicationMonitor/).

> [!IMPORTANT]
> Stav monitorování v2 je aktuálně ve verzi public preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti.
> Další informace najdete v tématu [dodatečných podmínkách použití systémů Microsoft Azure Preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)

## <a name="description"></a>Popis

Tato rutina vám umožní modul instrumentace nastavením některé klíče registru.
Restartujte službu IIS pro tyto změny se projeví.

Modul instrumentace můžete doplnit data shromážděná pomocí sady .NET SDK.
Události a zprávy budou shromážděna, které popisují spuštění spravovaného procesu. Mimo jiné včetně závislostí kódy výsledků, příkazy HTTP a Text příkazu SQL. 

Povolte modul instrumentace, pokud:
- Už jsme povolili monitorování pomocí povolení rutiny, ale nepovolili InstrumentationEngine.
- Ručně instrumentovali aplikaci, spolu se sadami SDK pro .NET a chcete shromažďovat další telemetrické údaje.

> [!IMPORTANT] 
> Tato rutina vyžaduje relaci Powershellu s oprávněními správce.

> [!NOTE] 
> Tato rutina se vyžadují, abyste přečtěte si a přijměte naše prohlášení licence a ochrana osobních údajů.

> [!NOTE] 
> Instrumentace stroj přidá další režii a je vypnuto ve výchozím nastavení.

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
 - [Zkoumání metrik](../../azure-monitor/app/metrics-explorer.md) pro monitorování výkonu a využití
- [Prohledávejte události a protokoly](../../azure-monitor/app/diagnostic-search.md) k diagnostice problémů
- [Analýzy](../../azure-monitor/app/analytics.md) pro pokročilejší dotazy
- [Vytváření řídicích panelů](../../azure-monitor/app/overview-dashboard.md)
 
 Přidání další telemetrie:
 - [Vytvářejte webové testy](monitor-web-app-availability.md) k Ujistěte se, že web zůstává živý.
- [Přidání telemetrie webového klienta](../../azure-monitor/app/javascript.md) pro zobrazení výjimek z kódu webové stránky a umožnění vložení trasovacích volání.
- [Přidejte sadu Application Insights SDK do kódu](../../azure-monitor/app/asp-net.md) , mohli vložit trasování a protokolování volání
 
 Lepší využití v2 monitorování stavu:
 - Pomocí naší příručce ke [Poradce při potížích](status-monitor-v2-troubleshoot.md) v2 monitorování stavu.
 - [Získání konfigurace](status-monitor-v2-api-get-config.md) potvrďte, že nastavení byly zaznamenány správně.
 - [Načíst stav](status-monitor-v2-api-get-status.md) ke kontrole monitorování.
