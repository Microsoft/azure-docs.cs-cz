---
title: Konfigurace monitorování PV pomocí kontejneru Insights | Microsoft Docs
description: Tento článek popisuje, jak můžete nakonfigurovat monitorování clusterů Kubernetes s trvalými svazky s využitím Container Insights.
ms.topic: conceptual
ms.date: 10/20/2020
ms.openlocfilehash: 0afbeab49a6909a0011cd75a0419f7325ca68132
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/03/2021
ms.locfileid: "101713724"
---
# <a name="configure-pv-monitoring-with-container-insights"></a>Konfigurace monitorování PV pomocí kontejneru Insights

Počínaje agentem verze *ciprod10052020* nyní integrovaný agent kontejneru Insights podporuje monitorování využití PV (trvalého svazku).

## <a name="pv-metrics"></a>Metriky PV

Služba Container Insights automaticky spustí sledování PV shromažďováním následujících metrik v intervalech 60sec a jejich uložením do tabulky **InsightMetrics** .

|Název metriky |Dimenze metriky (značky) |Popis |
|------------|------------------------|------------|
| `pvUsedBytes`|`container.azm.ms/pv`|Využité místo v bajtech pro konkrétní trvalý svazek s deklarací identity použitou v určitém typu pod. `pvCapacityBytes` je přeložen v jako dimenze v poli značky, aby se snížily náklady na příjem dat a zjednodušily se dotazy.|

## <a name="monitor-persistent-volumes"></a>Monitorovat trvalé svazky

Služba Container Insights obsahuje předem nakonfigurované grafy pro tuto metriku v sešitu pro každý cluster. Grafy najdete na kartě trvalého svazku sešitu s **podrobnostmi úloh** přímo z clusteru AKS tak, že v levém podokně vyberete sešity a v rozevíracím seznamu **Zobrazit sešity** v přehledu. Můžete také povolit Doporučené upozornění pro použití PV a dotazovat se na tyto metriky v Log Analytics.  

![Příklad sešitu úlohy Azure Monitor PV](./media/container-insights-persistent-volumes/pv-workload-example.PNG)

## <a name="next-steps"></a>Další kroky

- Další informace o shromažďovaných metrikách PV [najdete tady](./container-insights-agent-config.md).
