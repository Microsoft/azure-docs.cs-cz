---
title: Ověřování v Microsoft Identity Platform | Azure
description: Seznamte se se základy ověřování na platformě Microsoft identity (v 2.0).
services: active-directory
author: rwike77
manager: CelesteDG
ms.assetid: 0c84e7d0-16aa-4897-82f2-f53c6c990fd9
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 02/03/2020
ms.author: ryanwi
ms.reviewer: jmprieur, saeeda, sureshja, hirsin
ms.custom: aaddev, identityplatformtop40, scenarios:getting-started
ms.openlocfilehash: 3ec965318da1361454b4a6bb78ed7147562b5fea
ms.sourcegitcommit: d322d0a9d9479dbd473eae239c43707ac2c77a77
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/12/2020
ms.locfileid: "79138514"
---
# <a name="authentication-basics"></a>Základy ověřování

## <a name="what-is-authentication"></a>Co je ověřování

Tento článek popisuje mnohé z konceptů ověřování, které budete potřebovat při vytváření chráněných webových aplikací, webových rozhraní API nebo aplikací, které volají chráněná webová rozhraní API. Pokud se vám zobrazí termín, který neznáte, vyzkoušejte si náš [Glosář](developer-glossary.md) nebo naše [videa Microsoftu pro identitu platforem](identity-videos.md) , která se týkají základních konceptů.

**Ověřování** je proces prokazující, na koho jste se rozhodli. V angličtině se pro ověřování někdy používá zkrácené slovo AuthN.

**Autorizace** je způsob, jakým se uděluje oprávnění ověřené strany k tomu, aby něco projednalo. Určuje, jaká data máte povolený přístup, a co s nimi můžete dělat. V angličtině se pro autorizaci někdy používá zkrácené slovo AuthZ.

Místo vytváření aplikací, které každý z nich zachovávají vlastní uživatelské jméno a heslo, což má za následek vysokou administrativní zátěž, když potřebujete přidat nebo odebrat uživatele napříč více aplikacemi, můžou aplikace delegovat tuto odpovědnost na centralizovaného zprostředkovatele identity.

Azure Active Directory (Azure AD) je centralizovaný poskytovatel identity v cloudu. Delegování ověřování a autorizace do IT umožňuje scénáře, jako jsou zásady podmíněného přístupu, které vyžadují, aby uživatel byl v určitém umístění, použití vícefaktorového ověřování a aby se uživatel mohl přihlásit jednou a pak automaticky přihlášené ke všem webovým aplikacím, které sdílejí stejný centralizovaný adresář. Tato funkce se označuje jako jednotné přihlašování (SSO).

