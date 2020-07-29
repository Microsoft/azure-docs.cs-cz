---
title: Omezení adresy URL pro přesměrování & odpovědi URL – Microsoft Identity Platform | Azure
description: Omezení adres URL odpovědí/adres URL pro přesměrování & omezení
author: SureshJa
ms.author: sureshja
manager: CelesteDG
ms.date: 07/17/2020
ms.topic: conceptual
ms.subservice: develop
ms.custom: aaddev
ms.service: active-directory
ms.reviewer: lenalepa, manrath
ms.openlocfilehash: 4fdeb0018e27a2557161b2ec1c4794d975403523
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/28/2020
ms.locfileid: "87311615"
---
# <a name="redirect-urireply-url-restrictions-and-limitations"></a>Omezení pro adresu URL odpovědi / identifikátor URI přesměrování

Identifikátor URI pro přesměrování nebo adresa URL odpovědi je umístění, do kterého bude autorizační server odesílat uživatele po úspěšném ověření aplikace a udělení autorizačního kódu nebo přístupového tokenu. Kód nebo token jsou obsaženy v identifikátoru URI přesměrování nebo tokenu odpovědi, takže je důležité, abyste v rámci procesu registrace aplikace zaregistrovali správné umístění.

 Pro adresy URL odpovědí platí následující omezení:

* Adresa URL odpovědi musí začínat schématem `https` .

* Adresa URL odpovědi rozlišuje velká a malá písmena. Jeho velikost se musí shodovat s písmenem adresy URL vaší běžící aplikace. Například pokud vaše aplikace obsahuje jako součást cesty `.../abc/response-oidc` , nezadávejte `.../ABC/response-oidc` v adrese URL odpovědi. Vzhledem k tomu, že webový prohlížeč považuje cesty jako rozlišování velkých a malých písmen, mohou být soubory cookie přidružené k `.../abc/response-oidc` vyloučeny při přesměrování na neshodnou `.../ABC/response-oidc` adresu URL.
    
## <a name="maximum-number-of-redirect-uris"></a>Maximální počet identifikátorů URI pro přesměrování

Následující tabulka uvádí maximální počet identifikátorů URI přesměrování, které můžete přidat při registraci aplikace.

| Přihlášené účty | Maximální počet identifikátorů URI pro přesměrování | Popis |
|--------------------------|---------------------------------|-------------|
| Pracovní nebo školní účty Microsoftu v tenantovi Azure Active Directory v organizaci (Azure AD) | 256 | `signInAudience`pole v manifestu aplikace je nastaveno na hodnotu *AzureADMyOrg* nebo *AzureADMultipleOrgs* |
| Osobní účty Microsoft a pracovní a školní účty | 100 | `signInAudience`pole v manifestu aplikace je nastaveno na *AzureADandPersonalMicrosoftAccount* |

## <a name="maximum-uri-length"></a>Maximální délka identifikátoru URI

Pro každý identifikátor URI přesměrování, který přidáte do registrace aplikace, můžete použít maximálně 256 znaků.

## <a name="supported-schemes"></a>Podporovaná schémata

Aplikační model Azure AD dnes podporuje schémata HTTP i HTTPS pro aplikace, které přinášejí pracovní nebo školní účty Microsoftu v tenantovi Azure Active Directory v organizaci (Azure AD). To je `signInAudience` pole v manifestu aplikace je nastaveno na hodnotu *AzureADMyOrg* nebo *AzureADMultipleOrgs*. Pro aplikace, které přihlásily osobní účty Microsoft a pracovní a školní účty ( `signInAudience` nastavené na *AzureADandPersonalMicrosoftAccount*), je povolené jenom schéma https.

