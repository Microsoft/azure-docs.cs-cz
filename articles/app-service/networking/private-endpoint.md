---
title: Připojení soukromě k webové aplikaci Azure pomocí privátního koncového bodu
description: Připojení soukromě k webové aplikaci pomocí privátního koncového bodu Azure
author: ericgre
ms.assetid: 2dceac28-1ba6-4904-a15d-9e91d5ee162c
ms.topic: article
ms.date: 08/12/2020
ms.author: ericg
ms.service: app-service
ms.workload: web
ms.custom: fasttrack-edit, references_regions
ms.openlocfilehash: 9f593bd5e1d4970b43b25c434abfa87177b72066
ms.sourcegitcommit: a07a01afc9bffa0582519b57aa4967d27adcf91a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "91743008"
---
# <a name="using-private-endpoints-for-azure-web-app-preview"></a>Používání privátních koncových bodů pro webovou aplikaci Azure (Preview)

> [!Note]
> S aktualizací Preview jsme vydali funkci data exfiltrace Protection.
>
> Verze Preview je dostupná ve všech veřejných oblastech pro PremiumV2 a PremiumV3 aplikace pro Windows a Linux a elastické funkce Premium. 

Pro webovou aplikaci Azure můžete použít privátní koncový bod, který umožňuje klientům umístěným ve vaší privátní síti zabezpečený přístup k aplikaci prostřednictvím privátního propojení. Privátní koncový bod používá IP adresu z adresního prostoru virtuální sítě Azure. Síťový provoz mezi klientem v privátní síti a webovou aplikací prochází přes virtuální síť a privátní odkaz na páteřní síti Microsoftu, což eliminuje expozici veřejného Internetu.

Použití privátního koncového bodu pro vaši webovou aplikaci vám umožní:

- Zabezpečte svou webovou aplikaci konfigurací privátního koncového bodu, čímž Eliminujte veřejnou expozici.
- Připojte se bezpečně k webové aplikaci z místních sítí, které se připojují k virtuální síti pomocí privátního partnerského vztahu VPN nebo ExpressRoute.
- Vyhněte se jakýmkoli exfiltrace dat z vaší virtuální sítě. 

Pokud potřebujete zabezpečené připojení mezi vaší virtuální sítí a vaší webovou aplikací, je koncový bod služby nejjednodušší řešení. Pokud budete také potřebovat připojit se k webové aplikaci z místního prostředí prostřednictvím brány Azure, místní sítě s partnerským vztahem nebo globálně připojenou sítí, soukromý koncový bod je řešením.  

Další informace najdete v tématu [koncové body služby][serviceendpoint].

## <a name="conceptual-overview"></a>Základní přehled

Privátní koncový bod je speciální síťové rozhraní (NIC) pro webovou aplikaci Azure v podsíti ve vaší Virtual Network (virtuální síť).
Při vytváření privátního koncového bodu pro webovou aplikaci poskytuje zabezpečené připojení mezi klienty v privátní síti a vaší webovou aplikací. Privátnímu koncovému bodu je přiřazena IP adresa z rozsahu IP adres vaší virtuální sítě.
Připojení mezi soukromým koncovým bodem a webovou aplikací používá zabezpečený [privátní odkaz][privatelink]. Privátní koncový bod se používá jenom pro příchozí toky do vaší webové aplikace. Odchozí toky nepoužívají tento privátní koncový bod, ale můžete do sítě vložit odchozí toky v jiné podsíti prostřednictvím [funkce integrace virtuální][vnetintegrationfeature]sítě.

Podsíť, ve které zapojujete soukromý koncový bod, může mít v sobě jiné prostředky, a proto nebudete potřebovat vyhrazenou prázdnou podsíť.
Privátní koncový bod můžete nasadit i v jiné oblasti, než je webová aplikace. 

> [!Note]
>Funkce integrace virtuální sítě nemůže používat stejnou podsíť jako privátní koncový bod. Jedná se o omezení funkce integrace virtuální sítě.

Z hlediska zabezpečení:

