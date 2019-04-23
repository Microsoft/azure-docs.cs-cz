---
title: Přehled konfigurace služby Azure Application Gateway
description: Tento článek popisuje postup konfigurace komponenty Azure Application Gateway
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 03/20/2019
ms.author: absha
ms.openlocfilehash: 4b8e04babfffaf49d3719d8a7e90af16598814f4
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2019
ms.locfileid: "59998902"
---
# <a name="application-gateway-configuration-overview"></a>Přehled konfigurace služby Application Gateway

Azure Application Gateway se skládá z několika komponent, které můžete nakonfigurovat různými způsoby pro různé scénáře. Tento článek ukazuje postup při konfiguraci jednotlivých komponent.

![Application Gateway součásti vývojový diagram](./media/configuration-overview/configuration-overview1.png)

Tento obrázek znázorňuje aplikaci, která má tři naslouchacích procesů. První dvě jsou naslouchacích procesů více lokalit pro `http://acme.com/*` a `http://fabrikam.com/*`v uvedeném pořadí. Obě naslouchání na portu 80. Třetí je základní naslouchací proces, který má ukončení začátku do konce vrstvy SSL (Secure Sockets).


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Požadavky

### <a name="azure-virtual-network-and-dedicated-subnet"></a>Virtuální síť Azure a vyhrazenou podsíť

Službu application gateway je vyhrazené nasazení ve virtuální síti. V rámci vaší virtuální sítě je požadované pro službu application gateway vyhrazenou podsíť. Více instancí dané brány nasazení aplikace může mít v podsíti. Můžete také nasadit další služby application Gateway v podsíti. Ale žádnému jinému prostředku podsítě služby application gateway nelze nasadit.

> [!NOTE]
> Standard_v2 a Standard Azure Application Gateway nelze kombinovat ve stejné podsíti.

#### <a name="size-of-the-subnet"></a>Velikost podsítě.

Application Gateway využívá 1 privátní IP adresu na jednu instanci a jiné privátní IP adresu, pokud je nakonfigurovaný privátní front-endovou IP Adresou.

Azure si zároveň vyhrazuje 5 IP adres v každé podsíti pro interní použití: 4 první a poslední IP adresy. Představte si třeba 15 instance brány aplikací se žádné privátní front-endovou IP Adresou. Budete potřebovat alespoň 20 IP adres pro tuto podsíť: 5 pro interní použití a 15 pro instance brány aplikace. Musíte proto velikost/27 nebo větší velikost podsítě.

Vezměte v úvahu podsíť, která má 27 brány instancemi aplikace a IP adresu pro privátní front-endovou IP Adresou. V takovém případě budete potřebovat 33 IP adresy: 27. pro brány instance aplikace, 1 pro privátní front-endu a 5 pro interní použití. Musíte proto /26 podsítě velké nebo větší.

Doporučujeme použít velikost podsítě minimálně/28. Tato velikost nabízí 11 použitelných IP adresách. Pokud zatížení aplikace vyžaduje víc než 10 IP adresy, zvažte možnost/27 nebo/26 velikost podsítě.

#### <a name="network-security-groups-on-the-application-gateway-subnet"></a>Skupiny zabezpečení sítě na podsítě Application Gateway.

Skupiny zabezpečení sítě (Nsg) jsou podporované ve službě Application Gateway. Existuje ale několik omezení:

- Je nutné uvést výjimky pro příchozí provoz na portech 65503 65534 pro SKU v1 Application Gateway a porty 65200 – 65 535 pro v2 SKU. Tento rozsah portů je nutné pro komunikaci infrastruktury Azure. Tyto porty jsou chráněny (uzamknuty) s použitím certifikátů Azure. Externí entity, včetně zákazníků těchto bran, nemůžete zahájit změny činnost těchto koncových bodů bez příslušné certifikáty na místě.

- Odchozí připojení k Internetu, nejde blokovat. Odchozí výchozí pravidla v této skupině povolit připojení k Internetu. Doporučujeme vám:

  - Neodebírat odchozí výchozí pravidla.
  - Nevytvářejte dalších odchozích pravidel, které odepřou odchozí připojení k Internetu.

- Provoz z **AzureLoadBalancer** musí být povoleny značky.

##### <a name="whitelist-application-gateway-access-to-a-few-source-ips"></a>Seznam povolených Application Gateway přístup k několika zdrojové IP adresy