> [!NOTE]
> Nové prostředí [Registrace aplikací](https://go.microsoft.com/fwlink/?linkid=2083908) neumožňuje vývojářům přidávat identifikátory URI do schématu http na uživatelském rozhraní. Přidávání identifikátorů URI HTTP pro aplikace, které přinášejí pracovní nebo školní účty, se podporuje jenom přes editor manifestu aplikace. Díky dál nebudou nové aplikace moct používat schémata HTTP v identifikátoru URI přesměrování. Starší aplikace, které obsahují schémata HTTP v identifikátorech URI pro přesměrování, budou i nadále fungovat. Vývojáři musí v identifikátorech URI přesměrování používat schémata protokolu HTTPS.

## <a name="localhost-exceptions"></a>Výjimky localhost

V [bodech RFC 8252, 8,3](https://tools.ietf.org/html/rfc8252#section-8.3) a [7,3](https://tools.ietf.org/html/rfc8252#section-7.3), se identifikátory URI pro přesměrování "Loopback" nebo "localhost" dodávají se dvěma speciálními požadavky:

1. `http`Schémata identifikátorů URI jsou přijatelná, protože přesměrování zařízení nikdy neopouští.  To znamená `http://127.0.0.1/myApp` , že to je přijatelné a také `https://127.0.0.1/myApp` . 
1. Vzhledem k dočasným rozsahům portů, které jsou často vyžadovány nativními aplikacemi, je součást portu (například `:5001` nebo `:443` ) ignorována pro účely porovnání identifikátoru URI přesměrování.  V důsledku toho se `http://127.0.0.1:5000/MyApp` `http://127.0.0.1:1234/MyApp` obě shodují i `http://127.0.0.1/MyApp``http://127.0.0.1:8080/MyApp`

Z pohledu vývoje to znamená několik věcí:

1. Neregistrujte více identifikátorů URI odpovědi, které se liší pouze portem.  Přihlašovací server vybere jednu hodinu a použije chování přidružené k danému identifikátoru URI odpovědi (například bez ohledu na to, jestli se jedná `web` o `native` `spa` přesměrování typu, a.
1. Pokud potřebujete zaregistrovat více identifikátorů URI pro přesměrování na localhost pro testování různých toků během vývoje, rozlišit je pomocí komponenty *cesty* identifikátoru URI.  `http://127.0.0.1/MyWebApp`neodpovídá `http://127.0.0.1/MyNativeApp` .  
1. Pokyny pro RFC byste neměli používat `localhost` v identifikátoru URI přesměrování.  Místo toho použijte skutečnou IP adresu zpětné smyčky `127.0.0.1` . Tím se zabrání v přerušení vaší aplikace nenakonfigurovanými branami firewall nebo přejmenovanými síťovými rozhraními.

>[!NOTE]
> V současné době není `[::1]` v tuto chvíli podporována smyčka IPv6 ().  Přidá se k pozdějšímu datu.

## <a name="restrictions-using-a-wildcard-in-uris"></a>Omezení používající zástupné znaky v identifikátorech URI

Zástupné identifikátory URI, například `https://*.contoso.com` , jsou pohodlné, ale měly by se vyhnout. Použití zástupných znaků v identifikátoru URI přesměrování má vliv na zabezpečení. V souladu se specifikací OAuth 2,0 ([oddíl 3.1.2 dokumentu RFC 6749](https://tools.ietf.org/html/rfc6749#section-3.1.2)) musí být identifikátor URI koncového bodu přesměrování ABSOLUTNÍm identifikátorem URI.

Aplikační model Azure AD nepodporuje zástupné identifikátory URI pro aplikace, které jsou nakonfigurované k podepisování osobních účtů Microsoft a pracovních nebo školních účtů. Pro aplikace, které jsou nakonfigurované na pracovní nebo školní účty v tenantovi Azure AD ve vaší organizaci, ale jsou povolené identifikátory URI se zástupnými znaky.

> [!NOTE]
> Nové prostředí [Registrace aplikací](https://go.microsoft.com/fwlink/?linkid=2083908) neumožňuje vývojářům přidávat v uživatelském rozhraní identifikátory URI se zástupnými znaky. Přidání zástupný znak URI pro aplikace, které přihlašování pracovní nebo školní účty jsou podporované jenom prostřednictvím editoru manifestu aplikace. Až dál, nové aplikace nebudou moct v identifikátoru URI přesměrování používat zástupné znaky. Starší aplikace, které obsahují zástupné znaky v identifikátorech URI pro přesměrování, ale budou fungovat i nadále.

Pokud váš scénář vyžaduje víc identifikátorů URI přesměrování, než je maximální povolený limit, místo přidání identifikátoru URI pro přesměrování zástupného znaku zvažte následující postup.

### <a name="use-a-state-parameter"></a>Použít parametr stavu

Pokud máte řadu subdomén a v případě, že váš scénář vyžaduje, abyste uživatele po úspěšném ověření přesměrovali na stejnou stránku, kde bylo zahájeno, může být užitečné použít parametr State.

V tomto postupu:

1. Vytvořte "sdílený" identifikátor URI pro přesměrování na aplikaci pro zpracování tokenů zabezpečení, které obdržíte z koncového bodu autorizace.
1. Vaše aplikace může odeslat parametry specifické pro aplikaci (například adresu URL subdomény, kde uživatel vznikl nebo cokoli jako informace o brandingu) v parametru State. Při použití parametru State Guard chránit před CSRF ochranou, jak je uvedeno v [části 10,12 dokumentu RFC 6749](https://tools.ietf.org/html/rfc6749#section-10.12). 
1. Parametry specifické pro aplikaci budou zahrnovat všechny informace potřebné k tomu, aby aplikace mohla vykreslovat správné prostředí pro uživatele, tedy sestavit příslušný stav aplikace. Koncový bod autorizace Azure AD z parametru State vyřadí pruhy HTML, takže v tomto parametru nebudete předávat obsah HTML.
1. Když Azure AD pošle odpověď na "sdílený" identifikátor URI přesměrování, pošle parametr stavu zpátky do aplikace.
1. Aplikace pak může použít hodnotu v parametru State k určení, na kterou adresu URL má uživatel dál odeslat. Ověřte, že jste ověřili CSRF Protection.

> [!NOTE]
> Tento přístup umožňuje ohroženému klientovi upravit další parametry odeslané v parametru State a tím přesměrovat uživatele na jinou adresu URL, což je [otevřená hrozba přesměrovače](https://tools.ietf.org/html/rfc6819#section-4.2.4) , která je popsaná v dokumentu RFC 6819. Klient proto musí tyto parametry chránit šifrováním stavu nebo jeho ověřením jiným způsobem, jako je například ověření názvu domény v identifikátoru URI přesměrování proti tokenu.

## <a name="next-steps"></a>Další kroky

- Další informace o [manifestu aplikace](reference-app-manifest.md)
