---
title: Azure ExpressRoute – nejčastější dotazy | Microsoft Docs
description: ExpressRoute – nejčastější dotazy obsahuje informace o podporované služby Azure, náklady, dat a připojení, SLA, poskytovatelé a umístění, šířky pásma a další technické podrobnosti.
documentationcenter: na
services: expressroute
author: cherylmc
manager: timlt
editor: ''
ms.assetid: 09b17bc4-d0b3-4ab0-8c14-eed730e1446e
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/29/2018
ms.author: cherylmc
ms.openlocfilehash: 557c6a75140f399f74d77543de834af086871032
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2018
---
# <a name="expressroute-faq"></a>ExpressRoute – nejčastější dotazy

## <a name="what-is-expressroute"></a>Co je ExpressRoute?

ExpressRoute je služba Azure, která umožňuje vytvářet privátní připojení mezi datovými centry společnosti Microsoft a infrastruktury, který je místně nebo v budovy společné umístění. Připojení ExpressRoute nezadávejte přejděte prostřednictvím veřejného Internetu a nabízí vyšší zabezpečení, spolehlivost a rychlosti s nižší latenci než Typická připojení přes Internet.

### <a name="what-are-the-benefits-of-using-expressroute-and-private-network-connections"></a>Jaké jsou výhody používání ExpressRoute a připojení k privátní síti?

Připojení ExpressRoute se nepřenášejí prostřednictvím veřejného internetu. Nabízí vyšší zabezpečení, spolehlivost a rychlosti, s konzistentní a nižší latenci než Typická připojení přes Internet. V některých případech používání připojení ExpressRoute pro přenos dat mezi místní zařízení a Azure přispět výraznému snížení nákladů.

### <a name="where-is-the-service-available"></a>Kde je služba k dispozici?

Zobrazí tato stránka pro umístění služby a dostupnosti: [ExpressRoute partnery a umístění](expressroute-locations.md).

### <a name="how-can-i-use-expressroute-to-connect-to-microsoft-if-i-dont-have-partnerships-with-one-of-the-expressroute-carrier-partners"></a>Jak se připojit k Microsoft, pokud není k dispozici partnerství s jedním z partnerských serverů pro ExpressRoute poskytovatel použitím ExpressRoute?

Můžete vybrat místní poskytovatel a zobrazovat ethernetová připojení na jednu z podporovaných exchange umístění poskytovatele. Můžete pak partnerský vztah Microsoftu na adresu umístění poskytovatele. Zkontrolujte poslední část [ExpressRoute partnery a umístění](expressroute-locations.md) zobrazíte, když poskytovatele služeb se nachází v umístění serveru exchange. Potom můžete objednat okruh ExpressRoute prostřednictvím poskytovatele služeb pro připojení k Azure.

### <a name="how-much-does-expressroute-cost"></a>Kolik stojí ExpressRoute?

