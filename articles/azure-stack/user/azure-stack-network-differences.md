---
title: Rozdíly a aspekty sítí služby Azure Stack | Dokumentace Microsoftu
description: Další informace o rozdíly a aspekty při práci se sítěmi v Azure stacku.
services: azure-stack
keywords: ''
author: mattbriggs
manager: femila
ms.date: 10/22/2018
ms.topic: article
ms.service: azure-stack
ms.author: mabrigg
ms.reviewer: scottnap
ms.openlocfilehash: 4aa62319ee37b2f82455d1a3c7ad2d224988d93e
ms.sourcegitcommit: 33091f0ecf6d79d434fa90e76d11af48fd7ed16d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/09/2019
ms.locfileid: "54160059"
---
# <a name="considerations-for-azure-stack-networking"></a>Důležité informace týkající se sítích Azure stacku

*Platí pro: Azure Stack integrované systémy a Azure Stack Development Kit*

Sítě Azure Stack má mnoho z funkcí poskytovaných službou sítě Azure. Existují však některé hlavní rozdíly, které byste měli porozumět před nasazením služby Azure Stack network.

Tento článek poskytuje přehled o jedinečných důležité informace o sítích Azure stacku a jeho funkcí. Další informace o základní rozdíly mezi Azure Stack a Azure, najdete v článku [klíče aspekty](azure-stack-considerations.md) článku.

## <a name="cheat-sheet-networking-differences"></a>Tahák: Rozdíly sítě

