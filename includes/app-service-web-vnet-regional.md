---
author: ccompy
ms.service: app-service-web
ms.topic: include
ms.date: 02/27/2020
ms.author: ccompy
ms.openlocfilehash: c731e2a7d4da1a66aabb50b335d526fbb6a0a302
ms.sourcegitcommit: bc792d0525d83f00d2329bea054ac45b2495315d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/06/2020
ms.locfileid: "78671535"
---
Použití místní integrace virtuální sítě umožňuje aplikacím přístup k těmto akcím:

* prostředky ve virtuální síti ve stejné oblasti, ve které jste integraci s nástrojem 
* prostředky v virtuální sítě s partnerským vztahem k virtuální síti, které jsou ve stejné oblasti
* zabezpečené služby koncového bodu služby
* prostředky mezi ExpressRoute připojeními
* prostředky ve virtuální síti, ke které jste připojení
* prostředky napříč partnerskými připojeními, včetně připojení ExpressRoute
* Soukromé koncové body 

Při použití integrace virtuální sítě s virtuální sítě ve stejné oblasti můžete použít následující funkce sítě Azure:

* Skupiny zabezpečení sítě (skupin zabezpečení sítě) – odchozí přenosy můžete blokovat pomocí skupiny zabezpečení sítě, která je umístěná v podsíti integrace. Příchozí pravidla neplatí, protože nemůžete použít integraci virtuální sítě k poskytnutí příchozího přístupu do vaší aplikace.
* Směrovací tabulky (udr) – směrovací tabulku můžete umístit do podsítě Integration, abyste mohli odesílat odchozí přenosy tam, kde chcete. 

Ve výchozím nastavení bude vaše aplikace směrovat jenom RFC1918 provoz do vaší virtuální sítě. Pokud chcete směrovat veškerý odchozí provoz do vaší virtuální sítě, použijte nastavení aplikace WEBSITE_VNET_ROUTE_ALL pro vaši aplikaci. Konfigurace nastavení aplikace:

1. Na portálu aplikací přejdete na uživatelské rozhraní konfigurace. Vybrat **nové nastavení aplikace**
1. Do pole název zadejte **WEBSITE_VNET_ROUTE_ALL** a do pole hodnota zadejte hodnotu **1** .

   ![Zadat nastavení aplikace][4]

1. Vyberte **OK**.
1. Vyberte **Uložit**.

Pokud budete směrovat veškerý odchozí provoz do virtuální sítě, bude se vztahovat na skupin zabezpečení sítě a udr, které se použijí pro vaši podsíť integrace. Při směrování veškerého odchozího provozu do virtuální sítě budou vaše odchozí adresy pořád odchozími adresami, které jsou uvedené ve vlastnostech vaší aplikace, pokud nezadáte trasy k odeslání provozu jinde. 

Při použití integrace virtuální sítě s virtuální sítě ve stejné oblasti je potřeba mít určitá omezení:

* Nemůžete se připojit k prostředkům napříč globálními připojeními partnerských vztahů.
* Tato funkce je dostupná jenom z novějších jednotek škálování App Service, které podporují plány App Service PremiumV2.
* Podsíť integrace se dá použít jenom v jednom plánu App Service.
* Tuto funkci nemůže používat aplikace izolovaného plánu, které jsou ve App Service Environment.
* Tato funkce vyžaduje nepoužitou podsíť, která je/27 s 32 adresou nebo větší ve Správce prostředků VNet.
* Aplikace a virtuální síť musí být ve stejné oblasti.
* Virtuální síť nejde odstranit pomocí integrované aplikace. Před odstraněním virtuální sítě odeberte integraci. 
* Integraci s virtuální sítě můžete integrovat jenom do stejného předplatného jako aplikace.
* Pro každý App Service plán můžete mít jenom jednu místní integraci virtuální sítě. Víc aplikací ve stejném plánu App Service může používat stejnou virtuální síť. 
* Pokud máte aplikaci, která používá místní integraci virtuální sítě, nemůžete změnit předplatné aplikace ani plán.

Pro každou instanci plánu se používá jedna adresa. Při škálování aplikace na pět instancí se použije pět adres. Vzhledem k tomu, že velikost podsítě po přiřazení nelze změnit, je nutné použít podsíť, která je dostatečně velká, aby mohla pojmout libovolné škálování vaší aplikace. Doporučená velikost je a/26 s 64 adresami. A/26 s 64 adresami budou mít plán Premium s 30 instancemi. Když naplánujete horizontální navýšení nebo snížení kapacity plánu, budete potřebovat dvojnásobný počet adres po krátkou dobu. 

