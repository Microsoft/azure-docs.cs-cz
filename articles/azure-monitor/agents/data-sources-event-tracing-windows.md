---
title: Shromažďování událostí trasování událostí pro Windows (ETW) pro analýzu Azure Monitor protokolů
description: Naučte se shromažďovat trasování událostí pro Windows (ETW) pro analýzu v protokolech Azure Monitor.
services: azure-monitor
ms.topic: conceptual
ms.author: jamesfit
author: jimmyfit
ms.date: 01/29/2021
ms.openlocfilehash: 096d16bb2c7249ec99f37fbee9d8ffc8f0e45db2
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/04/2021
ms.locfileid: "102050762"
---
# <a name="collecting-event-tracing-for-windows-etw-events-for-analysis-azure-monitor-logs"></a>Shromažďování událostí trasování událostí pro Windows (ETW) pro analýzu Azure Monitor protokolů

*Trasování událostí pro Windows (ETW)* poskytuje mechanismus pro instrumentaci aplikací v uživatelském režimu a ovladačů režimu jádra. Agent Log Analytics se používá ke [shromažďování událostí Windows](./data-sources-windows-events.md) zapsaných do kanálu pro správu a provoz [trasování událostí](/windows/win32/wes/eventmanifestschema-channeltype-complextype)pro Windows. Občas ale potřebuje zachytit a analyzovat další události, jako jsou ty, které jsou zapsané do analytického kanálu.  

## <a name="event-flow"></a>Tok událostí

Chcete-li úspěšně shromažďovat [události ETW založené na manifestech](/windows/win32/etw/about-event-tracing#types-of-providers) k analýze v protokolu Azure monitor, je nutné použít [rozšíření Azure Diagnostics](./diagnostics-extension-overview.md) pro Windows (WAD). V tomto scénáři diagnostické rozšíření funguje jako příjemce ETW a zapisuje události do Azure Storage (tabulky) jako zprostředkující úložiště. Tady se uloží v tabulce s názvem **WADETWEventTable**. Log Analytics pak shromáždí data tabulky z Azure Storage a prezentuje je jako tabulka s názvem **ETWEvent**.

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

Ujistěte se, že je na všech zdrojových systémech [nainstalované](./diagnostics-extension-windows-install.md#install-with-azure-portal) *rozšíření Windows Diagnostics* .

### <a name="step-3-configure-etw-log-collection"></a>Krok 3: Konfigurace shromažďování protokolů ETW

1. Přejít do okna **nastavení diagnostiky** virtuálního počítače

2. Vyberte kartu **protokoly** .

3. Přejděte dolů a povolte možnost **události trasování událostí pro Windows (ETW)** ![ obrazovka nastavení diagnostiky.](./media/data-sources-event-tracing-windows/enable-event-tracing-windows-collection.png)

4. Nastavte identifikátor GUID zprostředkovatele nebo třídu poskytovatele na základě zprostředkovatele, pro který konfigurujete kolekci.

5. Nastavte [**úroveň protokolu**](/windows/win32/etw/configuring-and-starting-an-event-tracing-session) podle potřeby.

6. Klikněte na tři tečky vedle zadaného poskytovatele a pak klikněte na **Konfigurovat** .

7. Zajistěte, aby byla **výchozí cílová tabulka** nastavená na **etweventtable** .

8. V případě potřeby nastavte [**Filtr klíčových slov**](/windows/win32/wes/defining-keywords-used-to-classify-types-of-events) .

9. Uložit nastavení zprostředkovatele a protokolu

Po vygenerování vyhovujících událostí byste měli začít zobrazovat události ETW, které se zobrazují v tabulce **WADetweventtable** v Azure Storage. Tuto možnost můžete potvrdit pomocí Průzkumník služby Azure Storage.

### <a name="step-4-configure-log-analytics-storage-account-collection"></a>Krok 4: konfigurace Log Analytics kolekce účtů úložiště

Při shromažďování protokolů z Azure Storage postupujte podle [těchto pokynů](https://docs.microsoft.com/azure/azure-monitor/essentials/diagnostics-extension-logs#collect-logs-from-azure-storage) . Po nakonfigurování se data události trasování událostí pro Windows musí zobrazit v Log Analytics pod tabulkou **ETWEvent** .

## <a name="next-steps"></a>Další kroky
- Použití [vlastních polí](../logs/custom-fields.md) k vytvoření struktury v UDÁLOSTech ETW
- Přečtěte si o [dotazech protokolů](../logs/log-query-overview.md) , které analyzují data shromážděná ze zdrojů dat a řešení.
