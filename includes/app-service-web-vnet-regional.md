---
author: ccompy
ms.service: app-service-web
ms.topic: include
ms.date: 02/27/2020
ms.author: ccompy
ms.openlocfilehash: c731e2a7d4da1a66aabb50b335d526fbb6a0a302
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78671535"
---
Použití místní integrace virtuální sítě umožňuje vaší aplikaci přístup:

* prostředky ve virtuální síti ve stejné oblasti, kterou integrujete s 
* prostředky ve virtuálních sítích přihlížené do vaší virtuální sítě, které jsou ve stejné oblasti
* zabezpečené služby koncového bodu služby
* prostředky napříč připojeními ExpressRoute
* prostředky ve virtuální síti, ke které jste připojeni
* prostředky napříč partnerskými připojeními včetně připojení ExpressRoute
* privátní koncové body 

Při použití integrace virtuální sítě s virtuálními sítěmi ve stejné oblasti můžete použít následující funkce Azure Networking:

* Skupiny zabezpečení sítě ( skupiny nsgs) - můžete blokovat odchozí provoz se skupinou zabezpečení sítě, která je umístěna v podsíti integrace. Příchozí pravidla neplatí, protože nelze použít integraci virtuální sítě k poskytování příchozí přístup k vaší aplikaci.
* Směrovací tabulky (UDR) – Do podsítě integrace můžete umístit směrovací tabulku a odeslat odchozí provoz tam, kde chcete. 

Ve výchozím nastavení bude vaše aplikace směrovat jenom provoz RFC1918 do vaší virtuální sítě. Pokud chcete směrovat všechny odchozí provoz do virtuální sítě, použijte nastavení aplikace WEBSITE_VNET_ROUTE_ALL na vaši aplikaci. Postup konfigurace nastavení aplikace:

1. Přejděte na portálu aplikace do možnosti konfiguračního uznatí. Vybrat **nastavení nové aplikace**
1. Zadejte **WEBSITE_VNET_ROUTE_ALL** do pole Název a **1** do pole Hodnota.

   ![Poskytnout nastavení aplikace][4]

1. Vybrat **OK**
1. Vyberte **Uložit**.

Pokud směrujete všechny odchozí provoz do virtuální sítě, bude podléhat skupinám nsg a UDR, které se použijí pro podsíť integrace. Při směrování všech odchozích přenosů do virtuální sítě budou vaše odchozí adresy stále odchozí adresy, které jsou uvedeny ve vlastnostech aplikace, pokud neposkytnete trasy pro odeslání provozu jinam. 

Existují určitá omezení s použitím integrace virtuální sítě s virtuálními sítěmi ve stejné oblasti:

* Nelze dosáhnout prostředků napříč globálními partnerskými sítěmi
* Tato funkce je k dispozici pouze z novějších jednotek škálování služby App Service, které podporují plány služby PremiumV2 App Service.
* Integrační podsíť může používat pouze jeden plán služby App Service.
* Tuto funkci nelze používat aplikace izolovaného plánu, které jsou v prostředí služby App Service.
* Tato funkce vyžaduje nepoužívanou podsíť, která je /27 s 32 adresami nebo větší ve virtuální síti Správce prostředků
* Aplikace a virtuální síť musí být ve stejné oblasti
* Virtuální síť nelze odstranit pomocí integrované aplikace. Před odstraněním virtuální sítě odeberte integraci. 
* Můžete integrovat jenom s virtuálními sítěmi ve stejném předplatném jako aplikace
* Můžete mít jenom jednu regionální integraci virtuální sítě podle plánu služby App Service. Více aplikací ve stejném plánu služby App Service můžete použít stejnou virtuální síť. 
* Předplatné aplikace nebo plánu nemůžete změnit, když je aplikace, která používá místní integraci virtuální sítě.

Pro každou instanci plánu se používá jedna adresa. Pokud změníte velikost aplikace na pět instancí, použije se pět adres. Vzhledem k tomu, že velikost podsítě nelze po přiřazení změnit, musíte použít podsíť, která je dostatečně velká, aby vyhovovala všem měřítkům, na které vaše aplikace může dosáhnout. Doporučená velikost je A /26 s 64 adresami. A /26 s 64 adresami bude vyhovovat plánu Premium s 30 instancemi. Při škálování plánu nahoru nebo dolů, budete potřebovat dvakrát tolik adres na krátkou dobu. 

Pokud chcete, aby vaše aplikace v jiném plánu dosáhly virtuální sítě, která je připojena k již aplikacemi v jiném plánu, musíte vybrat jinou podsíť, než kterou používá již existující integrace virtuální sítě.  

