---
title: Integrace aplikace s využitím Azure Virtual Network
description: Integrujte aplikaci v Azure App Service s virtuálními sítěmi Azure.
author: ccompy
ms.assetid: 90bc6ec6-133d-4d87-a867-fcf77da75f5a
ms.topic: article
ms.date: 08/05/2020
ms.author: ccompy
ms.custom: seodec18, devx-track-azurepowershell
ms.openlocfilehash: 42391a073d7cb1d7e6850e298c2be32d550bb813
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2021
ms.locfileid: "107832063"
---
# <a name="integrate-your-app-with-an-azure-virtual-network"></a>Integrace aplikace do služby Azure Virtual Network

Tento článek popisuje funkci Integrace virtuální sítě Azure App Service a její nastavení s využitím aplikací v [Azure App Service](./overview.md). Pomocí služby [azure Virtual Network][VNETOverview] (virtuální sítě) můžete mnoho vašich prostředků Azure umístit do sítě, která není směrovatelný z Internetu. Funkce integrace virtuální sítě umožňuje aplikacím přistupovat k prostředkům v síti nebo prostřednictvím virtuální sítě. Integrace virtuální sítě neumožňuje, aby vaše aplikace byly dostupné soukromě.

Azure App Service má dvě varianty funkce integrace virtuální sítě:

[!INCLUDE [app-service-web-vnet-types](../../includes/app-service-web-vnet-types.md)]

## <a name="enable-vnet-integration"></a>Povolit integraci virtuální sítě

1. Přejít na uživatelské rozhraní **sítě** na portálu App Service. V části **Integrace virtuální** sítě vyberte **Konfigurovat kliknutím sem**.

1. Vyberte **Přidat virtuální síť**.

   ![Vybrat integraci virtuální sítě][1]

1. Rozevírací seznam obsahuje všechny Azure Resource Manager virtuální sítě ve vašem předplatném ve stejné oblasti. Pod seznamem je seznam virtuálních sítí Správce prostředků ve všech ostatních oblastech. Vyberte virtuální síť, se kterou chcete integrovat.

   ![Vyberte virtuální síť.][2]

   * Pokud je virtuální síť ve stejné oblasti, buď vytvořte novou podsíť, nebo vyberte prázdnou existující podsíť.
   * Pokud chcete vybrat virtuální síť v jiné oblasti, musíte mít zřízenou bránu virtuální sítě s povoleným odkazem na lokalitu.
   * Pokud chcete integrovat s klasickou virtuální sítí, místo výběru **Virtual Network** rozevíracího seznamu vyberte **kliknutím sem se připojte k klasické virtuální** síti. Vyberte klasickou virtuální síť, kterou chcete. Cílová virtuální síť musí mít již zřízenou bránu Virtual Network s povoleným Point-to-site.

    ![Vybrat klasickou virtuální síť][3]

Během integrace se aplikace restartuje. Po dokončení integrace se zobrazí podrobnosti o virtuální síti, do které jste integraci.

## <a name="regional-vnet-integration"></a>Místní integrace virtuální sítě

[!INCLUDE [app-service-web-vnet-types](../../includes/app-service-web-vnet-regional.md)]

### <a name="how-regional-vnet-integration-works"></a>Jak funguje místní integrace virtuální sítě

Aplikace v App Service jsou hostované na rolích pracovního procesu. Cenové tarify Basic a vyšší jsou vyhrazené plány hostování, kde na stejných pracovních procesech neběží žádné úlohy ostatních zákazníků. Regionální integrace virtuální sítě funguje připojením virtuálních rozhraní k adresám v delegované podsíti. Vzhledem k tomu, že adresa z je ve vaší virtuální síti, má přístup k většině věcí ve vaší virtuální síti, jako je třeba virtuální počítač ve vaší virtuální síti. Implementace sítě se liší od spuštění virtuálního počítače ve vaší virtuální síti. Proto některé síťové funkce nejsou pro tuto funkci ještě dostupné.

![Jak funguje místní integrace virtuální sítě][5]

Pokud je povolená místní integrace virtuální sítě, vaše aplikace provede odchozí volání na Internet přes stejné kanály jako normální. Odchozí adresy, které jsou uvedeny na portálu vlastností aplikace, jsou adresy, které vaše aplikace ještě používá. Jaké změny vaší aplikace jsou voláním služeb zabezpečeného koncovým bodem služby, nebo adresy RFC 1918 přecházejí do vaší virtuální sítě. Pokud je WEBSITE_VNET_ROUTE_ALL nastavené na 1, všechny odchozí přenosy se můžou poslat do vaší virtuální sítě.

