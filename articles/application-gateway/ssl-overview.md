---
title: Povolení koncového šifrování TLS v Azure Application Gateway
description: Tento článek představuje přehled Application Gateway kompletní podpora protokolu TLS.
services: application-gateway
author: surajmb
ms.service: application-gateway
ms.topic: conceptual
ms.date: 08/21/2020
ms.author: victorh
ms.openlocfilehash: c39401289ffc6f27c292168adaa15c5163a3967b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "96001282"
---
# <a name="overview-of-tls-termination-and-end-to-end-tls-with-application-gateway"></a>Přehled ukončení protokolu TLS a koncového a koncového protokolu TLS s Application Gateway

Protokol TLS (Transport Layer Security), dříve označovaný jako SSL (Secure Sockets Layer) (SSL), je standardní technologie zabezpečení pro vytváření šifrovaného propojení mezi webovým serverem a prohlížečem. Tento odkaz zajistí, že všechna data předaná mezi webovým serverem a prohlížečem zůstanou soukromá a šifrovaná. Aplikační brána podporuje ukončení protokolu TLS v bráně i koncové šifrování TLS.

## <a name="tls-termination"></a>Ukončení protokolu TLS

Application Gateway podporuje ukončení protokolu TLS v bráně, po kterém provoz obvykle přechází na back-end servery jako nešifrované. Ukončení protokolu TLS ve službě Application Gateway má několik výhod:

- **Vylepšený výkon** – největší dosažený výkon při dešifrování TLS je počáteční metodou handshake. Aby se zlepšil výkon, server, který provádí dešifrování, ukládá ID relací TLS a spravuje lístky relace TLS. Pokud se tato služba používá u služby Application Gateway, můžou všechny požadavky ze stejného klienta používat hodnoty uložené v mezipaměti. Pokud se to provede na back-end serverech, pak pokaždé, když požadavky klienta přejdou na jiný server, musí se klient znovu ověřit. Použití lístků protokolu TLS může přispět k zmírnění tohoto problému, ale není podporováno všemi klienty a může být obtížné ho nakonfigurovat a spravovat.
- **Lepší využití back-end serverů** – zpracování SSL/TLS je velmi náročné na procesor a výrazně se zvyšuje i při zvýšení velikosti klíče. Odebráním této práce ze serverů back-end se jim umožní soustředit se na to, co je pro vás nejúčinnější, a poskytovat obsah.
- **Inteligentní směrování** – dešifrováním provozu má Aplikační brána přístup k obsahu požadavku, jako jsou HLAVIČKY, URI a tak dále, a může tato data použít k směrování požadavků.
- **Správa certifikátů** – certifikáty je potřeba koupit a nainstalovat jenom na aplikační bránu, ne na všechny back-endové servery. Tím ušetříte čas i peníze.

Chcete-li nakonfigurovat ukončení protokolu TLS, je nutné přidat certifikát TLS/SSL do naslouchacího procesu, aby Application Gateway mohl odvodit symetrický klíč jako podle specifikace protokolu TLS/SSL. Symetrický klíč se pak použije k šifrování a dešifrování provozu odeslaného do brány. Certifikát TLS/SSL musí být ve formátu PFX (Personal Information Exchange). Tento formát souboru umožňuje exportovat privátní klíč, který služba Application Gateway vyžaduje k provádění šifrování a dešifrování provozu.

> [!IMPORTANT] 
> Certifikát na naslouchací službě vyžaduje nahrání celého řetězce certifikátů (kořenový certifikát od certifikační autority, zprostředkujících a listových certifikátů) k vytvoření řetězu důvěryhodnosti. 


> [!NOTE] 
>
> Application Gateway neposkytuje žádnou možnost vytvořit nový certifikát nebo odeslat žádost o certifikát certifikační autoritě.

Aby připojení TLS fungovalo, musíte zajistit, aby certifikát TLS/SSL splňoval tyto podmínky:

- Aktuální datum a čas spadá do rozsahu dat "platný od" a "platné do" na certifikátu.
- Běžný název certifikátu (CN) odpovídá hlavičce hostitele v požadavku. Například pokud klient posílá požadavek na `https://www.contoso.com/`, musí být CN `www.contoso.com`.

