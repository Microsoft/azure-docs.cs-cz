---
title: Povolení ukončení tls v bráně aplikace Azure
description: Tento článek je přehled aplikační brány end to end TLS podpory.
services: application-gateway
author: amsriva
ms.service: application-gateway
ms.topic: article
ms.date: 3/19/2019
ms.author: victorh
ms.openlocfilehash: ae80b49c3bfb40743665768622d3f4a8a6990c12
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2020
ms.locfileid: "81311848"
---
# <a name="overview-of-tls-termination-and-end-to-end-tls-with-application-gateway"></a>Přehled ukončení TLS a ukončení tls s aplikační bránou

Zabezpečení transportní vrstvy (TLS), dříve známé jako SSL (Secure Sockets Layer), je standardní technologie zabezpečení pro vytvoření šifrovaného propojení mezi webovým serverem a prohlížečem. Tento odkaz zajišťuje, že všechna data předávaná mezi webovým serverem a prohlížeči zůstanou soukromá a šifrovaná. Aplikační brána podporuje ukončení TLS na bráně i šifrování TLS od konce.

## <a name="tls-termination"></a>Ukončení TLS

Aplikační brána podporuje ukončení TLS na bránu, po kterém provoz obvykle proudí nešifrované na back-endové servery. Existuje celá řada výhod ukončení TLS na aplikační bráně:

- **Zlepšený výkon** – Největší výkon hit při provádění TLS dešifrování je počáteční handshake. Chcete-li zvýšit výkon, server, který provádí dešifrování, ukládá ID relace TLS do mezipaměti a spravuje lístky relace TLS. Pokud se tak děje v bráně aplikace, všechny požadavky od stejného klienta můžete použít hodnoty uložené v mezipaměti. Pokud se provádí na serverech back-end, pak pokaždé, když požadavky klienta přejít na jiný server klient musí znovu ověřit. Použití lístků TLS může pomoci zmírnit tento problém, ale nejsou podporovány všemi klienty a může být obtížné konfigurovat a spravovat.
- **Lepší využití back-endových serverů** – zpracování SSL/TLS je velmi náročné na procesor a s růstem velikosti klíčů je stále intenzivnější. Odebrání této práce z back-endových serverů jim umožňuje zaměřit se na to, co jsou nejúčinnější, poskytování obsahu.
- **Inteligentní směrování** – Dešifrováním provozu má aplikační brána přístup k obsahu požadavku, jako jsou záhlaví, identifikátor URI a tak dále, a může tato data použít k směrování požadavků.
- **Správa certifikátů** – Certifikáty je potřeba zakoupit a nainstalovat pouze na aplikační bráně a ne všechny back-endové servery. To šetří čas i peníze.

Chcete-li nakonfigurovat ukončení TLS, je nutné přidat certifikát TLS/SSL do naslouchací proces, aby aplikační brána mohla odvodit symetrický klíč podle specifikace protokolu TLS/SSL. Symetrický klíč se pak používá k šifrování a dešifrování přenosů odeslaných do brány. Certifikát TLS/SSL musí být ve formátu PFX (Personal Information Exchange). Tento formát souboru umožňuje exportovat soukromý klíč, který je vyžadován aplikační bránou k provedení šifrování a dešifrování provozu.

> [!NOTE] 
>
> Aplikační brána neposkytuje žádnou možnost vytvořit nový certifikát nebo odeslat žádost o certifikát certifikačnímu úřadu.

Aby připojení TLS fungovalo, musíte zajistit, aby certifikát TLS/SSL splňoval následující podmínky:

- Aktuální datum a čas je v rozsahu "Platné od" a "Platné do" na certifikátu.
- Běžný název certifikátu (CN) odpovídá hlavičce hostitele v požadavku. Například pokud klient posílá požadavek na `https://www.contoso.com/`, musí být CN `www.contoso.com`.

### <a name="certificates-supported-for-tls-termination"></a>Certifikáty podporované pro ukončení TLS

Aplikační brána podporuje následující typy certifikátů:

- Certifikát certifikační autority: Certifikát certifikační autority je digitální certifikát vydaný certifikační autoritou (CA)
- Certifikát EV (Extended Validation): Certifikát EV je certifikát, který odpovídá standardním pokynům certifikátu. Tím se změní na lištu lokátoru prohlížeče zeleně a zveřejní se také název společnosti.
- Certifikát se zástupnými znaky: Tento certifikát podporuje libovolný počet subdomén na základě *.site.com, kde by subdoména nahradila *. Nepodporuje však site.com, takže v případě, že uživatelé přistupují k vašim webovým stránkům bez zadání úvodního "www", certifikát se zástupnými symboly se na to nevztahuje.
- Certifikáty podepsané svým držitelem: Prohlížeče klienta těmto certifikátům nedůvěřují a upozorní uživatele, že certifikát virtuální služby není součástí řetězce důvěryhodnosti. Certifikáty podepsané svým držitelem jsou vhodné pro testování nebo prostředí, kde správci řídí klienty a mohou bezpečně obejít výstrahy zabezpečení prohlížeče. Produkční úlohy by nikdy neměly používat certifikáty podepsané svým držitelem.

