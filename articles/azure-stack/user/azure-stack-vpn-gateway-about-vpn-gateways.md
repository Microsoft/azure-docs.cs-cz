---
title: Informace o službě VPN gateway pro Azure Stack | Dokumentace Microsoftu
description: Další informace o a konfiguraci brány VPN, které používáte pro Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: 0e30522f-20d6-4da7-87d3-28ca3567a890
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/04/2019
ms.author: sethm
ms.openlocfilehash: aa2754d2de8771468f59225273d88731b4f2f545
ms.sourcegitcommit: 82cdc26615829df3c57ee230d99eecfa1c4ba459
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/19/2019
ms.locfileid: "54414837"
---
# <a name="about-vpn-gateway-for-azure-stack"></a>Informace o službě VPN gateway pro Azure Stack

*Platí pro: Azure Stack integrované systémy a Azure Stack Development Kit*

Předtím, než mohou posílat síťový provoz mezi virtuální sítí Azure a vaší místní lokalitě, musíte vytvořit bránu virtuální sítě pro vaši virtuální síť.

Brána VPN je typem brány virtuální sítě, která odesílá šifrovaný síťový provoz přes veřejné spojení. Brány VPN můžete použít k posílání síťového provozu bezpečně mezi virtuální sítí ve službě Azure Stack a virtuální sítě v Azure. Mohou také odesílat provoz bezpečně mezi virtuální sítí a jiné síti, která je připojena k zařízení VPN.

Při vytváření brány virtuální sítě musíte určit typ brány, který chcete vytvořit. Azure Stack podporuje jeden typ brány virtuální sítě: **Vpn** typu.

Každá virtuální síť může mít dvě brány virtuální sítě, ale každého typu jenom jednu. V závislosti na nastavení, které zvolíte, můžete k jedné bráně VPN vytvořit několik připojení. Příkladem je konfigurace připojení typu Multi-Site.

Než vytvoříte a nakonfigurujete brány VPN Gateway pro Azure Stack, projděte si [důležité informace týkající se služby Azure Stack sítě](azure-stack-network-differences.md) se dozvíte, jak konfigurace pro službu Azure Stack se liší od Azure.

>[!NOTE]
>V Azure musí být rozdělen propustnost šířky pásma pro bránu VPN zvolíte SKU napříč všechna připojení, které jsou připojeny k bráně. Ve službě Azure Stack však hodnotu šířky pásma pro skladové položky brány sítě VPN je použita na každý prostředek připojení, která je připojená k bráně.
>
> Příklad:
> * Základní SKU brány sítě VPN v Azure, zvládne přibližně 100 MB/s z celkové propustnosti. Pokud vytvoříte dvě spojení s touto bránou VPN a jedno připojení používá 50 MB/s šířky pásma, pak 50 MB/s je k dispozici pro jiné připojení.
> * Ve službě Azure Stack *každý* připojení k základní SKU brány VPN získá přidělené 100 MB/s propustnosti.

## <a name="configuring-a-vpn-gateway"></a>Konfigurace služby VPN gateway

Připojení brány VPN se spoléhá na několik prostředků nakonfigurovaných se specifickými nastaveními. Většina těchto prostředků je možné nakonfigurovat jednotlivě, ale v některých případech musí být nakonfigurovány v určitém pořadí.

### <a name="settings"></a>Nastavení

Nastavení, které zvolíte pro každý prostředek jsou důležité pro vytvoření úspěšného připojení.

Informace o jednotlivých prostředcích a nastaveních pro VPN gateway najdete v tématu [nastavení informace o službě VPN gateway pro Azure Stack](azure-stack-vpn-gateway-settings.md). Tento článek vám pomůže pochopit:

* Typy bran, typy sítí VPN a typy připojení.
* Podsítě brány, místní síťové brány a další nastavení prostředků, které můžete chtít zvážit.

### <a name="deployment-tools"></a>Nástroje pro nasazení

Můžete vytvořit a nakonfigurovat prostředky pomocí konfiguračního nástroje, jako je například na webu Azure portal. Později můžete přepnout na jiný nástroj, jako je PowerShell pro konfiguraci dalších prostředků nebo úpravám stávajících prostředků. V současné době nelze konfigurovat všechny prostředky a nastavení prostředku na webu Azure Portal. Pokyny v článcích pro každou topologii připojení určují, kdy je zapotřebí specifický konfigurační nástroj.

## <a name="connection-topology-diagrams"></a>Diagramy topologie připojení

K dispozici různé konfigurace pro připojení brány VPN. Určete, která konfigurace bude nejlépe vyhovovat vašim potřebám. V následujících částech si můžete zobrazit diagramy topologie a informace o následujících připojení brány VPN:

* dostupný model nasazení,
* dostupné konfigurační nástroje,
* odkazy na příslušný článek, pokud existuje.

