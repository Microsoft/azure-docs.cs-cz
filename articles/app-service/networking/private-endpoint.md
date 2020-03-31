---
title: Soukromé připojení k webové aplikaci pomocí Azure Private Endpoint
description: Soukromé připojení k webové aplikaci pomocí Azure Private Endpoint
author: ericgre
ms.assetid: 2dceac28-1ba6-4904-a15d-9e91d5ee162c
ms.topic: article
ms.date: 03/18/2020
ms.author: ericg
ms.service: app-service
ms.workload: web
ms.custom: fasttrack-edit
ms.openlocfilehash: c2717b1f29af39c6fdc4602b11acba131d959f03
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79534384"
---
# <a name="using-private-endpoints-for-azure-web-app-preview"></a>Použití privátních koncových bodů pro Azure Web App (preview)

> [!Note]
> Náhled je k dispozici v oblastech –USA a USA – východ 2 pro všechny prémiové windows a linuxové webové aplikace a elastické prémiové funkce. 

Privátní koncový bod pro Azure Web App můžete použít k tomu, abyste klientům umístěným v privátní síti umožnili bezpečný přístup k aplikaci přes privátní odkaz. Privátní koncový bod používá IP adresu z adresního prostoru virtuální sítě Azure. Síťový provoz mezi klientem v privátní síti a webovou aplikací prochází přes virtuální síť a privátní odkaz v páteřní síti Microsoftu, což eliminuje vystavení veřejné síti Internet.

Použití privátního koncového bodu pro webovou aplikaci umožňuje:

- Zabezpečte webovou aplikaci konfigurací koncového bodu služby a eliminací veřejné expozice.
- Bezpečně se připojte k Webové aplikaci z místních sítí, které se připojují k virtuální síti pomocí privátního partnerského vztahu VPN nebo ExpressRoute.

Pokud potřebujete zabezpečené připojení mezi virtuální sítí a webovou aplikací, koncový bod služby je nejjednodušší řešení. Pokud taky potřebujete oslovit webovou aplikaci z místního prostředí prostřednictvím brány Azure, globálně partnerské virtuální sítě nebo globálně partnerské virtuální sítě, privátní koncový bod je řešení.  

Další informace naleznete v [tématu Service Endpoints][serviceendpoint].

## <a name="conceptual-overview"></a>Koncepční přehled

Privátní koncový bod je speciální síťové rozhraní (NIC) pro vaši Azure Web App v podsíti ve vaší virtuální síti (VNet).
Když vytvoříte privátní koncový bod pro webovou aplikaci, poskytuje zabezpečené připojení mezi klienty v privátní síti a webovou aplikací. Privátní koncový bod je přiřazena IP adresa z rozsahu IP adres vaší virtuální sítě.
Připojení mezi privátním koncovým bodem a webovou aplikací používá zabezpečené [soukromé propojení][privatelink]. Privátní koncový bod se používá pouze pro příchozí toky do webové aplikace. Odchozí toky nebudou používat tento soukromý koncový bod, ale můžete vložit odchozí toky do vaší sítě v jiné podsíti prostřednictvím [funkce integrace virtuální sítě][vnetintegrationfeature].

Podsíť, do které připojíte soukromý koncový bod, může mít v sobě další prostředky, nepotřebujete vyhrazenou prázdnou podsíť.
Soukromý koncový bod můžete také nasadit v jiné oblasti než webová aplikace. 

> [!Note]
>Funkce integrace virtuální sítě nemůže používat stejnou podsíť jako private endpoint, jedná se o omezení funkce integrace virtuální sítě.

Z hlediska bezpečnosti:

- Když do webové aplikace povolíte soukromé koncové body, zakážete veškerý veřejný přístup.
- Můžete povolit více soukromých koncových bodů v jiných virtuálních sítích a podsítích, včetně virtuálních sítí v jiných oblastech.
- IP adresa privátní koncové ho nic musí být dynamická, ale zůstane stejná, dokud neodstraníte soukromý koncový bod.
- Nic soukromékoncového bodu nemůže mít nsg přidružené.
- Podsíť, která je hostitelem privátního koncového bodu, může mít přidružený soubor NSG, ale je nutné zakázat vynucení zásad sítě pro privátní koncový bod: viz [Zakázání síťových zásad pro privátní koncové body][disablesecuritype]. V důsledku toho nelze filtrovat podle žádné skupiny nsg přístup k privátní koncový bod.
- Když do webové aplikace povolíte privátní koncový bod, nebude vyhodnocena konfigurace [omezení přístupu][accessrestrictions] webové aplikace.
- Riziko exfiltrace dat z virtuální sítě můžete snížit odebráním všech pravidel sítě zabezpečení sítě, kde je cíl značky Internet nebo služby Azure. Přidání soukromého koncového bodu Webové aplikace do podsítě vám však umožní dosáhnout libovolné webové aplikace hostované ve stejném razítku nasazení a vystavené internetu.

V protokolech webového HTTP webové aplikace najdete zdrojovou IP adresu klienta. To je implementováno pomocí protokolu TCP Proxy, předávání vlastnosti IP klienta až do webové aplikace. Další informace naleznete [v tématu Získání informací o připojení pomocí protokolu TCP Proxy v2][tcpproxy].


  > [!div class="mx-imgBorder"]
  > ![Globální přehled privátního koncového bodu Webové aplikace](media/private-endpoint/global-schema-web-app.png)

## <a name="dns"></a>DNS

Vzhledem k tomu, že tato funkce je ve verzi Preview, nezměníme položku DNS během náhledu. Položku DNS musíte spravovat na privátním DNS serveru nebo privátní zóně Azure DNS sami.
Pokud potřebujete použít vlastní název DNS, musíte do webové aplikace přidat vlastní název. Během náhledu musí být vlastní název ověřen jako libovolný vlastní název pomocí veřejného překladu DNS. Další informace naleznete [v tématu vlastní ověření DNS.][dnsvalidation]

## <a name="pricing"></a>Ceny

Podrobnosti o cenách najdete v článku [Ceny Azure Private Link][pricing].

## <a name="limitations"></a>Omezení

Pravidelně vylepšujeme funkci private link a soukromý koncový bod, v [tomto článku][pllimitations] najdete aktuální informace o omezeních.

## <a name="next-steps"></a>Další kroky

Pokud chcete nasadit privátní koncový bod pro webovou aplikaci prostřednictvím portálu, přečtěte si, [jak se soukromě připojit k webové aplikaci.][howtoguide]




<!--Links-->
[serviceendpoint]: https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview
[privatelink]: https://docs.microsoft.com/azure/private-link/private-link-overview
[vnetintegrationfeature]: https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet
[disablesecuritype]: https://docs.microsoft.com/azure/private-link/disable-private-endpoint-network-policy
[accessrestrictions]: https://docs.microsoft.com/azure/app-service/app-service-ip-restrictions
[tcpproxy]: ../../private-link/private-link-service-overview.md#getting-connection-information-using-tcp-proxy-v2
[dnsvalidation]: https://docs.microsoft.com/azure/app-service/app-service-web-tutorial-custom-domain
[pllimitations]: https://docs.microsoft.com/azure/private-link/private-endpoint-overview#limitations
[pricing]: https://azure.microsoft.com/pricing/details/private-link/
[howtoguide]: https://docs.microsoft.com/azure/private-link/create-private-endpoint-webapp-portal
