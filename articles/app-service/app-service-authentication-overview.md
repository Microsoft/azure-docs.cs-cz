---
title: Ověřování a autorizace ve službě Azure App Service | Microsoft Docs
description: Reference konceptu a Přehled ověřování / autorizace funkcí pro Azure App Service
services: app-service
documentationcenter: ''
author: mattchenderson
manager: erikre
editor: ''
ms.assetid: b7151b57-09e5-4c77-a10c-375a262f17e5
ms.service: app-service
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 08/29/2016
ms.author: mahender
ms.openlocfilehash: c180dcf5d769245f3fa2485ccee2cbc18ecf5f67
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
ms.locfileid: "33763488"
---
# <a name="authentication-and-authorization-in-azure-app-service"></a>Ověřování a autorizace v prostředí Azure App Service

Aplikační služba Azure poskytuje integrované ověřování a autorizace podporovat, abyste mohli přihlásit uživatele a přístup k datům zápis minimální nebo žádný kód ve vaší webové aplikace, rozhraní API a mobilní back-end a také [Azure Functions](../azure-functions/functions-overview.md). Tento článek popisuje, jak služby App Service pomáhá zjednodušit ověřování a autorizace pro vaši aplikaci. 

Zabezpečení ověřování a autorizace vyžaduje hluboké znalosti zabezpečení, včetně federation, šifrování, [webové tokeny JSON (JWT)](https://wikipedia.org/wiki/JSON_Web_Token) správy, [typy udělení](https://oauth.net/2/grant-types/)a tak dále. Služba App Service poskytuje tyto nástroje tak, aby na obchodní hodnotu poskytnutí zákazníkovi strávíte více času a energie.

> [!NOTE]
> Nejste vyžaduje použití služby App Service pro ověřování a autorizaci. Mnoho webových rozhraní jsou dodávané společně s funkcí zabezpečení a jejich Pokud chcete použít. Pokud potřebujete větší flexibilitu, než poskytuje služby App Service, můžete taky napsat vlastní nástroje.  
>

Informace specifické pro nativní mobilních aplikací najdete v tématu [uživatele ověřování a autorizaci mobilních aplikací s Azure App Service](../app-service-mobile/app-service-mobile-auth.md).

## <a name="how-it-works"></a>Jak to funguje

Modul ověřování a autorizace se spouští v izolovaném prostoru stejný jako kód aplikace. Pokud je povolený, předá každého příchozího požadavku HTTP přes něj před zpracovanou kódu aplikace.

![](media/app-service-authentication-overview/architecture.png)

Tento modul zpracovává několik věcí pro vaši aplikaci:

- Ověřuje uživatele u zadaného zprostředkovatele
- Ověří, ukládá a aktualizuje tokeny
- Spravuje ověřená relace
- Vloží informace o identitě do hlavičky požadavku

Modul běží odděleně od kódu aplikace a je konfigurován pomocí nastavení aplikace. Je požadováno žádné sady SDK, konkrétní jazyky nebo změny kódu aplikace. 

### <a name="user-claims"></a>Deklarace identity uživatelů

Pro všechny jazykové rozhraní služby App Service zpřístupní deklaracích identity uživatele do kódu vložením do hlavičky žádosti. Pro technologii ASP.NET 4.6 aplikace služby App Service naplní [ClaimsPrincipal.Current](/dotnet/api/system.security.claims.claimsprincipal.current) s deklaracemi identity ověřeného uživatele, takže můžete postupovat podle vzoru standardní kódu .NET, včetně `[Authorize]` atribut. Podobně, pro aplikace PHP, naplní služby App Service `_SERVER['REMOTE_USER']` proměnné.

Pro [Azure Functions](../azure-functions/functions-overview.md), `ClaimsPrincipal.Current` není HYDRATOVANÝ pro kód .NET, ale stále můžete najít deklarace identity uživatelů v hlavičkách žádostí.

Další informace najdete v tématu [přístup deklarace identity uživatelů](app-service-authentication-how-to.md#access-user-claims).

### <a name="token-store"></a>Úložiště tokenů

Služba App Service poskytuje integrované úložiště tokenů, což je úložiště tokenů, které jsou spojeny s uživateli vaší webové aplikace, rozhraní API nebo nativní mobilní aplikace. Pokud povolíte ověřování pomocí kteréhokoli poskytovatele služeb, tento token úložiště je okamžitě k dispozici pro vaši aplikaci. Pokud kód aplikace potřebuje přístup k datům z těchto zprostředkovatelů jménem uživatele, jako například: 

- odeslání na ose Facebook ověřeného uživatele
- čtení uživatele podniková data z Azure Active Directory Graph API nebo i Microsoft Graph

Tokeny typu id, přístupové tokeny a tokeny obnovení do mezipaměti pro ověřená relace, a jsou přístupná jenom pro příslušné uživatele.  

Obvykle musíte napsat kód pro shromažďování, ukládání a aktualizovat tyto tokeny ve vaší aplikaci. S úložiště tokenů, jste právě [načíst tokeny](app-service-authentication-how-to.md#retrieve-tokens-in-app-code) případě potřeby a [službě App Service je aktualizovat říci](app-service-authentication-how-to.md#refresh-access-tokens) kdy začnou neplatný. 

Pokud nepotřebujete pro práci s tokeny ve vaší aplikaci, můžete zakázat úložiště tokenů.

### <a name="logging-and-tracing"></a>Protokolování a trasování

Pokud jste [povolte protokolování aplikací,](web-sites-enable-diagnostic-log.md), zobrazí se ověřování a autorizace trasování přímo do souborů protokolu. Pokud se zobrazí chyby ověřování, který by neměl být, můžete pohodlně najít všechny podrobnosti podle vaší existující protokoly aplikací. Pokud povolíte [trasování neúspěšných žádostí](web-sites-enable-diagnostic-log.md), uvidíte přesně jakou roli ověřování a autorizace modul může mít v chybné žádosti. V protokolech trasování, vyhledejte odkazy na modul s názvem `EasyAuthModule_32/64`. 

## <a name="identity-providers"></a>Zprostředkovatelé identit

Služby App Service používá [Federovaná identita](https://en.wikipedia.org/wiki/Federated_identity), ve kterém spravuje poskytovatel identity jiného výrobce identity uživatelů a tok ověřování za vás. Ve výchozím nastavení k dispozici jsou pět poskytovatelů identit: 

| Poskytovatel | Koncový bod přihlášení |
| - | - |
| [Azure Active Directory](../active-directory/active-directory-whatis.md) | `/.auth/login/aad` |
| [Účet Microsoft](../active-directory/develop/active-directory-appmodel-v2-overview.md) | `/.auth/login/microsoft` |
| [Facebook](https://developers.facebook.com/docs/facebook-login) | `/.auth/login/facebook` |
| [Google](https://developers.google.com/+/web/api/rest/oauth) | `/.auth/login/google` |
| [Twitter](https://developer.twitter.com/docs/basics/authentication) | `/.auth/login/twitter` |

Pokud povolíte ověřování a autorizace pomocí jednoho z těchto poskytovatelů, jeho koncový bod přihlášení je k dispozici pro ověřování uživatelů a pro ověření ověřovací tokeny od zprostředkovatele. Zajistíte uživatelům snadno s libovolným počtem tyto možnosti přihlašování. Můžete také integrovat jiného poskytovatele identity nebo [řešení vlastní identitu][custom-auth].

## <a name="authentication-flow"></a>Tok ověřování

Tok ověření je stejný pro všechny poskytovatele, ale se liší v závislosti na tom, jestli chcete k přihlášení pomocí poskytovatele sady SDK:

- Bez zprostředkovatele SDK: aplikace deleguje federovaných přihlášení do služby App Service. Toto je obvykle tomu u aplikace prohlížeče, které může být poskytovatele přihlašovací stránku pro uživatele. Kód serveru spravuje proces přihlášení, se označuje taky jako _směrované serveru toku_ nebo _server toku_. Tento případ se vztahuje na webové aplikace. Platí také pro nativní aplikace, které uživatele pomocí mobilní aplikace klienta SDK vzhledem k tomu, že otevře sadu SDK webové zobrazení na uživatele přihlašují pomocí ověřování služby App Service. 
- U poskytovatele sady SDK: aplikace se přihlásí uživatel ručně a poté odešle ověřovací token do služby App Service pro ověření. Toto je obvykle v případě s prohlížeči bez aplikace, které nelze poskytnout přihlašovací stránce poskytovatele uživateli. Kódu aplikace spravuje proces přihlášení, takže se označuje taky jako _přesměruje klienta toku_ nebo _tok klienta_. Tento případ se vztahuje na rozhraní REST API, [Azure Functions](../azure-functions/functions-overview.md)a klienty prohlížeče JavaScript, jakož i webové aplikace, které je třeba větší flexibilitu v procesu přihlášení. Platí také pro nativní mobilní aplikace, které uživatele pomocí poskytovatele sady SDK.

> [!NOTE]
> Volání z důvěryhodných prohlížeče aplikace v App Service volá jinou REST API v App Service nebo [Azure Functions](../azure-functions/functions-overview.md) mohou být ověřeny pomocí toku směrované serveru. Další informace najdete v tématu [ověřuje uživatele pomocí služby Azure App Service]().
>

Následující tabulka uvádí kroky tok ověřování.

| Krok | Bez poskytovatele sady SDK | U poskytovatele sady SDK |
| - | - | - |
| 1. Přihlášení uživatele v | Přesměruje klienta na `/.auth/login/<provider>`. | Kód klienta přihlásí uživatel přímo s poskytovatele sady SDK a přijímá ověřovací token. Informace najdete v dokumentaci poskytovatele. |
| 2. Následné ověření | Zprostředkovatel přesměruje klienta na `/.auth/login/<provider>/callback`. | Kód klienta odešle token od zprostředkovatele na `/.auth/login/<provider>` pro ověření. |
| 3. Vytvoření ověřená relace | Služby App Service přidá ověření souboru cookie odpovědi. | Služby App Service vrátí vlastní ověřovací token do kódu klienta. |
| 4. Poskytovat ověřené obsah | Klient zahrne soubor cookie pro ověřování v následných žádostí (automaticky zpracovávat prohlížeče). | Kód klienta představuje ověřovací token v `X-ZUMO-AUTH` hlavičky (automaticky zpracovává klientem Mobile Apps sady SDK). |

U klientských počítačů, můžete služby App Service automaticky nasměrovat všechny neověřené uživatele na `/.auth/login/<provider>`. Uživatelům může zobrazit také s jedním nebo více `/.auth/login/<provider>` odkazy na přihlášení do vaší aplikace pomocí jejich zprostředkovatele výběru.

<a name="authorization"></a>

## <a name="authorization-behavior"></a>Chování autorizace

V [portál Azure](https://portal.azure.com), autorizace služby App Service můžete nakonfigurovat s počtem chování.

![](media/app-service-authentication-overview/authorization-flow.png)

Následující záhlaví popisují možnosti.

### <a name="allow-all-requests-default"></a>Povolit všechny požadavky (výchozí)

Ověřování a autorizace není spravován službou App Service (vypnuto). 

Tuto možnost zvolte, pokud nepotřebujete ověřování a autorizaci, nebo pokud chcete napsat vlastní kód ověřování a autorizace.

### <a name="allow-only-authenticated-requests"></a>Povolit pouze ověřené žádosti

Možnost je **protokolu s \<zprostředkovatele >**. Služby App Service přesměruje všechny anonymní žádosti o `/.auth/login/<provider>` pro zprostředkovatele, který zvolíte. Pokud anonymní žádost pochází z nativní mobilní aplikace, je vrácený odpovědi `HTTP 401 Unauthorized`.

Tato možnost nemusíte psaní jakéhokoli kódu ověřování ve vaší aplikaci. Jemnějšího autorizace, jako je například autorizace specifické pro roli, mohou být zpracována zkontrolujete deklaracích identity uživatele (viz [přístup deklarace identity uživatelů](app-service-authentication-how-to.md#access-user-claims)).

### <a name="allow-all-requests-but-validate-authenticated-requests"></a>Povolit všechny požadavky, ale ověření ověřené žádosti

Možnost je **povolit anonymní požadavky**. Tato možnost zapne ověřování a autorizace ve službě App Service, ale autorizačních rozhodnutích kódu své aplikace odkládat údaje. U ověřených požadavků služby App Service také předá podél informace o ověřování v hlavičkách protokolu HTTP. 

Tato možnost poskytují větší flexibilitu při zpracování anonymních požadavků. Například umožňuje [k dispozici více možností přihlášení](app-service-authentication-how-to.md#configure-multiple-sign-in-options) pro vaše uživatele. Ale budete muset psát kód. 

## <a name="more-resources"></a>Další zdroje informací

[Kurz: Ověřování a autorizaci uživatelů klient server v Azure App Service (Windows)](app-service-web-tutorial-auth-aad.md)  
[Kurz: Ověřování a autorizaci uživatelů klient server v Azure App Service pro Linux](containers/tutorial-auth-aad.md)  
[Přizpůsobení ověřování a autorizace ve službě App Service](app-service-authentication-how-to.md)

Návody specifický pro zprostředkovatele:

* [Postup konfigurace aplikace použít Azure Active Directory přihlášení][AAD]
* [Postup konfigurace aplikace používat Facebook přihlášení][Facebook]
* [Postup konfigurace aplikace používat Google přihlášení][Google]
* [Postup konfigurace aplikace používat Account Microsoft přihlášení][MSA]
* [Postup konfigurace aplikace používat přihlášení služby Twitter.][Twitter]
* [Postupy: použití vlastního ověřování pro aplikaci][custom-auth]

[AAD]: app-service-mobile-how-to-configure-active-directory-authentication.md
[Facebook]: app-service-mobile-how-to-configure-facebook-authentication.md
[Google]: app-service-mobile-how-to-configure-google-authentication.md
[MSA]: app-service-mobile-how-to-configure-microsoft-authentication.md
[Twitter]: app-service-mobile-how-to-configure-twitter-authentication.md

[custom-auth]: ../app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#custom-auth

[ADAL-Android]: ../app-service-mobile/app-service-mobile-android-how-to-use-client-library.md#adal
[ADAL-iOS]: ../app-service-mobile/app-service-mobile-ios-how-to-use-client-library.md#adal
[ADAL-dotnet]: ../app-service-mobile/app-service-mobile-dotnet-how-to-use-client-library.md#adal
