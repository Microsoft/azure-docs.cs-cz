---
title: Povolení koncového ssl v bráně aplikace Azure
description: Tento článek je přehled aplikační brány end to-end SSL podpory.
services: application-gateway
author: amsriva
ms.service: application-gateway
ms.topic: article
ms.date: 3/19/2019
ms.author: victorh
ms.openlocfilehash: 9c4e6124acdbb35233f8e829f43d2665fd4a5176
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80284802"
---
# <a name="overview-of-ssl-termination-and-end-to-end-ssl-with-application-gateway"></a>Přehled ukončení SSL a ukončení ssl s aplikační bránou

Secure Sockets Layer (SSL) je standardní technologie zabezpečení pro vytvoření šifrovaného propojení mezi webovým serverem a prohlížečem. Tento odkaz zajišťuje, že všechna data předávaná mezi webovým serverem a prohlížeči zůstanou soukromá a šifrovaná. Aplikační brána podporuje jak ukončení SSL na bráně, tak šifrování SSL od konce.

## <a name="ssl-termination"></a>Ukončení protokolu SSL

Služba Application Gateway podporuje ukončení protokolu SSL na bráně, po čemž provoz typicky teče nešifrován na back-endové servery. Existuje celá řada výhod ukončení SSL na aplikační bráně:

- **Zlepšený výkon** – Největší výkon hit při provádění SSL dešifrování je počáteční handshake. Chcete-li zvýšit výkon, server, který provádí dešifrování, ukládá ID relací SSL a spravuje lístky relace TLS. Pokud se tak děje v bráně aplikace, všechny požadavky od stejného klienta můžete použít hodnoty uložené v mezipaměti. Pokud se provádí na serverech back-end, pak pokaždé, když požadavky klienta přejít na jiný server klient musí znovu ověřit. Použití lístků TLS může pomoci zmírnit tento problém, ale nejsou podporovány všemi klienty a může být obtížné konfigurovat a spravovat.
- **Lepší využití back-endových serverů** – zpracování SSL/TLS je velmi náročné na procesor a s růstem velikosti klíčů je stále intenzivnější. Odebrání této práce z back-endových serverů jim umožňuje zaměřit se na to, co jsou nejúčinnější, poskytování obsahu.
- **Inteligentní směrování** – Dešifrováním provozu má aplikační brána přístup k obsahu požadavku, jako jsou záhlaví, identifikátor URI a tak dále, a může tato data použít k směrování požadavků.
- **Správa certifikátů** – Certifikáty je potřeba zakoupit a nainstalovat pouze na aplikační bráně a ne všechny back-endové servery. To šetří čas i peníze.

Chcete-li nakonfigurovat ukončení SSL, je nutné přidat certifikát SSL do naslouchací proces, aby aplikační brána mohla odvodit symetrický klíč podle specifikace protokolu SSL. Symetrický klíč se pak používá k šifrování a dešifrování přenosů odeslaných do brány. Certifikát SSL musí být ve formátu PFX (Personal Information Exchange). Tento formát souboru umožňuje exportovat soukromý klíč, který je vyžadován aplikační bránou k provedení šifrování a dešifrování provozu.

> [!NOTE] 
>
> Aplikační brána neposkytuje žádnou možnost vytvořit nový certifikát nebo odeslat žádost o certifikát certifikačnímu úřadu.

Aby připojení SSL fungovalo, je třeba zajistit, aby certifikát SSL splňoval následující podmínky:

- Aktuální datum a čas je v rozsahu "Platné od" a "Platné do" na certifikátu.
- Běžný název certifikátu (CN) odpovídá hlavičce hostitele v požadavku. Například pokud klient posílá požadavek na `https://www.contoso.com/`, musí být CN `www.contoso.com`.

### <a name="certificates-supported-for-ssl-termination"></a>Certifikáty podporované pro ukončení ssl

Aplikační brána podporuje následující typy certifikátů:

- Certifikát certifikační autority: Certifikát certifikační autority je digitální certifikát vydaný certifikační autoritou (CA)
- Certifikát EV (Extended Validation): Certifikát EV je certifikát, který odpovídá standardním pokynům certifikátu. Tím se změní na lištu lokátoru prohlížeče zeleně a zveřejní se také název společnosti.
- Certifikát se zástupnými znaky: Tento certifikát podporuje libovolný počet subdomén na základě *.site.com, kde by subdoména nahradila *. Nepodporuje však site.com, takže v případě, že uživatelé přistupují k vašim webovým stránkům bez zadání úvodního "www", certifikát se zástupnými symboly se na to nevztahuje.
- Certifikáty podepsané svým držitelem: Prohlížeče klienta těmto certifikátům nedůvěřují a upozorní uživatele, že certifikát virtuální služby není součástí řetězce důvěryhodnosti. Certifikáty podepsané svým držitelem jsou vhodné pro testování nebo prostředí, kde správci řídí klienty a mohou bezpečně obejít výstrahy zabezpečení prohlížeče. Produkční úlohy by nikdy neměly používat certifikáty podepsané svým držitelem.

