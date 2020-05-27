---
title: Připojení soukromě k webové aplikaci pomocí privátního koncového bodu Azure
description: Připojení soukromě k webové aplikaci pomocí privátního koncového bodu Azure
author: ericgre
ms.assetid: 2dceac28-1ba6-4904-a15d-9e91d5ee162c
ms.topic: article
ms.date: 05/25/2020
ms.author: ericg
ms.service: app-service
ms.workload: web
ms.custom: fasttrack-edit
ms.openlocfilehash: 4c48a2fad927812cc45543243b48a2df81acf73b
ms.sourcegitcommit: 1f25aa993c38b37472cf8a0359bc6f0bf97b6784
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/26/2020
ms.locfileid: "83846949"
---
# <a name="using-private-endpoints-for-azure-web-app-preview"></a>Používání privátních koncových bodů pro webovou aplikaci Azure (Preview)

> [!Note]
> S aktualizací Preview jsme vydali funkci data exfiltrace Protection.
> Verze Preview je dostupná v oblastech Východní USA a Západní USA 2 pro všechny PremiumV2 Windows a Linux Web Apps a elastické funkce Premium. 

Pro webovou aplikaci Azure můžete použít privátní koncový bod, který umožňuje klientům umístěným ve vaší privátní síti zabezpečený přístup k aplikaci prostřednictvím privátního propojení. Privátní koncový bod používá IP adresu z adresního prostoru virtuální sítě Azure. Síťový provoz mezi klientem v privátní síti a webovou aplikací prochází přes virtuální síť a privátní odkaz na páteřní síti Microsoftu, což eliminuje expozici veřejného Internetu.

Použití privátního koncového bodu pro vaši webovou aplikaci vám umožní:

- Zabezpečte svou webovou aplikaci konfigurací privátního koncového bodu, čímž Eliminujte veřejnou expozici.
- Připojte se bezpečně k webové aplikaci z místních sítí, které se připojují k virtuální síti pomocí privátního partnerského vztahu VPN nebo ExpressRoute.
- Vyhněte se jakýmkoli exfiltrace dat z vaší virtuální sítě. 

Pokud potřebujete zabezpečené připojení mezi vaší virtuální sítí a vaší webovou aplikací, je koncový bod služby nejjednodušší řešení. Pokud budete také potřebovat připojit se k webové aplikaci z místního prostředí prostřednictvím brány Azure, místní sítě s partnerským vztahem nebo globálně připojenou sítí, soukromý koncový bod je řešením.  

Další informace najdete v tématu [koncové body služby][serviceendpoint].

## <a name="conceptual-overview"></a>Koncepční přehled

Privátní koncový bod je speciální síťové rozhraní (NIC) pro webovou aplikaci Azure v podsíti ve vaší Virtual Network (virtuální síť).
Při vytváření privátního koncového bodu pro webovou aplikaci poskytuje zabezpečené připojení mezi klienty v privátní síti a vaší webovou aplikací. Privátnímu koncovému bodu je přiřazena IP adresa z rozsahu IP adres vaší virtuální sítě.
Připojení mezi soukromým koncovým bodem a webovou aplikací používá zabezpečený [privátní odkaz][privatelink]. Privátní koncový bod se používá jenom pro příchozí toky do vaší webové aplikace. Odchozí toky nepoužívají tento privátní koncový bod, ale můžete do sítě vložit odchozí toky v jiné podsíti prostřednictvím [funkce integrace virtuální][vnetintegrationfeature]sítě.

Podsíť, ve které zapojujete soukromý koncový bod, může mít v sobě jiné prostředky, a proto nebudete potřebovat vyhrazenou prázdnou podsíť.
Privátní koncový bod můžete nasadit i v jiné oblasti, než je webová aplikace. 

> [!Note]
>Funkce integrace virtuální sítě nemůže používat stejnou podsíť než soukromý koncový bod. Jedná se o omezení funkce integrace virtuální sítě.

Z hlediska zabezpečení:

