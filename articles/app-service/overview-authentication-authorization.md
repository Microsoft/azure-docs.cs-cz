---
title: Ověřování a autorizace - službě Azure App Service | Dokumentace Microsoftu
description: Reference konceptu postupu a přehled o ověřování / autorizace funkcí pro službu Azure App Service
services: app-service
documentationcenter: ''
author: cephalin
manager: erikre
editor: ''
ms.assetid: b7151b57-09e5-4c77-a10c-375a262f17e5
ms.service: app-service
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 08/24/2018
ms.author: mahender,cephalin
ms.custom: seodec18
ms.openlocfilehash: 7fbf86a24ca6b2ba55ca33b20e67dea6a6e0c994
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/21/2018
ms.locfileid: "53731032"
---
# <a name="authentication-and-authorization-in-azure-app-service"></a>Ověřování a autorizace v prostředí Azure App Service

Azure App Service poskytuje integrované ověřování a autorizace podpory, abyste se mohli přihlásit uživatele a přístup k datům zápis minimální nebo žádný kód ve vaší webové aplikace, rozhraní RESTful API a mobilní back-end a také [Azure Functions](../azure-functions/functions-overview.md). Tento článek popisuje, jak služby App Service pomáhá zjednodušit ověřování a autorizaci pro vaši aplikaci. 

