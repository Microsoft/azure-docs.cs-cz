---
title: Přesměrování identifikátoru URI & omezení adres URL odpovědi – platforma identit společnosti Microsoft | Azure
description: Omezení adres URL/přesměrování adres URL odpovědí & omezení
author: SureshJa
ms.author: sureshja
manager: CelesteDG
ms.date: 06/29/2019
ms.topic: conceptual
ms.subservice: develop
ms.custom: aaddev
ms.service: active-directory
ms.reviewer: lenalepa, manrath
ms.openlocfilehash: 8fdc64632be8b5fcb3dca8de2ee833fef25719fe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77656734"
---
# <a name="redirect-urireply-url-restrictions-and-limitations"></a>Omezení pro adresu URL odpovědi / identifikátor URI přesměrování

Identifikátor URI přesměrování nebo adresa URL odpovědi je umístění, do kterého autorizační server odešle uživatele, jakmile bude aplikace úspěšně autorizována, a udělil autorizační kód nebo přístupový token. Kód nebo token je obsažen v identifikátoru URI přesměrování nebo tokenu odpovědi, takže je důležité zaregistrovat správné umístění jako součást procesu registrace aplikace.

 Pro adresy URL odpovědí platí následující omezení:

    * Adresa URL odpovědi musí `https`začínat schématem .
    * Adresa URL odpovědi rozlišuje malá a velká písmena. Jeho případ musí odpovídat případu cesty URL spuštěné aplikace. Pokud například aplikace obsahuje jako součást `.../abc/response-oidc`své cesty `.../ABC/response-oidc` , nezadávejte v adrese URL odpovědi. Vzhledem k tomu, že webový prohlížeč považuje `.../abc/response-oidc` cesty za rozlišující malá a velká `.../ABC/response-oidc` písmena, mohou být soubory cookie přidružené k případu vyloučeny, pokud jsou přesměrovány na adresu URL neodpovídající případ.
    
## <a name="maximum-number-of-redirect-uris"></a>Maximální počet identifikátorů URI přesměrování

V následující tabulce je uveden maximální počet identifikátorů URI přesměrování, které můžete přidat při registraci aplikace.

| Účty, které jsou přihlášeny | Maximální počet identifikátorů URI přesměrování | Popis |
|--------------------------|---------------------------------|-------------|
| Pracovní nebo školní účty Microsoftu v tenantovi Azure Active Directory (Azure AD) libovolné organizace | 256 | `signInAudience`pole v manifestu aplikace je nastaveno na *AzureADMyOrg* nebo *AzureADMultipleOrgs* |
| Osobní účty Microsoft a pracovní a školní účty | 100 | `signInAudience`pole v manifestu aplikace je nastaveno na *AzureADandPersonalMicrosoftAccount* |

## <a name="maximum-uri-length"></a>Maximální délka identifikátoru URI

Pro každý identifikátor URI přesměrování, který přidáte do registrace aplikace, můžete použít maximálně 256 znaků.

## <a name="supported-schemes"></a>Podporované režimy
Model aplikace Azure AD dnes podporuje schémata HTTP i HTTPS pro aplikace, které se přihlašují k pracovním nebo školním účtům Microsoftu v tenantovi Azure Active Directory (Azure AD) libovolné organizace. To `signInAudience` je pole v manifestu aplikace je nastavena na *AzureADMyOrg* nebo *AzureADMultipleOrgs*. Pro aplikace, které se přihlašují osobní `signInAudience` účty Microsoft a pracovní a školní účty (která je nastavena na *AzureADandPersonalMicrosoftAccount*) je povoleno pouze schéma HTTPS.

