---
author: ccompy
ms.service: app-service-web
ms.topic: include
ms.date: 10/21/2020
ms.author: ccompy
ms.openlocfilehash: 3f9dd35959980eef4e1bec550bf7e9f583cf30d2
ms.sourcegitcommit: f5b8410738bee1381407786fcb9d3d3ab838d813
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/14/2021
ms.locfileid: "98225309"
---
Použití místní integrace virtuální sítě umožňuje aplikacím přístup k těmto akcím:

* Prostředky ve virtuální síti ve stejné oblasti jako vaše aplikace.
* Prostředky v virtuální sítě s partnerským vztahem k virtuální síti, do které je vaše aplikace integrovaná.
* Zabezpečené služby koncového bodu služby.
* Prostředky v rámci připojení Azure ExpressRoute.
* Prostředky ve virtuální síti, do které jste integraci.
* Prostředky napříč partnerskými připojeními, mezi které patří připojení Azure ExpressRoute.
* Privátní koncové body 

Když použijete integraci virtuální sítě s virtuální sítě ve stejné oblasti, můžete použít tyto funkce sítě Azure:

* **Skupiny zabezpečení sítě (skupin zabezpečení sítě)**: můžete blokovat odchozí přenos pomocí NSG, který je umístěný v podsíti integrace. Příchozí pravidla se nevztahují, protože integraci virtuální sítě nemůžete použít k zajištění příchozího přístupu do vaší aplikace.
* **Směrovací tabulky (udr)**: můžete umístit směrovací tabulku do podsítě Integration pro odeslání odchozího provozu tam, kde chcete.

Ve výchozím nastavení vaše aplikace směruje jenom RFC1918 provoz do vaší virtuální sítě. Pokud chcete směrovat veškerý odchozí provoz do vaší virtuální sítě, použijte nastavení aplikace WEBSITE_VNET_ROUTE_ALL pro vaši aplikaci. Konfigurace nastavení aplikace:

1. Na portálu aplikací přejdete na uživatelské rozhraní **Konfigurace** . Vyberte **Nové nastavení aplikace**.
1. Do pole **název** zadejte **WEBSITE_VNET_ROUTE_ALL** a do pole **hodnota zadejte hodnotu** **1** .

   ![Zadat nastavení aplikace][4]

1. Vyberte **OK**.
1. Vyberte **Uložit**.

> [!NOTE]
> Pokud budete směrovat veškerý odchozí provoz do vaší virtuální sítě, bude se jednat o skupin zabezpečení sítě a udr, které se vztahují k vaší podsíti integrace. Při směrování veškerého odchozího provozu do virtuální sítě jsou vaše odchozí adresy stále výstupními adresami, které jsou uvedené ve vlastnostech vaší aplikace, pokud nezadáte trasy k odeslání provozu jinde.

Při použití integrace virtuální sítě s virtuální sítě ve stejné oblasti je potřeba mít určitá omezení:

* Nemůžete se připojit k prostředkům napříč globálními připojeními partnerských vztahů.
* Tato funkce je dostupná ze všech jednotek škálování App Service v Premium v2 a Premium v3. Je k dispozici i na úrovni Standard, ale jenom z novějších jednotek škálování App Service. Pokud používáte starší jednotku škálování, můžete ji použít jenom z plánu Premium v2 App Service. Pokud chcete mít jistotu, že je možné používat funkci v plánu Standard App Service, vytvořte si aplikaci v plánu App Service Premium v3. Tyto plány jsou podporované jenom na našich nejnovějších jednotkách škálování. V případě potřeby můžete horizontální navýšení kapacity snížit.  
* Podsíť Integration můžete použít jenom v jednom plánu App Service.
* Tuto funkci nemůžou používat aplikace pro izolované plánování, které jsou ve App Service Environment.
* Tato funkce vyžaduje nepoužitou podsíť, která je ve virtuální síti Azure Resource Manager a/28 nebo větší.
* Aplikace a virtuální síť musí být ve stejné oblasti.
* Virtuální síť nejde odstranit pomocí integrované aplikace. Před odstraněním virtuální sítě odeberte integraci.
* Pro každý App Service plán můžete mít jenom jednu místní integraci virtuální sítě. Víc aplikací ve stejném plánu App Service může používat stejnou virtuální síť.
* Pokud máte aplikaci, která používá místní integraci virtuální sítě, nemůžete změnit předplatné aplikace ani plán.
* Vaše aplikace nemůže překládat adresy v Azure DNS Private Zones bez změny konfigurace.

