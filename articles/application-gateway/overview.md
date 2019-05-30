---
title: Co je Azure Application Gateway?
description: Přečtěte si, jak můžete pomocí aplikační brány Azure spravovat webové přenosy do vaší aplikace.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: overview
ms.custom: mvc
ms.date: 4/30/2019
ms.author: victorh
ms.openlocfilehash: 78dd4b31991a15d3d946c47c5394f64bb3afea95
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/27/2019
ms.locfileid: "64947268"
---
# <a name="what-is-azure-application-gateway"></a>Co je Azure Application Gateway?

Azure Application Gateway je nástroj pro vyrovnávání zatížení webových přenosů, který vám umožní spravovat provoz do webových aplikací. Nástroje pro vyrovnávání zatížení obvykle fungují na přenosové vrstvě (vrstva OSI 4 – TCP a UDP) a směrují provoz na základě zdrojové IP adresy a portu do cílové IP adresy a portu.

![Koncepční Application Gateway](media/overview/figure1-720.png)

Pomocí služby Application Gateway můžete provádět rozhodnutí o směrování na základě dalších atributů požadavku protokolu HTTP, třeba hlavičky cestu nebo hostitele identifikátoru URI. Můžete například směrovat provoz na základě příchozí adresy URL. Pokud tedy příchozí adresa URL obsahuje `/images`, můžete směrovat provoz na konkrétní sadu serverů (označovanou jako fond), která je nakonfigurovaná pro obrázky. Pokud `/video` je v adrese URL, že provoz se směruje do jiného fondu, který je optimalizovaný pro videa.

![imageURLroute](./media/application-gateway-url-route-overview/figure1-720.png)

Tento typ směrování se označuje jako vyrovnávání zatížení aplikační vrstvy (vrstva OSI 7). Azure Application Gateway umí směrování na základě adresy URL, ale to není vše.

Součástí Azure Application Gateway jsou následující funkce:

## <a name="secure-sockets-layer-ssl-termination"></a>Ukončení protokolu SSL (Secure Sockets Layer)

Služba Application Gateway podporuje ukončení protokolu SSL na bráně, po čemž provoz typicky teče nešifrován na back-endové servery. Tato funkce webovým serverům umožňuje snížení nákladné režie spojené s šifrováním a dešifrováním. Ale někdy nešifrovaná komunikace s servery nepřijatelnou možností. Může to být kvůli požadavkům na zabezpečení, požadavky na dodržování předpisů, nebo aplikace může přijímat pouze zabezpečená připojení. Pro tyto aplikace služba application gateway podporuje koncové šifrování protokolu SSL.

## <a name="autoscaling"></a>Automatické škálování

Application Gateway nebo WAF nasazení na základě Standard_v2 nebo WAF_v2 SKU podporují automatické škálování můžete vertikálně navýšit kapacitu a dolů podle aktuálních vzory zatížení provozu. Automatické škálování také eliminuje nutnost zvolit během zřizování velikost nasazení nebo počet instancí. Další informace o službě Application Gateway standard_v2 a funkcích WAF_v2 najdete v tématu [SKU v2 automatické škálování](application-gateway-autoscaling-zone-redundant.md).

## <a name="zone-redundancy"></a>Redundanci zón

Služba Application Gateway nebo WAF nasazení v rámci Standard_v2 nebo WAF_v2 SKU může zahrnovat více zón dostupnosti, nabízí lepší odolnost proti a není potřeba zřizovat samostatný brány Application Gateway v každé zóně.

## <a name="static-vip"></a>Statické virtuálních IP adres

Služba application gateway virtuální IP adresy na Standard_v2 nebo WAF_v2 SKU výhradně podporuje statického typu virtuálních IP adres. Tím se zajistí, že virtuální IP adresy přidružené k službě application gateway nemění i přes životní cyklus služby Application Gateway.

## <a name="web-application-firewall"></a>Brána firewall webových aplikací

