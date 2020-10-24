---
title: Konfigurace monitorování PV pomocí Azure Monitor pro kontejnery | Microsoft Docs
description: Tento článek popisuje, jak můžete nakonfigurovat monitorování clusterů Kubernetes s trvalými svazky s Azure Monitor pro kontejnery.
ms.topic: conceptual
ms.date: 10/20/2020
ms.openlocfilehash: 30e99c2abbc66de257f9623dedc901fca51976c1
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/23/2020
ms.locfileid: "92492157"
---
# <a name="configure-pv-monitoring-with-azure-monitor-for-containers"></a>Konfigurace monitorování PV pomocí Azure Monitor pro kontejnery

Počínaje agentem verze *ciprod10052020*, integrovaný agent Azure monitor for Containers teď podporuje monitorování využití PV (trvalého objemu).

## <a name="pv-metrics"></a>Metriky PV

Azure Monitor pro kontejnery automaticky spustí sledování PV shromažďováním následujících metrik v intervalech 60sec a jejich uložením do tabulky **InsightMetrics** .

|Název metriky |Dimenze metriky (značky) |Popis |
|------------|------------------------|------------|
| `pvUsedBytes`|`container.azm.ms/pv`|Využité místo v bajtech pro konkrétní trvalý svazek s deklarací identity použitou v určitém typu pod. `pvCapacityBytes` je přeložen v jako dimenze v poli značky, aby se snížily náklady na příjem dat a zjednodušily se dotazy.|

## <a name="monitor-persistent-volumes"></a>Monitorovat trvalé svazky

Azure Monitor for containers obsahuje předem nakonfigurované grafy pro tuto metriku v sešitu pro každý cluster. Grafy najdete na kartě trvalého svazku sešitu s **podrobnostmi úloh** přímo z clusteru AKS tak, že v levém podokně vyberete sešity a v rozevíracím seznamu **Zobrazit sešity** v přehledu. Můžete také povolit Doporučené upozornění pro použití PV a dotazovat se na tyto metriky v Log Analytics.  

![Příklad sešitu úlohy Azure Monitor PV](./media/container-insights-persistent-volumes/pv-workload-example.PNG)

## <a name="next-steps"></a>Další kroky

- Další informace o shromažďovaných metrikách PV [najdete tady](https://aka.ms/ci/pvconfig).
