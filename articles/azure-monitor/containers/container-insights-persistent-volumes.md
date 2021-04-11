---
title: Konfigurace monitorování PV pomocí kontejneru Insights | Microsoft Docs
description: Tento článek popisuje, jak můžete nakonfigurovat monitorování clusterů Kubernetes s trvalými svazky s využitím Container Insights.
ms.topic: conceptual
ms.date: 03/03/2021
ms.openlocfilehash: 7c6ddd62bf06d313987289e444962378cea43fc8
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "105627893"
---
# <a name="configure-pv-monitoring-with-container-insights"></a>Konfigurace monitorování PV pomocí kontejneru Insights

Počínaje agentem verze *ciprod10052020* se teď Azure monitor for Containers Integrated Agent podporuje monitorování využití PV (trvalého svazku). S agentem verze *ciprod01112021* Agent podporuje monitorování inventáře PV, včetně informací o stavu, třídě úložiště, typu, režimech přístupu a dalších podrobnostech.
## <a name="pv-metrics"></a>Metriky PV

Služba Container Insights automaticky začne monitorovat využití SOUČHODNOTA shromažďováním následujících metrik v intervalech 60 – sec a jejich uložením do tabulky **InsightMetrics** .

| Název metriky | Dimenze metriky (značky) | Popis metriky |
|-----|-----------|----------|
| `pvUsedBytes`| podUID, podName, pvcName, pvcNamespace, capacityBytes, clusterId, název_clusteru| Využité místo v bajtech pro konkrétní trvalý svazek s deklarací identity použitou v určitém typu pod. `capacityBytes` je přeložen v jako dimenze v poli značky, aby se snížily náklady na příjem dat a zjednodušily se dotazy.|

Další informace o konfiguraci shromážděných metrik PV [najdete tady](./container-insights-agent-config.md).

## <a name="pv-inventory"></a>Inventář PV

Azure Monitor pro kontejnery automaticky spustí monitorování PVs shromažďováním následujících informací v intervalech 60 – sec a jejich uložením do tabulky **KubePVInventory** .

|Data |Zdroj dat| Typ dat| Pole|
|-----|-----------|----------|-------|
|Inventarizace trvalých svazků v clusteru Kubernetes |Rozhraní API pro Kube |`KubePVInventory` |    PVName, PVCapacityBytes, PVCName, PVCNamespace, PVStatus, PVAccessModes, PVType, PVTypeInfo, PVStorageClassName, PVCreationTimestamp, TimeGenerated, ClusterId, název_clusteru, _ResourceId |

## <a name="monitor-persistent-volumes"></a>Monitorovat trvalé svazky

Azure Monitor for containers obsahuje předem nakonfigurované grafy pro tuto metriku využití a informace o inventáři v šablonách sešitu pro každý cluster. Můžete také povolit Doporučené upozornění pro použití PV a dotazovat se na tyto metriky v Log Analytics.  

### <a name="workload-details-workbook"></a>Sešit s podrobnostmi o úlohách

Grafy využití pro konkrétní úlohy můžete najít na kartě trvalého svazku v sešitu s **podrobnostmi úloh** přímo z clusteru AKS, a to v rozevíracím seznamu **Zobrazit sešity** v podokně přehledy nebo na **kartě sestavy (Preview)** v podokně přehledy.


:::image type="content" source="./media/container-insights-persistent-volumes/pv-workload-example.PNG" alt-text="Příklad sešitu úlohy Azure Monitor PV":::

### <a name="persistent-volume-details-workbook"></a>Sešit podrobností trvalého svazku

Přehled trvalého inventáře svazku můžete najít v sešitu s **podrobnostmi o trvalém svazku** přímo z clusteru AKS tak, že v rozevíracím seznamu **Zobrazit sešity** v podokně přehledy vyberete sešity, nebo na kartě **sestavy (Preview)** v podokně přehledy.


:::image type="content" source="./media/container-insights-persistent-volumes/pv-details-workbook-example.PNG" alt-text="Příklad sešitu s podrobnostmi Azure Monitor PV":::

### <a name="persistent-volume-usage-recommended-alert"></a>Doporučené upozornění na trvalé použití svazku
Můžete povolit doporučenou výstrahu, která vás upozorní, když průměrné využití PV pro objekt pod je nad 80%. Přečtěte si další informace o upozorňování [tady](./container-insights-metric-alerts.md) a o tom, [jak můžete přepsat výchozí prahovou](./container-insights-metric-alerts.md#configure-alertable-metrics-in-configmaps)hodnotu.
## <a name="next-steps"></a>Další kroky

- Další informace o shromažďovaných metrikách PV [najdete tady](./container-insights-agent-config.md).