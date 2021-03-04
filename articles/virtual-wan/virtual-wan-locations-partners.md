---
title: Azure Virtual WAN partneři a umístění | Microsoft Docs
description: Tento článek obsahuje seznam partnerů Azure Virtual WAN a umístění centra.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 03/03/2021
ms.author: cherylmc
ms.custom: references_regions
Customer intent: As someone with a networking background, I want to find a Virtual WAN partner
ms.openlocfilehash: 1e8a8aa632d4c91cdfc6753d988a439d2de8938e
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/04/2021
ms.locfileid: "102040531"
---
# <a name="virtual-wan-partners-and-virtual-hub-locations"></a>Virtuální partneři sítě WAN a umístění virtuálních rozbočovačů

Tento článek poskytuje informace o oblastech a partnerech podpor Virtual WAN pro připojení k virtuálnímu rozbočovači.

Azure Virtual WAN je síťová služba poskytující optimalizované a automatizované možnosti propojení jednotlivých poboček prostřednictvím Azure. Virtuální síť WAN umožňuje připojit a nakonfigurovat zařízení v pobočkách tak, aby komunikovaly přes Azure. Připojení a konfiguraci lze provést buď ručně, nebo pomocí zařízení poskytovatele prostřednictvím virtuální sítě WAN. Používání partnerských zařízení umožňuje snadné použití, zjednodušení připojení a správu konfigurace.

Připojení z místního zařízení se vytváří automatizovaným způsobem pro virtuální rozbočovač. Virtuální rozbočovač je virtuální síť spravovaná Microsoftem. Rozbočovač obsahuje různé koncové body služby, které umožňují připojení z vaší místní sítě (vpnsite). Můžete mít jenom jedno centrum na oblast.

## <a name="branch-ipsec-connectivity-automation-from-partners"></a><a name="automation"></a>Automatizace připojení branou IPsec od partnerů

Zařízení, která se připojují k Azure Virtual WAN, mají vestavěnou automatizaci připojení. Tato možnost se obvykle nastavuje v uživatelském rozhraní pro správu zařízení (nebo ekvivalentní), která nastavuje připojení a správu konfigurací mezi zařízením sítě VPN na koncový bod VPN virtuálního rozbočovače Azure (Brána sítě VPN).

Následující automatizace na nejvyšší úrovni se nastavuje v konzole zařízení/centra pro správu:

* Správná oprávnění pro zařízení pro přístup ke skupině prostředků Azure Virtual WAN
* Nahrání firemního zařízení do Azure Virtual WAN
* Automatické stažení informací o připojení Azure
* Konfigurace místního zařízení pobočky 

Někteří partneři pro připojení můžou automatizovat automatizaci, aby zahrnovali vytvoření virtuální sítě a VPN Gateway virtuálního rozbočovače Azure. Pokud chcete získat další informace o automatizaci, přečtěte si téma [pokyny pro automatizaci virtuálních sítí WAN](virtual-wan-configure-automation-providers.md).

## <a name="branch-ipsec-connectivity-partners"></a><a name="partners"></a>Partneři připojení bran IPsec

[!INCLUDE [partners](../../includes/virtual-wan-partners-include.md)]

Následující partneři jsou plánované podle našeho plánu na základě seznamu podmínek podepsaného mezi společnostmi, které označují rozsah práce pro automatizaci připojení IPsec mezi partnerským zařízením a Azure Virtual WAN Gateway: 128 technologie, Arista, F5 Networks, Oracle SD-WAN (Talari) a SharpLink.

## <a name="partners-with-integrated-virtual-hub-offerings"></a>Partneři s integrovanými nabídkami virtuálního centra

Kromě automatizovaného připojení pobočky k protokolu IPsec můžou někteří partneři nabízet **Síťová virtuální zařízení (síťová virtuální zařízení)** , která se dají integrovat přímo do centra Azure Virtual WAN.  To zákazníkům umožňuje ukončit připojení svých poboček do kompatibilního zařízení třetí strany ve virtuálním centru.  

Partneři, kteří nabízejí síťové virtuální zařízení ve virtuálním centru WAN, musí:

* Implementovali jste automatizaci připojení pomocí protokolu IPsec ze svých firemních zařízení a připojili svou nabídku síťové virtuální zařízení do služby Azure Virtual WAN hub.
* Máte v Azure Marketplace k dispozici existující nabídku síťového virtuálního zařízení.

Pokud jste partnerem a máte dotazy týkající se spravovaného síťové virtuální zařízení v nabídce virtuálního rozbočovače, kontaktujte nás odesláním e-mailu na adresu. vwannvaonboarding@microsoft.com

## <a name="integrated-virtual-hub-nva-partners"></a>SÍŤOVÉ virtuální zařízení partneři s integrovaným virtuálním rozbočovačem

Tito partneři mají **spravované nabídky aplikací** , které jsou teď dostupné pro nasazení do virtuálního centra WAN.

|Partneři|Průvodce konfigurací/nasazením a nasazením|
|---|---|
|[Barracuda Networks](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/barracudanetworks.barracuda_cloudgenwan_gateway?tab=Overviewus/marketplace/apps/barracudanetworks.barracuda_cloudgenwan_gateway?tab=Overview)| [Průvodce nasazením Barracuda CloudGen WAN](https://campus.barracuda.com/product/cloudgenwan/doc/91980640/deployment/)|
|[VWAN se směrovačem služby Cisco Cloud Service (CSR)](https://aka.ms/ciscoMarketPlaceOffer)| Během Public Preview služby Cisco Cloud Services (CSR) WAN do centra VWAN vyžaduje Cisco registraci koncového zákazníka jako zákazníka Cisco EFT (předčasných zkušebních verzí), a to odesláním e-mailu vwan_public_preview@external.cisco.com a vyžádáním Průvodce nasazením vManage. |

Následující partneři jsou plánované k uvedení síťové virtuální zařízení do virtuálního centra v blízké budoucnosti: Aviatrix, Citrix, VeloCloud a naopak.

## <a name="locations"></a><a name="locations"></a>Polohy

[!INCLUDE [Virtual WAN regions file](../../includes/virtual-wan-regions-include.md)]

## <a name="next-steps"></a>Další kroky

* Další informace o virtuální síti WAN najdete v [nejčastějších dotazech k virtuální síti WAN](virtual-wan-faq.md).

* Další informace o tom, jak automatizovat připojení ke službě Azure Virtual WAN, najdete v tématu [pokyny pro automatizaci virtuálních sítí WAN](virtual-wan-configure-automation-providers.md).
