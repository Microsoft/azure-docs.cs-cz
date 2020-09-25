---
author: ccompy
ms.service: app-service-web
ms.topic: include
ms.date: 06/08/2020
ms.author: ccompy
ms.openlocfilehash: 9259e3d3e41a5ba4986fbef48e745210f80e6093
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91255234"
---
Použití místní integrace virtuální sítě umožňuje aplikacím přístup k těmto akcím:

* Prostředky ve virtuální síti ve stejné oblasti jako vaše aplikace.
* Prostředky v virtuální sítě s partnerským vztahem k virtuální síti, do které je vaše aplikace integrovaná.
* Zabezpečené služby koncového bodu služby.
* Prostředky v rámci připojení Azure ExpressRoute.
* Prostředky ve virtuální síti, do které jste integraci.
* Prostředky napříč partnerskými připojeními, mezi které patří připojení Azure ExpressRoute.
* Soukromé koncové body 

Když použijete integraci virtuální sítě s virtuální sítě ve stejné oblasti, můžete použít tyto funkce sítě Azure:

* **Skupiny zabezpečení sítě (skupin zabezpečení sítě)**: můžete blokovat odchozí přenos pomocí NSG, který je umístěný v podsíti integrace. Příchozí pravidla se nevztahují, protože integraci virtuální sítě nemůžete použít k zajištění příchozího přístupu do vaší aplikace.
* **Směrovací tabulky (udr)**: můžete umístit směrovací tabulku do podsítě Integration pro odeslání odchozího provozu tam, kde chcete.

Ve výchozím nastavení vaše aplikace směruje jenom RFC1918 provoz do vaší virtuální sítě. Pokud chcete směrovat veškerý odchozí provoz do vaší virtuální sítě, použijte nastavení aplikace WEBSITE_VNET_ROUTE_ALL pro vaši aplikaci. Konfigurace nastavení aplikace:

1. Na portálu aplikací přejdete na uživatelské rozhraní **Konfigurace** . Vyberte **Nové nastavení aplikace**.
1. Do pole **název** zadejte **WEBSITE_VNET_ROUTE_ALL** a do pole **hodnota zadejte hodnotu** **1** .

   ![Zadat nastavení aplikace][4]

1. Vyberte **OK**.
1. Vyberte **Uložit**.

Pokud budete směrovat veškerý odchozí provoz do vaší virtuální sítě, bude se jednat o skupin zabezpečení sítě a udr, které se vztahují k vaší podsíti integrace. Při směrování veškerého odchozího provozu do virtuální sítě jsou vaše odchozí adresy stále výstupními adresami, které jsou uvedené ve vlastnostech vaší aplikace, pokud nezadáte trasy k odeslání provozu jinde.

Při použití integrace virtuální sítě s virtuální sítě ve stejné oblasti je potřeba mít určitá omezení:

* Nemůžete se připojit k prostředkům napříč globálními připojeními partnerských vztahů.
* Tato funkce je dostupná jenom z novějších jednotek škálování Azure App Service, které podporují plány App Service PremiumV2. Všimněte si, že *to neznamená, že vaše aplikace musí běžet na cenové úrovni PremiumV2*, jenom to, že musí běžet v plánu App Service, kde je dostupná možnost PremiumV2 (což znamená, že se jedná o novější jednotku škálování, kde je tato funkce integrace virtuální sítě dostupná taky).
* Podsíť Integration můžete použít jenom v jednom plánu App Service.
* Tuto funkci nemůžou používat aplikace pro izolované plánování, které jsou ve App Service Environment.
* Tato funkce vyžaduje nepoužitou podsíť, která je a/27 s 32 adresou nebo větší ve Azure Resource Manager virtuální síti.
* Aplikace a virtuální síť musí být ve stejné oblasti.
* Virtuální síť nejde odstranit pomocí integrované aplikace. Před odstraněním virtuální sítě odeberte integraci.
* Integraci s virtuální sítě můžete integrovat jenom do stejného předplatného jako aplikace.
* Pro každý App Service plán můžete mít jenom jednu místní integraci virtuální sítě. Víc aplikací ve stejném plánu App Service může používat stejnou virtuální síť.
* Pokud máte aplikaci, která používá místní integraci virtuální sítě, nemůžete změnit předplatné aplikace ani plán.
* Vaše aplikace nemůže překládat adresy v Azure DNS Private Zones bez změny konfigurace.

Pro každou instanci plánu se používá jedna adresa. Při škálování aplikace na pět instancí se použije pět adres. Vzhledem k tomu, že velikost podsítě po přiřazení nelze změnit, je nutné použít dostatečně velkou podsíť, aby se vešlo do škálování, které vaše aplikace může dosáhnout. Doporučená velikost je a/26 s 64 adresami. A/26 s 64 adresami přizpůsobil plán Premium s 30 instancemi. Když naplánujete horizontální navýšení nebo snížení kapacity plánu, budete potřebovat dvojnásobný počet adres po krátkou dobu.

Pokud chcete, aby vaše aplikace v jiném plánu dosáhly virtuální sítě, ke které už jsou připojené aplikace v jiném plánu, vyberte jinou podsíť, než kterou používá stávající integrace virtuální sítě.

