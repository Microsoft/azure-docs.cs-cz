---
title: O službě Azure VPN Gateway
description: Přečtěte si, co je VPN Gateway a jak používat VPN Gateway pro připojení k virtuálním sítím VPN typu Site-to-site, VNet-to-VNet a VPN typu Point-to-site s protokolem IPsec.
services: vpn-gateway
author: cherylmc
Customer intent: As someone with a basic network background, but is new to Azure, I want to understand the capabilities of Azure VPN Gateway so that I can securely connect to my Azure virtual networks.
ms.service: vpn-gateway
ms.topic: overview
ms.date: 08/27/2020
ms.author: cherylmc
ms.custom: contperf-fy21q1
ms.openlocfilehash: c19e1156f897873defaba4cebaabb904d034ead3
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "97032741"
---
# <a name="what-is-vpn-gateway"></a>Co je VPN Gateway?

Brána VPN je specifický typ brány virtuální sítě, která se používá k posílání šifrovaného provozu mezi virtuální sítí Azure a místním umístěním přes veřejný internet. Bránu VPN můžete použít také k posílání šifrovaného provozu mezi virtuálními sítěmi Azure po síti Microsoftu. Každá virtuální síť může mít pouze jednu bránu VPN. K jedné bráně VPN však můžete vytvořit několik připojení. Když vytvoříte několik připojení ke stejné bráně VPN, všechny tunely VPN sdílejí dostupnou šířku pásma.

## <a name="what-is-a-virtual-network-gateway"></a><a name="whatis"></a>Co je brána virtuální sítě?

Brána virtuální sítě se skládá ze dvou nebo více virtuálních počítačů, které se nasazují v konkrétní podsíti, kterou vytvoříte s názvem *podsíť brány*. Virtuální počítače brány virtuální sítě obsahují směrovací tabulky a spouštějí konkrétní služby brány. Tyto virtuální počítače se vytvoří při vytváření brány virtuální sítě. Nemůžete přímo nakonfigurovat virtuální počítače, které jsou součástí brány virtuální sítě.

