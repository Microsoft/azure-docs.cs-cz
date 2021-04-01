---
title: Monitorování protokolů pomocí Azure Firewallho sešitu
description: Azure Firewall sešity poskytují flexibilní plátno pro Azure Firewall analýzu dat a vytváření bohatých vizuálních sestav v rámci Azure Portal.
services: firewall
author: gopimsft
ms.service: firewall
ms.topic: how-to
ms.date: 09/22/2020
ms.author: victorh
ms.openlocfilehash: e63336b0e84d303b51eda56f90ca6fb453d8ae0d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "99831837"
---
# <a name="monitor-logs-using-azure-firewall-workbook"></a>Monitorování protokolů pomocí Azure Firewallho sešitu

Azure Firewall sešit nabízí flexibilní plátno pro Azure Firewall analýzu dat. Můžete ho použít k vytvoření bohatých vizuálních sestav v rámci Azure Portal. Můžete klepnout na několik bran firewall nasazených v rámci Azure a kombinovat je do sjednocených interaktivních prostředí.

Můžete získat přehled o Azure Firewallch událostech, informace o vašich aplikacích a síťových pravidlech a zobrazit statistiku pro aktivity brány firewall napříč adresami URL, porty a adresami. Azure Firewall sešitu vám umožní filtrovat brány firewall a skupiny prostředků a dynamicky filtrovat jednotlivé kategorie pomocí snadno čitelných datových sad při zkoumání problému v protokolech. 

## <a name="prerequisites"></a>Požadavky

Než začnete, měli byste [Povolit protokolování diagnostiky](firewall-diagnostics.md#enable-diagnostic-logging-through-the-azure-portal) prostřednictvím Azure Portal. Také si přečtěte [Azure firewall protokoly a metriky](logs-and-metrics.md) pro přehled diagnostických protokolů a metrik, které jsou k dispozici pro Azure firewall.

## <a name="get-started"></a>Začínáme

Chcete-li nasadit sešit, vyhledejte Azure Firewall a postupujte podle pokynů na stránce [Azure monitor sešitu](https://github.com/Azure/Azure-Network-Security/tree/master/Azure%20Firewall/Workbook%20-%20Azure%20Firewall%20Monitor%20Workbook) . Azure Firewall sešit je navržený tak, aby fungoval napříč více klienty, vícenásobnými předplatnými a bylo možné je filtrovat na více bran firewall.

## <a name="overview-page"></a>Stránka přehledu

Stránka Přehled poskytuje způsob, jak filtrovat napříč pracovními prostory, časem a branami firewall. Zobrazuje události podle času napříč branami firewall a typy protokolů (aplikace, sítě, hrozba Intel, proxy server DNS).

:::image type="content" source="./media/firewall-workbook/firewall-workbook-overview.png" alt-text="Přehled Azure Firewall sešitu":::

## <a name="application-rule-log-statistics"></a>Statistika protokolu pravidel aplikace

Tato stránka zobrazuje jedinečné zdroje IP adres v čase, využití počtu pravidel aplikací, odepření/povolení plně kvalifikovaného názvu domény v čase a filtrovaná data. Data můžete filtrovat na základě IP adresy.

:::image type="content" source="./media/firewall-workbook/firewall-workbook-application-rule.png" alt-text="Protokol pravidel aplikace Azure Firewall sešitu":::

## <a name="network-rule-log-statistics"></a>Statistika protokolu síťového pravidla

Tato stránka poskytuje akce zobrazení podle pravidla – povolit/odepřít, cílový port podle IP adresy a DNAT v čase. Můžete také filtrovat podle akce, portu a typu cíle.

:::image type="content" source="./media/firewall-workbook/firewall-workbook-network-rule.png" alt-text="Protokol síťového pravidla Azure Firewall sešitu":::

Můžete také filtrovat protokoly na základě časového okna:

:::image type="content" source="./media/firewall-workbook/firewall-workbook-network-rule-time.png" alt-text="Časový interval protokolu Azure Firewallho síťového pravidla v sešitu":::

## <a name="investigations"></a>Zkoušen

Můžete se podívat na protokoly a dozvědět se víc o prostředku na základě zdrojové IP adresy. Můžete získat informace, jako je název virtuálního počítače a název síťového rozhraní. Filtrování prostředků z protokolů je jednoduché.

:::image type="content" source="./media/firewall-workbook/firewall-workbook-investigation.png" alt-text="Azure Firewall šetření sešitu":::

## <a name="next-steps"></a>Další kroky

- Další informace o [diagnostice Azure firewall](firewall-diagnostics.md)
