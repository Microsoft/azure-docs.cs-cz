---
title: Přední dveře Azure | Dokumenty společnosti Microsoft
description: Tento článek obsahuje přehled služby Azure Front Door. Zjistěte, zda je tou správnou volbou pro zatížení vyrovnávání uživatelskýprovoz pro vaši aplikaci.
services: frontdoor
documentationcenter: ''
author: sharad4u
editor: ''
ms.service: frontdoor
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/23/2019
ms.author: sharadag
ms.openlocfilehash: b2ee41324cfaefa4d5aec3aa02b2d0d8c75da78f
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2020
ms.locfileid: "80879118"
---
# <a name="what-is-azure-front-door"></a>Co je Azure Front Door?
Azure Front Door umožňuje definovat, spravovat a monitorovat globální směrování pro váš webový provoz optimalizací pro nejlepší výkon a okamžité globální převzetí služeb při selhání pro vysokou dostupnost. Pomocí aplikací Front Door můžete transformovat globální spotřebitelské a podnikové aplikace (s více oblastmi) na robustní, vysoce výkonné přizpůsobené moderní aplikace, api a obsah, který se díky Azure dostane k globálnímu publiku.

Front Door pracuje na vrstvě 7 nebo vrstvě HTTP/HTTPS a pro vylepšení globální konektivity využívá protokol pro všesměrové vysílání s rozděleným protokolem TCP a globální síť od Microsoftu. Na základě výběru jednotlivých metod směrování v konfiguraci můžete zajistit, že služba Front Door směruje žádosti klientů na nejrychlejší a nejdostupnější back-end aplikace. Back-end aplikace je jakákoli internetová služba hostovaná v rámci služby Azure nebo mimo ni. Front Door poskytuje celou řadu [metod směrování provozu](front-door-routing-methods.md) a [možností monitorování stavu back-endu](front-door-health-probes.md), takže vyhovuje různým požadavkům aplikací a modelům automatického převzetí služeb při selhání. Podobně jako [Traffic Manager](../traffic-manager/traffic-manager-overview.md) je služba Front Door odolná vůči selhání, a to i vůči selhání celé oblasti Azure.

