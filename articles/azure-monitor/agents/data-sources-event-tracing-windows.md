---
title: Shromažďování událostí trasování událostí pro Windows (ETW) pro analýzu Azure Monitor protokolů
description: Naučte se shromažďovat trasování událostí pro Windows (ETW) pro analýzu v protokolech Azure Monitor.
services: azure-monitor
ms.subservice: logs
ms.topic: conceptual
ms.author: jamesfit
author: jimmyfit
ms.date: 01/29/2021
ms.openlocfilehash: 6239cf48794c74c5dd810fda42476df399300578
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/17/2021
ms.locfileid: "100610554"
---
# <a name="collecting-event-tracing-for-windows-etw-events-for-analysis-azure-monitor-logs"></a>Shromažďování událostí trasování událostí pro Windows (ETW) pro analýzu Azure Monitor protokolů

*Trasování událostí pro Windows (ETW)* poskytuje mechanismus pro instrumentaci aplikací v uživatelském režimu a ovladačů režimu jádra. Agent Log Analytics se používá ke [shromažďování událostí Windows](https://docs.microsoft.com/azure/azure-monitor/platform/data-sources-windows-events) zapsaných do kanálu pro správu a provoz [trasování událostí](https://docs.microsoft.com/windows/win32/wes/eventmanifestschema-channeltype-complextype)pro Windows. Občas ale potřebuje zachytit a analyzovat další události, jako jsou ty, které jsou zapsané do analytického kanálu.  

## <a name="event-flow"></a>Tok událostí

Chcete-li úspěšně shromažďovat [události ETW založené na manifestech](https://docs.microsoft.com/windows/win32/etw/about-event-tracing#types-of-providers) k analýze v protokolu Azure monitor, je nutné použít [rozšíření Azure Diagnostics](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostics-extension-overview) pro Windows (WAD). V tomto scénáři diagnostické rozšíření funguje jako příjemce ETW a zapisuje události do Azure Storage (tabulky) jako zprostředkující úložiště. Tady se uloží v tabulce s názvem **WADETWEventTable**. Log Analytics pak shromáždí data tabulky z Azure Storage a prezentuje je jako tabulka s názvem **ETWEvent**.

![Tok událostí](./media/data-sources-event-tracing-windows/event-flow.png)

## <a name="configuring-etw-log-collection"></a>Konfigurace shromažďování protokolů ETW

### <a name="step-1-locate-the-correct-etw-provider"></a>Krok 1: vyhledání správného poskytovatele ETW

Pomocí některého z následujících příkazů můžete vytvořit výčet zprostředkovatelů ETW ve zdrojovém systému Windows.

Příkazový řádek:

```
logman query providers
```

PowerShell:
```
Get-NetEventProvider -ShowInstalled | Select-Object Name, Guid
```
Volitelně můžete zvolit přesměrování tohoto výstupu PowerShellu na Out-Gridview pro usnadnění navigace.

Poznamenejte si název poskytovatele ETW a identifikátor GUID, který se zařadí do protokolu pro analýzu nebo ladění, který je uveden v Prohlížeč událostí, nebo do modulu, pro který chcete shromažďovat data událostí.

### <a name="step-2-diagnostics-extension"></a>Krok 2: rozšíření diagnostiky

Ujistěte se, že je na všech zdrojových systémech [nainstalované](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostics-extension-windows-install#install-with-azure-portal) *rozšíření Windows Diagnostics* .

### <a name="step-3-configure-etw-log-collection"></a>Krok 3: Konfigurace shromažďování protokolů ETW

1. Přejít do okna **nastavení diagnostiky** virtuálního počítače

2. Vyberte kartu **protokoly** .

3. Přejděte dolů a povolte možnost **události trasování událostí pro Windows (ETW)** ![ obrazovka nastavení diagnostiky.](./media/data-sources-event-tracing-windows/enable-event-tracing-windows-collection.png)

4. Nastavte identifikátor GUID zprostředkovatele nebo třídu poskytovatele na základě zprostředkovatele, pro který konfigurujete kolekci.

5. Nastavte [**úroveň protokolu**](https://docs.microsoft.com/windows/win32/etw/configuring-and-starting-an-event-tracing-session) podle potřeby.

6. Klikněte na tři tečky vedle zadaného poskytovatele a pak klikněte na **Konfigurovat** .

7. Zajistěte, aby byla **výchozí cílová tabulka** nastavená na **etweventtable** .

8. V případě potřeby nastavte [**Filtr klíčových slov**](https://docs.microsoft.com/windows/win32/wes/defining-keywords-used-to-classify-types-of-events) .

9. Uložit nastavení zprostředkovatele a protokolu

Po vygenerování vyhovujících událostí byste měli začít zobrazovat události ETW, které se zobrazují v tabulce **WADetweventtable** v Azure Storage. Tuto možnost můžete potvrdit pomocí Průzkumník služby Azure Storage.

### <a name="step-4-configure-log-analytics-storage-account-collection"></a>Krok 4: konfigurace Log Analytics kolekce účtů úložiště

Při shromažďování protokolů z Azure Storage postupujte podle [těchto pokynů](https://docs.microsoft.com/azure/azure-monitor/platform/diagnostics-extension-logs#collect-logs-from-azure-storage) . Po nakonfigurování se data události trasování událostí pro Windows musí zobrazit v Log Analytics pod tabulkou **ETWEvent** .

## <a name="next-steps"></a>Další kroky
- Použití [vlastních polí](https://docs.microsoft.com/azure/azure-monitor/platform/custom-fields) k vytvoření struktury v UDÁLOSTech ETW
- Přečtěte si o [dotazech protokolů](https://docs.microsoft.com/azure/azure-monitor/log-query/log-query-overview) , které analyzují data shromážděná ze zdrojů dat a řešení.