Zabezpečené ověřování a autorizace vyžadují hlubší pochopení problematiky zabezpečení, včetně federace, šifrování, [webové tokeny JSON (JWT)](https://wikipedia.org/wiki/JSON_Web_Token) správy, [typy udělení](https://oauth.net/2/grant-types/), a tak dále. Služba App Service poskytuje tyto nástroje tak, aby vám zbylo více času i energie na získává obchodní hodnotu zákazníkům.

> [!NOTE]
> Nejste musí používat pro ověřování a autorizaci služby App Service. Mnoho architektur webových jsou spojeny s funkcemi zabezpečení, můžete si je Pokud chcete, můžete. Pokud potřebujete větší flexibility než poskytuje služby App Service, můžete zapsat také vlastních nástrojů.  
>

Informace specifické pro nativní mobilní aplikace, najdete v části [uživatele ověřování a autorizace pro mobilní aplikace pomocí služby Azure App Service](../app-service-mobile/app-service-mobile-auth.md).

## <a name="how-it-works"></a>Jak to funguje

Modul ověřování a autorizace se spouští v sandboxu stejný jako kód vaší aplikace. Pokud je povoleno, všechny příchozí žádost protokolu HTTP přes ni procházejí před zpracovávaných kódu aplikace.

![](media/app-service-authentication-overview/architecture.png)

Tento modul zpracuje několik věcí pro vaši aplikaci:

- Ověřuje uživatele u zadaného zprostředkovatele
- Ověří, uloží a obnoví tokeny
- Spravuje ověřená relace
- Vloží informace o identitě do hlavičky požadavku

Modul spouští samostatně od kódu aplikace a je nakonfigurovaný pomocí nastavení aplikace. Je požadováno žádné sady SDK, konkrétní jazyky nebo změny v kódu aplikace. 

### <a name="user-claims"></a>Deklarace identity uživatele

Pro všechny jazykových rozhraní služby App Service zpřístupňuje deklarací identity uživatele do kódu vkládání do hlavičky žádosti. Pro aplikace ASP.NET 4.6, naplní služby App Service [ClaimsPrincipal.Current](/dotnet/api/system.security.claims.claimsprincipal.current) díky deklaracím identity ověřeného uživatele, takže můžete postupovat podle standardního vzorek kódu .NET, včetně `[Authorize]` atribut. Podobně pro aplikace v PHP, naplní služby App Service `_SERVER['REMOTE_USER']` proměnné.

Pro [Azure Functions](../azure-functions/functions-overview.md), `ClaimsPrincipal.Current` není HYDRATOVANÝ pro kód .NET, ale stále můžete najít deklarace identity uživatelů v hlavičce požadavku.

Další informace najdete v tématu [přístup deklarace identity uživatelů](app-service-authentication-how-to.md#access-user-claims).

### <a name="token-store"></a>Úložiště tokenů

Služba App Service poskytuje předdefinované úložiště tokenů, což je úložiště tokenů, které jsou spojeny s uživateli z webových aplikací, rozhraní API a nativních mobilních aplikací. Když povolíte ověřování pomocí libovolného poskytovatele, tento token úložiště je ihned k dispozici do vaší aplikace. Pokud kód aplikace potřebuje přístup k datům z těchto zprostředkovatelů jménem uživatele, jako například: 

- příspěvek na časové ose Facebooku ověřeného uživatele
- Přečtěte si firemní data uživatele z Azure Active Directory Graph API nebo dokonce Microsoft Graphu

Obvykle nutné napsat kód k shromažďování, ukládání a aktualizaci těchto tokenů ve vaší aplikaci. Pomocí tokenu úložiště jste právě [načíst tokeny](app-service-authentication-how-to.md#retrieve-tokens-in-app-code) když je potřebujete a [informace služby App Service je aktualizovat](app-service-authentication-how-to.md#refresh-access-tokens) při stanou neplatnými. 

Tokeny typu id, přístupové tokeny a obnovovací tokeny v mezipaměti pro ověřená relace a jsou přístupná jenom pro příslušné uživatele.  

Pokud už nebudete potřebovat pro práci s tokeny ve vaší aplikaci, můžete zakázat úložiště tokenů.

### <a name="logging-and-tracing"></a>Protokolování a trasování

Pokud jste [povolte protokolování aplikací](troubleshoot-diagnostic-logs.md), ověřování a autorizace trasování se zobrazí přímo v souborech protokolu. Pokud se zobrazí chyby ověřování, která jste neočekávali, jednoduše najdete všechny podrobnosti vyhledáváním v existující protokolů aplikace. Pokud povolíte [trasování neúspěšných žádostí](troubleshoot-diagnostic-logs.md), můžete se podívat právě jakou roli ověřování a modul autorizace může mít opakování neúspěšného požadavku. V protokolech trasování vyhledat odkazy na modul s názvem `EasyAuthModule_32/64`. 

## <a name="identity-providers"></a>Zprostředkovatelé identit

Služba App Service používá [Federovaná identita](https://en.wikipedia.org/wiki/Federated_identity), ve které zprostředkovatel identity třetí strany spravuje identity uživatelů a tok ověřování za vás. K dispozici ve výchozím nastavení jsou pět zprostředkovatelů identity: 

| Poskytovatel | Koncový bod přihlašování |
| - | - |
| [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) | `/.auth/login/aad` |
| [Účet Microsoft](../active-directory/develop/v2-overview.md) | `/.auth/login/microsoftaccount` |
| [Facebook](https://developers.facebook.com/docs/facebook-login) | `/.auth/login/facebook` |
| [Google](https://developers.google.com/+/web/api/rest/oauth) | `/.auth/login/google` |
| [Twitter](https://developer.twitter.com/en/docs/basics/authentication) | `/.auth/login/twitter` |

Když povolíte ověřování a autorizace s jedním z těchto zprostředkovatelů, svůj koncový bod přihlašování je k dispozici pro ověřování uživatelů a ověření tokenů ověřování od poskytovatele. Uživatelům můžete poskytnout libovolný počet tyto možnosti přihlášení s lehkostí a elegancí. Můžete také integrovat jiného zprostředkovatele identity nebo [vlastní řešení identit][custom-auth].

## <a name="authentication-flow"></a>Tok ověřování

Tok ověřování je stejný pro všechny poskytovatele, ale se liší v závislosti na tom, jestli chcete se přihlásit pomocí poskytovatele sady SDK:

- Bez poskytovatele sady SDK: Použití delegátů federovaného přihlašování ve službě App Service. Obvykle se jedná o tomu u prohlížečových aplikací, které můžete předložit uživateli poskytovatele přihlašovací stránku. Do kódu serveru spravuje proces přihlašování, takže se také nazývá _směrované na server tok_ nebo _server tok_. Tento případ platí do prohlížečových aplikací. Platí také pro nativní aplikace, které uživatele v pomocí Mobile Apps klientské sady SDK, protože sada SDK se otevře webové zobrazení pro uživatele přihlašují pomocí ověřování služby App Service. 
- U poskytovatele sady SDK: Aplikace přihlásí uživatele ve zprostředkovateli ručně a poté odešle ověřovací token do služby App Service pro ověření. Obvykle se jedná o případ s aplikacemi bez prohlížeče, které nelze prezentovat poskytovatele přihlašovací stránku pro uživatele. Kód aplikace spravuje proces přihlašování, takže se také nazývá _přesměruje klienta tok_ nebo _tok klienta_. Tento případ platí pro rozhraní REST API [Azure Functions](../azure-functions/functions-overview.md)a prohlížeči klientů JavaScript, jakož i prohlížečových aplikací, které vyžadují větší flexibilitu v procesu přihlášení. Platí také pro nativní mobilní aplikace, které uživatele pomocí poskytovatele sady SDK.

> [!NOTE]
> Volání z aplikace pro důvěryhodného prohlížeče ve službě App Service volá jiné rozhraní REST API ve službě App Service nebo [Azure Functions](../azure-functions/functions-overview.md) je možné ověřit pomocí toku směrované na server. Další informace najdete v tématu [přizpůsobit ověřování a autorizace ve službě App Service](app-service-authentication-how-to.md).
>

V tabulce níže najdete kroky pro tok ověřování.

| Krok | Bez poskytovatele sady SDK | U poskytovatele sady SDK |
| - | - | - |
| 1. Přihlášení uživatele | Přesměruje klienta do `/.auth/login/<provider>`. | Klientský kód přihlásí uživatele přímo pomocí poskytovatele sady SDK a přijímá ověřovací token. Informace najdete v dokumentaci poskytovatele. |
| 2. Následné ověření | Zprostředkovatel přesměruje klienta do `/.auth/login/<provider>/callback`. | Klientský kód [odešle token od zprostředkovatele](app-service-authentication-how-to.md#validate-tokens-from-providers) k `/.auth/login/<provider>` pro ověření. |
| 3. Navázání ověřená relace | App Service přidá ověření souboru cookie odpovědi. | Vlastní ověřovací token služby App Service vrátí pro klientský kód. |
| 4. Poskytování ověřeného obsahu | Klient zahrne soubor cookie pro ověřování v následné žádosti (automaticky zpracovává prohlížeče). | Klientský kód představuje ověřovací token v `X-ZUMO-AUTH` záhlaví (automaticky zpracovává Mobile Apps klientské sady SDK). |

Pro prohlížeče klienta, může služby App Service automaticky směrovat všechny neověřené uživatele na `/.auth/login/<provider>`. Uživatelům může zobrazit také s jedním nebo více `/.auth/login/<provider>` odkazy na přihlášení do vaší aplikace pomocí jejich poskytovatele podle výběru.

<a name="authorization"></a>

## <a name="authorization-behavior"></a>Chování ověřování

V [webu Azure portal](https://portal.azure.com), můžete nakonfigurovat autorizace služby App Service s celou řadou chování.

![](media/app-service-authentication-overview/authorization-flow.png)

Možnosti jsou popsány následující záhlaví.

### <a name="allow-all-requests-default"></a>Povolit všechny požadavky (výchozí)

Ověřování a autorizaci služby App Service (vypnuto) nespravuje. 

Tuto možnost zvolte, pokud už nepotřebujete, ověřování a autorizace, nebo pokud chcete napsat vlastní kód ověřování a autorizace.

### <a name="allow-only-authenticated-requests"></a>Povolit pouze ověřených požadavků

Tato možnost není **přihlášení \<poskytovatele >**. App Service přesměruje všechny anonymních požadavků na `/.auth/login/<provider>` pro zprostředkovatele, který zvolíte. Pokud anonymní žádost pochází z nativní mobilní aplikace, je vrácená odpověď `HTTP 401 Unauthorized`.

S touto možností nemusíte psát jakýkoli kód ověřování ve vaší aplikaci. Jemnější autorizace, třeba autorizaci konkrétní role, mohou být zpracovány kontrola deklarací identity uživatele (viz [přístup deklarace identity uživatelů](app-service-authentication-how-to.md#access-user-claims)).

### <a name="allow-all-requests-but-validate-authenticated-requests"></a>Povolit všechny požadavky, ale ověření ověřených požadavků

Tato možnost není **povolit anonymní požadavky**. Tato možnost zapne ověřování a autorizace ve službě App Service, ale odloží rozhodování o autorizaci ke kódu aplikace. U ověřených požadavků služby App Service také předá informace o ověřování v hlavičkách protokolu HTTP. 

Tato možnost poskytuje větší flexibilitu při zpracování anonymních požadavků. Například umožňuje [k dispozici více zprostředkovatelů přihlášení](app-service-authentication-how-to.md#use-multiple-sign-in-providers) vašim uživatelům. Ale musíte napsat kód. 

## <a name="more-resources"></a>Další zdroje informací

[Kurz: Ověřování a autorizaci uživatelů začátku do konce ve službě Azure App Service (Windows)](app-service-web-tutorial-auth-aad.md)  
[Kurz: Ověřování a autorizaci uživatelů začátku do konce ve službě Azure App Service pro Linux](containers/tutorial-auth-aad.md)  
[Přizpůsobení ověřování a autorizace ve službě App Service](app-service-authentication-how-to.md)

Průvodce postupy specifickým pro zprostředkovatele:

* [Konfigurace aplikace pro použití přihlášení Azure Active Directory][AAD]
* [Konfigurace aplikace pro použití přihlášení k Facebooku][Facebook]
* [Jak nakonfigurovat aplikaci pro použití přihlášení k Google][Google]
* [Konfigurace aplikace pro použití Microsoft Account login][MSA]
* [Jak nakonfigurovat aplikaci pro použití přihlášení k Twitteru][Twitter]
* [Jak: Použití vlastního ověřování pro vaši aplikaci][custom-auth]

[AAD]: configure-authentication-provider-aad.md
[Facebook]: configure-authentication-provider-facebook.md
[Google]: configure-authentication-provider-google.md
[MSA]: configure-authentication-provider-microsoft.md
[Twitter]: configure-authentication-provider-twitter.md

[custom-auth]: ../app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#custom-auth

[ADAL-Android]: ../app-service-mobile/app-service-mobile-android-how-to-use-client-library.md#adal
[ADAL-iOS]: ../app-service-mobile/app-service-mobile-ios-how-to-use-client-library.md#adal
[ADAL-dotnet]: ../app-service-mobile/app-service-mobile-dotnet-how-to-use-client-library.md#adal
