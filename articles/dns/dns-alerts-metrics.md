---
title: Azure DNS metriky a upozornění | Dokumentace Microsoftu
description: Další informace o výstrahách a metrikách Azure DNS.
services: dns
documentationcenter: na
author: vhorne
manager: jennoc
editor: ''
ms.assetid: ''
ms.service: dns
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/17/2018
ms.author: victorh
ms.openlocfilehash: 0f50ecb086b45cffb4ff27135a26f962c92926bf
ms.sourcegitcommit: c2e61b62f218830dd9076d9abc1bbcb42180b3a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/15/2018
ms.locfileid: "53434457"
---
# <a name="azure-dns-metrics-and-alerts"></a>Azure DNS metrik a výstrah
Azure DNS je hostitelská služba určená pro domény DNS, která nabízí řešení názvů pomocí infrastruktury Microsoft Azure. Tento článek popisuje metrik a výstrah pro službu Azure DNS.

## <a name="azure-dns-metrics"></a>Metriky Azure DNS

Azure DNS poskytuje metriky pro zákazníky, aby se mohly sledovat konkrétní aspekty jejich ty, které jsou hostované ve službě DNS. Kromě toho s metrikami Azure DNS, můžete nakonfigurovat a dostávat upozornění na základě podmínek, které vás zajímají. Metriky jsou k dispozici prostřednictvím [služby Azure Monitor](../azure-monitor/index.yml). Azure DNS poskytuje následující metriky prostřednictvím služby Azure Monitor pro vaše zóny DNS:

-   QueryVolume
-   RecordSetCount
-   RecordSetCapacityUtilization

Můžete zobrazit také [definice tyto metriky](../azure-monitor/platform/metrics-supported.md#microsoftnetworkdnszones) na stránce dokumentace ke službě Azure Monitor.
>[!NOTE]
> V tuto chvíli tyto metriky jsou k dispozici pouze pro veřejné služby DNS zóny hostované v Azure DNS. Pokud máte hostované v Azure DNS Private Zones tyto metriky neposkytne data pro tyto zóny. Kromě toho metrik a výstrah funkce je podporován pouze v Azure veřejného cloudu. Podpora pro suverénní cloudy bude následovat později. 

Velmi podrobné úrovni dimenze pro tyto metriky se zóna DNS.

### <a name="query-volume"></a>Množství dotazů

*Množství dotazů* metriky ve službě Azure DNS ukazuje objem dotazy DNS (dotaz provoz), které se získaly Azure DNS pro zónu DNS. Měrné jednotky je počet a agregace je součet všech dotazů přijatých za časové období. Chcete-li zobrazit tuto metriku, na kartě monitorování na webu Azure Portal vyberte prostředí Průzkumníka metrik (preview). Z rozevíracího seznamu prostředků vyberte zónu DNS, vybrat metriku množství dotazů a vyberte součet jako agregace. Následující snímek obrazovky ukazuje příklad.  Další informace o Průzkumníku metrik prostředí a vytváření grafů, najdete v tématu [Průzkumník metrik Azure monitoru](../azure-monitor/platform/metrics-charts.md).

![Množství dotazů](./media/dns-alerts-metrics/dns-metrics-query-volume.png)

*Obrázek: Metriky Azure množství dotazů DNS*

### <a name="record-set-count"></a>Počet sady záznamů
*Nastavit počet záznamů* metrika zobrazuje počet sad záznamů pro zónu DNS v Azure DNS. Všechno, co se ve vaší zóně definované sadách záznamů se počítají. Měrné jednotky je počet a agregace je maximální počet sady záznamů. Chcete-li zobrazit tuto metriku, vyberte **metriky (preview)** prostředí Průzkumníka **monitorování** karta na portálu Azure portal. Vyberte zónu DNS z **prostředků** rozevíracího seznamu, vyberte **nastavit počet záznamů** metriky a pak vyberte **maximální** jako **agregace** . Další informace o Průzkumníku metrik prostředí a vytváření grafů, najdete v tématu [Průzkumník metrik Azure monitoru](../azure-monitor/platform/metrics-charts.md). 

![Počet sady záznamů](./media/dns-alerts-metrics/dns-metrics-record-set-count.png)

*Obrázek: Metriky Azure nastavit počet záznamů DNS*


### <a name="record-set-capacity-utilization"></a>Sada záznamů využití kapacity
*Využití kapacity nastavte záznam* metriky ve službě Azure DNS znázorňují příslušná procenta využití kapacity záznamů pro zónu DNS. Každé zóny DNS v Azure DNS se vztahují omezení sady záznamů, který definuje maximální počet sad záznamů, které jsou povoleny pro zóny (viz [limitů DNS](dns-zones-records.md#limits)). Proto tato metrika se dozvíte, jak blízko se v dosažení limitu záznamů. Například pokud máte 500 záznamů, které jsou nakonfigurované pro vaši zónu DNS a zóny má výchozí limit záznamů 5000, Metrika RecordSetCapacityUtilization zobrazí hodnotu 10 % (která se získá dělicí 500 podle 5000). Měrné jednotky je **procento** a **agregace** typ je **maximální**. Chcete-li zobrazit tuto metriku, na kartě monitorování na webu Azure Portal vyberte prostředí Průzkumníka metrik (preview). Z rozevíracího seznamu prostředků vyberte zónu DNS, vybrat metriky využití kapacity nastavte záznam a vyberte maximální počet jako agregace. Následující snímek obrazovky ukazuje příklad. Další informace o Průzkumníku metrik prostředí a vytváření grafů, najdete v tématu [Průzkumník metrik Azure monitoru](../azure-monitor/platform/metrics-charts.md). 

![Počet sady záznamů](./media/dns-alerts-metrics/dns-metrics-record-set-capacity-uitlization.png)

*Obrázek: Metriky využití kapacity nastavte záznam DNS Azure*

## <a name="alerts-in-azure-dns"></a>Upozornění v Azure DNS
Azure Monitor poskytuje možnost oznámení proti dostupné hodnoty metrik. Metriky DNS jsou k dispozici v nové prostředí konfigurace výstrah. Jak je popsáno v části [upozornění služby Azure Monitor dokumentaci](../monitoring-and-diagnostics/monitor-alerts-unified-usage.md), vyberte zónu DNS jako prostředek, zvolte typ metriky signálu a nakonfigurovat logika upozornění a další parametry, jako jsou například **období**a **frekvence**. Dále můžete definovat [skupiny akcí](../azure-monitor/platform/action-groups.md) pro při výstrahy, je splněna podmínka, zajišťovaný výstraha bude doručena prostřednictvím vybrané akce. Další informace o konfiguraci výstrah pro monitorování Azure metrik najdete v části [vytvoření, zobrazení a Správa výstrah pomocí Azure monitoru](../monitoring-and-diagnostics/monitor-alerts-unified-usage.md). 

## <a name="next-steps"></a>Další postup
- Další informace o [Azure DNS](dns-overview.md).
