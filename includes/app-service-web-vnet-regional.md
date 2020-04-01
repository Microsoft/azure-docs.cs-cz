---
author: ccompy
ms.service: app-service-web
ms.topic: include
ms.date: 02/27/2020
ms.author: ccompy
ms.openlocfilehash: e0db3ce7d31b838ca6f7d566083a33ee215d3399
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/31/2020
ms.locfileid: "80419530"
---
Použití místní integrace virtuální sítě umožňuje vaší aplikaci přístup:

* Prostředky ve virtuální síti ve stejné oblasti, kterou integrujete.
* Prostředky ve virtuálních sítích se zaměřily na virtuální síť, které jsou ve stejné oblasti.
* Zabezpečené služby koncového bodu služby.
* Prostředky napříč připojeními Azure ExpressRoute.
* Prostředky ve virtuální síti, ke které jste připojeni.
* Prostředky napříč partnerskými připojeními, která zahrnují připojení Azure ExpressRoute.
* Soukromé koncové body.

Při použití integrace virtuální sítě s virtuálními sítěmi ve stejné oblasti můžete použít následující síťové funkce Azure:

* **Skupiny zabezpečení sítě :** Odchozí provoz můžete blokovat pomocí skupiny zabezpečení sítě, která je umístěna v podsíti integrace. Příchozí pravidla neplatí, protože k poskytování příchozího přístupu k vaší aplikaci nemůžete použít integraci virtuální sítě.
* **Směrovací tabulky (UDR):** Můžete umístit směrovací tabulku do podsítě integrace a odeslat odchozí provoz tam, kde chcete.

Ve výchozím nastavení aplikace směruje pouze rfc1918 provoz do vaší virtuální sítě. Pokud chcete směrovat všechny odchozí provoz y do virtuální sítě, použijte nastavení aplikace WEBSITE_VNET_ROUTE_ALL na vaši aplikaci. Postup konfigurace nastavení aplikace:

1. Přejděte na portálu aplikace do možnosti **konfiguračního** uznatí. Vyberte **nové nastavení aplikace**.
1. Zadejte **WEBSITE_VNET_ROUTE_ALL** do pole **Název** a do pole Hodnota zadejte **hodnotu 1.** **Value**

   ![Poskytnout nastavení aplikace][4]

1. Vyberte **OK**.
1. Vyberte **Uložit**.

Pokud směrujete všechny odchozí provoz y do virtuální sítě, bude podléhat skupinám zabezpečení sítě a udr, které se použijí v podsíti integrace. Při směrování všech odchozích přenosů do virtuální sítě jsou odchozí adresy stále odchozí adresy, které jsou uvedeny ve vlastnostech aplikace, pokud neposkytnete trasy pro odeslání provozu jinam.

Existují určitá omezení s použitím integrace virtuální sítě s virtuálními sítěmi ve stejné oblasti:

* Nelze dosáhnout prostředky přes globální partnerských společností připojení.
* Tato funkce je dostupná jenom z novějších jednotek škálování služby Azure App Service, které podporují plány služby PremiumV2 App Service.
* Integrační podsíť může používat pouze jeden plán služby App Service.
* Tuto funkci nelze použít aplikace izolovaného plánu, které jsou v prostředí služby App Service.
* Tato funkce vyžaduje nepoužívanou podsíť, která je /27 s 32 adresami nebo větší ve virtuální síti Azure Resource Manager.
* Aplikace a virtuální síť musí být ve stejné oblasti.
* S integrovanou aplikací nemůžete odstranit virtuální síť. Před odstraněním virtuální sítě odeberte integraci.
* Můžete integrovat pouze s virtuálními sítěmi ve stejném předplatném jako aplikace.
* Můžete mít jenom jednu regionální integraci virtuální sítě podle plánu služby App Service. Více aplikací ve stejném plánu služby App Service můžete používat stejnou virtuální síť.
* Nemůžete změnit předplatné aplikace nebo plánu, když je aplikace, která používá místní integraci virtuální sítě.

Pro každou instanci plánu se používá jedna adresa. Pokud změníte velikost aplikace na pět instancí, použije se pět adres. Vzhledem k tomu, že velikost podsítě nelze po přiřazení změnit, musíte použít podsíť, která je dostatečně velká, aby vyhovovala všem měřítkům, na které vaše aplikace může dosáhnout. Doporučená velikost je A /26 s 64 adresami. A /26 s 64 adresami pojme plán Premium s 30 instancemi. Při škálování plánu nahoru nebo dolů, budete potřebovat dvakrát tolik adres na krátkou dobu.