V tomto scénáři pomocí skupin Nsg na podsítě Application Gateway. Umístěte následující omezení na podsítě v tomto pořadí:

1. Umožňuje příchozí provoz ze zdrojového rozsahu IP/IP.
2. Povolit příchozí žádosti ze všech zdrojů k portům 65503 65534 pro [stav back endu komunikace](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics). Tento rozsah portů je nutné pro komunikaci infrastruktury Azure. Tyto porty jsou chráněny (uzamknuty) s použitím certifikátů Azure. Bez příslušné certifikáty na místě nemůžete zahájit externí entity změny činnost těchto koncových bodů.
3. Povolit sondy příchozí Azure Load Balancer (*AzureLoadBalancer* značka) a příchozí provoz virtuální sítě (*VirtualNetwork* značka) na [skupinu zabezpečení sítě](https://docs.microsoft.com/azure/virtual-network/security-overview).
4. Blokovat veškerý příchozí provoz, s využitím pravidlo odepřít – to všechno.
5. Povolí odchozí provoz do Internetu pro všechny cíle.

#### <a name="user-defined-routes-supported-on-the-application-gateway-subnet"></a>Trasy definované uživatelem, které jsou podporovány na podsítě Application Gateway.

Pro v1 SKU trasy definované uživatelem (udr) se podporují na podsítě Application Gateway tak dlouho, dokud není mění komunikace začátku do konce žádostí a odpovědí. Můžete například nastavit uživatelem definovaná TRASA v podsíti brány aplikace tak, aby odkazoval na zařízení brány firewall pro kontrolu paketů. Ale ujistěte se, že jeho požadovaného cíle po kontrole dostat paket. K tomu může dojít v nesprávné sondu stavu nebo chování směrování provozu. To zahrnuje zjištěné trasy nebo výchozí trasy 0.0.0.0/0, které jsou šířeny přes Azure ExpressRoute nebo bran VPN ve virtuální síti.

Pro v2 SKU trasy definované uživatelem nejsou podporovány na podsítě Application Gateway. Další informace najdete v tématu [automatické škálování a redundanci zón pro službu Application Gateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant#known-issues-and-limitations).

> [!NOTE]
> Použití tras definovaných uživatelem na podsítě Application Gateway způsobí, že stav v [zobrazení stavu back-end](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics#back-end-health) se zobrazí "Unknown". Navíc způsobí, že generace služby Application Gateway protokoly a metriky, které selžou. Doporučujeme vám, že nepoužíváte trasy definované uživatelem na podsítě Application Gateway tak, aby se zobrazí stav back endu, protokoly a metriky.

## <a name="front-end-ip"></a>Front-endovou IP

Můžete nakonfigurovat application gateway s veřejnou IP adresu, privátní IP adresu nebo obojí. Při hostování back-end, klienti musí přistupovat přes internet prostřednictvím přístupem k Internetu virtuální IP (VIP) se vyžaduje veřejnou IP adresu. 

Veřejná IP adresa není vyžadováno pro vnitřní koncový bod, který není vystavený v Internetu. Který se označuje jako *interního nástroje load-balancer* koncový bod (ILB). Službu application gateway ILB je užitečná pro interní-obchodní aplikace, které nejsou vystaveny v Internetu. Je také užitečné služby a úrovně v vícevrstvou aplikaci v rámci hranice zabezpečení, které nejsou vystaveny v Internetu, ale kruhové dotazování, které vyžadují zatížení distribuce, dlouhodobost relace nebo ukončení protokolu SSL.

Je podporován pouze 1 veřejné IP adresy nebo 1 privátní IP adresu. Když vytvoříte službu application gateway zvolíte front-end IP adresu.

- Pro veřejnou IP adresu můžete vytvořit novou veřejnou IP adresu nebo použít stávající veřejnou IP adresu ve stejném umístění jako služba application gateway. Pokud jste vytvořili novou veřejnou IP adresu, typ IP adresy, kterou jste vybrali (statická nebo dynamická) není možné později změnit. Další informace najdete v tématu [statickou nebo dynamickou veřejnou IP adresu](https://docs.microsoft.com/en-us/azure/application-gateway/application-gateway-components#static-vs-dynamic-public-ip-address).

- Pro privátní IP adresu můžete zadat privátní IP adresu z podsítě, ve kterém se vytvoří aplikační bránu. Pokud nezadáte, je automaticky vybrán libovolnou IP adresu z podsítě. Další informace najdete v tématu [vytvoření služby application gateway pomocí interního nástroje](https://docs.microsoft.com/azure/application-gateway/application-gateway-ilb-arm).

Je přidružená k front-endovou IP adresu *naslouchací proces*, která kontroluje příchozí požadavky na front-endovou IP adresu.

## <a name="listeners"></a>Naslouchací procesy

Naslouchací proces je logická entita, která kontroluje příchozí požadavky na připojení s použitím port, protokol, hostitele a IP adresy. Při konfiguraci naslouchacího procesu, je nutné zadat hodnoty pro ty, které shodovat s odpovídajícími hodnotami v příchozím požadavku na bráně.

Při vytváření služby application gateway pomocí webu Azure portal, můžete také vytvořit naslouchací proces výchozí výběrem protokol a port naslouchacího procesu. Můžete zvolit, jestli se má povolit podporu HTTP2 na naslouchací proces. Když vytvoříte službu application gateway, můžete upravit nastavení tohoto naslouchacího procesu výchozí (*appGatewayHttpListener*/*appGatewayHttpsListener*) nebo vytvořit nový naslouchací procesy.

### <a name="listener-type"></a>Typ naslouchací proces

Pokud vytvoříte nový naslouchací proces, můžete si vybrat mezi [ *základní* a *Multi-Site*](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#types-of-listeners).

- Pokud máte jednu lokalitu za službou application gateway, zvolte základní. Přečtěte si [vytvoření služby application gateway s základní naslouchací proces](https://docs.microsoft.com/azure/application-gateway/quick-create-portal).

- Pokud konfigurujete víc než jednu webovou aplikaci nebo více poddomény stejné nadřazené domény ve stejné instanci application gateway, zvolte Multi-Site naslouchacího procesu. Naslouchací proces více lokalit zadejte název hostitele. Je to proto, že služba Application Gateway spoléhá na hlavičky hostitele HTTP 1.1 pro hostování více než jednoho webu na stejnou veřejnou IP adresu a port.

#### <a name="order-of-processing-listeners"></a>Pořadí zpracování naslouchacích procesů

Pro v1 SKU naslouchacích procesů se zpracovávají v pořadí, ve kterém jsou uvedené. Pokud základní naslouchací proces odpovídá příchozí žádosti, zpracuje naslouchací proces nejprve tento požadavek. Ano nakonfigurujte naslouchacích procesů více webů před základní naslouchací procesy, abyste měli jistotu, že provoz se směruje do správného back-endu.

Pro v2 SKU jsou zpracovány naslouchací procesy pro více webů před základní naslouchací procesy.

### <a name="front-end-ip"></a>Front-endovou IP

Vyberte front-endovou IP adresu, kterou chcete přidružit k tímto naslouchacím procesem. Naslouchací proces bude naslouchat na příchozí požadavky na tuto IP adresu.

### <a name="front-end-port"></a>Front-endový port

Vyberte front-endový port. Vyberte existující port nebo vytvořte novou. Zvolte libovolnou hodnotu od [povolený rozsah portů](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#ports). Můžete použít nejen běžně používaných portů, jako je například 80 a 443, ale povolené vlastní port, který je vhodný. Port je možné pro veřejně přístupných naslouchacích procesů nebo přístupem k privátní naslouchacích procesů.

### <a name="protocol"></a>Protocol (Protokol)

Vyberte protokol HTTP nebo HTTPS:

- Pokud vyberete možnost protokolu HTTP, Nešifrovaný přenos dat mezi klientem a application gateway.

- Vyberte protokol HTTPS, pokud chcete, aby [ukončení protokolu SSL](https://docs.microsoft.com/azure/application-gateway/overview#secure-sockets-layer-ssl-terminationl) nebo [šifrování SSL začátku do konce](https://docs.microsoft.com/azure/application-gateway/ssl-overview). Přenos dat mezi klientem a application gateway je zašifrovaná. A ukončení připojení protokolem SSL ve službě application gateway. Pokud chcete, aby šifrování SSL začátku do konce, musíte zvolit protokol HTTPS a nakonfigurovat **protokolu HTTP back-end** nastavení. Tím se zajistí, že provoz se šifruje znovu při jejich přenosu z aplikační brány pro back-endu.

Ke konfiguraci ukončení protokolu SSL a šifrování pomocí protokolu SSL začátku do konce, musíte přidat certifikát pro naslouchací proces povolení application gateway se odvodit symetrický klíč. To je dáno specifikace protokolu SSL. Symetrický klíč se používá k šifrování a dešifrování přenosů, které je odesláno brány. Certifikát brány musí být ve formátu Personal Information Exchange (PFX). Tento formát umožňuje exportovat soukromý klíč, které brána používá k šifrování a dešifrování přenosů.

#### <a name="supported-certificates"></a>Podporované certifikáty

Zobrazit [certifikátů podporovaných pro ukončení protokolu SSL](https://docs.microsoft.com/azure/application-gateway/ssl-overview#certificates-supported-for-ssl-termination).

### <a name="additional-protocol-support"></a>Podpora dalších protokolů

#### <a name="http2-support"></a>Podpora HTTP2

Podpora protokolu HTTP/2 je k dispozici pro klienty, které se připojují k application gateway pouze posluchače. Komunikace do fondů back-end serveru je přes HTTP/1.1. Ve výchozím nastavení je zakázána podpora HTTP/2. Následující fragment kódu prostředí Azure PowerShell ukazuje, jak povolit:

```azurepowershell
$gw = Get-AzApplicationGateway -Name test -ResourceGroupName hm

$gw.EnableHttp2 = $true

Set-AzApplicationGateway -ApplicationGateway $gw
```

#### <a name="websocket-support"></a>Podpora protokolu WebSocket

Ve výchozím nastavení je povolena podpora protokolu WebSocket. Neexistuje žádné uživatelem konfigurovatelné nastavení k povolení nebo zakázání ho. S naslouchacích procesů HTTP a HTTPS můžete použít objekty Websocket.

### <a name="custom-error-pages"></a>Vlastní chybové stránky

Můžete definovat vlastní chyby na globální úrovni nebo naslouchací proces. Ale vytváření globální úrovni vlastní chybové stránky na webu Azure Portal v tuto chvíli nepodporuje. Vlastní chybové stránky pro brány firewall chyby 403 webové aplikace nebo stránku 502 údržby můžete nakonfigurovat na úrovni naslouchacího procesu. Musíte zadat také adresu URL veřejně přístupná objektů blob, stavový kód dané chyby. Další informace najdete v tématu [Vytvoření vlastních chybových stránek služby Application Gateway](https://docs.microsoft.com/azure/application-gateway/custom-error).

![Kódy chyb aplikace brány](https://docs.microsoft.com/azure/application-gateway/media/custom-error/ag-error-codes.png)

Globální vlastní chybové stránky nakonfigurovat, najdete v části [konfigurace prostředí Azure PowerShell](https://docs.microsoft.com/azure/application-gateway/custom-error#azure-powershell-configuration).

### <a name="ssl-policy"></a>Zásady SSL

Můžete centralizovat správu certifikátů SSL a snížit šifrování dešifrování režie pro back endovém serveru farmy. Centralizované zpracování protokolu SSL umožňuje také určení centrální zásady protokolu SSL, který je vhodný pro vaše požadavky na zabezpečení. Můžete zvolit *výchozí*, *předdefinované*, nebo *vlastní* zásady protokolu SSL.

Můžete nakonfigurovat zásady protokolu SSL pro ovládací prvek verze protokolu SSL. Můžete nakonfigurovat službu application gateway pro protokol TLS 1.0, TLS1.1 a TLS1.2 odepření. Ve výchozím nastavení protokol SSL 2.0 a 3.0 jsou zakázané a nejsou konfigurovatelné. Další informace najdete v tématu [přehled zásad Application Gateway SSL](https://docs.microsoft.com/azure/application-gateway/application-gateway-ssl-policy-overview).

Po vytvoření naslouchacího procesu, přiřaďte ji k pravidlo směrování žádostí. Toto pravidlo určuje směrování požadavků, které jsou přijímány naslouchací proces do back-endu.

## <a name="request-routing-rules"></a>Pravidla směrování žádostí

Když vytvoříte službu application gateway pomocí webu Azure portal, vytvoření výchozího pravidla (*pravidla 1*). Toto pravidlo váže naslouchací proces výchozí (*appGatewayHttpListener*) s výchozí back endového fondu (*appGatewayBackendPool*) a výchozí nastavení HTTP back-end ( *appGatewayBackendHttpSettings*). Po vytvoření brány můžete upravit nastavení výchozího pravidla nebo vytvořit nová pravidla.

### <a name="rule-type"></a>Typ pravidla

Když vytvoříte pravidlo, můžete vybrat mezi [ *základní* a *na základě cest*](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#request-routing-rule).

- Pokud budete chtít předávat všechny žádosti na přidružený naslouchací proces, vyberte basic (například *blogu<i></i>.contoso.com/\*)* do jednoho back endového fondu.
- Zvolte cestu na základě Pokud chcete směrovat požadavky z konkrétní cesty adresy URL na konkrétní fondy back-end. Vzor cesty platí pouze pro cestu adresy URL, aby jeho parametry dotazu.

#### <a name="order-of-processing-rules"></a>Pořadí zpracování pravidla

Pro v1 SKU porovnávání vzorů příchozích požadavků, jsou zpracovávána v pořadí, cesty jsou uvedeny v mapě cestu adresy URL pravidla na základě cest. Pokud požadavek odpovídá vzoru v dvě nebo více cest v mapě cestu, cesta, která je uvedena nejprve je nalezena shoda. A předá požadavek back-endu, která je přidružená k této cestě.

Pro v2 SKU přesnou shodu znamená vyšší prioritu než pořadí cesty v objektu map cestu adresy URL. Pokud požadavek odpovídá vzoru ve dvou nebo více cest, požadavek předá do back-endu, který je spojen s cestou, který přesně odpovídá požadavku. Pokud cestu v příchozím požadavku neodpovídá přesně jakékoli cesty v objektu map, porovnávání vzorů žádosti jsou zpracovávána v pořadí cesty mapy pro pravidlo na základě cest.

### <a name="associated-listener"></a>Přidružený naslouchací proces

Přidružit naslouchací proces pro pravidlo tak, aby *pravidlo směrování žádostí* , který je přidružený naslouchací proces je vyhodnocován pro určení back endovém fondu směrovat žádosti.

### <a name="associated-back-end-pool"></a>Přidružený fond back-end

Přidružte k pravidlu back endového fondu, která obsahuje cíle back-end, které obsluhovat požadavky, které přijímá naslouchací proces.

 - Pro základní pravidlo je povolen pouze jeden back endového fondu. Všechny žádosti na přidružený naslouchací proces se předávají do tohoto back endového fondu.

 - Pro pravidlo na základě cest přidáte více fondů back-end, které odpovídají na jednotlivé cesty adresy URL. Požadavky, které odpovídají zadané cestě adresy URL se předávají do odpovídající back endového fondu. Přidejte také výchozí back endového fondu. Požadavky, které neodpovídají žádným adresy URL v pravidle se předávají do tohoto fondu.

### <a name="associated-back-end-http-setting"></a>Související nastavení HTTP back-end

Přidáte nastavení HTTP back-end pro každé pravidlo. Požadavky jsou směrovány z aplikační brány pro back-end cíle s použitím čísla portu, protokolu a další informace, které je zadaná v tomto nastavení.

Základní pravidlo je povoleno pouze jedno nastavení HTTP back-end. Všechny žádosti na přidružený naslouchací proces jsou předávány na odpovídající cíle back-end pomocí tohoto nastavení protokolu HTTP.

Přidáte nastavení HTTP back-end pro každé pravidlo. Požadavky jsou směrovány z aplikační brány pro back-end cíle s použitím čísla portu, protokolu a dalším informacím, který je zadán v tomto nastavení.

Základní pravidlo je povoleno pouze jedno nastavení HTTP back-end. Všechny žádosti na přidružený naslouchací proces jsou předávány na odpovídající cíle back-end pomocí tohoto nastavení protokolu HTTP.

Pro pravidlo na základě cest přidání více nastavení HTTP back-end, které odpovídají na jednotlivé cesty adresy URL. Požadavky, které odpovídají cestě adresy URL v tomto nastavení se předávají na odpovídající cíle back-end s použitím nastavení protokolu HTTP, které odpovídají každé cesty adresy URL. Přidejte také výchozí nastavení protokolu HTTP. Požadavky, které neodpovídají žádným adresy URL v tomto pravidle se předávají do výchozí back endového fondu s použitím výchozí nastavení protokolu HTTP.

### <a name="redirection-setting"></a>Nastavení přesměrování

Pokud přesměrování je nakonfigurovaný pro základní pravidlo, všechny žádosti na přidružený naslouchací proces se přesměrují do cíle. Toto je *globální* přesměrování. Pokud přesměrování je nakonfigurovaný pro pravidlo na základě cest, přesměrování pouze požadavky v oblasti určité lokalitě. Příkladem je nákupní košík oblast, která označuje symbolem */cart/\**. Toto je *na základě cest* přesměrování.

Další informace o přesměrování najdete v tématu [přehled přesměrování ve službě Application Gateway](https://docs.microsoft.com/azure/application-gateway/redirect-overview).

#### <a name="redirection-type"></a>Typ přesměrování

Zvolte typ potřebné přesměrování: *Permanent(301)*, *Temporary(307)*, *Found(302)*, nebo *naleznete v tématu other(303)*.

#### <a name="redirection-target"></a>Cíl přesměrování

Zvolte jinou naslouchací proces nebo externí web jako cíl přesměrování.

##### <a name="listener"></a>Naslouchací proces

Naslouchací proces, zvolte jako cíl přesměrování pro přesměrování přenosu dat z jeden naslouchací proces do druhého v bráně. Toto nastavení je povinné, pokud chcete povolit přesměrování HTTP na HTTPS. Přesměrování přenosů ze zdroje naslouchací proces, který kontroluje příchozí požadavky HTTP na cílový naslouchací proces, který kontroluje příchozí požadavky HTTPS. Můžete také zahrnout řetězec dotazu a cestu z původního požadavku na žádost, která je předávat na cíl přesměrování.

![Dialogové okno součásti aplikace brány](./media/configuration-overview/configure-redirection.png)

Další informace o přesměrování HTTP na HTTPS naleznete v tématu:
- [Přesměrování HTTP na HTTPS pomocí webu Azure portal](https://docs.microsoft.com/azure/application-gateway/redirect-http-to-https-portal)
- [Přesměrování HTTP na HTTPS pomocí prostředí PowerShell](https://docs.microsoft.com/azure/application-gateway/redirect-http-to-https-powershell)
- [Přesměrování HTTP na HTTPS pomocí Azure CLI](https://docs.microsoft.com/azure/application-gateway/redirect-http-to-https-cli)

##### <a name="external-site"></a>Externí web

Vyberte externí web, když chcete přesměrovat provoz na naslouchací proces, který je spojen s tímto pravidlem na externí web. Můžete zahrnout řetězec dotazu z původního požadavku na žádost, která je předávat na cíl přesměrování. Nelze předat cestu k externí web, který byl v původní požadavek.

Další informace o přesměrování najdete v tématu:
- [Přesměrování přenosů na externí web pomocí prostředí PowerShell](https://docs.microsoft.com/azure/application-gateway/redirect-external-site-powershell)
- [Přesměrování přenosů na externí web pomocí rozhraní příkazového řádku](https://docs.microsoft.com/azure/application-gateway/redirect-external-site-cli)

#### <a name="rewrite-the-http-header-setting"></a>Přepsat nastavení hlavičky HTTP

Toto nastavení přidá, odebere nebo aktualizuje hlaviček žádostí a odpovědí protokolu HTTP při požadavku a odpovědi pakety přesunout mezi klientem a back endové fondy. Můžete konfigurovat jenom tato funkce prostřednictvím prostředí PowerShell. Podpora rozhraní příkazového řádku a webu Azure portal ještě nejsou k dispozici. Další informace naleznete v tématu:

 - [Přepište přehled hlavičky protokolu HTTP](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers)
 - [Konfigurovat přepsání hlavičky protokolu HTTP](https://docs.microsoft.com/azure/application-gateway/add-http-header-rewrite-rule-powershell#specify-your-http-header-rewrite-rule-configuration)

## <a name="http-settings"></a>Nastavení HTTP

Application gateway s použitím konfigurace, které zadáte tady směruje provoz do back-end serverů. Když vytvoříte nastavení HTTP, musíte přidružit ho jedno nebo více pravidel směrování žádostí.

### <a name="cookie-based-affinity"></a>Spřažení na základě souborů cookie.

Tato funkce je užitečná, pokud chcete zachovat uživatelskou relaci na stejném serveru. Soubory cookie spravovaných bránou nechat aplikaci brány s přímým přístupem následný provoz z uživatelské relace na stejný server ke zpracování. To je důležité, pokud stav relace se uloží místně na serveru pro relaci uživatele. Pokud aplikace nemůže zpracovat spřažení na základě souborů cookie, nemůže pomocí této funkce. Jeho použití, ujistěte se, že klienti podporují soubory cookie.

### <a name="connection-draining"></a>Vyprázdnění připojení

Vyprázdnění připojení umožňuje řádně odeberte členy fondu back-end během plánovaných aktualizacích. Během vytváření pravidla můžete použít tato nastavení u všech členů fondu back-end. Zajišťuje, že všechny zrušit registraci instance back endového fondu nepřijímají žádné nové požadavky. Mezitím existujících žádostí mohou dokončit během nakonfigurovaného časového limitu. Vyprázdnění připojení se vztahuje na back endových instancí, které jsou výslovně odebrali z back endového fondu podle volání rozhraní API. Platí také pro back endových instancí, které jsou hlášeny jako *není v pořádku* podle stavu sondy.

### <a name="protocol"></a>Protocol (Protokol)

Služba Application Gateway podporuje protokol HTTP i HTTPS pro směrování žádostí na back-end serverů. Pokud vyberete možnost protokolu HTTP, nešifrovaný provoz do back-end serverů. Pokud nešifrovaná komunikace se nedají použít, vyberte HTTPS.

Toto nastavení v kombinaci s HTTPS v podporuje naslouchací proces [-kompletního protokolu SSL](https://docs.microsoft.com/azure/application-gateway/ssl-overview). To umožňuje bezpečně přenášet citlivá data šifrovaná v back-endu. Každý back-end serverů ve fondu back-end, který má povolené-kompletního protokolu SSL musí být nakonfigurován s certifikátem pro umožnění bezpečné komunikace.

### <a name="port"></a>Port

Toto nastavení určuje port, kde back-end serverů naslouchat provoz z aplikační brány. Můžete nakonfigurovat porty od 1 do 65535.

### <a name="request-timeout"></a>Časový limit žádosti

Toto nastavení je počet sekund, po které application gateway bude čekat na přijetí odpovědi z back endový fond, dříve, než vrátí chybovou zprávu "vypršel časový limit připojení".

### <a name="override-back-end-path"></a>Přepsat cestu back-end

Toto nastavení umožňuje nakonfigurovat cestu k volitelné vlastního předávajícího při předá požadavek back-endu. Libovolná součást, který odpovídá vlastní cesty v příchozí cestě **přepsat cestu back-endu** pole je zkopírován do Přesměrovaná cesta. Následující tabulka ukazuje, jak tato funkce funguje:

- Když nastavení protokolu HTTP je připojen k základní pravidlo směrování požadavků:

  | Původní žádost  | Přepsat cestu back-end | Požadavek předá do back-endu |
  | ----------------- | --------------------- | ---------------------------- |
  | /Home/            | /override/            | / Přepsat/home /              |
  | / home/secondhome / | /override/            | / přepsání/home/secondhome /   |

- Když nastavení protokolu HTTP je připojen k pravidlo směrování žádostí na základě cest:

  | Původní žádost           | Pravidlo cesty       | Přepsat cestu back-end | Požadavek předá do back-endu |
  | -------------------------- | --------------- | --------------------- | ---------------------------- |
  | /pathrule/home /            | /pathrule*      | /override/            | / Přepsat/home /              |
  | / parametru pathrule/home/secondhome / | /pathrule*      | /override/            | / přepsání/home/secondhome /   |
  | /Home/                     | /pathrule*      | /override/            | / Přepsat/home /              |
  | / home/secondhome /          | /pathrule*      | /override/            | / přepsání/home/secondhome /   |
  | /pathrule/home /            | / parametru pathrule/Domovská stránka * | /override/            | /override/                   |
  | / parametru pathrule/home/secondhome / | / parametru pathrule/Domovská stránka * | /override/            | / Přepsat/secondhome /        |

### <a name="use-for-app-service"></a>Použití pro službu app service

Toto je uživatelské rozhraní zástupce, který vybere dvě požadovaná nastavení pro back-endu Azure App Service. Umožňuje **vybrat název hostitele z back endových adres**, a vytvoří novou vlastní test paměti. (Další informace najdete v tématu [vybrat název hostitele z back endových adres](#pick) nastavení tohoto článku.) Vytvořit nový test a hlavičku testu je zvolena z adresy člen back-end.

### <a name="use-custom-probe"></a>Použít vlastní test paměti

Toto nastavení přiřadí [vlastní test paměti](https://docs.microsoft.com/azure/application-gateway/application-gateway-probe-overview#custom-health-probe) s nastavením protokolu HTTP. Nastavení HTTP můžete přidružit jenom jeden vlastní test paměti. Pokud není explicitně přiřadit vlastní test paměti, [výchozí kontroly](https://docs.microsoft.com/azure/application-gateway/application-gateway-probe-overview#default-health-probe-settings) se používá k monitorování stavu back-endu. Doporučujeme vám, že vytvoříte vlastní test paměti větší kontrolu nad monitorování stavu vaší back-endů.

> [!NOTE]
> Vlastní test paměti nebude monitorování stavu back endového fondu, pokud není explicitně přidružené naslouchací proces odpovídající nastavení HTTP.

### <a id="pick"/></a>Vybrat název hostitele z back endových adres

Tato funkce dynamicky nastaví *hostitele* hlavičky v požadavku na název hostitele back endového fondu. Použije IP adresu nebo plně kvalifikovaný název domény.

Tato funkce pomáhá, když se liší od názvu DNS služby application gateway název domény pro back-endu a back-endu závisí na konkrétní hlavičku hostitele nebo rozšíření indikace názvu serveru (SNI) překládání na správný koncový bod.

Příkladu případem je služeb s více tenanty jako back-endu. Služby app service je víceklientská služba, která používá sdílené místo jednu IP adresu. Ano služby app service je přístupný pouze prostřednictvím názvů hostitelů, které jsou nakonfigurované v nastavení vlastní domény.

Ve výchozím nastavení, název vlastní domény je *example.azurewebsites.<i> </i>net*. Pro přístup k vaší službě app service pomocí služby application gateway pomocí názvu hostitele, který je registrovaný nejsou explicitně ve službě app service nebo plně kvalifikovaný název domény application gateway, přepsat jako název hostitele v původní požadavek na název hostitele služby app service. Chcete-li to provést, povolte **vybrat název hostitele z back-endová adresa** nastavení.

Pro vlastní doménu, jejíž existujícího vlastního názvu DNS je mapována do služby app service nemusíte povolením tohoto nastavení.

> [!NOTE]
> Toto nastavení není vyžadováno pro App Service Environment pro PowerApps, který je vyhrazený nasazení.

### <a name="host-name-override"></a>Přepsání název hostitele

Tato novinka nahrazuje *hostitele* záhlaví v příchozím požadavku ve službě application gateway s názvem hostitele, který zadáte.

Například pokud *www.contoso<i></i>.com* je zadán v **název hostitele** nastavení původní požadavek *https:/<i></i>/appgw.eastus.cloudapp.net/path1* se změní na *https:/<i></i>/www.contoso.com/path1* při předá požadavek back endového serveru.

## <a name="back-end-pool"></a>Back endového fondu

Back endový fond může odkazovat na čtyři typy členů back-end: konkrétního virtuálního počítače, škálovací sadu virtuálních počítačů, IP adresu nebo plně kvalifikovaný název domény nebo služby app service. Každý fond back-end může odkazovat na více členy stejného typu. Odkazuje na členy různých typů ve stejném fondu back-end se nepodporuje.

Po vytvoření back endového fondu, musíte přidružit ho jedno nebo více pravidel směrování žádostí. Sondy stavu pro každý fond back-end musíte také nakonfigurovat ve službě application gateway. Když je splněna podmínka pravidla směrování žádostí, application gateway předává provoz na v pořádku servery (jako sond stavu) v odpovídající back endového fondu.

## <a name="health-probes"></a>Sondy stavu

Službu application gateway monitoruje stav všech prostředků v back-endu ve výchozím nastavení. Ale důrazně doporučujeme vytvoření vlastního testu paměti pro každé nastavení HTTP back-end získat větší kontrolu nad monitorování stavu. Zjistěte, jak nakonfigurovat vlastní test paměti, naleznete v tématu [vlastní nastavení sondy stavu](https://docs.microsoft.com/azure/application-gateway/application-gateway-probe-overview#custom-health-probe-settings).

> [!NOTE]
> Po vytvoření sondy stavu vlastní musíte přidružit k nastavení HTTP back-end. Vlastní test paměti nebude monitorování stavu back endového fondu, pokud není explicitně přidružené naslouchací proces odpovídající nastavení HTTP.

## <a name="next-steps"></a>Další postup

Teď už víte o součástech Application Gateway, můžete:

- [Vytvoření služby application gateway na webu Azure Portal](quick-create-portal.md)
- [Vytvoření služby application gateway pomocí Powershellu](quick-create-powershell.md)
- [Vytvoření služby application gateway pomocí Azure CLI](quick-create-cli.md)
