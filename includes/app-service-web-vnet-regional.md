---
author: ccompy
ms.service: app-service-web
ms.topic: include
ms.date: 04/15/2020
ms.author: ccompy
ms.openlocfilehash: 7f2b011b2de5af0e4ace9cbeb4399911d8e83b7f
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2020
ms.locfileid: "81312828"
---
Použití místní integrace virtuální sítě umožňuje vaší aplikaci přístup:

* Prostředky ve virtuální síti ve stejné oblasti jako vaše aplikace.
* Prostředky ve virtuálních sítích, které jsou součástí virtuální sítě, se kterou je vaše aplikace integrovaná.
* Zabezpečené služby koncového bodu služby.
* Prostředky napříč připojeními Azure ExpressRoute.
* Prostředky ve virtuální síti, do které jste integrovaní.
* Prostředky napříč partnerskými připojeními, která zahrnují připojení Azure ExpressRoute.
* Privátní koncové body – poznámka: DNS musí být spravované samostatně, nikoli pomocí privátních zón Azure DNS.

Když používáte integraci virtuální sítě s virtuálními sítěmi ve stejné oblasti, můžete použít následující síťové funkce Azure:

* **Skupiny zabezpečení sítě :** Odchozí provoz můžete blokovat pomocí skupiny zabezpečení sítě, která je umístěna v podsíti integrace. Příchozí pravidla neplatí, protože k poskytování příchozího přístupu k vaší aplikaci nemůžete použít integraci virtuální sítě.
* **Směrovací tabulky (UDR):** Můžete umístit směrovací tabulku do podsítě integrace a odeslat odchozí provoz tam, kde chcete.

Ve výchozím nastavení aplikace směruje jenom rfc1918 provoz do vaší virtuální sítě. Pokud chcete směrovat všechny odchozí provoz do virtuální sítě, použijte nastavení aplikace WEBSITE_VNET_ROUTE_ALL na vaši aplikaci. Postup konfigurace nastavení aplikace:

1. Přejděte na portálu aplikace do možnosti **konfiguračního** uznatí. Vyberte **nové nastavení aplikace**.
1. Zadejte **WEBSITE_VNET_ROUTE_ALL** do pole **Název** a do pole Hodnota zadejte **hodnotu 1.** **Value**

   ![Poskytnout nastavení aplikace][4]

1. Vyberte **OK**.
1. Vyberte **Uložit**.

Pokud směrujete všechny odchozí provoz do virtuální sítě, podléhá skupiny nsg a UDR, které se použijí pro podsíť integrace. Když směrujete všechny odchozí provoz do virtuální sítě, vaše odchozí adresy jsou stále odchozí adresy, které jsou uvedeny ve vlastnostech aplikace, pokud nezadáte trasy pro odeslání provozu jinam.

Existují určitá omezení s použitím integrace virtuální sítě s virtuálními sítěmi ve stejné oblasti:

* Nelze dosáhnout prostředky přes globální partnerských společností připojení.
* Tato funkce je dostupná jenom z novějších jednotek škálování služby Azure App Service, které podporují plány služby PremiumV2 App Service.
* Integrační podsíť může používat pouze jeden plán služby App Service.
* Tuto funkci nelze použít aplikace izolovaného plánu, které jsou v prostředí služby App Service.
* Tato funkce vyžaduje nepoužívanou podsíť, která je /27 s 32 adresami nebo větší ve virtuální síti Azure Resource Manager.
* Aplikace a virtuální síť musí být ve stejné oblasti.
* Virtuální síť nelze odstranit pomocí integrované aplikace. Před odstraněním virtuální sítě odeberte integraci.
* Můžete integrovat jenom s virtuálními sítěmi ve stejném předplatném jako aplikace.
* Můžete mít jenom jednu regionální integraci virtuální sítě podle plánu služby App Service. Více aplikací ve stejném plánu služby App Service můžete použít stejnou virtuální síť.
* Nemůžete změnit předplatné aplikace nebo plánu, když je aplikace, která používá místní integraci virtuální sítě.
* Vaše aplikace nemůže vyřešit adresy v privátních zónách Azure DNS.

Pro každou instanci plánu se používá jedna adresa. Pokud změníte velikost aplikace na pět instancí, použije se pět adres. Vzhledem k tomu, že velikost podsítě nelze po přiřazení změnit, musíte použít podsíť, která je dostatečně velká, aby vyhovovala všem měřítkům, na které vaše aplikace může dosáhnout. Doporučená velikost je A /26 s 64 adresami. A /26 s 64 adresami pojme plán Premium s 30 instancemi. Při škálování plánu nahoru nebo dolů, budete potřebovat dvakrát tolik adres na krátkou dobu.

