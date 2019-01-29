---
title: Diagnostika místního připojení prostřednictvím brány VPN pomocí služby Azure Network Watcher | Dokumentace Microsoftu
description: Tento článek popisuje, jak Diagnostika místního připojení prostřednictvím brány VPN s řešením potíží prostředků Azure Network Watcher.
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: ''
ms.assetid: aeffbf3d-fd19-4d61-831d-a7114f7534f9
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: f5c4f8d2c9cec4372ef5de70485d45ab33e022de
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/28/2019
ms.locfileid: "55099392"
---
# <a name="diagnose-on-premises-connectivity-via-vpn-gateways"></a>Diagnostika místního připojení prostřednictvím bran VPN

Brána VPN Gateway Azure umožňuje vytvářet hybridní řešení, které potřebu pro zabezpečené připojení mezi vaší místní sítí a virtuální sítí Azure. Vaše požadavky jsou jedinečné, proto je možnost místního zařízení VPN. Azure v současné době podporuje [několik zařízení VPN](../vpn-gateway/vpn-gateway-about-vpn-devices.md#devicetable) , které jsou neustále ověření ve spolupráci s dodavateli zařízení. Před konfigurací vaše místní zařízení VPN zkontrolujte nastavení konfigurace pro konkrétní zařízení. Podobně, Azure VPN Gateway má nakonfigurovanou sadu [nepodporuje parametry protokolu IPsec](../vpn-gateway/vpn-gateway-about-vpn-devices.md#ipsec) , který slouží k navázání připojení. Současné době neexistuje žádný způsob, jak zadat nebo vybrat konkrétní kombinaci parametry protokolu IPsec ze služby Azure VPN Gateway. Pro vytvoření úspěšného připojení mezi místními a Azure, místní nastavení zařízení VPN musí být v souladu s parametry protokolu IPsec stanovené službou Azure VPN Gateway. Pokud jsou nastavení správná, tady je ke ztrátě připojení a až do této chvíle při řešení těchto problémů není triviální a obvykle trvalo hodiny a identifikovat a opravit tento problém.

Pomocí služby Azure Network Watcher řešení potíží s funkcí, máte možnost diagnostikovat problémy s vaší brány a připojení a během několika minut mít dostatek informací, které se informovaně rozhodnout, jestli k nápravě problému.

## <a name="scenario"></a>Scénář

Chcete konfigurovat připojení site-to-site mezi Azure a místní pomocí FortiGate jako brána místní sítě VPN. K dosažení tohoto scénáře, by vyžadovaly následující nastavení:

1. Brána virtuální sítě – VPN Gateway v Azure
1. Brány místní sítě – [místní brána sítě VPN (FortiGate)](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md#LocalNetworkGateway) reprezentace v cloudu Azure
1. Připojení Site-to-site (směrování na základě) - [připojení mezi bránou VPN a místní směrovač](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal#createconnection)
1. [Konfigurace FortiGate](https://github.com/Azure/Azure-vpn-config-samples/blob/master/Fortinet/Current/Site-to-Site_VPN_using_FortiGate.md)

Podrobné pokyny ke konfiguraci konfigurace Site-to-Site najdete tématu: [Vytvoření virtuální sítě s připojením typu Site-to-Site pomocí webu Azure portal](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md).

Jeden z kroků konfigurace důležitých konfiguruje komunikace parametry protokolu IPsec, všechny chybné konfigurace vede ke ztrátě připojení mezi místní sítí a Azure. Azure VPN Gateway aktuálně jsou konfigurovány pro podporu následující parametry protokolu IPsec pro fázi 1. Všimněte si, jak bylo zmíněno dříve, že tato nastavení nelze změnit.  Jak je vidět v následující tabulce, jsou podporovány službou Azure VPN Gateway šifrovacích algoritmů AES256, AES128 a 3DES.

### <a name="ike-phase-1-setup"></a>Nastavení protokolu IKE fáze 1

| **Vlastnost** | **PolicyBased** | **Bránu typu RouteBased a standardní nebo VPN s vysokým výkonem** |
| --- | --- | --- |
| Verze IKE |IKEv1 |IKEv2 |
| Skupina Diffie-Hellman |Skupina 2 (1 024 bitů) |Skupina 2 (1 024 bitů) |
| Metoda ověřování |Předsdílený klíč |Předsdílený klíč |
| Algoritmy šifrování |AES256 AES128 3DES |AES256 3DES |
| Algoritmus hash |SHA1(SHA128) |SHA1(SHA128), SHA2(SHA256) |
| Životnost přidružení zabezpečení (SA) Fáze 1 (čas) |28 800 sekund |10 800 sekund |

Jako uživatel, bude potřeba nakonfigurovat váš FortiGate, najdete v ukázkové konfiguraci [Githubu](https://github.com/Azure/Azure-vpn-config-samples/blob/master/Fortinet/Current/fortigate_show%20full-configuration.txt). Neúmyslně jste nakonfigurovali váš FortiGate používat jako algoritmu hash SHA-512. Tento algoritmus není podporovaný algoritmus pro připojení na základě zásad, připojení k síti VPN funguje.

Je těžké k řešení těchto problémů a hlavní příčiny jsou často neintuitivní. V takovém případě můžete otevřít lístek podpory a získat nápovědu k řešení problému. Ale pomocí služby Azure Network Watcher řešit problémy rozhraní API, můžete určit tyto problémy s vlastními silami.

## <a name="troubleshooting-using-azure-network-watcher"></a>Řešení potíží s využitím Azure Network Watcher

K diagnostice připojení, připojte se k Azure Powershellu a zahájit `Start-AzureRmNetworkWatcherResourceTroubleshooting` rutiny. Můžete najít podrobnosti o použití této rutiny v [Poradce při potížích s brány virtuální sítě a připojení – prostředí PowerShell](network-watcher-troubleshoot-manage-powershell.md). Tato rutina může trvat až několik minut na dokončení.

Po dokončení rutiny můžete přejít na umístění úložiště, zadaná v rutině získat podrobné informace o problému a protokoly. Azure Network Watcher vytvoří složku zip, který obsahuje následující soubory protokolu:

![1][1]

Otevřete soubor s názvem IKEErrors.txt a zobrazí se následující chyba, oznamující problém s místními chybné konfigurace nastavení protokolu IKE.

```
Error: On-premises device rejected Quick Mode settings. Check values.
     based on log : Peer sent NO_PROPOSAL_CHOSEN notify
```

Můžete získat podrobné informace wfpdiag.txt Scrubbed o chybě, protože v tomto případě uvádí, že došlo `ERROR_IPSEC_IKE_POLICY_MATCH` , které umožňují připojení nefunguje správně.

Další běžné chybné konfigurace je určení nesprávné sdílené klíče. Pokud v předchozím příkladu má zadán jiné sdílené klíče, IKEErrors.txt zobrazí následující chyba: `Error: Authentication failed. Check shared key`.

Řešení potíží s Azure Network Watcher umožňuje pro diagnostiku a řešení potíží s bránou VPN a připojení se snadným jednoduchou rutinu prostředí PowerShell. Aktuálně podporujeme Diagnostika následující podmínky a pracujeme na přidání další podmínky.

### <a name="gateway"></a>brána

| Typ chyby | Důvod | Protokol|
|---|---|---|
| NoFault | Když je zjištěna žádná chyba. |Ano|
| GatewayNotFound | Nejde najít brána nebo brána není zřízená. |Ne|
| PlannedMaintenance |  Instance brány probíhá údržba.  |Ne|
| UserDrivenUpdate | Když uživatel aktualizace probíhá. To může být operace změny velikosti. | Ne |
| VipUnResponsive | Primární instance brány nemá přístup. To se stane, když selže sondu stavu. | Ne |
| PlatformInActive | Se vyskytl problém s platformou. | Ne|
| ServiceNotRunning | Základní služba není spuštěná. | Ne|
| NoConnectionsFoundForGateway | Neexistuje žádná připojení k bráně. Toto je pouze upozornění.| Ne|
| ConnectionsNotConnected | Žádné připojení jsou připojené. Toto je pouze upozornění.| Ano|
| GatewayCPUUsageExceeded | Aktuální využití brány využití procesoru je > 95 %. | Ano |

### <a name="connection"></a>Připojení

| Typ chyby | Důvod | Protokol|
|---|---|---|
| NoFault | Když je zjištěna žádná chyba. |Ano|
| GatewayNotFound | Nejde najít brána nebo brána není zřízená. |Ne|
| PlannedMaintenance | Instance brány probíhá údržba.  |Ne|
| UserDrivenUpdate | Když uživatel aktualizace probíhá. To může být operace změny velikosti.  | Ne |
| VipUnResponsive | Primární instance brány nemá přístup. To se stane, když selže sondu stavu. | Ne |
| ConnectionEntityNotFound | Konfigurace připojení se nenašel. | Ne |
| ConnectionIsMarkedDisconnected | Připojení je označen "odpojené". |Ne|
| ConnectionNotConfiguredOnGateway | Základní služba nemá nakonfigurované připojení. | Ano |
| ConnectionMarkedStandby | Základní služba je označena jako pohotovostní režim.| Ano|
| Authentication | Neshoda předsdílený klíč. | Ano|
| PeerReachability | Brána partnera není dostupná. | Ano|
| IkePolicyMismatch | Partner brány má IKE zásady, které nejsou podporovány službou Azure. | Ano|
| Chyba WfpParse | Došlo k chybě při analýze protokolu Ochrana souborů systému Windows. |Ano|

## <a name="next-steps"></a>Další postup

Zjistěte, aby se ověřilo připojení VPN Gateway pomocí Powershellu a Azure Automation návštěvou [monitorování VPN Gateway pomocí Azure Network Watcher troubleshooting](network-watcher-monitor-with-azure-automation.md)

[1]: ./media/network-watcher-diagnose-on-premises-connectivity/figure1.png
