---
title: Připojení soukromě k webové aplikaci pomocí privátního koncového bodu Azure
description: Připojení soukromě k webové aplikaci pomocí privátního koncového bodu Azure
author: ericgre
ms.assetid: 2dceac28-1ba6-4904-a15d-9e91d5ee162c
ms.topic: article
ms.date: 03/12/2020
ms.author: ericg
ms.service: app-service
ms.workload: web
ms.openlocfilehash: ad7e04d611129766fe9fb72285fe35ccfbb17626
ms.sourcegitcommit: 05a650752e9346b9836fe3ba275181369bd94cf0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/12/2020
ms.locfileid: "79136668"
---
# <a name="using-private-endpoints-for-azure-web-app-preview"></a>Používání privátních koncových bodů pro webovou aplikaci Azure (Preview)

Pro webovou aplikaci Azure můžete použít privátní koncový bod, který umožňuje klientům umístěným ve vaší privátní síti zabezpečený přístup k aplikaci prostřednictvím privátního propojení. Privátní koncový bod používá IP adresu z adresního prostoru virtuální sítě Azure. Síťový provoz mezi klientem v privátní síti a webovou aplikací prochází přes virtuální síť a privátní odkaz na páteřní síti Microsoftu, což eliminuje expozici veřejného Internetu.

Použití privátního koncového bodu pro vaši webovou aplikaci vám umožní:

- Zabezpečte svou webovou aplikaci konfigurací koncového bodu služby a odstraněním veřejné expozice
- Připojte se bezpečně k webové aplikaci z místních sítí, které se připojují k virtuální síti pomocí privátního partnerského vztahu VPN nebo ExpressRoute.

Pokud potřebujete zabezpečené připojení mezi vaší virtuální sítí a webovou aplikací, je koncový bod služby nejjednodušším řešením. Pokud budete také potřebovat připojit se k webové aplikaci z místního prostředí prostřednictvím brány Azure, místní sítě s partnerským vztahem nebo globálně připojenou virtuální sítí, jedná se o řešení privátního koncového bodu.  

Další informace o [koncovém bodu služby][serviceendpoint]

## <a name="conceptual-overview"></a>Koncepční přehled

Privátní koncový bod je speciální síťové rozhraní (nic) pro vaši webovou aplikaci Azure ve vaší podsíti ve vaší Virtual Network (virtuální síť).
Když vytvoříte privátní koncový bod pro webovou aplikaci, poskytuje zabezpečené připojení mezi klienty ve vaší privátní síti a vaší webovou aplikací. Privátnímu koncovému bodu je přiřazena IP adresa z rozsahu IP adres vaší virtuální sítě.
Připojení mezi soukromým koncovým bodem a webovou aplikací používá zabezpečený [privátní odkaz][privatelink]. Privátní koncový bod se používá jenom pro příchozí toky do vaší webové aplikace. Odchozí toky nepoužívají tento privátní koncový bod, ale můžete do sítě vložit odchozí toky v jiné podsíti prostřednictvím [funkce integrace virtuální][vnetintegrationfeature]sítě.

Podsíť, ve které zapojujete soukromý koncový bod, může mít v sobě jiné prostředky, a proto nebudete potřebovat vyhrazenou prázdnou podsíť.
Privátní koncový bod můžete nasadit v jiné oblasti, než je webová aplikace. 

> [!Note]
>Funkce integrace virtuální sítě nemůže používat stejnou podsíť než soukromý koncový bod, což je omezení funkce integrace virtuální sítě.

Z hlediska zabezpečení:

- Když pro webovou aplikaci povolíte koncový bod služby, zakážete tím veškerý veřejný přístup.
- Můžete povolit více privátních koncových bodů v jiných virtuální sítě a podsítích, včetně virtuální sítě v jiných oblastech.
- IP adresa privátního koncového bodu musí být dynamická, avšak zůstane stejná, dokud neodstraníte soukromý koncový bod.
- K síťovému rozhraní privátního koncového bodu se nedá přidružit NSG.
- K podsíti, která je hostitelem privátního koncového bodu, může být přidruženo NSG, ale je nutné zakázat vynucování zásad sítě pro soukromý koncový bod v [tomto článku][disablesecuritype]. V důsledku toho nebudete moct filtrovat podle NSG přístupu k privátnímu koncovému bodu.
- Když pro webovou aplikaci povolíte privátní koncový bod, konfigurace [omezení přístupu][accessrestrictions] webové aplikace se nevyhodnotí.
- Odstraněním všech pravidel NSG, kde destination je tag Internet nebo služby Azure, můžete snížit riziko exfiltrace dat z virtuální sítě. Ale přidání koncového bodu Web App Service do vaší podsítě vám umožní získat přístup k jakékoli webové aplikaci hostované ve stejném razítku a zpřístupnit ji pro Internet.

Privátní koncový bod pro webovou aplikaci je dostupný pro vrstvu PremiumV2 a izolovaný s externím pomocným mechanismem.

V protokolech HTTP webové aplikace se nachází zdrojová IP adresa klienta. Implementovali jsme protokol proxy protokolu TCP a předávali jsme webové aplikaci vlastnost IP adresy klienta. Další informace najdete v [tomto článku][tcpproxy].

![Globální přehled][1]


## <a name="dns"></a>DNS

Vzhledem k tomu, že tato funkce je ve verzi Preview, nezměníme položku DNS během verze Preview. Je potřeba spravovat položku DNS na privátním serveru DNS nebo Azure DNS privátní zóně. Pokud potřebujete použít vlastní název DNS, musíte do své webové aplikace přidat vlastní název. Ve verzi Preview se vlastní název musí ověřit jako libovolný vlastní název, a to pomocí veřejného překladu názvů DNS. [vlastní technické informace o ověřování DNS][dnsvalidation]

## <a name="pricing"></a>Ceny

Podrobnosti o cenách najdete v tématu [ceny za privátní propojení Azure][pricing].

## <a name="limitations"></a>Omezení

Pravidelně vylepšujeme funkci privátního propojení a soukromý koncový bod. Další informace o omezeních najdete v [tomto článku][pllimitations] .

## <a name="next-steps"></a>Další kroky

Nasazení privátního koncového bodu pro webovou aplikaci prostřednictvím portálu najdete v tématu [jak se připojit soukromě k webové aplikaci][howtoguide] .


<!--Image references-->
[1]: ./media/private-endpoint/schemaglobaloverview.png

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
