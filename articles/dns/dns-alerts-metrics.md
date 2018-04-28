---
title: Azure DNS metriky a výstrahy | Microsoft Docs
description: Další informace o Azure DNS metriky a výstrahy.
services: dns
documentationcenter: na
author: KumudD
manager: jennoc
editor: ''
ms.assetid: ''
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/17/2018
ms.author: kumud
ms.openlocfilehash: 54c4df446ee5c1bf8d29dd6c33b304f39ce8f1b8
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/19/2018
---
# <a name="azure-dns-metrics-and-alerts"></a>Azure DNS metriky a výstrahy
Azure DNS je hostování služba domén DNS, která poskytuje překlad pomocí infrastruktury Microsoft Azure. Tento článek popisuje metriky a výstrah pro službu Azure DNS.

## <a name="azure-dns-metrics"></a>Azure DNS metriky

Azure DNS poskytuje metriky pro zákazníky a umožňuje jim monitorování konkrétních aspektů jejich ty, které jsou hostované ve službě DNS. Kromě toho se metrik Azure DNS, můžete nakonfigurovat a přijímat výstrahy na základě podmínek, které vás zajímají. Podle metrik, které jsou k dispozici prostřednictvím [služba Azure sledování](../monitoring-and-diagnostics/index.yml). Azure DNS poskytuje následující metriky prostřednictvím monitorování Azure pro zóny DNS:

-   QueryVolume
-   RecordSetCount
-   RecordSetCapacityUtilization

Můžete také zjistit [definice tyto metriky](../monitoring-and-diagnostics/monitoring-supported-metrics.md#microsoftnetworkdnszones) na stránce dokumentace Azure monitorování.
>[!NOTE]
> V tomto okamžiku tyto metriky jsou k dispozici pouze pro veřejné DNS zóny hostované v Azure DNS. Pokud máte privátní zóny hostované v Azure DNS, nebude tyto metriky zadejte data pro tyto zóny. Kromě toho metriky a výstrah funkce je podporován pouze v Azure veřejného cloudu. Podporu pro svrchovaných cloudy bude postupovat podle později. 

Podrobné úroveň dimenze pro tyto metriky je zóna DNS.

### <a name="query-volume"></a>Dotaz svazku

*Dotazu svazku* údaj v Azure DNS se zobrazí na svazek dotazy DNS (dotazu provoz), který přijme Azure DNS pro zónu DNS. Jednotky měření počtu a agregace je celkový počet všech dotazů přijatých prostřednictvím v časovém intervalu. Chcete-li zobrazit tuto metriku, vyberte prostředí Průzkumníka metrik (preview) na kartě monitorování na portálu Azure. Vyberte z rozevíracího seznamu prostředků zónu DNS, vyberte metriku svazku dotazu a vyberte součet jako agregace. Následující snímek obrazovky ukazuje příklad.  Další informace o Průzkumníku metrik prostředí a grafů, najdete v tématu [Průzkumníku metrik Azure monitorování](../monitoring-and-diagnostics/monitoring-metric-charts.md).

![Dotaz svazku](./media/dns-alerts-metrics/dns-metrics-query-volume.png)

*Obrázek: Metriky Azure svazku dotazu DNS*

### <a name="record-set-count"></a>Počet sady záznamů
*Nastavit počet záznamů* metrika zobrazuje počet sad záznamů v Azure DNS pro zónu DNS. Všechny sady záznamů definované ve vaší zóně, se počítají. Jednotky měření počtu a agregace je maximální počet všechny sady záznamů. Chcete-li zobrazit tuto metriku, vyberte **metriky (preview)** rozhraní explorer z **monitorování** na portálu Azure. Vyberte zónu DNS z **prostředků** rozevíracího seznamu, vyberte **nastavit počet záznamů** metriky a pak vyberte **maximální** jako **agregace** . Další informace o Průzkumníku metrik prostředí a grafů, najdete v tématu [Průzkumníku metrik Azure monitorování](../monitoring-and-diagnostics/monitoring-metric-charts.md). 

![Počet sady záznamů](./media/dns-alerts-metrics/dns-metrics-record-set-count.png)

*Obrázek: Azure DNS záznam nastavit počet metriky*


### <a name="record-set-capacity-utilization"></a>Sady záznamů využití kapacity
*Využití kapacity nastavit záznam* metrika v Azure DNS zobrazuje procento využití kapacity sady záznamů v zóně DNS. Každé zóny DNS v Azure DNS je závislosti na limitu záznamů, která definuje maximální počet sad záznamů, které jsou povoleny pro danou zónu (viz [DNS omezení](dns-zones-records.md#limits)). Proto tato metrika ukazuje, jak blízko jste nedosáhli limitu záznamů. Například pokud máte 500 sady záznamů nakonfigurována pro vaší zónu DNS a zóna obsahuje výchozí limit sada záznamů 5000, RecordSetCapacityUtilization metrika se zobrazí hodnota % 10, (který byl získán dělicí 500 podle 5000). Měrné jednotky je **procento** a **agregace** typ je **maximální**. Chcete-li zobrazit tuto metriku, vyberte prostředí Průzkumníka metrik (preview) na kartě monitorování na portálu Azure. Vyberte z rozevíracího seznamu prostředků zónu DNS, vyberte záznam využití kapacity nastavte metriku a vyberte maximální počet jako agregace. Následující snímek obrazovky ukazuje příklad. Další informace o Průzkumníku metrik prostředí a grafů, najdete v tématu [Průzkumníku metrik Azure monitorování](../monitoring-and-diagnostics/monitoring-metric-charts.md). 

![Počet sady záznamů](./media/dns-alerts-metrics/dns-metrics-record-set-capacity-uitlization.png)

*Obrázek: Azure DNS záznam nastavit využití kapacity metriky*

## <a name="alerts-in-azure-dns"></a>Výstrahy v Azure DNS
Azure monitorování poskytuje schopnost výstraha s hodnotami dostupné metriky. Metriky DNS jsou k dispozici v nové prostředí konfigurace výstrahy. Jak je popsáno v části [Azure monitorování výstrahy dokumentaci](../monitoring-and-diagnostics/monitor-alerts-unified-usage.md), můžete vybrat zónu DNS jako prostředek, zvolte typ metriky signál a nakonfigurovat výstrahy logiku a další parametry, jako třeba **období**a **frekvence**. Dále můžete definovat [akce skupiny](../monitoring-and-diagnostics/monitoring-action-groups.md) pro když je splněna podmínka výstrahy, jímž výstrahy budou doručeny prostřednictvím vybrané akce. Další informace o tom, jak konfigurovat výstrahy pro monitorování Azure metriky najdete v tématu [vytvořit, zobrazit a spravovat výstrah pomocí Azure monitorování](../monitoring-and-diagnostics/monitor-alerts-unified-usage.md). 

## <a name="next-steps"></a>Další postup
- Další informace o [Azure DNS](dns-overview.md).