Diagramy a popisy v následujících částech můžete výběrem topologie připojení tak, aby odpovídaly vašim požadavkům. Diagramy popisují základní topologie, ale je možné vytvořit komplexnější konfigurace s použitím diagramů jako vodítko.

## <a name="site-to-site-and-multi-site-ipsecike-vpn-tunnel"></a>Site-to-site a multi-Site (tunel VPN IPsec/IKE)

### <a name="site-to-site"></a>Site-to-Site

A *site-to-site* (S2S) připojení brány VPN je připojení přes tunel VPN IPsec/IKE (IKEv1 nebo IKEv2). Tento typ připojení vyžaduje zařízení VPN, které se nacházejí na místních a je přiřazena veřejná IP adresa. Toto zařízení nesmí být umístěné za službou NAT. Připojení S2S můžete použít pro konfigurace mezi různými místy a pro hybridní konfigurace.

![Příklad konfigurace připojení Site-to-site VPN](media/azure-stack-vpn-gateway-about-vpn-gateways/vpngateway-site-to-site-connection-diagram.png)

### <a name="multi-site"></a>Multi-Site

A *Multi-Site* připojení je variací připojení site-to-site. Z brány virtuální sítě vytvoříte několik připojení VPN, obvykle pro připojení k několika místním lokalitám. Při práci s více připojeními, musíte použít (označované jako dynamická brána při práci s klasickými virtuálními sítěmi) typ sítě VPN založené na směrování. Vzhledem k tomu, že virtuální síť může mít jenom jednu bránu virtuální sítě, všechna připojení prostřednictvím brány sdílejí dostupnou šířku pásma.

![Příklad propojení Multi-Site pomocí Azure VPN Gateway](media/azure-stack-vpn-gateway-about-vpn-gateways/vpngateway-multisite-connection-diagram.png)

## <a name="gateway-skus"></a>Skladové jednotky (SKU) brány

Při vytváření brány virtuální sítě pro službu Azure Stack určit SKU brány, kterou chcete použít. Podporují se tyto skladové položky brány sítě VPN:

* Basic
* Standard
* High Performance

Když vyberete vyšší SKU, jako je například standardní přes Basic nebo vysoce výkonné brány přes Standard nebo Basic, jsou přiděleny víc procesorů a šířky pásma sítě k bráně. V důsledku toho může brána podporovat vyšší propustnost sítě k virtuální síti.

Azure Stack nepodporuje Ultra výkon brány SKU, která se používá výhradně službou Express Route.

Při výběru SKU zvažte následující:

* Azure Stack brány založené na zásadách nepodporuje.
* Protokol BGP (Border Gateway) není podporován v základní SKU.
* Konfigurace současně existujících brány ExpressRoute VPN nejsou podporovány ve službě Azure Stack.
* Připojení brány VPN typu S2S aktivní aktivní je možné nakonfigurovat na SKU pro vysokou dostupnost pouze.

## <a name="estimated-aggregate-throughput-by-sku"></a>Odhadovaná agregovaná propustnost podle typů SKU

Následující tabulka ukazuje typy brány a odhadovanou agregovanou propustnost podle SKU brány:

|   | Propustnost brány sítě VPN *(1)* | Tunelových propojení IPsec brány sítě VPN maximální *(2)* |
|-------|-------|-------|
|**Základní SKU** ***(3)***    | 100 Mb/s  | 20    |
|**Standardní SKU**       | 100 Mb/s  | 20    |
|**SKU pro vysoký výkon** | 200 Mb/s    | 10    |

**Poznámky k tabulce:**

*Poznámka: (1)* -propustnosti sítě VPN není zaručena propustnost pro připojení mezi místními systémy přes Internet. Jedná se o maximální možné měření propustnosti.  
*Poznámka: [2]* – maximální počet tunelových propojení je celkem jedno nasazení služby Azure Stack pro všechna předplatná.  
*Poznámka: [3]* – směrování protokolu BGP není podporován pro základní SKU.

>[!NOTE]
>Lze vytvořit pouze jedno připojení site-to-site VPN mezi dvěma nasazeními Azure Stack. Toto je kvůli omezením na platformě, která povoluje jenom jedno připojení sítě VPN pro stejnou IP adresu. Protože Azure Stack využívá víceklientská brána, který používá jednu veřejnou IP adresu pro všechny brány sítě VPN v systému Azure Stack, může existovat pouze jedno připojení VPN mezi dvěma systémy Azure Stack. Toto omezení platí také pro připojení více než jednoho připojení sítě VPN site-to-site VPN gateway, která používá jedna IP adresa. Azure Stack není povolena více než jeden prostředek brány místní sítě má být vytvořen pomocí stejné IP adresy.

## <a name="next-steps"></a>Další postup

[Konfigurace nastavení služby VPN gateway pro Azure Stack](azure-stack-vpn-gateway-settings.md)