- Když u webové aplikace povolíte soukromé koncové body, zakážete tím veškerý veřejný přístup.
- Můžete povolit více privátních koncových bodů v jiných virtuální sítě a podsítích, včetně virtuální sítě v jiných oblastech.
- IP adresa privátního koncového bodu musí být dynamická, ale zůstane stejná, dokud neodstraníte soukromý koncový bod.
- K síťovému rozhraní privátního koncového bodu nelze přidružit NSG.
- K podsíti, která je hostitelem privátního koncového bodu, může být přidruženo NSG, ale je nutné zakázat vynucování zásad sítě pro privátní koncový bod: viz [zakázání zásad sítě pro privátní koncové body][disablesecuritype]. V důsledku toho není možné filtrovat podle NSG přístup k vašemu privátnímu koncovému bodu.
- Když pro webovou aplikaci povolíte privátní koncový bod, konfigurace [omezení přístupu][accessrestrictions] webové aplikace se nevyhodnotí.
- Odstraněním všech pravidel NSG, kde destination je tag Internet nebo služby Azure, můžete eliminovat riziko exfiltrace dat z virtuální sítě. Při nasazení privátního koncového bodu pro webovou aplikaci můžete dosáhnout pouze této konkrétní webové aplikace prostřednictvím privátního koncového bodu. Pokud máte jinou webovou aplikaci, musíte pro tuto jinou webovou aplikaci nasadit jiný vyhrazený privátní koncový bod.

V protokolech HTTP webové aplikace se nachází zdrojová IP adresa klienta. To je implementováno pomocí protokolu proxy protokolu TCP, který předává vlastnost IP klienta do webové aplikace. Další informace najdete v tématu [získání informací o připojení pomocí proxy serveru TCP v2][tcpproxy].


  > [!div class="mx-imgBorder"]
  > ![Globální přehled privátního koncového bodu webové aplikace](media/private-endpoint/global-schema-web-app.png)

## <a name="dns"></a>DNS

Vzhledem k tomu, že tato funkce je ve verzi Preview, nezměníme položku DNS během verze Preview. Je potřeba spravovat položku DNS na privátním serveru DNS nebo Azure DNS privátní zóny sami.
Pokud potřebujete použít vlastní název DNS, musíte do své webové aplikace přidat vlastní název. Ve verzi Preview se vlastní název musí ověřit jako libovolný vlastní název, a to pomocí veřejného překladu názvů DNS. Další informace najdete v tématu [vlastní ověření DNS][dnsvalidation].

Pokud potřebujete použít konzolu Kudu nebo Kudu REST API (například nasazení s agenty pro samoobslužné hostování Azure DevOps), musíte vytvořit dva záznamy v privátní zóně Azure DNS nebo ve vlastním serveru DNS. 
- PrivateEndpointIP yourwebappname.azurewebsites.net 
- PrivateEndpointIP yourwebappname.scm.azurewebsites.net 

## <a name="pricing"></a>Ceny

Podrobnosti o cenách najdete v tématu [ceny za privátní propojení Azure][pricing].

## <a name="limitations"></a>Omezení

Když použijete funkci Azure v plánu elastické Premium s privátním koncovým bodem, spustíte nebo spustíte funkci na webovém portálu Azure, musíte mít přímý přístup k síti nebo se zobrazí chyba HTTP 403. Jinými slovy, váš prohlížeč musí být schopný spojit se s privátním koncovým bodem a spustit funkci z webového portálu Azure. 

Během období Preview se za soukromým koncovým bodem zveřejňuje jenom produkční slot, ale v rámci veřejného koncového bodu musí být dostupné jiné sloty.

Pravidelně vylepšujeme funkci privátního propojení a soukromý koncový bod. Další informace o omezeních najdete v [tomto článku][pllimitations] .

## <a name="next-steps"></a>Další kroky

Nasazení privátního koncového bodu pro webovou aplikaci prostřednictvím portálu najdete v tématu [jak se připojit soukromě k webové aplikaci][howtoguide] .




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
