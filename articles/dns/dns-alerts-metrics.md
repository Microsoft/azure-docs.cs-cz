---
title: Metriky a výstrahy – Azure DNS
description: S tímto studijním programem můžete začít s metrikami a výstrahami Azure DNS.
services: dns
documentationcenter: na
author: rohinkoul
manager: kumudD
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/17/2018
ms.author: rohink
ms.openlocfilehash: 42acbc0d32b3ce5de4befcf112b68f611ad70542
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76937456"
---
# <a name="azure-dns-metrics-and-alerts"></a>Metriky a výstrahy Azure DNS
Azure DNS je hostingová služba pro domény DNS, která poskytuje překlad názvů pomocí infrastruktury Microsoft Azure. Tento článek popisuje metriky a výstrahy pro službu Azure DNS.

## <a name="azure-dns-metrics"></a>Metriky Azure DNS

Azure DNS poskytuje metriky pro zákazníky, které jim umožní sledovat konkrétní aspekty jejich zón DNS hostovaných ve službě. Kromě toho s metrikami Azure DNS můžete konfigurovat a přijímat výstrahy na základě podmínek zájmu. Metriky jsou k dispozici prostřednictvím [služby Azure Monitor](../azure-monitor/index.yml). Azure DNS poskytuje následující metriky prostřednictvím Azure Monitor pro vaše zóny DNS:

-   QueryVolume
-   RecordSetCount
-   Využití kapacity recordSetCapacity

Definici těchto [metrik](../azure-monitor/platform/metrics-supported.md#microsoftnetworkdnszones) můžete také zobrazit na stránce dokumentace Azure Monitoru.
>[!NOTE]
> V tuto chvíli jsou tyto metriky dostupné jenom pro veřejné zóny DNS hostované ve službě Azure DNS. Pokud máte soukromé zóny hostované v Azure DNS, tyto metriky nebudou poskytovat data pro tyto zóny. Kromě toho metriky a upozornění funkce je podporována jenom v Azure Public cloudu. Podpora suverénních mraků bude následovat později. 

Nejpodrobnější prvek, pro který můžete zobrazit metriky, je zóna DNS. V současné době nelze zobrazit metriky pro jednotlivé záznamy o prostředcích v rámci zóny.

### <a name="query-volume"></a>Svazek dotazu

Metrika *svazku dotazů* ve službě Azure DNS zobrazuje objem dotazů DNS (provoz dotazů), který přijímá Azure DNS pro vaši zónu DNS. Měrná jednotka je Count a agregace je součet všech dotazů přijatých za určité časové období. 

Chcete-li tuto metriku zobrazit, vyberte možnost Metriky (náhled) prostředí průzkumníka na kartě Monitorování na webu Azure Portal. V rozevíracím okně Zdroj vyberte zónu DNS, vyberte metriku Objem dotazu a jako agregaci vyberte Součet. Níže snímek obrazovky ukazuje příklad.  Další informace o prostředí průzkumníka metrik a vytváření grafů najdete v tématu [Průzkumník metrik monitorování Azure](../azure-monitor/platform/metrics-charts.md).

![Svazek dotazu](./media/dns-alerts-metrics/dns-metrics-query-volume.png)

*Obrázek: Metriky svazku dotazů Azure DNS*

### <a name="record-set-count"></a>Počet sad záznamů
Metrika *Počet sad záznamů* zobrazuje počet sad záznamů ve službě Azure DNS pro zónu DNS. Počítají se všechny sady záznamů definované ve vaší zóně. Měrná jednotka je Počet a agregace je maximum všech sad záznamů. Chcete-li tuto metriku zobrazit, vyberte možnost **Metriky (náhled)** prostředí průzkumníka na kartě **Monitorování** na webu Azure Portal. V rozevíracím přehledu **Zdroj** vyberte zónu DNS, vyberte metriku **Počet sad záznamů** a pak jako **agregaci**vyberte **Možnost Max** . Další informace o prostředí průzkumníka metrik a vytváření grafů najdete v tématu [Průzkumník metrik monitorování Azure](../azure-monitor/platform/metrics-charts.md). 

![Počet sad záznamů](./media/dns-alerts-metrics/dns-metrics-record-set-count.png)

*Obrázek: Metriky počtu sad záznamů Dns Azure*


### <a name="record-set-capacity-utilization"></a>Využití kapacity sady záznamů
Metrika *Využití kapacity sady záznamů* ve službě Azure DNS zobrazuje procento využití kapacity sady záznamů pro zónu DNS. Na každou zónu DNS ve službě Azure DNS se vztahuje limit sady záznamů, který definuje maximální počet sad záznamů, které jsou pro zónu povoleny (viz [limity DNS](dns-zones-records.md#limits)). Proto tato metrika ukazuje, jak blízko jste k dosažení limitu sady záznamů. Pokud máte například 500 sad záznamů nakonfigurovaných pro zónu DNS a zóna má výchozí limit sady záznamů 5000, metrika RecordSetCapacityUtilization zobrazí hodnotu 10 % (která je získána vydělením 500 5000). Měrná jednotka je **Procento** a typ **agregace** je **Maximum**. Chcete-li tuto metriku zobrazit, vyberte možnost Metriky (náhled) prostředí průzkumníka na kartě Monitorování na webu Azure Portal. V rozevíracím seznamu Zdroj vyberte zónu DNS, vyberte metriku Využití kapacity sady záznamů a jako agregaci vyberte Max. Níže snímek obrazovky ukazuje příklad. Další informace o prostředí průzkumníka metrik a vytváření grafů najdete v tématu [Průzkumník metrik monitorování Azure](../azure-monitor/platform/metrics-charts.md). 

![Počet sad záznamů](./media/dns-alerts-metrics/dns-metrics-record-set-capacity-uitlization.png)

*Obrázek: Metriky využití kapacity sady záznamů Azure DNS*

## <a name="alerts-in-azure-dns"></a>Výstrahy ve službě Azure DNS
Azure Monitor poskytuje možnost výstrahy proti dostupným hodnotám metrik. Metriky DNS jsou k dispozici v novém prostředí konfigurace výstrah. Jak je podrobně popsáno v [dokumentaci výstrah Azure Monitor](../monitoring-and-diagnostics/monitor-alerts-unified-usage.md), můžete vybrat zónu DNS jako prostředek, zvolit typ signálu Metrika a nakonfigurovat logiku výstrah a další parametry, jako je **období** a **frekvence**. Dále můžete definovat [skupinu akcí](../azure-monitor/platform/action-groups.md) pro splnění podmínky výstrahy, kdy bude výstraha doručena prostřednictvím vybraných akcí. Další informace o konfiguraci výstrah pro metriky Azure Monitoru najdete v [tématu Vytváření, zobrazení a správa výstrah pomocí Azure Monitoru](../monitoring-and-diagnostics/monitor-alerts-unified-usage.md). 

## <a name="next-steps"></a>Další kroky
- Další informace o [Azure DNS](dns-overview.md).
