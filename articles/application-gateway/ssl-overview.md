---
title: Povolení koncového šifrování protokolu SSL ve službě Azure Application Gateway
description: Tento článek představuje přehled služby Application Gateway podporovat kompletního protokolu SSL.
services: application-gateway
author: amsriva
ms.service: application-gateway
ms.topic: article
ms.date: 3/19/2019
ms.author: victorh
ms.openlocfilehash: 1259e755642563a7baad5496bc84ed736d5499f8
ms.sourcegitcommit: 16cb78a0766f9b3efbaf12426519ddab2774b815
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/17/2019
ms.locfileid: "65849809"
---
# <a name="overview-of-ssl-termination-and-end-to-end-ssl-with-application-gateway"></a>Přehled ukončení protokolu SSL a koncového šifrování protokolu SSL s aplikační bránou

Vrstva SSL (Secure Sockets) je standard zabezpečení technologie pro vytvoření šifrovaného spojení mezi webový server a prohlížečem. Tento odkaz se zajistí, že všechna data předaná mezi webovým serverem a prohlížeče zůstávají privátní a šifrovaná. Služba Application gateway podporuje obě ukončení protokolu SSL na bráně, jakož i koncové šifrování protokolu SSL.

## <a name="ssl-termination"></a>Ukončení protokolu SSL

Služba Application Gateway podporuje ukončení protokolu SSL na bráně, po čemž provoz typicky teče nešifrován na back-end serverů. Existuje mnoho výhod dělat ukončení protokolu SSL ve službě application gateway:

- **Vylepšený výkon** – největší výkon přenosů při dešifrování SSL je počáteční metody handshake. Kvůli zvýšení výkonu se server způsobem dešifrování ukládá do mezipaměti ID relace SSL a spravuje lístky relace TLS. Pokud to provedete ve službě application gateway, můžete použít všechny žádosti od stejného klienta hodnoty uložené v mezipaměti. Pokud se provádí v back-end serverů, pokaždé, když požadavky klienta přejít k jinému serveru klienta má re‑authenticate. Použití protokolu TLS lístků může pomoci tento problém zmírnit, ale nepodporují všichni klienti a může být obtížné konfigurovat a spravovat.
- **Lepší využití back-end serverů** – zpracování SSL/TLS velmi náročné na CPU a je stále více náročné zvýšení velikosti klíče. Odstranění této práce z back-end serverů jim umožní zaměřit se na co je nejúčinnější při doručování obsahu.
- **Inteligentní směrování** – dešifrováním provoz application gateway má přístup k obsahu požadavku, jako je například záhlaví, identifikátor URI a tak dále a může pomocí těchto dat pro směrování požadavků.
- **Správa certifikátů** – certifikáty stačí být zakoupen a nainstalován na aplikační bránu a ne všechny back-end serverů. Tím ušetříte čas i peníze.

Ke konfiguraci ukončení protokolu SSL, certifikát SSL je potřeba přidat k naslouchacímu procesu Povolit aplikační brány pro odvození symetrického klíče podle specifikace protokolu SSL. Symetrický klíč se pak používá k šifrování a dešifrování provoz odeslaný na bránu. Certifikát SSL musí být ve formátu Personal Information Exchange (PFX). Tento formát souboru umožňuje exportovat soukromý klíč, který vyžaduje službu application gateway šifrování a dešifrování přenosů.

> [!NOTE] 
>
> Služba Application gateway neposkytuje žádné možnosti vytvořit nový certifikát nebo odeslat žádost o certifikát u certifikační autority.

Pro připojení SSL pro práci je potřeba zajistit, že certifikát SSL splňuje následující podmínky:

- Aktuální datum a čas je v rámci "Platnost od" a rozsah dat "Platí pro" na certifikátu.
- Běžný název certifikátu (CN) odpovídá hlavičce hostitele v požadavku. Například pokud klient posílá požadavek na `https://www.contoso.com/`, musí být CN `www.contoso.com`.

### <a name="certificates-supported-for-ssl-termination"></a>Certifikáty, které jsou podporovány pro ukončení protokolu SSL

Služba Application gateway podporuje následující typy certifikátů:

