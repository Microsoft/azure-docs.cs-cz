---
title: Povolení koncového šifrování protokolu SSL v Azure Application Gateway
description: Tento článek představuje přehled Application Gateway Podpora koncových protokolů SSL.
services: application-gateway
author: amsriva
ms.service: application-gateway
ms.topic: article
ms.date: 3/19/2019
ms.author: victorh
ms.openlocfilehash: 199fcdf2ebf10852906b842f09fe7beafd2acdb5
ms.sourcegitcommit: 4b431e86e47b6feb8ac6b61487f910c17a55d121
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/18/2019
ms.locfileid: "68326611"
---
# <a name="overview-of-ssl-termination-and-end-to-end-ssl-with-application-gateway"></a>Přehled ukončení protokolu SSL a koncového šifrování protokolu SSL s Application Gateway

SSL (Secure Sockets Layer) (SSL) je standardní technologie zabezpečení pro vytvoření šifrovaného propojení mezi webovým serverem a prohlížečem. Tento odkaz zajistí, že všechna data předaná mezi webovým serverem a prohlížečem zůstanou soukromá a šifrovaná. Aplikační brána podporuje jak ukončení protokolu SSL v bráně, tak i koncové šifrování protokolu SSL.

## <a name="ssl-termination"></a>Ukončení protokolu SSL

Služba Application Gateway podporuje ukončení protokolu SSL na bráně, po čemž provoz typicky teče nešifrován na back-endové servery. Ukončení protokolu SSL ve službě Application Gateway má několik výhod:

- **Vylepšený výkon** – největší dosažený výkon při dešifrování SSL je počáteční metodou handshake. Pro zvýšení výkonu Server, který provádí dešifrování, zapíše ID relací SSL a spravuje lístky relace TLS. Pokud se tato služba používá u služby Application Gateway, můžou všechny požadavky ze stejného klienta používat hodnoty uložené v mezipaměti. Pokud se to provede na back-end serverech, pak pokaždé, když požadavky klienta přejdou na jiný server, musí se klient znovu ověřit. Použití lístků protokolu TLS může přispět k zmírnění tohoto problému, ale není podporováno všemi klienty a může být obtížné ho nakonfigurovat a spravovat.
- **Lepší využití back-end serverů** – zpracování SSL/TLS je velmi náročné na procesor a výrazně se zvyšuje i při zvýšení velikosti klíče. Odebráním této práce ze serverů back-end se jim umožní soustředit se na to, co je pro vás nejúčinnější, a poskytovat obsah.
- **Inteligentní směrování** – dešifrováním provozu má Aplikační brána přístup k obsahu požadavku, jako jsou HLAVIČKY, URI a tak dále, a může tato data použít k směrování požadavků.
- **Správa certifikátů** – certifikáty je potřeba koupit a nainstalovat jenom na aplikační bránu, ne na všechny back-endové servery. Tím ušetříte čas i peníze.

Chcete-li nakonfigurovat ukončení protokolu SSL, je nutné přidat certifikát SSL do naslouchacího procesu, aby služba Application Gateway mohla odvodit symetrický klíč podle specifikace protokolu SSL. Symetrický klíč se pak použije k šifrování a dešifrování provozu odeslaného do brány. Certifikát SSL musí být ve formátu PFX (Personal Information Exchange). Tento formát souboru umožňuje exportovat privátní klíč, který služba Application Gateway vyžaduje k provádění šifrování a dešifrování provozu.

> [!NOTE] 
>
> Application Gateway neposkytuje žádnou možnost vytvořit nový certifikát nebo odeslat žádost o certifikát certifikační autoritě.

Aby připojení SSL fungovalo, musíte zajistit, aby certifikát SSL splňoval následující podmínky:

- Aktuální datum a čas spadá do rozsahu dat "platný od" a "platné do" na certifikátu.
- Běžný název certifikátu (CN) odpovídá hlavičce hostitele v požadavku. Například pokud klient posílá požadavek na `https://www.contoso.com/`, musí být CN `www.contoso.com`.

### <a name="certificates-supported-for-ssl-termination"></a>Certifikáty podporované pro ukončení SSL

Application Gateway podporuje následující typy certifikátů:

- Certifikát CA (certifikační autorita): Certifikát certifikační autority je digitální certifikát vydaný certifikační autoritou (CA).
- Certifikát EV (rozšířené ověřování): Certifikát EV je standardní Oborová pravidla pro certifikáty. Tím se zamění i název společnosti na panelu lokátoru v prohlížeči a zároveň na jeho publikování.
- Certifikát se zástupnými znaky: Tento certifikát podporuje libovolný počet subdomén na základě *. site.com, kde by vaše subdoména nahradila *. Ale nepodporuje site.com, takže pokud uživatelé přistupují k webu, aniž by museli psát úvodní "www", certifikát se zástupnými znaky nebude pokrývat.
- Certifikáty podepsané svým držitelem: Klientské prohlížeče nedůvěřují těmto certifikátům a upozorní uživatele, že certifikát virtuální služby není součástí řetězce důvěryhodnosti. Certifikáty podepsané svým držitelem jsou vhodné pro testování nebo prostředí, kde správci kontrolují klienty a můžou bezpečně obejít výstrahy zabezpečení v prohlížeči. Provozní úlohy by nikdy neměly používat certifikáty podepsané svým držitelem.