>[!NOTE]
> Azure pro vaše scénáře poskytuje sadu plně spravovaných řešení pro vyrovnávání zatížení. Pokud chcete zajistit globální směrování na základě DNS a **nechcete** ukončovat protokol TLS (tzv. přesměrování zpracování SSL) nebo zpracování jednotlivých požadavků HTTP nebo HTTPS na úrovni aplikace, přečtěte si o [Traffic Manageru](../traffic-manager/traffic-manager-overview.md). Pokud chcete zajistit vyrovnávání zatížení mezi servery v oblasti na vrstvě aplikace, přečtěte si o [Application Gateway](../application-gateway/application-gateway-introduction.md), a pokud chcete zajistit vyrovnávání zatížení na vrstvě sítě, přečtěte si o nástroji [Load Balancer](../load-balancer/load-balancer-overview.md). Vašim kompletním scénářům by mohla prospět kombinace těchto řešení podle potřeby.
>
> Porovnání možností vyrovnávání zatížení Azure najdete [v tématu Přehled možností vyrovnávání zatížení v Azure](https://docs.microsoft.com/azure/architecture/guide/technology-choices/load-balancing-overview).

Součástí služby Front Door jsou následující funkce:

## <a name="accelerate-application-performance"></a>Zrychlení výkonu aplikací
Prostřednictvím protokolu pro všesměrového vysílání na základě rozděleného protokolu TCP služba Front Door zajišťuje, že se koncoví uživatelů bezodkladně připojí k nejbližšímu bodu POP (bodu výskytu) služby Front Door. Pomocí globální sítě Microsoftu pro připojení back-endů aplikací z bodů POP služby Front Door zajišťuje vyšší dostupnost a spolehlivost a současně zachovává výkon. Toto připojení k back-endu je také založeno na nejnižší latenci sítě. Přečtěte si další informace o metodách směrování služby Front Door, jako jsou [rozdělený protokol TCP](front-door-routing-architecture.md#splittcp) a [protokol pro všesměrové vysílání](front-door-routing-architecture.md#anycast).

## <a name="increase-application-availability-with-smart-health-probes"></a>Zvýšení dostupnosti aplikací pomocí inteligentních sond stavu

Front Door poskytuje vysokou dostupnost důležitých aplikací pomocí inteligentních sond stavu, monitorování back-endů z hlediska latence a dostupnosti a tím, že poskytuje okamžité automatické převzetí služeb back-endu při selhání. U svých aplikací můžete provádět operace plánované údržby bez jakýchkoli výpadků. Zatímco probíhá údržba, Front Door směruje provoz do alternativních back-endů.

## <a name="url-based-routing"></a>Směrování na základě adresy URL
Směrování na základě cesty URL umožňuje směrovat provoz do back-endových fondů na základě cest URL žádosti. Jedním ze scénářů je směrování žádostí o různé typy obsahu do různých back-endových fondů.

Například žádosti na adresu `http://www.contoso.com/users/*` se směrují na UserProfilePool a žádosti na adresu `http://www.contoso.com/products/*` na ProductInventoryPool.  Front Door umožňuje dokonce i složitější scénáře párování tras pomocí algoritmu nejlepší shody. Pokud se neshoduje žádný vzor cesty, vybere se výchozí pravidlo směrování pro `http://www.contoso.com/*` a provoz se nasměruje na výchozí pravidlo směrování, které zachycuje vše. Přečtěte si další informace o [párování tras](front-door-route-matching.md).

## <a name="multiple-site-hosting"></a>Hostování několika webů
Hostování několika webů umožňuje konfigurovat více než jeden web ve stejné konfiguraci služby Front Door. Tato funkce umožňuje nakonfigurovat efektivnější topologii vašich nasazení tím, že přidá různé weby do jedné konfigurace služby Front Door. Na základě architektury vaší aplikace můžete nakonfigurovat Azure Front Door tak, aby buď nasměroval každý web do vlastního back-endového fondu, nebo aby různé weby byly směrovány do stejného back-endového fondu. Služba Front Door může například obsluhovat provoz pro `images.contoso.com` a `videos.contoso.com` ze dvou back-endových fondů s názvem ImagePool a VideoPool. Můžete také nakonfigurovat oba front-endové hostitele tak, aby směrovali provoz do jediného back-endového fondu s názvem MediaPool.

Podobně také můžete mít dvě různé domény – `www.contoso.com` a `www.fabrikam.com` – nakonfigurované ve stejné službě Front Door.

## <a name="session-affinity"></a>Spřažení relací
Funkce spřažení relací na základě souborů cookie je užitečná v případě, že chcete zachovat uživatelskou relaci na stejném back-endu aplikací. Využitím spravovaných souborů cookie služby Front Door je možné následný provoz z relace uživatele nasměrovat do stejného back-endu aplikace za účelem zpracování. Tato funkce je důležitá v případech, kdy se stav jednotlivých uživatelských relací ukládá místně na back-endu.

## <a name="tls-termination"></a>Ukončení TLS
Front Door podporuje ukončení TLS na okraji, to znamená, že jednotliví uživatelé mohou nastavit připojení TLS s prostředím front door namísto jeho navázání přes dálková připojení s back-endem aplikace. Služba Front Door dále podporuje připojení přes protokol HTTP i HTTPS mezi prostředími služby Front Door a back-endy. Můžete tedy také nastavit end-to-end šifrování TLS. Pokud například služba Front Door obdrží pro úlohu aplikace více než 5 000 žádostí za minutu, z důvodu opakovaného použití pohotovostního připojení se u aktivních služeb vytvoří řekněme 500 připojení k back-endu aplikace. Tím se výrazně sníží zátěž z back-endů.

## <a name="custom-domains-and-certificate-management"></a>Vlastní domény a správa certifikátů
Pokud k doručování obsahu používáte službu Front Door a chcete, aby se v adrese URL služby Front Door zobrazoval název vaší vlastní domény, potřebujete vlastní doménu. Srozumitelný název domény může být praktický pro vaše zákazníky a užitečný při budování značky.
Pro názvy vlastních domén podporuje služba Front Door také protokol HTTPS. Tuto funkci použijte buď výběrem spravovaných certifikátů Front Door pro váš provoz, nebo nahráním vlastního certifikátu TLS/SSL.

## <a name="application-layer-security"></a>Zabezpečení aplikační vrstvy
Azure Front Door umožňuje vytvářet vlastní pravidla brány firewall webových aplikací (WAF) pro řízení přístupu k ochraně úlohy HTTP/HTTPS před zneužitím na základě IP adres klienta, kódu země a parametrů http. Front Door dále umožňuje vytvořit pravidla omezení rychlosti, aby bylo možné vypořádat se s provozem škodlivých robotů. Další informace o bráně firewall webových aplikací najdete v tématu [Co je brána firewall pro webové aplikace Azure?](../web-application-firewall/overview.md)

Samotná platforma Front Door je chráněná službou [Azure DDoS Protection](../virtual-network/ddos-protection-overview.md) Basic. Z důvodu další ochrany je možné ve virtuálních sítích povolit službu Azure DDoS Protection Standard a pomocí automatického ladění a zmírnění chránit prostředky před útoky na vrstvě sítě (TCP/UDP). Front Door je reverzní proxy vrstvy 7 a standardně povoluje pouze webový přenos do back-endů a blokuje ostatní typy provozu.

## <a name="url-redirection"></a>Přesměrování adres URL
Se silným průmyslem tlačit na podporu pouze zabezpečené komunikace, webové aplikace se očekává, že automaticky přesměrovat všechny HTTP provoz na HTTPS. Tím je zajištěno, že veškerá komunikace mezi uživateli a aplikací probíhá přes šifrovanou cestu. 

Vlastníci aplikací se tradičně zabývali tímto požadavkem vytvořením vyhrazené služby, jejímž jediným účelem bylo přesměrovat požadavky, které obdrží v protokolu HTTP na protokol HTTPS. Azure Front Door podporuje možnost přesměrovat provoz z PROTOKOLU HTTP na protokol HTTPS. To zjednodušuje konfiguraci aplikací, optimalizuje využití prostředků a podporuje nové scénáře přesměrování, včetně globálního přesměrování a přesměrování na základě cesty. Přesměrování adresy URL z Azure Front Door není omezeno na http na přesměrování HTTPS samostatně, ale také k přesměrování na jiné název hostitele, přesměrování na jinou cestu nebo dokonce přesměrování na nový řetězec dotazu v adrese URL.

Další informace najdete [v tématu přesměrování provozu](front-door-url-redirect.md) pomocí Azure Front Door.

## <a name="url-rewrite"></a>Přepsání adresy URL
Front Door podporuje [přepsání adresy URL](front-door-url-rewrite.md) tím, že povoluje konfiguraci volitelné vlastní předávací cesty, která se má použít při vytváření žádosti o předání do back-endu. Služba Front Door dále umožňuje konfigurovat hlavičku hostitele, která se má při předání žádosti do back-endu odeslat.

## <a name="protocol-support---ipv6-and-http2-traffic"></a>Podpora přenosu přes protokoly IPv6 a HTTP/2
Azure Front Door nativně podporuje komplexní připojení přes protokol IPv6 a také protokol HTTP/2. 

Protokol HTTP/2 umožňuje plně duplexní komunikaci mezi back-endy aplikací a klientem přes dlouhotrvající připojení TCP. Tento protokol umožňuje interaktivnější komunikaci mezi back-endem a klientem, která může být obousměrná, aniž by bylo nutné dotazování, jak se to vyžaduje v implementacích založených na protokolu HTTP. Na rozdíl od protokolu HTTP má protokol HTTP/2 malou režii a může znovu použít stejné připojení protokolu TCP pro více žádostí nebo odpovědí. Díky tomu je využití prostředků efektivnější. Další informace o [podpoře HTTP/2 v Azure Front Door](front-door-http2.md).

## <a name="pricing"></a>Ceny

Informace o cenách najdete v tématu, které se věnuje [cenám služby Front Door](https://azure.microsoft.com/pricing/details/frontdoor/).

## <a name="next-steps"></a>Další kroky

- Přečtěte si, jak [vytvořit Front Door](quickstart-create-front-door.md).
- Přečtěte si, [jak služba Front Door funguje](front-door-routing-architecture.md).