Další informace naleznete v [tématu konfigurace ukončení protokolu SSL pomocí brány aplikace](https://docs.microsoft.com/azure/application-gateway/create-ssl-portal).

### <a name="size-of-the-certificate"></a>Velikost osvědčení
V části [Omezení aplikační brány](https://docs.microsoft.com/azure/azure-resource-manager/management/azure-subscription-service-limits#application-gateway-limits) zjistěte, jaká je maximální podporovaná velikost certifikátu SSL.

## <a name="end-to-end-ssl-encryption"></a>Šifrování SSL od konce do konce

Někteří zákazníci nemusí chtít nešifrovanou komunikaci se servery back-end. Může to být z důvodu požadavků na zabezpečení nebo dodržování předpisů nebo protože aplikace může přijímat pouze zabezpečená připojení. Pro takové aplikace služba Application Gateway podporuje koncové šifrování protokolu SSL.

Koncové šifrování protokolu SSL umožňuje bezpečně přenášet citlivá data do back-endu v zašifrované podobě a současně využívat výhody funkcí pro vyrovnávání zatížení vrstvy 7, které nabízí aplikační brána. Jde například o spřažení relací na základě souborů cookie, směrování na základě adres URL, podporu směrování založeného na webech nebo možnost vkládat hlavičky X-Forwarded-*.

Když je nakonfigurována s režimem komunikace koncového šifrování protokolu SSL, služba Application Gateway ukončuje na bráně relace protokolu SSL a dešifruje provoz uživatelů. Následně použije nakonfigurovaná pravidla k výběru příslušné instance back-endového fondu, na kterou provoz přesměruje. Služba Application Gateway poté zahájí nové připojení SSL k back-endovému serveru a před odesláním požadavku do back-endu znovu zašifruje data pomocí certifikátu s veřejným klíčem back-endového serveru. Každá odpověď webového serveru prochází ke koncovému uživateli stejným procesem. Ssl od konce do konce je povoleno nastavením protokolu v [nastavení HTTP back-endu](https://docs.microsoft.com/azure/application-gateway/configuration-overview#http-settings) na protokol HTTPS, který se pak použije na back-endový fond.

Zásady SSL platí pro front-endový i back-endový provoz. Na front-endu funguje aplikační brána jako server a vynucuje zásady. V back-endu aplikace gateway funguje jako klient a odešle informace o protokolu/šifra jako předvolba během handshake SSL.

Aplikační brána komunikuje pouze s těmi instancemi back-endu, které buď zadali svůj certifikát na seznamu povolených, s aplikační bránou nebo jejichž certifikáty jsou podepsány známými autoritami certifikační autority, kde certifikát Cn odpovídá názvu hostitele v protokolu HTTP nastavení back-endu. Patří mezi ně důvěryhodné služby Azure, jako jsou webové aplikace služby Azure App service a Správa rozhraní API Azure.

Pokud certifikáty členů v back-endovém fondu nejsou podepsány známými autoritami certifikační autority, musí být každá instance v back-endovém fondu s povoleným protokolem SSL od konce nakonfigurována pomocí certifikátu, který umožní zabezpečenou komunikaci. Přidání certifikátu zajistí, že aplikační brána komunikuje pouze se známými back-endovými instancemi. To dále zabezpečuje komunikaci mezi koncovými soubory.

> [!NOTE] 
>
> Nastavení ověřovacího certifikátu není vyžadováno pro důvěryhodné služby Azure, jako jsou webové aplikace služby Azure App service a Správa rozhraní Azure API.

> [!NOTE] 
>
> Certifikát přidaný do **nastavení HTTP back-endu** k ověření serverů back-endu může být stejný jako certifikát přidaný k **naslouchací procesu** pro ukončení protokolu SSL v bráně aplikace nebo jiný pro rozšířené zabezpečení.

![scénář koncového šifrování protokolu ssl][1]

V tomto příkladu jsou požadavky, které používají šifrování TLS 1.2, přesměrované koncovým šifrováním protokolu SSL na back-endové servery fondu Pool1.

## <a name="end-to-end-ssl-and-whitelisting-of-certificates"></a>Koncové šifrování protokolu SSL a vytváření seznamu povolených certifikátů

Služba Application Gateway komunikuje pouze se známými back-endovými instancemi, jejichž certifikáty jsou uvedeny v seznamu povolených certifikátů ve službě Application Gateway. Chcete-li povolit vytváření seznamu povolených certifikátů, musíte do aplikační brány nahrát veřejný klíč certifikátů back-endového serveru (nikoliv kořenový certifikát). Potom budou povolena jenom připojení ke známým back-endům uvedeným v seznamu. Zbývající back-endy způsobí chybu brány. Certifikáty podepsané svým držitelem slouží pouze k testování a nedoporučují se pro úlohy v produkčním prostředí. Tyto certifikáty musí být uvedeny na seznamu povolených s bránou aplikace, jak je popsáno v předchozích krocích, aby mohly být použity.

> [!NOTE]
> Nastavení ověřovacího certifikátu není vyžadováno pro důvěryhodné služby Azure, jako je služba Azure App Service.

## <a name="end-to-end-ssl-with-the-v2-sku"></a>Konec na konec SSL s v2 Skladové položky

Ověřovací certifikáty byly zastaralé a nahrazeny důvěryhodnými kořenovými certifikáty ve skladové jednotce sku aplikace v2. Fungují podobně jako ověřovací certifikáty s několika klíčovými rozdíly:

- Certifikáty podepsané známými autoritami certifikační autority, jejichž název propojené sítě odpovídá názvu hostitele v nastavení back-endu HTTP, nevyžadují žádný další krok, aby protokol SSL ukončil. 

   Pokud jsou například back-endové certifikáty vydávány známou certifikační autoritou a má kn contoso.com a pole hostitele nastavení back-endu http je také nastaveno na contoso.com, nejsou nutné žádné další kroky. Můžete nastavit protokol nastavení back-endu http na protokol HTTPS a sonda stavu i datová cesta by byla povolena protokolem SSL. Pokud používáte Azure App Service nebo jiné webové služby Azure jako back-end, pak jsou implicitně důvěryhodné a žádné další kroky jsou nutné pro koncové až koncové SSL.
   
> [!NOTE] 
>
> Aby byl certifikát SSL důvěryhodný, musí být tento certifikát back-endového serveru vydán certifikační autoritou, která je součástí důvěryhodného úložiště brány aplikace.Pokud certifikát nebyl vydán důvěryhodným certifikačním systémem, bude aplikační brána zkontrolována chcete-li zjistit, zda byl certifikát vystavující certifikační autority vydán důvěryhodnou certifikační autoritou a tak dále, dokud nebude nalezen adůvěryhodná certifikační autorita (v tomto okamžiku bude navázáno důvěryhodné a zabezpečené připojení) nebo dokud nebude nalezena důvěryhodná certifikační autorita (v tomto okamžiku bude brána aplikace označovat back-end nezdravé). Proto se doporučuje, aby certifikát back-endového serveru obsahoval kořenové i zprostředkující certifikační autority.

- Pokud je certifikát podepsán svým držitelem nebo podepsán neznámými zprostředkovateli, musí být definován důvěryhodný kořenový certifikát, aby byl povolen ssl konec ve sku v2. Aplikační brána bude komunikovat pouze s back-endy, jejichž kořenový certifikát certifikátu serveru odpovídá jednomu ze seznamu důvěryhodných kořenových certifikátů v nastavení http back-endu přidruženém ke fondu.

> [!NOTE] 
>
> Certifikát podepsaný svým držitelem musí být součástí řetězu certifikátů. Jeden certifikát podepsaný svým držitelem bez řetězce není ve skladové jednotce V2 podporován.

- Kromě shody kořenového certifikátu aplikace gateway také ověří, zda nastavení Host zadané v nastavení http back-endu odpovídá nastavení běžného názvu (CN) prezentovaného certifikátem SSL serveru back-endu. Při pokusu o vytvoření připojení SSL k back-endu nastaví aplikační brána rozšíření SNI (SNI) na hostitele určeného v nastavení http back-endu.
- Pokud je místo pole Host v nastavení http back-endu **vybránnázev hostitele z back-endové adresy,** je hlavička SNI vždy nastavena na hlavní název back-endového fondu a cn na serveru SSL serveru musí odpovídat svému konečnému názvu na název sítě. Back-endu členové fondu s IP adresy nejsou podporovány v tomto scénáři.
- Kořenový certifikát je kořenový certifikát kódovaný na základně 64 z certifikátů back-endového serveru.

## <a name="next-steps"></a>Další kroky

Po naučení o ssl od konce přejděte na [Konfigurovat protokol SSL od konce k ukončení pomocí aplikační brány s prostředím PowerShell](application-gateway-end-to-end-ssl-powershell.md) k vytvoření aplikační brány pomocí ssl od konce k ukončení.

<!--Image references-->

[1]: ./media/ssl-overview/scenario.png
