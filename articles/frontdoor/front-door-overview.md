---
title: Přední dvířka Azure | Microsoft Docs
description: Tento článek obsahuje přehled služby Azure Front Door. Zjistěte, zda se jedná o správnou volbu pro síťový provoz vyrovnávání zatížení pro vaši aplikaci.
services: frontdoor
documentationcenter: ''
author: duongau
editor: ''
ms.service: frontdoor
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/02/2020
ms.author: duau
ms.openlocfilehash: 5741e41e3c1474cef5cf49270fd40bbdf4fcaffb
ms.sourcegitcommit: 5a3b9f35d47355d026ee39d398c614ca4dae51c6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/02/2020
ms.locfileid: "89399408"
---
# <a name="what-is-azure-front-door"></a>Co je Azure Front Door?
Přední dvířka Azure umožňují definovat, spravovat a monitorovat globální směrování pro webový provoz optimalizací pro nejlepší výkon a rychlé globální převzetí služeb při selhání pro zajištění vysoké dostupnosti. S předními dvířky můžete transformaci svých globálních uživatelů a podnikových aplikací transformovat na robustní, vysoce výkonné přizpůsobené moderní aplikace, rozhraní API a obsah, který se službou Azure dosáhne globální cílové skupiny.

Front Door pracuje na vrstvě 7 nebo vrstvě HTTP/HTTPS a pro vylepšení globální konektivity využívá protokol pro všesměrové vysílání s rozděleným protokolem TCP a globální síť od Microsoftu. Na základě výběru jednotlivých metod směrování v konfiguraci můžete zajistit, že služba Front Door směruje žádosti klientů na nejrychlejší a nejdostupnější back-end aplikace. Back-end aplikace je jakákoli internetová služba hostovaná v rámci služby Azure nebo mimo ni. Front Door poskytuje celou řadu [metod směrování provozu](front-door-routing-methods.md) a [možností monitorování stavu back-endu](front-door-health-probes.md), takže vyhovuje různým požadavkům aplikací a modelům automatického převzetí služeb při selhání. Podobně jako [Traffic Manager](../traffic-manager/traffic-manager-overview.md) je služba Front Door odolná vůči selhání, a to i vůči selhání celé oblasti Azure.

