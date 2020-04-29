---
title: Povolení koncového šifrování TLS v Azure Application Gateway
description: Tento článek představuje přehled Application Gateway kompletní podpora protokolu TLS.
services: application-gateway
author: amsriva
ms.service: application-gateway
ms.topic: article
ms.date: 3/19/2019
ms.author: victorh
ms.openlocfilehash: ae80b49c3bfb40743665768622d3f4a8a6990c12
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "81311848"
---
# <a name="overview-of-tls-termination-and-end-to-end-tls-with-application-gateway"></a>Přehled ukončení protokolu TLS a koncového a koncového protokolu TLS s Application Gateway

Protokol TLS (Transport Layer Security), dříve označovaný jako SSL (Secure Sockets Layer) (SSL), je standardní technologie zabezpečení pro vytváření šifrovaného propojení mezi webovým serverem a prohlížečem. Tento odkaz zajistí, že všechna data předaná mezi webovým serverem a prohlížečem zůstanou soukromá a šifrovaná. Aplikační brána podporuje ukončení protokolu TLS v bráně i koncové šifrování TLS.

## <a name="tls-termination"></a>Ukončení protokolu TLS

Application Gateway podporuje ukončení protokolu TLS v bráně, po kterém provoz obvykle přechází na back-end servery jako nešifrované. Ukončení protokolu TLS ve službě Application Gateway má několik výhod:

- **Vylepšený výkon** – největší dosažený výkon při dešifrování TLS je počáteční metodou handshake. Aby se zlepšil výkon, server, který provádí dešifrování, ukládá ID relací TLS a spravuje lístky relace TLS. Pokud se tato služba používá u služby Application Gateway, můžou všechny požadavky ze stejného klienta používat hodnoty uložené v mezipaměti. Pokud se to provede na back-end serverech, pak pokaždé, když požadavky klienta přejdou na jiný server, musí se klient znovu ověřit. Použití lístků protokolu TLS může přispět k zmírnění tohoto problému, ale není podporováno všemi klienty a může být obtížné ho nakonfigurovat a spravovat.
- **Lepší využití back-end serverů** – zpracování SSL/TLS je velmi náročné na procesor a výrazně se zvyšuje i při zvýšení velikosti klíče. Odebráním této práce ze serverů back-end se jim umožní soustředit se na to, co je pro vás nejúčinnější, a poskytovat obsah.
- **Inteligentní směrování** – dešifrováním provozu má Aplikační brána přístup k obsahu požadavku, jako jsou HLAVIČKY, URI a tak dále, a může tato data použít k směrování požadavků.
- **Správa certifikátů** – certifikáty je potřeba koupit a nainstalovat jenom na aplikační bránu, ne na všechny back-endové servery. Tím ušetříte čas i peníze.

Ke konfiguraci ukončení protokolu TLS se vyžaduje, aby se certifikát TLS/SSL přidal do naslouchacího procesu, aby služba Application Gateway mohla odvodit symetrický klíč podle specifikace protokolu TLS/SSL. Symetrický klíč se pak použije k šifrování a dešifrování provozu odeslaného do brány. Certifikát TLS/SSL musí být ve formátu PFX (Personal Information Exchange). Tento formát souboru umožňuje exportovat privátní klíč, který služba Application Gateway vyžaduje k provádění šifrování a dešifrování provozu.

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
- Certifikáty podepsané svým držitelem: klientské prohlížeče nedůvěřují těmto certifikátům a upozorní uživatele, že certifikát virtuální služby není součástí řetězce důvěryhodnosti. Certifikáty podepsané svým držitelem jsou vhodné pro testování nebo prostředí, kde správci kontrolují klienty a můžou bezpečně obejít výstrahy zabezpečení v prohlížeči. Provozní úlohy by nikdy neměly používat certifikáty podepsané svým držitelem.