Tato funkce je ve verzi preview pro Linux. Linuxová forma funkce podporuje pouze volání na adresy RFC 1918 (10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16).

### <a name="web--function-app-for-containers"></a>Webová / funkční aplikace pro kontejnery

Pokud vlastníte aplikaci na Linuxu s integrovanými bitovými kopiemi, regionální integrace virtuální sítě funguje bez dalších změn. Pokud používáte webovou / funkční aplikaci pro kontejnery, musíte upravit image dockeru, abyste mohli používat integraci virtuální sítě. V bitové kopii dockeru použijte proměnnou prostředí PORT jako naslouchací port hlavního webového serveru namísto pevného čísla portu. Proměnná prostředí PORT je automaticky nastavena platformou v době spuštění kontejneru. Pokud používáte SSH, pak musí být demon SSH nakonfigurován tak, aby naslouchal číslu portu určenému proměnnou prostředí SSH_PORT při použití regionální integrace virtuální sítě.  Neexistuje žádná podpora pro bránu požadované integrace virtuální sítě na Linuxu. 

### <a name="service-endpoints"></a>Koncové body služby

Regionální integrace virtuální sítě umožňuje používat koncové body služby.  Pokud chcete s vaší aplikací používat koncové body služby, připojte se k vybrané virtuální síti k místní integraci virtuální sítě a pak nakonfigurujte koncové body služby v podsíti, kterou jste použili pro integraci. 

### <a name="network-security-groups"></a>Network Security Groups (Skupiny zabezpečení sítě)

Skupiny zabezpečení sítě umožňují blokovat příchozí a odchozí provoz na prostředky ve virtuální síti. Aplikace využívající místní integraci virtuální sítě může pomocí [skupiny zabezpečení sítě][VNETnsg] blokovat odchozí provoz na prostředky ve vaší virtuální síti nebo na Internetu. Chcete-li blokovat provoz na veřejné adresy, musíte mít nastavení aplikace WEBSITE_VNET_ROUTE_ALL nastavena na 1. Příchozí pravidla v souboru sítě sítě se nevztahují na vaši aplikaci, protože integrace virtuální sítě ovlivňuje jenom odchozí provoz z vaší aplikace. Chcete-li řídit příchozí provoz do aplikace, použijte funkci Omezení přístupu. Skupina nsg, která je použita pro podsíť integrace, bude účinná bez ohledu na všechny trasy použité pro podsíť integrace. Pokud WEBSITE_VNET_ROUTE_ALL byla nastavena na 1 a nemáte žádné trasy ovlivňující provoz veřejné adresy v podsíti integrace, všechny odchozí provoz y stále podléhají skupinám nsg přiřazených k podsíti integrace. Pokud WEBSITE_VNET_ROUTE_ALL nebylnastaven, sítě sítě nsg by se použily pouze pro přenosy RFC1918.

### <a name="routes"></a>Trasy

Tabulky tras umožňují směrovat odchozí provoz z aplikace kamkoli chcete. Ve výchozím nastavení ovlivní směrovací tabulky pouze cílový provoz RFC1918.  Pokud nastavíte WEBSITE_VNET_ROUTE_ALL na 1, budou ovlivněny všechny odchozí hovory. Trasy, které jsou nastaveny v podsíti integrace, neovlivní odpovědi na příchozí žádosti o aplikace. Mezi běžné cíle patří zařízení brány firewall nebo brány. Pokud chcete směrovat všechny odchozí přenosy místně, můžete použít směrovací tabulku k odeslání veškerého odchozího provozu do brány ExpressRoute. Pokud směrujete provoz do brány, nezapomeňte nastavit trasy v externí síti, aby se všechny odpovědi odeslala zpět.

Trasy protokolu Brána (Border Gateway Protocol) ovlivní také provoz aplikace. Pokud máte trasy BGP z něčeho jako brána ExpressRoute, bude ovlivněn odchozí provoz aplikace. Ve výchozím nastavení budou trasy Protokolu BGP mít vliv pouze na cílový provoz RFC1918. Pokud je WEBSITE_VNET_ROUTE_ALL nastavena na 1, mohou být všechny odchozí přenosy ovlivněny trasami Protokolu BGP. 


<!--Image references-->
[4]: ../includes/media/web-sites-integrate-with-vnet/vnetint-appsetting.png

<!--Links-->
[VNETnsg]: https://docs.microsoft.com/azure/virtual-network/security-overview/