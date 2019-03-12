---
title: Azure Application Gateway komponenty
description: Tento článek obsahuje informace o různých komponent ve službě Application Gateway
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 02/20/2019
ms.author: absha
ms.openlocfilehash: 0e1232391ddf4b57b5dad5ade9776e9c7cfea9b4
ms.sourcegitcommit: 235cd1c4f003a7f8459b9761a623f000dd9e50ef
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/11/2019
ms.locfileid: "57727327"
---
# <a name="application-gateway-components"></a>Součásti aplikace brány

 Službu application gateway slouží jako jediný bod kontaktu pro klienty. U více fondů back-end, jako jsou virtuální počítače Azure, škálovací sady virtuálních počítačů, aplikační služby nebo na místní nebo externí servery ji distribuuje příchozí aplikační provoz. Chcete-li to provést, používá několik komponent popsaných v tomto článku.

![application-gateway-components](.\media\application-gateway-components\application-gateway-components.png)

## <a name="frontend-ip-address"></a>IP adresa front-endu

IP adresa front-endu je IP adresa přidružená k bráně aplikace. Můžete nakonfigurovat application gateway buď mít veřejnou IP adresu privátní IP adresu nebo obojí. Je podporován pouze jednu veřejnou IP adresu nebo jednu privátní IP adresu. Virtuální síť a veřejnou IP adresu musí být ve stejném umístění jako vaše brána application gateway.

Front-endová IP adresa je přidružená k *naslouchací proces* po jeho vytvoření. 

### <a name="static-vs-dynamic-public-ip-address"></a>Porovnání statické a dynamické veřejnou IP adresu

V1 SKU podporuje statické interní IP adresy, ale nepodporuje statické veřejné IP adresy. Virtuální IP adresu můžete změnit, pokud application gateway je zastavit a spustit. Název DNS, který je přidružený k službě application gateway nemění přes životní cyklus brány. Z tohoto důvodu se doporučuje použít CNAME alias a přejděte na adresu DNS služby application gateway.

SKU v2 Application Gateway podporuje statické veřejné IP adresy, stejně jako statické interní IP adresy. 

## <a name="listeners"></a>Naslouchací procesy

Než začnete používat vaše brána application gateway, musíte přidat jednu nebo víc naslouchacích procesů. Naslouchací proces je logická entita, která kontroluje příchozí požadavky na připojení a přijímá požadavky, je-li protokol, port, hostitele a IP adresy přidružené k žádosti o shodu s protokol, port, hostitele a IP adresu přidruženou k konfiguraci naslouchacího procesu. Může existovat několik naslouchacích procesů, které jsou připojené k službě application gateway a mohou být použity pro stejný protokol. Po naslouchací proces detekuje příchozí žádosti od klientů, službu Application Gateway směrovat tyto žádosti na členy ve fondu back-end pomocí pravidel směrování žádosti, které definujete pro naslouchací proces, který obdržel příchozího požadavku.

Naslouchací procesy podporují následující porty a protokoly:

### <a name="ports"></a>Porty

Toto je port, na kterém naslouchá naslouchací proces pro požadavek klienta. Můžete nakonfigurovat porty rozsahu od 1 do 65502 pro V1 SKU a 1 pro 65199 pro V2 SKU.

### <a name="protocols"></a>Protokoly

Služba Application gateway podporuje následující čtyři protokoly: HTTP, HTTPS, HTTP/2 a protokol WebSocket

