---
title: Jaké jsou možnosti architektury Správce brány Azure?
description: Porovnejte a kontrastte pomocí architektury virtuální sítě rozbočovače nebo zabezpečeného virtuálního rozbočovače pomocí Správce azure firewallu.
author: vhorne
ms.service: firewall-manager
services: firewall-manager
ms.topic: article
ms.date: 02/18/2020
ms.author: victorh
ms.openlocfilehash: b946a360ced05500a4ef89cda7c623d8ae16658e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77444573"
---
# <a name="what-are-the-azure-firewall-manager-architecture-options"></a>Jaké jsou možnosti architektury Správce brány Azure?

Azure Firewall Manager může zajistit správu zabezpečení pro dva typy architektury sítě:

- **zabezpečené virtuální rozbočovač**

   Azure [Virtual WAN Hub](../virtual-wan/virtual-wan-about.md#resources) je prostředek spravovaný microsoftem, který umožňuje snadno vytvářet architektury rozbočovače a paprsků. Pokud jsou k takovému rozbočovači přidruženy zásady zabezpečení a směrování, označuje se jako *[zabezpečené virtuální rozbočovač](secured-virtual-hub.md)*. 
- **centrální virtuální síť**

   Jedná se o standardní virtuální síť Azure, kterou vytvoříte a spravujete sami. Pokud jsou k takovému rozbočovači přidruženy zásady zabezpečení, označuje se jako *virtuální síť rozbočovače*. V současné době je podporována pouze zásady Azure Firewall. Můžete peer mluvil virtuální sítě, které obsahují servery úlohy a služby. Můžete také spravovat brány firewall v samostatných virtuálních sítích, které nejsou peered na žádné paprsku.

## <a name="comparison"></a>Srovnání

Následující tabulka porovnává tyto dvě možnosti architektury a může vám pomoci rozhodnout, která z nich je vhodná pro požadavky na zabezpečení vaší organizace:


|  |**Virtuální síť centra**|**Zabezpečené virtuální rozbočovač**  |
|---------|---------|---------|
|**Základní zdroj**     |Virtuální síť|Virtuální rozbočovač WAN|
|**& paprsku hubu**     |Používá partnerský vztah virtuální sítě.|Automatické použití připojení k virtuální síti rozbočovače|
|**Připojení na prem**     |VPN Gateway až 10 Gb/s a 30 Připojení S2S; ExpressRoute|Škálovatelnější brána VPN s kapacitou 20 Gb/s a 1000 připojení S2S; Expresní trasa|
|**Automatické připojení pobočky pomocí sdwany**      |Nepodporuje se|Podporuje se|
|**Centra podle regionů**     |Více virtuálních sítí v oblasti|Jeden virtuální rozbočovač na oblast. Možné více rozbočovačů s více virtuálními wany|
|**Azure Firewall – více veřejných IP adres**      |Zákazník za předpokladu,|Automaticky generováno. K dispozici ga.|
|**Zóny dostupnosti azure firewall**     |Podporuje se|Není k dispozici ve verzi Preview. K dispozici ga|
|**Rozšířené zabezpečení Internetu s zabezpečením třetích stran jako servisními partnery**     |Zákazník vytvořil a spravoval připojení VPN k partnerské službě dle vlastního výběru|Automatizováno prostřednictvím toku důvěryhodného partnera pro zabezpečení a prostředí pro správu partnerů|
|**Centralizovaná správa tras pro směrování provozu do rozbočovače**     |Uživatelem definovaná trasa spravovaná zákazníkem|Podporováno pomocí protokolu BGP|
|**Firewall webových aplikací ve službě Application Gateway** |Podporováno ve virtuální síti|V současné době podporováno v síti spoke|
|**Síťové virtuální zařízení**|Podporováno ve virtuální síti|V současné době podporováno v síti spoke|

## <a name="next-steps"></a>Další kroky

- Kontrola [přehledu nasazení nástroje Azure Firewall Manager Preview](deployment-overview.md)
- Informace o [zabezpečených virtuálních rozbočovačích](secured-virtual-hub.md).