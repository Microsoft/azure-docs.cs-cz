---
title: Monitorování Azure Virtual WAN pomocí Azure Monitor Insights
description: Další informace o monitorování virtuální sítě WAN pomocí Azure Monitor Insights
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 07/22/2020
ms.author: cherylmc
ms.openlocfilehash: 8553a809173d955a21e6730de35c70de5b69e81b
ms.sourcegitcommit: 0e8a4671aa3f5a9a54231fea48bcfb432a1e528c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/24/2020
ms.locfileid: "87136148"
---
# <a name="azure-monitor-insights-for-virtual-wan-preview"></a>Azure Monitor Insights pro virtuální síť WAN (Preview)

[Azure monitor Insights](../azure-monitor/insights/network-insights-overview.md) pro virtuální síť WAN poskytuje uživatelům a operátorům možnost Zobrazit stav a stav virtuální sítě WAN, který je k dispozici prostřednictvím automatického zjišťování topologická mapa. Stav a stav prostředku jsou na mapě překryty, aby vám poskytovala snímek celkového stavu virtuální sítě WAN. Navigace na prostředku se na mapě povoluje přes přístup pomocí jediného kliknutí na stránky Konfigurace prostředků na portálu Virtual WAN.

Metriky na úrovni prostředků virtuální sítě WAN se shromažďují a prezentují prostřednictvím předem zabaleného sešitu metriky virtuální sítě WAN, který zobrazuje metriky na úrovni virtuální sítě WAN, centra, brány a připojení. Tento článek vás provede kroky k použití služby Azure Monitor Insights pro virtuální síť WAN k zobrazení vaší virtuální sítě WAN a metriky na jednom místě.

> [!NOTE]
> V rámci této verze Preview se probíhá zavádění nabídky přehledů.
>

## <a name="before-you-begin"></a>Než začnete

V krocích v tomto článku se předpokládá, že jste už nasadili virtuální síť WAN s jedním nebo více rozbočovači. Pokud chcete vytvořit novou virtuální síť WAN a nové centrum, použijte postup v následujících článcích:

* [Vytvoření virtuální sítě WAN](virtual-wan-site-to-site-portal.md#openvwan)
* [Vytvoření rozbočovače](virtual-wan-site-to-site-portal.md#hub)

## <a name="view-vwan-topology"></a><a name="topology"></a>Zobrazit topologii VWAN

V **Azure Portal->virtuální síti WAN**vyberte v nabídce **monitorování** na levé straně **přehledy (Preview)**. Tím zobrazíte **zobrazení přehledů**, které zobrazuje mapu závislostí virtuální sítě WAN a minimální sešit metrik nejvyšší úrovně.

**Obrázek 1: nabídka monitorování – přehled**

:::image type="content" source="./media/azure-monitor-insights/monitor-menu.png" alt-text="obrázek" lightbox="./media/azure-monitor-insights/monitor-menu.png":::

V zobrazení **přehledů** můžete zobrazit místně zjištěné virtuální sítě WAN, jako jsou například centra, brány, brány firewall, připojení a Paprskové virtuální sítě, třetí strana síťová virtuální zařízení a větve v komplexní virtuální síti WAN, jak je znázorněno na **obrázku 2**.

Stav a **stav** **prostředku** jsou barevně odlišené a překrývají se na ikonách prostředků na mapě. Metriky vysoké úrovně virtuální sítě WAN, jako jsou kapacity centra a využití brány, se na pravé straně zobrazí na pravém listu.

**Obrázek 2: zobrazení Insights**

:::image type="content" source="./media/azure-monitor-insights/insights-view.png" alt-text="obrázek" lightbox="./media/azure-monitor-insights/insights-view.png":::

## <a name="dependency-view"></a><a name="dependency"></a>Zobrazení závislostí

Zobrazení **závislostí** pro virtuální síť WAN usnadňuje vizualizaci propojeného zobrazení všech prostředků virtuální sítě WAN v podstatě uspořádaných do architektury hub-and-paprsek.

**Obrázek 3: zobrazení závislosti VWAN**

:::image type="content" source="./media/azure-monitor-insights/dependency-map.png" alt-text="Mapa závislostí" lightbox="./media/azure-monitor-insights/dependency-map.png":::

Mapa zobrazení závislostí zobrazuje následující prostředky jako připojený graf:

* Virtuální rozbočovače WAN napříč různými oblastmi Azure.
* Paprskový virtuální sítě, které jsou přímo připojené k rozbočovači.
* Lokality sítě VPN a ExpressRoute a uživatelé P2S, kteří jsou připojení ke každému centru prostřednictvím příslušných ExpressRoute, připojení S2S a P2S, a bran virtuální sítě.
* Brány firewall Azure (včetně proxy serverů brány firewall třetích stran) nasazené v centru (zabezpečené centrum).
* SÍŤOVÉ virtuální ZAŘÍZENÍy třetích stran (síťová virtuální zařízení), které jsou nasazené v paprskovém virtuální sítě.

Mapa závislostí také zobrazuje nepřímo připojenou virtuální sítě (virtuální síť, která má partnerský vztah s virtuální sítí WAN virtuální sítě).

Mapa závislostí umožňuje snadnou navigaci na nastavení konfigurace každého prostředku. Můžete například ukazatel myši na prostředek centra zobrazit tak, aby se zobrazila základní konfigurace prostředků, jako je například oblast centra, a předpona centra. Kliknutím pravým tlačítkem myši přejděte na stránku Azure Portal prostředku rozbočovače.

**Obrázek 4: přechod k informacím specifickým pro prostředky**

:::image type="content" source="./media/azure-monitor-insights/resource-information.png" alt-text="informace o prostředku":::

Panel hledání a filtrování v zobrazení závislostí nabízí snadnou možnost vyhledávání v grafu. Různé filtry poskytují podporu k zúžení vyhledávání na určitou cestu a stav.

**Obrázek 5: hledání a filtrování**

:::image type="content" source="./media/azure-monitor-insights/search-filter.png" alt-text="panel hledání a filtrování" lightbox="./media/azure-monitor-insights/search-filter.png":::

## <a name="detailed-metrics"></a><a name="detailed"></a>Podrobné metriky

Můžete vybrat **Zobrazit podrobné metriky** a získat přístup k podrobnostem stránky **metriky** . Stránka metriky je řídicí panel, který je předem nakonfigurovaný s oddělenými kartami a poskytuje užitečné poznatky o kapacitě, výkonu a využití prostředků virtuální sítě WAN na virtuální úrovni sítě WAN, úrovni centra a jednotlivých připojeních.

**Obrázek 6: podrobný řídicí panel metrik**

:::image type="content" source="./media/azure-monitor-insights/detailed-metrics.png" alt-text="podrobné metriky" lightbox="./media/azure-monitor-insights/detailed-metrics.png":::

## <a name="next-steps"></a>Další kroky

* Další informace o metrikách v Azure Monitor najdete v tématu [metriky v Azure monitor](../azure-monitor/platform/data-platform-metrics.md).
* Úplný popis všech metrik služby Virtual WAN najdete v tématu [virtuální sítě WAN a metriky](logs-metrics.md).
