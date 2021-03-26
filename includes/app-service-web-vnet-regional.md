---
author: ccompy
ms.service: app-service-web
ms.topic: include
ms.date: 10/21/2020
ms.author: ccompy
ms.openlocfilehash: 821746856cb37781c8f6a2e58659ce7db43e1479
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2021
ms.locfileid: "105609432"
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

Ve výchozím nastavení vaše aplikace směruje jenom RFC1918 provoz do vaší virtuální sítě. Pokud chcete směrovat veškerý odchozí provoz do vaší virtuální sítě, použijte následující postup a přidejte `WEBSITE_VNET_ROUTE_ALL` nastavení do aplikace: 

1. Na portálu aplikací přejdete na uživatelské rozhraní **Konfigurace** . Vyberte **Nové nastavení aplikace**.
1. `WEBSITE_VNET_ROUTE_ALL`Do pole **název** zadejte a zadejte `1` **hodnotu** do pole hodnota.

   ![Zadat nastavení aplikace][4]

1. Vyberte **OK**.
1. Vyberte **Uložit**.

> [!NOTE]
> Při směrování veškerého odchozího provozu do virtuální sítě se vztahují na skupin zabezpečení sítě a udr, které se vztahují k vaší podsíti integrace. Pokud `WEBSITE_VNET_ROUTE_ALL` je nastavená na `1` , odchozí přenosy se pořád odesílají z adres, které jsou uvedené ve vlastnostech vaší aplikace, pokud nezadáte trasy, které směrují provoz na jiné místo.
> 
> Místní integrace virtuální sítě nemůže používat port 25.

Při použití integrace virtuální sítě s virtuální sítě ve stejné oblasti je potřeba mít určitá omezení:

* Nemůžete se připojit k prostředkům napříč globálními připojeními partnerských vztahů.
* Tato funkce je dostupná ze všech jednotek škálování App Service v Premium v2 a Premium v3. Je dostupná taky standardně, ale jenom z novějších jednotek škálování App Service. Pokud používáte starší jednotku škálování, můžete ji použít jenom z plánu Premium v2 App Service. Pokud se chcete ujistit, že můžete používat funkci v plánu Standard App Service, vytvořte svou aplikaci v plánu App Service Premium v3. Tyto plány jsou podporované jenom na našich nejnovějších jednotkách škálování. V případě potřeby můžete horizontální navýšení kapacity snížit.  
* Podsíť Integration můžete použít jenom v jednom plánu App Service.
* Tuto funkci nemůžou používat aplikace pro izolované plánování, které jsou ve App Service Environment.
* Tato funkce vyžaduje nepoužitou podsíť, která je ve virtuální síti Azure Resource Manager a/28 nebo větší.
* Aplikace a virtuální síť musí být ve stejné oblasti.
* Virtuální síť nejde odstranit pomocí integrované aplikace. Před odstraněním virtuální sítě odeberte integraci.
* Pro každý App Service plán můžete mít jenom jednu místní integraci virtuální sítě. Víc aplikací ve stejném plánu App Service může používat stejnou virtuální síť.
* Pokud máte aplikaci, která používá místní integraci virtuální sítě, nemůžete změnit předplatné aplikace ani plán.
* Vaše aplikace nemůže překládat adresy v Azure DNS Private Zones bez změny konfigurace.

Integrace virtuální sítě závisí na vyhrazené podsíti. Při zřizování podsítě neztratí podsíť Azure pět IP adres od začátku. Jedna adresa se používá z podsítě integrace pro každou instanci plánu. Při škálování aplikace na čtyři instance se použijí čtyři adresy. 

Při horizontálním navýšení nebo zmenšení kapacity se požadovaný adresní prostor zdvojnásobí na krátkou dobu. To má vliv na reálné, dostupné podporované instance pro danou velikost podsítě. Následující tabulka uvádí maximální počet dostupných adres na jeden blok CIDR a vliv na horizontální škálování:

| Velikost bloku CIDR | Maximální počet dostupných adres | Maximální horizontální škálování (instance)<sup>*</sup> |
|-----------------|-------------------------|---------------------------------|
| za 28             | 11                      | 5                               |
| /27             | 27                      | 13                              |
| za 26             | 59                      | 29                              |

<sup>*</sup>Předpokládá, že v určitém okamžiku budete muset horizontální navýšení nebo snížení kapacity v libovolné velikosti nebo SKU. 

Vzhledem k tomu, že velikost podsítě po přiřazení nejde změnit, používejte dostatečně velkou podsíť, která bude vyhovovat libovolné škále aplikace, ke které může dojít. Abyste se vyhnuli jakýmkoli problémům s kapacitou podsítě, měli byste použít adresy 64 a/26.  

Pokud chcete, aby vaše aplikace v jiném plánu dosáhly virtuální sítě, ke které už jsou připojené aplikace v jiném plánu, vyberte jinou podsíť, než kterou používá již existující integrace virtuální sítě.

Tato funkce je plně podporovaná pro aplikace pro Windows i Linux, včetně [vlastních kontejnerů](../articles/app-service/quickstart-custom-container.md). Všechna chování se chovají stejně jako aplikace pro Windows a aplikace pro Linux.

### <a name="service-endpoints"></a>Koncové body služby