### <a name="certificates-supported-for-tls-termination"></a>Certifikáty podporované pro ukončení TLS

Application Gateway podporuje následující typy certifikátů:

- Certifikát CA (certifikační autorita): certifikát certifikační autority je digitální certifikát vydaný certifikační autoritou (CA).
- Certifikát EV (rozšířené ověřování): certifikát proev je certifikát, který vyhovuje běžným pokynům pro certifikáty v oboru. Tím se změní i panel lokátoru prohlížeče na zelenou a bude také publikován název společnosti.
- Certifikát se zástupnými znaky: Tento certifikát podporuje libovolný počet subdomén na základě *. site.com, kde by vaše subdoména nahradila *. Ale nepodporuje site.com, takže pokud uživatelé přistupují k webu, aniž by museli psát úvodní "www", certifikát se zástupnými znaky nebude pokrývat.
- Certifikáty Self-Signed: klientské prohlížeče nedůvěřují těmto certifikátům a upozorní uživatele, že certifikát virtuální služby není součástí řetězce důvěryhodnosti. Certifikáty podepsané svým držitelem jsou vhodné pro testování nebo prostředí, kde správci kontrolují klienty a můžou bezpečně obejít výstrahy zabezpečení v prohlížeči. Provozní úlohy by nikdy neměly používat certifikáty podepsané svým držitelem.

Další informace najdete v tématu [Konfigurace ukončení TLS pomocí služby Application Gateway](./create-ssl-portal.md).