Integrace virtuální sítě závisí na použití vyhrazené podsítě.  Při zřizování podsítě neztratí podsíť Azure 5 IP adres na začátku. Jedna adresa se používá z podsítě integrace pro každou instanci plánu. Při škálování aplikace na čtyři instance se použijí čtyři adresy. Hodnota MD z 5 adres z velikosti podsítě znamená, že maximální dostupné adresy na blok CIDR jsou:

- /28 má 11 adres
- /27 má 27 adres
- /26 má 59 adres

Při horizontálním navýšení nebo zmenšení kapacity budete potřebovat dvojnásobnou dobu potřebnou pro vaši adresu. Omezení velikosti znamená, že reálné dostupné instance na velikost podsítě jsou, pokud je vaše podsíť:

- /28, maximální horizontální stupnice je 5 instancí.
- /27, maximální horizontální škálování je 13 instancí.
- /26 je maximální horizontální stupnice 29 instancí

Limity zaznamenané u maximálního vodorovného měřítka předpokládají, že v určitém okamžiku bude nutné horizontální navýšení nebo snížení kapacity v libovolné velikosti nebo SKU. 

Vzhledem k tomu, že velikost podsítě po přiřazení nejde změnit, používejte dostatečně velkou podsíť, která bude vyhovovat libovolné škále aplikace, ke které může dojít. Aby nedocházelo k problémům s kapacitou podsítě, doporučená velikost je/26 a 64 adres.  

Pokud chcete, aby vaše aplikace v jiném plánu dosáhly virtuální sítě, ke které už jsou připojené aplikace v jiném plánu, vyberte jinou podsíť, než kterou používá stávající integrace virtuální sítě.

Tato funkce je plně podporovaná pro aplikace pro Windows i Linux, včetně [vlastních kontejnerů](../articles/app-service/quickstart-custom-container.md). Všechna chování se chovají stejně jako aplikace pro Windows a aplikace pro Linux.

### <a name="service-endpoints"></a>Koncové body služby

Místní integrace virtuální sítě umožňuje používat koncové body služby. Pokud chcete pro vaši aplikaci používat koncové body služby, použijte místní integraci virtuální sítě pro připojení k vybrané virtuální síti a pak nakonfigurujte koncové body služby s cílovou službou v podsíti, kterou jste použili pro integraci. Pokud jste pak chtěli získat přístup k koncovým bodům služby Service Over:

1. Konfigurace místní integrace virtuální sítě pomocí webové aplikace
1. Přejít na cílovou službu a nakonfigurovat koncové body služby proti podsíti používané pro integraci

### <a name="network-security-groups"></a>skupiny zabezpečení sítě,

Skupiny zabezpečení sítě můžete použít k blokování příchozího a odchozího provozu do prostředků ve virtuální síti. Aplikace, která používá místní integraci virtuální sítě, může používat [skupinu zabezpečení sítě][VNETnsg] k blokování odchozího provozu do prostředků ve vaší virtuální síti nebo v Internetu. Chcete-li zablokovat provoz na veřejné adresy, je nutné, aby nastavení aplikace WEBSITE_VNET_ROUTE_ALL nastaveno na hodnotu 1. Příchozí pravidla ve NSG se nevztahují na vaši aplikaci, protože integrace virtuální sítě má vliv jenom na odchozí přenosy z vaší aplikace.

K řízení příchozího provozu do aplikace použijte funkci omezení přístupu. NSG, který se používá pro vaši podsíť integrace, je v platnosti bez ohledu na to, jaké trasy se v podsíti pro integraci nastavily. Pokud je WEBSITE_VNET_ROUTE_ALL nastavené na hodnotu 1 a nemáte žádné trasy, které by ovlivnily provoz veřejných adres v podsíti Integration, bude se veškerý odchozí provoz dál řídit skupin zabezpečení sítě přiřazeným vaší podsíti integrace. Pokud WEBSITE_VNET_ROUTE_ALL není nastavená, skupin zabezpečení sítě se aplikují jenom na RFC1918 provoz.

