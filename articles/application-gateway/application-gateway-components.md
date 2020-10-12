---
title: Komponenty aplikační brány
description: Tento článek poskytuje informace o různých součástech v aplikační bráně.
services: application-gateway
author: surajmb
ms.service: application-gateway
ms.topic: conceptual
ms.date: 08/21/2020
ms.author: surmb
ms.openlocfilehash: ebd06b0b78ee511dce535ff4220df03087fb6906
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88723312"
---
# <a name="application-gateway-components"></a>Komponenty aplikační brány

 Aplikační brána slouží jako jeden kontaktní bod pro klienty. Distribuuje příchozí aplikační provoz do několika back-end fondů, mezi které patří virtuální počítače Azure, sady škálování virtuálních počítačů, Azure App Service a místní/externí servery. Pro distribuci provozu používá Aplikační brána několik součástí popsaných v tomto článku.

![Komponenty používané ve službě Application Gateway](./media/application-gateway-components/application-gateway-components.png)

## <a name="frontend-ip-addresses"></a>IP adresy front-endu

IP adresa front-end je IP adresa přidružená k aplikační bráně. Aplikační bránu můžete nakonfigurovat tak, aby měla veřejnou IP adresu, soukromou IP adresu nebo obojí. Aplikační brána podporuje jednu veřejnou nebo jednu privátní IP adresu. Vaše virtuální síť a veřejná IP adresa musí být ve stejném umístění jako brána Application Gateway. Po vytvoření se k naslouchacímu procesu přidruží IP adresa front-endu.

### <a name="static-versus-dynamic-public-ip-address"></a>Statická versus dynamická veřejná IP adresa

SKU Azure Application Gateway v2 je možné nakonfigurovat tak, aby podporovala buď statickou interní IP adresu, nebo statickou veřejnou IP adresu, nebo jenom statickou veřejnou IP adresu. Nedá se nakonfigurovat tak, aby podporovala pouze statickou interní IP adresu.

SKU v1 je možné nakonfigurovat tak, aby podporovala statickou nebo dynamickou interní IP adresu a dynamickou veřejnou IP adresu. Dynamická IP adresa Application Gateway se nemění na spuštěné bráně. Může se změnit jenom v případě, že bránu zastavíte nebo spustíte. Nemění se o selhání systému, aktualizacích, aktualizacích hostitelů Azure atd. 

Název DNS přidružený k aplikační bráně se v průběhu životního cyklu brány nezmění. V důsledku toho byste měli použít alias CNAME a nasměrovat ho na adresu DNS služby Application Gateway.

## <a name="listeners"></a>Naslouchací procesy

Naslouchací proces je logická entita, která kontroluje příchozí požadavky na připojení. Naslouchací proces přijme požadavek, pokud se protokol, port, název hostitele a IP adresa přidružené k žádosti shodují se stejnými prvky přidruženými ke konfiguraci naslouchacího procesu.

Předtím, než použijete Aplikační bránu, je nutné přidat alespoň jeden naslouchací proces. K aplikační bráně je možné připojit několik posluchačů, které je možné použít pro stejný protokol.

Jakmile naslouchací proces detekuje příchozí požadavky od klientů, služba Application Gateway tyto požadavky směruje na členy ve fondu back-end konfigurovaném v pravidle.

Naslouchací procesy podporují následující porty a protokoly.

### <a name="ports"></a>Porty

Port je místo, kde naslouchací proces naslouchá žádosti klienta. Pro SKU V1 a 1 až 65199 pro SKU v2 můžete nakonfigurovat porty od 1 do 65502.

### <a name="protocols"></a>Protokoly

Application Gateway podporuje čtyři protokoly: HTTP, HTTPS, HTTP/2 a WebSocket:
>[!NOTE]
>Podpora protokolu HTTP/2 je dostupná pro klienty připojující se pouze ke službě Application Gateway Listeners. Komunikace s fondy back-end serveru je vždycky přes HTTP/1.1. Ve výchozím nastavení je podpora HTTP/2 zakázaná. Můžete ji povolit.

