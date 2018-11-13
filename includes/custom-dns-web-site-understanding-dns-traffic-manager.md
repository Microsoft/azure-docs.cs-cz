---
author: cephalin
ms.service: app-service-web
ms.topic: include
ms.date: 11/09/2018
ms.author: cephalin
ms.openlocfilehash: 73e95f6259c916b06fe61cb47fd36beac4c7a427
ms.sourcegitcommit: 6b7c8b44361e87d18dba8af2da306666c41b9396
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/12/2018
ms.locfileid: "51571977"
---
V systému DNS (Domain Name) slouží k vyhledání věci na Internetu. Například pokud v prohlížeči zadejte adresu nebo kliknutím na odkaz na webové stránce, používá DNS přeložit doménu na IP adresu. IP adresa je ze například adresu ulice, ale nejedná se o velmi lidské popisný. Například je mnohem jednodušší pamatovat název DNS jako **contoso.com** než adresu IP, jako je například 192.168.1.88 nebo 2001:0:4137:1f67:24a2:3888:9cce:fea3 mějte na paměti.

Systém DNS je založen na *záznamy*. Záznamy přidružit konkrétní *název*, jako například **contoso.com**, IP adresu nebo jiný název DNS. Když aplikaci, jako je třeba webový prohlížeč, vyhledá názvu ve službě DNS, najde záznam a používá všechno, co odkazuje jako na adresu. Pokud je hodnota, na který odkazuje na IP adresu, prohlížeč použije tuto hodnotu. Ukazuje-li jiný název DNS, aplikace má rozlišení udělat znovu. Překlad všechny, takže v konečném důsledku, skončí za IP adresu.

Při vytváření na webu Azure k webu se automaticky přiřadí název DNS. Tento název má formu  **&lt;yoursitename&gt;. azurewebsites.net**. Při přidání webu jako koncový bod Azure Traffic Manageru, váš web bude přístupné prostřednictvím  **&lt;yourtrafficmanagerprofile&gt;. trafficmanager.net** domény.

> [!NOTE]
> Pokud váš web je nakonfigurovaný jako koncový bod služby Traffic Manager, použijte **. trafficmanager.net** adres při vytváření záznamů DNS.
> 
> Můžete použít pouze záznamů CNAME s Traffic Managerem
> 
> 

Existují také typy více záznamů, každá má své vlastní funkce a omezení, ale pro moduly websites nakonfigurovány jako koncové body Traffic Manageru, jsme pouze pro vás o některém; *CNAME* záznamy.

### <a name="cname-or-alias-record"></a>Záznam CNAME nebo Alias
Záznam CNAME, který se mapuje *konkrétní* název DNS, jako **mail.contoso.com** nebo **www.contoso.com**, na jiný název domény (kanonické). V případě Azure Websites pomocí Traffic Manageru, je název domény canonical  **&lt;myapp >. trafficmanager.net** název domény vašeho profilu Traffic Manageru. Po vytvoření záznamu CNAME vytvoří alias pro  **&lt;myapp >. trafficmanager.net** název domény. Záznam CNAME se přeloží IP adresu vašeho  **&lt;myapp >. trafficmanager.net** název domény automaticky, takže pokud se změní IP adresu webu, není nutné provádět žádnou akci.

Jakmile přenos dorazí na Traffic Manager, pak se směruje provoz na svůj web pomocí metody, které je nakonfigurována pro vyrovnávání zatížení. Toto je pro návštěvníky na svůj web zcela transparentní. Zobrazí se pouze název vlastní domény ve svém prohlížeči.

> [!NOTE]
> Některé domény registrátorů umožňují pouze pro mapování subdomény, při použití záznam CNAME, jako například **www.contoso.com**a není kořenový názvy, například **contoso.com**. Další informace o záznamech CNAME najdete v tématu v dokumentaci od vašeho registrátora <a href="http://en.wikipedia.org/wiki/CNAME_record">Wikipedia položka záznamu CNAME</a>, nebo <a href="http://tools.ietf.org/html/rfc1035">názvy domén IETF – implementace a specifikace</a> dokumentu.
> 
> 

