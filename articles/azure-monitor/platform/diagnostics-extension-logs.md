---
title: Použití úložiště objektů blob pro službu IIS a úložiště tabulek pro události ve službě Azure Monitor | Dokumenty společnosti Microsoft
description: Azure Monitor může číst protokoly pro služby Azure, které zapisují diagnostiku do úložiště tabulek nebo protokolů Služby IIS zapsaných do úložiště objektů blob.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/14/2020
ms.openlocfilehash: 44368ab90abd189c6a8a0792494828c87142eb20
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77672391"
---
# <a name="collect-data-from-azure-diagnostics-extension-to-azure-monitor-logs"></a>Shromažďování dat z rozšíření diagnostiky Azure do protokolů monitorování Azure
Rozšíření diagnostiky Azure je [agent ve službě Azure Monitor,](agents-overview.md) který shromažďuje data monitorování z hostovaného operačního systému výpočetních prostředků Azure včetně virtuálních počítačů. Tento článek popisuje, jak shromažďovat data shromážděná rozšířenídiagnostiky z Azure Storage do protokolů Azure Monitor.

> [!NOTE]
> Agent Log Analytics v Azure Monitor je obvykle upřednostňovanou metodou pro shromažďování dat z hostovaného operačního systému do protokolů monitorování Azure. Podrobný přehled [agentů Azure Monitoru](agents-overview.md) najdete v článku podrobné porovnání agentů.

## <a name="supported-data-types"></a>Podporované datové typy
Rozšíření diagnostiky Azure ukládá data v účtu Azure Storage. Aby protokoly monitorování Azure shromažďovaly tato data, musí být v následujících umístěních:

| Typ protokolu | Typ prostředku | Umístění |
| --- | --- | --- |
| Protokoly IIS |Virtuální počítače <br> Webové role <br> Role pracovního procesu |wad-iis-logfiles (Úložiště objektů blob) |
| Syslog |Virtuální počítače |LinuxsyslogVer2v0 (Ukládání tabulek) |
| Provozní události service fabric |Uzly Service Fabric |Tabulka WADServiceFabricSystemEventTable |
| Service Fabric Spolehlivé události actor |Uzly Service Fabric |WADServiceFabricReliableActorEventTable |
| Události spolehlivé služby Service Fabric |Uzly Service Fabric |WadServiceFabricReliableServiceEventTable |
| Protokoly událostí systému Windows |Uzly Service Fabric <br> Virtuální počítače <br> Webové role <br> Role pracovního procesu |WADWindowsEventLogsTable (úložiště tabulek) |
| Protokoly ETW systému Windows |Uzly Service Fabric <br> Virtuální počítače <br> Webové role <br> Role pracovního procesu |WADETWEventTable (úložiště stolů) |

## <a name="data-types-not-supported"></a>Datové typy nejsou podporovány.

- Údaje o výkonu z hostovaného operačního systému
- Protokoly služby IIS z webů Azure


## <a name="enable-azure-diagnostics-extension"></a>Povolení rozšíření diagnostiky Azure
Viz [Instalace a konfigurace rozšíření diagnostiky Windows Azure (WAD)](diagnostics-extension-windows-install.md) nebo Použití [linuxového diagnostického rozšíření ke sledování metrik a protokolů,](../../virtual-machines/extensions/diagnostics-linux.md) kde najdete podrobnosti o instalaci a konfiguraci rozšíření diagnostiky. To vám umožní zadat účet úložiště a nakonfigurovat shromažďování dat, které chcete předat protokolům monitorování Azure.


## <a name="collect-logs-from-azure-storage"></a>Shromažďování protokolů z Azure Storage
Pomocí následujícího postupu povolte shromažďování dat rozšíření diagnostiky z účtu Azure Storage:

1. Na webu Azure Portal přejděte na **pracovní prostory Analýzy protokolů** a vyberte svůj pracovní prostor.
1. V části **Zdroje dat pracovního prostoru** v nabídce klikněte na **protokoly účtů úložiště.**
2. Klepněte na tlačítko **Přidat**.
3. Vyberte **účet úložiště,** který obsahuje data, která chcete shromažďovat.
4. Vyberte **datový typ,** který chcete shromáždit.
5. Hodnota pro Source je automaticky naplněna na základě datového typu.
6. Chcete-li uložit konfiguraci, klepněte na tlačítko **OK.**
7. Tento postup opakujte pro další datové typy.

Přibližně za 30 minut, můžete zobrazit data z účtu úložiště v pracovním prostoru Log Analytics. Zobrazí se pouze data, která je zapsána do úložiště po použití konfigurace. Pracovní prostor nečte již existující data z účtu úložiště.

> [!NOTE]
> Portál neověřuje, zda zdroj existuje v účtu úložiště nebo pokud jsou zapisována nová data.



## <a name="next-steps"></a>Další kroky

* [Shromažďujte protokoly a metriky pro služby Azure](collect-azure-metrics-logs.md) pro podporované služby Azure.
* [Povolte řešení](../../azure-monitor/insights/solutions.md) poskytnout přehled o datech.
* K analýze dat [použijte vyhledávací dotazy.](../../azure-monitor/log-query/log-query-overview.md)