Tato funkce je plně podporovaná pro aplikace pro Windows i Linux, včetně [vlastních kontejnerů](../articles/app-service/quickstart-custom-container.md). Všechna chování se chovají stejně jako aplikace pro Windows a aplikace pro Linux.

### <a name="service-endpoints"></a>Koncové body služby

Místní integrace virtuální sítě umožňuje používat koncové body služby. Pokud chcete pro vaši aplikaci používat koncové body služby, použijte místní integraci virtuální sítě pro připojení k vybrané virtuální síti a pak nakonfigurujte koncové body služby s cílovou službou v podsíti, kterou jste použili pro integraci. Pokud jste pak chtěli získat přístup k koncovým bodům služby Service Over:

1. Konfigurace místní integrace virtuální sítě pomocí webové aplikace
1. Přejít na cílovou službu a nakonfigurovat koncové body služby proti podsíti používané pro integraci

### <a name="network-security-groups"></a>Skupiny zabezpečení sítě

Skupiny zabezpečení sítě můžete použít k blokování příchozího a odchozího provozu do prostředků ve virtuální síti. Aplikace, která používá místní integraci virtuální sítě, může používat [skupinu zabezpečení sítě][VNETnsg] k blokování odchozího provozu do prostředků ve vaší virtuální síti nebo v Internetu. Chcete-li zablokovat provoz na veřejné adresy, je nutné, aby nastavení aplikace WEBSITE_VNET_ROUTE_ALL nastaveno na hodnotu 1. Příchozí pravidla ve NSG se nevztahují na vaši aplikaci, protože integrace virtuální sítě má vliv jenom na odchozí přenosy z vaší aplikace.

K řízení příchozího provozu do aplikace použijte funkci omezení přístupu. NSG, který se používá pro vaši podsíť integrace, je v platnosti bez ohledu na to, jaké trasy se v podsíti pro integraci nastavily. Pokud je WEBSITE_VNET_ROUTE_ALL nastavené na hodnotu 1 a nemáte žádné trasy, které by ovlivnily provoz veřejných adres v podsíti Integration, bude se veškerý odchozí provoz dál řídit skupin zabezpečení sítě přiřazeným vaší podsíti integrace. Pokud WEBSITE_VNET_ROUTE_ALL není nastavená, skupin zabezpečení sítě se aplikují jenom na RFC1918 provoz.

### <a name="routes"></a>Trasy

Směrovací tabulky můžete použít ke směrování odchozího provozu z vaší aplikace do libovolného, kdykoli potřebujete. Ve výchozím nastavení mají směrovací tabulky vliv jenom na váš cílový provoz RFC1918. Pokud nastavíte WEBSITE_VNET_ROUTE_ALL na 1, budou ovlivněna všechna odchozí volání. Trasy, které jsou nastavené ve vaší podsíti Integration, neovlivní odpovědi na příchozí žádosti o aplikace. Mezi běžné cíle můžou patřit zařízení firewallu nebo brány.

Pokud chcete směrovat veškerý odchozí provoz do místní sítě, můžete k posílání všech odchozích přenosů do brány ExpressRoute použít směrovací tabulku. Pokud provedete směrování provozu do brány, nezapomeňte nastavit trasy v externí síti pro posílání odpovědí zpět.

Trasy Border Gateway Protocol (BGP) ovlivňují také přenosy aplikací. Pokud máte trasy protokolu BGP z nějakého ExpressRoute brány, bude to mít vliv na odchozí přenosy aplikace. Ve výchozím nastavení mají trasy protokolu BGP vliv jenom na váš cílový provoz RFC1918. Pokud je WEBSITE_VNET_ROUTE_ALL nastavené na 1, všechny odchozí přenosy můžou být ovlivněné vašimi trasami protokolu BGP.

### <a name="azure-dns-private-zones"></a>Azure DNS Private Zones 

Jakmile se vaše aplikace integruje s vaší virtuální sítí, používá stejný server DNS, se kterým je nakonfigurovaná vaše virtuální síť. Ve výchozím nastavení vaše aplikace nebude fungovat s Azure DNS Private Zones. Pokud chcete pracovat s Azure DNS Private Zones musíte přidat následující nastavení aplikace:

1. WEBSITE_DNS_SERVER s hodnotou 168.63.129.16 
1. WEBSITE_VNET_ROUTE_ALL s hodnotou 1

Tato nastavení budou posílat všechna vaše odchozí volání z vaší aplikace do vaší virtuální sítě a zároveň umožníte, aby aplikace používala Azure DNS privátní zóny.

### <a name="private-endpoints"></a>Soukromé koncové body

Pokud chcete provést volání do [soukromých koncových bodů][privateendpoints], musíte integrovat s Azure DNS Private Zones nebo spravovat privátní koncový bod na serveru DNS, který používá vaše aplikace. 

<!--Image references-->
[4]: ../includes/media/web-sites-integrate-with-vnet/vnetint-appsetting.png

<!--Links-->
[VNETnsg]: https://docs.microsoft.com/azure/virtual-network/security-overview/
[privateendpoints]: https://docs.microsoft.com/azure/app-service/networking/private-endpoint