Firewall webových aplikací (WAF) je funkce služby Application Gateway poskytující centralizovanou ochranu webových aplikací před běžným zneužitím a ohrožením zabezpečení. Firewall webových aplikací (WAF) je založený na [základní sadě pravidel OWASP (Open Web Application Security Project)](https://www.owasp.org/index.php/Category:OWASP_ModSecurity_Core_Rule_Set_Project) 3.0 nebo 2.2.9. 

Webové aplikace se čím dál častěji stávají cílem škodlivých útoků, které zneužívají běžně známé chyby zabezpečení. Mezi tyto běžné typy zneužití patří mimo jiné například útoky prostřednictvím injektáže SQL nebo skriptování mezi weby. Předcházet takovým útokům v kódu aplikace může být náročné a může vyžadovat pečlivou údržbu, opravy a monitorování mnoha vrstev topologie aplikace. Centralizovaný firewall webových aplikací značně zjednodušuje správu zabezpečení a nabízí správcům lepší ochranu aplikací před hrozbami neoprávněného vniknutí. Řešení Firewall webových aplikací (WAF) může také rychleji reagovat na ohrožení zabezpečení, protože opravuje známé chyby zabezpečení v centrálním umístění, namísto zabezpečování jednotlivých webových aplikací. Stávající aplikační brány je možné jednoduše převést na aplikační brány doplněné webovým aplikačním firewallem.

Další informace najdete v tématu [firewall webových aplikací (WAF) ve službě Application Gateway](https://docs.microsoft.com/azure/application-gateway/waf-overview)).

## <a name="url-based-routing"></a>Směrování na základě adresy URL

Směrování na základě cesty URL umožňuje směrovat provoz do fondů back-end serveru na základě cest URL požadavku. Jedním ze scénářů je směrování žádostí na různé typy obsahu do různých fondů.

Například žádosti na adresu `http://contoso.com/video/*` se směrují na VideoServerPool a žádosti na adresu `http://contoso.com/images/*` na ImageServerPool. Pokud nevyhovuje žádný vzor cesty, vybere se VychoziFondServeru.

Další informace najdete v tématu [směrování na základě adresy URL pomocí služby Application Gateway](https://docs.microsoft.com/azure/application-gateway/url-route-overview).

## <a name="multiple-site-hosting"></a>Hostování několika webů

Hostování několika webů umožňuje konfigurovat více než jeden web ve stejné instanci aplikační brány. Tato funkce umožňuje nakonfigurovat efektivnější topologii vašich nasazení tak, že přidáte až 100 webů do jedné služby application gateway. Každý web se dá přesměrovat na vlastní fond. Aplikační brána může například obsluhovat provoz pro `contoso.com` a `fabrikam.com` ze dvou fondů serverů s názvem ContosoServerPool a FabrikamServerPool.

Žádosti na adresu `http://contoso.com` se směrují na ContosoServerPool a žádosti na adresu `http://fabrikam.com` na FabrikamServerPool.

Podobně je možné ve stejném nasazení aplikační brány hostovat dvě poddomény stejné nadřazené domény. Příklady použití subdomén můžou zahrnovat adresy `http://blog.contoso.com` a `http://app.contoso.com` hostované v jednom nasazení aplikační brány.

Další informace najdete v tématu [hostování více webů pomocí služby Application Gateway](https://docs.microsoft.com/azure/application-gateway/multiple-site-overview).

## <a name="redirection"></a>Přesměrování

Běžným scénářem pro mnoho webových aplikací je podpora automatického přesměrování HTTP na HTTPS, aby se zajistilo, že veškerá komunikace mezi aplikací a jejími uživateli probíhá přes šifrovanou cestu.

V minulosti jste mohli použít techniky, jako je vytvoření vyhrazeného fondu, jehož jediným účelem je pro přesměrování požadavků, které obdrží na protokolu HTTP na HTTPS. Aplikační brána podporuje možnost přesměrovat provoz na Application Gateway. To zjednodušuje konfiguraci aplikací, optimalizuje využití prostředků a podporuje nové scénáře přesměrování, včetně globálního přesměrování a přesměrování na základě cesty. Podpora služby Application Gateway přesměrování není omezený na HTTP na HTTPS přesměrování samostatně. Jde o obecný mechanismus přesměrování, takže můžete provoz přesměrovat z kteréhokoliv portu a na kterýkoliv port, který definujete pomocí pravidel. Také podporuje přesměrování na externí web.

Podpora přesměrování Application Gateway nabízí následující možnosti:

- Globální přesměrování z jednoho portu na jiný port ve službě Application Gateway. To umožňuje přesměrování z HTTP na HTTPS na webu.
- Přesměrování na základě cesty. Tento typ přesměrování umožňuje přesměrování z HTTP na HTTPS jenom v konkrétní oblasti webu, například v oblasti nákupního košíku označené jako `/cart/*`.
- Přesměrování na externí web.

Další informace najdete v tématu [síťový provoz přesměruje](https://docs.microsoft.com/azure/application-gateway/redirect-overview) pomocí služby Application Gateway.

## <a name="session-affinity"></a>Spřažení relací

Funkce spřažení relací na základě souborů cookie je užitečná v případě, že chcete zachovat uživatelskou relaci na stejném serveru. Pomocí souborů cookie spravovaných bránou umí služba Application Gateway směrovat následný provoz z uživatelské relace ke zpracování na stejný server. To je důležité v případech, kdy se stav jednotlivých uživatelských relací ukládá místně na serveru.

## <a name="websocket-and-http2-traffic"></a>Provoz přes protokoly Websocket a HTTP/2

Application Gateway poskytuje nativní podporu pro protokoly WebSocket a HTTP/2. Neexistuje žádné uživatelsky konfigurovatelné nastavení pro selektivní povolení nebo zakázání podpory protokolu WebSocket.

Protokoly WebSocket a HTTP/2 umožňují plně duplexní komunikaci mezi serverem a klientem přes dlouhotrvající připojení TCP. To umožňuje interaktivnější komunikaci mezi webovým serverem a klientem, která může být obousměrná, aniž by bylo nutné dotazování, jak se to vyžaduje v implementacích založených na protokolu HTTP. Tyto protokoly mají nízké nároky, na rozdíl od protokolu HTTP a můžete znovu použít stejné připojení TCP pro více požadavku/odpovědi, což vede k mnohem efektivnější využití prostředků. Tyto protokoly jsou navrženy pro práci přes tradiční porty HTTP 80 a 443.

Další informace najdete v tématu [podpora protokolu WebSocket](https://docs.microsoft.com/azure/application-gateway/application-gateway-websocket) a [podpora HTTP/2](https://docs.microsoft.com/azure/application-gateway/configuration-overview#http2-support).

## <a name="azure-kubernetes-service-aks-ingress-controller-preview"></a>Preview řadiče příchozího přenosu dat služby Azure Kubernetes Service (AKS) 

Řadič příchozího přenosu dat služby Application Gateway běží jako pod v rámci clusteru AKS a umožňuje službě Application Gateway fungovat jako příchozí přenos dat pro cluster AKS. To je podporováno pouze v2 Application Gateway.

Další informace najdete v tématu [Řadič příchozího přenosu dat služby Azure Application Gateway](https://azure.github.io/application-gateway-kubernetes-ingress/).

## <a name="connection-draining"></a>Vyprázdnění připojení

Vyprázdnění připojení vám pomůže provést řádné odebrání členů back-endového fondu při plánovaných servisních aktualizacích. Toto nastavení je povoleno prostřednictvím nastavení HTTP back-endu a lze je použít při vytváření pravidla u všech členů back-endového fondu. Po povolení Application Gateway zajišťuje, že všechny zrušit registraci instance back-endového fondu nebudou přijímat žádné nové žádosti o zároveň umožní existující žádosti o dokončení v rámci nakonfigurovaného časového limitu. To platí pro back-end instance, které jsou výslovně odebrali z back-endového fondu podle volání rozhraní API a back-endových instancí, které jsou hlášený jako špatný, počítáno od sond stavu.

## <a name="custom-error-pages"></a>Vlastní chybové stránky

Služba Application Gateway vám umožní vytvořit vlastní chybové stránky místo zobrazení výchozích chybových stránek. U vlastní chybové stránky můžete použít vlastní značky a rozložení.

Další informace najdete v tématu [hlavičky protokolu HTTP přepsat](rewrite-http-headers.md).

## <a name="rewrite-http-headers"></a>Přepsání hlaviček HTTP

Hlavičky protokolu HTTP umožňují klientem a serverem pro předávání dalších informací s požadavku nebo odpovědi. Přepisování tyto hlavičky protokolu HTTP umožňuje provádět několik důležitých scénářů, jako například:

- Přidání pole hlavičky související se zabezpečením, jako HSTS / X-XSS ochrany.
- Odebrání pole hlavičky odpovědi, které může odhalit citlivé informace.
- Údaje o portech z hlavičky X-předané-pro odstranění.

Služba Application Gateway podporuje možnost přidat, odebrat nebo aktualizovat hlavičky požadavku a odpovědi protokolu HTTP, zatímco pakety žádostí a odpovědí přesunout mezi klientem a back endové fondy. Je také poskytuje možnost přidání podmínky k zajištění, že určených hlaviček jsou zapsány pouze v případě, že jsou splněny určité podmínky.

Další informace najdete v tématu [hlavičky protokolu HTTP přepsat](rewrite-http-headers.md).

## <a name="sizing"></a>Velikost

Application Gateway Standard_v2 a WAF_v2 SKU můžete nakonfigurovaný pro automatické škálování nebo pevné velikosti nasazení. Tyto SKU nenabízí velikostí jinou instancí.

Application Gateway Standard a WAF SKU je teď nabízíme ve třech velikostech: **Malé**, **střední**, a **velké**. Instance krátkodobého používání jsou určené pro scénáře vývoje a testování.

Úplný seznam omezení služby Application Gateway najdete na stránce [Omezení služby Application Gateway](../azure-subscription-service-limits.md?toc=%2fazure%2fapplication-gateway%2ftoc.json#application-gateway-limits).

Následující tabulka ukazuje průměrnou propustnost výkonu pro jednotlivé instance služby Application Gateway s povoleným přesměrováním zpracování SSL:

| Průměrná velikost odpovědi back endové stránky | Malé | Střední | Velké |
| --- | --- | --- | --- |
| 6 KB |7,5 Mb/s |13 Mb/s |50 Mb/s |
| 100 KB |35 Mb/s |100 Mb/s |200 Mb/s |

> [!NOTE]
> Tyto hodnoty jsou přibližné hodnoty propustnosti služby Application Gateway. Skutečná propustnost závisí na různých podrobnostech o prostředí, jako jsou například průměrná velikost stránky, umístění back-endových instancí a doba zpracování potřebná k doručení stránky. Přesné údaje o výkonu získáte, když spustíte vlastní testy. Tyto hodnoty slouží jenom jako vodítko při plánování kapacity.

## <a name="next-steps"></a>Další postup

V závislosti na vašich požadavcích a prostředí můžete vytvořit testovací Application Gateway pomocí portálu Azure Portal, Azure PowerShellu nebo rozhraní Azure CLI:

- [Rychlé zprovoznění: Přímé webového provozu s využitím Azure Application Gateway – Azure portal](quick-create-portal.md)
- [Rychlé zprovoznění: Přímé webového provozu s využitím Azure Application Gateway – Azure PowerShell](quick-create-powershell.md)
- [Rychlé zprovoznění: Přímé webového provozu s využitím Azure Application Gateway – rozhraní příkazového řádku Azure](quick-create-cli.md)