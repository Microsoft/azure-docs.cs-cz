---
title: Součásti aplikace brány
description: Tento článek obsahuje informace o různých komponentách ve službě application gateway
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 02/20/2019
ms.author: absha
ms.openlocfilehash: f5dfa34760bcef23bf54d65b35e3ad8f48cc2ee5
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "60831826"
---
# <a name="application-gateway-components"></a>Součásti aplikace brány

 Službu application gateway slouží jako jediný bod kontaktu pro klienty. U více fondů back-endu, mezi které patří virtuální počítače Azure, škálovací sady virtuálních počítačů, služby Azure App Service a na místní nebo externí servery ji distribuuje příchozí aplikační provoz. Aplikační brány pro distribuci provozu, používá několik komponent popsaných v tomto článku.

![Komponenty používané ve službě application gateway](./media/application-gateway-components/application-gateway-components.png)

## <a name="frontend-ip-addresses"></a>Front-endové IP adresy

IP adresa front-endu je IP adresa spojená s aplikační bránou. Můžete nakonfigurovat službu application gateway s veřejnou IP adresu, privátní IP adresu nebo obojí. Službu application gateway podporuje veřejné nebo privátní IP adresu. Virtuální síť a veřejnou IP adresu musí být ve stejném umístění jako vaše brána application gateway. Po jeho vytvoření, je přidružený naslouchací proces IP adresa front-endu.

### <a name="static-versus-dynamic-public-ip-address"></a>Statické a dynamické veřejné IP adresy

SKU v2 Azure Application Gateway podporuje obě statické interní a statické veřejné IP adresy, i když v1 SKU podporuje pouze statické interní IP adresy. Pokud po zastavení a spuštění služby application gateway můžete změnit virtuální adresy IP (VIP).

Název DNS, který je přidružený k službě application gateway nemění přes životní cyklus brány. V důsledku toho by měl použít CNAME alias a přejděte na adresu DNS služby application gateway.

## <a name="listeners"></a>Naslouchací procesy

Naslouchací proces je logická entita, která kontroluje příchozí požadavky na připojení. Naslouchací proces přijme žádost, pokud protokol, port, hostitele a IP adresy přidružené k požadavku odpovídá stejné prvky přidružené k konfiguraci naslouchacího procesu.

Než použijete službu application gateway, je nutné přidat alespoň jeden naslouchací proces. Může existovat několik naslouchacích procesů, které jsou připojené k službě application gateway a mohou být použity pro stejný protokol.

Po naslouchací proces detekuje příchozí žádosti od klientů, službu application gateway směrovat tyto žádosti na členy v back-endový fond. Application gateway používá pravidla směrování požadavku definované pro naslouchací proces, který obdržel příchozího požadavku.

Naslouchací procesy podporují následující porty a protokoly.

### <a name="ports"></a>Porty

Port je, ve kterém naslouchá naslouchací proces pro požadavek klienta. Můžete nakonfigurovat porty od 1 do 65502 pro v1 SKU a 1 pro 65199 pro v2 SKU.

### <a name="protocols"></a>Protokoly

Služba Application Gateway podporuje čtyři protokoly: HTTP, HTTPS, HTTP/2 a objektu websocket na straně:

- Zadejte protokol HTTP nebo HTTPS protokolů v konfiguraci naslouchacího procesu.
- Podpora pro [protokoly Websocket a HTTP/2](https://docs.microsoft.com/azure/application-gateway/overview#websocket-and-http2-traffic) je k dispozici nativně, a [podpora protokolu WebSocket](https://docs.microsoft.com/azure/application-gateway/application-gateway-websocket) je ve výchozím nastavení povolené. Neexistuje žádné uživatelsky konfigurovatelné nastavení pro selektivní povolení nebo zakázání podpory protokolu WebSocket. Protokoly Websocket pomocí naslouchacích procesů HTTP i HTTPS.
- Podpora protokolu HTTP/2 je k dispozici pro připojení klientů k application gateway pouze posluchače. Komunikace do fondů back-end serveru je přes HTTP/1.1. Ve výchozím nastavení je zakázána podpora HTTP/2. Můžete ji povolit.

Pro ukončení protokolu SSL pomocí naslouchací proces HTTPS. Naslouchací proces HTTPS eliminuje nutnost šifrování a dešifrování pro vaši bránu application gateway, aby nevznikaly webové servery pomocí režijní náklady. Vaše aplikace jsou pak můžete soustředit na obchodní logiku.

### <a name="custom-error-pages"></a>Vlastní chybové stránky

Služba Application Gateway umožňuje vytvořit vlastní chybové stránky místo výchozí chybové stránky. U vlastní chybové stránky můžete použít vlastní značky a rozložení. Služba Application Gateway zobrazí vlastní chybovou stránku, když požadavek nemá přístup na back-endu.

Další informace najdete v tématu [vlastní chybové stránky pro vaši bránu application gateway](https://docs.microsoft.com/azure/application-gateway/custom-error).

### <a name="types-of-listeners"></a>Typy naslouchacích procesů

Existují dva typy naslouchacích procesů:

- **Základní**. Tento typ naslouchací proces naslouchá na jednu doménu web, kde má jednoho mapování DNS na IP adresu služby application gateway. Tato konfigurace naslouchacího procesu je nutná, pokud je hostitelem jedné lokality za službou application gateway.

- **Multi-Site**. Tato konfigurace naslouchacího procesu je nutná, pokud nakonfigurujete více než jednu webovou aplikaci na stejné instance služby application gateway. Umožňuje nakonfigurovat efektivnější topologii vašich nasazení tak, že přidáte až 100 webů do jedné služby application gateway. Každou stránku lze přesměrovat na vlastní back-endový fond. Například tři subdomény, abc.contoso.com, xyz.contoso.com a pqr.contoso.com, bod na IP adresu služby application gateway. By vytvoříte tři Multi-Site naslouchacích procesů a nakonfigurujete každý naslouchací proces pro příslušný port a nastavení protokolu.

    Další informace najdete v tématu [hostování více webů](https://docs.microsoft.com/azure/application-gateway/application-gateway-web-app-overview).

Po vytvoření naslouchacího procesu, přiřaďte ji k pravidlo směrování požadavku. Toto pravidlo určuje, jak se mají směrovat žádosti o přijetí na naslouchací proces back-endu.

Služba Application Gateway zpracovává naslouchacích procesů v uvedeném pořadí. Pokud základní naslouchací proces odpovídá příchozí žádosti, to je zpracován jako první. Směrování provozu do správného back-endu, nakonfigurujte naslouchací proces více webů před základní naslouchací proces.

## <a name="request-routing-rules"></a>Pravidla směrování žádostí

Pravidlo směrování požadavku je klíčovou součástí služby application gateway, protože určuje, jak směrovat provoz na naslouchací proces. Pravidlo váže naslouchací proces, fondu back-end serverů a nastavení HTTP back-endu.

Pokud naslouchací proces přijme požadavek, pravidlo směrování požadavku předá požadavek back-endu nebo přesměruje jinde. Pokud předá požadavek back-endu, definuje pravidlo směrování žádostí kterému fondu serverů back-endu do předat dál. Navíc pravidlo směrování požadavku také určuje, zda záhlaví v žádosti, aby se povolilo. Jeden naslouchací proces lze připojit k jedno pravidlo.

Existují dva typy pravidel směrování žádosti:

- **Základní**. Všechny žádosti na přidružený naslouchací proces (například blog.contoso.com/*) jsou předány přidružené back-endový fond s použitím přidružené nastavení protokolu HTTP.

- **Na základě cest**. Toto pravidlo směrování umožňuje směrovat žádosti na přidružený naslouchací proces pro konkrétní back-endový fond, na základě adresy URL v požadavku. Pokud cesta adresy URL v požadavku odpovídá vzor cesty v pravidle na základě cest, pravidlo směruje žádosti. Vzor cesty se vztahuje pouze na cestě adresy URL, nikoli na jeho parametry dotazu. Pokud cestu adresy URL na naslouchací proces žádost neodpovídá žádné z pravidel na základě cest, přesměruje požadavek na výchozí back-endového fondu a nastavení HTTP.

Další informace najdete v tématu [směrování na základě adresy URL](https://docs.microsoft.com/azure/application-gateway/url-route-overview).

### <a name="redirection-support"></a>Podpora přesměrování

Pravidlo směrování požadavku také umožňuje přesměrování provozu ve službě application gateway. Toto je obecný přesměrování mechanismus, tak můžete přesměrovat na a z jakéhokoli portu můžete definovat pomocí pravidel.

Můžete zvolit cíl přesměrování na jiný naslouchací proces (který může pomoct, povolte automatické HTTP do HTTPS přesměrování) nebo externí web. Můžete také pro přesměrování být trvalé nebo dočasné nebo přesměrované adresu URL připojit řetězec identifikátoru URI cestu a dotaz.

Další informace najdete v tématu [přesměrování provozu ve službě application gateway](https://docs.microsoft.com/azure/application-gateway/redirect-overview).

### <a name="rewrite-http-headers"></a>Přepsání hlaviček HTTP

Pomocí pravidel směrování žádosti můžete přidat, odebrat nebo aktualizovat požadavku HTTP (S) a hlavičky odpovědi jako pakety žádostí a odpovědí přesunutí mezi klientem a back-endových fondů přes application gateway.

Záhlaví lze nastavit na statické hodnoty nebo na jiné záhlaví a proměnných serveru. To pomáhá pomocí důležité případy, jako je například extrahování klientských IP adres, odebrání citlivých informací o back-endu, přidání další zabezpečení a tak dále.

Další informace najdete v tématu [hlavičky HTTP přepsat na vaše brána application gateway](https://docs.microsoft.com/azure/application-gateway/rewrite-http-headers).

## <a name="http-settings"></a>Nastavení HTTP

Službu application gateway směruje provoz do back-end serverů (zadané v pravidle směrování žádosti, které zahrnují nastavení HTTP) s použitím čísla portu, protokolu a další nastavení podrobně popsané v této součásti.

Port a protokol použitý v nastavení HTTP zjistit, zda je přenos dat mezi servery brány a back-endu aplikace zašifrovány (poskytuje-kompletního protokolu SSL) nebo bez šifrování.

Tato součást taky umožňuje:

- Určení, zda je relace uživatele uchovávat na stejném serveru s použitím [spřažení relace na základě souborů cookie](https://docs.microsoft.com/azure/application-gateway/overview#session-affinity).

- Řádně odeberte členy fondu back-end pomocí [vyprázdnění připojení](https://docs.microsoft.com/azure/application-gateway/overview#connection-draining).

- Přidružení vlastní test paměti monitorování stavu back-endu, nastavit interval časového limitu žádosti, přepsat názvu hostitele a cestu v požadavku a zajištění snadného jedním kliknutím k zadání nastavení pro back-endu služby App Service.

## <a name="backend-pools"></a>Back-endové fondy

Back-endový fond přesměruje požadavek na back-end serverů, které požadavek vyřídit. Může obsahovat back-endové fondy:

- Síťové karty
- Škálovací sady virtuálních počítačů
- Veřejné IP adresy
- Interní IP adresy
- PLNĚ KVALIFIKOVANÝ NÁZEV DOMÉNY
- Víceklientské back-EndY (jako je App Service)

Členy fondu back-end brány aplikace nejsou vázané na skupinu dostupnosti. Službu application gateway může komunikovat s instancí mimo virtuální síť, která je v. V důsledku toho členy fondu back-end může být napříč clustery, napříč několika datacentry nebo mimo Azure, jako je připojení pomocí IP adresy.

Pokud používáte interní IP adresy jako členy fondu back-end, je nutné použít [partnerský vztah virtuální sítě](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) nebo [VPN gateway](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways). Partnerský vztah virtuální sítě je podporované a přínosné pro vyrovnávání zatížení provozu v jiných virtuálních sítí.

Službu application gateway může také komunikovat s k na místních serverech při připojení pomocí Azure ExpressRoute nebo VPN tunely Pokud provoz je povolený.

Můžete vytvořit jiný back-endové fondy pro různé typy požadavků. Například vytvořte jeden back-endového fondu pro obecné požadavky a pak opět back-endový fond pro požadavky na mikroslužby pro vaši aplikaci.

## <a name="health-probes"></a>Sondy stavu

Ve výchozím nastavení služby application gateway monitoruje stav všech prostředků v back-endového fondu a automaticky odebere z nich není v pořádku. Potom monitoruje instance v nedobrém stavu a přidá je zpět do fondu back-end v pořádku, když jsou k dispozici a reagují na sondy stavu.

Kromě používání výchozího stavu testu monitorování, můžete také přizpůsobit sondu stavu, aby odpovídala požadavkům vaší aplikace. Vlastní sondy povolit podrobnější kontrolu nad monitorování stavu. Pokud používáte vlastní sondy, můžete nakonfigurovat interval testu, adresu URL a cesty k testování a počet neúspěšných odpovědí tak, aby přijímal před instance back-endového fondu je označen jako není v pořádku. Doporučujeme, abyste nakonfigurovali vlastní sondy pro monitorování stavu jednotlivých back-endový fond.

Další informace najdete v tématu [monitorování stavu služby application gateway](https://docs.microsoft.com/azure/application-gateway/application-gateway-probe-overview).

## <a name="next-steps"></a>Další postup

Vytvoření služby application gateway:

* [Na webu Azure Portal](quick-create-portal.md)
* [Pomocí Azure Powershellu](quick-create-powershell.md)
* [Pomocí rozhraní příkazového řádku Azure](quick-create-cli.md)
