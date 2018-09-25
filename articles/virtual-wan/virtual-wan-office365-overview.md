---
title: Rovina řízení Office 365 v Azure virtuální sítě WAN
description: Další informace o rovina řízení služeb Office 365 do virtuální sítě WAN.
author: cherylmc
ms.service: virtual-wan
services: virtual-wan
ms.topic: article
ms.date: 9/24/2018
ms.author: cherylmc
ms.openlocfilehash: 815b91295540e93f8f0ffbc002fcf02ce1c13365
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46992670"
---
# <a name="office-365-control-plane-in-virtual-wan"></a>Rovina řízení Office 365 do virtuální sítě WAN

Virtuální sítě WAN zákazníkům vybrat SDWAN zařízení můžete nakonfigurovat zásady užitečných O365 Internet pro důvěryhodného provozu na webu Azure Portal. To umožňuje:
- O365 provoz do sítě společnosti Microsoft blízko uživatele poskytuje optimální uživatelské prostředí.
- Zabraňuje back tažení provoz a vlasů pining, a ušetřit tak náklady na síť WAN.
- Doručování na zásadách připojení O365.

## <a name="faqs"></a>Nejčastější dotazy
### <a name="what-is-the-customer-benefit"></a>Co je výhoda pro zákazníky?
Pomocí této funkce do virtuální sítě WAN, zákazníci teď můžou určit provoz kategorie Office 365, které důvěřují pro přímé užitečných internet. Tento důvěryhodný O365 provoz se obejít proxy a postup přímo z umístění uživatele do nejbližší POP Microsoft. Tím se vyhnete back tažení provoz a vlasů pining, proto poskytuje optimální uživatelské prostředí a šetřit náklady na síť WAN. 

### <a name="what-are-the-office-365-traffic-categories"></a>Co jsou kategorie provoz služeb Office 365?
Office 365 představují síťových adres a podsítí. Koncové body mohou být adresy URL, rozsahů IP adres nebo IP adres. Adresy URL může být plně kvalifikovaný název domény, jako je *account.office.net*, nebo jako adresa URL zástupný znak **. office365.com*. Koncové body jsou odděleni do tří kategorií - **optimalizace**, **povolit**, a **výchozí**podle jejich závažnosti. Další informace o kategoriích koncový bod se [tady](https://docs.microsoft.com/office365/enterprise/office-365-network-connectivity-principles#BKMK_Categories).

### <a name="which-office-365-traffic-category-is-recommended-by-microsoft-for-direct-internet-breakout"></a>Kategorie Office 365 provozu se doporučuje společností Microsoft pro přímé užitečných Internetu?
**Optimalizace** kategorie je nejdůležitější koncové body sítě a je potřeba obejít konec protokolu SSL a kontrolovat a další síťová zařízení zabezpečení. Měl by mít přímé Internet odchozího přenosu dat co nejblíž uživatelům. Tyto koncové body představují Office 365 scénáře, které jsou nejvíce citlivé na výkon, latenci a dostupnosti sítě. Tato kategorie zahrnuje (v řádu ~ 10) malého počtu klíč adresy URL a definovanou sadu IP podsítí vyhrazený pro úlohy základní Office 365, jako jsou Exchange Online, SharePoint Online, Skype pro firmy Online a Microsoft Teams. 

**Povolit** kategorie se také doporučuje pro přímý výstup Internetu. Povolit síťový provoz i když tolerovat určitou latenci sítě. Koncové body v kategoriích optimalizace a povolit jsou všechny umístěny v datových centrech společnosti Microsoft a spravovat v rámci Office 365. Výchozí kategorie mohou být přesměrováni na výchozím umístění Internet odchozího přenosu dat a nevyžadují přímý výstup Internetu nebo obejít konce protokolu SSL a zkontrolovat zařízení.

### <a name="how-do-i-set-my-o365-policies-via-virtual-wan"></a>Jak nastavím my O365 zásady prostřednictvím virtuální sítě WAN
Můžete povolit zásady prostřednictvím **virtuální sítě WAN** -> **nastavení** -> **konfigurace** kartu. Tady můžete zadat upřednostňované kategorie O365 provozu pro přímé užitečných internet.

![Konfigurace rovina řízení služeb Office 365 do virtuální sítě WAN](media/virtual-wan-office365-overview/configure-office365-control-plane.png)

### <a name="how-does-this-work"></a>Jak to funguje?

1.  Provoz O365 přejde do sítě společnosti Microsoft blízko uživatele poskytuje optimální prostředí.
2.  Zásady směrování se spotřebovávají SDWAN. Pak obcházejí proxy zabezpečení pro důvěryhodného kategorie a provádí místní s přímým přístupem užitečných pro tyto kategorie.
3.  Tažení zpět a provoz vlasů pining předcházet ukládání náklady na síť WAN.

### <a name="which-partner-devices-support-this-via-virtual-wan"></a>Partnerské zařízení, která to podporují prostřednictvím virtuální sítě WAN?
Citrix v současné době podporuje tyto zásady prostřednictvím virtuální sítě WAN.

### <a name="what-happens-to-the-remaining-categories-of-untrusted-o365-traffic"></a>Co se stane zbývající kategorie (nedůvěryhodné) provoz O365?
Zbývající provoz O365 bude následovat zákazníkům výchozí internet provoz cestu.

### <a name="what-if-i-have-already-specified-my-o365-policies-via-my-sdwan-provider"></a>Co když jsem už jste zadali své zásady O365 prostřednictvím poskytovatel SDWAN?
Pokud chcete zadat zásady prostřednictvím uživatelského rozhraní SDWAN i Azure virtuální sítě WAN, zásady nastavené v virtuální sítě WAN bude mít přednost.

## <a name="next-steps"></a>Další postup
- Další informace o [virtuální sítě WAN](virtual-wan-about.md).