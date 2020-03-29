---
title: Integrace aplikace s virtuální sítí Azure
description: Integrujte aplikace ve službě Azure App Service s virtuálními sítěmi Azure.
author: ccompy
ms.assetid: 90bc6ec6-133d-4d87-a867-fcf77da75f5a
ms.topic: article
ms.date: 02/27/2020
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 89aa78e0d26598eacf436ca88cc6c5549f91d2fc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78673214"
---
# <a name="integrate-your-app-with-an-azure-virtual-network"></a>Integrace aplikace s virtuální sítí Azure
Tento dokument popisuje funkci integrace virtuální sítě služby Azure App Service a jak ji nastavit pomocí aplikací ve [službě Azure App Service](https://go.microsoft.com/fwlink/?LinkId=529714). [Virtuální sítě Azure][VNETOverview] (Virtuální sítě) umožňují umístit mnoho prostředků Azure do sítě, která není přístupná internetu.  

Služba Azure App Service má dvě varianty.

[!INCLUDE [app-service-web-vnet-types](../../includes/app-service-web-vnet-types.md)]

## <a name="enable-vnet-integration"></a>Povolení integrace virtuální sítě 

1. Přejděte na portál u síťového uznatí. V části Integrace virtuální sítě vyberte *"Kliknutím sem nakonfigurujete"*. 

1. Vyberte **Přidat virtuální síť**.  

   ![Vybrat integraci virtuální sítě][1]

1. Rozevírací seznam bude obsahovat všechny virtuální sítě Správce prostředků ve vašem předplatném ve stejné oblasti a pod ním, který je seznamem všech virtuálních sítí Správce prostředků ve všech ostatních oblastech. Vyberte virtuální síť, se kterou chcete integrovat.

   ![Výběr virtuální sítě][2]

   * Pokud je virtuální síť ve stejné oblasti, vytvořte novou podsíť nebo vyberte prázdnou již existující podsíť. 

   * Chcete-li vybrat virtuální síť v jiné oblasti, musíte mít bránu virtuální sítě zřízenou s povoleným bodem na web.

   * Pokud se chcete integrovat s klasickou virtuální sítí, místo kliknutí na rozevírací nabídku virtuální sítě vyberte **Kliknutím sem se připojíte ke klasické virtuální síti**. Vyberte požadovanou klasickou virtuální síť. Cílová virtuální síť už musí mít bránu virtuální sítě zřízenou s povoleným odkazem na místo.

    ![Vybrat klasickou virtuální síť][3]
    
Během integrace se aplikace restartuje.  Po dokončení integrace se zobrazí podrobnosti o virtuální síti, se kterou jste integrovaní. 

Jakmile je vaše aplikace integrovaná s vaší virtuální sítí, bude používat stejný server DNS, se kterým je vaše virtuální síť nakonfigurovaná, pokud se nejedná o privátní zóny Azure DNS. V současné době nelze použít integraci virtuální sítě s Privátními zónami Azure DNS.

## <a name="regional-vnet-integration"></a>Regionální integrace virtuální sítě

[!INCLUDE [app-service-web-vnet-types](../../includes/app-service-web-vnet-regional.md)]

### <a name="how-regional-vnet-integration-works"></a>Jak funguje regionální integrace virtuální sítě

Aplikace ve službě App Service jsou hostované na rolích pracovních rolí. Základní a vyšší cenové plány jsou vyhrazené hostingové plány, kde neexistují žádné jiné úlohy zákazníků běžící na stejných pracovnících. Regionální integrace virtuální sítě funguje tak, že připojuje te virtuální rozhraní s adresami v delegované podsíti. Vzhledem k tomu, že adresa od je ve vaší virtuální síti, může mít přístup k většině věcí ve virtuální síti nebo prostřednictvím vaší virtuální sítě, stejně jako by to udělal virtuální počítač ve vaší virtuální síti. Implementace sítě se liší od spuštění virtuálního počítače ve virtuální síti, a proto některé síťové funkce ještě nejsou k dispozici při používání této funkce.

![Jak funguje regionální integrace virtuální sítě][5]

Když je povolená regionální integrace virtuální sítě, vaše aplikace bude pořád volat odchozí volání na internet prostřednictvím stejných kanálů jako obvykle. Odchozí adresy, které jsou uvedeny na portálu vlastností aplikace, jsou stále adresy používané vaší aplikací. Jaké změny pro vaši aplikaci jsou, volání služeb koncový bod zabezpečené služby nebo RFC 1918 adresy přejde do vaší virtuální sítě. Pokud WEBSITE_VNET_ROUTE_ALL je nastavena na 1, pak všechny odchozí provoz lze odeslat do virtuální sítě. 

Funkce podporuje pouze jedno virtuální rozhraní na pracovníka.  Jedno virtuální rozhraní na pracovníka znamená jednu regionální integraci virtuální sítě na plán služby App Service. Všechny aplikace ve stejném plánu služby App Service můžete použít stejnou integraci virtuální sítě, ale pokud potřebujete aplikaci pro připojení k další virtuální síti, budete muset vytvořit jiný plán služby App Service. Použité virtuální rozhraní není prostředek, ke kterému mají zákazníci přímý přístup.

Vzhledem k povaze fungování této technologie se provoz, který se používá s integrací virtuální sítě, nezobrazuje v protokolech toku Sledování sítě nebo NSG.  

## <a name="gateway-required-vnet-integration"></a>Brána vyžaduje integraci virtuální sítě

Brána vyžaduje integraci virtuální sítě podporuje připojení k virtuální síti v jiné oblasti nebo ke klasické virtuální síti. Brána vyžaduje integraci virtuální sítě: 

* Umožňuje aplikaci připojit se k jenom 1 virtuální síti najednou.
* Umožňuje integraci až pěti virtuálních sítí v rámci plánu služby App Service 
* Umožňuje, aby stejnou virtuální síť používalo více aplikací v plánu služby App Service, aniž by to mělo vliv na celkový počet, který může použít plán služby App Service.  Pokud máte šest aplikací, které používají stejnou virtuální síť ve stejném plánu služby App Service, počítá se to jako 1 virtuální síť, která se používá. 
* Podporuje 99,9% SLA kvůli SLA na bráně
* Umožňuje aplikacím používat DNS, se kterým je virtuální síť nakonfigurovaná.
* Před připojením k aplikaci vyžaduje bránu založenou na směrování virtuální sítě nakonfigurovanou pomocí sítě VPN s bodem na lokalitu. 

Nemůžete použít bránu požadovanou integraci virtuální sítě:

* S linuxovými aplikacemi
* S virtuální sítí připojenou k ExpressRoute 
* Přístup k zabezpečeným prostředkům koncových bodů služby
* S bránou koexistence, která podporuje jak ExpressRoute, tak přejděte na web/lokalitu sítě VIRTUÁLNÍ sítě

### <a name="set-up-a-gateway-in-your-vnet"></a>Nastavení brány ve virtuální síti ###

Vytvoření brány:

1. [Vytvořte podsíť brány][creategatewaysubnet] ve virtuální síti.  

1. [Vytvořte bránu VPN][creategateway]. Vyberte typ VPN založený na trase.

1. [Nastavte bod na adresy webu][setp2saddresses]. Pokud brána není v základní skladové jednotce, pak IKEV2 musí být zakázánv konfiguraci bodu do lokality a musí být vybrán sstp. Adresní prostor z bodu na pracoviště musí být v adresních blocích RFC 1918, 10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16

Pokud právě vytváříte bránu pro použití s integrací virtuální sítě služby App Service, nemusíte nahrát certifikát. Vytvoření brány může trvat 30 minut. Dokud se brána nezřídí, nebude možné integrovat vaši aplikaci s vaší virtuální sítí. 

### <a name="how-gateway-required-vnet-integration-works"></a>Jak funguje požadovaná integrace virtuálnísítě

Brána vyžaduje integraci virtuální sítě postavenou na technologii VPN od místa k webu. Přejděte na sítě virtuálních sítí omezuje přístup k síti pouze na virtuální počítač hostující aplikaci. Aplikace jsou omezeny na odesílání provozu na internet, prostřednictvím hybridních připojení nebo prostřednictvím integrace virtuální sítě. Když je vaše aplikace nakonfigurovaná s portálem pro použití brány požadované integrace virtuální sítě, komplexní vyjednávání se spravuje vaším jménem k vytvoření a přiřazení certifikátů na bráně a na straně aplikace. Konečným výsledkem je, že pracovníci, kteří slouží k hostování vašich aplikací, se můžou přímo připojit k bráně virtuální sítě ve vybrané virtuální síti. 

![Jak funguje požadovaná integrace virtuálnísítě][6]

### <a name="accessing-on-premises-resources"></a>Přístup k místním prostředkům

Aplikace mohou přistupovat k místním prostředkům integrací s virtuálními sítěmi, které mají připojení k webu. Pokud používáte bránu požadovanou integraci virtuální sítě, musíte aktualizovat místní trasy brány VPN pomocí bloků adres point-to-site. Při prvním nastavení sítě VPN mezi lokalitami by měly skripty použité ke konfiguraci správně nastavit trasy. Pokud po vytvoření sítě VPN mezi lokalitami přidáte adresy point-to-site, je třeba trasy aktualizovat ručně. Podrobnosti o tom, jak to udělat, se liší podle brány a nejsou zde popsány. Protokol BGP nelze nakonfigurovat pomocí připojení VPN mezi lokalitami.

Neexistuje žádná další konfigurace požadovaná pro místní funkci integrace virtuální sítě dosáhnout prostřednictvím virtuální sítě a do místní. Jednoduše potřebujete připojit virtuální síť k místnímu prostředí pomocí ExpressRoute nebo sítě VPN mezi lokalitami. 

> [!NOTE]
> Funkce integrace virtuální sítě vyžaduje bránu neintegruje aplikaci s virtuální sítí, která má bránu ExpressRoute. I v případě, že je brána ExpressRoute nakonfigurovaná v [režimu koexistence,][VPNERCoex] integrace virtuální sítě nefunguje. Pokud potřebujete přístup k prostředkům prostřednictvím připojení ExpressRoute, můžete použít funkci integrace místní virtuální sítě nebo [prostředí služby App Service][ASE], které běží ve vaší virtuální síti. 
> 
> 

### <a name="peering"></a>Partnerské vztahy

Pokud používáte partnerský vztah s místní integrací virtuální sítě, není nutné provádět žádné další konfigurace. 

Pokud používáte bránu požadovanou integraci virtuální sítě s partnerským vztahem, musíte nakonfigurovat několik dalších položek. Konfigurace partnerského vztahu pro práci s vaší aplikací:

1. Přidejte připojení partnerského vztahu na virtuální síť, ke které se vaše aplikace připojí. Při přidávání připojení partnerského vztahu povolte **povolit přístup k virtuální síti** a **zaškrtněte políčko Povolit předávaný provoz** a **Povolit přenos brány**.
1. Přidejte připojení partnerského vztahu na virtuální síti, která je právě peered do virtuální sítě, které jsou připojeny. Při přidávání připojení partnerského vztahu na cílovou virtuální síť povolte **povolit přístup k virtuální síti** a **zaškrtněte políčko Povolit předávaný provoz** a **Povolit vzdálené brány**.
1. Přejděte na portálu na plán služby App Service > síťové > integračního rozhraní virtuální sítě.  Vyberte virtuální síť, ke které se vaše aplikace připojuje. V části směrování přidejte rozsah adres virtuální sítě, která je propojená s virtuální sítí, ke které je vaše aplikace připojená.  

## <a name="managing-vnet-integration"></a>Správa integrace virtuální sítě 

Připojení a odpojení pomocí virtuální sítě je na úrovni aplikace. Operace, které můžou ovlivnit integraci virtuální sítě napříč více aplikacemi, jsou na úrovni plánu služby App Service. Z aplikace > portálu pro integraci virtuální sítě > sítě můžete získat podrobnosti o vaší virtuální síti. Podobné informace můžete zobrazit na úrovni ASP na portálu ASP > Networking > Integrační portál virtuální sítě.

Jedinou operací, kterou můžete provést v zobrazení aplikace integrace virtuální sítě, je odpojit vaši aplikaci od virtuální sítě, ke které je aktuálně připojená. Pokud chcete aplikaci odpojit od virtuální sítě, vyberte **Odpojit**. Vaše aplikace se restartuje, když se odpojíte od virtuální sítě. Odpojení mne nezmění virtuální síť. Podsíť nebo brána není odebrána. Pokud pak chcete odstranit virtuální síť, musíte nejprve odpojit aplikaci od virtuální sítě a odstranit prostředky v ní, jako jsou brány. 

U rozhraní integrace virtuální sítě ASP vám ukáže všechny integrace virtuální sítě používané aplikacemi ve vašem prostředí ASP. Pokud chcete zobrazit podrobnosti o jednotlivých virtuálních sítích, klikněte na virtuální síť, která vás zajímá. Existují dvě akce, které můžete provést zde pro bránu požadované integrace virtuální sítě.

* **Synchronizovat síť**. Operace synchronizační sítě je jenom pro funkci integrace virtuální sítě závislá na bráně. Provedení operace synchronizační sítě zajišťuje synchronizaci certifikátů a informací o síti. Pokud přidáte nebo změníte DNS virtuální sítě, budete muset provést operaci **sítě Synchronizace.** Tato operace restartuje všechny aplikace pomocí této virtuální sítě.
* **Přidání tras** Přidání tras bude řídit odchozí provoz do virtuální sítě. 

**Směrování integrace virtuální sítě vyžaduje bránu.** Trasy, které jsou definované ve vaší virtuální síti se používají k přímému provozu do virtuální sítě z vaší aplikace. Pokud potřebujete odeslat další odchozí provoz do virtuální sítě, můžete přidat tyto bloky adres zde. Tato funkce funguje jenom s bránou požadované integrace virtuální sítě. Tabulky tras nemají vliv na provoz aplikací při použití brány požadované integrace virtuální sítě tak, jak to dělají s regionální integrací virtuální sítě.

**Brána požadované certifikáty integrace virtuální sítě** Pokud brána vyžaduje integraci virtuální sítě povoleno, je vyžadována výměna certifikátů k zajištění zabezpečení připojení. Spolu s certifikáty jsou konfigurace DNS, trasy a další podobné věci, které popisují síť.
Pokud dojde ke změně certifikátů nebo informací o síti, musíte kliknout na tlačítko Synchronizovat síť. Když kliknete na "Synchronizovat síť", způsobíte krátký výpadek připojení mezi vaší aplikací a virtuální sítí. I když vaše aplikace není restartována, ztráta připojení může způsobit, že váš web nebude fungovat správně. 

## <a name="pricing-details"></a>Podrobnosti o cenách
Funkce integrace místní virtuální sítě nemá žádné další poplatky za použití nad rámec poplatků cenové úrovně ASP.

Existují tři související poplatky za použití funkce požadované brány integrace virtuální sítě:

* Poplatky za cenovou úroveň ASP – vaše aplikace musí být v tarifu služby Standard, Premium nebo PremiumV2 App Service. Další podrobnosti o těchto nákladech naleznete zde: [Ceny služby App Service][ASPricing]. 
* Náklady na přenos dat – za odchozí data se účtuje poplatek, i když je virtuální síť ve stejném datovém centru. Tyto poplatky jsou popsány v [podrobnostech o cenách přenosu dat][DataPricing]. 
* Náklady na bránu VPN – brána virtuální sítě je hrazena, která je vyžadována pro vpn s bodem na web. Podrobnosti jsou na stránce [Ceny brány VPN.][VNETPricing]

## <a name="troubleshooting"></a>Řešení potíží

[!INCLUDE [app-service-web-vnet-troubleshooting](../../includes/app-service-web-vnet-troubleshooting.md)]

## <a name="automation"></a>Automatizace

Existuje podpora rozhraní CLI pro regionální integraci virtuální sítě. Chcete-li získat přístup k následujícím příkazům, [nainstalujte příkazové příkazové příkazy Azure][installCLI]. 

        az webapp vnet-integration --help

        Group
            az webapp vnet-integration : Methods that list, add, and remove virtual network integrations
            from a webapp.
                This command group is in preview. It may be changed/removed in a future release.
        Commands:
            add    : Add a regional virtual network integration to a webapp.
            list   : List the virtual network integrations on a webapp.
            remove : Remove a regional virtual network integration from webapp.

        az appservice vnet-integration --help

        Group
            az appservice vnet-integration : A method that lists the virtual network integrations used in an
            appservice plan.
                This command group is in preview. It may be changed/removed in a future release.
        Commands:
            list : List the virtual network integrations used in an appservice plan.

Pro integraci virtuální sítě vyžaduje bránu, můžete integrovat službu App Service s virtuální sítí Azure pomocí PowerShellu. Skript připravený ke spuštění najdete [v tématu Připojení aplikace ve službě Azure App Service k virtuální síti Azure](https://gallery.technet.microsoft.com/scriptcenter/Connect-an-app-in-Azure-ab7527e3).


<!--Image references-->
[1]: ./media/web-sites-integrate-with-vnet/vnetint-app.png
[2]: ./media/web-sites-integrate-with-vnet/vnetint-addvnet.png
[3]: ./media/web-sites-integrate-with-vnet/vnetint-classic.png
[5]: ./media/web-sites-integrate-with-vnet/vnetint-regionalworks.png
[6]: ./media/web-sites-integrate-with-vnet/vnetint-gwworks.png


<!--Links-->
[VNETOverview]: https://azure.microsoft.com/documentation/articles/virtual-networks-overview/ 
[AzurePortal]: https://portal.azure.com/
[ASPricing]: https://azure.microsoft.com/pricing/details/app-service/
[VNETPricing]: https://azure.microsoft.com/pricing/details/vpn-gateway/
[DataPricing]: https://azure.microsoft.com/pricing/details/data-transfers/
[V2VNETP2S]: https://azure.microsoft.com/documentation/articles/vpn-gateway-howto-point-to-site-rm-ps/
[ILBASE]: environment/create-ilb-ase.md
[V2VNETPortal]: ../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md
[VPNERCoex]: ../expressroute/expressroute-howto-coexist-resource-manager.md
[ASE]: environment/intro.md
[creategatewaysubnet]: ../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md#creategw
[creategateway]: https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal#creategw
[setp2saddresses]: https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal#addresspool
[VNETRouteTables]: https://docs.microsoft.com/azure/virtual-network/manage-route-table/
[installCLI]: https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest/
