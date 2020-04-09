---
title: Ověřování na platformě identit microsoftu | Azure
description: Informace o základech ověřování na platformě microsoft identit (v2.0).
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 02/03/2020
ms.author: ryanwi
ms.reviewer: jmprieur, saeeda, sureshja, hirsin
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started
ms.openlocfilehash: e78f822a88b093992f065a509c2250e6a5c0dec2
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2020
ms.locfileid: "80885561"
---
# <a name="authentication-basics"></a>Základy ověřování

## <a name="what-is-authentication"></a>Co je ověřování

Tento článek popisuje mnoho konceptů ověřování, které budete muset pochopit k vytvoření chráněných webových aplikací, webových api nebo aplikací volajících chráněná webová api. Pokud vidíte termín, který neznáte, vyzkoušejte náš [glosář](developer-glossary.md) nebo [videa platformy microsoftových identit,](identity-videos.md) která pokrývají základní koncepty.

**Ověřování** je proces dokazování, že jste tím, za koho se znáte. V angličtině se pro ověřování někdy používá zkrácené slovo AuthN.

**Autorizace** je udělení oprávnění ověřené straně k něčemu. Určuje, k jakým datům máte povolen přístup a co můžete s těmito daty dělat. V angličtině se pro autorizaci někdy používá zkrácené slovo AuthZ.

Namísto vytváření aplikací, které si každý udržuje své vlastní informace o uživatelském jménu a hesle, což způsobuje vysokou administrativní zátěž, když potřebujete přidat nebo odebrat uživatele ve více aplikacích, mohou aplikace delegovat tuto odpovědnost na centralizovaného poskytovatele identity.

Azure Active Directory (Azure AD) je centralizovaný poskytovatel identity v cloudu. Delegování ověřování a autorizace na něj umožňuje scénáře, jako jsou zásady podmíněného přístupu, které vyžadují, aby se uživatel nachází v určitém umístění, použití vícefaktorového ověřování a také umožňuje uživateli přihlásit se jednou a pak se automaticky přihlásit ke všem webovým aplikacím, které sdílejí stejný centralizovaný adresář. Tato funkce se označuje jako jednotné přihlášení (SSO).