| Služba | Funkce | Azure (globální) | Azure Stack |
|--------------------------|----------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------|
| DNS | DNS s více tenanty | Podporováno | Není dosud podporován. |
|  | Záznamů AAAA služby DNS | Podporováno | Nepodporuje se |
|  | Zóny DNS na předplatné | 100 (výchozí)<br>Je možné zvýšit na vyžádání. | 100 |
|  | Za zónu sad záznamů DNS | 5000 (výchozí)<br>Je možné zvýšit na vyžádání. | 5000 |
|  | Názvové servery pro delegování zóny | Azure poskytuje čtyři názvové servery pro každou zónu uživatele (tenant), který je vytvořen. | Azure Stack nabízí dvě názvové servery pro každou zónu uživatele (tenant), který je vytvořen. |
| Virtual Network | Partnerské vztahy virtuálních sítí | Propojení dvou virtuálních sítí ve stejné oblasti prostřednictvím páteřní sítě Azure. | Není dosud podporován. |
|  | Adresy protokolu IPv6 | Můžete přiřadit adresu protokolu IPv6 v rámci [konfiguraci síťového rozhraní](https://docs.microsoft.com/azure/virtual-network/virtual-network-network-interface-addresses#ip-address-versions). | Podporovaný je jenom protokol IPv4. |
|  | Plán DDoS Protection | Podporováno | Ještě není podporované. |
|  | Konfigurace IP adresy škálovací sady | Podporováno | Ještě není podporované. |
|  | Privátní přístup ke službám (podsítě) | Podporováno | Ještě není podporované. |
|  | Koncové body služeb | Podporované pro interní (bez Internet) připojení ke službám Azure. | Ještě není podporované. |
| Podporovaný je jenom protokol IPv4. | Zásady koncového bodu služby | Podporováno | Ještě není podporované. |
|  | Tunelová propojení služby | Podporováno | Ještě není podporované.  |
| Network Security Groups (Skupiny zabezpečení sítě) | Rozšířená pravidla zabezpečení | Podporováno | Ještě není podporované. |
|  | Platná pravidla zabezpečení | Podporováno | Ještě není podporované. |
|  | Skupiny zabezpečení aplikací | Podporováno | Ještě není podporované. |
| Brány virtuálních sítí | Point-to-Site VPN Gateway | Podporováno | Ještě není podporované. |
|  | Brána připojení typu Vnet-to-Vnet | Podporováno | Ještě není podporované. |
|  | Typ brány virtuální sítě | Azure podporuje sítě VPN<br> ExpressRoute <br> Hyper Net | Azure Stack je momentálně podporuje pouze typ sítě VPN. |
|  | SKU služby VPN Gateway | Podporu pro Basic, GW1, GW2, GW3, standardní vysoký výkon, mimořádně vysoký výkon. | Podpora pro Basic, Standard a skladové položky High Performance. |
|  | Typ sítě VPN | Azure podporuje jak na základě zásad a na základě trasy. | Azure Stack podporuje směrování na základě pouze. |
|  | Nastavení protokolu BGP | Azure podporuje konfiguraci adresy partnerského vztahu protokolu BGP a váha partnerského uzlu. | Adresa partnerského vztahu protokolu BGP a váha partnerského uzlu jsou automaticky nakonfigurované ve službě Azure Stack. Neexistuje žádný způsob pro uživatele k nakonfigurování těchto nastavení vlastní hodnoty. |
|  | Výchozí server brány | Azure podporuje konfiguraci výchozí web pro vynucené tunelování. | Ještě není podporované. |
|  | Změna velikosti brány | Azure podporuje změnu velikosti brány po nasazení. | Znovu velikosti není podporované. |
|  | Aktivní/aktivní konfigurace | Podporováno | Ještě není podporované. |
|  | UsePolicyBasedTrafficSelectors | Azure podporuje používání selektorů přenosu na základě zásad s připojeními trasové brány. | Ještě není podporované. |
| Nástroj pro vyrovnávání zatížení | Skladová jednotka (SKU) | Základní a podporovaných nástrojů pro vyrovnávání zatížení | Je podporován pouze Load balanceru úrovně Basic.  Vlastnost SKU se nepodporuje. |
|  | Zóny | Zóny dostupnosti jsou podporovány. | Není dosud podporován. |
|  | Pravidla příchozího překladu adres podporu koncových bodů služby | Azure podporuje zadání koncových bodů služby pro pravidla příchozího překladu adres. | Azure Stack zatím nepodporuje koncové body služby, takže tyto nelze zadat. |
|  | Protocol (Protokol) | Azure podporuje zadávání GRE nebo ESP. | Třída protokolu není podporované ve službě Azure Stack. |
| Veřejná IP adresa | Verze veřejné IP adresy | Azure podporuje protokol IPv6 a IPv4 | Podporovaný je jenom protokol IPv4. |
| Síťové rozhraní | Získat efektivní směrovací tabulky | Podporováno | Ještě není podporované. |
|  | Získat efektivní seznamy ACL | Podporováno | Ještě není podporované. |
|  | Povolit akcelerované síťové služby | Podporováno | Ještě není podporované. |
|  | Předávání IP | Ve výchozím nastavení zakázané.  Je možné povolit. | Při přepnutí tohoto nastavení se nepodporuje.  Na ve výchozím nastavení. |
|  | Skupiny zabezpečení aplikací | Podporováno | Ještě není podporované. |
|  | Popisek názvu interního serveru DNS | Podporováno | Ještě není podporované. |
|  | Verze privátní IP adresa | Jsou podporovány IPv6 a IPv4. | Podporovaný je jenom protokol IPv4. |
| Network Watcher | Možnosti monitorování sítě tenanta sledovací proces sítě | Podporováno | Ještě není podporované. |
| CDN | Profily síť pro doručování obsahu | Podporováno | Ještě není podporované. |
| Application Gateway | Vyrovnávání zatížení vrstvy 7 | Podporováno | Ještě není podporované. |
| Traffic Manager | Směrování příchozího provozu pro zajištění optimálního výkonu aplikací a spolehlivost. | Podporováno | Ještě není podporované. |
| ExpressRoute | Nastavení rychlého privátního připojení ke cloudovým službám Microsoftu z vaší místní infrastruktury nebo společně umístěného zařízení. | Podporováno | Podpora pro připojení služby Azure Stack k okruhu Express Route. |

## <a name="next-steps"></a>Další postup

[DNS v Azure Stacku](azure-stack-dns.md)
