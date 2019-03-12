---
title: Přehled konfigurace služby Azure Application Gateway
description: Tento článek obsahuje informace o tom, jak nakonfigurovat různé komponenty ve službě Azure Application Gateway
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 03/04/2019
ms.author: absha
ms.openlocfilehash: 702101039c03b30bb8883ef0308fe68c5567a0c4
ms.sourcegitcommit: 1902adaa68c660bdaac46878ce2dec5473d29275
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/11/2019
ms.locfileid: "57733145"
---
# <a name="application-gateway-configuration-overview"></a>Přehled konfigurace služby Application Gateway

Služba Application gateway se skládá z několika komponent, které můžete nakonfigurovat různými způsoby pro provádění různých scénářů. Tento článek vás provede jak jednotlivé komponenty je potřeba nakonfigurovat.

![application-gateway-components](.\media\configuration-overview\configuration-overview1.png)

Na obrázku výše znázorňuje konfiguraci aplikace s 3 naslouchacích procesů. První dvě jsou naslouchacích procesů více lokalit pro http://acme.com/* a http://fabrikam.com/* v uvedeném pořadí. Obě naslouchají na portu 80. Třetí naslouchací proces je základní naslouchací proces s komplexní ukončení protokolu SSL. 

## <a name="prerequisites"></a>Požadavky

### <a name="azure-virtual-network-and-dedicated-subnet"></a>Virtuální síť Azure a vyhrazenou podsíť

Služba Application gateway je vyhrazené nasazení ve virtuální síti. V rámci vaší virtuální sítě je vyžadována pro vaši bránu application gateway vyhrazenou podsíť. V této podsíti můžete mít více instancí dané brány nasazení aplikace. Můžete ji nasadit taky další služby application Gateway v podsíti, ale žádnému jinému prostředku podsítě služby application gateway nelze nasadit.  

> [!NOTE]   
> Kombinování Standard_v2 a standardní Application Gateway ve stejné podsíti se nepodporuje.

#### <a name="size-of-the-subnet"></a>Velikost podsítě.

V případě v1 SKU application Gateway využívá jednu privátní IP adresu na jednu instanci a jiné privátní IP adresu Pokud je nakonfigurovaný privátní front-endovou konfiguraci IP. Azure si vyhrazuje první čtyři a poslední IP adresu v každé podsíti pro interní použití. Například, pokud služby application gateway je nastavena na tři instance a žádné privátní front-endovou IP, pak je/29 nebo větší velikost podsítě je potřeba. V tomto případě application gateway používá tři IP adresy. Pokud máte tři instance a IP adresu pro privátní front-endovou konfiguraci protokolu IP, pak o velikosti/28 podsíť, velikost nebo vyšší je potřeba, protože čtyři IP adresy jsou povinné.

#### <a name="network-security-groups-supported-on-the-application-gateway-subnet"></a>Skupiny zabezpečení sítě podporované na podsítě Application Gateway.

Skupiny zabezpečení sítě (Nsg) podporují na podsítě Application Gateway s následujícími omezeními: 

- Výjimky musí být umístěno v pro příchozí provoz na portech 65503 65534 pro službu Application Gateway v1 SKU a porty 65200 – 65 535 pro v2 SKU. Tento rozsah portů je nutné pro komunikaci infrastruktury Azure. Jsou chráněny (uzamknuty) s použitím certifikátů Azure. Bez správných certifikátů nemohou externí entity, včetně zákazníků těchto bran, nebudou se moct k zahájení změny činnost těchto koncových bodů.

- Odchozí připojení k Internetu, nejde blokovat.

- Musí se povolit provoz z značka AzureLoadBalancer.

##### <a name="whitelist-application-gateway-access-to-a-few-source-ips"></a>Seznam povolených Application Gateway přístup k několika zdrojové IP adresy

Tento scénář lze provést pomocí skupin Nsg na podsítě služby application gateway. Následující omezení by mělo být uvedeno na podsítě v uvedené pořadí podle priority:

