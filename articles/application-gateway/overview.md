---
title: Co je Azure Application Gateway?
description: Přečtěte si, jak můžete pomocí aplikační brány Azure spravovat webové přenosy do vaší aplikace.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: overview
ms.custom: mvc
ms.date: 5/31/2019
ms.author: victorh
ms.openlocfilehash: 5f7fd47a096ddd57150a466f85fabcfc2f7045d9
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/26/2019
ms.locfileid: "68564869"
---
# <a name="what-is-azure-application-gateway"></a>Co je Azure Application Gateway?

Azure Application Gateway je nástroj pro vyrovnávání zatížení webových přenosů, který vám umožní spravovat provoz do webových aplikací. Nástroje pro vyrovnávání zatížení obvykle fungují na přenosové vrstvě (vrstva OSI 4 – TCP a UDP) a směrují provoz na základě zdrojové IP adresy a portu do cílové IP adresy a portu.

![Application Gateway koncepční](media/overview/figure1-720.png)

V Application Gateway můžete provádět rozhodování o směrování na základě dalších atributů požadavku HTTP, jako je například cesta URI nebo hlavičky hostitele. Můžete například směrovat provoz na základě příchozí adresy URL. Pokud tedy příchozí adresa URL obsahuje `/images`, můžete směrovat provoz na konkrétní sadu serverů (označovanou jako fond), která je nakonfigurovaná pro obrázky. Pokud `/video` je v adrese URL, provoz se směruje do jiného fondu optimalizovaného pro videa.

![imageURLroute](./media/application-gateway-url-route-overview/figure1-720.png)

Tento typ směrování se označuje jako vyrovnávání zatížení aplikační vrstvy (vrstva OSI 7). Azure Application Gateway umí směrování na základě adresy URL, ale to není vše.

Součástí Azure Application Gateway jsou následující funkce:

## <a name="secure-sockets-layer-ssltls-termination"></a>Ukončení SSL (Secure Sockets Layer) (SSL/TLS)

Application Gateway podporuje ukončení protokolu SSL/TLS v bráně, po kterém provoz obvykle přechází do back-end serverů. Tato funkce webovým serverům umožňuje snížení nákladné režie spojené s šifrováním a dešifrováním. Někdy ale nešifrovaná komunikace na serverech není přijatelnou možností. Důvodem může být to, že požadavky na zabezpečení, požadavky na dodržování předpisů nebo aplikace můžou přijímat jenom zabezpečené připojení. Pro tyto aplikace Aplikační brána podporuje koncové šifrování protokolu SSL/TLS.

## <a name="autoscaling"></a>Automatické škálování

Application Gateway nebo WAF nasazení v rámci Standard_v2 nebo WAF_v2 SKU podporují automatické škálování a můžou vertikálně navyšovat nebo snížit kapacity na základě změny schémat zatížení provozu. Automatické škálování také eliminuje nutnost zvolit během zřizování velikost nasazení nebo počet instancí. Další informace o funkcích Application Gateway standard_v2 a WAF_v2 najdete v tématu Automatické [škálování v2 SKU](application-gateway-autoscaling-zone-redundant.md).

## <a name="zone-redundancy"></a>Redundance zóny

Nasazení Application Gateway nebo WAF v rámci SKU Standard_v2 nebo WAF_v2 může zahrnovat více Zóny dostupnosti, což nabízí lepší odolnost proti chybám a odstraňuje nutnost zřídit samostatné aplikační brány v každé zóně.

## <a name="static-vip"></a>Statická virtuální IP adresa

Virtuální IP adresa služby Application Gateway v Standard_v2 nebo WAF_v2 SKU podporuje výhradně statický typ VIP. Tím se zajistí, že se virtuální IP adresa přidružená k aplikační bráně nemění ani po dobu života Application Gateway.

## <a name="web-application-firewall"></a>Firewall webových aplikací