### <a name="routes"></a>Trasy

Směrovací tabulky můžete použít ke směrování odchozího provozu z vaší aplikace do libovolného, kdykoli potřebujete. Ve výchozím nastavení mají směrovací tabulky vliv jenom na váš cílový provoz RFC1918. Pokud nastavíte WEBSITE_VNET_ROUTE_ALL na 1, budou ovlivněna všechna odchozí volání. Trasy, které jsou nastavené ve vaší podsíti Integration, neovlivní odpovědi na příchozí žádosti o aplikace. Mezi běžné cíle můžou patřit zařízení firewallu nebo brány.

Pokud chcete směrovat veškerý odchozí provoz do místní sítě, můžete k posílání všech odchozích přenosů do brány ExpressRoute použít směrovací tabulku. Pokud provedete směrování provozu do brány, nezapomeňte nastavit trasy v externí síti pro posílání odpovědí zpět.

Trasy Border Gateway Protocol (BGP) ovlivňují také přenosy aplikací. Pokud máte trasy protokolu BGP z nějakého ExpressRoute brány, bude to mít vliv na odchozí přenosy aplikace. Ve výchozím nastavení mají trasy protokolu BGP vliv jenom na váš cílový provoz RFC1918. Pokud je WEBSITE_VNET_ROUTE_ALL nastavené na 1, všechny odchozí přenosy můžou být ovlivněné vašimi trasami protokolu BGP.

### <a name="azure-dns-private-zones"></a>Azure DNS Private Zones 

Jakmile se vaše aplikace integruje s vaší virtuální sítí, používá stejný server DNS, se kterým je nakonfigurovaná vaše virtuální síť. Ve výchozím nastavení vaše aplikace nebude fungovat s Azure DNS Private Zones. Chcete-li pracovat s Azure DNS Private Zones, je nutné přidat následující nastavení aplikace:


1. WEBSITE_DNS_SERVER s hodnotou 168.63.129.16
1. WEBSITE_VNET_ROUTE_ALL s hodnotou 1


Tato nastavení budou posílat všechna vaše odchozí volání z vaší aplikace do vaší virtuální sítě a zároveň umožníte, aby aplikace používala Azure DNS privátní zóny.   Tato nastavení budou posílat všechna odchozí volání z vaší aplikace do vaší virtuální sítě. Kromě toho umožní aplikaci použít Azure DNS dotazem Privátní DNS zóny na úrovni pracovního procesu. Tato funkce se použije, když běžící aplikace přistupuje k zóně Privátní DNS.

> [!NOTE]
>Pokus o přidání vlastní domény do webové aplikace pomocí Privátní DNS zóny není možné použít s Integrace virtuální sítě. Vlastní ověření domény se provádí na úrovni kontroleru, nikoli na úrovni pracovního procesu, což brání tomu, aby se záznamy DNS zobrazily. Pokud chcete použít vlastní doménu ze zóny Privátní DNS, musí být ověření obejít pomocí Application Gateway nebo interního nástroje App Service Environment.

### <a name="private-endpoints"></a>Privátní koncové body

Pokud chcete provést volání do [soukromých koncových bodů][privateendpoints], je nutné zajistit, aby se vaše vyhledávání DNS přeložilo na soukromý koncový bod. Aby vyhledávání DNS z vaší aplikace odkazovalo na vaše soukromé koncové body, můžete:

* integrace s Azure DNS Private Zones. Pokud vaše virtuální síť nemá vlastní server DNS, bude tato funkce automaticky
* Spravujte privátní koncový bod na serveru DNS, který používá vaše aplikace. K tomu potřebujete znát adresu privátního koncového bodu a pak na koncový bod, na který se pokoušíte připojit, nasměrovat záznam A.
* Konfigurace vlastního serveru DNS pro přeposílání na Azure DNS privátní zóny

<!--Image references-->
[4]: ../includes/media/web-sites-integrate-with-vnet/vnetint-appsetting.png

<!--Links-->
[VNETnsg]: /azure/virtual-network/security-overview/
[privateendpoints]: ../articles/app-service/networking/private-endpoint.md
