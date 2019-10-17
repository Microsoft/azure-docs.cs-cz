---
title: Přehled konfigurace Azure Application Gateway
description: Tento článek popisuje, jak nakonfigurovat komponenty služby Azure Application Gateway
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 6/1/2019
ms.author: absha
ms.openlocfilehash: d67a14b1cbd3fb352ee1c4b271945ab347ee7fed
ms.sourcegitcommit: bb65043d5e49b8af94bba0e96c36796987f5a2be
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/16/2019
ms.locfileid: "72389976"
---
# <a name="application-gateway-configuration-overview"></a>Přehled konfigurace Application Gateway

Azure Application Gateway se skládá z několika komponent, které můžete nakonfigurovat různými způsoby pro různé scénáře. V tomto článku se dozvíte, jak nakonfigurovat jednotlivé komponenty.

![Graf Flow komponent Application Gateway](./media/configuration-overview/configuration-overview1.png)

Tento obrázek znázorňuje aplikaci, která má tři naslouchací procesy. První dva jsou naslouchací procesy více lokalit pro `http://acme.com/*` a `http://fabrikam.com/*` v uvedeném pořadí. Naslouchat na portu 80. Třetí je základní naslouchací proces, který má ukončení na konci SSL (Secure Sockets Layer) (SSL).


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Předpoklady

### <a name="azure-virtual-network-and-dedicated-subnet"></a>Virtuální síť Azure a vyhrazená podsíť

Application Gateway je ve vaší virtuální síti vyhrazené nasazení. V rámci virtuální sítě se pro aplikační bránu vyžaduje vyhrazená podsíť. V podsíti můžete mít několik instancí daného nasazení aplikační brány. V podsíti můžete taky nasadit jiné aplikační brány. V podsíti aplikační brány ale nemůžete nasadit žádné další prostředky.

> [!NOTE]
> Nemůžete kombinovat Standard_v2 a standardní Application Gateway Azure ve stejné podsíti.

#### <a name="size-of-the-subnet"></a>Velikost podsítě

Application Gateway spotřebovává 1 soukromou IP adresu na instanci a další privátní IP adresu, pokud je nakonfigurovaná privátní front-end IP adresa.

Azure také rezervuje 5 IP adres v každé podsíti pro interní použití: první 4 a poslední IP adresy. Zvažte například 15 instancí aplikační brány bez soukromé front-endové IP adresy. Pro tuto podsíť potřebujete aspoň 20 IP adres: 5 pro interní použití a 15 pro instance služby Application Gateway. Proto potřebujete velikost podsítě/27 nebo větší.

Vezměte v úvahu podsíť, která má 27 instancí aplikační brány a IP adresu pro soukromou front-end IP adresu. V takovém případě potřebujete 33 IP adres: 27 pro instance aplikačních bran, 1 pro privátní front-end a 5 pro interní použití. Proto potřebujete velikost podsítě/26 nebo větší.

Doporučujeme použít velikost podsítě alespoň/28. Tato velikost poskytuje 11 použitelných IP adres. Pokud zatížení vaší aplikace vyžaduje více než 10 IP adres, vezměte v úvahu velikost podsítě/27 nebo/26.

#### <a name="network-security-groups-on-the-application-gateway-subnet"></a>Skupiny zabezpečení sítě na Application Gateway podsíti

V Application Gateway jsou podporovány skupiny zabezpečení sítě (skupin zabezpečení sítě). Existuje však několik omezení:

- Je *nutné, aby*příchozí internetový provoz na portech TCP 65503-65534 pro SKU Application Gateway V1 a porty TCP 65200-65535 pro SKU verze v2 s cílovou podsítí. Tento rozsah portů je nutný pro komunikaci s infrastrukturou Azure. Tyto porty jsou chráněné (uzamčené) pomocí certifikátů Azure. Externí entity, včetně zákazníků těchto bran, nemůžou zahájit změny těchto koncových bodů bez příslušných certifikátů.

- Odchozí připojení k Internetu nejde zablokovat. Výchozí odchozí pravidla v NSG umožňují připojení k Internetu. Doporučený postup:

  - Neodstraňujte Výchozí odchozí pravidla.
  - Nevytvářejte jiná odchozí pravidla, která odmítají odchozí připojení k Internetu.

- Musí být povolený provoz ze značky **AzureLoadBalancer** .

##### <a name="allow-application-gateway-access-to-a-few-source-ips"></a>Povolí Application Gateway přístup k několika zdrojovým IP adresám.

