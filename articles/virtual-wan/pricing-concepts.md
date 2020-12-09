---
title: Informace o cenách virtuální sítě WAN
titleSuffix: Azure Virtual WAN
description: Tento článek popisuje běžné otázky týkající se cen Virtual WAN.
services: virtual-wan
author: reyandap
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 12/08/2020
ms.author: reyandap
ms.custom: references_pricing
ms.openlocfilehash: 8ced9bb33be341d35904967092414676a6ffe3ac
ms.sourcegitcommit: 80c1056113a9d65b6db69c06ca79fa531b9e3a00
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/09/2020
ms.locfileid: "96905532"
---
# <a name="about-virtual-wan-pricing"></a>Informace o cenách virtuální sítě WAN

Služba Azure Virtual WAN přináší do sjednoceného rozhraní několik síťových a zabezpečených služeb. Je založený na architektuře hvězdicové architektury, kde jsou centra spravovaná Microsoftem pomocí různých služeb poskytovaných v rámci centra, jako je VPN, ExpressRoute, User VPN (Point-to-site), firewall, směrování atd.

Ceny každé služby ve virtuální síti WAN se účtují. Proto se pro virtuální síť WAN nedá navrhnout jedna cena. [Cenová Kalkulačka Azure](https://azure.microsoft.com/pricing/calculator/) poskytuje mechanismus pro odvození nákladů, který je založen na službách zřízených ve virtuální síti WAN. Tento článek popisuje Nejčastější dotazy týkající se cen virtuální sítě WAN.

>[!NOTE]
>Aktuální informace o cenách najdete v tématu [ceny služby Virtual WAN](https://azure.microsoft.com/pricing/details/virtual-wan/).
>

## <a name="common-pricing-questions"></a><a name="questions"></a>Běžné otázky týkající se cen

### <a name="what-is-a-scale-unit"></a><a name="scale-unit"></a>Co je jednotka škálování?

**Jednotka škálování** poskytuje jednotku pro agregovanou kapacitu typu Site-to-Site (S2S), Point-to-Site (P2S) a EXPRESSROUTE (ER) ve virtuálním rozbočovači. Například:

* **1 jednotka ŠKÁLOVÁNÍ S2S VPN** označuje celkovou kapacitu 500 MB/s VPN Gateway (duální instance se nasazují pro odolnost proti chybám) ve virtuálních rozbočovačích za cenu $0.361/hod.
* **1 jednotka pro ŠKÁLOVÁNÍ ER** implikuje celkem 2 GB/s.
* **5 jednotek ŠKÁLOVÁNÍ ER** by znamenalo celkovou bránu 10 GB/s v rámci ceny za virtuální síť rozbočovače ve výši $0.42; * 5/hod. ER se zvýší o $0,25/hod z 6 až 10 jednotek škálování.

### <a name="what-is-a-connection-unit"></a><a name="connection-unit"></a>Co je jednotka připojení?

**Jednotka připojení** se vztahuje na všechny místní nebo jiné koncové body, které se připojují k bránám Azure. Pro síť VPN typu Site-to-site implikuje tato hodnota větve. Pro uživatele VPN (Point-to-site) Tato hodnota implikuje vzdálené uživatele. Pro ExpressRoute tato hodnota implikuje připojení okruhu ExpressRoute.<br>Například:

* Jedno připojení k síti VPN se připojuje k Azure VPN v nákladech virtuálního rozbočovače. $0,05/hod. Proto připojení větví 100, která se připojují k virtuálnímu rozbočovači Azure, by znamenala náklady na $0,05 * 100/hod.

* Dvě připojení okruhu ExpressRoute, která se připojují k virtuálnímu rozbočovači, by měla náklady na hodnotu $0,05 * 2/hod

* Tři vzdálená uživatelská připojení, která se připojují k virtuálnímu rozbočovači Azure P2S Gateway, budou cost $0.03 * 3/HR.

### <a name="how-are-data-transfer-charges-calculated"></a><a name="data-transfer"></a>Jak se počítají poplatky za přenos dat?

* Jakékoli přenosy přicházející do Azure se neúčtují. Provoz, který opouští Azure (přes VPN, ExpressRoute nebo připojení VPN typu Point-to-site), podléhá standardním [poplatkům za Azure Data Transfer](https://azure.microsoft.com/pricing/details/bandwidth/).

* Pro poplatky za přenos dat mezi virtuálním rozbočovačem WAN a vzdáleným virtuálním rozbočovačem WAN nebo virtuální sítí v jiné oblasti, než je zdrojové centrum, platí poplatky za přenos dat pro provoz opouštějící rozbočovač. Příklad: provoz opouštící centrum Východní USA se bude účtovat za $0,02/GB a přejde na Západní USA centrum. Za provoz vstupu do centra Západní USA se nic neúčtuje. Všechna centra pro provoz na rozbočovači se vztahují na Inter-Region (uvnitř/mezi kontinentální) poplatky za [přenos dat Azure](https://azure.microsoft.com/pricing/details/bandwidth/). 

### <a name="what-is-the-difference-between-a-standard-hub-fee-and-a-standard-hub-processing-fee"></a><a name="fee"></a>Jaký je rozdíl mezi standardním poplatkem centra a standardním poplatkem za zpracování?

Virtuální síť WAN nabízí dva typy:

* **Základní virtuální síť WAN**, kde můžou uživatelé nasazovat víc rozbočovačů a využívat připojení VPN typu Site-to-site. Základní virtuální síť WAN nemá pokročilé možnosti, jako jsou například plně připojená rozbočovače, připojení ExpressRoute, připojení VPN typu Point-to-Site VPN, přenosné připojení typu VNet-to-VNet, připojení VPN a ExpressRoute, přenosová konektivita nebo Azure Firewall atd. Pro centra v základní virtuální síti WAN není k dispozici žádný základní poplatek ani poplatek za zpracování dat.

* **Standardní virtuální síť WAN** poskytuje pokročilé možnosti, jako jsou například plně připojená rozbočovače, připojení ExpressRoute, připojení VPN typu Point-to-Site VPN, přenosné připojení typu VNet-to-VNet, přenos přes síť VPN a ExpressRoute, Azure firewall atd. Všechna směrování virtuálního rozbočovače poskytuje směrovač, který umožňuje více služeb ve virtuálním rozbočovači. Pro centrum se účtuje základní poplatek, který má cenu za $0,25/hod. Pro přenos připojení typu VNet-to-VNet se za zpracování dat ve směrovači virtuálního rozbočovače účtuje také poplatek. Prohlédněte si následující obrázek.

 V následujícím **příkladu** obrázku jsou k dispozici dvě virtuální sítě, VNet 1 a VNET 2. Předpokládejme, že je 1 GB/s odesíláno z virtuálního počítače ve virtuální síti 1 na jiný virtuální počítač ve virtuální síti 2. Platí následující poplatky:

* Základní poplatek za virtuální rozbočovač $0,25/hod.

* Poplatek za zpracování dat virtuálního rozbočovače z $0,02/GB pro 1 GB/s

**Příklad**

   :::image type="content" source="./media/pricing-concepts/figure-1.png" alt-text="Příklad":::

## <a name="next-steps"></a>Další kroky

* Další informace o virtuální síti WAN najdete v části [Nejčastější dotazy](virtual-wan-faq.md).

* Aktuální ceny najdete v tématu [ceny služby Virtual WAN](https://azure.microsoft.com/pricing/details/virtual-wan/).