---
title: Azure Virtual WAN – Přehled | Microsoft Docs
description: Přečtěte si o možnostech automatického škálovatelného propojení poboček pomocí virtuální sítě WAN.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: overview
ms.date: 07/10/2018
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to understand what Virtual WAN is and if it is the right choice for my Azure network.
ms.openlocfilehash: 54cf6c356ec4bb51b123e48c52c5beebc990e59d
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/13/2018
ms.locfileid: "39009966"
---
# <a name="what-is-azure-virtual-wan-preview"></a>Co je Azure Virtual WAN? (Preview)

Azure Virtual WAN je síťová služba poskytující optimalizované a automatizované možnosti propojení jednotlivých poboček prostřednictvím Azure. Virtuální síť WAN umožňuje připojit a nakonfigurovat zařízení v pobočkách tak, aby komunikovaly přes Azure. Můžete to udělat buď ručně, nebo použitím zařízení od preferovaného poskytovatele prostřednictvím partnera pro Virtual WAN. Využití zařízení preferovaného poskytovatele usnadňuje použití, zjednodušuje připojení a umožňuje správu konfigurace. Vestavěný řídicí panel Azure WAN nabízí okamžité přehledy pro řešení potíží, které vám ušetří mnoho času, a snadnou cestu k zobrazení projení poboček ve velkém měřítku.

> [!IMPORTANT]
> Azure Virtual WAN je v současnosti ve spravované verzi Public Preview. Pokud chcete Virtual WAN používat, [zaregistrujte si verzi Preview](#enroll).
>
> Tato verze Public Preview se poskytuje bez smlouvy o úrovni služeb a neměla by se používat pro úlohy v produkčním prostředí. Některé funkce nemusí být podporované, můžou mít omezené možnosti nebo nemusí být dostupné ve všech oblastech Azure. Podrobnosti najdete v [dodatečných podmínkách použití systémů Microsoft Azure Preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Tento článek obsahuje rychlý přehled o síťovém propojení úloh v prostředí Azure i mimo ně. Virtual WAN nabízí následující výhody:

* **Integrované řešení připojení v hvězdicové architektuře:** Automatizujte konfiguraci propojení poboček a spojení mezi místními servery a rozbočovačem Azure pomocí nejrůznějších zdrojů, včetně partnerských řešení Virtual WAN.
* **Automatizované vytvoření a konfigurace koncových sítí:** Připojte bez starostí své virtuální sítě a úlohy k rozbočovači Azure.
* **Intuitivní řešení potíží:** Můžete zobrazit celý tok dat v rámci Azure a získané informace použít k provedení potřebných akcí.

![Diagram virtuální sítě WAN](./media/virtual-wan-about/virtualwan.png)

## <a name="vendor"></a>Práce s partnerem Virtual WAN

1. Řadič zařízení pobočky (VPN/SDWAN) se ověří pro export informací o místní síti do Azure s použitím instančního objektu služby Azure.
2. Řadič zařízení pobočky (VPN/SDWAN) získá informace o konfiguraci připojení k Azure a aktualizuje místní zařízení. To umožňuje automatizovat stažení konfigurace místního zařízení VPN i jeho úpravu a aktualizaci.
3. Jakmile má zařízení správnou konfiguraci Azure, je možné navázat spojení mezi pobočkami (dva aktivní tunely) v rámci sítě Azure WAN. Azure vyžaduje, aby řadič pobočky (VPN/SDWAN) podporoval IKEv2. Protokol BGP je volitelný.

## <a name="resources"></a>Prostředky pro Virtual WAN

Pro konfiguraci virtuální sítě WAN mezi koncovými body vytvořte následující prostředky:

* **virtualWAN:** Prostředek virtualWAN představuje virtuální překrytí vaší sítě Azure a jde o kolekci několika prostředků. Obsahuje odkazy na všechny vaše virtuální rozbočovače, které chcete mít v rámci virtuální sítě WAN. Prostředky Virtual WAN jsou navzájem izolované a nemohou obsahovat společný rozbočovač. Virtuální rozbočovače v síti Virtual WAN spolu navzájem nekomunikují.

* **Síť:** Prostředek označovaný jako vpnsite představuje vaše místní zařízení VPN a jeho nastavení. Pokud využíváte služeb partnera pro Virtual WAN, máte integrované řešení, které automaticky exportuje tyto informace do Azure.

* **Rozbočovač:** Virtuální rozbočovač je virtuální síť spravovaná Microsoftem. Rozbočovač obsahuje různé koncové body služby, které umožňují připojení z vaší místní sítě (vpnsite). Rozbočovač je základem vaší sítě v oblasti. V každé oblasti Azure může existovat jen jeden rozbočovač. Když vytvoříte rozbočovač pomocí webu Azure portal, automaticky se vytvoří virtuální síť virtuálního rozbočovače a jeho brána VPN.

  Brána rozbočovače není totéž, co brána virtuální sítě, kterou používáte pro ExpressRoute a VPN Gateway. Například při použití virtuální sítě WAN nevytváříte připojení mezi pobočkami z místní lokality přímo do vaší virtuální sítě. Místo toho vytváříte propojení s rozbočovačem. Provoz vždy prochází přes bránu rozbočovače. To znamená, že virtuální sítě nepotřebují své vlastní brány virtuální sítě. Virtual WAN umožňuje vašim virtuálním sítím využívat výhod snadného škálování prostřednictvím virtuálního rozbočovače a jeho brány. 

* **Připojení virtuální sítě k rozbočovači:** Prostředek připojení virtuální sítě k rozbočovači se používá k bezproblémovému propojení rozbočovače a virtuální sítě. V tuto chvíli můžete rozbočovač připojit pouze k virtuálním sítím, které jsou ve stejné geografické oblasti.

##<a name="enroll"></a>Registrace verze Preview

Než budete moci nakonfigurovat virtuální síť WAN, je nejdříve potřeba zaregistrovat vaše předplatné pro verzi Preview. Jinak nebude možné na portálu se službou Virtual WAN pracovat. Pokud se chcete zaregistrovat, pošlete e-mail s ID předplatného na adresu <azurevirtualwan@microsoft.com>. Jakmile se vaše předplatné zaregistruje, dostanete e-mail s potvrzením.

## <a name="faq"></a>Nejčastější dotazy

[!INCLUDE [Virtual WAN FAQ](../../includes/virtual-wan-faq-include.md)]

## <a name="feedback"></a>Zpětná vazba na verzi Preview

Vaší zpětné vazby si velmi vážíme. Chcete-li oznámit problémy nebo nám poskytnout zpětnou vazbu (kladnou i zápornou) na službu Virtual WAN, odešlete e-mail na adresu <azurevirtualwan@microsoft.com>. V řádku předmětu uveďte název vaší společnosti v hranatých závorkách „[]“. Pokud oznamujete problém, nezapomeňte také uvést ID předplatného.

## <a name="next-steps"></a>Další kroky

K vytvoření připojení typu Site-to-Site pomocí služby Virtual WAN můžete buď využít služeb [partnera pro Virtual WAN](https://aka.ms/virtualwan), nebo vytvořit připojení ručně. Postup ručního vytvoření připojení Site-to-Site najdete v tématu [Vytvoření připojení typu Site-to-Site pomocí služby Virtual WAN](virtual-wan-site-to-site-portal.md).