V tomto scénáři použijte skupin zabezpečení sítě v podsíti Application Gateway. V této prioritě vložte následující omezení podsítě:

1. Povolí příchozí provoz ze zdrojového rozsahu IP adres nebo IP adres do celé podsítě Application Gateway nebo konkrétní nakonfigurovanou soukromou IP adresu front-endu. NSG nefunguje na veřejné IP adrese.
2. Povolte příchozí požadavky ze všech zdrojů na porty 65503-65534 pro SKU Application Gateway V1 a porty 65200-65535 pro SKLADOVOU položku v2 pro [bezstavovou komunikaci v případě back-endu](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics). Tento rozsah portů je nutný pro komunikaci s infrastrukturou Azure. Tyto porty jsou chráněné (uzamčené) pomocí certifikátů Azure. Bez příslušných certifikátů nemohou externí entity zahájit změny těchto koncových bodů.
3. Povolit příchozí testy Azure Load Balancer (značka*AzureLoadBalancer* ) a příchozí provoz virtuální sítě (značka*VirtualNetwork* ) ve [skupině zabezpečení sítě](https://docs.microsoft.com/azure/virtual-network/security-overview).
4. Zablokuje všechny ostatní příchozí přenosy pomocí pravidla odepřít – vše.
5. Povolí odchozí provoz do Internetu pro všechny cíle.

#### <a name="user-defined-routes-supported-on-the-application-gateway-subnet"></a>Trasy definované uživatelem podporované v Application Gateway podsíti

V případě SKU v1 jsou uživatelsky definované trasy (udr) podporované v Application Gateway podsíti, pokud se nezmění na koncovou komunikaci mezi požadavky a odpověďmi. V Application Gateway podsíti můžete například nastavit UDR, aby odkazoval na zařízení brány firewall pro kontrolu paketů. Je ale nutné se ujistit, že paket se po kontrole může dostat k zamýšlenému cíli. V takovém případě může dojít k nesprávnému chování při testování stavu nebo směrování provozu. To zahrnuje naučené trasy nebo výchozí trasy 0.0.0.0/0, které šíří služby Azure ExpressRoute nebo VPN Gateway ve virtuální síti.

V případě SKU verze V2 nejsou udr podporovány v podsíti Application Gateway. Další informace najdete v tématu [SKU Azure Application Gateway v2](application-gateway-autoscaling-zone-redundant.md#differences-with-v1-sku).

> [!NOTE]
> Udr se pro SKU v2 nepodporují.  Pokud požadujete udr, měli byste nadále nasazovat SKU v1.

> [!NOTE]
> Použití udr v podsíti Application Gateway způsobí, že se stav v [zobrazení stavu back-endu](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics#back-end-health) zobrazuje jako "Neznámý". Také způsobí, že generování protokolů Application Gateway a metriky selže. Doporučujeme, abyste v Application Gateway podsíti nepoužívali udr, abyste mohli zobrazit stav back-endu, protokoly a metriky.

## <a name="front-end-ip"></a>Front-end IP adresa

Aplikační bránu můžete nakonfigurovat tak, aby měla veřejnou IP adresu, soukromou IP adresu nebo obojí. Veřejná IP adresa se vyžaduje při hostování back-endu, ke kterému musí klienti přistupovat přes Internet prostřednictvím internetové virtuální IP adresy (VIP). 

Veřejná IP adresa není nutná pro interní koncový bod, který není vystavený pro Internet. Označuje se jako koncový bod *interního nástroje pro vyrovnávání zatížení* (interního nástroje). Služba Application Gateway interního nástroje je užitečná pro interní obchodní aplikace, které nejsou přístupné pro Internet. Je to také užitečné pro služby a vrstvy v rámci hranice zabezpečení, které nejsou přístupné z Internetu, ale vyžadují distribuci zatížení kruhového dotazování, vytrvalost relace nebo ukončení protokolu SSL.

Podporovaná je jenom jedna veřejná IP adresa nebo 1 privátní IP adresa. Při vytváření aplikační brány zvolíte front-end IP adresu.

- U veřejné IP adresy můžete vytvořit novou veřejnou IP adresu nebo použít stávající veřejnou IP adresu ve stejném umístění jako Aplikační brána. Pokud vytvoříte novou veřejnou IP adresu, vybraný typ IP adresy (statický nebo dynamický) se nedá později změnit. Další informace najdete v tématu [statická a dynamická veřejná IP adresa](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#static-versus-dynamic-public-ip-address).

- U privátních IP adres můžete zadat privátní IP adresu z podsítě, ve které je Aplikační brána vytvořená. Pokud nezadáte žádnou hodnotu, z podsítě se automaticky vybere Libovolná IP adresa. Další informace najdete v tématu [Vytvoření aplikační brány s interním nástrojem pro vyrovnávání zatížení](https://docs.microsoft.com/azure/application-gateway/application-gateway-ilb-arm).

Front-end IP adresa je přidružená k *naslouchacího procesu*, který kontroluje příchozí požadavky na front-endové IP adresu.

## <a name="listeners"></a>Naslouchací procesy

Naslouchací proces je logická entita, která kontroluje příchozí požadavky na připojení pomocí portu, protokolu, hostitele a IP adresy. Při konfiguraci naslouchacího procesu je nutné zadat hodnoty, které odpovídají odpovídajícím hodnotám v příchozím požadavku na bráně.

Když vytváříte Aplikační bránu pomocí Azure Portal, vytvoříte také výchozí naslouchací proces zvolením protokolu a portu pro naslouchací proces. Můžete zvolit, jestli se má povolit podpora HTTP2 pro naslouchací proces. Po vytvoření služby Application Gateway můžete upravit nastavení tohoto výchozího naslouchacího procesu (*appGatewayHttpListener*/*appGatewayHttpsListener*) nebo vytvořit nové naslouchací procesy.

### <a name="listener-type"></a>Typ naslouchacího procesu

Při vytváření nového naslouchacího procesu si zvolíte mezi [ *základními* a *více lokalitami*](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#types-of-listeners).

- Pokud používáte jednu lokalitu za aplikační bránou, vyberte základní. Přečtěte si, [jak vytvořit Aplikační bránu s využitím základního naslouchacího procesu](https://docs.microsoft.com/azure/application-gateway/quick-create-portal).

- Pokud konfigurujete více než jednu webovou aplikaci nebo více subdomén stejné nadřazené domény ve stejné instanci služby Application Gateway, vyberte možnost naslouchací proces více webů. V případě naslouchacího procesu s více lokalitami je nutné zadat také název hostitele. Důvodem je to, že Application Gateway spoléhá na hlavičky hostitele HTTP 1,1 k hostování více než jednoho webu na stejné veřejné IP adrese a portu.

#### <a name="order-of-processing-listeners"></a>Pořadí naslouchacího procesu zpracování

Pro SKU V1 se naslouchací procesy zpracovávají v pořadí, v jakém jsou uvedeny. Pokud se základní naslouchací proces shoduje se vstupním požadavkem, naslouchací proces tyto požadavky nejprve zpracuje. Proto nakonfigurujte naslouchací procesy více lokalit před základními naslouchacími procesy, aby se zajistilo směrování provozu do správného back-endu.

V případě SKU verze v2 jsou procesy naslouchání na více lokalitách zpracovány před základními naslouchacími procesy.

### <a name="front-end-ip"></a>Front-end IP adresa

Vyberte front-end IP adresu, kterou plánujete přidružit k tomuto naslouchacímu procesu. Naslouchací proces bude naslouchat příchozím žádostem v této IP adrese.

### <a name="front-end-port"></a>Front-end port

Vyberte front-end port. Vyberte existující port nebo vytvořte nový. Vyberte libovolnou hodnotu z [povoleného rozsahu portů](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#ports). Můžete použít nejen známé porty, například 80 a 443, ale kterýkoli povolený vlastní port je vhodný. Port lze použít pro veřejné naslouchací procesy nebo privátní naslouchací procesy.

### <a name="protocol"></a>Protocol (Protokol)

Vyberte HTTP nebo HTTPS:

- Pokud zvolíte protokol HTTP, přenosy mezi klientem a aplikační bránou nejsou šifrované.

- Pokud chcete [ukončení protokolu SSL](https://docs.microsoft.com/azure/application-gateway/overview#secure-sockets-layer-ssltls-termination) nebo [komplexní šifrování SSL](https://docs.microsoft.com/azure/application-gateway/ssl-overview), vyberte https. Přenos dat mezi klientem a aplikační bránou je zašifrovaný. A připojení SSL se ukončí na aplikační bráně. Pokud chcete komplexní šifrování SSL, musíte zvolit HTTPS a nakonfigurovat nastavení **back-endu http** . Tím se zajistí, že se provoz po přenosu z aplikační brány do back-endu znovu zašifruje.

Chcete-li nakonfigurovat ukončení protokolu SSL a šifrování koncovým protokolem SSL, je nutné do naslouchacího procesu přidat certifikát, aby služba Application Gateway mohla odvodit symetrický klíč. To je určeno specifikací protokolu SSL. Symetrický klíč slouží k šifrování a dešifrování provozu, který je odeslán do brány. Certifikát brány musí být ve formátu PFX (Personal Information Exchange). Tento formát vám umožní exportovat soukromý klíč, který brána používá k šifrování a dešifrování provozu.

#### <a name="supported-certificates"></a>Podporované certifikáty

Viz [certifikáty podporované pro ukončení protokolu SSL](https://docs.microsoft.com/azure/application-gateway/ssl-overview#certificates-supported-for-ssl-termination).

### <a name="additional-protocol-support"></a>Podpora dalších protokolů

#### <a name="http2-support"></a>Podpora HTTP2

Podpora protokolu HTTP/2 je dostupná pro klienty, kteří se připojují pouze ke službě Application Gateway Listeners. Komunikace se fondy back-end serverů je přes protokol HTTP/1.1. Ve výchozím nastavení je podpora HTTP/2 zakázaná. Následující fragment kódu Azure PowerShell ukazuje, jak tuto možnost povolit:

```azurepowershell
$gw = Get-AzApplicationGateway -Name test -ResourceGroupName hm

$gw.EnableHttp2 = $true

Set-AzApplicationGateway -ApplicationGateway $gw
```

#### <a name="websocket-support"></a>Podpora protokolu WebSocket

Podpora protokolu WebSocket je ve výchozím nastavení povolená. Neexistuje žádné uživatelsky konfigurovatelné nastavení, které by bylo možné povolit nebo zakázat. Můžete použít objekty WebSockets s naslouchacími procesy HTTP i HTTPS.

### <a name="custom-error-pages"></a>Stránky vlastních chyb

Vlastní chybu můžete definovat na globální úrovni nebo na úrovni naslouchacího procesu. Ale vytváření vlastních chybových stránek na globální úrovni z Azure Portal aktuálně není podporováno. Vlastní chybovou stránku můžete nakonfigurovat pro chybu brány firewall webové aplikace 403 nebo pro stránku údržby 502 na úrovni naslouchacího procesu. Pro daný stavový kód chyby je nutné zadat také veřejně dostupnou adresu URL objektu BLOB. Další informace najdete v tématu [Vytvoření vlastních chybových stránek služby Application Gateway](https://docs.microsoft.com/azure/application-gateway/custom-error).

![Kódy chyb Application Gateway](https://docs.microsoft.com/azure/application-gateway/media/custom-error/ag-error-codes.png)

Pokud chcete nakonfigurovat globální vlastní chybovou stránku, přečtěte si téma [Azure PowerShell Configuration](https://docs.microsoft.com/azure/application-gateway/custom-error#azure-powershell-configuration).

### <a name="ssl-policy"></a>Zásady SSL

Můžete centralizovat správu certifikátů SSL a snížit režijní náklady na dešifrování u back-endové serverové farmy. Centralizované zpracování protokolu SSL také umožňuje určit centrální zásady protokolu SSL, které jsou vhodné pro vaše požadavky na zabezpečení. Můžete zvolit *výchozí*, *předdefinované*nebo *vlastní* zásady protokolu SSL.

Nakonfigurujete zásady protokolu SSL pro řízení verzí protokolu SSL. Můžete nakonfigurovat aplikační bránu, aby odmítala TLS 1.0, TLS 1.1 a TLS 1.2. Ve výchozím nastavení jsou SSL 2,0 a 3,0 zakázané a nedají se konfigurovat. Další informace najdete v tématu [Přehled zásad Application Gateway protokolu SSL](https://docs.microsoft.com/azure/application-gateway/application-gateway-ssl-policy-overview).

Po vytvoření naslouchacího procesu ho přidružíte k pravidlu směrování požadavků. Toto pravidlo určuje, jak jsou požadavky přijaté na naslouchací službě směrovány do back-endu.

## <a name="request-routing-rules"></a>Pravidla směrování žádostí

Když vytváříte Aplikační bránu pomocí Azure Portal, vytvoříte výchozí pravidlo (*rule1*). Toto pravidlo váže výchozí naslouchací proces (*appGatewayHttpListener*) s výchozím fondem back-end (*appGatewayBackendPool*) a výchozím nastavením back-endu http (*appGatewayBackendHttpSettings*). Po vytvoření brány můžete upravit nastavení výchozího pravidla nebo vytvořit nová pravidla.

### <a name="rule-type"></a>Typ pravidla

Když vytvoříte pravidlo, zvolíte mezi [ *základními* a *založenými na cestách*](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#request-routing-rules).

- Pokud chcete před všemi požadavky na přiřazený naslouchací proces (například *blog<i></i>. contoso.com/\*)* na jeden fond back-end, vyberte možnost Basic.
- Pokud chcete směrovat požadavky od konkrétních cest URL ke konkrétním fondům back-endu, vyberte na základě cesty. Vzor cesty je použit pouze pro cestu k adrese URL, nikoli k parametrům dotazu.

#### <a name="order-of-processing-rules"></a>Pořadí pravidel zpracování

V případě SKU V1 se porovnávání vzorů příchozích požadavků zpracovává v pořadí, v jakém jsou cesty uvedeny v mapě cesty URL pravidla založeného na cestě. Pokud požadavek odpovídá vzoru ve dvou nebo více cestách v mapě cesty, cesta uvedená jako první je shodná. A požadavek se přepošle na back-end, který je přidružený k této cestě.

V případě SKU v2 je přesnou shodou v mapě cesty URL přesná shoda vyšší Priorita než pořadí cest. Pokud požadavek odpovídá vzoru ve dvou nebo více cestách, požadavek se předá do back-endu přidruženého k cestě, která přesně odpovídá požadavku. Pokud cesta v příchozím požadavku neodpovídá přesně žádné cestě v mapě, zpracovává se porovnávání vzorů žádosti v seznamu pořadí mapy cest pro pravidlo na základě cesty.

### <a name="associated-listener"></a>Přidružený naslouchací proces

Přidružte naslouchací proces k pravidlu, aby se vyhodnotilo *pravidlo směrování požadavků* , které je přidružené ke naslouchacího procesu, aby se zjistilo, že fond back-end má směrovat požadavek.

### <a name="associated-back-end-pool"></a>Přidružený fond back-end

Přidružte k pravidlu fond back-end, který obsahuje cíle back-endu, které obsluhují požadavky, které naslouchací proces obdrží.

 - Pro základní pravidlo je povolen pouze jeden fond back-end. Všechny požadavky na přidruženém naslouchacího procesu se předají do tohoto fondu back-end.

 - V případě pravidla založeného na cestách přidejte více fondů back-end, které odpovídají každé cestě URL. Požadavky, které odpovídají zadané cestě URL, se předají do odpovídajícího fondu back-end. Přidejte také výchozí fond back-end. Žádosti, které neodpovídají žádné cestě URL v pravidle, se předají do tohoto fondu.

### <a name="associated-back-end-http-setting"></a>Přidružené nastavení HTTP back-endu

Přidejte nastavení back-endu protokolu HTTP pro každé pravidlo. Požadavky jsou směrovány z aplikační brány na cíle back-endu pomocí čísla portu, protokolu a dalších informací, které jsou zadány v tomto nastavení.

Pro základní pravidlo je povolené jenom jedno nastavení back-endu HTTP. Všechny požadavky na přidruženém naslouchacího procesu jsou předávány odpovídajícím cílům back-endu pomocí tohoto nastavení protokolu HTTP.

Přidejte nastavení back-endu protokolu HTTP pro každé pravidlo. Požadavky jsou směrovány z aplikační brány na cíle back-endu pomocí čísla portu, protokolu a dalších informací, které jsou zadány v tomto nastavení.

Pro základní pravidlo je povolené jenom jedno nastavení back-endu HTTP. Všechny žádosti u přidruženého naslouchacího procesu se předají do odpovídajících cílů back-endu pomocí tohoto nastavení protokolu HTTP.

Pro pravidlo založené na cestách přidejte více nastavení HTTP back-endu, která odpovídají každé cestě URL. Požadavky, které odpovídají cestě URL v tomto nastavení, se předají do odpovídajících cílů back-endu pomocí nastavení protokolu HTTP, které odpovídá každé cestě URL. Přidejte také výchozí nastavení HTTP. Požadavky, které neodpovídají žádné cestě URL v tomto pravidle, se předají do výchozího fondu back-end s použitím výchozího nastavení HTTP.

### <a name="redirection-setting"></a>Nastavení přesměrování

Pokud je přesměrování nakonfigurováno pro základní pravidlo, všechny požadavky na přidruženém naslouchací službě budou přesměrovány do cíle. Toto je *globální* přesměrování. Pokud je přesměrování nakonfigurováno pro pravidlo na základě cesty, budou přesměrovány pouze požadavky v určité oblasti lokality. Příkladem je oblast nákupního košíku, která je označená */cart/\** . Toto je přesměrování *na základě cest* .

Další informace o přesměrování najdete v tématu [Přehled přesměrování Application Gateway](https://docs.microsoft.com/azure/application-gateway/redirect-overview).

#### <a name="redirection-type"></a>Typ přesměrování

Vyberte typ požadovaného přesměrování: *trvalá (301)* , *dočasná (307)* , *Nalezeno (302*) nebo *jiný (303)* .

#### <a name="redirection-target"></a>Cíl přesměrování

Jako cíl přesměrování vyberte jiný naslouchací proces nebo externí Web.

##### <a name="listener"></a>Služby

Jako cíl přesměrování vyberte naslouchací proces pro přesměrování provozu z jednoho naslouchacího procesu do jiného v bráně. Toto nastavení se vyžaduje, když chcete povolit přesměrování od protokolu HTTP do HTTPS. Přesměruje provoz od naslouchacího procesu zdroje, který kontroluje příchozí požadavky HTTP na cílový naslouchací proces, který kontroluje příchozí požadavky HTTPS. Můžete také zvolit, že se má v požadavku, který předává cíli přesměrování, zahrnout řetězec dotazu a cestu z původního požadavku.

![Dialogové okno Application Gateway součásti](./media/configuration-overview/configure-redirection.png)

Další informace o přesměrování mezi HTTP a HTTPS najdete v těchto tématech:
- [Přesměrování HTTP na HTTPS pomocí Azure Portal](https://docs.microsoft.com/azure/application-gateway/redirect-http-to-https-portal)
- [Přesměrování HTTP na HTTPS pomocí PowerShellu](https://docs.microsoft.com/azure/application-gateway/redirect-http-to-https-powershell)
- [Přesměrování HTTP na HTTPS pomocí rozhraní příkazového řádku Azure](https://docs.microsoft.com/azure/application-gateway/redirect-http-to-https-cli)

##### <a name="external-site"></a>Externí web

Vyberte externí web, když chcete přesměrovat provoz na naslouchací proces, který je přidružený k tomuto pravidlu, na externí Web. Můžete zvolit zahrnutí řetězce dotazu z původní žádosti do žádosti, která je předána cíli přesměrování. Cestu k externímu webu, který byl v původní žádosti, nelze přeslat.

Další informace o přesměrování najdete v tématu:
- [Přesměrování provozu na externí web pomocí prostředí PowerShell](https://docs.microsoft.com/azure/application-gateway/redirect-external-site-powershell)
- [Přesměrování provozu na externí web pomocí rozhraní příkazového řádku](https://docs.microsoft.com/azure/application-gateway/redirect-external-site-cli)

#### <a name="rewrite-the-http-header-setting"></a>Přepsání nastavení záhlaví HTTP

Toto nastavení přidá, odebere nebo aktualizuje hlavičku požadavku a odpovědi HTTP, zatímco pakety požadavků a odpovědí přecházejí mezi klienty klienta a back-endové fondy. Tuto možnost můžete nakonfigurovat jenom přes PowerShell. Azure Portal a podpora rozhraní příkazového řádku ještě nejsou k dispozici. Další informace:

 - [Přehled hlaviček protokolu HTTP přepisu](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers)
 - [Konfigurace přepsání hlaviček HTTP](https://docs.microsoft.com/azure/application-gateway/add-http-header-rewrite-rule-powershell#specify-the-http-header-rewrite-rule-configuration)

## <a name="http-settings"></a>Nastavení HTTP

Služba Application Gateway směruje provoz na back-endové servery pomocí konfigurace, kterou tady zadáte. Po vytvoření nastavení HTTP je potřeba ho přidružit k jednomu nebo více pravidlům směrování požadavků.

### <a name="cookie-based-affinity"></a>Spřažení na základě souborů cookie.

Tato funkce je užitečná, když chcete zachovat relaci uživatele na stejném serveru. Soubory cookie spravované branou umožňují službě Application Gateway přímý přenos dat z uživatelské relace na stejný server ke zpracování. To je důležité, pokud je stav relace uložen místně na serveru pro relaci uživatele. Pokud aplikace nemůže zpracovat spřažení na základě souborů cookie, nemůžete tuto funkci použít. Pokud ho chcete použít, ujistěte se, že klienti podporují soubory cookie.

### <a name="connection-draining"></a>Vyprázdnění připojení

Vyprazdňování připojení pomáhá řádně odebrat členy fondu back-end během plánovaných aktualizací služby. Toto nastavení můžete použít pro všechny členy fondu back-end během vytváření pravidla. Zajišťuje, aby všechny instance zrušení registrace fondu back-end nepřijímaly žádné nové žádosti. Mezitím se můžou existující požadavky dokončit v nakonfigurovaném časovém limitu. Vyprazdňování připojení se vztahuje na instance back-endu, které jsou explicitně odebrány z back-endového fondu pomocí volání rozhraní API. Platí také pro back-endové instance, které jsou hlášeny jako *špatné* v důsledku sond stavu.

### <a name="protocol"></a>Protocol (Protokol)

Application Gateway podporuje HTTP i HTTPS pro požadavky směrování na back-endové servery. Pokud zvolíte protokol HTTP, přenosy na back-endové servery budou nešifrované. Pokud nešifrovaná komunikace není přijatelná, vyberte HTTPS.

Toto nastavení kombinované s protokolem HTTPS v naslouchací službě podporuje [koncové šifrování protokolu SSL](https://docs.microsoft.com/azure/application-gateway/ssl-overview). Díky tomu můžete bezpečně přenášet citlivá data zašifrovaná do back-endu. Každý back-end Server ve fondu back-end, který má zapnuté koncové šifrování SSL, musí být nakonfigurovaný s certifikátem, aby bylo možné zabezpečenou komunikaci.

### <a name="port"></a>Port

Toto nastavení určuje port, ve kterém back-endové servery naslouchají provozu z aplikační brány. Můžete nakonfigurovat porty v rozsahu od 1 do 65535.

### <a name="request-timeout"></a>Časový limit žádosti

Toto nastavení určuje dobu v sekundách, po kterou brána Application Gateway počká, než obdrží odpověď z fondu back-end, a potom vrátí chybovou zprávu "vypršel časový limit připojení".

### <a name="override-back-end-path"></a>Přepsat back-end cestu

Toto nastavení umožňuje nakonfigurovat volitelnou vlastní cestu přesměrování, která se má použít, když se požadavek přesměruje do back-endu. Všechny části příchozí cesty, které se shodují s vlastní cestou v poli **přepište cestu k back-endu** , se zkopírují do přesměrované cesty. Následující tabulka ukazuje, jak tato funkce funguje:

- Když je nastavení HTTP připojené k základnímu pravidlu pro směrování požadavků:

  | Původní požadavek  | Přepsat back-end cestu | Požadavek předaný do back-endu |
  | ----------------- | --------------------- | ---------------------------- |
  | domovské            | /override musí obsahovat            | /override/home/              |
  | /home/secondhome/ | /override musí obsahovat            | /override/home/secondhome/   |

- Pokud je nastavení HTTP připojeno k pravidlu směrování požadavků založené na cestách:

  | Původní požadavek           | Pravidlo cesty       | Přepsat back-end cestu | Požadavek předaný do back-endu |
  | -------------------------- | --------------- | --------------------- | ---------------------------- |
  | /pathrule/home/            | pathrule      | /override musí obsahovat            | /override/home/              |
  | /pathrule/home/secondhome/ | pathrule      | /override musí obsahovat            | /override/home/secondhome/   |
  | domovské                     | pathrule      | /override musí obsahovat            | /override/home/              |
  | /home/secondhome/          | pathrule      | /override musí obsahovat            | /override/home/secondhome/   |
  | /pathrule/home/            | /pathrule/home* | /override musí obsahovat            | /override musí obsahovat                   |
  | /pathrule/home/secondhome/ | /pathrule/home* | /override musí obsahovat            | /override/secondhome/        |
  | pathrule                 | pathrule      | /override musí obsahovat            | /override musí obsahovat                   |

### <a name="use-for-app-service"></a>Použít pro App Service

Toto je zástupce uživatelského rozhraní, který vybere dvě požadovaná nastavení pro back-end Azure App Service. Umožňuje **Vybrat název hostitele z back-endové adresy**a vytvoří nový vlastní test paměti. (Další informace naleznete v části [výběr názvu hostitele z nastavení back-endové adresy](#pick) v tomto článku.) Vytvoří se nový test paměti a hlavička testu se vybere z adresy člena, který je back-end.

### <a name="use-custom-probe"></a>Použít vlastní test paměti

Toto nastavení přidruží [vlastní test](https://docs.microsoft.com/azure/application-gateway/application-gateway-probe-overview#custom-health-probe) s nastavením http. K nastavení HTTP můžete přidružit jenom jeden vlastní test paměti. Pokud nechcete explicitně přidružit vlastní test, použije se k monitorování stavu back-endu [výchozí test](https://docs.microsoft.com/azure/application-gateway/application-gateway-probe-overview#default-health-probe-settings) . Doporučujeme vytvořit vlastní test pro lepší kontrolu nad monitorováním stavu back-endu.

> [!NOTE]
> Vlastní test nemonitoruje stav fondu back-end, pokud není odpovídající nastavení HTTP explicitně přidruženo k naslouchacímu procesu.

### <a id="pick"/> @ no__t-1Pick název hostitele z back-endové adresy

Tato funkce dynamicky nastaví hlavičku *hostitele* v požadavku na název hostitele back-end fondu. Používá IP adresu nebo plně kvalifikovaný název domény.

Tato funkce pomáhá v případě, že se název domény back-endu liší od názvu DNS aplikační brány a back-end spoléhá na konkrétní hlavičku hostitele nebo Indikace názvu serveru (SNI), která se má vyřešit na správný koncový bod.

Příkladem případu jsou služby pro více tenantů jako back-end. App Service je víceklientské služby, které používají sdílený prostor s jednou IP adresou. Služba App Service je proto dostupná jenom prostřednictvím názvů hostitelů, které jsou nakonfigurované v nastavení vlastní domény.

Ve výchozím nastavení je název vlastní domény *example. azurewebsites.<i> </i> síť*. Pokud chcete ke službě App Service přistupovat pomocí služby Application Gateway prostřednictvím názvu hostitele, který není explicitně zaregistrovaný ve službě App Service nebo prostřednictvím plně kvalifikovaného názvu domény služby Application Gateway, přepište název hostitele v původní žádosti na název hostitele App Service. Provedete to tak, že povolíte nastavení **Vybrat název hostitele z back-endu adresy** .

U vlastní domény, jejíž existující vlastní název DNS je namapovaný na službu App Service, nemusíte toto nastavení Povolit.

> [!NOTE]
> Toto nastavení není vyžadováno pro App Service Environment pro PowerApps, což je vyhrazené nasazení.

### <a name="host-name-override"></a>Přepsání názvu hostitele

Tato funkce nahrazuje hlavičku *hostitele* v příchozím požadavku na aplikační bráně zadaným názvem hostitele.

Pokud je například v nastavení **název hostitele** zadána možnost *www. contoso<i></i>. com* , původní požadavek *https:/<i></i>/appgw.eastus.cloudapp.NET/path1* se změní na *https:/<i></i>/www.contoso.com/path1* , pokud požadavek se přepošle na back-end Server.

## <a name="back-end-pool"></a>Back-endový fond

Back-end fond můžete nasměrovat na čtyři typy členů back-endu: konkrétní virtuální počítač, sada škálování virtuálního počítače, IP adresa nebo plně kvalifikovaný název domény nebo služba App Service. Každý fond back-end může ukazovat na více členů stejného typu. Přechod na členy různých typů ve stejném fondu back-end není podporován.

Až vytvoříte fond back-end, musíte ho přidružit k jednomu nebo více pravidlům směrování požadavků. Pro každý fond back-end v bráně Application Gateway musíte taky nakonfigurovat testy stavu. Pokud je splněna podmínka pravidla směrování požadavků, služba Application Gateway přesměruje provoz na servery, které jsou v pořádku (podle určení sond stavu), do odpovídajícího fondu back-end.

## <a name="health-probes"></a>Sondy stavu

Služba Application Gateway ve výchozím nastavení monitoruje stav všech prostředků ve svém back-endu. Důrazně doporučujeme vytvořit vlastní test pro každé nastavení back-endu HTTP a získat tak větší kontrolu nad monitorováním stavu. Informace o tom, jak nakonfigurovat vlastní test paměti, najdete v tématu [vlastní nastavení sondy stavu](https://docs.microsoft.com/azure/application-gateway/application-gateway-probe-overview#custom-health-probe-settings).

> [!NOTE]
> Po vytvoření vlastního sondy stavu je potřeba ho přidružit k nastavení back-endu HTTP. Vlastní test nemonitoruje stav fondu back-end, pokud není odpovídající nastavení HTTP explicitně přidruženo k naslouchacího procesu.

## <a name="next-steps"></a>Další kroky

Teď, když víte o Application Gateway komponent, můžete:

- [Vytvoření aplikační brány v Azure Portal](quick-create-portal.md)
- [Vytvoření aplikační brány pomocí PowerShellu](quick-create-powershell.md)
- [Vytvoření aplikační brány pomocí Azure CLI](quick-create-cli.md)