Další informace najdete v tématu [Konfigurace ukončení TLS pomocí služby Application Gateway](https://docs.microsoft.com/azure/application-gateway/create-ssl-portal).

### <a name="size-of-the-certificate"></a>Velikost certifikátu
V části [omezení Application Gateway](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#application-gateway-limits) můžete zjistit, že je podporovaná maximální velikost certifikátu TLS/SSL.

## <a name="end-to-end-tls-encryption"></a>Koncové šifrování TLS

Někteří zákazníci možná nevyžadují nešifrovanou komunikaci se servery back-end. Může to být z důvodu požadavků na zabezpečení nebo dodržování předpisů nebo protože aplikace může přijímat pouze zabezpečená připojení. U takových aplikací Application Gateway podporuje koncové šifrování TLS.

Koncová TLS umožňuje bezpečně přenášet citlivá data do back-endu v zašifrované době a přitom stále využívat výhody funkcí vyrovnávání zatížení vrstvy 7, které nabízí Aplikační brána. Jde například o spřažení relací na základě souborů cookie, směrování na základě adres URL, podporu směrování založeného na webech nebo možnost vkládat hlavičky X-Forwarded-*.

Při konfiguraci s režimem komunikace koncového protokolu TLS ukončí brána Application Gateway relace TLS v bráně a dešifruje provoz uživatelů. Následně použije nakonfigurovaná pravidla k výběru příslušné instance back-endového fondu, na kterou provoz přesměruje. Application Gateway pak Inicializuje nové připojení TLS k back-end serveru a před odesláním požadavku do back-endu znovu zašifruje data pomocí certifikátu veřejného klíče back-end serveru. Každá odpověď webového serveru prochází ke koncovému uživateli stejným procesem. Koncový protokol TLS je povolený nastavením nastavení protokolu v [Nastavení http back-endu](https://docs.microsoft.com/azure/application-gateway/configuration-overview#http-settings) na https, které se pak použije na back-end fond.

Zásady TLS se vztahují na provoz front-endu i back-endu. Na front-endu Application Gateway funguje jako server a vynutila zásady. V back-endu Application Gateway slouží jako klient a odesílá informace protokolu/šifry jako preference během ověřování TLS.

Služba Application Gateway komunikuje pouze s instancemi back-end, které mají buď povolený certifikát s aplikační bránou, nebo jejichž certifikáty jsou podepsané známými CERTIFIKAČNÍmi autoritami, kde CN certifikátu odpovídá názvu hostitele v nastavení back-endu HTTP. Patří mezi ně důvěryhodné služby Azure, jako jsou Azure App Service Web Apps a Azure API Management.

Pokud certifikáty členů ve fondu back-end nejsou podepsané známými autoritami certifikační autority, musí být každá instance fondu back-end s povoleným koncovým protokolem TLS nakonfigurovaná s certifikátem, který umožňuje zabezpečenou komunikaci. Přidáním certifikátu zajistíte, aby brána Application Gateway komunikovala pouze se známými back-end instancemi. Tím se dále zabezpečuje koncová komunikace.

> [!NOTE] 
>
> Pro důvěryhodné služby Azure, jako jsou Azure App Service Web Apps a Azure API Management se nevyžaduje nastavení ověřovacího certifikátu.

> [!NOTE] 
>
> Certifikát přidaný do **back-endu http** k ověření back-end serverů může být stejný jako certifikát přidaný do **naslouchacího procesu** pro ukončení TLS ve službě Application Gateway nebo jiný pro rozšířené zabezpečení.

![Scénář koncového protokolu TLS][1]

V tomto příkladu se požadavky využívající TLS 1.2 směrují na servery back-end v Pool1 s využitím koncového šifrování TLS.

## <a name="end-to-end-tls-and-whitelisting-of-certificates"></a>Konec na konec TLS a seznam povolených certifikátů

Služba Application Gateway komunikuje pouze se známými back-endovými instancemi, jejichž certifikáty jsou uvedeny v seznamu povolených certifikátů ve službě Application Gateway. Chcete-li povolit vytváření seznamu povolených certifikátů, musíte do aplikační brány nahrát veřejný klíč certifikátů back-endového serveru (nikoliv kořenový certifikát). Potom budou povolena jenom připojení ke známým back-endům uvedeným v seznamu. Zbývající back-endy způsobí chybu brány. Certifikáty podepsané svým držitelem slouží pouze k testování a nedoporučují se pro úlohy v produkčním prostředí. Tyto certifikáty musí být v seznamu povolených s aplikační bránou, jak je popsáno v předchozích krocích předtím, než je můžete použít.

> [!NOTE]
> Pro důvěryhodné služby Azure, jako je Azure App Service, se nevyžaduje nastavení ověřovacího certifikátu.

## <a name="end-to-end-tls-with-the-v2-sku"></a>Koncový protokol TLS s SKU v2

Ověřovací certifikáty jsou zastaralé a v SKU Application Gateway v2 byly nahrazeny důvěryhodnými kořenovými certifikáty. Fungují podobně jako u ověřovacích certifikátů s několika klíčovými rozdíly:

- Certifikáty podepsané dobře známými CERTIFIKAČNÍmi autoritami, jejichž CN odpovídá názvu hostitele v nastavení back-endu HTTP, nevyžaduje žádný další krok, aby mohl koncovým protokolem TLS fungovat. 

   Pokud jsou například certifikáty back-end vydávány dobře známou certifikační autoritou a má CN of contoso.com a pole hostitel nastavení http back-endu je také nastaveno na contoso.com, nejsou nutné žádné další kroky. Můžete nastavit protokol nastavení http back-endu na HTTPS a sondu stavu a datovou cestu by byl povolený protokol TLS. Pokud jako svůj back-end používáte Azure App Service nebo jiné webové služby Azure, pak jsou tyto aplikace implicitně důvěryhodné a v koncovém protokolu TLS nejsou nutné žádné další kroky.
   
> [!NOTE] 
>
> Aby byl certifikát TLS/SSL důvěryhodný, musí být tento certifikát back-endu vydaný certifikační autoritou, která je součástí důvěryhodného úložiště Application Gateway. Pokud certifikát nebyl vydán důvěryhodnou certifikační autoritou, Application Gateway pak zkontroluje, jestli certifikát vydávající certifikační autority vystavila důvěryhodná certifikační autorita, a tak dále, dokud nenalezne buď důvěryhodnou certifikační autoritu (v němž navázala důvěryhodné, zabezpečené připojení), nebo nenalezne žádnou důvěryhodnou certifikační autoritu (v tom případě bude Application Gateway označovat, že back-end není v pořádku). Proto se doporučuje certifikát serveru back-end obsahovat jak kořenové, tak zprostředkující certifikační autority.

- Pokud je certifikát podepsaný svým držitelem nebo je podepsaný neznámými zprostředkujícími zprostředkovateli, pak se musí definovat důvěryhodný kořenový certifikát s koncovým protokolem TLS v v2 SKU. Application Gateway bude komunikovat jenom s back-endy, jejichž kořenový certifikát serveru se shoduje s jedním ze seznamů důvěryhodných kořenových certifikátů v nastavení http back-endu přidružené k tomuto fondu.

> [!NOTE] 
>
> Certifikát podepsaný svým držitelem musí být součástí řetězu certifikátů. V SKU v2 není podporován jediný certifikát podepsaný svým držitelem bez řetězu.

- Kromě shody s kořenovým certifikátem Application Gateway taky ověří, jestli nastavení hostitele zadané v nastavení http back-endu odpovídá běžnému názvu (CN), který prezentuje certifikát TLS/SSL back-end serveru. Při pokusu o navázání připojení TLS k back-endu Application Gateway nastaví rozšíření Indikace názvu serveru (SNI) na hostitele zadané v nastavení http back-endu.
- Pokud je zvolena možnost **Vybrat název hostitele z back-endu** , místo aby bylo pole hostitel v nastavení http back-endu nastaveno, pak je hlavička sni vždy nastavená na plně kvalifikovaný název domény fondu back-end a CN v certifikátu TLS/SSL serveru back-end se musí shodovat s názvem FQDN. V tomto scénáři nejsou podporovány členy fondu back-end s IP adresami.
- Kořenový certifikát je kořenový certifikát kódovaný v kódování Base64 od certifikátů back-end serveru.

## <a name="next-steps"></a>Další kroky

Po získání informací o koncovém protokolu TLS na konci můžete [pomocí Application Gateway s PowerShellem](application-gateway-end-to-end-ssl-powershell.md) vytvořit Aplikační bránu s koncovým protokolem TLS a vytvořit tak koncovou TLS.

<!--Image references-->

[1]: ./media/ssl-overview/scenario.png