Další informace najdete v tématu [Konfigurace ukončení protokolu SSL pomocí služby Application Gateway](https://docs.microsoft.com/azure/application-gateway/create-ssl-portal).

### <a name="size-of-the-certificate"></a>Velikost certifikátu
V části [omezení Application Gateway](https://docs.microsoft.com/azure/azure-subscription-service-limits#application-gateway-limits) můžete zjistit, že je podporovaná maximální velikost certifikátu SSL.

## <a name="end-to-end-ssl-encryption"></a>Koncové šifrování protokolu SSL

Někteří zákazníci možná nevyžadují nešifrovanou komunikaci se servery back-end. Může to být z důvodu požadavků na zabezpečení nebo dodržování předpisů nebo protože aplikace může přijímat pouze zabezpečená připojení. Pro takové aplikace služba Application Gateway podporuje koncové šifrování protokolu SSL.

Koncové šifrování protokolu SSL umožňuje bezpečně přenášet citlivá data do back-endu v zašifrované podobě a současně využívat výhody funkcí pro vyrovnávání zatížení vrstvy 7, které nabízí aplikační brána. Jde například o spřažení relací na základě souborů cookie, směrování na základě adres URL, podporu směrování založeného na webech nebo možnost vkládat hlavičky X-Forwarded-*.

Když je nakonfigurována s režimem komunikace koncového šifrování protokolu SSL, služba Application Gateway ukončuje na bráně relace protokolu SSL a dešifruje provoz uživatelů. Následně použije nakonfigurovaná pravidla k výběru příslušné instance back-endového fondu, na kterou provoz přesměruje. Služba Application Gateway poté zahájí nové připojení SSL k back-endovému serveru a před odesláním požadavku do back-endu znovu zašifruje data pomocí certifikátu s veřejným klíčem back-endového serveru. Každá odpověď webového serveru prochází ke koncovému uživateli stejným procesem. Koncové šifrování protokolu SSL je povolené nastavením nastavení protokolu v [Nastavení http back-endu](https://docs.microsoft.com/azure/application-gateway/configuration-overview#http-settings) na https, které se pak použije na back-end fond.

Zásady SSL se vztahují na provoz front-endu i back-endu. Na front-endu Application Gateway funguje jako server a vynutila zásady. V back-endu Application Gateway slouží jako klient a odesílá informace protokolu/šifry jako preference během ověřování SSL.

Služba Application Gateway komunikuje pouze s instancemi back-end, které mají buď povolený certifikát s aplikační bránou, nebo jejichž certifikáty jsou podepsané známými CERTIFIKAČNÍmi autoritami, kde CN certifikátu odpovídá názvu hostitele v HTTP. nastavení back-endu. Patří mezi ně důvěryhodné služby Azure, jako jsou Azure App Service Web Apps a Azure API Management.

Pokud certifikáty členů ve fondu back-end nejsou podepsané známými autoritami certifikační autority, musí být každá instance ve fondu back-end s povoleným koncovým protokolem SSL nakonfigurovaná s certifikátem, aby bylo možné zabezpečenou komunikaci. Přidáním certifikátu zajistíte, aby brána Application Gateway komunikovala pouze se známými back-end instancemi. Tím se dále zabezpečuje koncová komunikace.

> [!NOTE] 
>
> Pro důvěryhodné služby Azure, jako jsou Azure App Service Web Apps a Azure API Management se nevyžaduje nastavení ověřovacího certifikátu.

> [!NOTE] 
>
> Certifikát přidaný do **back-endu http** k ověření back-end serverů může být stejný jako certifikát přidaný do **naslouchacího procesu** pro ukončení protokolu SSL v aplikační bráně nebo jiný pro rozšířené zabezpečení.

![scénář koncového šifrování protokolu ssl][1]

V tomto příkladu jsou požadavky, které používají šifrování TLS 1.2, přesměrované koncovým šifrováním protokolu SSL na back-endové servery fondu Pool1.

## <a name="end-to-end-ssl-and-whitelisting-of-certificates"></a>Koncové šifrování protokolu SSL a vytváření seznamu povolených certifikátů

Služba Application Gateway komunikuje pouze se známými back-endovými instancemi, jejichž certifikáty jsou uvedeny v seznamu povolených certifikátů ve službě Application Gateway. Chcete-li povolit vytváření seznamu povolených certifikátů, musíte do aplikační brány nahrát veřejný klíč certifikátů back-endového serveru (nikoliv kořenový certifikát). Potom budou povolena jenom připojení ke známým back-endům uvedeným v seznamu. Zbývající back-endy způsobí chybu brány. Certifikáty podepsané svým držitelem slouží pouze k testování a nedoporučují se pro úlohy v produkčním prostředí. Tyto certifikáty musí být v seznamu povolených s aplikační bránou, jak je popsáno v předchozích krocích předtím, než je můžete použít.

> [!NOTE]
> Pro důvěryhodné služby Azure, jako je Azure App Service, se nevyžaduje nastavení ověřovacího certifikátu.

## <a name="end-to-end-ssl-with-the-v2-sku"></a>Koncové šifrování protokolu SSL s SKU v2

Ověřovací certifikáty jsou zastaralé a v SKU Application Gateway v2 byly nahrazeny důvěryhodnými kořenovými certifikáty. Fungují podobně jako u ověřovacích certifikátů s několika klíčovými rozdíly:

- Certifikáty podepsané dobře známými CERTIFIKAČNÍmi autoritami, jejichž CN odpovídá názvu hostitele v nastavení back-endu HTTP, nevyžaduje žádný další krok pro fungování koncového protokolu SSL. 

   Pokud jsou například certifikáty back-end vydávány dobře známou certifikační autoritou a má CN of contoso.com a pole hostitel nastavení http back-endu je také nastaveno na contoso.com, nejsou nutné žádné další kroky. Můžete nastavit protokol nastavení http back-endu na HTTPS a sondu stavu a datovou cestu by byl protokol SSL povolený. Pokud jako svůj back-end používáte Azure App Service nebo jiné webové služby Azure, pak jsou tyto aplikace implicitně důvěryhodné a v koncovém protokolu SSL nejsou nutné žádné další kroky.
   
> [!NOTE] 
>
> Aby byl certifikát SSL důvěryhodný, musí být tento certifikát serveru back-end vydaný certifikační autoritou, která je součástí důvěryhodného úložiště brány applicatin. Pokud certifikát nebyl vydán důvěryhodnou certifikační autoritou, bude Application Gateway pak kontrolovat Podívejte se, jestli certifikát vydávající certifikační autority vystavila důvěryhodná certifikační autorita, a tak dále, dokud nenalezne důvěryhodnou certifikační autoritu (v takovém případě bude navázáno důvěryhodné připojení) nebo nenalezne žádnou důvěryhodnou certifikační autoritu (v takovém případě označí bránu applicatin back-end unhe. althy). Proto se doporučuje certifikát back-end serveru, který obsahuje kořenové i intermidiate certifikační autority.

- Pokud je certifikát podepsaný svým držitelem nebo je podepsaný neznámými zprostředkujícími zprostředkovateli, pak se musí definovat důvěryhodný kořenový certifikát v části v2 SKU. Application Gateway bude komunikovat jenom s back-endy, jejichž kořenový certifikát serveru se shoduje s jedním ze seznamů důvěryhodných kořenových certifikátů v nastavení http back-endu přidružené k tomuto fondu.

> [!NOTE] 
>
> Certifikát podepsaný svým držitelem musí být součástí řetězu certifikátů. V SKU v2 není podporován jediný certifikát podepsaný svým držitelem bez řetězu.

- Kromě shody s kořenovým certifikátem Application Gateway taky ověří, jestli nastavení hostitele zadané v nastavení http back-endu odpovídá běžnému názvu (CN), který prezentuje certifikát SSL back-end serveru. Při pokusu o navázání připojení SSL k back-endu Application Gateway nastaví rozšíření Indikace názvu serveru (SNI) na hostitele zadané v nastavení http back-endu.
- Pokud je zvolená možnost **Vybrat název hostitele z back-endu** , místo pole hostitel v nastavení http back-endu, pak je hlavička sni vždycky nastavená na plně kvalifikovaný název domény fondu back-end a CN v certifikátu SSL back-end serveru musí odpovídat svému plně kvalifikovanému názvu domény. V tomto scénáři nejsou podporovány členy fondu back-end s IP adresami.
- Kořenový certifikát je kořenový certifikát kódovaný v kódování Base64 od certifikátů back-end serveru.

## <a name="next-steps"></a>Další postup

Po získání informací o koncovém šifrování protokolu SSL v [prostředí PowerShell](application-gateway-end-to-end-ssl-powershell.md) použijte k vytvoření služby Application Gateway s KONCOVým protokolem SSL konfiguraci koncového a koncového protokolu ssl pomocí Application Gateway.

<!--Image references-->

[1]: ./media/ssl-overview/scenario.png