Místní integrace virtuální sítě umožňuje přístup ke službám Azure, které jsou zabezpečené pomocí koncových bodů služby. Chcete-li získat přístup ke službě zabezpečeného koncovému bodu služby, je nutné provést následující akce:

1. Nakonfigurujte místní integraci virtuální sítě s vaší webovou aplikací tak, aby se připojovala k určité podsíti pro integraci.
1. Přejít na cílovou službu a nakonfigurovat koncové body služby proti podsíti Integration.

### <a name="network-security-groups"></a>Skupiny zabezpečení sítě

Skupiny zabezpečení sítě můžete použít k blokování příchozího a odchozího provozu do prostředků ve virtuální síti. Aplikace, která používá místní integraci virtuální sítě, může používat [skupinu zabezpečení sítě][VNETnsg] k blokování odchozího provozu do prostředků ve vaší virtuální síti nebo v Internetu. Pro blokování provozu na veřejné adresy musíte mít nastavení aplikace `WEBSITE_VNET_ROUTE_ALL` nastavené na `1` . Příchozí pravidla ve NSG se nevztahují na vaši aplikaci, protože integrace virtuální sítě má vliv jenom na odchozí přenosy z vaší aplikace.

K řízení příchozího provozu do aplikace použijte funkci omezení přístupu. NSG, který se používá pro vaši podsíť integrace, je v platnosti bez ohledu na to, jaké trasy se v podsíti pro integraci nastavily. Pokud `WEBSITE_VNET_ROUTE_ALL` je nastavená na `1` a nemáte žádné trasy, které by ovlivnily provoz veřejných adres v podsíti Integration, veškerý odchozí provoz se pořád řídí skupin zabezpečení sítě přiřazeným vaší podsíti integrace. Pokud `WEBSITE_VNET_ROUTE_ALL` není nastavená, skupin zabezpečení sítě se aplikují jenom na RFC1918 provoz.

### <a name="routes"></a>Trasy

Směrovací tabulky můžete použít ke směrování odchozího provozu z vaší aplikace do libovolného, kdykoli potřebujete. Ve výchozím nastavení mají směrovací tabulky vliv jenom na váš cílový provoz RFC1918. Když nastavíte `WEBSITE_VNET_ROUTE_ALL` , bude to `1` mít vliv na všechna odchozí volání. Trasy, které jsou nastavené ve vaší podsíti Integration, neovlivní odpovědi na příchozí žádosti o aplikace. Mezi běžné cíle můžou patřit zařízení firewallu nebo brány.

Pokud chcete směrovat veškerý odchozí provoz do místní sítě, můžete k posílání všech odchozích přenosů do brány ExpressRoute použít směrovací tabulku. Pokud provedete směrování provozu do brány, nezapomeňte nastavit trasy v externí síti pro posílání odpovědí zpět.

Trasy Border Gateway Protocol (BGP) ovlivňují také přenosy aplikací. Pokud máte trasy protokolu BGP z nějakého, jako je brána ExpressRoute, ovlivní se tím odchozí přenosy aplikací. Ve výchozím nastavení mají trasy protokolu BGP vliv jenom na váš cílový provoz RFC1918. Když `WEBSITE_VNET_ROUTE_ALL` je nastavená na `1` , všechny odchozí přenosy můžou být ovlivněné vašimi TRASAMI protokolu BGP.

### <a name="azure-dns-private-zones"></a>Azure DNS privátní zóny 

Jakmile se vaše aplikace integruje s vaší virtuální sítí, používá stejný server DNS, se kterým je nakonfigurovaná vaše virtuální síť. Ve výchozím nastavení vaše aplikace nebude fungovat se Azure DNS privátními zónami. Chcete-li pracovat s Azure DNS privátní zóny, je nutné přidat následující nastavení aplikace:

1. `WEBSITE_DNS_SERVER` s hodnotou `168.63.129.16`
1. `WEBSITE_VNET_ROUTE_ALL` s hodnotou `1`

Tato nastavení odesílají všechna odchozí volání z vaší aplikace do vaší virtuální sítě a umožňují vaší aplikaci přístup k Azure DNS privátní zóně. Pomocí těchto nastavení může vaše aplikace používat Azure DNS dotazem na soukromou zónu DNS na úrovni pracovního procesu.  

### <a name="private-endpoints"></a>Privátní koncové body

Pokud chcete provést volání [privátních koncových bodů][privateendpoints], je nutné zajistit, aby se vyhledávání DNS přeložilo na soukromý koncový bod. Toto chování můžete vyhovět jedním z následujících způsobů: 

* Integrace s Azure DNS privátní zóny. Pokud vaše virtuální síť nemá vlastní server DNS, provede se to automaticky.
* Spravujte privátní koncový bod na serveru DNS, který používá vaše aplikace. K tomu je potřeba znát adresu privátního koncového bodu a potom koncový bod, ke kterému se pokoušíte připojit, použít záznam A.
* Nakonfigurujte si vlastní server DNS pro přeposílání na Azure DNS privátní zóny.

<!--Image references-->
[4]: ../includes/media/web-sites-integrate-with-vnet/vnetint-appsetting.png

<!--Links-->
[VNETnsg]: /azure/virtual-network/security-overview/
[privateendpoints]: ../articles/app-service/networking/private-endpoint.md