Centralizovaný poskytovatel identit je ještě důležitější pro aplikace, které mají uživatele umístěné po celém světě, kteří se nemusí nutně přihlašovat ze sítě podniku. Azure AD ověřuje uživatele a poskytuje přístupové tokeny. [Přístupový token](https://docs.microsoft.com/azure/active-directory/develop/developer-glossary#access-token) je token zabezpečení vydaný autorizačním serverem. Obsahuje informace o uživateli a aplikaci, pro kterou je token určen; které lze použít pro přístup k webovým apim a dalším chráněným prostředkům.

Platforma identit Microsoftu zjednodušuje ověřování vývojářům aplikací tím, že poskytuje identitu jako službu s podporou standardních protokolů, jako jsou [OAuth 2.0](https://oauth.net/2/) a [OpenID Connect](https://openid.net/connect/), a také knihoven s otevřeným zdrojovým kódem pro různé platformy, které vám pomohou rychle začít s kódováním. Umožňuje vývojářům vytvářet aplikace, které přihlašují všechny identity Microsoftu, získají tokeny pro volání [Microsoft Graphu](https://developer.microsoft.com/graph/), dalších rozhraní API společnosti Microsoft nebo rozhraní API, která vývojáři vytvořili. Další informace naleznete [v tématu Evolution of Microsoft identity platformy](about-microsoft-identity-platform.md).

### <a name="tenants"></a>Tenanti

Poskytovatel cloudových identit slouží mnoha organizacím. Chcete-li udržet uživatele z různých organizací oddělené, Azure AD je rozdělena do tenantů, s jedním tenantem na organizaci.

Klienti sledují uživatele a jejich přidružené aplikace. Platforma identit microsoftu také podporuje uživatele, kteří se přihlašují pomocí osobních účtů Microsoft.

Azure AD také poskytuje Azure Active Directory B2C tak, aby organizace můžete přihlásit uživatele, obvykle zákazníky, pomocí sociálních identit, jako je účet Google. Další informace naleznete v [dokumentaci k službě Azure Active Directory B2C](https://docs.microsoft.com/azure/active-directory-b2c) .

### <a name="security-tokens"></a>Tokeny zabezpečení

Tokeny zabezpečení obsahují informace o uživatelích a aplikacích. Azure AD používá tokeny založené na JSON (JWTs), které obsahují deklarace identity.

Deklarace poskytuje kontrolní výrazy týkající se jedné entity, například [klientské aplikace](https://docs.microsoft.com/azure/active-directory/develop/developer-glossary#client-application) nebo [vlastníka prostředku](https://docs.microsoft.com/azure/active-directory/develop/developer-glossary#resource-owner), jiné entitě, například [serveru prostředků](https://docs.microsoft.com/azure/active-directory/develop/developer-glossary#resource-server).

Deklarace jsou dvojice název/hodnota, které přenášejí fakta o předmětu tokenu. Deklarace může například obsahovat fakta o objektu zabezpečení, který byl ověřen [autorizačním serverem](https://docs.microsoft.com/azure/active-directory/develop/developer-glossary#authorization-server). Deklarace identity přítomné v daném tokenu závisí na mnoha věcech, včetně typu tokenu, typu pověření použitého k ověření předmětu, konfigurace aplikace a tak dále.

Aplikace mohou používat deklarace identity pro různé úkoly, jako jsou:

* Ověření tokenu
* Identifikace klienta subjektu tokenu
* Zobrazení informací o uživateli
* Určení oprávnění subjektu

Deklarace se skládá z párů klíč hodnota, které poskytují informace, jako je:

* Server tokenů zabezpečení, který vygeneroval token
* Datum, kdy byl token vygenerován
* Předmět (například uživatel-- s výjimkou daemonů)
* Cílová skupina publika, což je aplikace, pro kterou byl token vygenerován
* Aplikace (klient), který požádal o token. V případě webových aplikací to může být stejné jako publikum

Podrobnější informace o deklaraci pohledávky najdete [v tématu přístupové tokeny](access-tokens.md) a [tokeny ID](id-tokens.md).

Je na aplikaci, pro kterou byl generován token, webové aplikace, která přihlášena uživatele nebo webové rozhraní API, které je voláno, k ověření tokenu. Token je podepsán serverem tokenů zabezpečení (STS) se soukromým klíčem. STS publikuje odpovídající veřejný klíč. Chcete-li ověřit token, aplikace ověří podpis pomocí veřejného klíče STS k ověření, že podpis byl vytvořen pomocí soukromého klíče.

Tokeny jsou platné pouze po omezenou dobu. Služby STS obvykle poskytuje dvojici tokenů: přístupový token pro přístup k aplikaci nebo chráněnému prostředku a obnovovací token používaný k aktualizaci přístupového tokenu, když se přístupový token blíží vypršení platnosti.

Přístupové tokeny jsou předány webovému rozhraní `Authorization` API jako nosný token v záhlaví. Aplikace může poskytnout obnovovací token pro STS a pokud přístup uživatele k aplikaci nebyl odvolán, získá zpět nový přístupový token a nový obnovovací token. Takhle se řeší scénář, že někdo opustí podnik. Když STS obdrží obnovovací token, nebude vydávat jiný platný přístupový token, pokud uživatel již není autorizován.

### <a name="how-each-flow-emits-tokens-and-codes"></a>Jak každý tok vyzařuje tokeny a kódy

V závislosti na tom, jak je váš klient sestaven, může použít jeden (nebo několik) toků ověřování podporovaných službou Azure AD. Tyto toky můžete vytvářet různé tokeny (id_tokens, obnovovací tokeny, přístupové tokeny) a také autorizační kódy a vyžadují různé tokeny, aby fungovaly. Tento graf poskytuje přehled:

|Tok | Vyžaduje | id_token | přístupový token | obnovovací token | autorizační kód | 
|-----|----------|----------|--------------|---------------|--------------------|
|[Tok autorizačního kódu](v2-oauth2-auth-code-flow.md) | | x | x | x | x|  
|[Implicitní tok](v2-oauth2-implicit-grant-flow.md) | | x        | x    |      |                    |
|[Hybridní TOK OIDC](v2-protocols-oidc.md#get-access-tokens)| | x  | |          |            x   |
|[Obnovení uplatnění tokenu](v2-oauth2-auth-code-flow.md#refresh-the-access-token) | obnovovací token | x | x | x| |
|[Tok On-Behalf-Of](v2-oauth2-on-behalf-of-flow.md) | přístupový token| x| x| x| |
|[Přihlašovací údaje klienta](v2-oauth2-client-creds-grant-flow.md) | | | x (pouze aplikace)| | |

Tokeny vydané prostřednictvím implicitního režimu mají omezení délky kvůli předání `response_mode` `query` zpět `fragment`do prohlížeče prostřednictvím adresy URL (kde je nebo ).  Některé prohlížeče mají limit na velikost url, které mohou být uvedeny v panelu prohlížeče a selhání, když je příliš dlouhá.  Proto tyto tokeny nemají `groups` `wids` nebo deklarace identity. 

Teď, když máte přehled o základech, přečtěte si o pochopení modelu aplikace identity a rozhraní API, zjistěte, jak funguje zřizování ve službě Azure AD a získejte odkazy na podrobné informace o běžných scénářích, které Azure AD podporuje.

## <a name="application-model"></a>Aplikační model

Aplikace můžete přihlásit uživatele sami nebo delegovat přihlášení k poskytovateli identity. Informace o scénářích přihlášení podporovaných službou Azure AD najdete v tématu [Toky ověřování a scénáře aplikací.](authentication-flows-app-scenarios.md)

Aby poskytovatel identity věděl, že uživatel má přístup k určité aplikaci, musí být uživatel i aplikace zaregistrováni u poskytovatele identity. Když zaregistrujete aplikaci s Azure AD, poskytujete konfiguraci identity pro vaši aplikaci, která umožňuje integraci s Azure AD. Registrace aplikace také umožňuje:

* Přizpůsobte značku aplikace v přihlašovacím dialogovém okně. To je důležité, protože se jedná o první prostředí, které uživatel bude mít s vaší aplikací.
* Rozhodněte se, zda chcete uživatelům uvolit přihlášení pouze v případě, že patří do vaší organizace. Toto je aplikace jednoho klienta. Nebo povolte uživatelům přihlásit se pomocí libovolného pracovního nebo školního účtu. Toto je víceklientská aplikace. Můžete také povolit osobní účty Microsoft nebo sociální účet z LinkedIn, Google a tak dále.
* Požádejte o oprávnění oboru. Můžete například požádat o obor "user.read", který uděluje oprávnění ke čtení profilu přihlášeného uživatele.
* Definujte obory, které definují přístup k webovému rozhraní API. Obvykle, když aplikace chce přístup k rozhraní API, bude muset požádat o oprávnění k oborům, které definujete.
* Sdílejte tajný klíč s Azure AD, který dokazuje identitu aplikace ve službě Azure AD.  To je důležité v případě, kdy je aplikace důvěrnou klientskou aplikací. Důvěrná klientská aplikace je aplikace, která může bezpečně uchovávat pověření. K uložení pověření vyžadují důvěryhodný back-endový server.

Po registraci aplikace bude mít jedinečný identifikátor, který aplikace sdílí s Azure AD, když požaduje tokeny. Pokud je aplikace [důvěrná klientská aplikace](https://docs.microsoft.com/azure/active-directory/develop/developer-glossary#client-application), bude také sdílet tajný nebo veřejný klíč*- v závislosti na tom, zda byly použity certifikáty nebo tajné klíče.

Platforma identit společnosti Microsoft představuje aplikace pomocí modelu, který plní dvě hlavní funkce:

* Identifikace aplikace podle ověřovacích protokolů, které podporuje
* Zadejte všechny identifikátory, adresy URL, tajné kódy a související informace, které jsou potřebné k ověření

Platforma identit společnosti Microsoft:

* Obsahuje všechna data potřebná pro podporu ověřování za běhu.
* Obsahuje všechna data pro rozhodování o tom, jaké prostředky aplikace může potřebovat pro přístup a za jakých okolností by měla být splněna daná žádost
* Poskytuje infrastrukturu pro implementaci zřizování aplikací v rámci tenanta vývojáře aplikace a pro všechny ostatní klienty Azure AD
* Zpracovává souhlas uživatele během doby žádosti o token a usnadňuje dynamické zřizování aplikací napříč tenanty.

Souhlas je proces vlastníka prostředku udělujícího autorizaci klientské aplikace pro přístup k chráněným prostředkům pod určitými oprávněními jménem vlastníka prostředku. Platforma identit společnosti Microsoft:

* Umožňuje uživatelům a správcům dynamicky udělovat nebo odepírat souhlas s tím, aby aplikace jejich jménem měla přístup k prostředkům.
* Umožňuje správcům nakonec rozhodnout, co můžou aplikace provádět a kteří uživatelé můžou konkrétní aplikace používat a jak se přistupuje k prostředkům adresáře.

V platformě identit y Microsoft [objekt aplikace](https://docs.microsoft.com/azure/active-directory/develop/developer-glossary#application-object) popisuje aplikaci. V době nasazení platforma identit microsoftu používá objekt aplikace jako podrobný plán k vytvoření [instančního objektu](https://docs.microsoft.com/azure/active-directory/develop/developer-glossary#service-principal-object), který představuje konkrétní instanci aplikace v rámci adresáře nebo klienta. Instanční objekt definuje, co aplikace může ve skutečnosti dělat v konkrétním cílovém adresáři, kdo ji může používat, k jakým prostředkům má přístup a tak dále. Platforma identit společnosti Microsoft vytvoří instanční objekt z objektu aplikace prostřednictvím **souhlasu**.

Následující diagram znázorňuje zjednodušený tok zřizování platformy identit microsoftu řízený souhlasem. Zobrazuje dva klienty: A a B. Tenant A vlastní aplikaci. Tenant B je vytváření instancí aplikace prostřednictvím instančního objektu.  

![Zjednodušený tok zřizování s využitím souhlasu](./media/authentication-scenarios/simplified-provisioning-flow-consent-driven.svg)

V tomto toku zřizování:

1. Uživatel z klienta B se pokusí přihlásit pomocí aplikace, koncový bod autorizace požaduje token pro aplikaci.
1. Pověření uživatele jsou získána a ověřena pro ověření.
1. Uživatel je vyzván k poskytnutí souhlasu pro aplikaci získat přístup k klientovi B.
1. Platforma identit Microsoftu používá aplikační objekt v tenantovi A jako podrobný plán pro vytvoření instančního objektu v tenantovi B.
1. Uživatel obdrží požadovaný token.

Tento proces můžete opakovat pro další klienty. Tenant A zachová podrobný plán pro aplikaci (aplikační objekt). Uživatelé a správci všech ostatních klientů, kde je aplikaci udělen souhlas, zachovávejte kontrolu nad tím, co je aplikace povolena, prostřednictvím odpovídajícího objektu instančního objektu v každém klientovi. Další informace naleznete [v tématu Application and service principal objects in Microsoft identity platform .](app-objects-and-service-principals.md)

## <a name="web-app-sign-in-flow-with-azure-ad"></a>Tok přihlášení z webové aplikace pomocí Azure AD

Když uživatel přejde v prohlížeči do webové aplikace, stane se následující:

* Webová aplikace určuje, zda je uživatel ověřen.
* Pokud uživatel není ověřen, webové aplikace deleguje na Azure AD k přihlášení uživatele. Toto přihlášení bude v souladu se zásadami organizace, což může znamenat, že uživatel zadá svá pověření pomocí vícefaktorového ověřování nebo vůbec nepoužije heslo (například pomocí Windows Hello).
* Uživatel je požádán o souhlas s přístupem, který klientská aplikace potřebuje. To je důvod, proč klientské aplikace musí být registrovány ve službě Azure AD, aby Azure AD můžete doručovat tokeny představující přístup, který uživatel souhlasil.

Pokud se uživatel úspěšně ověří:

* Azure AD odešle token do webové aplikace.
* Soubor cookie se uloží, přidružený k doméně Azure AD, který obsahuje identitu uživatele v prohlížeči cookie jar. Při příštím použití prohlížeče k přechodu na koncový bod autorizace Azure AD prohlížeč zobrazí soubor cookie tak, aby se uživatel nemusel znovu přihlašovat. To je také způsob, jakým je dosaženo sazí. Soubor cookie je vytvářen azure ad a může být srozumitelný jenom ve službě Azure AD.
* Webová aplikace pak ověří token. Pokud je ověření úspěšné, webová aplikace zobrazí chráněnou stránku a uloží soubor cookie relace do nádoby cookie prohlížeče. Když uživatel přejde na jinou stránku, webová aplikace ví, že uživatel je ověřen na základě souboru cookie relace.

Následující sekvenční diagram shrnuje tuto interakci:

![Proces ověřování webové aplikace](media/authentication-scenarios/web-app-how-it-appears-to-be.png)

### <a name="how-a-web-app-determines-if-the-user-is-authenticated"></a>Jak webová aplikace určuje, zda je uživatel ověřen

Vývojáři webových aplikací mohou určit, zda všechny nebo pouze určité stránky vyžadují ověření. Například v ASP.NET/ASP.NET Core, to se `[Authorize]` provádí přidáním atributu k akcím kontroleru. 

Tento atribut způsobí, že ASP.NET zkontrolovat přítomnost souboru cookie relace obsahujícího identitu uživatele. Pokud soubor cookie není k dispozici, ASP.NET přesměruje ověřování na zadaného zprostředkovatele identity. Pokud je poskytovatelem identity Azure AD, webová `https://login.microsoftonline.com`aplikace přesměruje ověřování na , který zobrazí přihlašovací dialogové okno.

### <a name="how-a-web-app-delegates-sign-in-to-azure-ad-and-obtains-a-token"></a>Jak webová aplikace deleguje přihlášení k Azure AD a získá token

Ověření uživatele probíhá prostřednictvím prohlížeče. Protokol OpenID používá standardní zprávy protokolu HTTP.
* Webová aplikace odešle HTTP 302 (přesměrování) do prohlížeče k použití Azure AD.
* Když je uživatel ověřen, Azure AD odešle token do webové aplikace pomocí přesměrování prostřednictvím prohlížeče.
* Přesměrování poskytuje webová aplikace ve formě identifikátoru URI přesměrování. Tento identifikátor URI přesměrování je registrován s objektem aplikace Azure AD. Může existovat několik identifikátorů URI přesměrování, protože aplikace může být nasazena na několika adresách URL. Takže webová aplikace bude také muset zadat přesměrování URI použít.
* Azure AD ověří, že identifikátor URI přesměrování odeslaný webovou aplikací je jedním z registrovaných identifikátorů URI přesměrování pro aplikaci.

## <a name="desktop-and-mobile-app-sign-in-flow-with-azure-ad"></a>Tok přihlášení k desktopovým a mobilním aplikacím pomocí Azure AD

Výše popsaný průtok platí s mírnými rozdíly pro stolní a mobilní aplikace.

Desktopové a mobilní aplikace mohou pro ověřování používat integrovaný webový ovládací prvek nebo systémový prohlížeč. Následující diagram znázorňuje, jak desktopová nebo mobilní aplikace používá ověřovací knihovnu Microsoftu (MSAL) k získání přístupových tokenů a volání webových rozhraní API.

![Desktopová aplikace, jak se zdá být](media/authentication-scenarios/desktop-app-how-it-appears-to-be.png)

MSAL používá prohlížeč získat tokeny. Stejně jako u webových aplikací se ověřování deleguje na Azure AD.

Vzhledem k tomu, že Azure AD ukládá stejnou identitu cookie v prohlížeči jako pro webové aplikace, pokud nativní nebo mobilní aplikace používá systémový prohlížeč okamžitě získá přihlašovací služby s odpovídající webové aplikace.

Ve výchozím nastavení používá msal systémový prohlížeč. Výjimkou jsou desktopové aplikace rozhraní .NET Framework, kde se integrovaný ovládací prvek používá k poskytování integrovanějšího uživatelského prostředí.

## <a name="next-steps"></a>Další kroky

* Seznamte se s [běžnými termíny v glosáři vývojářů platformy Microsoft pro identity.](developer-glossary.md)
* Další informace o dalších scénářích ověřování uživatelů podporovaných platformou identit Microsoftu najdete v tématu [Toky ověřování a scénáře aplikací.](authentication-flows-app-scenarios.md)
* Najdete [v tématu knihovny MSAL](msal-overview.md) informace o knihovnách Microsoftu, které vám pomohou vyvíjet aplikace, které pracují s účty Microsoft, účty Azure AD a uživateli Azure AD B2C v jediném, zjednodušeném programovacím modelu.
* Informace o konfiguraci ověřování pro aplikaci App Service najdete v tématu [Integrace služby App Service s platformou identit Microsoftu.](/azure/app-service/configure-authentication-provider-aad)