>[!NOTE]
> Azure pro vaše scénáře poskytuje sadu plně spravovaných řešení pro vyrovnávání zatížení. Pokud chcete zajistit globální směrování na základě DNS a **nechcete** ukončovat protokol TLS (tzv. přesměrování zpracování SSL) nebo zpracování jednotlivých požadavků HTTP nebo HTTPS na úrovni aplikace, přečtěte si o [Traffic Manageru](../traffic-manager/traffic-manager-overview.md). Pokud chcete zajistit vyrovnávání zatížení mezi servery v oblasti na vrstvě aplikace, přečtěte si o [Application Gateway](../application-gateway/application-gateway-introduction.md), a pokud chcete zajistit vyrovnávání zatížení na vrstvě sítě, přečtěte si o nástroji [Load Balancer](../load-balancer/load-balancer-overview.md). Vašim kompletním scénářům by mohla prospět kombinace těchto řešení podle potřeby.
>
> Porovnání možností vyrovnávání zatížení Azure najdete v tématu [Přehled možností vyrovnávání zatížení v Azure](https://docs.microsoft.com/azure/architecture/guide/technology-choices/load-balancing-overview).

Součástí služby Front Door jsou následující funkce:

## <a name="accelerate-application-performance"></a>Zrychlení výkonu aplikací
Prostřednictvím protokolu pro všesměrového vysílání na základě rozděleného protokolu TCP služba Front Door zajišťuje, že se koncoví uživatelů bezodkladně připojí k nejbližšímu bodu POP (bodu výskytu) služby Front Door. Pomocí globální sítě Microsoftu pro připojení back-endů aplikací z bodů POP služby Front Door zajišťuje vyšší dostupnost a spolehlivost a současně zachovává výkon. Toto připojení k back-endu je také založeno na nejnižší latenci sítě. Přečtěte si další informace o metodách směrování služby Front Door, jako jsou [rozdělený protokol TCP](front-door-routing-architecture.md#splittcp) a [protokol pro všesměrové vysílání](front-door-routing-architecture.md#anycast).

## <a name="increase-application-availability-with-smart-health-probes"></a>Zvýšení dostupnosti aplikací pomocí inteligentních sond stavu

Přední dvířka poskytují vysokou dostupnost pro vaše důležité aplikace pomocí svých inteligentních sond stavu a sledují back-endy v případě latence i dostupnosti a poskytování rychlého automatického převzetí služeb při selhání v případě výpadku. U svých aplikací můžete provádět operace plánované údržby bez jakýchkoli výpadků. Zatímco probíhá údržba, Front Door směruje provoz do alternativních back-endů.

## <a name="url-based-routing"></a>Směrování na základě adresy URL
Směrování na základě cesty URL umožňuje směrovat provoz do back-endových fondů na základě cest URL žádosti. Jedním ze scénářů je směrování žádostí o různé typy obsahu do různých back-endových fondů.

Například žádosti na adresu `http://www.contoso.com/users/*` se směrují na UserProfilePool a žádosti na adresu `http://www.contoso.com/products/*` na ProductInventoryPool.  Front Door umožňuje dokonce i složitější scénáře párování tras pomocí algoritmu nejlepší shody. Pokud se neshoduje žádný vzor cesty, vybere se výchozí pravidlo směrování pro `http://www.contoso.com/*` a provoz se nasměruje na výchozí pravidlo směrování, které zachycuje vše. Přečtěte si další informace o [párování tras](front-door-route-matching.md).

## <a name="multiple-site-hosting"></a>Hostování několika webů
Hostování několika webů umožňuje konfigurovat více než jeden web ve stejné konfiguraci služby Front Door. Tato funkce umožňuje nakonfigurovat efektivnější topologii vašich nasazení tím, že přidá různé weby do jedné konfigurace služby Front Door. Na základě architektury vaší aplikace můžete nakonfigurovat přední dveře Azure tak, aby každý web nasměrovaly na svůj vlastní back-end fond nebo aby se na stejný back-end fond směrovaly různé weby. Služba Front Door může například obsluhovat provoz pro `images.contoso.com` a `videos.contoso.com` ze dvou back-endových fondů s názvem ImagePool a VideoPool. Můžete také nakonfigurovat oba front-endové hostitele tak, aby směrovali provoz do jediného back-endového fondu s názvem MediaPool.

Podobně také můžete mít dvě různé domény – `www.contoso.com` a `www.fabrikam.com` – nakonfigurované ve stejné službě Front Door.

## <a name="session-affinity"></a>Spřažení relací
Funkce spřažení relací na základě souborů cookie je užitečná v případě, že chcete zachovat uživatelskou relaci na stejném back-endu aplikací. Využitím spravovaných souborů cookie služby Front Door je možné následný provoz z relace uživatele nasměrovat do stejného back-endu aplikace za účelem zpracování. Tato funkce je důležitá v případech, kdy se stav jednotlivých uživatelských relací ukládá místně na back-endu.

## <a name="tls-termination"></a>Ukončení protokolu TLS
Přední dvířka podporují ukončení protokolu TLS na hraničních zařízeních. jednotliví uživatelé mohou nastavit připojení TLS pomocí prostředí front-endu místo toho, aby je nastavily přes dlouhé připojení k back-endu aplikace. Služba Front Door dále podporuje připojení přes protokol HTTP i HTTPS mezi prostředími služby Front Door a back-endy. Můžete také nastavit šifrování TLS od začátku až po koncové. Pokud například služba Front Door obdrží pro úlohu aplikace více než 5 000 žádostí za minutu, z důvodu opakovaného použití pohotovostního připojení se u aktivních služeb vytvoří řekněme 500 připojení k back-endu aplikace. Tím se výrazně sníží zátěž z back-endů.

## <a name="custom-domains-and-certificate-management"></a>Vlastní domény a správa certifikátů
Pokud k doručování obsahu používáte službu Front Door a chcete, aby se v adrese URL služby Front Door zobrazoval název vaší vlastní domény, potřebujete vlastní doménu. Srozumitelný název domény může být praktický pro vaše zákazníky a užitečný při budování značky.
Pro názvy vlastních domén podporuje služba Front Door také protokol HTTPS. Tuto funkci můžete použít buď tak, že zvolíte certifikáty spravované přes dvířka pro váš provoz nebo nahráváte vlastní certifikát TLS/SSL.

## <a name="application-layer-security"></a>Zabezpečení aplikační vrstvy
Přední dvířka Azure umožňují vytvářet vlastní pravidla firewallu webových aplikací (WAF) pro řízení přístupu, která chrání vaše úlohy HTTP/HTTPS před zneužitím na základě IP adres klientů, kódu země a parametrů protokolu HTTP. Front Door dále umožňuje vytvořit pravidla omezení rychlosti, aby bylo možné vypořádat se s provozem škodlivých robotů. Další informace o firewallu webových aplikací najdete v tématu [co je firewall webových aplikací Azure?](../web-application-firewall/overview.md)

Samotná platforma Front Door je chráněná službou [Azure DDoS Protection](../virtual-network/ddos-protection-overview.md) Basic. Z důvodu další ochrany je možné ve virtuálních sítích povolit službu Azure DDoS Protection Standard a pomocí automatického ladění a zmírnění chránit prostředky před útoky na vrstvě sítě (TCP/UDP). Front Door je reverzní proxy vrstvy 7 a standardně povoluje pouze webový přenos do back-endů a blokuje ostatní typy provozu.

## <a name="url-redirection"></a>Přesměrování adres URL
Díky silnému nabízenému vysílání na podporu zabezpečené komunikace se očekává, že webové aplikace automaticky přesměrují všechny přenosy HTTP na HTTPS. Tím se zajistí, že se veškerá komunikace mezi uživateli a aplikací stane přes šifrovanou cestu. 

Vlastníci aplikací tento požadavek provedli vytvořením vyhrazené služby, jejíž jediným účelem bylo přesměrování požadavků, které přijímá v HTTP na HTTPS. Přední dveře Azure podporují možnost přesměrovat provoz z HTTP na HTTPS. To zjednodušuje konfiguraci aplikací, optimalizuje využití prostředků a podporuje nové scénáře přesměrování, včetně globálního přesměrování a přesměrování na základě cesty. Přesměrování adresy URL z front Azure na předních dveřích není omezeno pouze na přesměrování HTTP na protokol HTTPS, ale také pro přesměrování na jiný název hostitele, přesměrování na jinou cestu nebo dokonce přesměrování na nový řetězec dotazu v adrese URL.

Další informace najdete v tématu [přesměrování provozu](front-door-url-redirect.md) pomocí front-dveří Azure.

## <a name="url-rewrite"></a>Přepsání adresy URL
Front Door podporuje [přepsání adresy URL](front-door-url-rewrite.md) tím, že povoluje konfiguraci volitelné vlastní předávací cesty, která se má použít při vytváření žádosti o předání do back-endu. Služba Front Door dále umožňuje konfigurovat hlavičku hostitele, která se má při předání žádosti do back-endu odeslat.

## <a name="protocol-support---ipv6-and-http2-traffic"></a>Podpora přenosu přes protokoly IPv6 a HTTP/2
Azure Front Door nativně podporuje komplexní připojení přes protokol IPv6 a také protokol HTTP/2. 

Protokol HTTP/2 umožňuje plně duplexní komunikaci mezi back-endy aplikací a klientem přes dlouhotrvající připojení TCP. Tento protokol umožňuje interaktivnější komunikaci mezi back-endem a klientem, která může být obousměrná, aniž by bylo nutné dotazování, jak se to vyžaduje v implementacích založených na protokolu HTTP. Na rozdíl od protokolu HTTP má protokol HTTP/2 malou režii a může znovu použít stejné připojení protokolu TCP pro více žádostí nebo odpovědí. Díky tomu je využití prostředků efektivnější. Přečtěte si další informace o [podpoře http/2 v Azure front-dveřích](front-door-http2.md).

## <a name="pricing"></a>Ceny

Informace o cenách najdete v tématu, které se věnuje [cenám služby Front Door](https://azure.microsoft.com/pricing/details/frontdoor/).

## <a name="whats-new"></a>Co je nového?

Přihlaste se k odběru informačního kanálu RSS a na stránce s [aktualizacemi Azure](https://azure.microsoft.com/updates/?category=networking&query=Azure%20Front%20Door) si prohlédněte nejnovější aktualizace funkcí služby Azure front-dveří.

## <a name="next-steps"></a>Další kroky

- Přečtěte si, jak [vytvořit Front Door](quickstart-create-front-door.md).
- Přečtěte si, [jak služba Front Door funguje](front-door-routing-architecture.md).