Pokud chcete, aby vaše aplikace v jiném plánu dosáhly virtuální sítě, která je už připojená aplikacemi v jiném plánu, vyberte jinou podsíť, než kterou používá již existující integrace virtuální sítě.

Tato funkce je ve verzi preview pro Linux. Linuxová forma funkce podporuje pouze volání na adresy RFC 1918 (10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16).

### <a name="web-or-function-app-for-containers"></a>Webová nebo funkční aplikace pro kontejnery

Pokud vlastníte aplikaci na Linuxu s integrovanými bitovými kopiemi, regionální integrace virtuální sítě funguje bez dalších změn. Pokud používáte webovou nebo funkční aplikaci pro kontejnery, musíte upravit image dockeru tak, aby se používala integrace virtuální sítě. V bitové kopii dockeru použijte proměnnou prostředí PORT jako naslouchací port hlavního webového serveru namísto pevného čísla portu. Proměnná prostředí PORT je automaticky nastavena platformou v době spuštění kontejneru. Pokud používáte SSH, musí být daemon SSH nakonfigurován tak, aby naslouchal číslu portu určenému proměnnou prostředí SSH_PORT při použití místní integrace virtuální sítě. Neexistuje žádná podpora pro integraci virtuální sítě v y na Linuxu po dálnici.

### <a name="service-endpoints"></a>Koncové body služby

Regionální integrace virtuální sítě umožňuje používat koncové body služby. Pokud chcete s vaší aplikací používat koncové body služby, připojte se k vybrané virtuální síti k místní integraci virtuální sítě a pak nakonfigurujte koncové body služby s cílovou službou v podsíti, kterou jste použili pro integraci. Pokud jste pak chtěli získat přístup ke službě přes koncové body služby:

1. konfigurace regionální integrace virtuální sítě s webovou aplikací
1. přejděte na cílovou službu a nakonfigurujte koncové body služby proti podsíti používané pro integraci

### <a name="network-security-groups"></a>Skupiny zabezpečení sítě

Skupiny zabezpečení sítě můžete použít k blokování příchozích a odchozích přenosů do prostředků ve virtuální síti. Aplikace, která používá místní integraci virtuální sítě, může pomocí [skupiny zabezpečení sítě][VNETnsg] blokovat odchozí provoz na prostředky ve vaší virtuální síti nebo na Internetu. Chcete-li blokovat provoz na veřejné adresy, musíte mít nastavení aplikace WEBSITE_VNET_ROUTE_ALL nastavena na 1. Příchozí pravidla v souboru sítě sítě se nevztahují na vaši aplikaci, protože integrace virtuální sítě ovlivňuje jenom odchozí provoz z vaší aplikace.

Chcete-li řídit příchozí provoz do aplikace, použijte funkci Omezení přístupu. Skupina nsg, která je použita pro podsíť integrace je ve skutečnosti bez ohledu na všechny trasy použité pro podsíť integrace. Pokud je WEBSITE_VNET_ROUTE_ALL nastavena na 1 a nemáte žádné trasy, které by ovlivnily provoz veřejné adresy v podsíti integrace, veškerý odchozí provoz stále podléhá skupinám nsg přiřazených k podsíti integrace. Pokud WEBSITE_VNET_ROUTE_ALL není nastavena, nsgs jsou použity pouze pro provoz RFC1918.

### <a name="routes"></a>Trasy

Pomocí tabulek tras můžete směrovat odchozí provoz z aplikace kamkoli chcete. Ve výchozím nastavení ovlivňují tabulky tras pouze cílový provoz RFC1918. Pokud nastavíte WEBSITE_VNET_ROUTE_ALL na 1, budou ovlivněny všechny odchozí hovory. Trasy nastavené v podsíti integrace neovlivní odpovědi na příchozí žádosti o aplikace. Mezi běžné cíle patří zařízení brány firewall nebo brány.

Pokud chcete směrovat všechny odchozí přenosy místně, můžete použít směrovací tabulku k odeslání veškerého odchozího provozu do brány ExpressRoute. Pokud směrujete provoz do brány, nezapomeňte nastavit trasy v externí síti, aby se všechny odpovědi odeslala zpět.

Trasy protokolu Brána ohraničení (BGP) také ovlivňují provoz aplikace. Pokud máte trasy BGP z něčeho jako brána ExpressRoute, bude ovlivněn odchozí provoz aplikace. Ve výchozím nastavení trasy Protokolu BGP ovlivňují pouze cílový provoz RFC1918. Pokud je WEBSITE_VNET_ROUTE_ALL nastavena na 1, všechny odchozí provozy mohou být ovlivněny trasy BGP.


<!--Image references-->
[4]: ../includes/media/web-sites-integrate-with-vnet/vnetint-appsetting.png

<!--Links-->
[VNETnsg]: https://docs.microsoft.com/azure/virtual-network/security-overview/
