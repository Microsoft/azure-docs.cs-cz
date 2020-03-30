---
title: Diagnostika místního připojení přes bránu VPN
titleSuffix: Azure Network Watcher
description: Tento článek popisuje, jak diagnostikovat místní připojení přes bránu VPN s řešením potíží s prostředky Azure Network Watcher.
services: network-watcher
documentationcenter: na
author: damendo
ms.assetid: aeffbf3d-fd19-4d61-831d-a7114f7534f9
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: damendo
ms.openlocfilehash: 835b3a69e779b536961110b674ae67f4e8c13ce0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76845064"
---
# <a name="diagnose-on-premises-connectivity-via-vpn-gateways"></a>Diagnostika místního připojení prostřednictvím bran VPN

Azure VPN Gateway umožňuje vytvořit hybridní řešení, které řeší potřebu zabezpečeného připojení mezi místní sítí a virtuální sítí Azure. Vzhledem k tomu, že vaše požadavky jsou jedinečné, je také volba místního zařízení VPN. Azure v současné době podporuje [několik zařízení VPN,](../vpn-gateway/vpn-gateway-about-vpn-devices.md#devicetable) která jsou neustále ověřována ve spolupráci s dodavateli zařízení. Před konfigurací místního zařízení VPN zkontrolujte nastavení konfigurace pro konkrétní zařízení. Podobně azure vpn gateway je nakonfigurovaná se sadou [podporovaných parametrů IPsec,](../vpn-gateway/vpn-gateway-about-vpn-devices.md#ipsec) které se používají pro navazování připojení. V současné době neexistuje žádný způsob, jak určit nebo vybrat konkrétní kombinaci parametrů IPsec z brány Azure VPN Gateway. Pro navázání úspěšného připojení mezi místním a Azure musí být místní nastavení zařízení VPN v souladu s parametry IPsec předepsanými službou Azure VPN Gateway. Pokud jsou nastavení nesprávná, dojde ke ztrátě připojení a až dosud nebylo řešení těchto problémů triviální a obvykle trvalo hodiny, než byl problém identifikován a opraven.

Díky funkci azure network watcher řešení potíží, můžete diagnostikovat všechny problémy s bránou a připojení a během několika minut mít dostatek informací, aby se informované rozhodnutí k nápravě problému.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="scenario"></a>Scénář

Chcete nakonfigurovat připojení mezi lokalitou k webu mezi Azure a místním použitím FortiGate jako místní brány VPN. K dosažení tohoto scénáře budete vyžadovat následující nastavení:

1. Brána virtuální sítě – brána VPN v Azure
1. Brána místní sítě – [místní (FortiGate) reprezentace brány VPN](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md#LocalNetworkGateway) v cloudu Azure
1. Připojení k webu (na základě trasy) – [připojení mezi bránou VPN a místním směrovačem](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal#CreateConnection)
1. [Konfigurace FortiGate](https://github.com/Azure/Azure-vpn-config-samples/blob/master/Fortinet/Current/Site-to-Site_VPN_using_FortiGate.md)

Podrobné pokyny krok za krokem pro konfiguraci konfigurace site-to-site najdete na adrese: [Vytvoření virtuální sítě s připojením site-to-site pomocí portálu Azure](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md).

Jedním z kritických kroků konfigurace je konfigurace komunikačních parametrů Protokolu IPsec, jakákoli chybná konfigurace vede ke ztrátě připojení mezi místní sítí a Azure. V současné době jsou brány Azure VPN nakonfigurované tak, aby podporovaly následující parametry IPsec pro fázi 1. Jak můžete vidět v následující tabulce, šifrovací algoritmy podporované službou Azure VPN Gateway jsou AES256, AES128 a 3DES.

### <a name="ike-phase-1-setup"></a>Nastavení ike fáze 1

| **Vlastnost** | **PolicyBased** | **Směrová a standardní nebo vysoce výkonná brána VPN** |
| --- | --- | --- |
| Verze IKE |IKEv1 |IKEv2 |
| Skupina Diffie-Hellman |Skupina 2 (1 024 bitů) |Skupina 2 (1 024 bitů) |
| Metoda ověřování |Předsdílený klíč |Předsdílený klíč |
| Algoritmy šifrování |AES256 AES128 3DES |AES256 3DES |
| Algoritmus hash |SHA1(SHA128) |SHA1(SHA128), SHA2(SHA256) |
| Životnost přidružení zabezpečení (SA) Fáze 1 (čas) |28 800 sekund |28 800 sekund |

Jako uživatel byste museli nakonfigurovat FortiGate, ukázkovou konfiguraci lze nalézt na [GitHubu](https://github.com/Azure/Azure-vpn-config-samples/blob/master/Fortinet/Current/fortigate_show%20full-configuration.txt). Nevědomky jste nakonfigurovali fortigate, aby používal SHA-512 jako algoritmus hash. Vzhledem k tomu, že tento algoritmus není podporovanýalgoritmus pro připojení založená na zásadách, vaše připojení VPN funguje.

Tyto problémy jsou obtížné řešit a hlavní příčiny jsou často neintuitivní. V takovém případě můžete otevřít lístek podpory a získat nápovědu k řešení problému. Ale s Azure Network Watcher poradce rozhraní API, můžete identifikovat tyto problémy na vlastní pěst.

## <a name="troubleshooting-using-azure-network-watcher"></a>Poradce při potížích pomocí Azure Network Watcher

Pokud chcete diagnostikovat připojení, připojte se `Start-AzNetworkWatcherResourceTroubleshooting` k Azure PowerShellu a iniciujte rutinu. Podrobnosti o použití této rutiny najdete na [adrese Poradce při potížích s bránou virtuální sítě a připojeními - PowerShell](network-watcher-troubleshoot-manage-powershell.md). Dokončení této rutiny může trvat až několik minut.

Po dokončení rutiny můžete přejít do umístění úložiště určenév rutině získat podrobné informace o problému a protokoly. Azure Network Watcher vytvoří složku ZIP, která obsahuje následující soubory protokolu:

![1][1]

Otevřete soubor s názvem IKEErrors.txt a zobrazí následující chybu, která označuje problém s místní chybnou konfigurací protokolu IKE.

```
Error: On-premises device rejected Quick Mode settings. Check values.
     based on log : Peer sent NO_PROPOSAL_CHOSEN notify
```

Můžete získat podrobné informace z Scrubbed-wfpdiag.txt o chybě, jako v tomto `ERROR_IPSEC_IKE_POLICY_MATCH` případě se uvádí, že tam byl, že vést k připojení nefunguje správně.

Další běžnou chybnou konfigurací je určení nesprávných sdílených klíčů. Pokud jste v předchozím příkladu zadali různé sdílené klíče, soubor IKEErrors.txt zobrazí následující chybu: `Error: Authentication failed. Check shared key`.

Funkce řešení potíží s Azure Network Watcher umožňuje diagnostikovat a řešit potíže s bránou VPN a připojením s lehkostí jednoduché rutiny prostředí PowerShell. V současné době podporujeme diagnostiku následujících podmínek a pracujeme na přidání dalších podmínek.

### <a name="gateway"></a>brána

| Typ chyby | Důvod | Protokol|
|---|---|---|
| NoFault | Pokud není zjištěna žádná chyba. |Ano|
| GatewayNotFound | Nelze najít gateway nebo gateway není zřízena. |Ne|
| PlannedMaintenance |  V instanci brány probíhá údržba.  |Ne|
| UserDrivenUpdate | Když probíhá aktualizace uživatele. Může se jedná o operaci změny velikosti. | Ne |
| VipUnResponsive | Nelze dosáhnout primární instance brány. K tomu dochází, když se nezdaří sonda stavu. | Ne |
| PlatformInActive | Došlo k problému s platformou. | Ne|
| Služba není spuštěna. | Základní služba není spuštěna. | Ne|
| Bez připojenífoundforgateway | V bráně neexistuje žádná připojení. Tohle je jen varování.| Ne|
| Připojení není připojeno | Žádné připojení není připojeno. Tohle je jen varování.| Ano|
| GatewayCPUUsageExceeded | Aktuální využití procesoru brány je > 95 %. | Ano |

### <a name="connection"></a>Připojení

| Typ chyby | Důvod | Protokol|
|---|---|---|
| NoFault | Pokud není zjištěna žádná chyba. |Ano|
| GatewayNotFound | Nelze najít gateway nebo gateway není zřízena. |Ne|
| PlannedMaintenance | V instanci brány probíhá údržba.  |Ne|
| UserDrivenUpdate | Když probíhá aktualizace uživatele. Může se jedná o operaci změny velikosti.  | Ne |
| VipUnResponsive | Nelze dosáhnout primární instance brány. Stává se to, když sonda stavu selže. | Ne |
| ConnectionEntityNotFound | Chybí konfigurace připojení. | Ne |
| ConnectionIsMarkedOdpojened | Připojení je označeno jako odpojené. |Ne|
| ConnectionNotConfiguredonGateway | Základní služba nemá nakonfigurované připojení. | Ano |
| ConnectionMarkedStandby | Základní služba je označena jako pohotovostní režim.| Ano|
| Ověřování | Předsdílený nesoulad klíčů. | Ano|
| PeerReachability | Brána partnera není dostupná. | Ano|
| IkePolicyMismatch | Brána partnera má zásady IKE, které Azure nepodporuje. | Ano|
| Chyba wfpParse | Při analýzě protokolu WFP došlo k chybě. |Ano|

## <a name="next-steps"></a>Další kroky

Naučte se kontrolovat připojení brány VPN s PowerShellem a Azure Automation tak, že navštívíte [monitorovat brány VPN pomocí řešení potíží s Azure Network Watcher](network-watcher-monitor-with-azure-automation.md)

[1]: ./media/network-watcher-diagnose-on-premises-connectivity/figure1.png
