---
title: Monitorování virtuální sítě WAN pomocí Azure Monitor Insights
description: V tomto článku se dozvíte o monitorování Azure Virtual WAN pomocí Azure Monitor Insights.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: cherylmc
ms.openlocfilehash: 052fd0ea7619d566e78806580ee7b39e49cc85d2
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/29/2020
ms.locfileid: "91448619"
---
# <a name="azure-monitor-insights-for-virtual-wan-preview"></a>Azure Monitor Insights pro virtuální síť WAN (Preview)

[Azure monitor Insights](../azure-monitor/insights/network-insights-overview.md) pro Azure Virtual WAN poskytuje uživatelům a operátorům možnost Zobrazit stav a stav virtuální sítě WAN, který je k dispozici prostřednictvím automatického zjišťování topologická mapa. Překrytí stavu prostředku a stavu na mapě vám umožní zobrazit snímek celkového stavu virtuální sítě WAN. Prostředky na mapě můžete procházet pomocí přístupu jedním kliknutím na stránky Konfigurace prostředků na portálu Virtual WAN.

Metriky na úrovni prostředků virtuální sítě WAN se shromažďují a prezentují prostřednictvím předem zabaleného sešitu metrik virtuální sítě WAN. Sešit zobrazuje metriky na úrovních sítě WAN, hub, Gateway a Connection. Tento článek vás provede kroky k použití služby Azure Monitor Insights pro virtuální síť WAN k zobrazení vaší virtuální sítě WAN a metriky na jednom místě.

> [!NOTE]
> V tuto chvíli se zavádí možnost nabídky **Insights** na virtuálním portálu sítě WAN. Při zavádění této nabídky můžete získat přístup k sešitu s topologií a metrikami virtuální sítě WAN pomocí Azure Monitor pro sítě. Další informace najdete v tématu [Azure monitor pro sítě](../azure-monitor/insights/network-insights-overview.md). 
>

## <a name="before-you-begin"></a>Než začnete

K dokončení kroků v tomto článku potřebujete virtuální síť WAN s jedním nebo více rozbočovači. Pokud chcete vytvořit virtuální síť WAN a centrum, postupujte podle kroků v těchto článcích:

* [Vytvoření virtuální sítě WAN](virtual-wan-site-to-site-portal.md#openvwan)
* [Vytvoření rozbočovače](virtual-wan-site-to-site-portal.md#hub)

## <a name="view-vwan-topology"></a><a name="topology"></a>Zobrazit topologii VWAN

Přejít na **Azure Portal**  >  **virtuální síť WAN**. V nabídce **monitorování** v levém podokně vyberte **přehledy (Preview)**. Zobrazí se zobrazení **přehledy** . Zobrazuje se mapa závislostí virtuální sítě WAN a minimální sešit s **metrikami** vysoké úrovně.

**Obrázek 1: nabídka monitorování >ch přehledů**

:::image type="content" source="./media/azure-monitor-insights/monitor-menu.png" alt-text="Snímek obrazovky zobrazující zobrazení přehledy (Preview)" lightbox="./media/azure-monitor-insights/monitor-menu.png":::

V zobrazení **přehledů** můžete zobrazit informace o prostředcích virtuálních sítí WAN. Mezi tyto prostředky patří centra, brány, brány firewall, připojení a virtuální sítě typu paprsek, síťová virtuální zařízení třetích stran a větve v komplexní virtuální síti WAN. Příklad najdete na **obrázku 2**.

Stav a stav prostředku jsou barevně odlišené a překrývají se na ikonách prostředků na mapě. Na pravé straně okna se zobrazí na pravé straně sítě WAN metriky vysoké úrovně, jako jsou kapacity centra a využití brány, a to v minim sešitu.

**Obrázek 2: zobrazení Insights**

:::image type="content" source="./media/azure-monitor-insights/insights-view.png" alt-text="Snímek obrazovky zobrazující zobrazení přehledy (Preview)" lightbox="./media/azure-monitor-insights/insights-view.png":::

## <a name="dependency-view"></a><a name="dependency"></a>Zobrazení závislostí

Zobrazení **závislostí** pro virtuální síť WAN vám pomůže vizualizovat propojené zobrazení všech prostředků virtuální sítě WAN, které jsou v podstatě uspořádané do architektury centra a paprsků.

**Obrázek 3: zobrazení závislosti VWAN**

:::image type="content" source="./media/azure-monitor-insights/dependency-map.png" alt-text="Snímek obrazovky zobrazující zobrazení přehledy (Preview)" lightbox="./media/azure-monitor-insights/dependency-map.png":::

Mapa zobrazení **závislostí** zobrazuje následující prostředky jako připojený graf:

* Virtuální rozbočovače WAN napříč různými oblastmi Azure.
* Paprskové virtuální sítě, které jsou přímo připojené k rozbočovači.
* Sítě VPN a Azure ExpressRoute a P2S uživatelé připojení ke každému centru prostřednictvím příslušných připojení ExpressRoute, S2S a P2S a bran virtuální sítě.
* Brány firewall Azure (včetně proxy serverů brány firewall třetích stran) nasazené v centru (zabezpečené centrum).
* Síťová virtuální zařízení třetích stran (síťová virtuální zařízení), která jsou nasazená ve virtuálních sítích s paprsky.

Mapa závislostí také zobrazuje nepřímo připojené virtuální sítě (virtuální sítě, které jsou v partnerském vztahu virtuálních sítí WAN).

Mapa závislostí umožňuje snadnou navigaci na nastavení konfigurace každého prostředku. Můžete například ukazatel myši na prostředek centra zobrazit tak, aby se zobrazila základní konfigurace prostředků, jako je například oblast centra a předpona centra. Kliknutím pravým tlačítkem myši přejděte na stránku Azure Portal prostředku rozbočovače.

**Obrázek 4: přechod k informacím specifickým pro prostředky**

:::image type="content" source="./media/azure-monitor-insights/resource-information.png" alt-text="Snímek obrazovky zobrazující zobrazení přehledy (Preview)":::

Panel hledání a filtrování v zobrazení **závislostí** poskytuje snadný způsob, jak hledat v grafu. Různé filtry poskytují podporu k zúžení vyhledávání na určitou cestu a stav.

**Obrázek 5: hledání a filtrování**

:::image type="content" source="./media/azure-monitor-insights/search-filter.png" alt-text="Snímek obrazovky zobrazující zobrazení přehledy (Preview)" lightbox="./media/azure-monitor-insights/search-filter.png":::

## <a name="detailed-metrics"></a><a name="detailed"></a>Podrobné metriky

Můžete vybrat **Zobrazit podrobné metriky** a získat přístup k podrobnostem stránky **metriky** . Stránka **metrika** je řídicí panel, který je předem nakonfigurovaný se samostatnými kartami. Tyto karty poskytují přehled o kapacitě, výkonu a využití prostředků virtuální sítě WAN na úrovni služby Virtual-WAN a na úrovni centra a na úrovni jednotlivých připojení.

**Obrázek 6: podrobný řídicí panel metrik**

:::image type="content" source="./media/azure-monitor-insights/detailed-metrics.png" alt-text="Snímek obrazovky zobrazující zobrazení přehledy (Preview)" lightbox="./media/azure-monitor-insights/detailed-metrics.png":::

## <a name="next-steps"></a>Další kroky

* Další informace najdete v tématu [metriky v Azure monitor](../azure-monitor/platform/data-platform-metrics.md).
* Úplný popis všech metrik služby Virtual WAN najdete v tématu [virtuální sítě WAN a metriky](logs-metrics.md).