> [!NOTE]
> `WEBSITE_VNET_ROUTE_ALL` v kontejnerech Windows není aktuálně podporován.
> 

Funkce podporuje pouze jedno virtuální rozhraní na pracovní proces. Jedno virtuální rozhraní na pracovní proces znamená jednu místní integraci virtuální sítě podle plánu App Service. Všechny aplikace ve stejném plánu App Service můžou používat stejnou integraci virtuální sítě. Pokud potřebujete aplikaci pro připojení k další virtuální síti, je potřeba vytvořit jiný plán App Service. Použité virtuální rozhraní není prostředkem, ke kterému mají zákazníci přímý přístup.

Vzhledem k tomu, jak tato technologie funguje, se provoz, který se používá při integraci virtuální sítě, nezobrazuje v protokolech Azure Network Watcher ani NSG Flow.

## <a name="gateway-required-vnet-integration"></a>Brána – požadovaná integrace virtuální sítě

Brána – požadovaná integrace virtuální sítě podporuje připojení k virtuální síti v jiné oblasti nebo v klasické virtuální síti. Brána – požadovaná integrace virtuální sítě:

* Umožňuje aplikaci, aby se připojovala pouze k jedné virtuální síti v jednom okamžiku.
* Umožňuje integrovat až pět virtuální sítě v rámci plánu App Service.
* Umožňuje, aby stejná virtuální síť byla používána více aplikacemi v plánu App Service bez vlivu na celkový počet, který může App Service plán použít. Pokud máte šest aplikací využívajících stejnou virtuální síť v rámci stejného App Serviceho plánu, která se počítá jako jedna virtuální síť, která se používá.
* V důsledku smlouvy SLA pro bránu podporuje smlouvu SLA o úrovni 99,9%.
* Umožňuje aplikacím používat službu DNS, se kterou je nakonfigurovaná virtuální síť.
* Předtím, než bude možné připojit se k aplikaci, je nutné, aby byla nakonfigurovaná Virtual Network brána založená na trasách s VPN Point-to-site s protokolem SSTP.

Nemůžete použít integraci virtuální sítě požadovaná bránou:

* S virtuální sítí připojenou ke službě Azure ExpressRoute.
* Z aplikace pro Linux.
* Z [kontejneru Windows](quickstart-custom-container.md).
* Pro přístup k prostředkům zabezpečeného koncového bodu služby.
* S bránou koexistence, která podporuje ExpressRoute i sítě VPN typu Point-to-site nebo site-to-site.

### <a name="set-up-a-gateway-in-your-azure-virtual-network"></a>Nastavení brány ve službě Azure Virtual Network ###

Postup vytvoření brány:

1. Vytvořte ve virtuální síti [podsíť brány][creategatewaysubnet] .

1. [Vytvořte bránu VPN][creategateway]. Vyberte typ sítě VPN založený na trasách.

1. [Nastavte adresy Point-to-site][setp2saddresses]. Pokud brána není v skladové jednotce Basic, musí být IKEV2 v konfiguraci Point-to-site zakázaná a musí se vybrat protokol SSTP. Adresní prostor Point-to-site musí být v umístění RFC 1918 bloků adres 10.0.0.0/8, 172.16.0.0/12 a 192.168.0.0/16.

Pokud vytvoříte bránu pro použití s App Service Integration VNet, nemusíte nahrávat certifikát. Vytvoření brány může trvat až 30 minut. Dokud bránu nezřídíte, nebudete moct svoji aplikaci integrovat s vaší virtuální sítí.

### <a name="how-gateway-required-vnet-integration-works"></a>Jak brána vyžaduje integraci virtuální sítě

Brána – požadovaná integrace virtuální sítě je postavená na technologii VPN typu Point-to-site. Sítě VPN typu Point-to-site omezují síťový přístup k virtuálnímu počítači, který hostuje aplikaci. Aplikace jsou omezené tak, aby odesílaly přenosy na Internet jenom prostřednictvím Hybrid Connections nebo prostřednictvím integrace virtuální sítě. Když je vaše aplikace nakonfigurovaná s portálem, aby používala integraci virtuální sítě požadovaná bránou, je složité vyjednávání spravovaná vaším jménem k vytváření a přiřazování certifikátů v bráně a na straně aplikace. Výsledkem je, že se pracovní procesy používané k hostování vašich aplikací můžou přímo připojit k bráně virtuální sítě ve vybrané virtuální síti.