Když nakonfigurujete bránu virtuální sítě, nakonfigurujete nastavení, které určuje typ brány. Typ brány určuje, jak se bude používat Brána virtuální sítě, a akce, které brána přijímá. Typ brány VPN určuje, že typ brány virtuální sítě vytvořená je brána sítě VPN. Tím se odlišuje od brány ExpressRoute, která používá jiný typ brány. Virtuální síť může mít dvě brány virtuální sítě. Jedna Brána VPN a jedna brána ExpressRoute. Další informace najdete v části [Typy bran](vpn-gateway-about-vpn-gateway-settings.md#gwtype).

Vytvoření vytváření brány virtuální sítě může trvat až 45 minut. Při vytvoření brány virtuální sítě se virtuální počítače brány nasadí do podsítě brány a nakonfigurují s použitím nastavení, která zadáte. Po vytvoření brány VPN můžete vytvořit tunelové propojení IPsec/IKE mezi touto bránou VPN a jinou bránou VPN (VNet-to-VNet) nebo tunelové propojení IPsec/IKE mezi místními sítěmi mezi bránou VPN a místním zařízením VPN (Site-to-Site). Můžete také vytvořit připojení VPN typu Point-to-Site (VPN přes OpenVPN, IKEv2 nebo SSTP), které vám umožní připojit se k virtuální síti ze vzdáleného umístění, například z konference nebo z domova.

## <a name="configuring-a-vpn-gateway"></a><a name="configuring"></a>Konfigurace služby VPN Gateway

Připojení brány VPN se spoléhá na několik prostředků nakonfigurovaných se specifickými nastaveními. Většinu prostředků je možné nakonfigurovat jednotlivě, nicméně některé prostředky je potřeba konfigurovat v určitém pořadí.

### <a name="design"></a><a name="diagrams"></a>Návrh

Je důležité vědět, že pro připojení brány VPN jsou dostupné různé konfigurace. Musíte určit, která konfigurace bude nejlépe vyhovovat vašim potřebám. Například připojení Point-to-site, Site-to-site a souběžná existující ExpressRoute/site-to-site mají různé pokyny a požadavky na konfiguraci. Informace o návrhu a zobrazení diagramů topologie připojení najdete v tématu [design](design.md).

### <a name="planning-table"></a><a name="planningtable"></a>Plánovací tabulka

Následující tabulka vám může pomoci se zvolením nejlepší možnosti připojení pro vaše řešení.

[!INCLUDE [cross-premises](../../includes/vpn-gateway-cross-premises-include.md)]

### <a name="settings"></a><a name="settings"></a>Nastavení

Nastavení, která jste pro jednotlivé zdroje zvolili, jsou pro vytvoření úspěšného připojení zásadní. Informace o jednotlivých prostředcích a nastaveních služby VPN Gateway najdete v tématu [Informace o nastavení služby VPN Gateway](vpn-gateway-about-vpn-gateway-settings.md). Tento článek obsahuje informace, které vám pomůžou pochopit typy bran, skladové položky bran, typy sítí VPN, typy připojení, podsítě brány, místní síťové brány a různá další nastavení prostředků, o kterých možná uvažujete.

### <a name="deployment-tools"></a><a name="tools"></a>Nástroje pro nasazení

Prostředky můžete začít vytvářet a konfigurovat pomocí konfiguračního nástroje, jako je například Azure Portal. Později se můžete rozhodnout používat ke konfiguraci dalších prostředků nebo úpravám stávajících prostředků jiný nástroj, třeba PowerShell. V současné době nelze konfigurovat všechny prostředky a nastavení prostředků pomocí webu Azure Portal. Pokyny v článcích pro každou topologii připojení určují, kdy je zapotřebí specifický konfigurační nástroj.

## <a name="gateway-skus"></a><a name="gwsku"></a>Skladové položky brány

Při vytváření brány virtuální sítě zadáváte jednotku SKU brány, kterou chcete použít. Vyberte jednotku SKU, která splňuje vaše požadavky na základě typů úloh, propustnosti, funkcí a SLA.

* Další informace o SKU brány, včetně podporovaných funkcí, produkčního a vývojového a testovacího prostředí, najdete v článku [VPN Gateway nastavení – SKU brány](vpn-gateway-about-vpn-gateway-settings.md#gwsku) .
* Informace o starších verzích SKU najdete v tématu [práce se staršími SKU](vpn-gateway-about-skus-legacy.md).

### <a name="gateway-skus-by-tunnel-connection-and-throughput"></a><a name="benchmark"></a>SKU brány podle tunelu, připojení a propustnosti

[!INCLUDE [Aggregated throughput by SKU](../../includes/vpn-gateway-table-gwtype-aggtput-include.md)]

## <a name="availability-zones"></a><a name="availability"></a>Zóny dostupnosti

Brány VPN se dají nasadit v Zóny dostupnosti Azure. To přináší odolnost proti chybám, škálovatelnost a vyšší dostupnost bran virtuálních sítí. Nasazování bran v rámci Zón dostupnosti Azure fyzicky a logicky odděluje brány v rámci oblasti, přičemž zároveň chrání připojení vaší místní sítě k Azure před výpadky na úrovni zóny. Další informace najdete [v tématu o branách redundantní virtuální sítě v zóně zóny dostupnosti Azure](about-zone-redundant-vnet-gateways.md).

## <a name="pricing"></a><a name="pricing"></a>Ceny

[!INCLUDE [vpn-gateway-about-pricing-include](../../includes/vpn-gateway-about-pricing-include.md)]

Další informace o skladových jednotkách (SKU) brány pro službu VPN Gateway najdete v tématu [Skladové jednotky (SKU) brány](vpn-gateway-about-vpn-gateway-settings.md#gwsku).

## <a name="faq"></a><a name="faq"></a>Časté otázky

Nejčastější dotazy týkající se služby VPN Gateway najdete v tématu [Nejčastější dotazy ke službě VPN Gateway](vpn-gateway-vpn-faq.md).

## <a name="whats-new"></a><a name="new"></a>Co je nového?

Přihlaste se k odběru informačního kanálu RSS a zobrazte nejnovější VPN Gateway aktualizace funkcí na stránce s [aktualizacemi Azure](https://azure.microsoft.com/updates/?category=networking&query=VPN%20Gateway) .

## <a name="next-steps"></a>Další kroky

- Další informace najdete v tématu věnovaném [nejčastějším dotazům k VPN Gateway](vpn-gateway-vpn-faq.md).
- Přečtěte si téma [Předplatné a omezení služeb](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits).
- Informace o některých dalších klíčových [možnostech sítě](../networking/networking-overview.md) v Azure.