Firewall webových aplikací (WAF) je funkce služby Application Gateway poskytující centralizovanou ochranu webových aplikací před běžným zneužitím a ohrožením zabezpečení. Firewall webových aplikací (WAF) je založený na [základní sadě pravidel OWASP (Open Web Application Security Project)](https://www.owasp.org/index.php/Category:OWASP_ModSecurity_Core_Rule_Set_Project) 3.0 nebo 2.2.9. 

Webové aplikace se čím dál častěji stávají cílem škodlivých útoků, které zneužívají běžně známé chyby zabezpečení. Mezi tyto běžné typy zneužití patří mimo jiné například útoky prostřednictvím injektáže SQL nebo skriptování mezi weby. Předcházet takovým útokům v kódu aplikace může být náročné a může vyžadovat pečlivou údržbu, opravy a monitorování mnoha vrstev topologie aplikace. Centralizovaný firewall webových aplikací značně zjednodušuje správu zabezpečení a nabízí správcům lepší ochranu aplikací před hrozbami neoprávněného vniknutí. Řešení Firewall webových aplikací (WAF) může také rychleji reagovat na ohrožení zabezpečení, protože opravuje známé chyby zabezpečení v centrálním umístění, namísto zabezpečování jednotlivých webových aplikací. Stávající aplikační brány je možné jednoduše převést na aplikační brány doplněné webovým aplikačním firewallem.

Další informace najdete v tématu věnovaném [firewallu webových aplikací (WAF) v Application Gateway](https://docs.microsoft.com/azure/application-gateway/waf-overview)).

## <a name="url-based-routing"></a>Směrování na základě adresy URL

Směrování na základě cesty URL umožňuje směrovat provoz do fondů back-end serveru na základě cest URL požadavku. Jedním ze scénářů je směrování žádostí na různé typy obsahu do různých fondů.

Například žádosti na adresu `http://contoso.com/video/*` se směrují na VideoServerPool a žádosti na adresu `http://contoso.com/images/*` na ImageServerPool. Pokud nevyhovuje žádný vzor cesty, vybere se VychoziFondServeru.

Další informace najdete v tématu [směrování na základě adresy URL s Application Gateway](https://docs.microsoft.com/azure/application-gateway/url-route-overview).

## <a name="multiple-site-hosting"></a>Hostování několika webů

Hostování několika webů umožňuje konfigurovat více než jeden web ve stejné instanci aplikační brány. Tato funkce umožňuje nakonfigurovat efektivnější topologii nasazení přidáním až 100 webů do jedné aplikační brány. Každý web se dá přesměrovat na vlastní fond. Aplikační brána může například obsluhovat provoz pro `contoso.com` a `fabrikam.com` ze dvou fondů serverů s názvem ContosoServerPool a FabrikamServerPool.

Žádosti na adresu `http://contoso.com` se směrují na ContosoServerPool a žádosti na adresu `http://fabrikam.com` na FabrikamServerPool.

Podobně je možné ve stejném nasazení aplikační brány hostovat dvě poddomény stejné nadřazené domény. Příklady použití subdomén můžou zahrnovat adresy `http://blog.contoso.com` a `http://app.contoso.com` hostované v jednom nasazení aplikační brány.

Další informace najdete v tématu [hostování více lokalit pomocí Application Gateway](https://docs.microsoft.com/azure/application-gateway/multiple-site-overview).

## <a name="redirection"></a>Přesměrování

Běžným scénářem pro mnoho webových aplikací je podpora automatického přesměrování HTTP na HTTPS, aby se zajistilo, že veškerá komunikace mezi aplikací a jejími uživateli probíhá přes šifrovanou cestu.

V minulosti jste mohli používat techniky, jako je například vytvoření vyhrazeného fondu, jehož jediným účelem je přesměrování požadavků, které přijímá v HTTP na HTTPS. Aplikační brána podporuje možnost přesměrovat provoz na Application Gateway. To zjednodušuje konfiguraci aplikací, optimalizuje využití prostředků a podporuje nové scénáře přesměrování, včetně globálního přesměrování a přesměrování na základě cesty. Podpora přesměrování Application Gateway není omezená jenom na přesměrování HTTP na HTTPS. Jde o obecný mechanismus přesměrování, takže můžete provoz přesměrovat z kteréhokoliv portu a na kterýkoliv port, který definujete pomocí pravidel. Také podporuje přesměrování na externí web.

Podpora přesměrování Application Gateway nabízí následující možnosti:

- Globální přesměrování z jednoho portu na jiný port ve službě Application Gateway. To umožňuje přesměrování z HTTP na HTTPS na webu.
- Přesměrování na základě cesty. Tento typ přesměrování umožňuje přesměrování z HTTP na HTTPS jenom v konkrétní oblasti webu, například v oblasti nákupního košíku označené jako `/cart/*`.
- Přesměrování na externí web.

Další informace najdete v tématu [přesměrování provozu](https://docs.microsoft.com/azure/application-gateway/redirect-overview) pomocí Application Gateway.

## <a name="session-affinity"></a>Spřažení relací

Funkce spřažení relací na základě souborů cookie je užitečná v případě, že chcete zachovat uživatelskou relaci na stejném serveru. Pomocí souborů cookie spravovaných bránou umí služba Application Gateway směrovat následný provoz z uživatelské relace ke zpracování na stejný server. To je důležité v případech, kdy se stav jednotlivých uživatelských relací ukládá místně na serveru.

## <a name="websocket-and-http2-traffic"></a>Provoz přes protokoly Websocket a HTTP/2

Application Gateway poskytuje nativní podporu pro protokoly WebSocket a HTTP/2. Neexistuje žádné uživatelsky konfigurovatelné nastavení pro selektivní povolení nebo zakázání podpory protokolu WebSocket.

Protokoly WebSocket a HTTP/2 umožňují plně duplexní komunikaci mezi serverem a klientem přes dlouhotrvající připojení TCP. To umožňuje interaktivnější komunikaci mezi webovým serverem a klientem, která může být obousměrná, aniž by bylo nutné dotazování, jak se to vyžaduje v implementacích založených na protokolu HTTP. Tyto protokoly mají nízkou režii, na rozdíl od HTTP, a umožňují opakované použití stejného připojení TCP pro více požadavků nebo odpovědí, což má za následek efektivnější využití prostředků. Tyto protokoly jsou navrženy pro práci přes tradiční porty HTTP 80 a 443.

Další informace najdete v tématu [](https://docs.microsoft.com/azure/application-gateway/application-gateway-websocket) podpora WebSocket a [Podpora protokolu HTTP/2](https://docs.microsoft.com/azure/application-gateway/configuration-overview#http2-support).

## <a name="azure-kubernetes-service-aks-ingress-controller-preview"></a>Preview řadiče příchozího přenosu dat služby Azure Kubernetes Service (AKS) 

Řadič příchozího přenosu dat služby Application Gateway běží jako pod v rámci clusteru AKS a umožňuje službě Application Gateway fungovat jako příchozí přenos dat pro cluster AKS. To se podporuje jenom v Application Gateway v2.

Další informace najdete v tématu [Řadič příchozího přenosu dat služby Azure Application Gateway](https://azure.github.io/application-gateway-kubernetes-ingress/).

## <a name="connection-draining"></a>Vyprázdnění připojení

Vyprázdnění připojení vám pomůže provést řádné odebrání členů back-endového fondu při plánovaných servisních aktualizacích. Toto nastavení je povoleno prostřednictvím nastavení HTTP back-endu a lze je použít při vytváření pravidla u všech členů back-endového fondu. Po povolení Application Gateway zajistí, že všechny instance rušení registrace ve fondu back-endu neobdrží žádnou novou žádost, zatímco se existující požadavky dokončí v nakonfigurovaném časovém limitu. To platí pro instance back-end, které se explicitně odebraly z fondu back-endu prostřednictvím volání rozhraní API, a back-endové instance, které jsou nahlášené jako chybné v závislosti na sondách stavu.

## <a name="custom-error-pages"></a>Vlastní chybové stránky

Služba Application Gateway vám umožní vytvořit vlastní chybové stránky místo zobrazení výchozích chybových stránek. U vlastní chybové stránky můžete použít vlastní značky a rozložení.

Další informace najdete v tématu [vlastní chyby](custom-error.md).

## <a name="rewrite-http-headers"></a>Přepsání hlaviček HTTP

Hlavičky protokolu HTTP umožňují klientovi a serveru předat další informace s požadavkem nebo odpovědí. Přepsání těchto hlaviček HTTP vám pomůže dosáhnout několika důležitých scénářů, například:

- Přidání polí záhlaví souvisejících se zabezpečením, jako je HSTS/X-XSS-Protection.
- Odebrání polí hlavičky odpovědi, která mohou odhalit citlivé informace.
- Odstranění informací o portech z předávaných hlaviček od X-po.

Application Gateway podporuje možnost přidávat, odebírat nebo aktualizovat požadavky HTTP a hlavičky odpovědí, zatímco pakety požadavků a odpovědí přecházejí mezi klienty klienta a back-endové fondy. Poskytuje taky možnost přidat podmínky, abyste zajistili, že zadaná záhlaví se přepíší jenom v případě, že jsou splněné určité podmínky.

Další informace najdete v tématu [přepis hlaviček protokolu HTTP](rewrite-http-headers.md).

## <a name="sizing"></a>Velikost

Application Gateway Standard_v2 a WAF_v2 SKU lze nakonfigurovat pro nasazení s automatickým škálováním nebo pevnou velikostí. Tyto SKU nenabízejí různé velikosti instancí.

Application Gateway Standard a SKU WAF se momentálně nabízí ve třech velikostech: **Malá**, **střední**a **Velká**. Instance krátkodobého používání jsou určené pro scénáře vývoje a testování.

Úplný seznam omezení služby Application Gateway najdete na stránce [Omezení služby Application Gateway](../azure-subscription-service-limits.md?toc=%2fazure%2fapplication-gateway%2ftoc.json#application-gateway-limits).

Následující tabulka ukazuje průměrnou propustnost výkonu pro jednotlivé instance služby Application Gateway s povoleným přesměrováním zpracování SSL:

| Průměrná velikost odpovědi back-endu stránky | Malé | Střední | Velké |
| --- | --- | --- | --- |
| 6 KB |7,5 Mb/s |13 Mb/s |50 Mb/s |
| 100 KB |35 Mb/s |100 Mb/s |200 Mb/s |

> [!NOTE]
> Tyto hodnoty jsou přibližné hodnoty propustnosti služby Application Gateway. Skutečná propustnost závisí na různých podrobnostech o prostředí, jako jsou například průměrná velikost stránky, umístění back-endových instancí a doba zpracování potřebná k doručení stránky. Přesné údaje o výkonu získáte, když spustíte vlastní testy. Tyto hodnoty slouží jenom jako vodítko při plánování kapacity.

## <a name="next-steps"></a>Další postup

V závislosti na vašich požadavcích a prostředí můžete vytvořit testovací Application Gateway pomocí portálu Azure Portal, Azure PowerShellu nebo rozhraní Azure CLI:

- [Rychlé zprovoznění: Přímý webový provoz pomocí Azure Application Gateway-Azure Portal](quick-create-portal.md)
- [Rychlé zprovoznění: Přímý webový provoz pomocí Azure Application Gateway-Azure PowerShell](quick-create-powershell.md)
- [Rychlé zprovoznění: Přímý webový provoz pomocí Azure Application Gateway – Azure CLI](quick-create-cli.md)
