---
title: Funkce aplikační brány Azure
description: Informace o funkcích Azure Application Gateway
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: conceptual
ms.date: 03/04/2020
ms.author: victorh
ms.openlocfilehash: 550d9f4f5396b2165260e39cd28222b083dd6756
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79279985"
---
# <a name="azure-application-gateway-features"></a>Funkce aplikační brány Azure

[Azure Application Gateway](overview.md) je nástroj pro vyrovnávání zatížení webového provozu, který umožňuje spravovat provoz do webových aplikací.

![Koncepční brána aplikace](media/overview/figure1-720.png)

Aplikační brána obsahuje následující funkce:

- [Ukončení ssl (SSL/TLS)](#secure-sockets-layer-ssltls-termination)
- [Automatické škálování](#autoscaling)
- [Redundance zóny](#zone-redundancy)
- [Statické VIP](#static-vip)
- [Brána firewall webové aplikace](#web-application-firewall)
- [Kontroler Ingress pro AKS](#ingress-controller-for-aks)
- [Směrování založené na adrese URL](#url-based-routing)
- [Hostování několika webů](#multiple-site-hosting)
- [Přesměrování](#redirection)
- [Spřažení relací](#session-affinity)
- [Provoz přes protokoly Websocket a HTTP/2](#websocket-and-http2-traffic)
- [Vyprázdnění připojení](#connection-draining)
- [Stránky vlastních chyb](#custom-error-pages)
- [Přepsání hlaviček HTTP](#rewrite-http-headers)
- [Velikosti](#sizing)

## <a name="secure-sockets-layer-ssltls-termination"></a>Ukončení ssl (SSL/TLS)

Aplikační brána podporuje ukončení SSL/TLS v bráně, po kterém přenosy obvykle proudí nešifrované na back-endové servery. Tato funkce webovým serverům umožňuje snížení nákladné režie spojené s šifrováním a dešifrováním. Někdy však nešifrovaná komunikace se servery není přijatelnou volbou. To může být z důvodu požadavků na zabezpečení, požadavky na dodržování předpisů nebo aplikace může přijmout pouze zabezpečené připojení. Pro tyto aplikace podporuje aplikační brána šifrování SSL/TLS od konce.

Další informace naleznete v [tématu Přehled ukončení SSL a ukončení ssl s aplikační bránou](ssl-overview.md)

## <a name="autoscaling"></a>Automatické škálování

Standard_v2 brány aplikace podporuje automatické škálování a může vertikálně navýšit nebo snížit kapacitu na základě měnících se vzorů zatížení provozu. Automatické škálování také eliminuje nutnost zvolit během zřizování velikost nasazení nebo počet instancí. 

Další informace o funkcích Standard_v2 brány aplikace naleznete v [tématu Automatické škálování v2 skladové položky](application-gateway-autoscaling-zone-redundant.md).

## <a name="zone-redundancy"></a>Redundance zóny

Standard_v2 aplikační brány může protábovat více zón dostupnosti, nabízí lepší odolnost proti chybám a odstraňuje potřebu zřídit samostatné aplikační brány v každé zóně.

## <a name="static-vip"></a>Statické VIP

Aplikační brána Standard_v2 skladové položky podporuje výhradně statický typ VIP. Tím je zajištěno, že virtuální ip adresy přidružené k bráně aplikace nezmění ani po dobu životnosti brány aplikace.

## <a name="web-application-firewall"></a>Web Application Firewall (Brána firewall webových aplikací)

Firewall webových aplikací (WEB Application Firewall) je služba, která poskytuje centralizovanou ochranu webových aplikací před běžnými zneužitími a chybami zabezpečení. WAF je založen na pravidlech ze [základních sad pravidel OWASP (Open Web Application Security Project)](https://www.owasp.org/index.php/Category:OWASP_ModSecurity_Core_Rule_Set_Project) 3.1 (pouze WAF_v2), 3.0 a 2.2.9. 

Webové aplikace se čím dál častěji stávají cílem škodlivých útoků, které zneužívají běžně známé chyby zabezpečení. Mezi tyto běžné typy zneužití patří mimo jiné například útoky prostřednictvím injektáže SQL nebo skriptování mezi weby. Předcházet takovým útokům v kódu aplikace může být náročné a může vyžadovat pečlivou údržbu, opravy a monitorování mnoha vrstev topologie aplikace. Centralizovaný firewall webových aplikací značně zjednodušuje správu zabezpečení a nabízí správcům lepší ochranu aplikací před hrozbami neoprávněného vniknutí. Řešení Firewall webových aplikací (WAF) může také rychleji reagovat na ohrožení zabezpečení, protože opravuje známé chyby zabezpečení v centrálním umístění, namísto zabezpečování jednotlivých webových aplikací. Existující aplikační brány lze snadno převést na bránu aplikací s povolenou bránou webových aplikací.

Další informace najdete v tématu [Co je brána firewall pro webové aplikace Azure?](../web-application-firewall/overview.md).

## <a name="ingress-controller-for-aks"></a>Kontroler Ingress pro AKS
Řadič příchozího přenosu dat aplikační brány (AGIC) umožňuje používat aplikační bránu jako příchozí přenos dat pro cluster [služby Azure Kubernetes Service (AKS).](https://azure.microsoft.com/services/kubernetes-service/) 

Řadič příchozího přenosu dat běží jako pod v rámci clusteru AKS a spotřebovává [Kubernetes Ingress Resources](https://kubernetes.io/docs/concepts/services-networking/ingress/) a převede je na konfiguraci aplikační brány, která umožňuje bráně vyvažování provozu na pody Kubernetes. Řadič příchozího přenosu dat podporuje pouze Standard_v2 aplikační brány a WAF_v2 jednotkách. 

Další informace naleznete [v tématu Řadič příchozího přenosu dat (AGIC)](ingress-controller-overview.md)aplikační brány .

## <a name="url-based-routing"></a>Směrování na základě adresy URL

Směrování na základě cesty URL umožňuje směrovat provoz do fondů back-end serveru na základě cest URL požadavku. Jedním ze scénářů je směrování žádostí na různé typy obsahu do různých fondů.

Například žádosti na adresu `http://contoso.com/video/*` se směrují na VideoServerPool a žádosti na adresu `http://contoso.com/images/*` na ImageServerPool. Pokud nevyhovuje žádný vzor cesty, vybere se VychoziFondServeru.

Další informace naleznete v tématu [Přehled směrování na základě cesty URL](url-route-overview.md).

## <a name="multiple-site-hosting"></a>Hostování několika webů

Hostování několika webů umožňuje konfigurovat více než jeden web ve stejné instanci aplikační brány. Tato funkce umožňuje nakonfigurovat efektivnější topologii pro vaše nasazení přidáním až 100 webových serverů do jedné aplikační brány (pro optimální výkon). Každý web se dá přesměrovat na vlastní fond. Aplikační brána může například obsluhovat provoz pro `contoso.com` a `fabrikam.com` ze dvou fondů serverů s názvem ContosoServerPool a FabrikamServerPool.

Žádosti na adresu `http://contoso.com` se směrují na ContosoServerPool a žádosti na adresu `http://fabrikam.com` na FabrikamServerPool.

Podobně je možné ve stejném nasazení aplikační brány hostovat dvě poddomény stejné nadřazené domény. Příklady použití subdomén můžou zahrnovat adresy `http://blog.contoso.com` a `http://app.contoso.com` hostované v jednom nasazení aplikační brány.

Další informace naleznete v [tématu Application Gateway multiple site hosting](multiple-site-overview.md).

## <a name="redirection"></a>Přesměrování

Běžným scénářem pro mnoho webových aplikací je podpora automatického přesměrování HTTP na HTTPS, aby se zajistilo, že veškerá komunikace mezi aplikací a jejími uživateli probíhá přes šifrovanou cestu.

V minulosti jste pravděpodobně použili techniky, jako je například vytvoření vyhrazeného fondu, jehož jediným účelem je přesměrovat požadavky, které obdrží v protokolu HTTP na protokol HTTPS. Aplikační brána podporuje možnost přesměrovat provoz na Application Gateway. To zjednodušuje konfiguraci aplikací, optimalizuje využití prostředků a podporuje nové scénáře přesměrování, včetně globálního přesměrování a přesměrování na základě cesty. Podpora přesměrování aplikační brány není omezena na http na samotné přesměrování HTTPS. Jde o obecný mechanismus přesměrování, takže můžete provoz přesměrovat z kteréhokoliv portu a na kterýkoliv port, který definujete pomocí pravidel. Také podporuje přesměrování na externí web.

Podpora přesměrování Application Gateway nabízí následující možnosti:

- Globální přesměrování z jednoho portu na jiný port ve službě Application Gateway. To umožňuje přesměrování z HTTP na HTTPS na webu.
- Přesměrování na základě cesty. Tento typ přesměrování umožňuje přesměrování z HTTP na HTTPS jenom v konkrétní oblasti webu, například v oblasti nákupního košíku označené jako `/cart/*`.
- Přesměrování na externí web.

Další informace naleznete v tématu [Application Gateway redirect overview](redirect-overview.md).

## <a name="session-affinity"></a>Spřažení relací

Funkce spřažení relací na základě souborů cookie je užitečná v případě, že chcete zachovat uživatelskou relaci na stejném serveru. Pomocí souborů cookie spravovaných bránou umí služba Application Gateway směrovat následný provoz z uživatelské relace ke zpracování na stejný server. To je důležité v případech, kdy se stav jednotlivých uživatelských relací ukládá místně na serveru.

Další informace naleznete [v tématu Jak funguje aplikační brána](how-application-gateway-works.md#modifications-to-the-request).

## <a name="websocket-and-http2-traffic"></a>Provoz přes protokoly Websocket a HTTP/2

Application Gateway poskytuje nativní podporu pro protokoly WebSocket a HTTP/2. Neexistuje žádné uživatelsky konfigurovatelné nastavení pro selektivní povolení nebo zakázání podpory protokolu WebSocket.

Protokoly WebSocket a HTTP/2 umožňují plně duplexní komunikaci mezi serverem a klientem přes dlouhotrvající připojení TCP. To umožňuje interaktivnější komunikaci mezi webovým serverem a klientem, která může být obousměrná, aniž by bylo nutné dotazování, jak se to vyžaduje v implementacích založených na protokolu HTTP. Tyto protokoly mají nízkou režii, na rozdíl od protokolu HTTP, a mohou znovu použít stejné připojení TCP pro více požadavků a odpovědí, což má za následek efektivnější využití prostředků. Tyto protokoly jsou navrženy pro práci přes tradiční porty HTTP 80 a 443.

Další informace naleznete v [tématu WebSocket support](application-gateway-websocket.md) a [HTTP/2 support](configuration-overview.md#http2-support).

## <a name="connection-draining"></a>Vyprázdnění připojení

Vyprázdnění připojení vám pomůže provést řádné odebrání členů back-endového fondu při plánovaných servisních aktualizacích. Toto nastavení je povoleno prostřednictvím nastavení HTTP back-endu a lze je použít při vytváření pravidla u všech členů back-endového fondu. Jakmile je aplikace povolena, application gateway zajišťuje, že všechny instance zrušení registrace back-endového fondu neobdrží žádný nový požadavek a současně povolí dokončení existujících požadavků v rámci nakonfigurovaného časového limitu. To platí pro obě back-endové instance, které jsou explicitně odebrány z back-endového fondu změnou konfigurace uživatele, a back-endové instance, které jsou hlášeny jako nefunkční, jak je určeno sondami stavu. Jedinou výjimkou jsou požadavky vázané na zrušení registrace instancí, které byly explicitně zrušeny, z důvodu spřažení relací spravované bránou a nadále jsou proxied k deregistrace instancí.

Další informace naleznete v tématu [Přehled konfigurace aplikační brány](configuration-overview.md#connection-draining).

## <a name="custom-error-pages"></a>Stránky vlastních chyb

Služba Application Gateway vám umožní vytvořit vlastní chybové stránky místo zobrazení výchozích chybových stránek. U vlastní chybové stránky můžete použít vlastní značky a rozložení.

Další informace naleznete [v tématu Vlastní chyby](custom-error.md).

## <a name="rewrite-http-headers"></a>Přepsání hlaviček HTTP

Hlavičky PROTOKOLU HTTP umožňují klientovi a serveru předat další informace s požadavkem nebo odpovědí. Přepsání těchto záhlaví protokolu HTTP vám pomůže provést několik důležitých scénářů, například:

- Přidání polí záhlaví souvisejících se zabezpečením, jako je HSTS / X-XSS-Protection.
- Odebrání polí záhlaví odpovědi, která mohou odhalit citlivé informace.
- Odstranění informací o portu ze záhlaví X-Forwarded-For.

Aplikační brána podporuje možnost přidávat, odebírat nebo aktualizovat hlavičky požadavků a odpovědí HTTP, zatímco pakety požadavků a odpovědí se přesouvají mezi klientským a back-endovým fondem. Poskytuje také možnost přidat podmínky, které zajistí, že zadané hlavičky budou přepsány pouze v případě, že jsou splněny určité podmínky.

Další informace naleznete [v tématu Přepis záhlaví protokolu HTTP](rewrite-http-headers.md).

## <a name="sizing"></a>Velikosti

Standard_v2 aplikační brány lze nakonfigurovat pro automatické škálování nebo nasazení s pevnou velikostí. Tato skladová položka nenabízí různé velikosti instancí. Další informace o výkonu v2 a ceny, naleznete [v tématu automatické škálování v2 Skladové položky](application-gateway-autoscaling-zone-redundant.md#pricing).

Standard aplikační brány je nabízen ve třech velikostech: **Malé**, **Střední**a **Velké**. Instance krátkodobého používání jsou určené pro scénáře vývoje a testování.

Úplný seznam omezení služby Application Gateway najdete na stránce [Omezení služby Application Gateway](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fapplication-gateway%2ftoc.json#application-gateway-limits).

V následující tabulce je uvedena průměrná propustnost výkonu pro každou instanci aplikační brány v1 s povoleným snížením zátěže SSL:

| Průměrná velikost odpovědi na stránku back-end | Krátkodobé používání | Střednědobé používání | Dlouhodobé používání |
| --- | --- | --- | --- |
| 6 KB |7,5 Mb/s |13 Mb/s |50 Mb/s |
| 100 KB |35 Mb/s |100 Mb/s |200 Mb/s |

> [!NOTE]
> Tyto hodnoty jsou přibližné hodnoty propustnosti služby Application Gateway. Skutečná propustnost závisí na různých podrobnostech o prostředí, jako jsou například průměrná velikost stránky, umístění back-endových instancí a doba zpracování potřebná k doručení stránky. Přesné údaje o výkonu získáte, když spustíte vlastní testy. Tyto hodnoty slouží jenom jako vodítko při plánování kapacity.

## <a name="next-steps"></a>Další kroky

- Zjistěte, jak funguje aplikační brána – [Jak funguje aplikační brána](how-application-gateway-works.md)