---
title: Funkce Azure Application Gateway
description: Přečtěte si o funkcích Azure Application Gateway
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: conceptual
ms.date: 09/25/2020
ms.author: victorh
ms.openlocfilehash: ba9f42bc932a37e1052f17db2ae00413e0769d59
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "91355733"
---
# <a name="azure-application-gateway-features"></a>Funkce Azure Application Gateway

[Azure Application Gateway](overview.md) je nástroj pro vyrovnávání zatížení webového provozu, který umožňuje spravovat provoz do webových aplikací.

![Application Gateway koncepční](media/overview/figure1-720.png)

Application Gateway obsahuje následující funkce:

- [Ukončení SSL (Secure Sockets Layer) (SSL/TLS)](#secure-sockets-layer-ssltls-termination)
- [Automatické škálování](#autoscaling)
- [Zónová redundance](#zone-redundancy)
- [Statická virtuální IP adresa](#static-vip)
- [Firewall webových aplikací](#web-application-firewall)
- [Kontroler Ingress pro AKS](#ingress-controller-for-aks)
- [Směrování na základě adresy URL](#url-based-routing)
- [Hostování několika webů](#multiple-site-hosting)
- [Přesměrování](#redirection)
- [Spřažení relací](#session-affinity)
- [Provoz přes protokoly Websocket a HTTP/2](#websocket-and-http2-traffic)
- [Vyprázdnění připojení](#connection-draining)
- [Vlastní chybové stránky](#custom-error-pages)
- [Přepsání hlaviček HTTP a adres URL](#rewrite-http-headers-and-url)
- [Velikosti](#sizing)

## <a name="secure-sockets-layer-ssltls-termination"></a>Ukončení SSL (Secure Sockets Layer) (SSL/TLS)

Application Gateway podporuje ukončení protokolu SSL/TLS v bráně, po kterém provoz obvykle přechází do back-end serverů. Tato funkce webovým serverům umožňuje snížení nákladné režie spojené s šifrováním a dešifrováním. Někdy ale nešifrovaná komunikace na servery není přijatelnou možností. Důvodem může být to, že požadavky na zabezpečení, požadavky na dodržování předpisů nebo aplikace můžou přijímat jenom zabezpečené připojení. Pro tyto aplikace Aplikační brána podporuje koncové šifrování protokolu SSL/TLS.

Další informace naleznete v tématu [Přehled ukončení protokolu SSL a koncového šifrování protokolu SSL s Application Gateway](ssl-overview.md)

## <a name="autoscaling"></a>Automatické škálování

Application Gateway Standard_v2 podporuje automatické škálování a umožňuje horizontální navýšení nebo snížení kapacity na základě změn schémat zatížení provozu. Automatické škálování také eliminuje nutnost zvolit během zřizování velikost nasazení nebo počet instancí. 

Další informace o funkcích Application Gateway Standard_v2 najdete v tématu Automatické [škálování v2 SKU](application-gateway-autoscaling-zone-redundant.md).

## <a name="zone-redundancy"></a>Zónová redundance

Application Gateway Standard_v2 může zahrnovat více Zóny dostupnosti, což nabízí lepší odolnost proti chybám a odstraňuje nutnost zřídit samostatné aplikační brány v každé zóně.

## <a name="static-vip"></a>Statická virtuální IP adresa

SKU služby Application Gateway Standard_v2 podporuje výhradně statický typ VIP. Tím se zajistí, že se virtuální IP adresa přidružená k aplikační bráně nemění ani po dobu života Application Gateway.

## <a name="web-application-firewall"></a>Firewall webových aplikací

Firewall webových aplikací (WAF) je služba, která poskytuje centralizovanou ochranu webových aplikací před běžným zneužitím a ohrožením zabezpečení. WAF je založená na pravidlech z [OWASP (Open Web Application Security Project) základních sad pravidel](https://www.owasp.org/index.php/Category:OWASP_ModSecurity_Core_Rule_Set_Project) 3,1 (jenom WAF_v2), 3,0 a 2.2.9. 

Webové aplikace se čím dál častěji stávají cílem škodlivých útoků, které zneužívají běžně známé chyby zabezpečení. Mezi tyto běžné typy zneužití patří mimo jiné například útoky prostřednictvím injektáže SQL nebo skriptování mezi weby. Předcházet takovým útokům v kódu aplikace může být náročné a může vyžadovat pečlivou údržbu, opravy a monitorování mnoha vrstev topologie aplikace. Centralizovaný firewall webových aplikací značně zjednodušuje správu zabezpečení a nabízí správcům lepší ochranu aplikací před hrozbami neoprávněného vniknutí. Řešení Firewall webových aplikací (WAF) může také rychleji reagovat na ohrožení zabezpečení, protože opravuje známé chyby zabezpečení v centrálním umístění, namísto zabezpečování jednotlivých webových aplikací. Existující aplikační brány je možné snadno převést na bránu Application Gateway s povoleným firewallem webových aplikací.

Další informace najdete v tématu [co je firewall webových aplikací Azure?](../web-application-firewall/overview.md).

## <a name="ingress-controller-for-aks"></a>Kontroler Ingress pro AKS
AGIC () umožňuje používat Application Gateway jako příchozí přenosy pro cluster [Azure Kubernetes Service (AKS)](https://azure.microsoft.com/services/kubernetes-service/) . Application Gateway 

Kontroler příchozího provozu funguje jako pod clusterem AKS a využívá [Kubernetes prostředky](https://kubernetes.io/docs/concepts/services-networking/ingress/) příchozího přenosu dat a převede je na konfiguraci Application Gateway, která umožňuje bráně vyrovnávat zatížení v Kubernetes luskech. Kontroler příchozího přenosu dat podporuje jenom Application Gateway Standard_v2 a WAF_v2 SKU. 

Další informace najdete v tématu Application Gateway řadič příchozího přenosu dat [(AGIC)](ingress-controller-overview.md).

## <a name="url-based-routing"></a>Směrování na základě adresy URL

Směrování na základě cesty URL umožňuje směrovat provoz do fondů back-end serveru na základě cest URL požadavku. Jedním ze scénářů je směrování žádostí na různé typy obsahu do různých fondů.

Například žádosti na adresu `http://contoso.com/video/*` se směrují na VideoServerPool a žádosti na adresu `http://contoso.com/images/*` na ImageServerPool. Pokud nevyhovuje žádný vzor cesty, vybere se VychoziFondServeru.

Další informace najdete v tématu [Přehled směrování na základě cest URL](url-route-overview.md).

## <a name="multiple-site-hosting"></a>Hostování několika webů

Pomocí Application Gateway můžete nakonfigurovat směrování na základě názvu hostitele nebo názvu domény pro více než jednu webovou aplikaci na stejné aplikační bráně. Díky možnosti přidat do jedné služby Application Gateway více než 100 webů můžete nakonfigurovat efektivnější topologii vašich nasazení. Každou stránku lze přesměrovat na vlastní back-endový fond. Příklad: na IP adresu služby Application Gateway odkazují tři domény – contoso.com, fabrikam.com a adatum.com. Můžete vytvořit tři naslouchací procesy pro více webů a jednotlivé naslouchací procesy nakonfigurovat pro příslušené nastavení portu a protokolu. 

Požadavky na `http://contoso.com` jsou směrovány na fondserverucontoso, `http://fabrikam.com` jsou směrovány do fondserverufabrikam a tak dále.

Podobně je možné ve stejném nasazení aplikační brány hostovat dvě poddomény stejné nadřazené domény. Příklady použití subdomén můžou zahrnovat adresy `http://blog.contoso.com` a `http://app.contoso.com` hostované v jednom nasazení aplikační brány. Další informace najdete v tématu [Application Gateway hostování více lokalit](multiple-site-overview.md).

V naslouchacím procesu pro více webů můžete také definovat názvy hostitelů se zástupnými znaky a až 5 názvů hostitelů na naslouchací proces. Další informace najdete v tématu [názvy hostitelů se zástupnými znaky ve službě Listener (Preview)](multiple-site-overview.md#wildcard-host-names-in-listener-preview).

## <a name="redirection"></a>Přesměrování

Běžným scénářem pro mnoho webových aplikací je podpora automatického přesměrování HTTP na HTTPS, aby se zajistilo, že veškerá komunikace mezi aplikací a jejími uživateli probíhá přes šifrovanou cestu.

V minulosti jste mohli používat techniky, jako je například vytvoření vyhrazeného fondu, jehož jediným účelem je přesměrování požadavků, které přijímá v HTTP na HTTPS. Aplikační brána podporuje možnost přesměrovat provoz na Application Gateway. To zjednodušuje konfiguraci aplikací, optimalizuje využití prostředků a podporuje nové scénáře přesměrování, včetně globálního přesměrování a přesměrování na základě cesty. Podpora přesměrování Application Gateway není omezená jenom na přesměrování HTTP na HTTPS. Jde o obecný mechanismus přesměrování, takže můžete provoz přesměrovat z kteréhokoliv portu a na kterýkoliv port, který definujete pomocí pravidel. Také podporuje přesměrování na externí web.

Podpora přesměrování Application Gateway nabízí následující možnosti:

- Globální přesměrování z jednoho portu na jiný port ve službě Application Gateway. To umožňuje přesměrování z HTTP na HTTPS na webu.
- Přesměrování na základě cesty. Tento typ přesměrování umožňuje přesměrování z HTTP na HTTPS jenom v konkrétní oblasti webu, například v oblasti nákupního košíku označené jako `/cart/*`.
- Přesměrování na externí web.

Další informace najdete v tématu [Přehled přesměrování Application Gateway](redirect-overview.md).

## <a name="session-affinity"></a>Spřažení relací

Funkce spřažení relací na základě souborů cookie je užitečná v případě, že chcete zachovat uživatelskou relaci na stejném serveru. Pomocí souborů cookie spravovaných bránou umí služba Application Gateway směrovat následný provoz z uživatelské relace ke zpracování na stejný server. To je důležité v případech, kdy se stav jednotlivých uživatelských relací ukládá místně na serveru.

Další informace najdete v tématu [Jak funguje Aplikační brána](how-application-gateway-works.md#modifications-to-the-request).

## <a name="websocket-and-http2-traffic"></a>Provoz přes protokoly Websocket a HTTP/2

Application Gateway poskytuje nativní podporu pro protokoly WebSocket a HTTP/2. Neexistuje žádné uživatelsky konfigurovatelné nastavení pro selektivní povolení nebo zakázání podpory protokolu WebSocket.

Protokoly WebSocket a HTTP/2 umožňují plně duplexní komunikaci mezi serverem a klientem přes dlouhotrvající připojení TCP. To umožňuje interaktivnější komunikaci mezi webovým serverem a klientem, která může být obousměrná, aniž by bylo nutné dotazování, jak se to vyžaduje v implementacích založených na protokolu HTTP. Tyto protokoly mají nízkou režii, na rozdíl od HTTP, a umožňují opakované použití stejného připojení TCP pro více požadavků nebo odpovědí, což má za následek efektivnější využití prostředků. Tyto protokoly jsou navrženy pro práci přes tradiční porty HTTP 80 a 443.

Další informace najdete v tématu Podpora [WebSocket](application-gateway-websocket.md) a [Podpora protokolu HTTP/2](configuration-listeners.md#http2-support).

## <a name="connection-draining"></a>Vyprázdnění připojení

Vyprázdnění připojení vám pomůže provést řádné odebrání členů back-endového fondu při plánovaných servisních aktualizacích. Toto nastavení je povoleno prostřednictvím nastavení HTTP back-endu a lze je použít při vytváření pravidla u všech členů back-endového fondu. Po povolení Application Gateway zajistí, aby všechny odregistrované instance back-endu nedostaly žádné nové žádosti, a současně umožnily dokončení stávajících požadavků v nakonfigurovaném časovém limitu. To platí pro instance back-end, které se explicitně odebraly z fondu back-endu pomocí změny konfigurace uživatele, a back-endové instance, které jsou nahlášené jako chybné v závislosti na sondách stavu. Jedinou výjimkou jsou požadavky vázané na zrušení registrace instancí, které byly odregistrovány explicitně, vzhledem k spřažení relace spravované bránou a nadále budou zavedeny do proxy serverů odregistrované instance.

Další informace najdete v tématu [Přehled konfigurace Application Gateway](configuration-http-settings.md#connection-draining).

## <a name="custom-error-pages"></a>Vlastní chybové stránky

Služba Application Gateway vám umožní vytvořit vlastní chybové stránky místo zobrazení výchozích chybových stránek. U vlastní chybové stránky můžete použít vlastní branding a rozložení.

Další informace najdete v tématu [vlastní chyby](custom-error.md).

## <a name="rewrite-http-headers-and-url"></a>Přepsání hlaviček HTTP a adres URL

Hlavičky protokolu HTTP umožňují klientovi a serveru předat další informace s požadavkem nebo odpovědí. Přepsání těchto hlaviček HTTP vám pomůže dosáhnout několika důležitých scénářů, například:

- Přidání polí záhlaví souvisejících se zabezpečením, jako je HSTS/X-XSS-Protection.
- Odebrání polí hlavičky odpovědi, která mohou odhalit citlivé informace.
- Odstranění informací o portech z předávaných hlaviček od X-po.

Když se pakety požadavků a odpovědí pohybují mezi klientem a back-endovými fondy, služba Application Gateway a WAF se skladovou položkou v2 umožňují přidat, odebrat nebo aktualizovat hlavičky požadavků a odpovědí HTTP. Můžete také přepsat adresy URL, parametry řetězců dotazu a název hostitele. S využitím přepsání adres URL a směrování na základě cest URL můžete směrovat požadavky do jednoho z back-endových fondů na základě původní cesty nebo přepsané cesty s použitím možnosti Znovu vyhodnotit mapu cesty. 

Můžete také přidat podmínky, které zajistí, že se zadané hlavičky nebo adresy URL přepíší pouze při splnění těchto podmínek. Tyto podmínky vycházejí z informací o požadavku a odpovědi.

Další informace najdete v tématu [přepis hlaviček protokolu HTTP a adresy URL](rewrite-http-headers-url.md).

## <a name="sizing"></a>Velikosti

Pro nasazení automatického škálování nebo pevné velikosti se dá nakonfigurovat Application Gateway Standard_v2. SKU v2 nenabízí různé velikosti instancí. Další informace o výkonu a cenách v2 najdete v tématu Automatické [škálování v2](application-gateway-autoscaling-zone-redundant.md) a [Princip cen](understanding-pricing.md).

Verze Application Gateway Standard (V1) se nabízí ve třech velikostech: **malá**, **střední** a **Velká**. Instance krátkodobého používání jsou určené pro scénáře vývoje a testování.

Úplný seznam omezení služby Application Gateway najdete na stránce [Omezení služby Application Gateway](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fapplication-gateway%2ftoc.json#application-gateway-limits).

Následující tabulka ukazuje průměrnou propustnost výkonu pro jednotlivé instance služby Application Gateway V1 s povoleným snižováním zatížení SSL:

| Průměrná velikost odpovědi back-endu stránky | Malá | Střední | Velká |
| --- | --- | --- | --- |
| 6 KB |7,5 Mb/s |13 Mb/s |50 Mb/s |
| 100 KB |35 Mb/s |100 Mb/s |200 Mb/s |

> [!NOTE]
> Tyto hodnoty jsou přibližné hodnoty propustnosti služby Application Gateway. Skutečná propustnost závisí na různých podrobnostech o prostředí, jako jsou například průměrná velikost stránky, umístění back-endových instancí a doba zpracování potřebná k doručení stránky. Přesné údaje o výkonu získáte, když spustíte vlastní testy. Tyto hodnoty slouží jenom jako vodítko při plánování kapacity.

## <a name="version-feature-comparison"></a>Porovnání funkcí verze

Porovnání funkcí Application Gateway v1-v2 najdete v tématu Automatické [škálování a redundantní Application Gateway v2](application-gateway-autoscaling-zone-redundant.md#feature-comparison-between-v1-sku-and-v2-sku) .

## <a name="next-steps"></a>Další kroky

- Informace o tom, jak Application Gateway funguje – [Jak funguje Aplikační brána](how-application-gateway-works.md)