![Jak brána vyžaduje integraci virtuální sítě][6]

### <a name="access-on-premises-resources"></a>Přístup k místním prostředkům

Aplikace mají přístup k místním prostředkům integrací s virtuální sítě, které mají připojení Site-to-site. Pokud používáte integraci virtuální sítě požadovaná bránou, aktualizujte své místní trasy brány VPN pomocí bloků adres typu Point-to-site. Při prvním nastavení sítě VPN typu Site-to-site musí být skripty používané ke konfiguraci správně nastaveny. Pokud přidáte adresy typu Point-to-site po vytvoření sítě VPN typu Site-to-site, je nutné trasy aktualizovat ručně. Podrobnosti o tom, jak to udělat, se liší podle brány a nejsou popsány zde. Nejde nakonfigurovat protokol BGP s připojením VPN typu Site-to-site.

Žádná další konfigurace není nutná, aby funkce Místní integrace virtuální sítě dosáhla vaší virtuální sítě k místním prostředkům. Stačí připojit virtuální síť k místním prostředkům pomocí ExpressRoute nebo VPN typu Site-to-site.

> [!NOTE]
> Funkce integrace virtuální sítě požadovaná bránou Neintegruje aplikaci s virtuální sítí, která má bránu ExpressRoute. I v případě, že je brána ExpressRoute nakonfigurovaná v [režimu koexistence][VPNERCoex], integrace virtuální sítě nefunguje. Pokud potřebujete přístup k prostředkům prostřednictvím připojení ExpressRoute, použijte funkci regionální integrace virtuální sítě nebo [App Service Environment][ASE], která běží ve vaší virtuální síti.
>
>

### <a name="peering"></a>Partnerské vztahy

Pokud používáte partnerské vztahy s místní integrací virtuální sítě, nemusíte provádět žádnou další konfiguraci.

Pokud používáte integraci virtuální sítě požadovaná bránou s partnerským vztahem, budete muset nakonfigurovat několik dalších položek. Konfigurace partnerského vztahu pro práci s vaší aplikací:

1. Přidejte připojení partnerského vztahu do virtuální sítě, ke které se aplikace připojuje. Když přidáte připojení partnerského vztahu, povolte **Povolit přístup k virtuální síti** a vyberte **Povolit přesměrovaný přenos** a **Povolit přenos brány**.
1. Přidejte připojení partnerského vztahu ve virtuální síti, která je v partnerském vztahu k virtuální síti, ke které jste se připojili. Pokud přidáte připojení partnerského vztahu do cílové virtuální sítě, povolte možnost **Povolit přístup k virtuální síti** a vyberte **Povolit předaný přenos** a **Povolit vzdálené brány**.
1. V portálu **App Service Naplánujte**  >  **síťové**  >  rozhraní **Integrace virtuální** sítě. Vyberte virtuální síť, ke které se aplikace připojuje. V části směrování přidejte rozsah adres virtuální sítě, která má partnerský vztah s virtuální sítí, ke které je vaše aplikace připojená.

## <a name="manage-vnet-integration"></a>Správa integrace virtuální sítě

Připojení a odpojení pomocí virtuální sítě se nachází na úrovni aplikace. Operace, které mohou ovlivnit integraci virtuální sítě napříč více aplikacemi, jsou na úrovni plánu App Service. Na portálu Integration **Networking služby App > Networking**  >   můžete získat podrobnosti o vaší virtuální síti. Podobné informace najdete na úrovni plánu App Service na   >  portálu pro integraci **síťových virtuálních sítí** v App Service  >   .

Jedinou operací, kterou můžete provést v zobrazení aplikace vaší instance integrace virtuální sítě, je odpojení vaší aplikace od virtuální sítě, ke které je aktuálně připojeno. Pokud chcete aplikaci odpojit od virtuální sítě, vyberte **Odpojit**. Vaše aplikace se po odpojení od virtuální sítě restartuje. Odpojením se nemění vaše virtuální síť. Podsíť nebo brána se neodeberou. Pokud budete chtít virtuální síť odstranit, nejdřív odpojte aplikaci od virtuální sítě a odstraňte v ní prostředky, jako jsou brány.

