---
title: Omezení adresy URI přesměrování (adresa URL odpovědi) | Azure
titleSuffix: Microsoft identity platform
description: Popis omezení a omezení pro formát identifikátoru URI přesměrování (adresa URL odpovědi) vynutilý platformou Microsoft identity
author: SureshJa
ms.author: sureshja
manager: CelesteDG
ms.date: 11/23/2020
ms.topic: conceptual
ms.subservice: develop
ms.custom: aaddev
ms.service: active-directory
ms.reviewer: marsma, lenalepa, manrath
ms.openlocfilehash: 30ea74b249937544a0bf9811cad60f02c1ca45c7
ms.sourcegitcommit: 1bf144dc5d7c496c4abeb95fc2f473cfa0bbed43
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/24/2020
ms.locfileid: "95752778"
---
# <a name="redirect-uri-reply-url-restrictions-and-limitations"></a>Omezení a omezení URI pro přesměrování (adresa URL odpovědi)

Identifikátor URI pro přesměrování nebo adresa URL odpovědi je umístění, kde autorizační Server odešle uživatele po úspěšném autorizaci a udělení autorizačního kódu nebo přístupového tokenu. Autorizační server pošle kód nebo token do identifikátoru URI přesměrování, takže je důležité, abyste v rámci procesu registrace aplikace zaregistrovali správné umístění.

 Pro identifikátory URI přesměrování platí následující omezení:

* Identifikátor URI přesměrování musí začínat schématem `https` . Pro identifikátory URI přesměrování [localhost existují výjimky](#localhost-exceptions) .

* Identifikátor URI pro přesměrování rozlišuje velká a malá písmena. Jeho velikost se musí shodovat s písmenem adresy URL vaší běžící aplikace. Například pokud vaše aplikace obsahuje jako součást cesty `.../abc/response-oidc` , nezadávejte `.../ABC/response-oidc` v identifikátoru URI přesměrování. Vzhledem k tomu, že webový prohlížeč považuje cesty jako rozlišování velkých a malých písmen, mohou být soubory cookie přidružené k `.../abc/response-oidc` vyloučeny při přesměrování na neshodnou `.../ABC/response-oidc` adresu URL.

## <a name="maximum-number-of-redirect-uris"></a>Maximální počet identifikátorů URI pro přesměrování

Tato tabulka uvádí maximální počet identifikátorů URI přesměrování, které můžete přidat do registrace aplikace na platformě Microsoft identity.

| Přihlášené účty | Maximální počet identifikátorů URI pro přesměrování | Popis |
|--------------------------|---------------------------------|-------------|
| Pracovní nebo školní účty Microsoftu v tenantovi Azure Active Directory v organizaci (Azure AD) | 256 | `signInAudience` pole v manifestu aplikace je nastaveno na hodnotu *AzureADMyOrg* nebo *AzureADMultipleOrgs* |
| Osobní účty Microsoft a pracovní a školní účty | 100 | `signInAudience` pole v manifestu aplikace je nastaveno na *AzureADandPersonalMicrosoftAccount* |

## <a name="maximum-uri-length"></a>Maximální délka identifikátoru URI

Pro každý identifikátor URI přesměrování, který přidáte do registrace aplikace, můžete použít maximálně 256 znaků.

## <a name="supported-schemes"></a>Podporovaná schémata

Aplikační model Azure Active Directory (Azure AD) aktuálně podporuje schémata HTTP i HTTPS pro aplikace, které přihlásily pracovní nebo školní účty v tenantovi Azure AD organizace. Tyto typy účtů jsou určeny `AzureADMyOrg` `AzureADMultipleOrgs` hodnotami a v `signInAudience` poli manifestu aplikace. Pro aplikace, které přihlásily osobní účty Microsoft (MSA) *a* pracovní a školní účty (tj `signInAudience` . je nastavené na `AzureADandPersonalMicrosoftAccount` ), je povolené jenom schéma https.

Pokud chcete přidat identifikátory URI pro přesměrování se schématem HTTP do registrací aplikací, které přihlásily pracovní nebo školní účty, musíte použít editor manifestu aplikace v [Registrace aplikací](https://go.microsoft.com/fwlink/?linkid=2083908) Azure Portal. Přestože je ale možné nastavit identifikátor URI přesměrování založený na protokolu HTTP pomocí editoru manifestu, *důrazně* doporučujeme, abyste pro identifikátory URI přesměrování používali schéma https.

## <a name="localhost-exceptions"></a>Výjimky localhost

V [bodech RFC 8252, 8,3](https://tools.ietf.org/html/rfc8252#section-8.3) a [7,3](https://tools.ietf.org/html/rfc8252#section-7.3), se identifikátory URI pro přesměrování "Loopback" nebo "localhost" dodávají se dvěma speciálními požadavky:

1. `http` Schémata identifikátorů URI jsou přijatelná, protože přesměrování zařízení nikdy neopouští. V takovém případě jsou oba tyto identifikátory URI přijatelné:
    - `http://localhost/myApp`
    - `https://localhost/myApp`
1. Vzhledem k dočasným rozsahům portů, které jsou často vyžadovány nativními aplikacemi, je součást portu (například `:5001` nebo `:443` ) ignorována pro účely porovnání identifikátoru URI přesměrování. V důsledku toho jsou všechny tyto identifikátory URI považovány za ekvivalentní:
    - `http://localhost/MyApp`
    - `http://localhost:1234/MyApp`
    - `http://localhost:5000/MyApp`
    - `http://localhost:8080/MyApp`

Z pohledu vývoje to znamená několik věcí:

* Neregistrujte více identifikátorů URI přesměrování, přičemž se liší pouze port. Přihlašovací server vybere jednu z nich a použije chování spojené s tímto identifikátorem URI přesměrování (například bez ohledu na to, zda se jedná o `web` `native` přesměrování typu a, nebo `spa` ).

    To je obzvláště důležité, pokud chcete použít různé toky ověřování ve stejné registraci aplikace, například udělení autorizačního kódu i implicitní tok. Aby bylo možné přidružit správné chování odpovědí každému identifikátoru URI přesměrování, musí mít přihlašovací server schopnost rozlišovat identifikátory URI pro přesměrování a nemůže tak učinit, pokud se liší pouze port.
* Pokud potřebujete zaregistrovat více identifikátorů URI pro přesměrování na localhost pro testování různých toků během vývoje, rozlišit je pomocí komponenty *cesty* identifikátoru URI. Například `http://localhost/MyWebApp` neodpovídá `http://localhost/MyNativeApp` .
* Adresa zpětné smyčky IPv6 ( `[::1]` ) se momentálně nepodporuje.

#### <a name="prefer-127001-over-localhost"></a>Preferovat 127.0.0.1 přes localhost

Pokud chcete zabránit tomu, aby se vaše aplikace přerušila pomocí nesprávně nakonfigurovaných bran firewall nebo přejmenovaných síťových rozhraní, místo použijte adresu zpětné smyčky IP adresy `127.0.0.1` v identifikátoru URI přesměrování `localhost` . Například, `https://127.0.0.1`.

Nemůžete však použít textové pole pro **přesměrování identifikátorů URI** v Azure Portal k přidání identifikátoru URI přesměrování založeného na zpětné smyčce, který používá `http` schéma:

:::image type="content" source="media/reply-url/portal-01-no-http-loopback-redirect-uri.png" alt-text="Chybový dialog v Azure Portal zobrazuje nepovolený identifikátor URI přesměrování zpětné smyčky na základě protokolu http":::

Chcete-li přidat identifikátor URI pro přesměrování, který používá `http` schéma s `127.0.0.1` adresou zpětné smyčky, je nutné v [manifestu aplikace](reference-app-manifest.md)v současné době Upravit atribut [replyUrlsWithType](reference-app-manifest.md#replyurlswithtype-attribute) .

## <a name="restrictions-on-wildcards-in-redirect-uris"></a>Omezení zástupných znaků v identifikátorech URI pro přesměrování

Zástupné identifikátory URI `https://*.contoso.com` , jako by se mohly zdát pohodlné, ale měly by se vyhýbat z důvodu důsledků zabezpečení. V souladu se specifikací OAuth 2,0 ([oddíl 3.1.2 dokumentu RFC 6749](https://tools.ietf.org/html/rfc6749#section-3.1.2)) musí být identifikátor URI koncového bodu přesměrování ABSOLUTNÍm identifikátorem URI.

Zástupné identifikátory URI se v současné době nepodporují v registracích aplikací nakonfigurovaných pro přihlášení k osobním účtům Microsoft a pracovním nebo školním účtům. Pro aplikace, které jsou nakonfigurované tak, aby se přihlásily pouze pracovní nebo školní účty v tenantovi Azure AD organizace, jsou povolené zástupné identifikátory URI.

Chcete-li přidat identifikátory URI pro přesměrování se zástupnými znaky k registrům aplikací, které přihlásí pracovní nebo školní účty, je nutné použít editor manifestu aplikace v [Registrace aplikací](https://go.microsoft.com/fwlink/?linkid=2083908) Azure Portal. I když je možné nastavit identifikátor URI pro přesměrování se zástupným znakem pomocí editoru manifestu, *důrazně* doporučujeme, abyste dodržovali [oddíl 3.1.2 RFC 6749](https://tools.ietf.org/html/rfc6749#section-3.1.2) a používali pouze absolutní identifikátory URI.

Pokud váš scénář vyžaduje víc identifikátorů URI pro přesměrování, než je maximální povolený limit, zvažte následující [přístup k parametrům stavu](#use-a-state-parameter) místo přidávání identifikátoru URI pro přesměrování zástupných znaků.

#### <a name="use-a-state-parameter"></a>Použít parametr stavu

Pokud máte několik subdomén a váš scénář vyžaduje, abyste po úspěšném ověření přesměrovali uživatele na stejnou stránku, ze které začala, použití parametru State může být užitečné.

V tomto postupu:

1. Vytvořte "sdílený" identifikátor URI pro přesměrování na aplikaci pro zpracování tokenů zabezpečení, které obdržíte z koncového bodu autorizace.
1. Vaše aplikace může odeslat parametry specifické pro aplikaci (například adresu URL subdomény, kde uživatel vznikl nebo cokoli jako informace o brandingu) v parametru State. Při použití parametru State Guard chránit před CSRF ochranou, jak je uvedeno v [části 10,12 dokumentu RFC 6749](https://tools.ietf.org/html/rfc6749#section-10.12).
1. Parametry specifické pro aplikaci budou zahrnovat všechny informace potřebné k tomu, aby aplikace mohla vykreslovat správné prostředí pro uživatele, tedy sestavit příslušný stav aplikace. Koncový bod autorizace Azure AD z parametru State vyřadí pruhy HTML, takže v tomto parametru nebudete předávat obsah HTML.
1. Když Azure AD pošle odpověď na "sdílený" identifikátor URI přesměrování, pošle parametr stavu zpátky do aplikace.
1. Aplikace pak může použít hodnotu v parametru State k určení, na kterou adresu URL má uživatel dál odeslat. Ověřte, že jste ověřili CSRF Protection.

> [!WARNING]
> Tento přístup umožňuje ohroženému klientovi upravit další parametry odeslané v parametru State a tím přesměrovat uživatele na jinou adresu URL, což je [otevřená hrozba přesměrovače](https://tools.ietf.org/html/rfc6819#section-4.2.4) , která je popsaná v dokumentu RFC 6819. Klient proto musí tyto parametry chránit šifrováním stavu nebo jeho ověřením jiným způsobem, jako je například ověření názvu domény v identifikátoru URI přesměrování proti tokenu.

## <a name="next-steps"></a>Další kroky

Přečtěte si o [manifestu aplikace](reference-app-manifest.md)pro registraci aplikací.
