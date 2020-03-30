---
title: Součásti aplikační brány
description: Tento článek obsahuje informace o různých součástech v bráně aplikace
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: article
ms.date: 02/20/2019
ms.author: absha
ms.openlocfilehash: 90b3c3fd18bc9211c731ccf16dd646a64a4a1116
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80133091"
---
# <a name="application-gateway-components"></a>Součásti aplikační brány

 Aplikační brána slouží jako jediné kontaktní místo pro klienty. Distribuuje příchozí provoz aplikací napříč několika fondy back-endu, které zahrnují virtuální počítače Azure, škálovací sady virtuálních strojů, službu Azure App Service a místní/externí servery. K distribuci provozu používá aplikační brána několik součástí popsaných v tomto článku.

![Součásti používané v aplikační bráně](./media/application-gateway-components/application-gateway-components.png)

## <a name="frontend-ip-addresses"></a>Adresy IP front-end

Front-endová IP adresa je IP adresa přidružená k aplikační bráně. Můžete nakonfigurovat aplikační bránu tak, aby měla veřejnou IP adresu, privátní IP adresu nebo obojí. Aplikační brána podporuje jednu veřejnou nebo jednu privátní IP adresu. Vaše virtuální síť a veřejná IP adresa musí být ve stejném umístění jako brána aplikace. Po vytvoření je front-endová IP adresa přidružena k naslouchací proces.

### <a name="static-versus-dynamic-public-ip-address"></a>Statická versus dynamická veřejná IP adresa

SKU aplikační brány Azure V2 lze nakonfigurovat tak, aby podporovala buď statickou interní IP adresu, tak statickou veřejnou IP adresu, nebo jenom statickou veřejnou IP adresu. Nelze jej nakonfigurovat tak, aby podporoval pouze statickou interní adresu IP.

Skladovou položku V1 lze nakonfigurovat tak, aby podporovala statickou nebo dynamickou interní IP adresu a dynamickou veřejnou IP adresu. Dynamická IP adresa aplikační brány se na spuštěné bráně nezmění. Může se změnit pouze v případě, že bránu zastavíte nebo spustíte. Nemění se na selhání systému, aktualizace, aktualizace hostitele Azure atd. 

Název DNS přidružený k aplikační bráně se v průběhu životního cyklu brány nemění. V důsledku toho byste měli použít alias CNAME a nasměrovat ho na adresu DNS aplikační brány.

## <a name="listeners"></a>Naslouchací procesy

Naslouchací proces je logická entita, která kontroluje příchozí požadavky na připojení. Naslouchací proces přijme požadavek, pokud protokol, port, název hostitele a adresa IP přidružená k požadavku odpovídají stejným prvkům přidruženým ke konfiguraci posluchače.

Před použitím brány aplikace je nutné přidat alespoň jeden naslouchací proces. K aplikační bráně může být připojeno více naslouchacích procesů a lze je použít pro stejný protokol.

Poté, co naslouchací proces zjistí příchozí požadavky od klientů, brána aplikace směruje tyto požadavky na členy v back-endovém fondu nakonfigurovaném v pravidle.

Naslouchací procesy podporují následující porty a protokoly.

### <a name="ports"></a>Porty

Port je místo, kde naslouchací proces naslouchá požadavku klienta. Můžete nakonfigurovat porty v rozsahu od 1 do 65502 pro sku v1 a 1 do 65199 pro skladovou položku v2.

### <a name="protocols"></a>Protokoly

Aplikační brána podporuje čtyři protokoly: HTTP, HTTPS, HTTP/2 a WebSocket:
>[!NOTE]
>Podpora protokolu HTTP/2 je k dispozici pouze klientům, kteří se připojují k naslouchacím procesům aplikační brány. Komunikace s fondy back-endového serveru je vždy přes HTTP/1.1. Ve výchozím nastavení je podpora http/2 zakázána. Můžete ji povolit.