Rozhraní App Service plánování virtuální sítě Integration UI zobrazuje všechny integrace virtuální sítě používané aplikacemi ve vašem plánu App Service. Pokud si chcete zobrazit podrobnosti o každé virtuální síti, vyberte virtuální síť, na kterou vás zajímáte. K dispozici jsou dvě akce, které můžete provést pro bránu. integrace virtuální sítě vyžaduje:

* **Synchronizace sítě**: Síťová operace synchronizace se používá jenom pro funkci Integrace virtuální sítě závislé na bráně. Při provádění synchronizace sítě se zajistí, že budou synchronizovány i certifikáty a informace o síti. Pokud přidáte nebo změníte DNS vaší virtuální sítě, proveďte synchronizaci síťové operace. Tato operace restartuje všechny aplikace, které používají tuto virtuální síť. Tato operace nebude fungovat, pokud používáte aplikaci a virtuální síť, která patří do různých předplatných.
* **Přidání tras**: přidání tras k odchozímu provozu do vaší virtuální sítě.

Privátní IP adresa přiřazená k instanci je vystavena prostřednictvím proměnné prostředí **WEBSITE_PRIVATE_IP**. Uživatelské rozhraní konzoly Kudu také zobrazuje seznam proměnných prostředí, které jsou k dispozici pro webovou aplikaci. Tato IP adresa se přiřazuje z rozsahu adres integrované podsítě. Pro místní integraci virtuální sítě je hodnotou WEBSITE_PRIVATE_IP IP adresa z rozsahu adres delegované podsítě a pro integraci virtuální sítě vyžadované bránou je tato hodnota IP adresa z rozsahu adres ve fondu adres Point-to-site, který je nakonfigurovaný na Virtual Network bráně. Toto je IP adresa, kterou webová aplikace použije k připojení k prostředkům prostřednictvím Virtual Network. 

> [!NOTE]
> Hodnota WEBSITE_PRIVATE_IP je svázána se změnou. Bude se ale jednat o IP adresu v rozsahu adres podsítě integrace nebo rozsahu adres Point-to-site, takže budete muset umožnit přístup z celého rozsahu adres.
>

### <a name="gateway-required-vnet-integration-routing"></a>Brána – požadované směrování integrace virtuální sítě
Trasy, které jsou definované ve vaší virtuální síti, se používají k přímému směrování provozu do vaší virtuální sítě z vaší aplikace. Pokud chcete do virtuální sítě odeslat další odchozí provoz, přidejte sem tyto bloky adres. Tato možnost funguje jenom s integrací virtuální sítě požadovaná bránou. Směrovací tabulky nemají vliv na provoz vaší aplikace při použití integrace virtuální sítě vyžadované bránou způsobem, který provádí s regionální integrací virtuální sítě.

### <a name="gateway-required-vnet-integration-certificates"></a>Brána – vyžadované certifikáty pro integraci virtuální sítě
Pokud je povolená integrace virtuální sítě požadovaná bránou, k zajištění zabezpečení připojení je nutný certifikát Exchange. Společně s certifikáty jsou to konfigurace DNS, trasy a další podobné věci, které popisují síť.

Pokud se změní certifikáty nebo informace o síti, vyberte **synchronizovat síť**. Když vyberete **synchronizovat síť**, dojde k krátkému výpadku připojení mezi vaší aplikací a vaší virtuální sítí. I když se vaše aplikace nerestartuje, ztráta připojení by mohla způsobit, že vaše lokalita nebude správně fungovat.

## <a name="pricing-details"></a>Podrobnosti o cenách
Funkce regionální integrace virtuální sítě nemá žádné další poplatky za použití nad rámec sazeb za cenové úrovně v plánu App Service.

Tři poplatky souvisejí s používáním funkce integrace virtuální sítě vyžadované bránou:

* **Poplatky za cenové úrovně v App Service plánu**: vaše aplikace musí být v plánu Standard, Premium, PremiumV2 nebo App Service PremiumV3. Další informace o těchto nákladech najdete v tématu [App Service ceny][ASPricing].
* **Náklady na přenos dat**: za výstup dat se účtuje i v případě, že virtuální síť je ve stejném datacentru. Tyto poplatky jsou popsané v [podrobnostech o cenách přenos dat][DataPricing].
* **Náklady na bránu VPN**: pro bránu virtuální sítě, která je potřeba pro síť VPN typu Point-to-site, se účtuje náklady. Další informace najdete v tématu [ceny služby VPN Gateway][VNETPricing].