- Když u webové aplikace povolíte soukromé koncové body, zakážete tím veškerý veřejný přístup.
- Můžete povolit více privátních koncových bodů v jiných virtuální sítě a podsítích, včetně virtuální sítě v jiných oblastech.
- IP adresa privátního koncového bodu musí být dynamická, ale zůstane stejná, dokud neodstraníte soukromý koncový bod.
- K síťovému rozhraní privátního koncového bodu nelze přidružit NSG.
- K podsíti, která je hostitelem privátního koncového bodu, může být přidruženo NSG, ale je nutné zakázat vynucování zásad sítě pro privátní koncový bod: viz [zakázání zásad sítě pro privátní koncové body][disablesecuritype]. V důsledku toho není možné filtrovat podle NSG přístup k vašemu privátnímu koncovému bodu.
- Když pro webovou aplikaci povolíte privátní koncový bod, konfigurace [omezení přístupu][accessrestrictions] webové aplikace se nevyhodnotí.
- Odstraněním všech pravidel NSG, kde destination je tag Internet nebo služby Azure, můžete eliminovat riziko exfiltrace dat z virtuální sítě. Při nasazení privátního koncového bodu pro webovou aplikaci můžete dosáhnout pouze této konkrétní webové aplikace prostřednictvím privátního koncového bodu. Pokud máte jinou webovou aplikaci, musíte pro tuto jinou webovou aplikaci nasadit jiný vyhrazený privátní koncový bod.

V protokolech HTTP webové aplikace se nachází zdrojová IP adresa klienta. Tato funkce je implementovaná pomocí protokolu proxy protokolu TCP, který předává vlastnost IP adresy klienta do webové aplikace. Další informace najdete v tématu [získání informací o připojení pomocí proxy serveru TCP v2][tcpproxy].


  > [!div class="mx-imgBorder"]
  > ![Globální přehled privátního koncového bodu webové aplikace](media/private-endpoint/global-schema-web-app.png)

## <a name="dns"></a>DNS

Pokud pro webovou aplikaci použijete privátní koncový bod, požadovaná adresa URL se musí shodovat s názvem vaší webové aplikace. Ve výchozím nastavení mywebappname.azurewebsites.net.

Ve výchozím nastavení bez privátního koncového bodu je veřejný název vaší webové aplikace kanonický název clusteru.
Například překlad názvů bude:

|Název |Typ |Hodnota |
|-----|-----|------|
|mywebapp.azurewebsites.net|CNAME|clustername.azurewebsites.windows.net|
|clustername.azurewebsites.windows.net|CNAME|cloudservicename.cloudapp.net|
|cloudservicename.cloudapp.net|A|40.122.110.154| 


Při nasazení privátního koncového bodu aktualizujeme položku DNS tak, aby odkazovala na kanonický název mywebapp.privatelink.azurewebsites.net.
Například překlad názvů bude:

|Název |Typ |Hodnota |Přeznačit |
|-----|-----|------|-------|
|mywebapp.azurewebsites.net|CNAME|mywebapp.privatelink.azurewebsites.net|
|mywebapp.privatelink.azurewebsites.net|CNAME|clustername.azurewebsites.windows.net|
|clustername.azurewebsites.windows.net|CNAME|cloudservicename.cloudapp.net|
|cloudservicename.cloudapp.net|A|40.122.110.154|< – tato veřejná IP adresa není vaším privátním koncovým bodem. zobrazí se Chyba 403.|

Musíte nastavit privátní server DNS nebo privátní zónu Azure DNS, pro testy můžete změnit položku hostitele testovacího počítače.
Zóna DNS, kterou potřebujete vytvořit, je: **privatelink.azurewebsites.NET**. Zaregistrujte si záznam pro vaši webovou aplikaci pomocí záznamu a a IP adresy privátního koncového bodu.
Například překlad názvů bude:

|Název |Typ |Hodnota |Přeznačit |
|-----|-----|------|-------|
|mywebapp.azurewebsites.net|CNAME|mywebapp.privatelink.azurewebsites.net|
|mywebapp.privatelink.azurewebsites.net|A|10.10.10.8|< – tuto položku můžete spravovat v systému DNS, aby odkazovala na IP adresu privátního koncového bodu.|

Po této konfiguraci DNS se můžete připojit k webové aplikaci soukromě s výchozím názvem mywebappname.azurewebsites.net.


