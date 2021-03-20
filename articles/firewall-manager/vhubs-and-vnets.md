---
title: Jaké jsou možnosti architektury Azure Firewall Manageru?
description: Porovnejte a narozdílujte pomocí architektury virtuální sítě rozbočovače nebo zabezpečeného virtuálního rozbočovače s nástrojem Azure Firewall Manager.
author: vhorne
ms.service: firewall-manager
services: firewall-manager
ms.topic: article
ms.date: 09/14/2020
ms.author: victorh
ms.openlocfilehash: 71ff23e749139087f24da406474403167dcc1c0d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "90563144"
---
# <a name="what-are-the-azure-firewall-manager-architecture-options"></a>Jaké jsou možnosti architektury Azure Firewall Manageru?

Správce Azure Firewall může poskytovat správu zabezpečení pro dva typy architektury sítě:

- **zabezpečené virtuální centrum**

   [Azure Virtual WAN hub](../virtual-wan/virtual-wan-about.md#resources) je prostředek spravovaný Microsoftem, který umožňuje snadno vytvářet architektury hub a paprsků. Pokud jsou zásady zabezpečení a směrování přidruženy k takovému centru, označuje se jako *[zabezpečené virtuální rozbočovač](secured-virtual-hub.md)*. 
- **virtuální síť centra**

   Toto je standardní virtuální síť Azure, kterou vytváříte a spravujete sami. Pokud jsou zásady zabezpečení přidružené k takovému centru, označuje se jako *virtuální síť rozbočovače*. V tuto chvíli se podporují jenom zásady Azure Firewall. Můžete vytvořit partnerský paprsek virtuálních sítí, které obsahují vaše servery a služby pro úlohy. Brány firewall můžete spravovat i v samostatných virtuálních sítích, které nejsou partnerského vztahu k žádnému rozbočovači.

## <a name="comparison"></a>Porovnání

Následující tabulka porovnává tyto dvě možnosti architektury a může vám rozhodnout, která z nich je pro požadavky na zabezpečení vaší organizace nejvhodnější:


|  |**Virtuální síť centra**|**Zabezpečené virtuální centrum**  |
|---------|---------|---------|
|**Základní prostředek**     |Virtuální síť|Virtuální centrum sítě WAN|
|**Rozbočovač & paprskový**     |Používá partnerský vztah virtuálních sítí|Automatizované pomocí připojení k virtuální síti rozbočovače|
|**Připojení on-Prem**     |VPN Gateway až 10 GB/s a 30 připojení S2S; ExpressRoute|Víc škálovatelných VPN Gateway připojení S2S 20 GB/s a 1000. Expresní trasa|
|**Automatizované připojení větví pomocí SDWAN**      |Nepodporováno|Podporováno|
|**Centra na oblast**     |Více virtuálních sítí na oblast|Jedno virtuální centrum na oblast Několik rozbočovačů je možné s několika virtuálními sítěmi WAN|
|**Azure Firewall – několik veřejných IP adres**      |Poskytnutý zákazník|Automaticky vygenerováno|
|**Azure Firewall Zóny dostupnosti**     |Podporováno|Zatím nedostupné|
|**Pokročilé zabezpečení Internetu s zabezpečením třetích stran jako partneři služeb**     |Navázáno a spravované připojení VPN k partnerské službě podle výběru|Automatizované prostřednictvím poskytovatele zabezpečení partnera a prostředí pro správu partnerů|
|**Centralizovaná správa tras pro směrování provozu do centra**     |Uživatelem definovaná trasa spravovaná zákazníkem|Podporováno pomocí protokolu BGP|
|**Podpora více poskytovatelů zabezpečení**|Podporuje se ručně nakonfigurovaným vynuceným tunelovým propojením na brány firewall třetích stran.|Automatizovaná podpora pro dva poskytovatele zabezpečení: Azure Firewall pro filtrování privátních přenosů a třetí strana pro filtrování Internetu|
|**Firewall webových aplikací ve službě Application Gateway** |Podporováno v Virtual Network|Aktuálně podporované v síti paprsků|
|**Síťové virtuální zařízení**|Podporováno v Virtual Network|Aktuálně podporované v síti paprsků|
|**Podpora Azure DDoS Protection Standard**|Yes|No|

## <a name="next-steps"></a>Další kroky

- [Přehled nasazení Azure firewall Manageru](deployment-overview.md)
- Přečtěte si o [zabezpečených virtuálních rozbočovačích](secured-virtual-hub.md).