Pokud chcete, aby se vaše aplikace v jiném plánu dostaly do virtuální sítě, ke které už jsou aplikace připojené v jiném plánu, vyberte jinou podsíť, než kterou používá předchozí integrace virtuální sítě.

Tato funkce je ve verzi preview pro Linux. Linuxová forma funkce podporuje pouze volání na adresy RFC 1918 (10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16).

### <a name="web-or-function-app-for-containers"></a>Webová nebo funkční aplikace pro kontejnery

Pokud vlastníte aplikaci na Linuxu s integrovanými bitovými kopiemi, regionální integrace virtuální sítě funguje bez dalších změn. Pokud používáte webovou nebo funkční aplikaci pro kontejnery, musíte upravit image dockeru tak, aby se používala integrace virtuální sítě. V bitové kopii dockeru použijte proměnnou prostředí PORT jako naslouchací port hlavního webového serveru namísto pevného čísla portu. Proměnná prostředí PORT je automaticky nastavena platformou v době spuštění kontejneru. Pokud používáte SSH, musí být daemon SSH nakonfigurován tak, aby naslouchal číslu portu určenému proměnnou prostředí SSH_PORT při použití místní integrace virtuální sítě. Neexistuje žádná podpora pro integraci virtuální sítě v y na Linuxu po dálnici.

### <a name="service-endpoints"></a>Koncové body služby

S regionální integrací virtuální sítě můžete použít koncové body služby. Pokud chcete s aplikací používat koncové body služby, připojte se k vybrané virtuální síti pomocí místní integrace virtuální sítě. Potom nakonfigurujte koncové body služby v podsíti, kterou jste použili pro integraci.

### <a name="network-security-groups"></a>Skupiny zabezpečení sítě

Skupiny zabezpečení sítě můžete použít k blokování příchozího a odchozího provozu prostředků ve virtuální síti. Aplikace, která používá místní integraci virtuální sítě, může pomocí [skupiny zabezpečení sítě][VNETnsg] blokovat odchozí provoz na prostředky ve vaší virtuální síti nebo na Internetu. Chcete-li blokovat provoz na veřejné adresy, musíte mít nastavení aplikace WEBSITE_VNET_ROUTE_ALL nastavena na 1. Příchozí pravidla v souboru sítě sítě se nevztahují na vaši aplikaci, protože integrace virtuální sítě ovlivňuje jenom odchozí provoz z vaší aplikace.

Chcete-li řídit příchozí provoz do aplikace, použijte funkci Omezení přístupu. Skupina nsg, která je použita pro podsíť integrace je ve skutečnosti bez ohledu na všechny trasy použité pro podsíť integrace. Pokud je WEBSITE_VNET_ROUTE_ALL nastavena na 1 a nemáte žádné trasy, které by ovlivnily provoz veřejné adresy v podsíti integrace, veškerý odchozí provoz stále podléhá skupinám nsg přiřazených k podsíti integrace. Pokud WEBSITE_VNET_ROUTE_ALL není nastavena, nsgs jsou použity pouze pro provoz RFC1918.

### <a name="routes"></a>Trasy

Pomocí tabulek tras můžete směrovat odchozí provoz z aplikace kamkoli chcete. Ve výchozím nastavení ovlivňují tabulky tras pouze cílový provoz RFC1918. Pokud nastavíte WEBSITE_VNET_ROUTE_ALL na 1, budou ovlivněny všechny odchozí hovory. Trasy nastavené v podsíti integrace neovlivní odpovědi na příchozí žádosti o aplikace. Mezi běžné cíle patří zařízení brány firewall nebo brány.

Pokud chcete směrovat všechny odchozí přenosy místně, můžete použít směrovací tabulku k odeslání veškerého odchozího provozu do brány ExpressRoute. Pokud směrujete provoz do brány, nezapomeňte nastavit trasy v externí síti, aby se všechny odpovědi odeslala zpět.

Trasy protokolu Brána ohraničení (BGP) také ovlivňují provoz aplikace. Pokud máte trasy BGP z něčeho jako brána ExpressRoute, bude ovlivněn odchozí provoz aplikace. Ve výchozím nastavení trasy Protokolu BGP ovlivňují pouze cílový provoz RFC1918. Pokud je WEBSITE_VNET_ROUTE_ALL nastavena na 1, všechny odchozí provozy mohou být ovlivněny trasy BGP.


<!--Image references-->
[4]: ../includes/media/web-sites-integrate-with-vnet/vnetint-appsetting.png

<!--Links-->
[VNETnsg]: https://docs.microsoft.com/azure/virtual-network/security-overview/