### <a name="size-of-the-certificate"></a>Velikost certifikátu
V části [omezení Application Gateway](../azure-resource-manager/management/azure-subscription-service-limits.md#application-gateway-limits) můžete zjistit, že je podporovaná maximální velikost certifikátu TLS/SSL.

## <a name="end-to-end-tls-encryption"></a>Komplexní šifrování TLS

Možná nebudete chtít nešifrovaná komunikace se servery back-end. Je možné, že máte požadavky na zabezpečení, požadavky na dodržování předpisů nebo aplikace mohou přijmout pouze zabezpečené připojení. Pro podporu těchto požadavků má Azure Application Gateway komplexní šifrování TLS.

Koncová TLS umožňuje šifrovat a bezpečně přenášet citlivá data do back-endu při použití funkcí vyrovnávání zatížení vrstvy 7 Application Gateway. Mezi tyto funkce patří spřažení relace na základě souborů cookie, směrování na základě adresy URL, podpora směrování založeného na lokalitách, možnost přepisování nebo vkládání hlaviček X předávaných do * a tak dále.

Při konfiguraci s koncovým režimem komunikace TLS Application Gateway ukončí relace TLS u brány a dešifruje provoz uživatelů. Následně použije nakonfigurovaná pravidla k výběru příslušné instance back-endového fondu, na kterou provoz přesměruje. Application Gateway pak iniciuje nové připojení TLS k back-end serveru a před odesláním požadavku do back-endu znovu zašifruje data pomocí certifikátu veřejného klíče back-end serveru. Každá odpověď webového serveru prochází ke koncovému uživateli stejným procesem. Kompletní protokol TLS je povolený nastavením nastavení protokolu v [Nastavení http back-endu](./configuration-overview.md#http-settings) na https, které se pak použije na back-end fond.

Pro SKU Application Gateway a WAF V1 se zásady TLS vztahují na provoz front-endu i back-endu. Na front-endu Application Gateway funguje jako server a vynutila zásady. V back-endu Application Gateway slouží jako klient a odesílá informace protokolu/šifry jako preference během ověřování TLS.

V případě SKU Application Gateway a WAF v2 se zásady TLS vztahují jenom na provoz na front-endu a na back-end Server se nabízí všechny šifry, které v rámci metody handshake mají za výběr konkrétní šifry a verze protokolu TLS.

Application Gateway komunikuje pouze se servery back-end, které mají buď povolený seznam certifikátů, s Application Gateway nebo jejichž certifikáty jsou podepsány známými CERTIFIKAČNÍmi autoritami a CN certifikátu se shoduje s názvem hostitele v nastavení back-endu HTTP. Mezi ně patří důvěryhodné služby Azure, například Azure App Service/Web Apps a Azure API Management.

Pokud certifikáty členů v back-endu nejsou podepsané známými autoritami certifikační autority, musí být každá instance fondu back-end s povoleným koncovým protokolem TLS nakonfigurovaná s certifikátem, který umožňuje zabezpečenou komunikaci. Přidáním certifikátu zajistíte, aby brána Application Gateway komunikovala pouze se známými back-end instancemi. Tím se dále zabezpečuje koncová komunikace.

> [!NOTE] 
>
> Certifikát přidaný do **back-endu http** k ověření back-end serverů může být stejný jako certifikát přidaný do **naslouchacího procesu** pro ukončení TLS ve službě Application Gateway nebo jiný pro rozšířené zabezpečení.

![Scénář koncového protokolu TLS][1]

V tomto příkladu se požadavky využívající TLS 1.2 směrují na servery back-end v Pool1 s využitím koncového šifrování TLS.

## <a name="end-to-end-tls-and-allow-listing-of-certificates"></a>Koncová a koncová TLS a povolení výpisu certifikátů

Application Gateway komunikuje pouze se známými back-end instancemi, které mají povolený seznam certifikátů s aplikační bránou. V rámci kompletního procesu nastavení TLS se vyskytly rozdíly v souvislosti s použitou verzí Application Gateway. V následující části jsou vysvětleny jednotlivě.

## <a name="end-to-end-tls-with-the-v1-sku"></a>Komplexní protokol TLS s SKU v1

Pokud chcete povolit kompletní protokol TLS se servery back-end a Application Gateway směrovat požadavky do nich, sondy stavu musí být úspěšné a vracet reakce v pořádku.

V případě sond stavu HTTPS používá Application Gateway v1 SKU přesnou shodu ověřovacího certifikátu (veřejného klíče certifikátu back-end serveru a nikoli kořenového certifikátu), který se má odeslat do nastavení protokolu HTTP.

Pak jsou povolena pouze připojení ke známým a povoleným back-endy. Zbývající back-endy se v sondách stavu považují za chybné. Certifikáty podepsané svým držitelem slouží pouze k testování a nedoporučují se pro úlohy v produkčním prostředí. Tyto certifikáty musí být povoleny v seznamu s aplikační bránou, jak je popsáno v předchozích krocích předtím, než je lze použít.

> [!NOTE]
> Pro důvěryhodné služby Azure, jako je Azure App Service, se nevyžaduje nastavení ověřování a důvěryhodných kořenových certifikátů. Ve výchozím nastavení se považují za důvěryhodné.

## <a name="end-to-end-tls-with-the-v2-sku"></a>Komplexní TLS s SKU v2

Ověřovací certifikáty jsou zastaralé a v SKU Application Gateway v2 byly nahrazeny důvěryhodnými kořenovými certifikáty. Fungují podobně jako u ověřovacích certifikátů s několika klíčovými rozdíly:

- Certifikáty podepsané dobře známými CERTIFIKAČNÍmi autoritami, jejichž CN odpovídá názvu hostitele v nastavení back-endu HTTP, nevyžaduje žádný další krok, aby mohl koncovým protokolem TLS fungovat. 

   Pokud jsou například certifikáty back-end vydávány dobře známou certifikační autoritou a má CN of contoso.com a pole hostitel nastavení http back-endu je také nastaveno na contoso.com, nejsou nutné žádné další kroky. Můžete nastavit protokol nastavení http back-endu na HTTPS a sondu stavu a datovou cestu by byl povolený protokol TLS. Pokud jako svůj back-end používáte Azure App Service nebo jiné webové služby Azure, pak jsou tyto aplikace implicitně důvěryhodné a v koncovém protokolu TLS nejsou nutné žádné další kroky.
   
> [!NOTE] 
>
> Aby byl certifikát TLS/SSL důvěryhodný, musí být tento certifikát serveru back-end vydaný certifikační autoritou, která je dobře známá. Pokud certifikát nebyl vydán důvěryhodnou certifikační autoritou, služba Application Gateway pak zkontroluje, jestli certifikát vydávající certifikační autority vystavila důvěryhodná certifikační autorita, a tak dále, dokud nenalezne důvěryhodnou certifikační autoritu (v takovém případě se naváže důvěryhodné připojení), nebo nenalezne žádnou důvěryhodnou certifikační autoritu (v takovém případě bude služba Application Gateway označovat, že back-end není v pořádku). Proto se doporučuje certifikát serveru back-end obsahovat jak kořenové, tak zprostředkující certifikační autority.

- Pokud je certifikát podepsaný svým držitelem nebo je podepsaný neznámými zprostředkovateli, pak pokud chcete povolit kompletní protokol TLS v SKU verze 2, musí být definovaný důvěryhodný kořenový certifikát. Application Gateway komunikuje jenom s back-endy, jejichž kořenový certifikát serveru odpovídá jednomu ze seznamu důvěryhodných kořenových certifikátů v nastavení http back-endu přidruženého k fondu.

- Kromě shody s kořenovým certifikátem Application Gateway v2 taky ověří, jestli nastavení hostitele zadané v nastavení http back-endu odpovídá běžnému názvu (CN), který prezentuje certifikát TLS/SSL serveru back-endu. Při pokusu o navázání připojení TLS k back-endu nastaví Application Gateway v2 rozšíření Indikace názvu serveru (SNI) na hostitele zadané v nastavení http back-endu.

- Pokud je zvolena možnost **Vybrat název hostitele ze služby back-end** hosta místo v poli Hostitel v nastavení http back-endu, pak je hlavička sni vždy nastavená na plně kvalifikovaný název domény fondu back-end a CN v certifikátu TLS/SSL serveru back-end musí odpovídat jeho plně kvalifikovanému názvu V tomto scénáři nejsou podporovány členy fondu back-end s IP adresami.

- Kořenový certifikát je kořenový certifikát kódovaný v kódování Base64 od certifikátů back-end serveru.

## <a name="sni-differences-in-the-v1-and-v2-sku"></a>SNI rozdíly v SKU V1 a v2

Jak už bylo zmíněno dříve, Application Gateway ukončí provoz TLS od klienta v procesu Application Gateway Listener (Pojďme ho zavolat), dešifruje provoz, používá nezbytná pravidla k určení back-end serveru, na který se má požadavek přeslat, a vytvoří novou relaci protokolu TLS se back-end serverem (Pojďme zavolat připojení back-end).

V následujících tabulkách jsou vysvětlované rozdíly v SNI mezi SKU V1 a V2 v souvislosti s front-endové a back-endové připojení.

### <a name="frontend-tls-connection-client-to-application-gateway"></a>Připojení front-endu TLS (klient ke aplikační bráně)

---
Scenario | V1 | v2 |
| --- | --- | --- |
| Pokud klient Určuje hlavičku SNI a všechny naslouchací procesy pro více lokalit jsou povolené pomocí příznaku "vyžadovat SNI". | Vrátí příslušný certifikát, a pokud web neexistuje (podle server_name), připojení se resetuje. | Vrátí odpovídající certifikát, pokud je k dispozici, v opačném případě vrátí certifikát prvního nakonfigurovaného naslouchacího procesu HTTPS (v uvedeném pořadí).|
| Pokud klient nezadá hlavičku SNI a pokud jsou všechna záhlaví více lokalit povolená pomocí příkazu "vyžadovat SNI". | Obnoví připojení. | Vrátí certifikát prvního nakonfigurovaného naslouchacího procesu HTTPS (v uvedeném pořadí).
| Pokud klient nezadá hlavičku SNI a je-li k dispozici základní naslouchací proces nakonfigurovaný s certifikátem | Vrátí certifikát nakonfigurovaný v rámci základního naslouchacího procesu pro klienta (výchozí nebo záložní certifikát). | Vrátí certifikát prvního nakonfigurovaného naslouchacího procesu HTTPS (v uvedeném pořadí). |

### <a name="backend-tls-connection-application-gateway-to-the-backend-server"></a>Připojení back-end TLS (Aplikační brána k back-end serveru)

#### <a name="for-probe-traffic"></a>Pro přenos sondy

---
Scenario | V1 | v2 |
| --- | --- | --- |
| SNI (server_name) záhlaví během metody handshake TLS jako plně kvalifikovaný název domény | Nastavte jako plně kvalifikovaný název domény z back-endu fondu. Podle [specifikace RFC 6066](https://tools.ietf.org/html/rfc6066)nejsou v názvu hostitele sni povoleny literály IPv4 a IPv6 adresy. <br> **Poznámka:** Plně kvalifikovaný název domény ve fondu back-end by měl DNS přeložit na IP adresu back-end serveru (veřejná nebo privátní). | SNI Header (server_name) je nastavená jako název hostitele z vlastní sondy připojené k nastavení HTTP (Pokud je nakonfigurovaná), jinak z názvu hostitele uvedeného v nastavení HTTP, jinak od plně kvalifikovaného názvu domény zmíněného ve fondu back-endu. Pořadí priorit je vlastní test > nastavení HTTP > back-end fondu. <br> **Poznámka:** Pokud se názvy hostitelů nakonfigurované v nastavení HTTP a vlastní sondy liší, pak se SNI podle priority nastaví jako název hostitele z vlastního testu.
| Je-li adresa fondu back-endu IP adresa (V1) nebo pokud je vlastní název hostitele testu paměti nakonfigurován jako IP adresa (v2) | SNI (server_name) nebude nastavena. <br> **Poznámka:** V takovém případě by back-end server měl být schopný vrátit výchozí nebo záložní certifikát, který by měl být povolený v nastavení HTTP v části ověřovací certifikát. Pokud na back-end serveru není nakonfigurovaný žádný výchozí/záložní certifikát a očekává se SNI, server může připojení resetovat a bude mít za následek selhání sondy. | V pořadí výše zmíněné výše platí, že pokud mají IP adresu jako název hostitele, SNI nebude nastavené na základě [RFC 6066](https://tools.ietf.org/html/rfc6066). <br> **Poznámka:** Pokud není nakonfigurovaný žádný vlastní test paměti a v nastavení HTTP nebo ve fondu back-endu není nastavený žádný název hostitele, SNI se taky nenastaví v sondách v2. |

> [!NOTE] 
> Pokud není nakonfigurovaný vlastní test paměti, Application Gateway odešle výchozí test paměti v tomto formátu \<protocol\> :://127.0.0.1: \<port\> /. Například pro výchozí sondu HTTPS se pošle jako https://127.0.0.1:443/ . Upozorňujeme, že adresa 127.0.0.1 uvedená tady se používá jenom jako Hlavička hostitele HTTP a jako na základě RFC 6066 se nepoužije jako SNI hlavička. Další informace o chybách sondy stavu najdete v [Průvodci odstraňováním potíží se stavem back-endu](application-gateway-backend-health-troubleshooting.md).

#### <a name="for-live-traffic"></a>Pro živý provoz

---
Scenario | V1 | v2 |
| --- | --- | --- |
| SNI (server_name) záhlaví během metody handshake TLS jako plně kvalifikovaný název domény | Nastavte jako plně kvalifikovaný název domény z back-endu fondu. Podle [specifikace RFC 6066](https://tools.ietf.org/html/rfc6066)nejsou v názvu hostitele sni povoleny literály IPv4 a IPv6 adresy. <br> **Poznámka:** Plně kvalifikovaný název domény ve fondu back-end by měl DNS přeložit na IP adresu back-end serveru (veřejná nebo privátní). | SNI Header (server_name) je nastavená jako název hostitele z nastavení HTTP, jinak, pokud je zvolená možnost *PickHostnameFromBackendAddress* , nebo pokud se nezmiňuje žádný název hostitele, pak se nastaví jako plně kvalifikovaný název domény v konfiguraci fondu back-endu.
| Pokud adresa fondu back-endu není IP adresa nebo název hostitele není nastavený v nastavení HTTP | SNI nebude nastavená na základě [RFC 6066](https://tools.ietf.org/html/rfc6066) , pokud položka fondu back-endu není plně kvalifikovaný název domény. | SNI se nastaví jako název hostitele ze vstupního plně kvalifikovaného názvu domény z klienta a CN certifikátu back-end se musí shodovat s tímto názvem hostitele.

## <a name="next-steps"></a>Další kroky

Po získání informací o koncovém protokolu TLS na konci můžete [pomocí Application Gateway s PowerShellem](application-gateway-end-to-end-ssl-powershell.md) vytvořit Aplikační bránu s koncovým protokolem TLS a vytvořit tak koncovou TLS.

<!--Image references-->

[1]: ./media/ssl-overview/scenario.png