---
title: Metriky a výstrahy – Azure DNS
description: Pomocí této cesty výukového programu začněte pracovat s Azure DNS metrikami a výstrahami.
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
ms.openlocfilehash: 391109727877544a4e94bae376ecef4d33a13cfb
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "100575272"
---
# <a name="azure-dns-metrics-and-alerts"></a>Azure DNS metriky a výstrahy
Azure DNS je hostingová služba pro domény DNS, která poskytuje překlad adres IP pomocí infrastruktury Microsoft Azure. Tento článek popisuje metriky a výstrahy pro službu Azure DNS.

## <a name="azure-dns-metrics"></a>Azure DNS metriky

Azure DNS poskytuje zákazníkům metriky, které jim umožní monitorovat konkrétní aspekty svých zón DNS, které jsou hostovány ve službě. Kromě toho s Azure DNS metrikami můžete nakonfigurovat a přijímat výstrahy na základě podmínek zájmu. Metriky jsou k dispozici prostřednictvím [služby Azure monitor](../azure-monitor/index.yml). Azure DNS poskytuje následující metriky prostřednictvím Azure Monitor pro zóny DNS:

-   QueryVolume
-   RecordSetCount
-   RecordSetCapacityUtilization

Na stránce dokumentace Azure Monitor můžete také zobrazit [definici těchto metrik](../azure-monitor/essentials/metrics-supported.md#microsoftnetworkdnszones) .
>[!NOTE]
> V tuto chvíli jsou tyto metriky dostupné jenom pro veřejné zóny DNS hostované v Azure DNS. Pokud máte v Azure DNS hostované soukromé zóny, tyto metriky nebudou poskytovat data pro tyto zóny. Funkce metrik a upozorňování se navíc podporují jenom ve veřejném cloudu Azure. Podpora pro cloudy svrchovaného provozu bude následovat později. 

Nejpodrobnějiný prvek, pro který vidíte metriky, je zóna DNS. V tuto chvíli nemůžete vidět metriky pro jednotlivé záznamy prostředků v rámci zóny.

### <a name="query-volume"></a>Svazek dotazu

Metrika *svazku dotazu* v Azure DNS zobrazuje objem dotazů DNS (přenos dotazů), které jsou přijímány Azure DNS pro vaši zónu DNS. Měrnou jednotkou je počet a agregace je celková hodnota všech dotazů přijatých v časovém intervalu. 

Tuto metriku zobrazíte tak, že na kartě monitorování v Azure Portal vyberete možnosti Průzkumník metrik (Preview). Vyberte zónu DNS z rozevíracího seznamu prostředek, vyberte metriku svazku dotazu a jako agregaci Vyberte Sum. Pod ukázkou se zobrazuje příklad.  Další informace o prostředí Průzkumník metrik a vytváření grafů najdete v tématu [Azure Monitor Průzkumník metrik](../azure-monitor/essentials/metrics-charts.md).

![Svazek dotazu](./media/dns-alerts-metrics/dns-metrics-query-volume.png)

*Obrázek: Azure DNS dotazování na metriky svazků*

### <a name="record-set-count"></a>Počet sad záznamů
Metrika *počtu sad záznamů* zobrazuje počet sad záznamů v Azure DNS pro vaši zónu DNS. Započítávají se všechny sady záznamů definované ve vaší zóně. Jednotkou měření je počet a agregace je maximální hodnota všech sad záznamů. Tuto metriku zobrazíte tak, že na kartě **monitorování** v Azure Portal vyberete možnosti Průzkumník **metrik (Preview)** . Vyberte zónu DNS z rozevíracího seznamu **prostředek** , vyberte metriku **počtu sad záznamů** a pak jako **agregaci** vyberte **Maximum** . Další informace o prostředí Průzkumník metrik a vytváření grafů najdete v tématu [Azure Monitor Průzkumník metrik](../azure-monitor/essentials/metrics-charts.md). 

![Počet sad záznamů](./media/dns-alerts-metrics/dns-metrics-record-set-count.png)

*Obrázek: Azure DNS metriky počtu sad záznamů*


### <a name="record-set-capacity-utilization"></a>Využití kapacity sady záznamů
Metrika *využití kapacity sady záznamů* v Azure DNS zobrazuje procento využití kapacity vaší sady záznamů v zóně DNS. Každá zóna DNS v Azure DNS podléhá omezení sady záznamů, které definuje maximální počet sad záznamů, které jsou povoleny pro zónu (viz [omezení služby DNS](dns-zones-records.md#limits)). Proto tato metrika ukazuje, jak blízko vás limit sady záznamů zasáhne. Pokud máte například pro vaši zónu DNS nakonfigurované sady záznamů 500 a tato zóna má výchozí limit sady záznamů 5000, metrika RecordSetCapacityUtilization zobrazí hodnotu 10% (která se získá vydělením 500 hodnotou 5000). Měrnou jednotkou je **procento** a typ **agregace** je **maximální**. Tuto metriku zobrazíte tak, že na kartě monitorování v Azure Portal vyberete možnosti Průzkumník metrik (Preview). Vyberte zónu DNS z rozevíracího seznamu prostředek, vyberte metriku využití kapacity sady záznamů a jako agregaci vyberte max. Pod ukázkou se zobrazuje příklad. Další informace o prostředí Průzkumník metrik a vytváření grafů najdete v tématu [Azure Monitor Průzkumník metrik](../azure-monitor/essentials/metrics-charts.md). 

![Snímek obrazovky, který whows příklad zobrazení metrik.](./media/dns-alerts-metrics/dns-metrics-record-set-capacity-uitlization.png)

*Obrázek: metriky využití kapacity sady záznamů Azure DNS*

## <a name="alerts-in-azure-dns"></a>Výstrahy v Azure DNS
Azure Monitor poskytuje možnost upozorňování na dostupné hodnoty metrik. Metriky DNS jsou k dispozici v novém prostředí pro konfiguraci výstrah. Jak je podrobně popsáno v [dokumentaci k výstrahám Azure monitor](../azure-monitor/alerts/alerts-metric.md), můžete jako prostředek vybrat zónu DNS, zvolit typ signálu metriky a nakonfigurovat logiku výstrah a další parametry, jako je například **tečka** a **frekvence**. Můžete dále definovat [skupinu akcí](../azure-monitor/alerts/action-groups.md) pro situaci, kdy je splněna podmínka výstrahy, a na základě kterých bude výstraha doručena prostřednictvím zvolených akcí. Další informace o tom, jak nakonfigurovat upozorňování pro Azure Monitor metriky, najdete v tématu [Vytvoření, zobrazení a správa výstrah pomocí Azure monitor](../azure-monitor/alerts/alerts-metric.md). 

## <a name="next-steps"></a>Další kroky
- Přečtěte si další informace o [Azure DNS](dns-overview.md).