Explicitně neurčíte možností volby mezi protokoly HTTP a HTTPS v konfiguraci naslouchacího procesu. [Podporu pro protokoly Websocket a HTTP/2](https://docs.microsoft.com/azure/application-gateway/overview#websocket-and-http2-traffic) je k dispozici nativně. [Podpora protokolu Websocket](https://docs.microsoft.com/azure/application-gateway/application-gateway-websocket) je ve výchozím nastavení povolené. Neexistuje žádné uživatelsky konfigurovatelné nastavení pro selektivní povolení nebo zakázání podpory protokolu WebSocket. S naslouchacích procesů HTTP a HTTPS můžete použít objekty Websocket. Podpora protokolu HTTP/2 je k dispozici pro připojení klientů k application gateway pouze posluchače. Komunikace do fondů back-end serveru je přes HTTP/1.1. Ve výchozím nastavení je zakázána podpora HTTP/2. Můžete ji povolit.

Naslouchací proces HTTPS můžete použít pro ukončení protokolu SSL. Naslouchací proces HTTPS snižuje zátěž při šifrování a dešifrování pracovat pro vaši bránu application gateway tak, aby vaše webové servery nevznikaly podle tohoto režijní náklady. Vaše aplikace je pak zdarma a zaměřte se na jejich obchodní logiku.

### <a name="custom-error-pages"></a>Vlastní chybové stránky

Služba Application gateway umožňuje vytvořit vlastní chybové stránky místo výchozí chybové stránky. U vlastní chybové stránky můžete použít vlastní značky a rozložení. Služba Application gateway můžou zobrazovat vlastní chybovou stránku, když požadavek nemá přístup na back-endu. Další informace najdete v tématu [vlastní chybové stránky pro vaši bránu Application Gateway](https://docs.microsoft.com/azure/application-gateway/custom-error).

### <a name="types-of-listeners"></a>Typy naslouchacích procesů

Existují dva typy naslouchacích procesů:

- **Základní**: Tento typ naslouchací proces naslouchá na jednu doménu web, kde má jednoho mapování DNS na IP adresu služby application gateway. Tato konfigurace naslouchacího procesu je nutná, pokud jsou hostiteli jedné lokality za službou application gateway.
- **Multi-Site**: Tato konfigurace naslouchacího procesu je nutná, pokud nakonfigurujete více než jednu webovou aplikaci na stejné instance služby application gateway. To umožňuje nakonfigurovat efektivnější topologii vašich nasazení tak, že přidáte až 100 webů do jedné služby application gateway. Každou stránku lze přesměrovat na vlastní back-endový fond. Příklad:  Pro tři subdomény – abc.contoso.com, xyz.contoso.com a pqr.contoso.com přejděte na adresu IP služby application gateway. Vytvoření tří naslouchacích procesů typu *Multi-Site* a konfigurovat každý naslouchací proces pro příslušný port a protokol nastavení. Další informace najdete v tématu [hostování více webů](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-app-overview).

Po vytvoření naslouchacího procesu, přiřaďte ji k pravidlo směrování požadavku, která určuje, jak se žádost o přijetí na naslouchací proces má být směrována na back-endu.

Naslouchací procesy jsou zpracovávány v pořadí, ve kterém jsou zobrazeny. Z tohoto důvodu Pokud základní naslouchací proces odpovídá příchozí žádosti zpracovávat ji nejprve. Naslouchací procesy Multi-Site by měl být nakonfigurovaný před základní naslouchací proces chcete zajistit, aby provoz se směruje do správného back endu.

## <a name="request-routing-rule"></a>Požádat o pravidlo směrování

Toto je nejdůležitější komponenta služby application gateway a určuje, jak se směrují provoz na naslouchací proces spojený s tímto pravidlem. Pravidlo váže naslouchací proces, fondu back-end serverů a nastavení HTTP back-endu. Jakmile požadavek se přijal naslouchací proces, pravidlo směrování požadavku určuje, zda požadavek předávají back-endu, nebo se přesměruje jinde. Pokud požadavek je určen k přeposlání do back-endu, definuje pravidlo směrování požadavku kterému fondu back endového serveru mají ho předávat. Kromě toho pravidlo směrování požadavku také určuje, zda záhlaví v žádosti, aby se povolilo. Jeden naslouchací proces lze připojit k pouze jedno pravidlo.

Může existovat dva typy pravidel směrování žádosti:

- **Basic:** Všechny žádosti na přidružený naslouchací proces (například: blog.contoso.com/*) se předávají do přidružené back-endový fond, pomocí přidružené nastavení protokolu HTTP.
- **Na základě cest:** Tento typ pravidla umožňuje směrovat žádosti na přidružený naslouchací proces pro konkrétní back-end fondu na základě adresy URL v požadavku. Pokud cesta adresy URL v požadavku odpovídá vzor cesty v pravidle na základě cest, požadavek se směruje pomocí tohoto pravidla. Vzor cesty platí pouze pro cestu adresy URL, aby jeho parametry dotazu. Pokud cesta adresy URL požadavku na naslouchací proces neodpovídá žádné z pravidel na základě cest, pak požadavek se přesměruje do *výchozí* back-endového fondu a *výchozí* nastavení HTTP. Další informace najdete v tématu [směrování na základě adresy URL](https://docs.microsoft.com/azure/application-gateway/url-route-overview).

### <a name="redirection-support"></a>Podpora přesměrování

Pravidlo směrování požadavku také umožňuje přesměrování provozu ve službě application gateway. Jde o obecný mechanismus přesměrování, takže můžete provoz přesměrovat z kteréhokoliv portu a na kterýkoliv port, který definujete pomocí pravidel. Můžete zvolit cíl přesměrování na jiný naslouchací proces (který může pomoct, povolte automatické HTTP do HTTPS přesměrování) nebo externí web, zvolte přesměrování být trvalé nebo dočasné nebo zvolte připojení řetězec identifikátoru URI cestu a dotaz na přesměrované adresu URL. Další informace najdete v tématu [přesměrování provozu ve službě Application Gateway](https://docs.microsoft.com/azure/application-gateway/redirect-overview).

#### <a name="rewrite-http-headers"></a>Přepsání hlaviček HTTP

Pomocí pravidel směrování žádosti můžete přidat, odebrat nebo aktualizovat HTTP (S) žádosti a hlaviček odpovědí při požadavku a odpovědi pakety přesouvat mezi klientem a back-endové fondy přes application gateway. Záhlaví nejde nastavit pouze na statické hodnoty, ale také k jiné záhlaví a důležitých serverových proměnných. To vám pomůže provádět několik případů použití důležité, jako je například extrahování IP adresu klienti odebrání citlivých informací o back-endu, přidat další bezpečnostní opatření, atd. Další informace najdete v tématu [hlavičky HTTP přepsat na vaše brána application gateway](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers).

## <a name="http-settings"></a>Nastavení HTTP

Aplikace brány směruje provoz do back-end, které používají číslo portu protokolu servery (zadané v pravidle směrování požadavku, ke kterému jsou připojené nastavení HTTP) a další nastavení zadané v této součásti. Port a protokol použitý v nastavení HTTP určit, jestli je přenos dat mezi servery brány a back-endu aplikace šifrovaná, tedy provádění kompletního protokolu SSL, nebo bez šifrování. Tato součást se také používá k: určení, zda je relace uživatele uchovávat na stejném serveru s použitím [spřažení relace na základě souborů cookie](https://docs.microsoft.com/azure/application-gateway/overview#session-affinity), provést řádné odebrání členů fondu back-end pomocí [připojení vyprázdnění](https://docs.microsoft.com/azure/application-gateway/overview#connection-draining)přidružit vlastní test paměti k monitorování stavu back-endu, nastavit interval časového limitu žádosti, přepište název hostitele a cestu v požadavku a poskytují jedním kliknutím snadno zadat nastavení back-endu pro back-end aplikace App service. 

## <a name="backend-pool"></a>Back-endový fond

Back-endového fondu se používá k směrovat požadavky do back-end serverů, které požadavek vyřídit. Back-endové fondy se může skládat ze síťových adaptérů, škálování virtuálních počítačů sady, veřejné IP adresy, interní IP adres, plně kvalifikovaný název domény a více tenantů back EndY, jako je Azure App Service. Členy fondu back-end brány aplikace nejsou vázané na skupinu dostupnosti. Služba Application Gateway může komunikovat s instancí mimo virtuální síť, že je v, proto může být členy back-endové fondy napříč clustery, datových centrech, nebo mimo Azure, jako je připojení pomocí IP adresy. Pokud plánujete používat interní IP adresy jako členy fondu back-end, pak vyžaduje [VNET Peering](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) nebo [VPN Gateway](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways). VNet peering se podporuje a je přínosné pro vyrovnávání zatížení provozu v jiných virtuálních sítí. Application gateway může také komunikovat s k na místních serverech při připojení pomocí ExpressRoute nebo VPN tunely tak dlouho, dokud je povolený provoz.

Můžete vytvořit jiný back-endové fondy pro různé typy požadavků. Například vytvořte jeden back-endového fondu pro obecné požadavky a druhý fond back-end pro požadavky na mikroslužby pro vaši aplikaci.

## <a name="health-probes"></a>Sondy stavu

Ve výchozím nastavení služba Application gateway monitoruje stav všech prostředků v back-endového fondu a automaticky odstraní všechny prostředky z fondu považoval za poškozený. Pokračuje v monitorování instance v nedobrém stavu a přidá je zpět do fondu back-end v pořádku, jakmile budou k dispozici a reagují na sond stavu. Kromě používání výchozího stavu testu monitorování, můžete také přizpůsobit sondu stavu, aby odpovídala požadavkům vaší aplikace. Vlastní sondy vám umožní mít podrobnější kontrolu nad monitorování stavu. Pokud používáte vlastní sondy, můžete nakonfigurovat interval testu, adresu URL a cesty k testování a počet neúspěšných odpovědí tak, aby přijímal před instance back endového fondu je označen jako není v pořádku. Důrazně doporučujeme, abyste nakonfigurovali vlastní sondy pro sledování stavu jednotlivých back-endového fondu. Další informace najdete v tématu [monitorovat stav služby Application gateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-probe-overview).

## <a name="next-steps"></a>Další postup

Po získání informací o službě Application Gateway součásti, můžete:
* [Vytvoření služby Application Gateway na webu Azure Portal](quick-create-portal.md)
* [Vytvoření služby Application Gateway pomocí Azure Powershellu](quick-create-powershell.md)
* [Vytvoření služby Application Gateway pomocí Azure CLI](quick-create-cli.md)
