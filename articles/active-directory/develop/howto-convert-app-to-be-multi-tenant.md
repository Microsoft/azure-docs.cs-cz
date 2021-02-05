---
title: Vytváření aplikací přihlašování uživatelů Azure AD
titleSuffix: Microsoft identity platform
description: Ukazuje, jak vytvořit aplikaci s více klienty, která se může přihlásit uživatele z libovolného klienta Azure Active Directory.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 10/27/2020
ms.author: ryanwi
ms.reviewer: marsma, jmprieur, lenalepa, sureshja, kkrishna
ms.custom: aaddev
ms.openlocfilehash: 825a7d8c53552120a861657c7f3df7ae8f488c18
ms.sourcegitcommit: 2817d7e0ab8d9354338d860de878dd6024e93c66
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/05/2021
ms.locfileid: "99581716"
---
# <a name="how-to-sign-in-any-azure-active-directory-user-using-the-multi-tenant-application-pattern"></a>Postup: Přihlášení libovolného uživatele služby Azure Active Directory pomocí vzoru aplikace s více tenanty

Pokud nabízíte aplikaci SaaS (software jako služba) pro mnoho organizací, můžete aplikaci nakonfigurovat tak, aby přijímala přihlášení z libovolného tenanta Azure Active Directory (Azure AD). Tato konfigurace se nazývá *Vytvoření víceklientské aplikace*. Uživatelé v tenantovi Azure AD se budou moci přihlásit ke své aplikaci po jejím souhlasu s používáním svého účtu s vaší aplikací.

Pokud máte existující aplikaci, která má svůj vlastní systém účtů, nebo podporuje jiné typy přihlášení od jiných poskytovatelů cloudu, je přidání přihlášení k Azure AD ze všech klientů jednoduché. Stačí zaregistrovat aplikaci, přidat přihlašovací kód přes OAuth2, OpenID připojit nebo SAML a vložit do aplikace [tlačítko Přihlásit se účtem Microsoft][AAD-App-Branding] .

> [!NOTE]
> V tomto článku se předpokládá, že jste už obeznámení s vytvářením aplikace pro jednoho tenanta pro Azure AD. Pokud nejste, začněte s některým z rychlých startů na [domovské stránce Příručky pro vývojáře][AAD-Dev-Guide].

Existují čtyři kroky k převedení aplikace na víceklientské aplikace Azure AD:

1. [Aktualizace registrace aplikace pro více tenantů](#update-registration-to-be-multi-tenant)
2. [Aktualizace kódu pro odesílání požadavků do koncového bodu/běžné](#update-your-code-to-send-requests-to-common)
3. [Aktualizace kódu pro zpracování více hodnot vystavitelů](#update-your-code-to-handle-multiple-issuer-values)
4. [Pochopení souhlasu uživatele a správce a provádění správných změn kódu](#understand-user-and-admin-consent)

Pojďme se podrobněji podívat na jednotlivé kroky. Můžete také přejít přímo k ukázce [sestavení webové aplikace SaaS s více klienty, která volá Microsoft Graph pomocí Azure AD a OpenID Connect](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/master/2-WebApp-graph-user/2-3-Multi-Tenant/README.md).

## <a name="update-registration-to-be-multi-tenant"></a>Aktualizace registrace na více tenantů

Ve výchozím nastavení jsou registrace webové aplikace nebo rozhraní API ve službě Azure AD jedním klientem. Registraci pro více tenantů můžete udělat tak, že v podokně **ověřování** registrace vaší aplikace v [Azure Portal][AZURE-portal] zadáte přepínač **podporované typy účtů** a nakonfigurujete je na **účty v libovolném organizačním adresáři**.

Předtím, než může být aplikace vytvořená pro více tenantů, vyžaduje Azure AD identifikátor URI ID aplikace, který má být globálně jedinečný. Identifikátor URI ID aplikace je jedním ze způsobů, kterými se může aplikace ve zprávách protokolu identifikovat. U aplikace s jedním tenantem stačí, když bude identifikátor URI ID aplikace jedinečný v rámci daného tenanta. U aplikace s více tenanty musí být globálně jedinečný, aby služba Azure AD aplikaci našla mezi všemi tenanty. Globální jedinečnost se vynucuje požadavkem, aby Identifikátor URI ID aplikace obsahoval název hostitele, který odpovídá ověřené doméně tenanta Azure AD.

Ve výchozím nastavení mají aplikace vytvořené prostřednictvím Azure Portal globálně jedinečný identifikátor URI ID aplikace nastavený při vytváření aplikace, ale tuto hodnotu můžete změnit. Například pokud je název vašeho tenanta contoso.onmicrosoft.com, pak bude platný identifikátor URI ID aplikace `https://contoso.onmicrosoft.com/myapp` . Pokud má tenant ověřenou doménu z `contoso.com` , bude také platný identifikátor URI ID aplikace `https://contoso.com/myapp` . Pokud identifikátor URI ID aplikace nepoužívá tento vzor, nastavení aplikace jako aplikace s více tenanty se nezdaří.

## <a name="update-your-code-to-send-requests-to-common"></a>Aktualizace kódu pro odesílání požadavků do/běžné

V aplikaci s jedním klientem se žádosti o přihlášení odesílají do koncového bodu přihlášení klienta. Například pro contoso.onmicrosoft.com by koncový bod byl: `https://login.microsoftonline.com/contoso.onmicrosoft.com` . Požadavky odeslané na koncový bod tenanta se můžou přihlašovat uživatelům (nebo hostů) v tomto tenantovi k aplikacím v tomto tenantovi.

U víceklientské aplikace neví aplikace, ze které uživatele pochází, takže nemůžete odesílat žádosti do koncového bodu klienta. Místo toho se požadavky odesílají do koncového bodu, který je ve všech klientech Azure AD. `https://login.microsoftonline.com/common`

Když Microsoft Identity Platform dostane požadavek na koncový bod/běžné, podepíše uživatele v a. v důsledku toho zjistí, ze kterého tenanta uživatel pochází. Koncový bod/běžné funguje se všemi ověřovacími protokoly, které podporuje Azure AD: OpenID Connect, OAuth 2,0, SAML 2,0 a WS-Federation.

Přihlašovací odpověď na aplikaci pak obsahuje token představující uživatele. Hodnota vystavitele v tokenu oznamuje aplikaci, ze které se uživatel nachází. Když se odpověď vrátí z koncového bodu/běžné, hodnota vystavitele tokenu odpovídá tenantovi uživatele.

> [!IMPORTANT]
> Koncový bod/běžné není tenant a není vystavitelem, je to jenom multiplexor. Při použití/běžné je třeba aktualizovat logiku vaší aplikace na ověření tokenů, aby se tento účet zohlednil.

## <a name="update-your-code-to-handle-multiple-issuer-values"></a>Aktualizace kódu pro zpracování více hodnot vystavitelů

Webové aplikace a webová rozhraní API přijímají a ověřují tokeny z platformy Microsoft Identity Platform.

> [!NOTE]
> I když nativní klientské aplikace požadují a získávají tokeny od platformy Microsoft identity, jejich odeslání do rozhraní API, kde jsou ověřeny. Nativní aplikace neověřují přístupové tokeny a musí je nakládat jako neprůhledné.

Pojďme se podívat, jak aplikace ověřuje tokeny, které obdrží od platformy Microsoft identity. Jediná klientská aplikace obvykle přebírá hodnotu koncového bodu jako:

```http
https://login.microsoftonline.com/contoso.onmicrosoft.com
```

... a používá ho k vytvoření adresy URL metadat (v tomto případě OpenID Connect), jako je:

```http
https://login.microsoftonline.com/contoso.onmicrosoft.com/.well-known/openid-configuration
```

stažení dvou důležitých informací, které se používají k ověření tokenů: podpisové klíče klienta a hodnota vystavitele.

Každý tenant služby Azure AD má jedinečnou hodnotu vystavitele formuláře:

```http
https://sts.windows.net/31537af4-6d77-4bb9-a681-d2394888ea26/
```

... kde hodnota identifikátoru GUID je verze s identifikátorem ID tenanta v tenantovi, která je bezpečná pro přejmenování. Pokud vyberete odkaz předchozí metadata pro `contoso.onmicrosoft.com` , můžete tuto hodnotu vystavitele zobrazit v dokumentu.

Když aplikace pro jednoho tenanta ověří token, zkontroluje signaturu tokenu proti podpisovým klíčům z dokumentu metadat. Tento test umožňuje, aby se zajistilo, že hodnota vystavitele v tokenu odpovídá tomu, který byl nalezen v dokumentu metadat.

Vzhledem k tomu, že koncový bod/běžné neodpovídá tenantovi a není vystavitelem, při prohlédnutí hodnoty vystavitele v metadatech pro/běžné má místo skutečné hodnoty adresu URL šablony:

```http
https://sts.windows.net/{tenantid}/
```

Proto aplikace s více klienty nemůže ověřit tokeny jenom tak, že odpovídají hodnotě vystavitele v metadatech s `issuer` hodnotou v tokenu. Víceklientská aplikace potřebuje logiku k rozhodnutí, které hodnoty vystavitele jsou platné a které nejsou založené na části ID tenanta v hodnotě vystavitele.

Pokud například aplikace s více klienty povolí pouze přihlášení ze specifických klientů, kteří se zaregistrovali ke své službě, musí ověřit buď hodnotu vystavitele, nebo `tid` hodnotu deklarace identity v tokenu, aby se zajistilo, že se tenant nachází v seznamu předplatitelů. Pokud se víceklientské aplikace týká jenom jednotlivců a neprovádí rozhodování o přístupu na základě tenantů, může hodnotu vystavitele zcela ignorovat.

V [ukázkách s více klienty][AAD-Samples-MT]je ověřování vystavitele zakázané, aby se mohl přihlásit libovolný tenant služby Azure AD.

## <a name="understand-user-and-admin-consent"></a>Vysvětlení souhlasu uživatele a správce

Aby se uživatel mohl přihlásit k aplikaci v Azure AD, musí být aplikace reprezentovaná v tenantovi uživatele. To umožňuje organizaci provádět akce, jako je použití jedinečných zásad, když se uživatelé z jejich tenanta přihlásí k aplikaci. V případě aplikace s jedním klientem je tato registrace snazší; je to ten, který se stane při registraci aplikace v [Azure Portal][AZURE-portal].

Pro víceklientské aplikace, počáteční registrace aplikace v tenantovi Azure AD, kterou používá vývojář. Když se uživatel z jiného tenanta do aplikace poprvé přihlásí, služba Azure AD si vyžádá souhlas s oprávněními požadovanými aplikací. Pokud souhlasí, pak se v tenantovi uživatele vytvoří reprezentace aplikace označovaná jako *instanční objekt* a přihlášení může pokračovat. Delegování je také vytvořeno v adresáři, který zaznamenává vyjádření souhlasu uživatele s aplikací. Podrobnosti o aplikacích a objektech aplikace ServicePrincipal a o tom, jak se vzájemně vzájemně vztahují, najdete v tématu [objekty aplikace a instanční objekty služby][AAD-App-SP-Objects].

![Ukazuje souhlas s jednou vrstvou aplikace.][Consent-Single-Tier]

Toto používání souhlasu je ovlivněno oprávněními požadovanými aplikací. Platforma Microsoft Identity Platform podporuje dva druhy oprávnění, jenom aplikace a delegované.

* Delegované oprávnění uděluje aplikaci možnost chovat se jako přihlášený uživatel pro podmnožinu věcí, kterou může uživatel provádět. Aplikaci můžete například udělit delegovanému oprávnění ke čtení kalendáře přihlášeného uživatele.
* Oprávnění pouze pro aplikaci je uděleno přímo identitě aplikace. Aplikaci můžete například udělit jenom oprávnění ke čtení seznamu uživatelů v tenantovi bez ohledu na to, kdo je přihlášený k aplikaci.

Některé oprávnění může zasílat běžný uživatel, zatímco jiné vyžadují souhlas správce tenanta.

Další informace o souhlasu uživatele a správce najdete v tématu [Konfigurace pracovního postupu pro vyjádření souhlasu správce](../manage-apps/configure-admin-consent-workflow.md).

### <a name="admin-consent"></a>Souhlas správce

Oprávnění pouze pro aplikace vždy vyžadují souhlas správce tenanta. Pokud vaše aplikace požaduje oprávnění pouze pro přístup k aplikacím a uživatel se pokusí přihlásit k aplikaci, zobrazí se chybová zpráva oznamující, že uživatel nemůže souhlasit.

Určitá delegovaná oprávnění také vyžadují souhlas správce tenanta. Například možnost vracet se zpátky do služby Azure AD, protože přihlášený uživatel vyžaduje souhlas správce tenanta. Stejně jako oprávnění pouze pro aplikace, pokud se běžný uživatel pokusí přihlásit k aplikaci, která žádá o delegované oprávnění, které vyžaduje souhlas správce, aplikace obdrží chybu. Bez ohledu na to, jestli oprávnění vyžaduje souhlas správce, se určí vývojář, který prostředek publikoval, a najdete ho v dokumentaci k prostředku. Dokumentace k oprávněním pro [rozhraní Microsoft Graph API][MSFT-Graph-permission-scopes] určuje, která oprávnění vyžadují souhlas správce.

Pokud vaše aplikace používá oprávnění, která vyžadují souhlas správce, měli byste gesto, jako je tlačítko nebo odkaz, kde může správce zahájit akci. Požadavek, který vaše aplikace posílá pro tuto akci, je obvyklým požadavkem na autorizaci OAuth2/OpenID Connect, který také obsahuje `prompt=admin_consent` parametr řetězce dotazu. Jakmile správce souhlasí a objekt služby se vytvoří v tenantovi zákazníka, následné žádosti o přihlášení nepotřebují `prompt=admin_consent` parametr. Vzhledem k tomu, že správce rozhodl, že požadovaná oprávnění jsou přijatelná, žádné další uživatele v tenantovi nebudou vyzváni k jejich souhlasu od tohoto okamžiku.

Správce klienta může zakázat možnost pro běžné uživatele, aby jim souhlasili s aplikacemi. Pokud je tato možnost zakázaná, bude pro použití v tenantovi vždycky potřeba souhlas správce. Pokud chcete otestovat aplikaci pomocí souhlasu koncového uživatele, můžete najít přepínač konfigurace v části [Azure Portal][AZURE-portal] v části **[nastavení uživatele](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/UserSettings/menuId/)** v části **podnikové aplikace**.

Tento `prompt=admin_consent` parametr můžou používat i aplikace, které vyžadují oprávnění, které nevyžadují souhlas správce. V takovém případě je třeba použít například v případě, že aplikace vyžaduje prostředí, kde se správce klienta zaregistruje jednou, a žádné další uživatele nebudou vyzváni k souhlasu od tohoto okamžiku.

Pokud aplikace vyžaduje souhlas správce a správce se přihlásí bez `prompt=admin_consent` odeslání parametru, když správce úspěšně souhlasí s aplikací, bude platit **pouze pro svůj uživatelský účet**. Pravidelným uživatelům se stále nebude moci přihlásit ani vyjádřit souhlas s aplikací. Tato funkce je užitečná v případě, že chcete dát správci tenanta možnost prozkoumat vaši aplikaci předtím, než povolíte ostatním uživatelům přístup.

### <a name="consent-and-multi-tier-applications"></a>Souhlas a vícevrstvé aplikace

Vaše aplikace může mít více vrstev, z nichž každý představuje vlastní registraci ve službě Azure AD. Například nativní aplikace, která volá webové rozhraní API, nebo webovou aplikaci, která volá webové rozhraní API. V obou těchto případech klient (nativní aplikace nebo webová aplikace) požaduje oprávnění k volání prostředku (webové rozhraní API). Aby bylo možné klienta úspěšně zaslat do tenanta zákazníka, musí již v tenantovi zákazníka existovat všechny prostředky, na které žádá oprávnění. Pokud tato podmínka není splněna, služba Azure AD vrátí chybu, kterou je třeba přidat do prostředku.

#### <a name="multiple-tiers-in-a-single-tenant"></a>Více vrstev v jednom tenantovi

To může být problém, pokud se vaše logická aplikace skládá ze dvou nebo více registrací aplikace, například samostatného klienta a prostředku. Jak napřed získat prostředek do tenanta zákazníka? Azure AD pokrývá tento případ tím, že umožňuje klientovi a prostředku souhlas v jednom kroku. Uživatel uvidí celkový součet oprávnění vyžádaného klientem i prostředkem na stránce souhlasu. Aby bylo možné toto chování povolit, musí registrace aplikace prostředku zahrnovat ID aplikace klienta jako `knownClientApplications` v [manifestu aplikace][AAD-App-Manifest]. Příklad:

```json
"knownClientApplications": ["94da0930-763f-45c7-8d26-04d5938baab2"]
```

To je znázorněno ve vícevrstvém nativním klientovi, který volá ukázku webového rozhraní API v části [související obsah](#related-content) na konci tohoto článku. Následující diagram poskytuje přehled o souhlasu vícevrstvé aplikace zaregistrované v jednom tenantovi.

![Znázorňuje vyjádření souhlasu s více úrovněmi známé klientské aplikace.][Consent-Multi-Tier-Known-Client]

#### <a name="multiple-tiers-in-multiple-tenants"></a>Více vrstev ve více klientech

K podobnému případu dojde, pokud jsou různé úrovně aplikace zaregistrované v různých klientech. Zvažte například případ sestavení nativní klientské aplikace, která volá rozhraní API Exchange Online. Pro vývoj nativní aplikace a novější pro spuštění nativní aplikace v tenantovi zákazníka musí být k dispozici objekt zabezpečení Exchange Online. V takovém případě musí vývojář a zákazník koupit Exchange Online, aby se instanční objekt vytvořil ve svých klientech.

Pokud se jedná o rozhraní API vytvořené organizací jinou než Microsoft, vývojář rozhraní API musí poskytnout způsob, jak zákazníkům udělit souhlas s tím, aby si tuto aplikaci mohli v tenantovi svých zákazníků vyjádřit. Doporučeným návrhem je vývojář třetí strany, který sestaví rozhraní API tak, aby mohl také fungovat jako webový klient pro implementaci registrace. Použijte následující postup:

1. Postupujte podle předchozích částí a ujistěte se, že rozhraní API implementuje požadavky na registraci a kódování aplikace pro více tenantů.
2. Kromě vystavení oborů a rolí rozhraní API se ujistěte, že registrace zahrnuje oprávnění "přihlašovat a číst profil uživatele" (ve výchozím nastavení je k dispozici).
3. Implementujte přihlašovací nebo registrační stránku ve webovém klientovi a postupujte podle pokynů pro [vyjádření souhlasu správce](#admin-consent) .
4. Jakmile uživatel souhlasí s aplikací, vytvoří se ve svém tenantovi odkazy na delegování objektu služby a souhlasu a nativní aplikace může získat tokeny pro toto rozhraní API.

Následující diagram poskytuje přehled o souhlasu vícevrstvé aplikace zaregistrované v různých klientech.

![Ukazuje souhlas s vícevrstvou aplikací pro více stran.][Consent-Multi-Tier-Multi-Party]

### <a name="revoking-consent"></a>Odvolávání souhlasu

Uživatelé a správci můžou kdykoli odvolat svůj souhlas s vaší aplikací:

* Uživatelé odvolají přístup k jednotlivým aplikacím odebráním ze seznamu [aplikací na přístupovém panelu][AAD-Access-Panel] .
* Správci odvolají přístup k aplikacím jejich odebráním pomocí oddílu [podnikové aplikace](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps) v [Azure Portal][AZURE-portal].

Pokud správce souhlasí s aplikací pro všechny uživatele v tenantovi, uživatelé nemůžou přístup odvolat individuálně. Jenom správce může odvolat přístup a jenom pro celou aplikaci.

## <a name="multi-tenant-applications-and-caching-access-tokens"></a>Víceklientské aplikace a přístupové tokeny pro ukládání do mezipaměti

Víceklientské aplikace mohou také získat přístupové tokeny pro volání rozhraní API, která jsou chráněna službou Azure AD. Běžnou chybou při použití knihovny Microsoft Authentication Library (MSAL) s více klienty aplikace je prvotní vyžádání tokenu pro uživatele pomocí/běžné, přijetí odpovědi a následnému vyžádání dalšího tokenu pro stejného uživatele pomocí/Common. Vzhledem k tomu, že odpověď z Azure AD přichází z tenanta, ne/běžné, MSAL uloží token jako z tenanta. Následné volání/běžné pro získání přístupového tokenu pro uživatele, který položku mezipaměti neobdrží, a uživatel je vyzván, aby se znovu přihlásil. Aby nedocházelo k chybějící mezipaměti, zajistěte, aby se v koncovém bodu klienta provedla další volání již přihlášeného uživatele.

## <a name="related-content"></a>Související obsah

* [Ukázka víceklientské aplikace](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/master/2-WebApp-graph-user/2-3-Multi-Tenant/README.md)
* [Pokyny pro branding aplikací][AAD-App-Branding]
* [Aplikační objekty a instanční objekty služby][AAD-App-SP-Objects]
* [Integrace aplikací se službou Azure Active Directory][AAD-Integrating-Apps]
* [Přehled rozhraní pro vyjádření souhlasu][AAD-Consent-Overview]
* [Microsoft Graph obory oprávnění rozhraní API][MSFT-Graph-permission-scopes]

## <a name="next-steps"></a>Další kroky

V tomto článku jste zjistili, jak vytvořit aplikaci, která se může přihlásit uživatele z libovolného tenanta Azure AD. Po povolení jednotného Sign-On (SSO) mezi vaší aplikací a službou Azure AD můžete také aktualizovat aplikaci pro přístup k rozhraním API vystaveným prostředky Microsoftu, jako je Microsoft 365. To vám umožní nabízet v aplikaci přizpůsobené prostředí, například zobrazení kontextových informací uživatelům, jako je například profilový obrázek nebo jejich další schůzka v kalendáři.

Další informace o tom, jak volat rozhraní API pro Azure AD a Microsoft 365 služby, jako jsou Exchange, SharePoint, OneDrive, OneNote a další, najdete v [Microsoft Graph API][MSFT-Graph-overview].

<!--Reference style links IN USE -->
[AAD-Access-Panel]:  https://myapps.microsoft.com
[AAD-App-Branding]:howto-add-branding-in-azure-ad-apps.md
[AAD-App-Manifest]:reference-azure-ad-app-manifest.md
[AAD-App-SP-Objects]:app-objects-and-service-principals.md
[AAD-Auth-Scenarios]:authentication-scenarios.md
[AAD-Consent-Overview]:consent-framework.md
[AAD-Dev-Guide]:azure-ad-developers-guide.md
[AAD-Integrating-Apps]:quickstart-v1-integrate-apps-with-azure-ad.md
[AAD-Samples-MT]: /samples/browse/?products=azure-active-directory
[AAD-Why-To-Integrate]: ./active-directory-how-to-integrate.md
[AZURE-portal]: https://portal.azure.com
[MSFT-Graph-overview]: /graph/
[MSFT-Graph-permission-scopes]: /graph/permissions-reference

<!--Image references-->
[AAD-Sign-In]: ./media/active-directory-devhowto-multi-tenant-overview/sign-in-with-microsoft-light.png
[Consent-Single-Tier]: ./media/howto-convert-app-to-be-multi-tenant/consent-flow-single-tier.svg
[Consent-Multi-Tier-Known-Client]: ./media/howto-convert-app-to-be-multi-tenant/consent-flow-multi-tier-known-clients.svg
[Consent-Multi-Tier-Multi-Party]: ./media/howto-convert-app-to-be-multi-tenant/consent-flow-multi-tier-multi-party.svg

<!--Reference style links -->
[AAD-App-Manifest]:reference-azure-ad-app-manifest.md
[AAD-App-SP-Objects]:app-objects-and-service-principals.md
[AAD-Auth-Scenarios]:authentication-scenarios.md
[AAD-Integrating-Apps]:quickstart-v1-integrate-apps-with-azure-ad.md
[AAD-Dev-Guide]:azure-ad-developers-guide.md
[AAD-How-To-Integrate]: ./active-directory-how-to-integrate.md
[AAD-Security-Token-Claims]: ./active-directory-authentication-scenarios/#claims-in-azure-ad-security-tokens
[AAD-Tokens-Claims]:access-tokens.md
[AAD-V2-Dev-Guide]: v2-overview.md
[AZURE-portal]: https://portal.azure.com
[Duyshant-Role-Blog]: http://www.dushyantgill.com/blog/2014/12/10/roles-based-access-control-in-cloud-applications-using-azure-ad/
[JWT]: https://tools.ietf.org/html/draft-ietf-oauth-json-web-token-32
[O365-Perm-Ref]: /graph/permissions-reference
[OAuth2-Access-Token-Scopes]: https://tools.ietf.org/html/rfc6749#section-3.3
[OAuth2-AuthZ-Grant-Types]: https://tools.ietf.org/html/rfc6749#section-1.3
[OAuth2-Client-Types]: https://tools.ietf.org/html/rfc6749#section-2.1
[OAuth2-Role-Def]: https://tools.ietf.org/html/rfc6749#page-6
[OpenIDConnect]: https://openid.net/specs/openid-connect-core-1_0.html
[OpenIDConnect-ID-Token]: https://openid.net/specs/openid-connect-core-1_0.html#IDToken