- Zadejte mezi protokoly HTTP a HTTPS v konfiguraci naslouchacího procesu.
- Podpora [pro protokoly WebSockets a HTTP/2](features.md#websocket-and-http2-traffic) je poskytována nativně a [podpora WebSocket](application-gateway-websocket.md) je ve výchozím nastavení povolena. Neexistuje žádné uživatelsky konfigurovatelné nastavení pro selektivní povolení nebo zakázání podpory protokolu WebSocket. Použijte WebSockets s naslouchacími procesy HTTP i HTTPS.

Použijte naslouchací proces HTTPS pro ukončení TLS. Naslouchací proces HTTPS přečte práci šifrování a dešifrování do vaší aplikační brány, takže vaše webové servery nejsou zatíženy režií.

### <a name="custom-error-pages"></a>Stránky vlastních chyb

Aplikace Gateway umožňuje vytvářet vlastní chybové stránky namísto zobrazení výchozích chybových stránek. U vlastní chybové stránky můžete použít vlastní značky a rozložení. Brána aplikace zobrazí vlastní chybovou stránku, když se požadavek nemůže dostat do back-endu.

Další informace naleznete [v tématu Vlastní chybové stránky pro bránu aplikace](custom-error.md).

### <a name="types-of-listeners"></a>Typy posluchačů

Existují dva typy posluchačů:

- **Základní**. Tento typ naslouchacího procesu naslouchá jedné lokalitě domény, kde má jediné mapování DNS na IP adresu aplikační brány. Tato konfigurace naslouchací proces je vyžadována, pokud hostujete jednu lokalitu za aplikační bránou.

- **Více míst**. Tato konfigurace naslouchací proces je vyžadována, pokud nakonfigurujete více než jednu webovou aplikaci ve stejné instanci aplikační brány. Umožňuje nakonfigurovat efektivnější topologii pro vaše nasazení přidáním až 100 webů do jedné aplikační brány. Každou stránku lze přesměrovat na vlastní back-endový fond. Například tři subdomény, abc.contoso.com, xyz.contoso.com a pqr.contoso.com, odkazují na IP adresu aplikační brány. Vytvořili byste tři naslouchací procesy s více sítěmi a nakonfigurovali každý naslouchací proces pro příslušné nastavení portu a protokolu.

    Další informace naleznete [v tématu Hosting na více webech](application-gateway-web-app-overview.md).

Po vytvoření naslouchací proces, přidružení k pravidlu směrování požadavku. Toto pravidlo určuje, jak by měl být požadavek přijatý na naslouchací proces směrován do back-endu.

Aplikace Gateway zpracovává posluchače v [uvedeném pořadí](configuration-overview.md#order-of-processing-listeners).

## <a name="request-routing-rules"></a>Požádat o pravidla směrování

Pravidlo směrování požadavku je klíčovou součástí aplikační brány, protože určuje způsob směrování provozu na naslouchací proces. Pravidlo váže naslouchací proces, fond serveru back-end a nastavení http back-endu.

Když naslouchací proces přijme požadavek, pravidlo směrování požadavku předá požadavek back-endu nebo jej přesměruje jinam. Pokud je požadavek předán back-endu, pravidlo směrování požadavku definuje, do kterého back-endového fondu serveru se má předávat. Pravidlo směrování požadavku také určuje, zda mají být hlavičky v požadavku přepsány. Jeden naslouchací proces lze připojit k jednomu pravidlu.

Existují dva typy pravidel směrování požadavků:

- **Základní**. Všechny požadavky na přidružené naslouchací proces (například blog.contoso.com/*) jsou předány do přidruženého back-endového fondu pomocí přidruženého nastavení HTTP.

- **Na základě cesty**. Toto pravidlo směrování umožňuje směrovat požadavky na přidružený naslouchací proces do konkrétního back-endového fondu na základě adresy URL v požadavku. Pokud cesta adresy URL v požadavku odpovídá vzoru cesty v pravidle založeném na cestě, pravidlo tento požadavek směruje. Použije vzorek cesty pouze na cestu URL, nikoli na parametry dotazu. Pokud cesta URL v požadavku na naslouchací proces neodpovídá žádnému z pravidel založených na cestě, směruje požadavek do výchozího back-endového fondu a nastavení HTTP.

Další informace naleznete [v tématu směrování založené na adrese URL](url-route-overview.md).

### <a name="redirection-support"></a>Podpora přesměrování

Pravidlo směrování požadavků také umožňuje přesměrovat provoz na aplikační bráně. Toto je obecný mechanismus přesměrování, takže můžete přesměrovat na a z libovolného portu, který definujete pomocí pravidel.

Cíl přesměrování můžete zvolit jako jiný naslouchací proces (který může pomoci povolit automatické přesměrování HTTP na HTTPS) nebo externí web. Můžete také zvolit, aby přesměrování bylo dočasné nebo trvalé, nebo připojit cestu URI a řetězec dotazu k přesměrované adrese URL.

Další informace naleznete v [tématu Přesměrování provozu na aplikační bráně](redirect-overview.md).

### <a name="rewrite-http-headers"></a>Přepsání hlaviček HTTP

Pomocí pravidel směrování požadavků můžete přidávat, odebírat nebo aktualizovat hlavičky požadavků a odpovědí HTTP(S), protože pakety požadavků a odpovědí se přesouvají mezi klientem a back-endovými fondy prostřednictvím aplikační brány.

Záhlaví lze nastavit na statické hodnoty nebo na jiné hlavičky a proměnné serveru. To pomáhá s důležitými případy použití, jako je například extrahování ip adres klienta, odebrání citlivých informací o back-endu, přidání většího zabezpečení a tak dále.

Další informace naleznete [v tématu Přepis záhlaví protokolu HTTP na aplikační bráně](rewrite-http-headers.md).

## <a name="http-settings"></a>Nastavení HTTP

Aplikační brána směruje provoz na servery back-end (zadaná v pravidle směrování požadavků, která obsahují nastavení protokolu HTTP) pomocí čísla portu, protokolu a dalších nastavení podrobně popsaných v této součásti.

Port a protokol použitý v nastavení protokolu HTTP určují, zda je provoz mezi aplikační bránou a servery back-end šifrovaný (poskytující komplexní protokol TLS) nebo nešifrovaný.

Tato součást se také používá k:

- Pomocí [spřažení relací založených na souborech cookie](features.md#session-affinity)určete, zda má být relace uživatele uchovávána na stejném serveru .

- Řádně odebrat členy back-endfondu pomocí [připojení vyprázdnění](features.md#connection-draining).

- Přidružte vlastní sondu ke sledování stavu back-endu, nastavte časový interval požadavku, přepište název hostitele a cestu v požadavku a poskytněte snadnost jedním kliknutím pro určení nastavení back-endu služby App Service.

## <a name="backend-pools"></a>Back-endové fondy

Back-endový fond směruje požadavek na back-endové servery, které tento požadavek obsluhují. Back-endové fondy mohou obsahovat:

- Síťové karty
- Škálovací sady virtuálních počítačů
- Veřejné IP adresy
- Interní IP adresy
- FQDN
- Víceklientské back-endy (například Služba aplikací)

Členové back-endu aplikační brány nejsou vázáni na sadu dostupnosti. Aplikační brána může komunikovat s instancemi mimo virtuální síť, ve které se nachází. V důsledku toho mohou být členové back-endových fondů napříč clustery, napříč datovými centry nebo mimo Azure, pokud je připojení IP.

Pokud používáte interní IP adresy jako členy back-endu fondu, musíte použít [partnerský vztah virtuální sítě](../virtual-network/virtual-network-peering-overview.md) nebo [bránu VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md). Partnerský vztah virtuální sítě je podporovaný a výhodný pro vyrovnávání zatížení v jiných virtuálních sítích.

Aplikační brána může komunikovat s místními servery, když jsou připojené tunely Azure ExpressRoute nebo VPN, pokud je povolen provoz.

Můžete vytvořit různé back-endové fondy pro různé typy požadavků. Například vytvořte jeden back-endový fond pro obecné požadavky a pak jiný back-endový fond pro požadavky na mikroslužby pro vaši aplikaci.

## <a name="health-probes"></a>Sondy stavu

Ve výchozím nastavení aplikace brána monitoruje stav všech prostředků v back-endovém fondu a automaticky odebere ty není v pořádku. Potom monitoruje instance není v pořádku a přidá je zpět do fondu back-endu v pořádku, jakmile budou k dispozici a reagovat na sondy stavu.

Kromě použití výchozího monitorování sondy stavu můžete také přizpůsobit sondu stavu tak, aby vyhovovala požadavkům vaší aplikace. Vlastní sondy umožňují podrobnější kontrolu nad monitorování stavu. Při použití vlastních sond můžete nakonfigurovat interval sondy, adresu URL a cestu k testování a počet neúspěšných odpovědí, které mají být přijaty před tím, než je instance back-endového fondu označena jako nefunkční. Doporučujeme nakonfigurovat vlastní sondy pro sledování stavu každého back-endového fondu.

Další informace naleznete [v tématu Sledování stavu brány aplikace](../application-gateway/application-gateway-probe-overview.md).

## <a name="next-steps"></a>Další kroky

Vytvoření aplikační brány:

* [Na webu Azure Portal](quick-create-portal.md)
* [Pomocí Azure PowerShellu](quick-create-powershell.md)
* [Pomocí azure cli](quick-create-cli.md)
