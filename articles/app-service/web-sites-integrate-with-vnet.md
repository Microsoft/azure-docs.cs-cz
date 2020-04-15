---
title: Integrace aplikace s virtuální sítí Azure
description: Integrujte aplikaci ve službě Azure App Service s virtuálními sítěmi Azure.
author: ccompy
ms.assetid: 90bc6ec6-133d-4d87-a867-fcf77da75f5a
ms.topic: article
ms.date: 04/15/2020
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: cb5747c6780da134dfb2f5ab088348b848c5f04a
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2020
ms.locfileid: "81312822"
---
# <a name="integrate-your-app-with-an-azure-virtual-network"></a>Integrace aplikace s virtuální sítí Azure

Tento článek popisuje funkci integrace virtuální sítě Služby Azure App Service a jak ji nastavit pomocí aplikací ve [službě Azure App Service](https://go.microsoft.com/fwlink/?LinkId=529714). S [Virtuální sítí Azure][VNETOverview] (Virtuální sítě) můžete umístit mnoho prostředků Azure do sítě, která není přístupná internetu.

Služba Azure App Service má dvě varianty:

[!INCLUDE [app-service-web-vnet-types](../../includes/app-service-web-vnet-types.md)]

## <a name="enable-vnet-integration"></a>Povolení integrace virtuální sítě

1. Přejděte na portál **u síťového** uznatí. V části **Integrace virtuální sítě**vyberte Klepnutím **sem nakonfigurujete**.

1. Vyberte **Přidat virtuální síť**.

   ![Vybrat integraci virtuální sítě][1]

1. Rozevírací seznam obsahuje všechny virtuální sítě Azure Resource Manager ve vašem předplatném ve stejné oblasti. Pod tím je seznam virtuálních sítí Resource Manager ve všech ostatních oblastech. Vyberte virtuální síť, se kterou chcete integrovat.

   ![Výběr virtuální sítě][2]

   * Pokud je virtuální síť ve stejné oblasti, vytvořte novou podsíť nebo vyberte prázdnou již existující podsíť.
   * Chcete-li vybrat virtuální síť v jiné oblasti, musíte mít bránu virtuální sítě zřízenou s povoleným bodem na web.
   * Pokud se chcete integrovat s klasickou virtuální sítí, místo výběru rozevíracího seznamu **Virtuální síť** vyberte **Kliknutím sem se připojíte ke klasické virtuální síti**. Vyberte klasickou virtuální síť, kterou chcete. Cílová virtuální síť už musí mít bránu virtuální sítě zřízenou s povoleným bodem na web.

    ![Vybrat klasickou virtuální síť][3]

Během integrace se aplikace restartuje. Po dokončení integrace uvidíte podrobnosti o virtuální síti, do které jste integrovaní.

Po integrované aplikace s vaší virtuální sítě, používá stejný server DNS, že vaše virtuální síť je nakonfigurovaný s, pokud je Azure DNS privátní zóny. V současné době nelze použít integraci virtuální sítě s Privátními zónami Azure DNS.

## <a name="regional-vnet-integration"></a>Regionální integrace virtuální sítě

[!INCLUDE [app-service-web-vnet-types](../../includes/app-service-web-vnet-regional.md)]

### <a name="how-regional-vnet-integration-works"></a>Jak funguje regionální integrace virtuální sítě

Aplikace ve službě App Service jsou hostované na rolích pracovních rolí. Základní a vyšší cenové plány jsou vyhrazené hostingové plány, kde neexistují žádné úlohy jiných zákazníků běžící na stejných pracovnících. Regionální integrace virtuální sítě funguje tak, že připojuje te virtuální rozhraní s adresami v delegované podsíti. Vzhledem k tomu, že adresa od je ve vaší virtuální síti, může přistupovat k většině věcí ve vaší virtuální síti nebo prostřednictvím ní, jako by to udělal virtuální počítač ve vaší virtuální síti. Implementace sítě se liší od spuštění virtuálního počítače ve vaší virtuální síti. Proto některé síťové funkce ještě nejsou pro tuto funkci k dispozici.

![Jak funguje regionální integrace virtuální sítě][5]

Když je povolená regionální integrace virtuální sítě, vaše aplikace provádí odchozí volání na internet prostřednictvím stejných kanálů jako obvykle. Odchozí adresy, které jsou uvedeny na portálu vlastností aplikace, jsou adresy, které vaše aplikace stále používá. Jaké změny pro vaši aplikaci jsou volání služby koncový bod zabezpečené služby nebo RFC 1918 adresy přejít do vaší virtuální sítě. Pokud WEBSITE_VNET_ROUTE_ALL je nastavena na 1, všechny odchozí provoz y lze odeslat do virtuální sítě.

Tato funkce podporuje pouze jedno virtuální rozhraní na pracovníka. Jedno virtuální rozhraní na pracovníka znamená jednu regionální integraci virtuální sítě na plán služby App Service. Všechny aplikace ve stejném plánu služby App Service můžou používat stejnou integraci virtuální sítě. Pokud potřebujete aplikaci pro připojení k další virtuální síti, musíte vytvořit jiný plán služby App Service. Použité virtuální rozhraní není prostředek, ke kterému mají zákazníci přímý přístup.

Vzhledem k povaze fungování této technologie se provoz, který se používá s integrací virtuální sítě, nezobrazuje v azure network watcheru nebo protokolech toku NSG.

## <a name="gateway-required-vnet-integration"></a>Integrace virtuální sítě požadovaná v bráně

Integrace virtuální sítě požadovaná bránou podporuje připojení k virtuální síti v jiné oblasti nebo ke klasické virtuální síti. Integrace virtuální sítě vyžadované bránou:

* Umožňuje aplikaci připojit se k jenom k jedné virtuální síti najednou.
* Umožňuje integrovat až pět virtuálních sítí v rámci plánu služby App Service.
* Umožňuje, aby stejnou virtuální síť používalo více aplikací v plánu služby App Service, aniž by to ovlivnilo celkový počet, který může použít plán služby App Service. Pokud máte šest aplikací, které používají stejnou virtuální síť ve stejném plánu služby App Service, počítá se to jako jedna virtuální síť, která se používá.
* Podporuje 99,9% SLA kvůli sla na bráně.
* Umožňuje aplikacím používat DNS, se kterým je virtuální síť nakonfigurovaná.
* Před připojením k aplikaci vyžaduje bránu založenou na směrování virtuální sítě nakonfigurovanou pomocí sítě VPN s bodem na lokalitu SSTP.

Nelze použít integraci virtuální sítě požadovanou bránou:

* S linuxovými aplikacemi.
* S virtuální sítí připojenou k Azure ExpressRoute.
* Přístup ke zabezpečeným prostředkům koncového bodu služby.
* S bránou koexistence, která podporuje sítě SÍTĚ ExpressRoute i sítě VIRTUÁLNÍ SÍTĚ z bodu na web nebo sítě site-to-site.

### <a name="set-up-a-gateway-in-your-azure-virtual-network"></a>Nastavení brány ve virtuální síti Azure ###

Vytvoření brány:

1. [Vytvořte podsíť brány][creategatewaysubnet] ve virtuální síti.  

1. [Vytvořte bránu VPN][creategateway]. Vyberte typ VPN založený na trase.

1. [Nastavte adresy bodu k webu][setp2saddresses]. Pokud brána není v základní skladové jednotce, musí být IKEV2 zakázána v konfiguraci bodu k lokalitě a musí být vybránsstp. Adresní prostor point-to-site musí být v adresním blocích RFC 1918 10.0.0.0/8, 172.16.0.0/12 a 192.168.0.0/16.

Pokud vytvoříte bránu pro použití s integrací virtuální sítě služby App Service, nemusíte nahrát certifikát. Vytvoření brány může trvat 30 minut. Dokud se brána nezřídí, nebudete moct integrovat svou aplikaci s virtuální sítí.

### <a name="how-gateway-required-vnet-integration-works"></a>Jak funguje integrace virtuální sítě vyžadované bránou

Integrace virtuální sítě vyžadované bránou je postavená na technologii VPN od bodu do lokality. Sítě VIRTUÁLNÍ SÍTĚ point-to-site omezují přístup k síti k virtuálnímu počítači, který je hostitelem aplikace. Aplikace jsou omezeny na odesílání provozu na internet jenom prostřednictvím hybridních připojení nebo prostřednictvím integrace virtuální sítě. Když je vaše aplikace nakonfigurovaná s portálem tak, aby používala integraci virtuální sítě požadovanou bránou, je vaším jménem spravováno složité vyjednávání za účelem vytvoření a přiřazení certifikátů na straně brány a aplikace. Výsledkem je, že pracovníci, kteří slouží k hostování vašich aplikací, se můžou přímo připojit k bráně virtuální sítě ve vybrané virtuální síti.

![Jak funguje integrace virtuální sítě vyžadované bránou][6]

### <a name="access-on-premises-resources"></a>Přístup k místním prostředkům

Aplikace mohou přistupovat k místním prostředkům integrací s virtuálními sítěmi, které mají připojení k webu. Pokud používáte integraci virtuální sítě požadovanou bránou, aktualizujte místní trasy brány VPN pomocí bloků adres point-to-site. Při prvním nastavení sítě VPN mezi lokalitami by měly skripty použité ke konfiguraci správně nastavit trasy. Pokud po vytvoření sítě VPN mezi lokalitami přidáte adresy point-to-site, je třeba trasy aktualizovat ručně. Podrobnosti o tom, jak to udělat, se liší podle brány a nejsou zde popsány. Protokol BGP nelze nakonfigurovat pomocí připojení VPN mezi lokalitami.

Žádná další konfigurace je vyžadována pro místní funkci integrace virtuální sítě dosáhnout prostřednictvím virtuální sítě do místních prostředků. Jednoduše potřebujete připojit virtuální síť k místním prostředkům pomocí ExpressRoute nebo sítě VPN mezi lokalitami.

> [!NOTE]
> Funkce integrace virtuální sítě požadovaná bránou neintegruje aplikaci s virtuální sítí, která má bránu ExpressRoute. I v případě, že brána ExpressRoute je nakonfigurovaná v [režimu koexistence][VPNERCoex], integrace virtuální sítě nefunguje. Pokud potřebujete přístup k prostředkům prostřednictvím připojení ExpressRoute, použijte funkci integrace místní virtuální sítě nebo [prostředí služby App Service][ASE], které běží ve vaší virtuální síti.
> 
> 

### <a name="peering"></a>Partnerské vztahy

Pokud používáte partnerský vztah s místní integrací virtuální sítě, nemusíte provádět žádnou další konfiguraci.

Pokud používáte integraci virtuální sítě vyžadované bránou s partnerským vztahem, musíte nakonfigurovat několik dalších položek. Konfigurace partnerského vztahu pro práci s vaší aplikací:

1. Přidejte připojení partnerského vztahu na virtuální síť, ke které se vaše aplikace připojí. Když přidáte připojení partnerského vztahu, povolte **povolit přístup k virtuální síti** a vyberte **Povolit předávaný provoz** a **Povolit přenos brány**.
1. Přidejte připojení partnerského vztahu na virtuální síť, která je plychová na virtuální síti, ke které jste připojeni. Když přidáte připojení partnerského vztahu na cílovou virtuální síť, povolte **povolit přístup k virtuální síti** a vyberte **Povolit předávaný provoz** a **Povolit vzdálené brány**.
1. Přejděte na portálu**VNet Integration** na **plán** > Služby pro vytváření**sítí.** >  Vyberte virtuální síť, ke které se vaše aplikace připojuje. V části směrování přidejte rozsah adres virtuální sítě, která je propojená s virtuální sítí, ke které je vaše aplikace připojená.

## <a name="manage-vnet-integration"></a>Správa integrace virtuální sítě

Připojení a odpojení pomocí virtuální sítě je na úrovni aplikace. Operace, které můžou ovlivnit integraci virtuální sítě napříč více aplikacemi, jsou na úrovni plánu služby App Service. Z aplikace > **portálu** > **integrace virtuální sítě** sítě můžete získat podrobnosti o vaší virtuální síti. Podobné informace se zobrazí na úrovni plánu služby App Service na portálu**pro integraci** **síťové** > virtuální sítě plán **služby App Service.** > 

Jedinou operací, kterou můžete provést v zobrazení aplikace instanci integrace virtuální sítě, je odpojit vaši aplikaci od virtuální sítě, ke které je aktuálně připojená. Pokud chcete aplikaci odpojit od virtuální sítě, vyberte **Odpojit**. Vaše aplikace se restartuje, když se odpojíte od virtuální sítě. Odpojení mne nezmění virtuální síť. Podsíť nebo brána se neodebere. Pokud pak chcete odstranit virtuální síť, nejprve odpojte aplikaci od virtuální sítě a odstraňte prostředky v ní, jako jsou brány.

U rozhraní pro integraci virtuální sítě service služby App Service zobrazuje všechny integrace virtuální sítě používané aplikacemi v plánu služby App Service. Pokud chcete zobrazit podrobnosti o jednotlivých virtuálních sítích, vyberte virtuální síť, která vás zajímá. Existují dvě akce, které můžete provést zde pro integraci virtuální sítě požadovanou bránou:

* **Synchronizační síť**: Operace synchronizační sítě se používá jenom pro funkci integrace virtuální sítě závislou na bráně. Provedení operace synchronizační sítě zajišťuje synchronizaci certifikátů a informací o síti. Pokud přidáte nebo změníte DNS virtuální sítě, proveďte operaci synchronizační sítě. Tato operace restartuje všechny aplikace, které používají tuto virtuální síť.
* **Přidání tras:** Přidání tras řídí odchozí provoz do virtuální sítě.

### <a name="gateway-required-vnet-integration-routing"></a>Směrování integrace virtuální sítě požadované bránou
Trasy, které jsou definované ve vaší virtuální síti se používají k přímému provozu do virtuální sítě z vaší aplikace. Chcete-li odeslat další odchozí provoz do virtuální sítě, přidejte tyto bloky adres zde. Tato funkce funguje jenom s integrací virtuální sítě požadované bránou. Tabulky tras nemají vliv na provoz aplikací, když používáte integraci virtuální sítě požadovanou bránou tak, jak to dělají s regionální integrací virtuální sítě.

### <a name="gateway-required-vnet-integration-certificates"></a>Certifikáty integrace virtuální sítě požadované bránou
Pokud je povolena integrace virtuální sítě požadovaná bránou, je vyžadována výměna certifikátů, aby bylo zajištěno zabezpečení připojení. Spolu s certifikáty jsou konfigurace DNS, trasy a další podobné věci, které popisují síť.

Pokud dojde ke změně certifikátů nebo informací o síti, vyberte **možnost Synchronizovat síť**. Když vyberete **Synchronizovat síť**, způsobíte krátký výpadek v připojení mezi vaší aplikací a virtuální sítí. I když vaše aplikace není restartována, ztráta připojení může způsobit, že váš web nebude fungovat správně.

## <a name="pricing-details"></a>Podrobnosti o cenách
Funkce integrace místní virtuální sítě nemá žádné další poplatky za použití nad rámec poplatků za cenovou úroveň plánu služby App Service.

Tři poplatky souvisejí s použitím funkce integrace virtuální sítě pobráně:

* **Poplatky za cenovou úroveň plánu služby App Service**: Vaše aplikace musí být v tarifu Standard, Premium nebo PremiumV2 App Service. Další informace o těchto nákladech najdete v [tématu Ceny služby App Service][ASPricing].
* **Náklady na přenos dat**: Za odchozí data se účtuje poplatek, i když je virtuální síť ve stejném datovém centru. Tyto poplatky jsou popsány v [podrobnostech o cenách přenosu dat][DataPricing].
* **Náklady na bránu VPN:** Brána virtuální sítě je vyžadována pro vpn bodu na místo. Další informace naleznete v tématu [ceny brány VPN][VNETPricing].

## <a name="troubleshooting"></a>Řešení potíží

[!INCLUDE [app-service-web-vnet-troubleshooting](../../includes/app-service-web-vnet-troubleshooting.md)]

## <a name="automation"></a>Automation

Podpora rozhraní příkazového příkazu je k dispozici pro regionální integraci virtuální sítě. Chcete-li získat přístup k následujícím příkazům, [nainstalujte příkazové příkazové příkazy Azure][installCLI].

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

Pro integraci virtuální sítě požadovanou bránou můžete integrovat službu App Service s virtuální sítí Azure pomocí PowerShellu. Skript připravený ke spuštění najdete [v tématu Připojení aplikace ve službě Azure App Service k virtuální síti Azure](https://gallery.technet.microsoft.com/scriptcenter/Connect-an-app-in-Azure-ab7527e3).


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
