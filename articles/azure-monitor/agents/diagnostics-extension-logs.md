---
title: Použijte úložiště objektů BLOB pro služby IIS a úložiště tabulek pro události v Azure Monitor | Microsoft Docs
description: Azure Monitor může číst protokoly pro služby Azure, které zapisují diagnostiku do tabulkového úložiště nebo do protokolů IIS zapsaných do úložiště objektů BLOB.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/14/2020
ms.openlocfilehash: deb6b5f3718c1a7c84e3591bf9abcceb72b785da
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/04/2021
ms.locfileid: "102054579"
---
# <a name="collect-data-from-azure-diagnostics-extension-to-azure-monitor-logs"></a>Shromažďovat data z rozšíření diagnostiky Azure do protokolů Azure Monitor
Rozšíření Azure Diagnostics je [Agent v Azure monitor](../agents/agents-overview.md) , který shromažďuje data monitorování z hostovaného operačního systému výpočetních prostředků Azure, včetně virtuálních počítačů. Tento článek popisuje, jak shromažďovat data shromážděná diagnostickým rozšířením z Azure Storage do Azure Monitor protokolů.

> [!NOTE]
> Agent Log Analytics v Azure Monitor je obvykle upřednostňovanou metodou shromažďování dat z hostovaného operačního systému do protokolů Azure Monitor. Podrobné porovnání agentů najdete v tématu [Přehled agentů Azure monitor](../agents/agents-overview.md) .

## <a name="supported-data-types"></a>Podporované datové typy
Rozšíření diagnostiky Azure ukládá data do účtu Azure Storage. Protokoly Azure Monitor ke shromáždění těchto dat musí být v následujících umístěních:

| Typ protokolu | Typ prostředku | Umístění |
| --- | --- | --- |
| Protokoly IIS |Virtual Machines <br> Webové role <br> Role pracovního procesu |WAD – IIS – soubory protokolu (Blob Storage) |
| Syslog |Virtual Machines |LinuxsyslogVer2v0 (Table Storage) |
| Service Fabric provozní události |Uzly Service Fabric |WADServiceFabricSystemEventTable |
| Service Fabric spolehlivých událostí objektu actor |Uzly Service Fabric |WADServiceFabricReliableActorEventTable |
| Service Fabric spolehlivých událostí služby |Uzly Service Fabric |WADServiceFabricReliableServiceEventTable |
| Protokoly událostí systému Windows |Uzly Service Fabric <br> Virtual Machines <br> Webové role <br> Role pracovního procesu |WADWindowsEventLogsTable (Table Storage) |
| Protokoly ETW systému Windows |Uzly Service Fabric <br> Virtual Machines <br> Webové role <br> Role pracovního procesu |WADETWEventTable (Table Storage) |

## <a name="data-types-not-supported"></a>Datové typy nejsou podporovány.

- Údaje o výkonu hostovaného operačního systému
- Protokoly služby IIS z Azure websites


## <a name="enable-azure-diagnostics-extension"></a>Povolit rozšíření Azure Diagnostics
Podrobnosti o instalaci a konfiguraci rozšíření diagnostiky najdete v tématu [instalace a konfigurace rozšíření Windows Azure Diagnostics (WAD)](../agents/diagnostics-extension-windows-install.md) nebo [použití diagnostického rozšíření Linux k monitorování metrik a protokolů](../../virtual-machines/extensions/diagnostics-linux.md) . To vám umožní zadat účet úložiště a nakonfigurovat shromažďování dat, která chcete přeAzure Monitor protokoly.


## <a name="collect-logs-from-azure-storage"></a>Shromažďovat protokoly z Azure Storage
Pomocí následujícího postupu můžete povolit shromažďování diagnostických dat rozšíření z Azure Storage účtu:

1. V Azure Portal přejdete do **Log Analytics pracovních prostorů** a vyberete svůj pracovní prostor.
1. V nabídce v části **zdroje dat pracovního prostoru** klikněte na **protokoly účtů úložiště** .
2. Klikněte na tlačítko  **Přidat**.
3. Vyberte **účet úložiště** , který obsahuje data, která se mají shromažďovat.
4. Vyberte **datový typ** , který chcete shromáždit.
5. Hodnota pro zdroj se vyplní automaticky na základě datového typu.
6. Konfiguraci uložíte kliknutím na **OK** .
7. Opakujte pro další datové typy.

Za přibližně 30 minut budete moci zobrazit data z účtu úložiště v pracovním prostoru Log Analytics. Po použití konfigurace se zobrazí pouze data zapsaná do úložiště. Pracovní prostor nečte již existující data z účtu úložiště.

> [!NOTE]
> Portál neověřuje, jestli zdroj existuje v účtu úložiště, nebo jestli se zapisují nová data.



## <a name="next-steps"></a>Další kroky

* [Shromážděte protokoly a metriky pro služby Azure](../essentials/resource-logs.md#send-to-log-analytics-workspace) pro podporované služby Azure.
* [Povolte řešení](../insights/solutions.md) a poskytněte vám tak přehled o datech.
* Data můžete analyzovat [pomocí vyhledávacích dotazů](../logs/log-query-overview.md) .