Centralizovaný poskytovatel identity je ještě důležitější pro aplikace, které mají uživatele na celém světě, kteří se nemusí nutně přihlašovat ze sítě v podniku. Azure AD ověřuje uživatele a poskytuje přístupové tokeny. [Přístupový token](https://docs.microsoft.com/azure/active-directory/develop/developer-glossary#access-token) je token zabezpečení, který je vydaný autorizačním serverem. Obsahuje informace o uživateli a aplikaci, pro kterou je token určen. který se dá použít pro přístup k webovým rozhraním API a dalším chráněným prostředkům.

Platforma Microsoft Identity usnadňuje ověřování pro vývojáře aplikací tím, že poskytuje identitu jako službu a podporuje standardní protokoly, jako je [OAuth 2,0](https://oauth.net/2/) a [OpenID Connect](https://openid.net/connect/), a také open source knihovny pro různé platformy, které vám pomůžou rychle začít vytvářet kódování. Umožňuje vývojářům sestavovat aplikace, které přihlásí všechny identity Microsoftu, získat tokeny pro volání [Microsoft Graph](https://developer.microsoft.com/graph/), jiná rozhraní API Microsoftu nebo rozhraní API, která vývojáři sestavili. Další informace najdete v tématu [vývoj platformy Microsoft Identity Platform](about-microsoft-identity-platform.md).

### <a name="tenants"></a>Klienti

Cloud identity provider obsluhuje mnoho organizací. Aby bylo možné uživatele z různých organizací oddělit, je služba Azure AD rozdělená na klienty s jedním klientem na organizaci.

Klienti udržují přehled o uživatelích a jejich přidružených aplikacích. Platforma Microsoft identity podporuje také uživatele, kteří se přihlásí pomocí osobních účtů Microsoft.

Azure AD taky poskytuje Azure Active Directory B2C, aby se organizace mohli přihlašovat uživatelům, obvykle zákazníkům, pomocí sociálních identit, jako je třeba účet Google. Další informace najdete v [dokumentaci Azure Active Directory B2C](https://docs.microsoft.com/azure/active-directory-b2c) .

### <a name="security-tokens"></a>Tokeny zabezpečení

Tokeny zabezpečení obsahují informace o uživatelích a aplikacích. Azure AD používá tokeny založené na JSON (JWTs), které obsahují deklarace identity.

Deklarace identity poskytuje kontrolní výrazy týkající se jedné entity, jako je například [klientská aplikace](https://docs.microsoft.com/azure/active-directory/develop/developer-glossary#client-application) nebo [vlastník prostředku](https://docs.microsoft.com/azure/active-directory/develop/developer-glossary#resource-owner), k jiné entitě, jako je například [server prostředků](https://docs.microsoft.com/azure/active-directory/develop/developer-glossary#resource-server).

Deklarace identity jsou páry název-hodnota, které přenášejí fakta týkající se předmětu tokenu. Deklarace identity může například obsahovat fakta o objektu zabezpečení, který byl ověřen [autorizačním serverem](https://docs.microsoft.com/azure/active-directory/develop/developer-glossary#authorization-server). Deklarace identity přítomné v daném tokenu závisí na mnoha věcí, včetně typu tokenu, typu přihlašovacího údaje použitého k ověření předmětu, konfigurace aplikace atd.

Aplikace mohou používat deklarace identity pro různé úlohy, jako například:

* Ověřování tokenu
* Identifikace tenanta předmětu tokenu
* Zobrazení informací o uživateli
* Určení autorizace subjektu

Deklarace identity se skládá z párů klíč-hodnota, které poskytují informace, jako jsou:

* Server tokenu zabezpečení, který vygeneroval token
* Datum, kdy se token vygeneroval
* Předmět (například uživatel – s výjimkou démonů)
* Cílová skupina, což je aplikace, pro kterou se token vygeneroval
* Aplikace (klient), která požádala o token. V případě webových aplikací to může být stejné jako cílová skupina.

Podrobnější informace o deklaraci identity najdete v tématu [přístupové tokeny](access-tokens.md) a [tokeny ID](id-tokens.md).

Je to až aplikace, pro kterou se token vygeneroval, webovou aplikaci, která se přihlásila jako uživatel, nebo webové rozhraní API, které se zavolá, aby se token ověřil. Token je podepsaný serverem tokenu zabezpečení (STS) pomocí privátního klíče. Služba STS publikuje odpovídající veřejný klíč. K ověření tokenu aplikace ověří podpis pomocí veřejného klíče služby STS a ověří, zda byl podpis vytvořen pomocí privátního klíče.

Tokeny jsou platné pouze po omezené množství času. Služba STS obvykle poskytuje dvojici tokenů: přístupový token pro přístup k aplikaci nebo chráněnému prostředku a obnovovací token, který se používá k aktualizaci přístupového tokenu, když je přístupový token blízko vypršení platnosti.

Přístupové tokeny se předávají webovému rozhraní API jako nosný token v hlavičce `Authorization`. Aplikace může službě STS poskytnout obnovovací token a pokud se uživatel k aplikaci neodvolává, vrátí nový přístupový token a nový obnovovací token. To je způsob, jakým se zpracovává scénář někoho, kdo opouští podnik. Když STS obdrží obnovovací token, nevydá jiný platný přístupový token, pokud už uživatel není autorizovaný.

## <a name="application-model"></a>Aplikační model

Aplikace se můžou přihlašovat sami nebo delegovat přihlášení k poskytovateli identity. V tématu [toky ověřování a scénáře aplikací](authentication-flows-app-scenarios.md) se dozvíte o scénářích přihlašování podporovaných službou Azure AD.

Aby mohl poskytovatel identity zjistit, jestli má uživatel přístup ke konkrétní aplikaci, musí být uživatel i aplikace zaregistrované u poskytovatele identity. Při registraci aplikace ve službě Azure AD poskytujete konfiguraci identity pro vaši aplikaci, která umožňuje integraci se službou Azure AD. Registrace aplikace vám taky umožní:

* Přizpůsobte si branding své aplikace v dialogovém okně přihlášení. To je důležité, protože se jedná o první prostředí, které bude mít uživatel s vaší aplikací.
* Rozhodněte se, jestli chcete uživatelům umožnit, aby se přihlásili jenom v případě, že patří do vaší organizace. Toto je jediná klientská aplikace. Nebo Umožněte uživatelům, aby se přihlásili pomocí pracovního nebo školního účtu. Toto je víceklientské aplikace. Můžete také umožnit osobní účty Microsoft nebo sociální účet z LinkedInu, Google atd.
* Požádat o oprávnění rozsahu. Můžete například požádat o obor "User. Read", který uděluje oprávnění ke čtení profilu přihlášeného uživatele.
* Definujte obory definující přístup k webovému rozhraní API. Když aplikace chce získat přístup k vašemu rozhraní API, obvykle bude potřebovat požádat o oprávnění k definovaným oborům.
* Sdílejte tajný klíč k Azure AD, který prokáže identitu aplikace službě Azure AD.  To je důležité v případě, kdy je aplikace důvěrná klientská aplikace. Důvěrná klientská aplikace je aplikace, která může bezpečně uchovávat přihlašovací údaje. Pro uložení přihlašovacích údajů vyžadují důvěryhodný back-end Server.

Po registraci se aplikaci udělí jedinečný identifikátor, který aplikace sdílí se službou Azure AD při žádosti o tokeny. Pokud je aplikace [důvěrná klientská aplikace](https://docs.microsoft.com/azure/active-directory/develop/developer-glossary#client-application), bude také sdílet tajný klíč nebo veřejný klíč * – v závislosti na tom, zda byly použity certifikáty nebo tajné klíče.

Platforma Microsoft Identity reprezentuje aplikace pomocí modelu, který splňuje dvě hlavní funkce:

* Identifikujte aplikaci pomocí ověřovacích protokolů, které podporuje.
* Zadejte všechny identifikátory, adresy URL, tajné klíče a související informace, které jsou potřeba k ověření.

Platforma Microsoft identity:

* Obsahuje všechna data potřebná k podpoře ověřování za běhu.
* Obsahuje všechna data pro rozhodování o tom, k jakým prostředkům může aplikace potřebovat přístup, a za jakých okolností by měla být daná žádost splněna.
* Poskytuje infrastrukturu pro implementaci zřizování aplikací v tenantovi vývojáře aplikace a na jakéhokoli jiného tenanta Azure AD.
* Zpracovává souhlas uživatele během doby žádosti o tokeny a usnadňuje dynamické zřizování aplikací napříč klienty.

Souhlas je proces vlastníka prostředku, který uděluje autorizaci pro klientské aplikace pro přístup k chráněným prostředkům v rámci konkrétních oprávnění jménem vlastníka prostředku. Platforma Microsoft identity:

* Umožňuje uživatelům a správcům dynamicky udělovat nebo odepírat souhlas s tím, aby aplikace jejich jménem měla přístup k prostředkům.
* Umožňuje správcům nakonec rozhodnout, co můžou aplikace provádět a kteří uživatelé můžou konkrétní aplikace používat a jak se přistupuje k prostředkům adresáře.

Na platformě Microsoft identity je [objekt aplikace](https://docs.microsoft.com/azure/active-directory/develop/developer-glossary#application-object) popisuje aplikaci. V době nasazení používá platforma Microsoft identity objekt aplikace jako podrobný plán k vytvoření [instančního](https://docs.microsoft.com/azure/active-directory/develop/developer-glossary#service-principal-object)objektu, který představuje konkrétní instanci aplikace v rámci adresáře nebo tenanta. Instanční objekt definuje, co může aplikace dělat v určitém cílovém adresáři, kdo ho může používat, k jakým prostředkům má přístup, a tak dále. Platforma Microsoft Identity vytvoří instanční objekt z objektu aplikace prostřednictvím **souhlasu**.

Následující diagram znázorňuje zjednodušený postup zřizování platformy Microsoft Identity Platform založený na základě souhlasu. Zobrazuje dva klienty: a a B. tenant A vlastní aplikaci. Tenant B vytváří instanci aplikace prostřednictvím instančního objektu.  

![Zjednodušený tok zřizování s využitím souhlasu](./media/authentication-scenarios/simplified-provisioning-flow-consent-driven.svg)

V tomto toku zřizování:

1. Uživatel z tenanta B se pokusí přihlásit k aplikaci, koncový bod autorizace požaduje token pro aplikaci.
1. Přihlašovací údaje uživatele se získávají a ověřují pro ověřování.
1. Uživatel je vyzván k poskytnutí souhlasu aplikace, aby získal přístup k tenantovi B.
1. Platforma Microsoft identity používá aplikační objekt v tenantovi A jako plán pro vytvoření instančního objektu v tenantovi B.
1. Uživatel obdrží požadovaný token.

Tento postup můžete opakovat pro další klienty. Tenant A uchovává podrobný plán aplikace (objekt aplikace). Uživatelům a správcům všech ostatních tenantů, na kterých se aplikace uděluje, si můžete řídit, co může aplikace provádět přes odpovídající objekt instančního objektu v každém tenantovi. Další informace najdete v tématu [aplikační a instanční objekty v platformě Microsoft Identity Platform](app-objects-and-service-principals.md).

## <a name="web-app-sign-in-flow-with-azure-ad"></a>Postup přihlášení k webové aplikaci pomocí Azure AD

Když uživatel přejde v prohlížeči do webové aplikace, dojde k následujícímu:

* Webová aplikace určuje, jestli je uživatel ověřený.
* Pokud se uživatel neověřuje, Webová aplikace se přihlásí k Azure AD, aby se přihlásili uživateli. Toto přihlášení bude vyhovovat zásadám organizace, což může znamenat, že uživatel musí zadat své přihlašovací údaje, použít vícefaktorové ověřování nebo vůbec nepoužívat heslo (například Windows Hello).
* Uživateli se zobrazí výzva k vyjádření souhlasu s přístupem, který klientská aplikace potřebuje. Důvodem je, že klientské aplikace musí být zaregistrované ve službě Azure AD, aby služba Azure AD mohla poskytovat tokeny představující přístup, ke kterému se uživatel poslal.

Po úspěšném ověření uživatele:

* Azure AD pošle token webové aplikaci.
* Soubor cookie je uložen přidružený k doméně služby Azure AD, která obsahuje identitu uživatele v JAR souboru cookie prohlížeče. Když aplikace příště používá prohlížeč k přechodu do koncového bodu autorizace Azure AD, prohlížeč prezentuje soubor cookie, takže se uživatel nemusí znovu přihlašovat. To je také způsob, jakým je dosaženo jednotného přihlašování. Soubor cookie se vytvoří pomocí Azure AD a může ho rozumět jenom Azure AD.
* Webová aplikace potom ověří token. Pokud je ověření úspěšné, Webová aplikace zobrazí chráněnou stránku a uloží soubor cookie relace do jar v souboru cookie prohlížeče. Když uživatel přejde na jinou stránku, Webová aplikace ví, že se uživatel ověřuje na základě souboru cookie relace.

Následující sekvenční diagram shrnuje tuto interakci:

![proces ověřování webové aplikace](media/authentication-scenarios/web-app-how-it-appears-to-be.png)

### <a name="how-a-web-app-determines-if-the-user-is-authenticated"></a>Jak webová aplikace určuje, jestli je uživatel ověřený

Vývojáři webové aplikace mohou určit, zda některé stránky vyžadují ověření. Například v ASP.NET/ASP.NET Core je to provedeno přidáním atributu `[Authorize]` k akcím kontroleru. 

Tento atribut způsobí, že ASP.NET zkontroluje přítomnost souboru cookie relace, který obsahuje identitu uživatele. Pokud soubor cookie přítomen není, ASP.NET přesměruje ověřování na zadaného zprostředkovatele identity. Pokud je poskytovatel identity Azure AD, přesměruje webová aplikace ověřování na `https://login.microsoftonline.com`, které zobrazí přihlašovací dialog.

### <a name="how-a-web-app-delegates-sign-in-to-azure-ad-and-obtains-a-token"></a>Způsob, jakým webová aplikace přihlašuje přihlášení ke službě Azure AD a získá token

K ověřování uživatelů dochází prostřednictvím prohlížeče. Protokol OpenID používá standardní zprávy protokolu HTTP.
* Webová aplikace pošle do prohlížeče protokol HTTP 302 (přesměrování), aby mohl používat službu Azure AD.
* Po ověření uživatele služba Azure AD pošle token do webové aplikace pomocí přesměrování přes prohlížeč.
* Přesměrování je poskytováno webovou aplikací ve formě identifikátoru URI přesměrování. Tento identifikátor URI přesměrování je zaregistrován u objektu aplikace služby Azure AD. Může existovat několik identifikátorů URI pro přesměrování, protože aplikaci je možné nasadit na několik adres URL. Proto bude webová aplikace také muset určit identifikátor URi pro přesměrování, který se má použít.
* Azure AD ověří, že identifikátor URI přesměrování odesílaný webovou aplikací je jedním z registrovaných identifikátorů URI pro přesměrování pro aplikaci.

## <a name="desktop-and-mobile-app-sign-in-flow-with-azure-ad"></a>Postup přihlášení k desktopovým a mobilním aplikacím pomocí Azure AD

Výše popsaný postup se týká mírně rozdílů pro stolní a mobilní aplikace.

Desktopové a mobilní aplikace můžou k ověřování používat vložený webový ovládací prvek nebo systémový prohlížeč. Následující diagram znázorňuje, jak desktopová nebo mobilní aplikace používá knihovnu Microsoft Authentication Library (MSAL) k získání přístupových tokenů a volání webových rozhraní API.

![Desktopová aplikace, jak vypadá](media/authentication-scenarios/desktop-app-how-it-appears-to-be.png)

MSAL pomocí prohlížeče získá tokeny. Stejně jako u webových aplikací je ověřování delegované do Azure AD.

Vzhledem k tomu, že Azure AD ukládá stejný soubor cookie identity v prohlížeči jako v případě webových aplikací, pokud nativní nebo mobilní aplikace používá prohlížeč systému, bude ihned přihlášená k odpovídající webové aplikaci.

Ve výchozím nastavení používá MSAL prohlížeč systému. Výjimkou je .NET Framework desktopové aplikace, kde se k zajištění více integrovaných uživatelských prostředí používá integrovaný ovládací prvek.

## <a name="next-steps"></a>Další kroky

* Seznamte se s běžnými podmínkami na [webu Microsoft Identity Platform Developer Glosář](developer-glossary.md) .
* Další informace o dalších scénářích pro ověřování uživatelů, které podporuje platforma Microsoft identity, najdete v tématu [toky ověřování a scénáře aplikací](authentication-flows-app-scenarios.md) .
* V tématu [knihovny MSAL](msal-overview.md) najdete informace o knihovnách Microsoftu, které vám pomůžou vyvíjet aplikace, které pracují s účty Microsoft, účty Azure AD a Azure AD B2C uživatelů v jednom, zjednodušeném programovacím modelu.
* Informace o tom, jak nakonfigurovat ověřování pro App Service aplikaci, najdete v tématu věnovaném [integraci App Service s Microsoft Identity platformou](/azure/app-service/configure-authentication-provider-aad) .