- Zadejte mezi protokoly HTTP a HTTPS v konfiguraci naslouchacího procesu.
- Podpora [protokolů WebSockets a HTTP/2](features.md#websocket-and-http2-traffic) je poskytována nativně a [Podpora protokolu WebSocket](application-gateway-websocket.md) je ve výchozím nastavení povolena. Neexistuje žádné uživatelsky konfigurovatelné nastavení pro selektivní povolení nebo zakázání podpory protokolu WebSocket. Použijte objekty WebSockets s naslouchacími procesy HTTP i HTTPS.

Použijte naslouchací proces HTTPS pro ukončení TLS. Naslouchací proces HTTPS přesměruje šifrování a dešifrovací práci do vaší aplikační brány, takže vaše webové servery nebudou režie zatíženy.

### <a name="custom-error-pages"></a>Vlastní chybové stránky

Application Gateway umožňuje vytvářet vlastní chybové stránky místo zobrazení výchozích chybových stránek. U vlastní chybové stránky můžete použít vlastní branding a rozložení. Pokud se žádost nemůže připojit k back-endu, Application Gateway zobrazí vlastní chybovou stránku.

Další informace najdete v tématu [vlastní chybové stránky pro bránu Application Gateway](custom-error.md).

### <a name="types-of-listeners"></a>Typy naslouchacího procesu

Existují dva typy naslouchacího procesu:

- **Basic**. Tento typ naslouchacího procesu naslouchá pro jednu doménovou lokalitu, kde má jedno mapování DNS na IP adresu služby Application Gateway. Tato konfigurace naslouchacího procesu se vyžaduje, když hostete jednu lokalitu za aplikační bránou.

- **Více lokalit**. Tato konfigurace naslouchacího procesu se vyžaduje v případě, že chcete nakonfigurovat směrování na základě názvu hostitele nebo názvu domény pro více než jednu webovou aplikaci na stejné aplikační bráně. Díky možnosti přidat do jedné služby Application Gateway více než 100 webů můžete nakonfigurovat efektivnější topologii vašich nasazení. Každou stránku lze přesměrovat na vlastní back-endový fond. Příklad: na IP adresu služby Application Gateway odkazují tři domény – contoso.com, fabrikam.com a adatum.com. Vytvořili jste tři [naslouchací procesy pro více webů](multiple-site-overview.md) a nakonfigurujete každého naslouchacího procesu pro příslušný port a nastavení protokolu. 

    V naslouchacím procesu pro více webů můžete také definovat názvy hostitelů se zástupnými znaky a až 5 názvů hostitelů na naslouchací proces. Další informace najdete v tématu [názvy hostitelů se zástupnými znaky ve službě Listener (Preview)](multiple-site-overview.md#wildcard-host-names-in-listener-preview).

    Další informace o tom, jak nakonfigurovat naslouchací proces pro více webů, najdete v tématu [hostování více webů v Application Gateway pomocí Azure Portal](create-multiple-sites-portal.md).

Po vytvoření naslouchacího procesu ho přidružíte k pravidlu směrování požadavků. Toto pravidlo určuje, jak má být žádost o naslouchací službě směrována do back-endu. Pravidlo směrování požadavků taky obsahuje back-end fond, který se má směrovat, a nastavení HTTP, kde se zmiňuje port back-endu, protokol atd.

## <a name="request-routing-rules"></a>Vyžádání pravidel směrování

Pravidlo směrování požadavků je klíčovou součástí služby Application Gateway, protože určuje, jak směrovat provoz na naslouchací proces. Pravidlo váže naslouchací proces, fond back-end serverů a nastavení HTTP back-endu.

Když naslouchací proces přijme požadavek, pravidlo směrování požadavku přepošle požadavek do back-endu nebo ho přesměruje jinam. Pokud se požadavek přesměruje do back-endu, pravidlo směrování požadavků definuje, na který fond back-end serverů se má přesměrovat. Pravidlo směrování požadavků také určuje, zda mají být hlavičky v požadavku přepsány. Jeden naslouchací proces lze připojit k jednomu pravidlu.

Existují dva typy pravidel směrování žádostí:

- **Basic**. Všechny požadavky na přidruženém naslouchacího procesu (například blog.contoso.com/*) se předají do přidruženého back-endu s použitím přidruženého nastavení protokolu HTTP.

- **Na základě cesty**. Toto pravidlo směrování umožňuje směrovat požadavky na přiřazený naslouchací proces do konkrétního back-endu na základě adresy URL v žádosti. Pokud cesta adresy URL v požadavku odpovídá vzoru cesty v pravidle na základě cesty, pravidlo tyto požadavky směruje. Aplikuje vzor cesty pouze na cestu URL, nikoli na jeho parametry dotazu. Pokud cesta URL na žádost naslouchacího procesu neodpovídá žádné z pravidel založených na cestě, směruje požadavek do výchozího back-endu a nastavení protokolu HTTP.

Další informace najdete v tématu [směrování na základě adresy URL](url-route-overview.md).

### <a name="redirection-support"></a>Podpora přesměrování

Pravidlo směrování požadavků také umožňuje přesměrovat provoz na aplikační bránu. Toto je obecný mechanismus přesměrování, takže můžete přesměrovat na libovolný port, který definujete pomocí pravidel.

Můžete zvolit cíl přesměrování tak, aby byl jiný naslouchací proces (což může pomáhat povolit přesměrování automatického přesměrování protokolu HTTP na HTTPS) nebo externí Web. Můžete také zvolit, že má být přesměrování dočasná nebo trvalá, nebo připojit cestu URI a řetězec dotazu k přesměrované adrese URL.

Další informace najdete v tématu [přesměrování provozu ve službě Application Gateway](redirect-overview.md).

### <a name="rewrite-http-headers-and-url"></a>Přepsání hlaviček HTTP a adres URL

Pomocí pravidel pro přepsání můžete přidat, odebrat nebo aktualizovat žádosti a hlavičky odpovědí HTTP (S) a také cestu URL a parametry řetězce dotazu, protože pakety požadavků a odpovědí se pohybují mezi klienty klienta a back-endu přes Aplikační bránu.

Parametry hlaviček a adres URL lze nastavit na statické hodnoty nebo na jiné hlavičky a proměnné serveru. To pomáhá s důležitými případy použití, jako je například extrakce IP adres klientů, odebrání citlivých informací o back-endu, přidání dalších zabezpečení atd.

Další informace najdete v tématu [přepis hlaviček protokolu HTTP a adresy URL ve službě Application Gateway](rewrite-http-headers-url.md).

## <a name="http-settings"></a>Nastavení HTTP

Služba Application Gateway směruje provoz na servery back-end (zadané v pravidle směrování požadavků, které zahrnuje nastavení HTTP) pomocí čísla portu, protokolu a dalších nastavení popsaných v této součásti.

Port a protokol, který se používá v nastavení HTTP, určuje, jestli je přenos mezi aplikační bránou a back-end servery šifrovaný (poskytuje kompletní protokol TLS) nebo není šifrovaný.

Tato součást se používá také k těmto akcím:

- Určete, jestli má být uživatelská relace na stejném serveru zachovaná pomocí [přidružení relace na základě souborů cookie](features.md#session-affinity).

- Řádně odeberte členy back-end fondu pomocí [vyprazdňování připojení](features.md#connection-draining).

- Přidružte vlastní test, který monitoruje stav back-endu, nastavte interval časového limitu požadavku, přepište název hostitele a cestu v žádosti a poskytněte možnosti snadného kliknutí, abyste určili nastavení pro App Service back-endu.

## <a name="backend-pools"></a>Back-endové fondy

Back-end fond směruje požadavky na back-endové servery, které slouží k žádosti. Back-endové fondy můžou obsahovat:

- Síťové karty
- Škálovací sady virtuálních počítačů
- Veřejné IP adresy
- Interní IP adresy
- FQDN
- Back-endy víceklientské architektury (například App Service)

Application Gateway členové fondu back-endu nejsou vázáni na skupinu dostupnosti. Aplikační brána může komunikovat s instancemi mimo virtuální síť, ve které je. Výsledkem je, že členové fondů back-endu můžou být napříč clustery, v datových centrech nebo mimo Azure, pokud se jedná o připojení IP.

Pokud jako členy fondu back-end používáte interní IP adresy, musíte použít [partnerský vztah virtuální sítě](../virtual-network/virtual-network-peering-overview.md) nebo [bránu VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md). Vytváření partnerských vztahů virtuálních sítí je podporované a je výhodné pro provoz vyrovnávání zatížení v jiných virtuálních sítích.

Aplikační brána taky může komunikovat s místními servery, pokud jsou připojené přes Azure ExpressRoute nebo tunely VPN, pokud je povolený provoz.

Můžete vytvořit různé back-endové fondy pro různé typy požadavků. Můžete například vytvořit jeden back-end fond pro obecné požadavky a potom další back-end fond pro požadavky na mikroslužby pro vaši aplikaci.

## <a name="health-probes"></a>Sondy stavu

Ve výchozím nastavení brána Application Gateway monitoruje stav všech prostředků v back-endu fondu a automaticky odebere chybné. Pak monitoruje poškozené instance a přidává je zpátky do back-endového fondu, jakmile budou k dispozici a reagují na sondy stavu.

Kromě používání výchozího monitorování sondy stavu můžete také přizpůsobit sondu stavu tak, aby vyhovovala požadavkům vaší aplikace. Vlastní testy umožňují podrobnější kontrolu nad monitorováním stavu. Pokud používáte vlastní testy, můžete nakonfigurovat vlastní název hostitele, cestu k adrese URL, interval testu a počet neúspěšných odpovědí, které se mají přijmout před označením instance back-endu jako chybné, vlastních stavových kódů a shody textu odpovědí atd. Doporučujeme nakonfigurovat vlastní testy, abyste mohli monitorovat stav jednotlivých back-end fondů.

Další informace najdete v tématu [monitorování stavu služby Application Gateway](../application-gateway/application-gateway-probe-overview.md).

## <a name="next-steps"></a>Další kroky

Vytvoření aplikační brány:

* [Na webu Azure Portal](quick-create-portal.md)
* [Pomocí Azure PowerShell](quick-create-powershell.md)
* [Pomocí Azure CLI](quick-create-cli.md)