1. Umožňuje příchozí provoz ze zdrojového rozsahu IP/IP.
2. Povolit příchozí žádosti ze všech zdrojů k portům 65503 65534 pro [komunikace stavu back-endu](https://docs.microsoft.com/azure/application-gateway/application-gateway-diagnostics). Tento rozsah portů je nutné pro komunikaci infrastruktury Azure. Jsou chráněny (uzamknuty) s použitím certifikátů Azure. Bez správných certifikátů nemohou externí entity, včetně zákazníků těchto bran, nebude možné zahájit žádné změny na tyto koncové body.
3. Povolit příchozí sondy pro Azure Load Balancer (značka AzureLoadBalancer) a příchozí přenosy virtuální sítě (značka VirtualNetwork) na [NSG](https://docs.microsoft.com/azure/virtual-network/security-overview).
4. Blokovat všechna ostatní příchozí provoz s odepřít všechna pravidla.
5. Povolí odchozí provoz do Internetu pro všechny cíle.

#### <a name="user-defined-routes-supported-on-the-application-gateway-subnet"></a>Trasy definované uživatelem, které jsou podporovány na podsítě Application Gateway.

V případě SKU v1 jsou podporovány trasy definované uživatelem (udr) na podsítě služby application gateway, za předpokladu, nijak nemění komunikace začátku do konce žádostí a odpovědí.

Například trasu UDR v podsítě služby application gateway můžete nastavit tak, aby odkazoval na zařízení brány firewall pro kontrolu paketů, ale musíte zajistit, aby paketu přístup jeho příspěvku kontrole požadovaného cíle. K tomu může dojít nesprávný stav testu nebo provoz směrování chování. To zahrnuje zjištěné trasy nebo výchozí trasy 0.0.0.0/0 šířeny přes ExpressRoute nebo bran VPN ve virtuální síti.

V případě v2 nejsou podporované skladové položky, trasy definované uživatelem na podsítě služby application gateway. Další informace najdete v tématu [automatické škálování a zónově redundantní služba Application Gateway (Public Preview)](https://docs.microsoft.com/azure/application-gateway/application-gateway-autoscaling-zone-redundant#known-issues-and-limitations).

## <a name="frontend-ip"></a>IP front-endu

Můžete nakonfigurovat application gateway buď mít veřejnou IP adresu privátní IP adresu nebo obojí. Veřejná IP adresa je potřeba při hostujete back-end, který je potřeba přistupovat klienti přes internet prostřednictvím virtuálních IP adres přístupem k Internetu. Veřejná IP adresa není potřeba pro vnitřní koncový bod, který není vystavený v Internetu, označované také jako koncový interní služby load balancer (ILB). Konfigurace brány pomocí ILB je užitečná pro interní-obchodní aplikace, které nejsou vystaveny v Internetu. To se hodí i pro služby a vrstvy v rámci vícevrstvé aplikace, které se nacházejí v rámci hranice zabezpečení nevystavené k Internetu, ale stále vyžadují distribuci zatížení modelu kruhového dotazování, dlouhodobost relace nebo ukončení SSL (Secure Sockets Layer).

Je podporován pouze jednu veřejnou IP adresu nebo jednu privátní IP adresu. Zvolte IP adresu front-endu při vytvořením služby Application Gateway. 

- V případě veřejnou IP adresu můžete vytvořit novou veřejnou IP adresu nebo použít stávající veřejnou IP adresu ve stejném umístění jako služba Application Gateway. Pokud jste vytvořili novou veřejnou IP adresu, IP adres jako typu vybrané (statická nebo dynamická) není možné později změnit. Další informace najdete v tématu [porovnání statické a dynamické veřejné IP](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#static-vs-dynamic-public-ip) 

- V případě privátní IP adresa můžete zadat privátní IP adresu z podsítě, ve kterém se vytvoří aplikační bránu. Pokud není explicitně zadán, IP adresu libovolného vybere se automaticky z podsítě. Další informace najdete v tématu [vytvořte aplikační bránu s koncovým bodem interní služby load balancer (ILB).](https://docs.microsoft.com/azure/application-gateway/application-gateway-ilb-arm)

Front-endová IP je přidružená k *naslouchací proces* kontroluje příchozí požadavky na IP adresu front-endu.

## <a name="listeners"></a>Naslouchací procesy

Naslouchací proces je logická entita, která kontroluje příchozí požadavky na připojení použití port, protokol, hostitele a IP adresy. Proto při konfiguraci naslouchacího procesu, budete muset zadat tyto hodnoty port, protokol, hostitele a IP adres jsou stejné jako odpovídající hodnoty v příchozím požadavku na bráně. Při vytváření služby application gateway pomocí webu Azure portal, můžete také vytvořit naslouchací proces výchozí výběrem protokol a port naslouchacího procesu. Kromě toho můžete nastavit, jestli chcete povolit podporu HTTP2 na naslouchací proces. Jakmile se vytvoří aplikační bránu, můžete upravit nastavení tohoto naslouchacího procesu výchozí (*appGatewayHttpListener*/*appGatewayHttpsListener*) a/nebo vytvořit nový naslouchací procesy.

### <a name="listener-type"></a>Typ naslouchací proces

Můžete si vybrat mezi [základní nebo multi-Site naslouchací proces](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#types-of-listeners) při vytváření nový naslouchací proces. 

- Pokud jsou hostiteli jedné lokality za službou Application gateway, zvolte základní naslouchací proces. Přečtěte si [vytvoření služby application gateway s s základní naslouchací proces](https://docs.microsoft.com/azure/application-gateway/quick-create-portal).

- Pokud konfigurujete víc než jednu webovou aplikaci nebo více poddomény stejné nadřazené domény na stejné instance služby application gateway, zvolte Multi-Site naslouchací proces. Pro naslouchací proces více lokalit bude kromě toho musíte zadat název hostitele. Je to proto, že služba Application Gateway spoléhá na hlavičky hostitele HTTP 1.1 pro hostování více než jednoho webu na stejnou veřejnou IP adresu a port.![1551057450710](C:\Users\absha\AppData\Roaming\Typora\typora-user-images\1551057450710.png)


> [!NOTE]
> V případě SKU v1 naslouchacích procesů se zpracovávají v pořadí, ve kterém jsou zobrazeny. Z tohoto důvodu Pokud základní naslouchací proces odpovídá příchozí žádosti zpracovávat jej nejprve. Proto by měl být nakonfigurovaný naslouchací procesy pro více webů před základní naslouchací proces chcete zajistit, aby provoz se směruje do správného back endu.
>
> V případě SKU v2 jsou zpracovány naslouchací procesy pro více webů před základní naslouchací procesy.

### <a name="frontend-ip"></a>IP front-endu

Vyberte IP adresu front-endu, který chcete spojit s tímto naslouchacím procesem. Naslouchací proces bude naslouchat na příchozí požadavek na tuto IP adresu.

### <a name="frontend-port"></a>Front-endový Port

Vyberte front-endový port. Můžete vybrat z existujících porty nebo vytvořte novou. Můžete použít libovolnou hodnotu od [povolený rozsah portů](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#ports). To umožňuje nejen použijete běžně používaných portů, jako je například 80 a 443, ale žádné povolené port. Tento vlastní port vhodný pro použití. Jeden port je možné použít pro veřejný internetový naslouchacích procesů nebo soukromých protilehlé naslouchacích procesů.

### <a name="protocol"></a>Protocol (Protokol)

Musíte zvolit protokol HTTP a HTTPS. 

- Pokud vyberete možnost protokolu HTTP, budou směrovat přenos dat mezi brány klienta a aplikačního nezašifrované.

- Vyberte protokol HTTPS, pokud vás zajímají [vrstvy SSL (Secure Sockets) ukončení](https://docs.microsoft.com/azure/application-gateway/overview#secure-sockets-layer-ssl-terminationl) nebo [koncové šifrování protokolu SSL](https://docs.microsoft.com/azure/application-gateway/ssl-overview). Pokud vyberete možnost HTTPS, přenos dat mezi brány klienta a aplikaci budou zašifrovaná a ukončí připojení protokolem SSL ve službě application gateway.  Pokud chcete koncové šifrování protokolu SSL, bude kromě toho musíte zvolit protokol HTTPS při konfiguraci *nastavení HTTP back-endu*. Tím se zajistí, že provoz je znovu zašifrován při jejich přenosu z aplikační brány pro back-endu.

  Pokud chcete nakonfigurovat ukončení vrstvy SSL (Secure Sockets) a koncového šifrování protokolu SSL, je vyžadován certifikát přidat k naslouchacímu procesu, aby byla zajištěna aplikační brány pro odvození symetrického klíče podle specifikace protokolu SSL. Symetrický klíč se pak používá k šifrování a dešifrování provoz odeslaný na bránu. Certifikát brány musí být ve formátu Personal Information Exchange (PFX). Tento formát souboru umožňuje exportovat soukromý klíč, který vyžaduje službu application gateway šifrování a dešifrování přenosů. 

#### <a name="supported-certs"></a>Podporované certifikátů

Certifikáty podepsané svým držitelem, certifikáty CA certs, zástupný znak – certifikáty a certifikáty EV jsou podporovány.

### <a name="additional-protocol-support"></a>Podpora dalších protokolů

#### <a name="http2-support"></a>Podpora HTTP2

Podpora protokolu HTTP/2 je k dispozici pro připojení klientů k application gateway pouze posluchače. Komunikace do fondů back-end serveru je přes HTTP/1.1. Ve výchozím nastavení je zakázána podpora HTTP/2. Následující příklad fragmentu kódu prostředí Azure PowerShell ukazuje, jak můžete zajistit:

```azurepowershell
$gw = Get-AzureRmApplicationGateway -Name test -ResourceGroupName hm

$gw.EnableHttp2 = $true

Set-AzureRmApplicationGateway -ApplicationGateway $gw
```

#### <a name="websocket-support"></a>Podpora protokolu Websocket

Ve výchozím nastavení je povolena podpora protokolu Websocket. Neexistuje žádné uživatelsky konfigurovatelné nastavení pro selektivní povolení nebo zakázání podpory protokolu WebSocket. S naslouchacích procesů HTTP a HTTPS můžete použít objekty Websocket. 

### <a name="custom-error-page"></a>Vlastní chybové stránky

Vlastní chybové stránky dají definovat na globální úrovni, jakož i na úrovni naslouchacího procesu, ale není aktuálně podporováno vytváření globální úrovni vlastní chybové stránky na webu Azure Portal. Vlastní chybové stránky pro chybu 403 WAF nebo 502 stránce Údržba můžete nakonfigurovat na úrovni naslouchacího procesu. Také musíte zadat adresu URL veřejně přístupná objektů blob, stavový kód dané chyby. Další informace najdete v tématu [vytvořit vlastní chybovou stránku](https://docs.microsoft.com/azure/application-gateway/custom-error).

![Kódy chyb aplikace brány](https://docs.microsoft.com/azure/application-gateway/media/custom-error/ag-error-codes.png)

Pokud chcete konfigurovat globální vlastní chybovou stránku, použijte [prostředí Azure PowerShell pro konfiguraci](https://docs.microsoft.com/azure/application-gateway/custom-error#azure-powershell-configuration) 

### <a name="ssl-policy"></a>Zásady protokolu SSL

Můžete centralizovat správu certifikátů SSL a snížit režijní náklady na šifrování a dešifrování z back endovém serveru farmy. Tento centrální SSL zpracování také umožňuje určit centrální zásady protokolu SSL, který je vhodný pro požadavky vaší organizace na zabezpečení.  Můžete zvolit výchozí, předdefinovaná a vlastní zásady protokolu SSL. 

Můžete nakonfigurovat zásady protokolu SSL pro řízení verze protokolu SSL. Můžete nakonfigurovat aplikační brány pro protokol TLS 1.0, TLS1.1 a TLS1.2 odepřít. Protokol SSL 2.0 a 3.0 jsou již ve výchozím nastavení zakázána a se nedají konfigurovat. Další informace najdete v tématu [přehled zásad Application Gateway SSL](https://docs.microsoft.com/azure/application-gateway/application-gateway-ssl-policy-overview).

Po vytvoření naslouchacího procesu, přiřaďte ji k žádosti o pravidlo směrování, která určuje, jak se žádost o přijetí na naslouchací proces má být směrována na back-endu.

## <a name="request-routing-rule"></a>Požádat o pravidlo směrování

Při vytváření application gateway pomocí webu Azure portal, vytvoření výchozího pravidla (*pravidla 1*), který váže naslouchací proces výchozí (*appGatewayHttpListener*) s výchozí back-endového fondu (*appGatewayBackendPool*) a výchozí nastavení back-endu HTTP (*appGatewayBackendHttpSettings*). Jakmile se vytvoří aplikační bránu, můžete upravit nastavení toto výchozí pravidlo nebo vytvořit nová pravidla.

### <a name="rule-type"></a>Typ pravidla

Můžete si vybrat mezi [basic nebo na základě cest pravidlo](https://docs.microsoft.com/azure/application-gateway/application-gateway-components#request-routing-rule) při vytváření nové pravidlo. 

- Pokud budete chtít předávat všechny žádosti na přidružený naslouchací proces (např: blog.contoso.com/*) Chcete-li jeden back-endový fond, zvolte základní naslouchací proces. 
- Zvolte cestu naslouchací proces založený na Pokud chcete směrovat požadavky s konkrétní cestu adresy URL ke konkrétní back-endové fondy. Vzor cesty platí pouze pro cestu adresy URL, aby jeho parametry dotazu.


> [!NOTE]
>
> V případě SKU v1 odpovídající vzorku příchozích požadavků, jsou zpracovávána v pořadí, ve kterém jsou uvedeny cesty v objektu map cestu adresy URL pravidla na základě cest. Z tohoto důvodu Pokud požadavek odpovídá vzoru v dvě nebo více cest v mapě cestu adresy URL, pak cestu, která je uvedená nejprve bude odpovídat a požadavek se předají do back-endu přidružené k této cestě.
>
> V případě SKU v2 obsahuje přesnou shodu vyšší prioritu v pořadí, ve kterém jsou uvedeny cesty v objektu map cestu adresy URL. Pro tento důvod, pokud požadavek odpovídá vzoru ve dvou nebo více cest, pak žádost se předají do back-endu přidružené této cesty, který přesně odpovídá požadavku. Pokud cestu v příchozím požadavku přesně neodpovídá žádné cesty v objektu map cestu adresy URL, pak odpovídající vzorku příchozích požadavků jsou zpracovávány v pořadí, ve kterém jsou uvedené cesty v objektu map cestu adresy URL pravidla na základě cest.

### <a name="associated-listener"></a>Přidružený naslouchací proces

Je nutné přidružit naslouchací proces pro pravidlo tak, aby *požádat o pravidlo směrování* spojené s *naslouchací proces* je vyhodnocován pro určení *back-endový fond* ke kterému požadavek se bude směrovat.

### <a name="associated-backend-pool"></a>Přidružené back-endového fondu

Přidružte back-endový fond obsahující cíle back-end, které budou obsluhovat požadavky přijímány naslouchací proces. V případě základní pravidlo je pouze jeden back-endový fond povolená, protože všechny požadavky na přidružený naslouchací proces se předají do tohoto back-endový fond. V případě pravidlo na základě cest přidáte více fondů back-endu odpovídající každé cesty adresy URL. Požadavky, které odpovídají zde zadaná cesta URL se předají do odpovídající back-endový fond. Také přidáte výchozí back-endový fond, protože ho budou předány požadavky, které se neshodují s libovolnou cestu adresy URL zadané v tomto pravidle.

### <a name="associated-backend-http-setting"></a>Přidružené back-endového nastavení HTTP

Přidáte nastavení HTTP back-endu pro každé pravidlo. Požadavky se budou směrovat z aplikační brány pro back-endu cíle pomocí číslo portu, protokolu a další nastavení zadané v tomto nastavení. V případě základní pravidlo je pouze jedno nastavení HTTP back-endu povolená, protože všechny požadavky na přidružený naslouchací proces se předají do odpovídající cíle back-end pomocí tohoto nastavení protokolu HTTP. V případě pravidlo na základě cest přidání více nastavení HTTP back-endu odpovídající každé cesty adresy URL. Požadavky, které odpovídají zde zadaná cesta URL se předají do odpovídající cíle back-end pomocí protokolu HTTP nastavení odpovídající každé cesty adresy URL. Také přidáte výchozí nastavení protokolu HTTP, protože požadavky, které se neshodují s libovolnou cestu adresy URL zadané v tomto pravidle se předají do výchozí fond back-end pomocí výchozích nastavení protokolu HTTP.

### <a name="redirection-setting"></a>Nastavení přesměrování

Pokud pro základní pravidlo je nakonfigurované přesměrování, všechny požadavky na přidružený naslouchací proces budete přesměrováni na cíl přesměrování, a tím umožnit globální přesměrování. Pokud přesměrování je nakonfigurovaný pro pravidlo na základě cest, požadavky pouze na určitém webovém serveru oblast, třeba nákupního košíku oblasti udávají/košík / *, budete přesměrováni na cíl přesměrování, a tím umožnit přesměrování na základě cest. 

Informace o funkci přesměrování najdete v tématu [přehled přesměrování](https://docs.microsoft.com/azure/application-gateway/redirect-overview).

- #### <a name="redirection-type"></a>Typ přesměrování

  Zvolte typ přesměrování vyžadované od: Trvalé, dočasný, najít nebo zobrazit jiné.

- #### <a name="redirection-target"></a>Cíl přesměrování

  Můžete zvolit jinou naslouchací proces nebo externí web jako cíl přesměrování. 

  - ##### <a name="listener"></a>Naslouchací proces

    Když zvolíte naslouchací proces jako cíl přesměrování pomáhá přesměrování z jeden naslouchací proces na jiné naslouchací proces na bráně. Toto nastavení je povinné, pokud chcete povolit HTTP na HTTPS přesměrování, například přesměrovat provoz z zdroj naslouchací proces kontroly pro příchozí požadavky HTTP na cílový naslouchací proces kontroly příchozím požadavkům HTTPS. Můžete také zvolit řetězec dotazu a cesty v původní žádosti, které mají být zahrnuty v žádosti o předávat na cíl přesměrování.![application-gateway-components](.\media\configuration-overview\configure-redirection.png)

    Další informace o protokolu HTTP na HTTPS přesměrování najdete v tématu [přesměrování HTTP na protokol HTTP pomocí portálu](https://docs.microsoft.com/azure/application-gateway/redirect-http-to-https-portal), [přesměrování HTTP na HTTP pomocí prostředí PowerShell](https://docs.microsoft.com/azure/application-gateway/redirect-http-to-https-powershell), [přesměrování HTTP na protokol HTTP pomocí rozhraní příkazového řádku](https://docs.microsoft.com/azure/application-gateway/redirect-http-to-https-cli)

  - ##### <a name="external-site"></a>Externí web

    Vyberte externí web, když chcete přesměrovat provoz na naslouchací proces přidružená k pravidlu tedy přesměrovat na externí web. Můžete řetězec dotazu v původní žádosti, které mají být zahrnuty v žádosti o předávat na cíl přesměrování. Cesta v původní požadavek na externí web nemohou předat dál.

    Další informace o přesměrování na externí web, naleznete v tématu [přesměrování provozu na externí web pomocí prostředí PowerShell](https://docs.microsoft.com/azure/application-gateway/redirect-external-site-powershell) a [https://docs.microsoft.com/azure/application-gateway/redirect-external-site-cli](https://docs.microsoft.com/azure/application-gateway/redirect-external-site-cli)

#### <a name="rewrite-http-header-setting"></a>Přepsat nastavení hlavičky HTTP

Díky této funkci můžete přidat, odebrat nebo aktualizovat hlaviček žádostí a odpovědí protokolu HTTP při požadavku a odpovědi pakety přesouvat mezi klientem a back-endové fondy.    Můžete nakonfigurovat tato možnost jenom přes PowerShell. Podpora portálu a rozhraní příkazového řádku ještě není k dispozici. Další informace najdete v tématu [hlavičky protokolu HTTP přepsat](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers) přehled a [revize záhlaví HTTP konfigurace](https://docs.microsoft.com/azure/application-gateway/add-http-header-rewrite-rule-powershell#specify-your-http-header-rewrite-rule-configuration).

## <a name="http-settings"></a>Nastavení HTTP

Application gateway směruje provoz do back-end serverů pomocí konfigurace zadaná v této komponentě. Jakmile vytvoříte nastavení HTTP, budete se muset spojit s jedním nebo více pravidel směrování žádostí.

### <a name="cookie-based-affinity"></a>Spřažení na základě souborů cookie

Tato funkce je užitečná, pokud chcete zachovat uživatelskou relaci na stejném serveru. Pomocí souborů cookie spravovaných bránou dokáže aplikační brána směrovat následný provoz z uživatelské relace ke zpracování na stejný server. To je důležité v případech, kdy se stav jednotlivých uživatelských relací ukládá místně na serveru. Pokud aplikace nemůže zpracovat spřažení na základě souborů cookie, pak nebudete moct používat tuto funkci. Použití spřažení relace na základě souborů cookie, se ujistěte, že klienti musí podporovat soubory cookie. 

### <a name="connection-draining"></a>Vyprázdnění připojení

Vyprázdnění připojení vám pomůže provést řádné odebrání členů back-endového fondu při plánovaných servisních aktualizacích. Toto nastavení lze použít u všech členů fondu back-endu během vytváření pravidla. Po povolení službě application gateway zajišťuje, že všechny zrušit registraci instance back-endového fondu nebudou přijímat žádné nové požadavky zároveň umožní existující žádosti o dokončení v rámci nakonfigurovaného časového limitu. To platí jak pro instance back-endu, které jsou výslovně odebrány z back-endového fondu na základě volání rozhraní API, tak pro takové, které jsou podle sond stavu hlášeny jako poškozené.

### <a name="protocol"></a>Protocol (Protokol)

Služba Application gateway podporuje protokoly HTTP a HTTPS pro směrování žádostí na back-end serverů. Pokud je vybrán protokol HTTP, provoz pak toky nešifrován na back-end serverů. V těchto případech nešifrovaná komunikace s back-end servery nepřijatelnou možností měli byste zvolit protokol HTTPS. Toto nastavení kombinované s výběrem protokolu HTTPS v naslouchací proces umožňuje povolit [kompletního protokolu SSL](https://docs.microsoft.com/azure/application-gateway/ssl-overview). Který umožňuje bezpečně přenášet citlivá data do back-endu v zašifrované. Každý back-endový server v back-endovém fondu s povoleným koncovým šifrováním protokolu SSL musí být pro umožnění bezpečné komunikace nakonfigurován s certifikátem.

### <a name="port"></a>Port

Toto je port, servery back-end naslouchají na provoz přicházející z aplikační brány. Můžete nakonfigurovat porty od 1 do 65535.

### <a name="request-timeout"></a>Časový limit žádosti

Počet sekund, po které application gateway bude čekat na přijetí odpovědi z back-endový fond před vrácením chybu "Vypršel časový limit připojení".

### <a name="override-backend-path"></a>Přepsat cestu na back-end

Toto nastavení umožňuje nakonfigurovat cestu k volitelné vlastního předávajícího při předá požadavek back-endu. Tím dojde ke zkopírování libovolná součást, která odpovídá do vlastní cesty zadané v příchozí cestě **přepsat cestu back-endu** pole na předané cesty. Viz Tabulka níže pochopit, jak funkce funguje.

- Když nastavení protokolu HTTP je připojen k základní požadavek pravidel směrování:

  | Původní žádost  | Přepsat cestu na back-end | Požadavek předá do back-endu |
  | ----------------- | --------------------- | ---------------------------- |
  | /Home/            | /override/            | / Přepsat/home /              |
  | / home/secondhome / | /override/            | / přepsání/home/secondhome /   |

- Když nastavení protokolu HTTP je připojen k pravidlo směrování žádostí podle cesty:

  | Původní žádost           | Pravidlo cesty       | Přepsat cestu na back-end | Požadavek předá do back-endu |
  | -------------------------- | --------------- | --------------------- | ---------------------------- |
  | /pathrule/home /            | /pathrule*      | /override/            | / Přepsat/home /              |
  | / parametru pathrule/home/secondhome / | /pathrule*      | /override/            | / přepsání/home/secondhome /   |
  | /Home/                     | /pathrule*      | /override/            | / Přepsat/home /              |
  | / home/secondhome /          | /pathrule*      | /override/            | / přepsání/home/secondhome /   |
  | /pathrule/home /            | / parametru pathrule/Domovská stránka * | /override/            | /override/                   |
  | / parametru pathrule/home/secondhome / | / parametru pathrule/Domovská stránka * | /override/            | / Přepsat/secondhome /        |

### <a name="use-for-app-service"></a>Použít pro službu App Service

Je to zástupce uživatelského rozhraní, který vybere dvě požadovaná nastavení pro back-end aplikace App service – umožňuje vybrat název hostitele z adres back-endu a vytvoří novou vlastní test paměti. V části je vysvětlen důvod, proč se provádí bývalé **vybrat název hostitele z back-endová adresa** nastavení. Nový test je vytvořen, kde hlavičku testu také vybere ze člen back-endových adres.

### <a name="use-custom-probe"></a>Použít vlastní test paměti

Toto nastavení slouží k přidružení [vlastní test paměti](https://docs.microsoft.com/azure/application-gateway/application-gateway-probe-overview#custom-health-probe) s tímto nastavením protokolu HTTP. Nastavení HTTP můžete přidružit jenom jeden vlastní test paměti. Pokud není přidružíte explicitně vlastní test paměti, pak [výchozí kontroly](https://docs.microsoft.com/azure/application-gateway/application-gateway-probe-overview#default-health-probe-settings) se použije k monitorování stavu back-endu. Doporučuje se, že vytvoříte vlastní test paměti mít podrobnější kontrolu nad monitorování stavu vaší back-EndY.

> [!NOTE]   
> Vlastní test paměti nezahájí, monitorování stavu back-endového fondu, pokud není explicitně přidružené naslouchací proces odpovídající nastavení HTTP.

### <a name="pick-host-name-from-backend-address"></a>Název hostitele vybraný z back-endové adresy

Tato funkce dynamicky nastaví *hostitele* hlavičky v požadavku na název hostitele back-endového fondu pomocí IP adresy nebo plně kvalifikovaný název domény (FQDN). To je užitečné v situacích, kdy se liší od názvu DNS služby application gateway název domény pro back-endu a back-endu závisí na konkrétní hlavičku hostitele nebo rozšíření SNI přeložit na správný koncový bod, jako například v případě služeb s více tenanty jako back-endu. App service je víceklientská služba sdílené místo pomocí jednu IP adresu, službu App service je přístupný pouze s názvy hostitelů nakonfigurované v nastavení vlastní domény. Ve výchozím nastavení, název vlastní domény je *example.azurewebsites.net*. Proto pokud chcete přístup k vaší službě App service pomocí služba application gateway, buď název hostitele není explicitně registrován ve službě App service nebo s Application gateway plně kvalifikovaný název domény, musíte přepsat jako název hostitele v původní požadavek na název hostitele služby App service, tak, že povolení **vybrat název hostitele z back-endová adresa** nastavení.

Pokud jste vlastní doménu a namapované existujícího vlastního názvu DNS do služby App service, není potřeba povolit toto nastavení.

> [!NOTE]   
> Toto nastavení se nevyžaduje pro App Service Environment (ASE), protože služba ASE je vyhrazená nasazení. 

### <a name="host-name-override"></a>Přepsání název hostitele

Tato novinka nahrazuje *hostitele* záhlaví v příchozím požadavku ve službě application gateway na název hostitele, který zde určíte. Například, pokud je stanoveno www.contoso.com **název hostitele** nastavení původní požadavek https://appgw.eastus.cloudapp.net/path1 se změní na https://www.contoso.com/path1 při předá požadavek back-end serveru. 

## <a name="backend-pool"></a>Back-endový fond

Back-endový fond můžete odkazovala na čtyři typy členů back-end: konkrétního virtuálního počítače, škálovací sadu virtuálních počítačů, IP adresu nebo plně kvalifikovaný název domény nebo služby app service. Každý fond back-end může odkazovat na více členy stejného typu. Odkazující členy různých typů ve stejném fondu back-end se nepodporuje. 

Po vytvoření back-endového fondu, musíte ji přidružit k pravidla směrování požadavku, jeden nebo více. Budete potřebovat ke konfiguraci sondy stavu pro každý fond back-endu na vaše brána application gateway. Při splnění podmínkou pravidla směrování žádostí na službu application gateway předává provoz na v pořádku servery (jako sond stavu) v odpovídající back-endový fond.

## <a name="health-probes"></a>Sondy stavu

I v případě, že služba application gateway monitoruje stav všech prostředků v jeho back-endu ve výchozím nastavení, doporučujeme vytvoření vlastního testu paměti pro každé nastavení HTTP back-endu mohla tak mít podrobnější kontrolu nad monitorování stavu. Další informace o konfiguraci vlastních stavových testu, naleznete v tématu [vlastní nastavení sondy stavu](https://docs.microsoft.com/azure/application-gateway/application-gateway-probe-overview#custom-health-probe-settings).

> [!NOTE]   
> Po vytvoření sondy stavu vlastní musíte přidružit k nastavení HTTP back-endu. Vlastní test paměti nezahájí, monitorování stavu back-endového fondu, pokud není explicitně přidružené naslouchací proces odpovídající nastavení HTTP.

## <a name="next-steps"></a>Další postup

Po získání informací o službě Application Gateway součásti, můžete:

- [Vytvoření služby Application Gateway na webu Azure Portal](quick-create-portal.md)
- [Vytvoření služby Application Gateway pomocí Powershellu](quick-create-powershell.md)
- [Vytvoření služby Application Gateway pomocí Azure CLI](quick-create-cli.md)