Pokud chcete, aby vaše aplikace v jiném plánu dosáhly virtuální sítě, ke které už aplikace v jiném plánu připojuje, je nutné vybrat jinou podsíť, než kterou používá již existující integrace virtuální sítě.  

Tato funkce je ve verzi Preview pro Linux. Forma systému Linux funkce podporuje pouze volání adres RFC 1918 (10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16).

### <a name="web--function-app-for-containers"></a>Web/Function App pro kontejnery

Pokud vaše aplikace hostuje v systému Linux pomocí integrovaných imagí, místní integrace virtuální sítě funguje bez dalších změn. Pokud používáte web/Function App pro kontejnery, je nutné upravit image Docker, aby bylo možné použít integraci virtuální sítě. V imagi Docker použijte proměnnou prostředí portu jako port naslouchání hlavního webového serveru namísto použití pevně zakódované čísla portu. Proměnná prostředí portu je automaticky nastavená platformou v době spuštění kontejneru. Pokud používáte SSH, musí být démon procesu SSH nakonfigurovaný tak, aby naslouchal na čísle portu určeném proměnnou prostředí SSH_PORT při použití místní integrace virtuální sítě.  V systému Linux není dostupná žádná podpora pro bránu VNet. 

### <a name="service-endpoints"></a>Koncové body služeb

Místní integrace virtuální sítě umožňuje používat koncové body služby.  Pokud chcete pro svou aplikaci používat koncové body služby, použijte místní integraci virtuální sítě pro připojení k vybrané virtuální síti a pak nakonfigurujte koncové body služby v podsíti, kterou jste použili pro integraci. 

### <a name="network-security-groups"></a>Network Security Groups (Skupiny zabezpečení sítě)

Skupiny zabezpečení sítě umožňují blokovat příchozí a odchozí provoz do prostředků ve virtuální síti. Aplikace, která používá místní integraci virtuální sítě, může používat [skupinu zabezpečení sítě][VNETnsg] k blokování odchozího provozu do prostředků ve vaší virtuální síti nebo v Internetu. Chcete-li zablokovat provoz na veřejné adresy, je nutné, aby nastavení aplikace WEBSITE_VNET_ROUTE_ALL nastaveno na hodnotu 1. Příchozí pravidla ve NSG se nevztahují na vaši aplikaci, protože integrace virtuální sítě má vliv jenom na odchozí přenosy z vaší aplikace. K řízení příchozího provozu do aplikace použijte funkci omezení přístupu. NSG, který se aplikuje na vaši podsíť integrace, bude platit bez ohledu na to, jaké trasy jste v podsíti pro integraci nastavili. Pokud byla WEBSITE_VNET_ROUTE_ALL nastavena na hodnotu 1 a žádné trasy, které mají vliv na provoz veřejných adres v podsíti integrace, budou mít všechny odchozí přenosy i nadále skupin zabezpečení sítě přiřazené k podsíti integrace. Pokud není WEBSITE_VNET_ROUTE_ALL nastavené, skupin zabezpečení sítě se použije jenom na RFC1918 provoz.

### <a name="routes"></a>Trasy

Směrovací tabulky vám umožní směrovat odchozí přenosy z vaší aplikace do libovolného, kdykoli potřebujete. Ve výchozím nastavení budou směrovací tabulky mít vliv pouze na váš cílový provoz RFC1918.  Pokud WEBSITE_VNET_ROUTE_ALL nastavíte na 1, bude to mít vliv na všechna odchozí volání. Trasy, které jsou nastavené ve vaší podsíti Integration, nebudou mít vliv na odpovědi na příchozí žádosti o aplikace. Mezi běžné cíle můžou patřit zařízení firewallu nebo brány. Pokud chcete směrovat veškerý odchozí provoz do místní sítě, můžete k posílání všech odchozích přenosů do brány ExpressRoute použít směrovací tabulku. Pokud provedete směrování provozu do brány, nezapomeňte nastavit trasy v externí síti pro posílání odpovědí zpět.

Trasy protokolu Border Gateway Protocol (BGP) budou mít vliv i na přenosy aplikací. Pokud máte trasy protokolu BGP z nějakého ExpressRoute brány, bude to mít vliv na odchozí přenosy aplikace. Ve výchozím nastavení budou trasy protokolu BGP ovlivnit jenom váš cílový provoz RFC1918. Pokud je WEBSITE_VNET_ROUTE_ALL nastavené na 1, všechny odchozí přenosy můžou být ovlivněné vašimi trasami protokolu BGP. 


<!--Image references-->
[4]: ../includes/media/web-sites-integrate-with-vnet/vnetint-appsetting.png

<!--Links-->
[VNETnsg]: https://docs.microsoft.com/azure/virtual-network/security-overview/