- Certifikát CA (certifikační autorita): Certifikát certifikační Autority je digitální certifikát vydaný certifikační autoritou (CA)
- Certifikát Rozšířeného (rozšířeného ověření): Certifikát Rozšířeného je standardní certifikát pokyny oboru. Se změní na zelenou Lokátor prohlížeče a publikovat také název společnosti.
- Certifikát se zástupným znakem: Tento certifikát podporuje libovolný počet subdomény na základě *. site.com, kde by byly nahrazeny vaše subdomény *. Ale nepodporuje site.com, takže v případě, že uživatelé jsou přístup k webu, aniž by museli zadávat úvodního "www", certifikát se zástupným znakem nebudeme se zabývat, který.
- Certifikáty podepsané svým držitelem: Klientský prohlížeč nepovažujete tyto certifikáty a upozorní uživatele, že certifikát virtuální služby není součástí řetěz certifikátů. Certifikáty podepsané svým držitelem jsou vhodné pro testování a prostředí, kde správci řídit klienti mohou bezpečně obejít upozornění zabezpečení prohlížeče. Úlohy v produkčním prostředí byste nikdy neměli používat certifikáty podepsané svým držitelem.

Další informace najdete v tématu [konfiguraci ukončení protokolu SSL s aplikační bránou](https://docs.microsoft.com/azure/application-gateway/create-ssl-portal).

### <a name="size-of-the-certificate"></a>Velikost certifikátu
Soubor Personal Information Exchange (PFX) s informacemi o certifikátu SSL by neměl být více než 10 KB.

## <a name="end-to-end-ssl-encryption"></a>Koncové šifrování protokolu SSL

Někteří zákazníci nemusí mít nešifrovaná komunikace s back-end serverů. Může to být z důvodu požadavků na zabezpečení nebo dodržování předpisů nebo protože aplikace může přijímat pouze zabezpečená připojení. Pro takové aplikace služba Application Gateway podporuje koncové šifrování protokolu SSL.

Koncové šifrování protokolu SSL umožňuje bezpečně přenášet citlivá data do back-endu v zašifrované, zatímco stále využívat výhody funkcí vyrovnávání zatížení vrstvy 7 které služba application gateway poskytuje. Jde například o spřažení relací na základě souborů cookie, směrování na základě adres URL, podporu směrování založeného na webech nebo možnost vkládat hlavičky X-Forwarded-*.

Když je nakonfigurována s režimem komunikace koncového šifrování protokolu SSL, služba Application Gateway ukončuje na bráně relace protokolu SSL a dešifruje provoz uživatelů. Následně použije nakonfigurovaná pravidla k výběru příslušné instance back-endového fondu, na kterou provoz přesměruje. Služba Application Gateway poté zahájí nové připojení SSL k back-endovému serveru a před odesláním požadavku do back-endu znovu zašifruje data pomocí certifikátu s veřejným klíčem back-endového serveru. Každá odpověď webového serveru prochází ke koncovému uživateli stejným procesem. Koncové šifrování protokolu SSL se povoluje nastavením nastavení protokolu [nastavení HTTP back-endu](https://docs.microsoft.com/azure/application-gateway/configuration-overview#http-settings) na protokol HTTPS, který se následně použije na back-endový fond.

Zásady protokolu SSL se vztahuje na front-endových a back-endu provoz. Na front-endu služba Application Gateway funguje jako server a vynutí zásady. Na back-endu služba Application Gateway funguje jako klient a odešle informace protokolu a šifer jako předvolba během metody handshake SSL.

Služba Application gateway komunikuje pouze se tyto back-end instance, které mají buď přidat na seznam povolených svého certifikátu s aplikační bránou nebo jejichž certifikáty jsou podepsané dobře známé Certifikační autority kde název CN certifikátu odpovídá názvu hostitele http nastavení back-endu. Patří mezi důvěryhodné služby Azure, například Azure App service web apps a Azure API Management.

V případě nejsou dobře známé Certifikační autority podepsané certifikáty členy v back-endový fond, každá instance v back-endový fond s kompletního protokolu SSL povoleno musí být nakonfigurovaná s certifikátem pro umožnění bezpečné komunikace. Přidání certifikátu se zajistí, že službu application gateway komunikuje pouze se známými back endových instancí. To dále zabezpečuje komunikaci začátku do konce.

> [!NOTE] 
>
> Instalace certifikátu ověřování se nevyžaduje pro důvěryhodného služby Azure, jako je například Azure App service web apps a Azure API Management.

> [!NOTE] 
>
> Certifikát přidaný do **nastavení HTTP back-endu** ověřování back-end servery mohou být stejné jako na certifikát přidat do **naslouchací proces** pro ukončení protokolu SSL ve službě application gateway nebo jiné pro zvýšené zabezpečení.

![scénář koncového šifrování protokolu ssl][1]

V tomto příkladu jsou požadavky, které používají šifrování TLS 1.2, přesměrované koncovým šifrováním protokolu SSL na back-endové servery fondu Pool1.

## <a name="end-to-end-ssl-and-whitelisting-of-certificates"></a>Koncové šifrování protokolu SSL a vytváření seznamu povolených certifikátů

Služba Application Gateway komunikuje pouze se známými back-endovými instancemi, jejichž certifikáty jsou uvedeny v seznamu povolených certifikátů ve službě Application Gateway. Chcete-li povolit vytváření seznamu povolených certifikátů, musíte do aplikační brány nahrát veřejný klíč certifikátů back-endového serveru (nikoliv kořenový certifikát). Potom budou povolena jenom připojení ke známým back-endům uvedeným v seznamu. Zbývající back-endy způsobí chybu brány. Certifikáty podepsané svým držitelem slouží pouze k testování a nedoporučují se pro úlohy v produkčním prostředí. Tyto certifikáty musí být povolené ve službě application gateway popsané v předchozích krocích předtím, než je možné.

> [!NOTE]
> Instalace certifikátu ověřování se nevyžaduje pro důvěryhodného služby Azure, jako je Azure App Service.

## <a name="end-to-end-ssl-with-the-v2-sku"></a>Koncové šifrování protokolu SSL s v2 SKU

Ověřovací certifikáty byly zastaralé a nahrazují důvěryhodných kořenových certifikátů ve službě Application Gateway SKU v2. Fungují podobně jako ověřovací certifikáty s několik klíčových rozdílů:

- Certifikáty podepsané dobře známé Certifikační autority jehož CN odpovídá názvu hostitele v nastavení HTTP back-endu nevyžadují žádné další krok pro kompletního protokolu SSL pro práci. 

   Například pokud společnost dobře známé certifikační Autorita vydá nové certifikáty back-endu a má CN contoso.com a pole nastavení http back-endu hostitele je také nastavena na contoso.com, pak nejsou žádné další kroky požadované. Můžete nastavit back-endu http nastavení protokolu HTTPS a oba stavu sondy cesta bude protokol SSL povolený. Pokud používáte službu Azure App Service nebo jiné služby Azure web jako back-endu, ty jsou také implicitně důvěryhodné a žádné další kroky jsou požadovány pro kompletního protokolu SSL.
- Pokud certifikát podepsaný svým držitelem nebo podepsány Neznámý prostředníci, pak k povolení koncového šifrování protokolu SSL v SKU v2 pro důvěryhodného kořenového certifikátu musí být definovaný. Aplikační brána bude komunikovat jenom s back-EndY certifikát serveru, jehož kořenový certifikát odpovídá jednomu z seznam důvěryhodných kořenových certifikátů v nastavení http back-endu přidružené k fondu.
- Kromě shodu kořenový certifikát služba Application Gateway také ověří, jestli se hostitel nastavení zadaného v nastavení http back-end se shoduje s běžného názvu (CN) předložený certifikát SSL back-endového serveru. Při pokusu o navázání připojení protokolem SSL k back-endu, nastaví služba Application Gateway rozšíření indikace názvu serveru (SNI) hostitel zadaný v nastavení http back-endu.
- Pokud **vybrat název hostitele z back-endová adresa** je vybrán místo pole Host v nastavení http back-end, pak záhlaví SNI je vždycky nastavený na back-endový fond, plně kvalifikovaný název domény a CN na back-end serveru SSL certifikát se musí shodovat jeho plně kvalifikovaný název domény. V tomto scénáři nejsou podporovány členů fondu back-end s IP adresami.
- Kořenový certifikát je ve formátu base64 kódovaného kořenový certifikát z certifikátů back-endového serveru.

## <a name="next-steps"></a>Další postup

Po získání informací o kompletního protokolu SSL, přejděte na [konfigurace kompletního protokolu SSL pomocí Application Gateway pomocí Powershellu](application-gateway-end-to-end-ssl-powershell.md) k vytvoření služby application gateway používající kompletního protokolu SSL.

<!--Image references-->

[1]: ./media/ssl-overview/scenario.png