> [!NOTE]
> Nové prostředí registrace aplikací neumožňuje vývojářům přidávat [identifikátory](https://go.microsoft.com/fwlink/?linkid=2083908) URI s http schématem v uživatelském rozhraní. Přidání identifikátorů HTTP URI pro aplikace, které přihlašují pracovní nebo školní účty, je podporováno jenom prostřednictvím editoru manifestu aplikace. Do budoucna nebudou nové aplikace moci používat http schémata v identifikátoru URI přesměrování. Starší aplikace, které obsahují http schémata v přesměrování identifikátorů URI však bude i nadále fungovat. Vývojáři musí používat schémata HTTPS v přesměrování identifikátorů URI.

## <a name="restrictions-using-a-wildcard-in-uris"></a>Omezení pomocí zástupných symbolů v identifikátorech URI

Identifikátory URI se `https://*.contoso.com`zástupnými symboly, například , jsou vhodné, ale je třeba se jim vyhnout. Použití zástupných znaků v identifikátoru URI přesměrování má důsledky pro zabezpečení. Podle specifikace OAuth 2.0 ([oddíl 3.1.2 RFC 6749](https://tools.ietf.org/html/rfc6749#section-3.1.2)) musí být identifikátor URI koncového bodu přesměrování absolutní identifikátor URI. 

Model aplikace Azure AD nepodporuje identifikátory URI se zástupnými kódy pro aplikace, které jsou nakonfigurované pro přihlašování osobních účtů Microsoft a pracovních nebo školních účtů. Identifikátory URI se zástupnými symboly se zástupnými symboly jsou však povoleny pro aplikace, které jsou nakonfigurované pro přihlášení pracovních nebo školních účtů v tenantovi Azure AD organizace dnes. 
 
> [!NOTE]
> Nové prostředí registrace aplikací neumožňuje vývojářům přidávat [identifikátory](https://go.microsoft.com/fwlink/?linkid=2083908) URI se zástupnými kódy do uživatelského rozhraní. Přidání identifikátoru URI ve wilcard u aplikací, které přihlašují pracovní nebo školní účty, je podporováno pouze prostřednictvím editoru manifestu aplikace. Do budoucna nebudou nové aplikace moci používat zástupné znaky v identifikátoru URI přesměrování. Starší aplikace, které obsahují zástupné znaky v přesměrování identifikátorů URI, však budou nadále fungovat.

Pokud váš scénář vyžaduje více identifikátorů URI přesměrování, než je maximální povolený limit, namísto přidání identifikátoru URI přesměrování se zástupnými symboly, zvažte následující přístup.

### <a name="use-a-state-parameter"></a>Použití parametru stavu

Pokud máte několik dílčích domén a váš scénář vyžaduje přesměrování uživatelů po úspěšném ověření na stejnou stránku, kde začali, může být užitečné použít parametr stavu. 

V tomto přístupu:

1. Vytvořte "sdílené" přesměrování URI pro aplikaci ke zpracování tokenů zabezpečení, které obdržíte z koncového bodu autorizace.
1. Aplikace může odesílat parametry specifické pro aplikaci (například adresu URL subdomény, kde uživatel pochází nebo něco jako informace o značce) v parametru stavu. Při použití parametru stavu chraňte ochranu PROTI OCHRANĚ CSRF podle [bodu 10.12 RFC 6749](https://tools.ietf.org/html/rfc6749#section-10.12)). 
1. Parametry specifické pro aplikaci budou obsahovat všechny informace potřebné pro aplikaci k vykreslení správné ho zachůzky pro uživatele, to znamená, že vytvoří příslušný stav aplikace. Koncový bod autorizace Azure AD proužky HTML z parametru stavu, takže se ujistěte, že nejste předávání obsahu HTML v tomto parametru.
1. Když Azure AD odešle odpověď na "sdílené" přesměrování URI, odešle parametr stavu zpět do aplikace.
1. Aplikace pak můžete použít hodnotu v parametru state k určení, která adresa URL dále odesílat uživatele. Ujistěte se, že jste ověřili ochranu CSRF.

> [!NOTE]
> Tento přístup umožňuje ohroženému klientovi upravit další parametry odeslané v parametru stavu, a tím přesměrovat uživatele na jinou adresu URL, což je [hrozba otevřeného přesměrovače](https://tools.ietf.org/html/rfc6819#section-4.2.4) popsaná v rfc 6819. Klient proto musí tyto parametry chránit šifrováním stavu nebo jeho ověřením jiným způsobem, například ověřením názvu domény v identifikátoru URI přesměrování proti tokenu.

## <a name="next-steps"></a>Další kroky

- Další informace o [manifestu aplikace](reference-app-manifest.md)