Pokud potřebujete použít vlastní název DNS, musíte do své webové aplikace přidat vlastní název. Ve verzi Preview se vlastní název musí ověřit jako libovolný vlastní název, a to pomocí veřejného překladu názvů DNS. Další informace najdete v tématu [vlastní ověření DNS][dnsvalidation].

V případě konzoly Kudu nebo Kudu REST API (například nasazení pomocí samoobslužných agentů Azure DevOps) musíte vytvořit dva záznamy v privátní zóně Azure DNS nebo ve vlastním serveru DNS. 

| Název | Typ | Hodnota |
|-----|-----|-----|
| mywebapp.privatelink.azurewebsites.net | A | PrivateEndpointIP | 
| mywebapp.scm.privatelink.azurewebsites.net | A | PrivateEndpointIP | 



## <a name="pricing"></a>Ceny

Podrobnosti o cenách najdete v tématu [ceny za privátní propojení Azure][pricing].

## <a name="limitations"></a>Omezení

Když použijete funkci Azure v plánu elastické Premium s privátním koncovým bodem, spustíte nebo spustíte funkci na webovém portálu Azure, musíte mít přímý přístup k síti nebo se zobrazí chyba HTTP 403. Jinými slovy, váš prohlížeč musí být schopný spojit se s privátním koncovým bodem a spustit funkci z webového portálu Azure. 

Během období Preview se za soukromým koncovým bodem zveřejňuje jenom produkční slot, ale v rámci veřejného koncového bodu musí být dostupné jiné sloty.

Pravidelně vylepšujeme funkci privátního propojení a soukromý koncový bod. Další informace o omezeních najdete v [tomto článku][pllimitations] .

## <a name="next-steps"></a>Další kroky

- Postup nasazení privátního koncového bodu pro webovou aplikaci prostřednictvím portálu najdete v tématu [Jak připojit soukromě k webové aplikaci pomocí portálu][howtoguide1] .
- Postup nasazení privátního koncového bodu pro webovou aplikaci pomocí rozhraní příkazového řádku Azure najdete v tématu [Jak připojit soukromě k webové aplikaci pomocí Azure CLI][howtoguide2] .
- Postup nasazení privátního koncového bodu pro webovou aplikaci pomocí prostředí PowerShell najdete v tématu [Postup připojení soukromě k webové aplikaci pomocí prostředí PowerShell][howtoguide3] .
- Postup nasazení privátního koncového bodu pro webovou aplikaci pomocí šablony Azure najdete v tématu [Jak připojit soukromě k webové aplikaci pomocí šablony Azure][howtoguide4] .
- Komplexní příklad, jak připojit front-end webovou aplikaci k zabezpečené webové aplikaci pomocí injektáže virtuální sítě a privátního koncového bodu se šablonou ARM, najdete v tomto [rychlém][howtoguide5] startu.
- Komplexní příklad, jak připojit front-end webovou aplikaci k zabezpečené webové aplikaci pomocí injektáže virtuální sítě a privátního koncového bodu s terraformu, najdete v této [ukázce][howtoguide6]


<!--Links-->
[serviceendpoint]: ../../virtual-network/virtual-network-service-endpoints-overview.md
[privatelink]: ../../private-link/private-link-overview.md
[vnetintegrationfeature]: ../web-sites-integrate-with-vnet.md
[disablesecuritype]: ../../private-link/disable-private-endpoint-network-policy.md
[accessrestrictions]: ../app-service-ip-restrictions.md
[tcpproxy]: ../../private-link/private-link-service-overview.md#getting-connection-information-using-tcp-proxy-v2
[dnsvalidation]: ../app-service-web-tutorial-custom-domain.md
[pllimitations]: ../../private-link/private-endpoint-overview.md#limitations
[pricing]: https://azure.microsoft.com/pricing/details/private-link/
[howtoguide1]: ../../private-link/create-private-endpoint-webapp-portal.md
[howtoguide2]: ../scripts/cli-deploy-privateendpoint.md
[howtoguide3]: ../scripts/powershell-deploy-private-endpoint.md
[howtoguide4]: ../scripts/template-deploy-private-endpoint.md
[howtoguide5]: https://github.com/Azure/azure-quickstart-templates/tree/master/101-webapp-privateendpoint-vnet-injection
[howtoguide6]: ../scripts/terraform-secure-backend-frontend.md