## <a name="troubleshooting"></a>Řešení potíží

> [!NOTE]
> Integrace virtuální sítě se nepodporuje pro scénáře Docker Compose v App Service.
> Omezení přístupu Azure Functions jsou ignorována, pokud je přítomen soukromý koncový bod.
>

[!INCLUDE [app-service-web-vnet-troubleshooting](../../includes/app-service-web-vnet-troubleshooting.md)]

## <a name="automation"></a>Automation

Podpora rozhraní příkazového řádku je k dispozici pro místní integraci virtuální sítě. Pokud chcete získat přístup k následujícím příkazům, [nainstalujte rozhraní příkazového řádku Azure][installCLI].

```azurecli
az webapp vnet-integration --help

Group
    az webapp vnet-integration : Methods that list, add, and remove virtual network
    integrations from a webapp.
        This command group is in preview. It may be changed/removed in a future release.
Commands:
    add    : Add a regional virtual network integration to a webapp.
    list   : List the virtual network integrations on a webapp.
    remove : Remove a regional virtual network integration from webapp.

az appservice vnet-integration --help

Group
    az appservice vnet-integration : A method that lists the virtual network
    integrations used in an appservice plan.
        This command group is in preview. It may be changed/removed in a future release.
Commands:
    list : List the virtual network integrations used in an appservice plan.
```

Podpora PowerShellu pro místní integraci virtuální sítě je také k dispozici, ale je nutné vytvořit obecný prostředek s polem vlastností podsítě resourceID.

```azurepowershell
# Parameters
$sitename = 'myWebApp'
$resourcegroupname = 'myRG'
$VNetname = 'myVNet'
$location = 'myRegion'
$integrationsubnetname = 'myIntegrationSubnet'
$subscriptionID = 'aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee'

#Property array with the SubnetID
$properties = @{
  subnetResourceId = "/subscriptions/$subscriptionID/resourceGroups/$resourcegroupname/providers/Microsoft.Network/virtualNetworks/$VNetname/subnets/$integrationsubnetname"
}

#Creation of the VNet integration
$vNetParams = @{
  ResourceName = "$sitename/VirtualNetwork"
  Location = $location
  ResourceGroupName = $resourcegroupname
  ResourceType = 'Microsoft.Web/sites/networkConfig'
  PropertyObject = $properties
}
New-AzResource @vNetParams
```


U brány – požadovaná integrace virtuální sítě můžete App Service integrovat s Azure Virtual Network pomocí PowerShellu. Skript připravený ke spuštění najdete v tématu [připojení aplikace v Azure App Service k virtuální síti Azure](https://gallery.technet.microsoft.com/scriptcenter/Connect-an-app-in-Azure-ab7527e3).


<!--Image references-->
[1]: ./media/web-sites-integrate-with-vnet/vnetint-app.png
[2]: ./media/web-sites-integrate-with-vnet/vnetint-addvnet.png
[3]: ./media/web-sites-integrate-with-vnet/vnetint-classic.png
[5]: ./media/web-sites-integrate-with-vnet/vnetint-regionalworks.png
[6]: ./media/web-sites-integrate-with-vnet/vnetint-gwworks.png


<!--Links-->
[VNETOverview]: ../virtual-network/virtual-networks-overview.md
[AzurePortal]: https://portal.azure.com/
[ASPricing]: https://azure.microsoft.com/pricing/details/app-service/
[VNETPricing]: https://azure.microsoft.com/pricing/details/vpn-gateway/
[DataPricing]: https://azure.microsoft.com/pricing/details/data-transfers/
[V2VNETP2S]: ../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md
[ILBASE]: environment/create-ilb-ase.md
[V2VNETPortal]: ../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md
[VPNERCoex]: ../expressroute/expressroute-howto-coexist-resource-manager.md
[ASE]: environment/intro.md
[creategatewaysubnet]: ../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md#creategw
[creategateway]: ../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md#creategw
[setp2saddresses]: ../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md#addresspool
[VNETRouteTables]: ../virtual-network/manage-route-table.md
[installCLI]: /cli/azure/install-azure-cli
[privateendpoints]: networking/private-endpoint.md
