---
title: Integrace aplikace s využitím Azure Virtual Network
description: Integrujte aplikace v Azure App Service s virtuálními sítěmi Azure.
author: ccompy
ms.assetid: 90bc6ec6-133d-4d87-a867-fcf77da75f5a
ms.topic: article
ms.date: 02/27/2020
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 89aa78e0d26598eacf436ca88cc6c5549f91d2fc
ms.sourcegitcommit: bc792d0525d83f00d2329bea054ac45b2495315d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/06/2020
ms.locfileid: "78673214"
---
# <a name="integrate-your-app-with-an-azure-virtual-network"></a>Integrace aplikace s Virtual Network Azure
Tento dokument popisuje funkci Integrace virtuální sítě Azure App Service a jak ji nastavit pomocí aplikací v [Azure App Service](https://go.microsoft.com/fwlink/?LinkId=529714). [Virtuální sítě Azure][VNETOverview] (virtuální sítě) umožňují umístit spoustu vašich prostředků Azure do sítě směrovatelné do jiné sítě.  

Azure App Service má dvě variace.

[!INCLUDE [app-service-web-vnet-types](../../includes/app-service-web-vnet-types.md)]

## <a name="enable-vnet-integration"></a>Povolit integraci virtuální sítě 

1. Přejít na uživatelské rozhraní sítě na portálu App Service. V části Integrace virtuální sítě vyberte *kliknutím sem proveďte konfiguraci*. 

1. Vyberte **Přidat virtuální síť**.  

   ![Vybrat integraci virtuální sítě][1]

1. Rozevírací seznam bude obsahovat všechny Správce prostředků virtuální sítě ve vašem předplatném ve stejné oblasti a níže je seznam všech Správce prostředků virtuální sítě ve všech ostatních oblastech. Vyberte virtuální síť, se kterou chcete integrovat.

   ![Vyberte virtuální síť.][2]

   * Pokud je virtuální síť ve stejné oblasti, pak buď vytvořte novou podsíť, nebo vyberte prázdnou již existující podsíť. 

   * Pokud chcete vybrat virtuální síť v jiné oblasti, musíte mít zřízenou bránu Virtual Network s povoleným odkazem na lokalitu.

   * Pokud chcete integrovat s klasickou virtuální sítí, místo kliknutí na rozevírací nabídku virtuální síť vyberte **kliknutím sem se připojte k klasické virtuální**síti. Vyberte požadovanou klasickou virtuální síť. Cílová virtuální síť již musí mít zřízenou bránu Virtual Network s povoleným odkazem na lokalitu.

    ![Vybrat klasickou virtuální síť][3]
    
Během integrace se aplikace restartuje.  Po dokončení integrace se zobrazí podrobnosti o virtuální síti, do které jste integrováni. 

Jakmile se vaše aplikace integruje do vaší virtuální sítě, bude používat stejný server DNS, se kterým je nakonfigurovaná vaše virtuální síť, pokud není Azure DNS Private Zones. V tuto chvíli nemůžete použít integraci virtuální sítě s Azure DNS Private Zones.

## <a name="regional-vnet-integration"></a>Místní integrace virtuální sítě

[!INCLUDE [app-service-web-vnet-types](../../includes/app-service-web-vnet-regional.md)]

### <a name="how-regional-vnet-integration-works"></a>Jak funguje místní integrace virtuální sítě

Aplikace v App Service jsou hostované na rolích pracovního procesu. Cenové tarify Basic a vyšší jsou vyhrazené plány hostování, ve kterých se na stejných pracovních procesech neběží žádné jiné zákazníky. Regionální integrace virtuální sítě funguje připojením virtuálních rozhraní k adresám v delegované podsíti. Vzhledem k tomu, že adresa z je ve vaší virtuální síti, může mít přístup k většině věcí ve vaší virtuální síti, stejně jako virtuální počítač ve vaší virtuální síti. Implementace sítě se liší od spuštění virtuálního počítače ve virtuální síti, což znamená, proč některé síťové funkce nejsou při používání této funkce ještě dostupné.

![Jak funguje místní integrace virtuální sítě][5]

Pokud je povolená místní integrace virtuální sítě, bude vaše aplikace pořád provádět odchozí volání na Internet přes stejné kanály jako normální. Odchozí adresy, které jsou uvedené na portálu vlastností aplikace, jsou pořád adresy používané vaší aplikací. K jakým změnám vaší aplikace patří volání služeb zabezpečeného koncového bodu služby nebo adres RFC 1918 do vaší virtuální sítě. Pokud je WEBSITE_VNET_ROUTE_ALL nastavené na hodnotu 1, pak se do vaší virtuální sítě dá odeslat veškerý odchozí provoz. 

Funkce podporuje jenom jedno virtuální rozhraní na pracovní proces.  Jedno virtuální rozhraní na pracovní proces znamená jednu místní integraci virtuální sítě podle plánu App Service. Všechny aplikace ve stejném plánu App Service můžou používat stejnou integraci virtuální sítě, ale pokud potřebujete aplikaci, aby se připojila k další virtuální síti, budete muset vytvořit jiný plán App Service. Použité virtuální rozhraní není prostředkem, ke kterému mají zákazníci přímý přístup.

Vzhledem k povaze toho, jak tato technologie funguje, se nezobrazuje přenos, který se používá pro integraci virtuální sítě, v Network Watcher ani v protokolech toku NSG.  

## <a name="gateway-required-vnet-integration"></a>Požadovaná brána Integration VNet

Požadovaná brána VNet Integration VNet podporuje připojení k virtuální síti v jiné oblasti nebo k klasické virtuální síti. Požadovaná brána Integration VNet: 

* Umožňuje aplikaci připojit se v jednu chvíli k jenom 1 síti VNet.
* Umožňuje integraci až pěti virtuální sítě do plánu App Service. 
* Umožňuje, aby se stejná síť VNet používala ve více aplikacích v plánu App Service, aniž by to mělo vliv na celkový počet, který může App Service plán použít.  Pokud máte šest aplikací využívajících stejnou virtuální síť ve stejném plánu App Service, počítá se jako 1 virtuální síť. 
* V důsledku smlouvy SLA pro bránu podporuje smlouvu SLA o úrovni 99,9%.
* Umožňuje aplikacím používat službu DNS, se kterou je nakonfigurovaná virtuální síť.
* Aby bylo možné připojit k aplikaci, je nutné, aby byla nakonfigurovaná Virtual Network brána založená na trasách s protokolem SSTP typu Point-to-Site VPN. 

Nemůžete použít bránu, která vyžaduje integraci virtuální sítě:

* S aplikacemi pro Linux
* S virtuální sítí připojenou s ExpressRoute 
* Přístup k zabezpečeným prostředkům koncových bodů služby
* S bránou koexistence, která podporuje ExpressRoute i Point-to-site/site pro sítě VPN

### <a name="set-up-a-gateway-in-your-vnet"></a>Nastavení brány ve virtuální síti ###

Postup vytvoření brány:

1. Vytvořte ve virtuální síti [podsíť brány][creategatewaysubnet] .  

1. [Vytvořte bránu VPN][creategateway]. Vyberte typ sítě VPN založený na trasách.

1. [Nastavte odkaz na adresy webu][setp2saddresses]. Pokud brána není v skladové jednotce Basic, pak musí být IKEV2 zakázaná v umístění Konfigurace lokality a musí být vybraný protokol SSTP. Odkaz na adresní prostor lokality musí být v blocích adres RFC 1918, 10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16.

Pokud jenom vytváříte bránu pro použití s App Service integrací virtuální sítě, nemusíte nahrávat certifikát. Vytvoření brány může trvat až 30 minut. Dokud bránu nezřídíte, nebudete moct svoji aplikaci integrovat s vaší virtuální sítí. 

### <a name="how-gateway-required-vnet-integration-works"></a>Jak funguje požadovaná integrace virtuální sítě v bráně

Požadovaná integrace virtuální sítě v bráně je postavená na technologii VPN v lokalitě. Point-to Site VPN omezuje síťový přístup jenom na virtuální počítač, který hostuje aplikaci. Aplikace jsou omezené jenom na přenos přes Internet, prostřednictvím Hybrid Connections nebo prostřednictvím integrace virtuální sítě. Když je vaše aplikace nakonfigurovaná s portálem, aby používala bránu, která vyžaduje integraci virtuální sítě, při vytváření a přiřazování certifikátů v bráně a na straně aplikace se pro vás bude spravovat složité vyjednávání. Konečným výsledkem je, že pracovní procesy používané k hostování vašich aplikací se můžou přímo připojit k bráně virtuální sítě ve vybrané virtuální síti. 

![Jak funguje požadovaná integrace virtuální sítě v bráně][6]

### <a name="accessing-on-premises-resources"></a>Přístup k místním prostředkům

Aplikace mají přístup k místním prostředkům integrací s virtuální sítě, které mají připojení Site-to-site. Pokud používáte bránu, která vyžaduje integraci virtuální sítě, je potřeba aktualizovat trasy místních bran VPN pomocí bloků adres typu Point-to-site. Při prvním nastavení sítě VPN typu Site-to-site musí být skripty používané ke konfiguraci správně nastaveny. Pokud přidáte adresy typu Point-to-site po vytvoření sítě VPN typu Site-to-site, je nutné trasy aktualizovat ručně. Podrobnosti o tom, jak to udělat, se liší podle brány a nejsou popsány zde. Nejde nakonfigurovat protokol BGP s připojením VPN typu Site-to-site.

Není nutná žádná další konfigurace, aby funkce Místní integrace virtuální sítě dosáhla přes vaši síť VNet a místní. Potřebujete jednoduše připojit virtuální síť k místní síti pomocí ExpressRoute nebo VPN typu Site-to-site. 

> [!NOTE]
> Funkce integrace virtuální sítě požadovaná bránou Neintegruje aplikaci s virtuální sítí, která má bránu ExpressRoute. I v případě, že je brána ExpressRoute nakonfigurovaná v [režimu koexistence][VPNERCoex] , integrace virtuální sítě nefunguje. Pokud potřebujete přístup k prostředkům prostřednictvím připojení ExpressRoute, můžete použít funkci regionální integrace virtuální sítě nebo [App Service Environment][ASE], která běží ve vaší virtuální síti. 
> 
> 

### <a name="peering"></a>Partnerské vztahy

Pokud používáte partnerský vztah s místní integrací virtuální sítě, nemusíte provádět žádnou další konfiguraci. 

Pokud používáte bránu, která vyžaduje integraci virtuální sítě s partnerským vztahem, budete muset nakonfigurovat několik dalších položek. Konfigurace partnerského vztahu pro práci s vaší aplikací:

1. Přidejte připojení partnerského vztahu do virtuální sítě, ke které se aplikace připojuje. Při přidávání partnerského připojení povolte povolit **přístup k virtuální síti** a zaškrtněte **Povolit přesměrované přenosy** a **Povolte přenos brány**.
1. Přidejte připojení partnerského vztahu do virtuální sítě, která je v partnerském vztahu k virtuální síti, ke které jste se připojili. Když do cílové virtuální sítě přidáte připojení partnerského vztahu, povolte možnost **Povolit přístup k virtuální síti** a zaškrtněte **Povolit přesměrované přenosy** a **Povolte vzdálené brány**.
1. Na portálu přejdete na App Service plán > síťové rozhraní integrace virtuální sítě >.  Vyberte virtuální síť, ke které se aplikace připojuje. V části směrování přidejte rozsah adres virtuální sítě, která má partnerský vztah s virtuální sítí, ke které je vaše aplikace připojená.  

## <a name="managing-vnet-integration"></a>Správa integrace virtuální sítě 

Připojení a odpojení pomocí virtuální sítě se nachází na úrovni aplikace. Operace, které mohou ovlivnit integraci virtuální sítě napříč více aplikacemi, jsou na úrovni plánu App Service. Ze služby App > Networking > portál Integration Portal můžete získat podrobnosti o vaší virtuální síti. Podobné informace najdete na úrovni ASP na webu ASP > Networking > na portálu Integration VNet.

Jedinou operací, kterou můžete provést v zobrazení aplikace integrace virtuální sítě, je odpojení vaší aplikace od virtuální sítě, ke které je aktuálně připojená. Pokud chcete aplikaci odpojit od virtuální sítě, vyberte **Odpojit**. Když se odpojíte od virtuální sítě, vaše aplikace se restartuje. Odpojením se nemění vaše virtuální síť. Podsíť nebo brána se neodeberou. Pokud budete chtít virtuální síť odstranit, musíte nejdřív odpojit aplikaci od virtuální sítě a odstranit prostředky, jako jsou brány. 

V uživatelském rozhraní integrace virtuální sítě ASP se zobrazí všechny integrace virtuální sítě používané aplikacemi ve vašem ASP. Pokud chcete zobrazit podrobnosti o každé virtuální síti, klikněte na virtuální síť, které vás zajímá. Existují dvě akce, které můžete provést pro bránu, která vyžaduje integraci virtuální sítě.

* **Synchronizovat síť** Operace synchronizace sítě je jenom pro funkci Integrace virtuální sítě závislé na bráně. Při provádění synchronizace sítě se zajistí, že budou synchronizovány i certifikáty a informace o síti. Pokud přidáváte nebo měníte DNS vaší virtuální sítě, musíte provést **synchronizační operaci sítě** . Tato operace restartuje všechny aplikace, které používají tuto virtuální síť.
* **Přidat trasy** Přidání tras bude prosazovat odchozí přenosy do vaší virtuální sítě. 

**Směrování požadované virtuální sítě pro bránu** Trasy, které jsou definované ve vaší virtuální síti, se používají k přímému směrování provozu do vaší virtuální sítě z vaší aplikace. Pokud potřebujete poslat do virtuální sítě další odchozí přenosy, pak sem můžete přidat tyto bloky adres. Tato možnost funguje jenom s potřebnou integrací virtuální sítě VNet s bránou. Směrovací tabulky nemají vliv na provoz vaší aplikace při použití brány požadovaná integrace virtuální sítě způsobem, který se v místní integraci virtuální sítě používá.

**Vyžadované brány certifikáty pro integraci virtuální** sítě Pokud brána vyžaduje integraci virtuální sítě, k zajištění zabezpečení připojení se vyžaduje certifikát Exchange. Společně s certifikáty jsou to konfigurace DNS, trasy a další podobné věci, které popisují síť.
Pokud se změní certifikáty nebo informace o síti, musíte kliknout na synchronizovat síť. Po kliknutí na synchronizovat síť dojde k krátkému výpadku připojení mezi vaší aplikací a vaší virtuální sítí. I když se vaše aplikace nerestartuje, ztráta připojení by mohla způsobit, že vaše lokalita nebude správně fungovat. 

## <a name="pricing-details"></a>Podrobnosti o cenách
Funkce regionální integrace virtuální sítě nemá žádné další poplatky za použití nad rámec cen na cenové úrovni ASP.

Existují tři související poplatky za použití funkce integrace virtuální sítě VNet vyžadované bránou:

* Poplatky za cenové úrovně ASP – vaše aplikace musí být ve schématu Standard, Premium nebo PremiumV2 App Service. Další podrobnosti o těchto nákladech najdete tady: [App Service ceny][ASPricing]. 
* Náklady na přenos dat – odchozí data se účtují i v případě, že virtuální síť je ve stejném datovém centru. Tyto poplatky jsou popsané v [podrobnostech o cenách přenos dat][DataPricing]. 
* VPN Gateway náklady – pro bránu virtuální sítě, která je vyžadována pro síť VPN typu Point-to-site, se účtují náklady. Podrobnosti najdete na stránce s [cenami VPN Gateway][VNETPricing] .

## <a name="troubleshooting"></a>Řešení potíží

[!INCLUDE [app-service-web-vnet-troubleshooting](../../includes/app-service-web-vnet-troubleshooting.md)]

## <a name="automation"></a>Automation

Pro místní integraci virtuální sítě je k dispozici podpora rozhraní příkazového řádku. Pokud chcete získat přístup k následujícím příkazům, nainstalujte rozhraní příkazového [řádku Azure][installCLI]. 

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

Pro bránu, která vyžaduje integraci virtuální sítě, můžete App Service integrovat s Azure Virtual Network pomocí PowerShellu. Skript připravený ke spuštění najdete v tématu [připojení aplikace v Azure App Service k Azure Virtual Network](https://gallery.technet.microsoft.com/scriptcenter/Connect-an-app-in-Azure-ab7527e3).


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
