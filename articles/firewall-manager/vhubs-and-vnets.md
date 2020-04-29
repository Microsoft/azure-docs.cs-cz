---
title: Jaké jsou možnosti architektury Azure Firewall Manageru?
description: Porovnejte a narozdílujte pomocí architektury virtuální sítě rozbočovače nebo zabezpečeného virtuálního rozbočovače s nástrojem Azure Firewall Manager.
author: vhorne
ms.service: firewall-manager
services: firewall-manager
ms.topic: article
ms.date: 02/18/2020
ms.author: victorh
ms.openlocfilehash: b946a360ced05500a4ef89cda7c623d8ae16658e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "77444573"
---
# <a name="what-are-the-azure-firewall-manager-architecture-options"></a>Jaké jsou možnosti architektury Azure Firewall Manageru?

Správce Azure Firewall může poskytovat správu zabezpečení pro dva typy architektury sítě:

- **zabezpečené virtuální centrum**

   [Azure Virtual WAN hub](../virtual-wan/virtual-wan-about.md#resources) je prostředek spravovaný Microsoftem, který umožňuje snadno vytvářet architektury hub a paprsků. Pokud jsou zásady zabezpečení a směrování přidruženy k takovému centru, označuje se jako *[zabezpečené virtuální rozbočovač](secured-virtual-hub.md)*. 
- **virtuální síť centra**

   Toto je standardní virtuální síť Azure, kterou vytváříte a spravujete sami. Pokud jsou zásady zabezpečení přidružené k takovému centru, označuje se jako *virtuální síť rozbočovače*. V tuto chvíli se podporují jenom zásady Azure Firewall. Můžete vytvořit partnerský paprsek virtuálních sítí, které obsahují vaše servery a služby pro úlohy. Brány firewall můžete spravovat i v samostatných virtuálních sítích, které nejsou partnerského vztahu k žádnému rozbočovači.

## <a name="comparison"></a>Srovnání

Následující tabulka porovnává tyto dvě možnosti architektury a může vám rozhodnout, která z nich je pro požadavky na zabezpečení vaší organizace nejvhodnější:


|  |**Virtuální síť centra**|**Zabezpečené virtuální centrum**  |
|---------|---------|---------|
|**Základní prostředek**     |Virtuální síť|Virtuální centrum sítě WAN|
|**Rozbočovač & paprskový**     |Používá partnerský vztah virtuálních sítí|Automatizované pomocí připojení k virtuální síti rozbočovače|
|**Připojení on-Prem**     |VPN Gateway až 10 GB/s a 30 připojení S2S; ExpressRoute|Víc škálovatelných VPN Gateway připojení S2S 20 GB/s a 1000. Expresní trasa|
|**Automatizované připojení větví pomocí SDWAN**      |Nepodporuje se|Podporuje se|
|**Centra na oblast**     |Více virtuálních sítí na oblast|Jedno virtuální centrum na oblast Několik rozbočovačů je možné s několika virtuálními sítěmi WAN|
|**Azure Firewall – několik veřejných IP adres**      |Poskytnutý zákazník|Automaticky vygenerováno. Bude k dispozici pro GA.|
|**Azure Firewall Zóny dostupnosti**     |Podporuje se|Není k dispozici ve verzi Preview. K dispozici pro GA|
|**Pokročilé zabezpečení Internetu s zabezpečením třetích stran jako partneři služeb**     |Navázáno a spravované připojení VPN k partnerské službě podle výběru|Automatizované prostřednictvím důvěryhodného partnera zabezpečení a prostředí pro správu partnerů|
|**Centralizovaná správa tras pro směrování provozu do centra**     |Uživatelem definovaná trasa spravovaná zákazníkem|Podporováno pomocí protokolu BGP|
|**Firewall webových aplikací ve službě Application Gateway** |Podporováno v Virtual Network|Aktuálně podporované v síti paprsků|
|**Síťové virtuální zařízení**|Podporováno v Virtual Network|Aktuálně podporované v síti paprsků|

## <a name="next-steps"></a>Další kroky

- [Přehled nasazení Preview Azure firewall Manageru](deployment-overview.md)
- Přečtěte si o [zabezpečených virtuálních rozbočovačích](secured-virtual-hub.md).