Další informace naleznete v [tématu konfigurace ukončení protokolu TLS pomocí brány aplikace](https://docs.microsoft.com/azure/application-gateway/create-ssl-portal).

### <a name="size-of-the-certificate"></a>Velikost osvědčení
V části [Omezení aplikační brány](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#application-gateway-limits) zjistěte, jak je maximální podporovaná velikost certifikátu TLS/SSL.

## <a name="end-to-end-tls-encryption"></a>Šifrování TLS od konce do konce

Někteří zákazníci nemusí chtít nešifrovanou komunikaci se servery back-end. Může to být z důvodu požadavků na zabezpečení nebo dodržování předpisů nebo protože aplikace může přijímat pouze zabezpečená připojení. Pro takové aplikace podporuje aplikační brána šifrování TLS od konce.

End to-end TLS umožňuje bezpečně přenášet citlivá data do back-endu šifrované a zároveň využívat výhod layer 7 vyrovnávání zatížení funkce, které poskytuje aplikační brána. Jde například o spřažení relací na základě souborů cookie, směrování na základě adres URL, podporu směrování založeného na webech nebo možnost vkládat hlavičky X-Forwarded-*.

Při konfiguraci s režimem komunikace TLS od konce do konce ukončí aplikační brána relace TLS u brány a dešifruje uživatelský provoz. Následně použije nakonfigurovaná pravidla k výběru příslušné instance back-endového fondu, na kterou provoz přesměruje. Aplikační brána pak iniciuje nové připojení TLS k back-endovému serveru a před přenosem požadavku do back-endu znovu šifruje data pomocí certifikátu veřejného klíče serveru back-end. Každá odpověď webového serveru prochází ke koncovému uživateli stejným procesem. Možnost TLS od konce ke konci je povolena nastavením protokolu v [nastavení HTTP back-endu](https://docs.microsoft.com/azure/application-gateway/configuration-overview#http-settings) na protokol HTTPS, který se pak použije na back-endový fond.

Zásady TLS platí pro front-endový i back-endový provoz. Na front-endu funguje aplikační brána jako server a vynucuje zásady. V back-endu funguje aplikační brána jako klient a odešle informace o protokolu/šifra jako předvolbu během handshake TLS.

Aplikační brána komunikuje pouze s těmi instancemi back-endu, které buď zadali svůj certifikát na seznamu povolených, s aplikační bránou nebo jejichž certifikáty jsou podepsány známými autoritami certifikační autority, kde certifikát CN odpovídá názvu hostitele v nastavení back-endu HTTP. Patří mezi ně důvěryhodné služby Azure, jako jsou webové aplikace služby Azure App service a Správa rozhraní API Azure.

Pokud certifikáty členů v back-endovém fondu nejsou podepsány známými autoritami certifikační autority, musí být každá instance v back-endovém fondu s povoleným koncovým do koncovým seznamem nakonfigurována s certifikátem umožňujícím zabezpečenou komunikaci. Přidání certifikátu zajistí, že aplikační brána komunikuje pouze se známými back-endovými instancemi. To dále zabezpečuje komunikaci mezi koncovými soubory.

> [!NOTE] 
>
> Nastavení ověřovacího certifikátu není vyžadováno pro důvěryhodné služby Azure, jako jsou webové aplikace služby Azure App service a Správa rozhraní Azure API.

> [!NOTE] 
>
> Certifikát přidaný do **nastavení HTTP back-endu** k ověření serverů back-endu může být stejný jako certifikát přidaný k **naslouchací procesu** pro ukončení protokolu TLS v bráně aplikace nebo jiný pro rozšířené zabezpečení.

![scénář od konce do konce tls][1]

V tomto příkladu jsou požadavky používající TLS1.2 směrovány do back-endových serverů v poolu1 pomocí koncového tls.

## <a name="end-to-end-tls-and-whitelisting-of-certificates"></a>End-to-end TLS a whitelisting certifikátů

Služba Application Gateway komunikuje pouze se známými back-endovými instancemi, jejichž certifikáty jsou uvedeny v seznamu povolených certifikátů ve službě Application Gateway. Chcete-li povolit vytváření seznamu povolených certifikátů, musíte do aplikační brány nahrát veřejný klíč certifikátů back-endového serveru (nikoliv kořenový certifikát). Potom budou povolena jenom připojení ke známým back-endům uvedeným v seznamu. Zbývající back-endy způsobí chybu brány. Certifikáty podepsané svým držitelem slouží pouze k testování a nedoporučují se pro úlohy v produkčním prostředí. Tyto certifikáty musí být uvedeny na seznamu povolených s bránou aplikace, jak je popsáno v předchozích krocích, aby mohly být použity.

> [!NOTE]
> Nastavení ověřovacího certifikátu není vyžadováno pro důvěryhodné služby Azure, jako je služba Azure App Service.

## <a name="end-to-end-tls-with-the-v2-sku"></a>End to end TLS s v2 SKU

Ověřovací certifikáty byly zastaralé a nahrazeny důvěryhodnými kořenovými certifikáty ve skladové jednotce sku aplikace v2. Fungují podobně jako ověřovací certifikáty s několika klíčovými rozdíly:

- Certifikáty podepsané známými autoritami certifikační autority, jejichž název propojené sítě odpovídá názvu hostitele v nastavení back-endu HTTP, nevyžadují žádný další krok, aby koncovka tls fungovala. 

   Pokud jsou například back-endové certifikáty vydávány známou certifikační autoritou a má kn contoso.com a pole hostitele nastavení back-endu http je také nastaveno na contoso.com, nejsou nutné žádné další kroky. Můžete nastavit protokol nastavení back-endu http na protokol HTTPS a sonda stavu i datová cesta by byly povoleny protokolem TLS. Pokud používáte Azure App Service nebo jiné webové služby Azure jako back-end, pak jsou implicitně důvěryhodné a žádné další kroky jsou nutné pro koncové až koncové TLS.
   
> [!NOTE] 
>
> Aby byl certifikát TLS/SSL důvěryhodný, musí být tento certifikát back-endového serveru vydán certifikační autoritou, která je součástí důvěryhodného úložiště brány aplikace.Pokud certifikát nebyl vydán důvěryhodnou certifikační autoritou, Aplikační brána pak zkontroluje, zda certifikát vystavující certifikační autority byl vydán důvěryhodnou certifikační autoritou a tak dále, dokud nebude nalezen a důvěryhodné ho CA (v tomto okamžiku bude navázáno důvěryhodné a zabezpečené připojení) nebo dokud nebude nalezen a nedůvěryhodný certifikační orgán (v tomto okamžiku bude aplikační brána označovat back-end jako nefunkční). Proto se doporučuje, aby certifikát back-endového serveru obsahoval kořenové i zprostředkující certifikační autority.

- Pokud je certifikát podepsán svým držitelem nebo podepsán neznámými zprostředkovateli, musí být definován důvěryhodný kořenový certifikát, aby bylo možné ukončit tls ve sku v2, musí být definován důvěryhodný kořenový certifikát. Aplikační brána bude komunikovat pouze s back-endy, jejichž kořenový certifikát certifikátu serveru odpovídá jednomu ze seznamu důvěryhodných kořenových certifikátů v nastavení http back-endu přidruženém ke fondu.

> [!NOTE] 
>
> Certifikát podepsaný svým držitelem musí být součástí řetězu certifikátů. Jeden certifikát podepsaný svým držitelem bez řetězce není ve skladové jednotce V2 podporován.

- Kromě shody kořenového certifikátu aplikace gateway také ověří, zda nastavení Host zadané v nastavení http back-endu odpovídá nastavení běžného názvu (CN) prezentovaného certifikátem TLS/SSL serveru back-endového serveru. Při pokusu o vytvoření připojení TLS k back-endu nastaví aplikační brána rozšíření SNI (SNI) na hostitele určeného v nastavení http back-endu.
- Pokud je místo pole Host v nastavení http back-endu **vybránnázev hostitele z back-endové adresy,** je hlavička SNI vždy nastavena na hlavní název back-endového fondu a cn na back-endovém serveru TLS/SSL certifikát musí odpovídat svému fqdn. Back-endu členové fondu s IP adresy nejsou podporovány v tomto scénáři.
- Kořenový certifikát je kořenový certifikát kódovaný na základně 64 z certifikátů back-endového serveru.

## <a name="next-steps"></a>Další kroky

Po naučení o od konci do konce TLS, přejděte na [Konfigurace od konce na konec TLS pomocí aplikační brány s PowerShell](application-gateway-end-to-end-ssl-powershell.md) vytvořit aplikační bránu pomocí end-to-end TLS.

<!--Image references-->

[1]: ./media/ssl-overview/scenario.png