Zkontrolujte [podrobnosti o cenách](https://azure.microsoft.com/pricing/details/expressroute/) informace o cenách.

### <a name="if-i-pay-for-an-expressroute-circuit-of-a-given-bandwidth-does-the-vpn-connection-i-purchase-from-my-network-service-provider-have-to-be-the-same-speed"></a>Pokud platí I pro okruh ExpressRoute dané šířky pásma, připojení k síti VPN, které zakoupit od svého poskytovatele síťové služby musí být stejné rychlosti?

Ne. Připojení k síti VPN žádné rychlostí si můžete zakoupit od vašeho poskytovatele služeb. Připojení k Azure je však omezená na šířku pásma okruhu ExpressRoute, který jste si koupili.

### <a name="if-i-pay-for-an-expressroute-circuit-of-a-given-bandwidth-do-i-have-the-ability-to-burst-up-to-higher-speeds-if-necessary"></a>Pokud platí I pro okruh ExpressRoute dané šířky pásma, je nutné možnost až vyšší rychlosti v případě potřeby?

Ano. Okruhy ExpressRoute jsou konfigurována pro umožňují burst až dvakrát limit šířky pásma, které jste si opatřili pro bez dalších nákladů. Zeptejte se svého poskytovatele služby, pokud jejich podporu této možnosti.

### <a name="can-i-use-the-same-private-network-connection-with-virtual-network-and-other-azure-services-simultaneously"></a>Je možné používat stejné připojení privátní sítě pomocí virtuální sítě a dalším službám Azure současně?

Ano. Okruh ExpressRoute, po nastavení, můžete současně získat přístup ke službám v rámci virtuální sítě a dalším službám Azure. Připojení k virtuálním sítím přes cestou soukromého partnerského vztahu a k jiným službám prostřednictvím cesty veřejného partnerského vztahu.

### <a name="does-expressroute-offer-a-service-level-agreement-sla"></a>Nabízí ExpressRoute smlouvy úroveň služeb (SLA)?

Informace najdete v tématu [ExpressRoute SLA](https://azure.microsoft.com/support/legal/sla/) stránky.

## <a name="supported-services"></a>Podporované služby

ExpressRoute podporuje [tři domény směrování](expressroute-circuit-peerings.md) pro různé typy služeb.

### <a name="private-peering"></a>Soukromý partnerský vztah

* Virtuální sítě, včetně všech virtuálních počítačů a cloudové služby

### <a name="public-peering"></a>Veřejný partnerský vztah

* Power BI
* Dynamics 365 Finance a operace (dříve označované jako Dynamics AX Online)
* Většina služeb Azure jsou podporovány. Zkontrolujte prosím přímo se na službu, kterou chcete použít k ověření podpory.<br>
  NEJSOU podporovány následující služby:
    * CDN
    * Visual Studio Team Services zátěžové testování
    * Multi-factor Authentication
    * Traffic Manager

### <a name="microsoft-peering"></a>Partnerský vztah Microsoftu

* [Office 365](http://aka.ms/ExpressRouteOffice365)
* Aplikace Dynamics 365 zákaznické Engagement (dříve označované jako CRM Online)
  * Dynamics 365 pro prodej
  * Dynamics 365 zákaznický servis
  * Dynamics 365 služby pole
  * Dynamics 365 for Project Service
* Pomocí [směrovat filtry](#route-filters-for-microsoft-peering), získat přístup ke službám ve stejné veřejné s partnerského vztahu Microsoftu:
  * Power BI
  * Dynamics 365 Finance a operace
  * Většina služeb Azure jsou podporovány. Zkontrolujte prosím přímo se na službu, kterou chcete použít k ověření podpory.<br>
  NEJSOU podporovány následující služby:
    * CDN
    * Visual Studio Team Services zátěžové testování
    * Multi-factor Authentication
    * Traffic Manager

## <a name="data-and-connections"></a>Data a připojení

### <a name="are-there-limits-on-the-amount-of-data-that-i-can-transfer-using-expressroute"></a>Existují omezení množství dat, která může přenést pomocí ExpressRoute?

Na objem přenosu dat jsme nenastavujte omezení. Odkazovat na [podrobnosti o cenách](https://azure.microsoft.com/pricing/details/expressroute/) informace o šířku pásma sazby.

### <a name="what-connection-speeds-are-supported-by-expressroute"></a>ExpressRoute podporuje rychlost co připojení?

Podporované šířky pásma nabízí:

50 Mb/s, 100 Mb/s, 200 Mb/s, 500 Mb/s, 1 Gb/s, 2 Gb/s, 5 Gb/s, 10 Gb/s

### <a name="which-service-providers-are-available"></a>Které poskytovatelé jsou k dispozici?

V tématu [ExpressRoute partnery a umístění](expressroute-locations.md) seznam poskytovatelé a umístění.

## <a name="technical-details"></a>Technické podrobnosti

### <a name="what-are-the-technical-requirements-for-connecting-my-on-premises-location-to-azure"></a>Jaké jsou požadavky na technické pro připojení k Azure Moje místní umístění?

V tématu [stránky požadavky ExpressRoute](expressroute-prerequisites.md) požadavky.

### <a name="are-connections-to-expressroute-redundant"></a>Jsou připojení expressroute redundantní?

Ano. Každý okruh ExpressRoute má redundantní dvojici křížové připojení nakonfigurovaná k zajištění vysoké dostupnosti.

### <a name="will-i-lose-connectivity-if-one-of-my-expressroute-links-fail"></a>Bude ztrátě připojení, pokud jeden z odkazů Moje ExpressRoute?

Pokud jeden z křížové připojení selže, nedojde ke ztrátě připojení. Redundantní připojení je k dispozici pro podporu zatížení sítě a zajištění vysoké dostupnosti váš okruh ExpressRoute. Kromě toho můžete vytvořit okruh v jiném umístění partnerského vztahu k dosažení okruh úroveň odolnosti.

### <a name="how-do-i-ensure-high-availability-on-a-virtual-network-connected-to-expressroute"></a>Jak zjistím, vysokou dostupnost ve virtuální síti připojená k ExpressRoute?

Vysoké dostupnosti můžete dosáhnout připojením okruhy ExpressRoute v různých umístění partnerského vztahu (např. Singapur, Singapore2) k virtuální síti. Pokud jeden okruh ExpressRoute přestane fungovat, připojení bude převzetí služeb při selhání jiného okruhu ExpressRoute. Ve výchozím nastavení je veden odchozího provozu z vaší virtuální sítě založené na stejné náklady s více cestami směrování ECMP (). Váha připojení můžete do jiného dáváte přednost jeden okruh. V tématu [optimalizace směrování ExpressRoute](expressroute-optimize-routing.md) další podrobnosti o připojení váhy.

### <a name="onep2plink"></a>Pokud nejste společně umístěné na výměně cloudu a poskytovateli služeb nabízí připojení typu point-to-point, je nutné pořadí dvě fyzického připojení mezi Moje místní sítí a Microsoftem?

Pokud váš poskytovatel služeb mohou vytvořit dva okruhy virtuální sítě Ethernet přes fyzické připojení, potřebujete jenom jedno fyzické připojení. Fyzické připojení (například optického vlákna) bylo ukončeno ve vrstvě 1 (Ú1) zařízení (viz obrázek). Dva okruhy virtuální sítě Ethernet jsou označené jiné ID sítě VLAN, jeden pro primární okruh a jeden pro sekundární. Tyto identifikátory ID sítě VLAN jsou ve vnější 802.1Q záhlaví sítě Ethernet. Vnitřní 802.1Q záhlaví Ethernet (není vidět) je namapována na konkrétní [domény směrování ExpressRoute](expressroute-circuit-peerings.md).

![](./media/expressroute-faqs/expressroute-p2p-ref-arch.png)

### <a name="can-i-extend-one-of-my-vlans-to-azure-using-expressroute"></a>Je možné rozšířit jeden z mých sítě VLAN do Azure pomocí služby ExpressRoute?

Ne. Nepodporujeme rozšíření vrstvy 2 připojení do Azure.

### <a name="can-i-have-more-than-one-expressroute-circuit-in-my-subscription"></a>Může mít více než jeden okruh ExpressRoute v Moje předplatné?

Ano. Můžete mít více než jeden okruh ExpressRoute v rámci vašeho předplatného. Výchozí limit je nastaven na hodnotu 10. Microsoft Support o zvýšení limitu, si můžete ověřit v případě potřeby.

### <a name="can-i-have-expressroute-circuits-from-different-service-providers"></a>Může mít okruhů ExpressRoute z různé poskytovatele služeb?

Ano. Okruhy ExpressRoute může mít s mnoho poskytovatelů služeb. Každý okruh ExpressRoute je přidružen pouze jedna služba Zprostředkovatel. 

### <a name="i-see-two-expressroute-peering-locations-in-the-same-metro-eg-singapore-and-singapore2-which-peering-location-should-i-choose-to-create-my-expressroute-circuit"></a>Zobrazuje, dvě umístění partnerského vztahu ExpressRoute ve stejné metro, například Singapur a Singapore2. Které umístění partnerského vztahu by měl vybrat pro vytvoření okruhu ExpressRoute?
Pokud váš poskytovatel služeb nabízí ExpressRoute v obou lokalitách, můžete pracovat s poskytovatelem a vyberte buď server a nastavte ExpressRoute. 

### <a name="can-i-have-multiple-expressroute-circuits-in-the-same-metro-can-i-link-them-to-the-same-virtual-network"></a>Může mít více okruhů ExpressRoute ve stejné metro. Můžete propojit je s stejnou virtuální síť?

Ano. Můžete mít více okruhů ExpressRoute zprostředkovatelům stejné nebo jiné služby. Pokud metro má více umístění partnerského vztahu ExpressRoute a okruhy se vytvářejí v různých umístění partnerského vztahu, můžete propojit stejné virtuální síti. Pokud jsou okruhy vytvořené na stejné umístění partnerského vztahu, nelze propojit stejné virtuální síti.

### <a name="how-do-i-connect-my-virtual-networks-to-an-expressroute-circuit"></a>Postup připojení Můj virtuální sítě k okruhu ExpressRoute

Toto jsou základní kroky:

* Vytvoření okruhu ExpressRoute a poskytovatele služeb, povolte ji.
* Můžete, nebo poskytovatele, musíte nakonfigurovat protokol BGP partnerského vztahu (s).
* Propojení virtuální sítě k okruhu ExpressRoute.

Další informace najdete v tématu [zřizování okruhů a stavy okruhů ve workflowech ExpressRoute](expressroute-workflows.md).

### <a name="are-there-connectivity-boundaries-for-my-expressroute-circuit"></a>Existují připojení hranice pro okruh ExpressRoute?

Ano. [ExpressRoute partnery a umístění](expressroute-locations.md) článek obsahuje přehled hranice připojení pro okruh ExpressRoute. Připojení pro okruh ExpressRoute je omezený na jednu geopolitické oblasti. Připojení můžete rozšířit, aby mezi geopolitické oblasti povolením funkci ExpressRoute premium.

### <a name="can-i-link-to-more-than-one-virtual-network-to-an-expressroute-circuit"></a>Můžete propojit více než jeden virtuální sítě k okruhu ExpressRoute?

Ano. Máte na standardní okruh ExpressRoute a až 100 až 10 připojení virtuální sítě na [okruh ExpressRoute premium](#expressroute-premium). 

### <a name="i-have-multiple-azure-subscriptions-that-contain-virtual-networks-can-i-connect-virtual-networks-that-are-in-separate-subscriptions-to-a-single-expressroute-circuit"></a>Mám několik předplatných Azure, které obsahují virtuální sítě. Je možné připojit virtuální sítě, které jsou v samostatných odběrů jeden okruh ExpressRoute?

Ano. Můžete povolit až 10 jiná předplatná Azure, používat jeden okruh ExpressRoute. Toto omezení je možné zvýšit povolení funkce ExpressRoute premium.

Další informace najdete v tématu [sdílení okruh ExpressRoute napříč více předplatných](expressroute-howto-linkvnet-arm.md).

### <a name="i-have-multiple-azure-subscriptions-associated-to-different-azure-active-directory-tenants-or-enterprise-agreement-enrollments-can-i-connect-virtual-networks-that-are-in-separate-tenants-and-enrollments-to-a-single-expressroute-circuit-not-in-the-same-tenant-or-enrollment"></a>Je nutné víc předplatných Azure, které jsou přidružené k jiných klientů Azure Active Directory nebo smlouva Enterprise Agreement registrace. Je možné připojit virtuální sítě, které jsou v samostatných klientů a registrace na jeden okruh ExpressRoute není ve stejné klienta nebo registraci?

Ano. Povolení ExpressRoute může mít rozsah hranice předplatné, klienta a registrace s bez další nezbytné konfigurace. 

Další informace najdete v tématu [sdílení okruh ExpressRoute napříč více předplatných](expressroute-howto-linkvnet-arm.md).

### <a name="are-virtual-networks-connected-to-the-same-circuit-isolated-from-each-other"></a>Připojeni ke stejnému okruhu navzájem izolované virtuální sítě?

Ne. Všechny virtuální sítě propojené ke stejnému okruhu ExpressRoute z hlediska směrování jsou součástí stejné domény směrování a nejsou od sebe navzájem oddělené. Pokud potřebujete izolace trasy, budete muset vytvořit samostatné okruh ExpressRoute.

### <a name="can-i-have-one-virtual-network-connected-to-more-than-one-expressroute-circuit"></a>Může mít jednu virtuální síť připojen k více než jeden okruh ExpressRoute?

Ano. Můžete propojit jedné virtuální sítě s až čtyři okruhy ExpressRoute. Musejí být seřazeny prostřednictvím čtyři různé [umístění ExpressRoute](expressroute-locations.md).

### <a name="can-i-access-the-internet-from-my-virtual-networks-connected-to-expressroute-circuits"></a>Z mé virtuální sítě připojený k okruhy ExpressRoute, přístup k Internetu?

Ano. Pokud nebyly inzerované výchozí trasy (0.0.0.0/0) nebo předpony trasy Internet prostřednictvím relace protokolu BGP, můžete se připojit k Internetu z virtuální sítě propojené s okruhem ExpressRoute.

### <a name="can-i-block-internet-connectivity-to-virtual-networks-connected-to-expressroute-circuits"></a>Můžete blokovat připojení k Internetu k virtuálním sítím, které jsou připojené k okruhy ExpressRoute?

Ano. Můžete prezentovat výchozí směrování (0.0.0.0/0) blokování všech připojení k Internetu pro virtuální počítače nasazené v rámci virtuální sítě a směrovat všechny přenosy se prostřednictvím okruhu ExpressRoute.

Pokud jste prezentovat výchozí směrování, jsme vynutit ke službám nabízeným přes veřejné zpět partnerského vztahu (například úložiště Azure a databázi SQL) a vaše místní provoz. Budete muset nakonfigurovat směrovače se vraťte provoz do Azure prostřednictvím cesty veřejného partnerského vztahu nebo přes Internet. Pokud jste povolili koncového bodu služby (preview) pro službu, není vynutit vaše místní provoz na službu. Provoz zůstane v rámci Azure páteřní síti. Další informace o koncových bodů služby najdete v tématu [koncové body služby virtuální sítě](../virtual-network/virtual-network-service-endpoints-overview.md?toc=%2fazure%2fexpressroute%2ftoc.json)

### <a name="can-virtual-networks-linked-to-the-same-expressroute-circuit-talk-to-each-other"></a>Mohou mezi sebou komunikovat virtuální sítě propojené ke stejnému okruhu ExpressRoute?

Ano. Virtuální počítače nasazené ve virtuálních sítích, které jsou připojené ke stejnému okruhu ExpressRoute můžete vzájemně komunikovat.

### <a name="can-i-use-site-to-site-connectivity-for-virtual-networks-in-conjunction-with-expressroute"></a>Můžete použít připojení site-to-site pro virtuální sítě ve spojení s ExpressRoute?

Ano. ExpressRoute mohou existovat vedle sebe sítě site-to-site VPN.

### <a name="can-i-move-a-virtual-network-from-site-to-site--point-to-site-configuration-to-use-expressroute"></a>Můžete přesunout virtuální sítě z konfigurace site-to-site / point-to-site pomocí ExpressRoute?

Ano. Budete muset vytvořit bránu ExpressRoute v rámci virtuální sítě. Existuje malé výpadek spojených s procesem.

### <a name="why-is-there-a-public-ip-address-associated-with-the-expressroute-gateway-on-a-virtual-network"></a>Proč se veřejné IP adresy přidružené k bránu virtuální sítě?

Veřejná IP adresa se používá pro interní správu jenom a nepředstavuje ohrožení zabezpečení vaší virtuální sítě.

### <a name="what-do-i-need-to-connect-to-azure-storage-over-expressroute"></a>Co je potřeba připojit k úložišti Azure přes ExpressRoute?

Musíte vytvořit okruh ExpressRoute a nakonfigurovat trasy pro veřejný partnerský vztah.

### <a name="are-there-limits-on-the-number-of-routes-i-can-advertise"></a>Existují omezení počtu tras, které můžete umisťovat reklamy?

Ano. Přijímáme až 4000 předpon trasy pro soukromý partnerský vztah a 200 veřejného partnerského vztahu a partnerského vztahu Microsoftu. Můžete to zvýšit na 10 000 tras pro soukromý partnerský vztah, pokud povolíte funkci ExpressRoute premium.

### <a name="are-there-restrictions-on-ip-ranges-i-can-advertise-over-the-bgp-session"></a>Existují omezení IP adres I můžete inzerovat přes relaci protokolu BGP?

Ve veřejných i relaci partnerského vztahu protokolu BGP Microsoft jsme nepřijímají privátní předpony (RFC1918).

### <a name="what-happens-if-i-exceed-the-bgp-limits"></a>Co se stane, když být delší než protokolu BGP omezuje?

Relace protokolu BGP se zahodí. Bude resetován po počet předponu přejde pod limit.

### <a name="what-is-the-expressroute-bgp-hold-time-can-it-be-adjusted"></a>Co je doba uchování ExpressRoute BGP? Lze jej upravit?

Doba uchování je 180. Zprávy keep-alive odešlou každých 60 sekund. Tyto jsou pevně dané nastavení na straně společnosti Microsoft, kterou nelze změnit. Je možné, můžete nakonfigurovat jiné časovače a bude odpovídajícím způsobem vyjednávat parametry relace protokolu BGP.

### <a name="after-i-advertise-the-default-route-00000-to-my-virtual-networks-i-cant-activate-windows-running-on-my-azure-vms-how-to-i-fix-this"></a>Po I prezentovat výchozí trasa (0.0.0.0/0) Můj virtuální sítě, nelze aktivovat systém Windows spuštěn na můj virtuálních počítačích Azure. Jak na lze tento problém odstranit?

Následující postup pomůže rozpoznat požadavek na aktivaci Azure:

1. Vytvoření veřejného partnerského vztahu pro váš okruh ExpressRoute.
2. Provádět vyhledávání DNS a vyhledat IP adresu **kms.core.windows.net**
3. Služba správy klíčů musí rozpoznat, že žádost o aktivaci pochází z Azure a dodržet žádosti. Proveďte jeden z následujících tří úloh:

   * V místní síti směrujte provoz určený pro IP adresu, kterou jste získali v kroku 2 zpět do Azure prostřednictvím veřejného partnerského vztahu.
   * Máte NSP zprostředkovatele kříž pin provozu zpět do Azure prostřednictvím veřejného partnerského vztahu.
   * Vytvoření trasy definované uživatelem, který odkazuje IP, která má Internetu jako další segment a použijte ho pro podsítí, kde jsou tyto virtuální počítače.

### <a name="can-i-change-the-bandwidth-of-an-expressroute-circuit"></a>Můžete změnit šířku pásma okruhu ExpressRoute?

Ano, pokuste se zvětšit šířku pásma okruhu ExpressRoute na portálu Azure nebo pomocí prostředí PowerShell. Pokud na fyzický port na kterém byla vytvořena váš okruh je dostupná kapacita, bude úspěšná změna. 

Pokud změny selže, znamená to buď není k dispozici dostatek kapacita na aktuální port vlevo a budete muset vytvořit nové okruh ExpressRoute s větší šířku pásma, nebo v tomto umístění není žádné další kapacitu, v takovém případě nebudete moct zvětšit šířku pásma. 

Bude mít i po u svého poskytovatele připojení zajistit aktualizaci omezení v rámci svých sítí na podporu zvýšení šířky pásma. Nelze, ale snížit šířku pásma okruhu ExpressRoute. Budete muset vytvořit nové okruh ExpressRoute s menší šířkou pásma a odstranit staré okruh.

### <a name="how-do-i-change-the-bandwidth-of-an-expressroute-circuit"></a>Jak změnit šířku pásma okruhu ExpressRoute?

Můžete aktualizovat šířku pásma okruhu ExpressRoute pomocí rozhraní API REST nebo rutiny prostředí PowerShell.

## <a name="expressroute-premium"></a>ExpressRoute premium

### <a name="what-is-expressroute-premium"></a>Co je ExpressRoute premium?

ExpressRoute premium je kolekce následující funkce:

* Vyšší směrovací tabulky limit z 4000 tras na 10 000 tras pro soukromý partnerský vztah.
* Zvýšit počet virtuálních sítí, který může být připojen k okruhu ExpressRoute (výchozí hodnota je 10). Další informace najdete v tématu [ExpressRoute omezení](#limits) tabulky.
* Připojení k Office 365 a Dynamics 365.
* Globální připojení prostřednictvím základní sítě Microsoft. Nyní můžete propojit virtuální síť v geopolitické oblasti jeden okruh ExpressRoute v jiné oblasti.<br>
    **Příklady:**

    *  Můžete propojit virtuální sítě vytvořené v západní Evropa pro okruh ExpressRoute vytvořený v Silicon Valley. 
    *  Ve veřejném partnerském vztahu jsou inzerované předpony z jiných geopolitických oblastí, tak, aby se můžete připojit k, například SQL Azure v západní Evropa z okruhu ze Silicon Valley.


### <a name="limits"></a>Tom, kolik virtuálních sítí můžete propojit s okruhem ExpressRoute Pokud I povolené ExpressRoute premium?

Následující tabulky popisují omezení ExpressRoute a počet virtuálních sítí na jeden okruh ExpressRoute:

[!INCLUDE [ExpressRoute limits](../../includes/expressroute-limits.md)]

### <a name="how-do-i-enable-expressroute-premium"></a>Povolení ExpressRoute premium

Funkce ExpressRoute premium lze povolit, pokud je tato funkce povolena a může být vypnut tím, že aktualizuje stav okruhu. Povolit v okamžiku vytvoření okruhu ExpressRoute premium, nebo můžete volat rozhraní API REST nebo rutiny prostředí PowerShell.

### <a name="how-do-i-disable-expressroute-premium"></a>Jakým způsobem vypnout ExpressRoute premium?

ExpressRoute premium můžete zakázat pomocí volání rozhraní API REST nebo rutiny prostředí PowerShell. Musí se ujistěte, že máte škálovat vaše připojení musí splňovat výchozí omezení dříve, než zakážete ExpressRoute premium. Pokud vaše využití škáluje nad výchozí omezení, požadavek na zakázání ExpressRoute premium se nezdaří.

### <a name="can-i-pick-and-choose-the-features-i-want-from-the-premium-feature-set"></a>Může I vybrat funkce, která má být ze sady funkcí premium?

Ne. Nelze vybrat funkce. Jsme povolení všech funkcí po zapnutí ExpressRoute premium.

### <a name="how-much-does-expressroute-premium-cost"></a>Kolik stojí ExpressRoute premium?

Odkazovat na [podrobnosti o cenách](https://azure.microsoft.com/pricing/details/expressroute/) náklady.

### <a name="do-i-pay-for-expressroute-premium-in-addition-to-standard-expressroute-charges"></a>Je platit pro ExpressRoute premium kromě standardní poplatky za ExpressRoute?

Ano. ExpressRoute premium poplatky za nad poplatky okruh ExpressRoute a poplatky požadované poskytovatelem připojení.

## <a name="expressroute-for-office-365-and-dynamics-365"></a>ExpressRoute pro Office 365 a Dynamics 365

[!INCLUDE [expressroute-office365-include](../../includes/expressroute-office365-include.md)]

### <a name="how-do-i-create-an-expressroute-circuit-to-connect-to-office-365-services-and-dynamics-365"></a>Vytvoření okruhu ExpressRoute připojit ke službám Office 365 a Dynamics 365

1. Zkontrolujte [stránky požadavky ExpressRoute](expressroute-prerequisites.md) k zajistěte, aby se splnily požadavky.
2. Aby splnění potřeb připojení, projděte si seznam poskytovatelé a umístění v [ExpressRoute partnery a umístění](expressroute-locations.md) článku.
3. Plánování požadavků na kapacitu kontrolou [plánování sítě a optimalizace výkonu pro Office 365](http://aka.ms/tune/).
4. Postupujte podle kroků uvedených v pracovních postupech nastavit propojení [zřizování okruhů a stavy okruhů ve workflowech ExpressRoute](expressroute-workflows.md).

> [!IMPORTANT]
> Ujistěte se, že je povoleno doplněk ExpressRoute premium při konfiguraci připojení ke službám Office 365 a Dynamics 365.
> 
> 

### <a name="do-i-need-to-enable-azure-public-peering-to-connect-to-office-365-services-and-dynamics-365"></a>Je potřeba povolit veřejný partnerský vztah Azure pro připojení ke službám Office 365 a Dynamics 365?

Ne, jenom musíte povolit Peering společnosti Microsoft. Ověřování přenosy do služby Azure AD se odesílají prostřednictvím Peering společnosti Microsoft. 

### <a name="can-my-existing-expressroute-circuits-support-connectivity-to-office-365-services-and-dynamics-365"></a>Můj stávající okruhy ExpressRoute podporuje připojení ke službám Office 365 a Dynamics 365?

Ano. Vaše stávající okruh ExpressRoute může být nakonfigurované pro podporu připojení ke službám Office 365. Ujistěte se, že máte dostatečnou kapacitu k připojení ke službám Office 365 a že je povoleno doplněk premium. [Plánování sítě a optimalizace výkonu pro Office 365](http://aka.ms/tune/) musí vám pomůže s plánováním připojení. Další informace naleznete v [vytvoření a úprava okruhu ExpressRoute](expressroute-howto-circuit-classic.md).

### <a name="what-office-365-services-can-be-accessed-over-an-expressroute-connection"></a>Jaké Office 365 služby je přístupná přes připojení ExpressRoute?

Odkazovat na [Office 365 adresy URL a rozsahy IP adres](http://aka.ms/o365endpoints) stránky pro aktuální seznam služeb podporovaných přes ExpressRoute.

### <a name="how-much-does-expressroute-for-office-365-services-and-dynamics-365-cost"></a>Kolik podporuje ExpressRoute pro služby Office 365 a náklady na Dynamics 365?

Služby Office 365 a Dynamics 365 vyžadují doplněk premium, aby byl povolen. Najdete v článku [stránce s podrobnostmi o cenách](https://azure.microsoft.com/pricing/details/expressroute/) pro náklady.

### <a name="what-regions-is-expressroute-for-office-365-supported-in"></a>Jaké oblasti je ExpressRoute pro Office 365 v podporovaná?

V tématu [ExpressRoute partnery a umístění](expressroute-locations.md) informace.

### <a name="can-i-access-office-365-over-the-internet-even-if-expressroute-was-configured-for-my-organization"></a>Můžete získat přístup k Office 365 přes Internet, i když ExpressRoute byla nakonfigurována pro Moje organizace?

Ano. Koncové body služby Office 365 jsou dostupné prostřednictvím Internetu, i když ExpressRoute byla nakonfigurována pro vaší sítě. Zkontrolujte síťové týmem vaší organizace, pokud síť ve vaší lokalitě je nakonfigurován pro připojení ke službám Office 365 prostřednictvím ExpressRoute.

### <a name="how-can-i-plan-for-high-availability-for-office-365-network-traffic-on-azure-expressroute"></a>Jak se můžete naplánovat pro zajištění vysoké dostupnosti pro Office 365 síťový provoz na Azure ExpressRoute?
Viz doporučení pro [vysoké dostupnosti a převzetí služeb při selhání s Azure ExpressRoute](https://aka.ms/erhighavailability)

### <a name="can-i-access-office-365-us-government-community-gcc-services-over-an-azure-us-government-expressroute-circuit"></a>Můžete přístup služeb Office 365 US Government komunity (RSZ) přes US Government okruhu Azure ExpressRoute?

Ano. Koncové body služby Office 365 RSZ jsou dostupné prostřednictvím Azure US Government ExpressRoute. Však musíte nejprve otevřete lístek podpory na portálu Azure k poskytování předpony, které chcete inzerovat do společnosti Microsoft. Připojení ke službám Office 365 RSZ bude vytvořeno po vyřešení lístku podpory. 

### <a name="can-dynamics-365-for-operations-formerly-known-as-dynamics-ax-online-be-accessed-over-an-expressroute-connection"></a>Dynamics 365 pro operace (dříve označované jako Dynamics AX Online) jsou přístupné přes připojení ExpressRoute?

Ano. [Dynamics 365 pro operace](https://www.microsoft.com/dynamics365/operations) je hostovaná v Azure. Můžete povolit veřejný partnerský vztah Azure na váš okruh ExpressRoute se připojit k němu.

## <a name="route-filters-for-microsoft-peering"></a>Filtry tras pro partnerský vztah Microsoftu

### <a name="i-am-turning-on-microsoft-peering-for-the-first-time-what-routes-will-i-see"></a>I mě zapnete partnerského vztahu Microsoftu poprvé, jaké postupy je se zobrazí?

Neuvidíte žádné trasy. Budete muset připojit filtr trasy pro váš okruh zahájíte předponu oznámení o inzerovaném programu. Pokyny najdete v tématu [filtry konfigurace směrování pro partnerský vztah Microsoftu](how-to-routefilter-powershell.md).

### <a name="i-turned-on-microsoft-peering-and-now-i-am-trying-to-select-exchange-online-but-it-is-giving-me-an-error-that-i-am-not-authorized-to-do-it"></a>Po zapnutí partnerského vztahu Microsoftu a nyní pokouším vyberte Exchange Online, ale ho mi hlásí chybu, že není mohou provést.

Pokud používáte filtry tras, všechny zákazníka můžete zapnout partnerského vztahu Microsoftu. Ale pro využívání služeb Office 365, stále musíte získat oprávnění v rámci služeb Office 365.

### <a name="do-i-need-to-get-authorization-for-turning-on-dynamics-365-over-microsoft-peering"></a>Je potřeba získat autorizaci pro zapnutí Dynamics 365 prostřednictvím partnerského vztahu Microsoftu?

Ne, není nutné autorizace pro Dynamics 365. Můžete vytvořit pravidlo a vyberte Dynamics 365 community bez autorizace.

### <a name="i-enabled-microsoft-peering-prior-to-august-1st-2017-how-can-i-take-advantage-of-route-filters"></a>I povolen partnerský vztah před 1. srpna 2017, jak můžete využít výhod filtry tras Microsoftu?

Existujícího okruhu bude pokračovat inzerování předpony pro Office 365 a Dynamics 365. Pokud chcete přidat oznámení o inzerovaném programu Azure předpony veřejných přes stejné partnerského vztahu Microsoftu, můžete vytvořit filtr trasy, vyberte služby, které potřebujete inzerované (včetně služeb Office 365, ke kterým potřebujete a Dynamics 365) a připojte filtr vaší společnosti Microsoft partnerský vztah. Pokyny najdete v tématu [filtry konfigurace směrování pro partnerský vztah Microsoftu](how-to-routefilter-powershell.md).

### <a name="i-have-microsoft-peering-at-one-location-now-i-am-trying-to-enable-it-at-another-location-and-i-am-not-seeing-any-prefixes"></a>Je nutné na jedno umístění partnerského vztahu Microsoftu, teď pokouším povolit v jiném umístění a nejsou zobrazeny všechny předpony.

* Okruhy ExpressRoute, které byly nakonfigurovány před 1. srpna 2017 partnerského vztahu Microsoftu bude mít všechny služby předpony inzerované prostřednictvím Microsoft partnerský vztah, i když nejsou definovány filtry tras.

* Okruhy ExpressRoute, které jsou nakonfigurované na nebo po 1 srpen 2017 partnerského vztahu Microsoftu nebude mít všechny předpony inzerované dokud trasy filtr je připojen k okruhu. Ve výchozím nastavení se zobrazí bez předpony.
