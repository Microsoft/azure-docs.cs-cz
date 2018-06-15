---
title: Diagnostikujte místní připojení prostřednictvím brány sítě VPN s sledovací proces sítě Azure | Microsoft Docs
description: Tento článek popisuje, jak diagnostikovat místní připojení prostřednictvím brány sítě VPN s odstraňováním problémů prostředků sledovací proces sítě Azure.
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
ms.openlocfilehash: e51d31035a8b05238ef0f8d13dd6b6c3f9ad02e8
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2017
ms.locfileid: "26374198"
---
# <a name="diagnose-on-premises-connectivity-via-vpn-gateways"></a>Diagnostika místní připojení prostřednictvím brány sítě VPN

Služba Azure VPN Gateway umožňuje vytvářet hybridní řešení, které řeší potřebu zabezpečené připojení mezi místní sítí a virtuální sítě Azure. Vaše požadavky jsou jedinečné, takže je volba místní zařízení VPN. Azure aktuálně podporuje [několika zařízeními VPN](../vpn-gateway/vpn-gateway-about-vpn-devices.md#devicetable) , jsou neustále ověřit ve spolupráci s dodavateli zařízení. Zkontrolujte nastavení konfigurace specifických zařízení před konfigurací vaše místní zařízení VPN. Podobně je Azure VPN Gateway nakonfigurovat sadu [podporované parametry protokolu IPsec](../vpn-gateway/vpn-gateway-about-vpn-devices.md#ipsec) , který se používá pro navazování připojení. Aktuálně neexistuje žádný způsob, jak zadat nebo vybrat konkrétní kombinaci parametrů IPsec z Azure VPN Gateway. Pro vytvoření úspěšné připojení mezi místními a Azure, nastavení místní zařízení VPN musí být v souladu s parametry protokolu IPsec podle Azure VPN Gateway. Pokud je nastavení správné, zde je ke ztrátě připojení a až doteď řešení těchto potíží nebyla trivial a obvykle trvalo hodin identifikovat a opravit potíže.

Řešení potíží s funkcí s sledovací proces sítě Azure, budete moci diagnostikovat problémy s brány a připojení a během minut mít dostatek informací pro přijmout informované rozhodnutí o vyřešení problému.

## <a name="scenario"></a>Scénář

Chcete nakonfigurovat připojení site-to-site mezi Azure a místními pomocí FortiGate jako brána místní sítě VPN. K dosažení tohoto scénáře, by vyžadovaly následující nastavení:

1. Brána virtuální sítě - bránu sítě VPN v Azure
1. Brána místní sítě - [místní brány sítě VPN (FortiGate)](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md#LocalNetworkGateway) reprezentace v cloudu Azure
1. Připojení Site-to-site (trasy na základě) - [připojení mezi služby VPN Gateway a místní směrovač](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal#createconnection)
1. [Konfigurace FortiGate](https://github.com/Azure/Azure-vpn-config-samples/blob/master/Fortinet/Current/Site-to-Site_VPN_using_FortiGate.md)

Podrobné pokyny krok za krokem pro konfiguraci konfigurace Site-to-Site najdete navštivte stránky: [vytvoření virtuální sítě s připojením Site-to-Site pomocí webu Azure portal](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md).

Jeden z kroků kritické konfigurace je konfigurace komunikační parametry protokolu IPsec, všechny chybné konfigurace vede ke ztrátě připojení mezi místní sítí a Azure. Aktuálně Azure VPN Gateway jsou konfigurovány pro podporu následující parametry protokolu IPsec pro fázi 1. Všimněte si, jak je uvedeno výše, že toto nastavení nelze změnit.  Jak můžete vidět v následující tabulce, jsou šifrovací algoritmy, které podporuje Azure VPN Gateway AES256, AES128 a 3DES.

### <a name="ike-phase-1-setup"></a>Nastavení protokolu IKE fáze 1

| **Vlastnost** | **PolicyBased** | **RouteBased a standardní nebo vysoce výkonné VPN gateway** |
| --- | --- | --- |
| Verze IKE |IKEv1 |IKEv2 |
| Skupina Diffie-Hellman |Skupina 2 (1 024 bitů) |Skupina 2 (1 024 bitů) |
| Metoda ověřování |Předsdílený klíč |Předsdílený klíč |
| Algoritmy šifrování |AES256 AES128 3DES |AES256 3DES |
| Algoritmus hash |SHA1(SHA128) |SHA1(SHA128), SHA2(SHA256) |
| Životnost přidružení zabezpečení (SA) Fáze 1 (čas) |28 800 sekund |10 800 sekund |

Jako uživatel, bude potřeba nakonfigurovat vaše FortiGate, ukázkové konfiguraci najdete na [Githubu](https://github.com/Azure/Azure-vpn-config-samples/blob/master/Fortinet/Current/fortigate_show%20full-configuration.txt). Nechtěně jste nakonfigurovali vaší FortiGate používat jako algoritmu hash SHA-512. Protože tento algoritmus není podporované algoritmus pro připojení na základě zásad, připojení k síti VPN funguje.

Tyto problémy se obtížně řešení a základní příčiny často intuitivní. V takovém případě můžete otevřít lístek podpory, chcete-li získat nápovědu k řešení problému. Ale s sledovací proces sítě Azure Poradce při potížích s rozhraní API, můžete identifikovat tyto problémy sami.

## <a name="troubleshooting-using-azure-network-watcher"></a>Řešení problémů pomocí sledovací proces sítě Azure

Při diagnostice připojení, připojení k prostředí Azure PowerShell a zahájit `Start-AzureRmNetworkWatcherResourceTroubleshooting` rutiny. Můžete najít podrobnosti o použití této rutiny v [řešení brány virtuální sítě a připojení - PowerShell](network-watcher-troubleshoot-manage-powershell.md). Tato rutina může trvat až několik minut na dokončení.

Po dokončení rutiny můžete přejít do zadaného v rutině k získání podrobných informací o problému a protokoly umístění úložiště. Azure sledovací proces sítě se vytvoří složky zip, který obsahuje následující soubory protokolu:

![1][1]

Otevřete soubor s názvem IKEErrors.txt a zobrazí se chybová zpráva, která znamená problém s místním chybné konfigurace nastavení protokolu IKE.

```
Error: On-premises device rejected Quick Mode settings. Check values.
     based on log : Peer sent NO_PROPOSAL_CHOSEN notify
```

Podrobné informace můžete získat z Scrubbed-wfpdiag.txt o chybě, protože v takovém případě uvádí, že došlo `ERROR_IPSEC_IKE_POLICY_MATCH` které vedou k připojení nepracuje správně.

Další běžné chybné konfigurace je zadání nesprávné sdílených klíčů. Je-li v předchozím příkladu byl zadán jiný sdílených klíčů, IKEErrors.txt ukazuje k následující chybě: `Error: Authentication failed. Check shared key`.

Řešení potíží s Azure sledovací proces sítě umožňuje při diagnostice a řešení brány sítě VPN a připojení snadno jednoduché rutiny prostředí PowerShell. V současné době podporují diagnostikování následující podmínky a pracují směrem přidáním další podmínky.

### <a name="gateway"></a>brána

| Typ chyby | Důvod | Protokol|
|---|---|---|
| NoFault | Když je zjištěna žádná chyba. |Ano|
| GatewayNotFound | Nelze najít, že není zřízený brány nebo brána. |Ne|
| PlannedMaintenance |  Instance brány je v rámci údržby.  |Ne|
| UserDrivenUpdate | Pokud je aktualizace uživatele v průběhu. To může být operace změny velikosti. | Ne |
| VipUnResponsive | Nelze kontaktovat primární instance brány. To se stane, když selže test stavu. | Ne |
| PlatformInActive | Nastane problém s platformou. | Ne|
| ServiceNotRunning | Základní služba není spuštěna. | Ne|
| NoConnectionsFoundForGateway | Žádná připojení existuje v bráně. Toto je pouze upozornění.| Ne|
| ConnectionsNotConnected | Žádná z připojení jsou připojené. Toto je pouze upozornění.| Ano|
| GatewayCPUUsageExceeded | Aktuální využití brány využití procesoru je > 95 %. | Ano |

### <a name="connection"></a>Připojení

| Typ chyby | Důvod | Protokol|
|---|---|---|
| NoFault | Když je zjištěna žádná chyba. |Ano|
| GatewayNotFound | Nelze najít, že není zřízený brány nebo brána. |Ne|
| PlannedMaintenance | Instance brány je v rámci údržby.  |Ne|
| UserDrivenUpdate | Pokud je aktualizace uživatele v průběhu. To může být operace změny velikosti.  | Ne |
| VipUnResponsive | Nelze kontaktovat primární instance brány. Ho se stane, když selže test stavu. | Ne |
| ConnectionEntityNotFound | Konfigurace připojení nebyl nalezen. | Ne |
| ConnectionIsMarkedDisconnected | Připojení je označena "odpojené". |Ne|
| ConnectionNotConfiguredOnGateway | Základní služby není k dispozici připojení nakonfigurovaná. | Ano |
| ConnectionMarkedStandy | Základní služby je označena jako pohotovostní režim.| Ano|
| Authentication | Neshoda předsdílený klíč. | Ano|
| PeerReachability | Sdílené brána není dostupný. | Ano|
| IkePolicyMismatch | Bránu sdílené má IKE zásady, které nejsou podporované službou Azure. | Ano|
| Chyba WfpParse | Došlo k chybě při analýze protokolů Ochrana souborů systému Windows. |Ano|

## <a name="next-steps"></a>Další kroky

Další informace zkontrolujte připojení VPN Gateway pomocí prostředí PowerShell a automatizace Azure. navštivte stránky [monitorování VPN Gateway se řešení potíží s sledovací proces sítě Azure](network-watcher-monitor-with-azure-automation.md)

[1]: ./media/network-watcher-diagnose-on-premises-connectivity/figure1.png
