---
title: Jak vytvořit aplikaci, která může přihlášení jakéhokoli uživatele Azure AD
description: Ukazuje, jak vytvářet aplikace s více tenanty, které se můžete přihlásit uživatele z žádného tenanta Azure Active Directory.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 35af95cb-ced3-46ad-b01d-5d2f6fd064a3
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/24/2018
ms.author: celested
ms.reviewer: justhu, elisol
ms.custom: aaddev
ms.openlocfilehash: f9a1b303fb66e71ffb1834c5b5fb4796867a0944
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55819412"
---
# <a name="how-to-sign-in-any-azure-active-directory-user-using-the-multi-tenant-application-pattern"></a>Postup: Přihlášení jakéhokoli uživatele Azure Active Directory pomocí vzoru aplikace s více tenanty

Pokud nabízíte Software jako služba (SaaS) aplikací pro mnoho organizací, můžete nakonfigurovat aplikaci tak, aby přijímal přihlášení z žádného tenanta služby Azure Active Directory (Azure AD). Tato konfigurace se nazývá *provádění aplikace víceklientské*. Uživatelé v žádného tenanta Azure AD budou moct přihlásit do aplikace po vyjádření souhlasu s použít svůj účet s vaší aplikací. 

Pokud máte existující aplikaci, která má svůj vlastní účet systému nebo podporuje jiné druhy přihlášení z jiných poskytovatelů cloudových služeb, přidání služby Azure AD z žádného tenanta je jednoduché. Stačí svou aplikaci zaregistrovat, přidejte kód přihlášení přes OAuth2, OpenID Connect nebo SAML a vložit [tlačítko "Sign in with Microsoft"] [ AAD-App-Branding] ve vaší aplikaci.

> [!NOTE] 
> Tento článek předpokládá, že jste již obeznámeni s vytvářením aplikace pro jednoho tenanta pro službu Azure AD. Pokud si nejste, začněte s jedním z postupů rychlý start na [domovské stránce průvodce pro vývojáře][AAD-Dev-Guide].

Existují čtyři jednoduché kroky k převedení vaší aplikace do aplikace více tenantů Azure AD:

1. [Aktualizace registrace vaší aplikace bude více tenantů](#update-registration-to-be-multi-tenant)
2. [Aktualizujte svůj kód k odesílání požadavků / Common koncového bodu](#update-your-code-to-send-requests-to-common)
3. [Aktualizujte svůj kód pro zpracování více hodnot vystavitele](#update-your-code-to-handle-multiple-issuer-values)
4. Principy souhlasu uživatele a správce a proveďte odpovídající kód změny

Podívejme se na každý krok v podrobností. Můžete také přejít přímo na [tento seznam s více tenanty ukázky][AAD-Samples-MT].

## <a name="update-registration-to-be-multi-tenant"></a>Aktualizace registrace na více tenantů

Registrace webové aplikace nebo rozhraní API ve službě Azure AD jsou ve výchozím nastavení jednoho tenanta. Můžete provést registraci více tenantů hledáním **s bezproblémovým** zapnout **vlastnosti** podokně Registrace vaší aplikace v [webu Azure portal] [ AZURE-portal] a nastavíte ho na **Ano**.

Dřív, než aplikace může být více tenantů, vyžaduje Azure AD aplikace být globálně jedinečný identifikátor URI ID aplikace. Identifikátor URI ID aplikace je jedním ze způsobů, kterými se může aplikace ve zprávách protokolu identifikovat. U aplikace s jedním tenantem stačí, když bude identifikátor URI ID aplikace jedinečný v rámci daného tenanta. U aplikace s více tenanty musí být globálně jedinečný, aby služba Azure AD aplikaci našla mezi všemi tenanty. Globální jedinečnost se vynucuje požadavkem, aby Identifikátor URI ID aplikace obsahoval název hostitele, který odpovídá ověřené doméně tenanta Azure AD. 

Ve výchozím nastavení aplikace vytvořené prostřednictvím webu Azure portal mají globálně jedinečný identifikátor ID URI aplikace nastavte na vytváření aplikací, ale tuto hodnotu můžete změnit. Například pokud byl název tenanta contoso.onmicrosoft.com pak platný identifikátor URI ID aplikace by `https://contoso.onmicrosoft.com/myapp`. Pokud váš tenant měl ověřené domény `contoso.com`, pak by také platný identifikátor URI ID aplikace `https://contoso.com/myapp`. Pokud identifikátor URI ID aplikace nepoužívá tento vzor, nastavení aplikace jako aplikace s více tenanty se nezdaří.

> [!NOTE] 
> Registrace nativního klienta systému stejně jako [aplikace v2.0](./active-directory-appmodel-v2-overview.md) jsou ve výchozím nastavení s více tenanty. Není nutné provádět žádnou akci, aby tyto aplikace registrace více tenantů.

## <a name="update-your-code-to-send-requests-to-common"></a>Aktualizujte svůj kód k odesílání požadavků/Common

Aplikace pro jednoho tenanta žádostí o přihlášení se odešlou do tenanta přihlášení koncový bod. Například contoso.onmicrosoft.com koncový bod by být: `https://login.microsoftonline.com/contoso.onmicrosoft.com`. Požadavky odeslané na koncový bod tenanta můžou přihlásit uživatele (nebo hostů) v tomto tenantovi pro aplikace v tomto tenantovi. 

Pomocí aplikace s více tenanty aplikace nebude vědět, ještě před zahájením jaké tenanta je uživatel, aby nemohli odesílat žádosti do koncových bodů klienta. Místo toho jsou odesílány požadavky na koncový bod, který spojuje napříč všechny klienty Azure AD: `https://login.microsoftonline.com/common`

Když Azure AD obdrží požadavek na / Common koncového bodu, se uživatel přihlásí a, v důsledku toho zjišťuje kterého tenanta uživatel je z. / Společný koncový bod funguje se všemi ověřování protokolů podporovaných službou Azure AD:  OpenID Connect, OAuth 2.0, SAML 2.0 a WS-Federation.

Odpověď přihlášení k aplikaci pak obsahuje token představující uživatele. Hodnotu issuer v tokenu říká jaké tenanta je uživatel z aplikace. Když se vrátí odpověď z / Common koncový bod, hodnota vystavitele v tokenu odpovídá tenanta uživatele. 

> [!IMPORTANT]
> / Společný koncový bod není klienta a není vystavitele, je právě multiplexor. Při použití/Common, je potřeba aktualizovat tak, aby vzít v úvahu logiky v aplikaci pro ověření tokenů. 

## <a name="update-your-code-to-handle-multiple-issuer-values"></a>Aktualizujte svůj kód pro zpracování více hodnot vystavitele

Webové aplikace a webová rozhraní API přijímat a ověřovat tokeny ze služby Azure AD. 

> [!NOTE]
> I když nativní klientské aplikace požadovat a přijímat tokeny z Azure AD, dělají a odeslat je do rozhraní API, ve kterém se ověří tak. Nativní aplikace nelze ověřit tokeny a nutné je považovat neprůhledné.

Pojďme se podívat na způsob, jakým aplikace ověřuje tokeny přijme ze služby Azure AD. Aplikace pro jednoho tenanta obvykle trvá hodnotu koncového bodu, jako je:

    https://login.microsoftonline.com/contoso.onmicrosoft.com

a použije je k vytvoření adresu URL metadat (v tomto případě OpenID Connect) jako:

    https://login.microsoftonline.com/contoso.onmicrosoft.com/.well-known/openid-configuration

Chcete-li stáhnout dvě důležité údaje, které se používají pro ověření tokenů: tenant podepisování klíče a hodnotu issuer. Každý tenant Azure AD má jedinečného vystavitele hodnotu ve formátu:

    https://sts.windows.net/31537af4-6d77-4bb9-a681-d2394888ea26/

kde je hodnota identifikátoru GUID verze bezpečná pro přejmenování tenant ID tenanta. Pokud jste vybrali na předchozí odkaz metadat `contoso.onmicrosoft.com`, zobrazí se tato hodnota issuer v dokumentu.

Když aplikace jednoho tenanta ověří token, zkontroluje podpis tokenu proti podpisové klíče z dokumentu metadat. Tento test umožňuje zajistit, aby že hodnota issuer v token, který odpovídá ten, který byl nalezen v dokumentu metadat.

Vzhledem k tomu, / společný koncový bod klienta neodpovídá a není vystavitele, při kontrole hodnotu issuer v metadatech pro/common má místo skutečnou hodnotu bez vizuálního vzhledu adresu URL:

    https://sts.windows.net/{tenantid}/

Proto nelze ověřit tokeny právě to provede spárováním odpovídajících hodnotu issuer v metadatech se aplikaci s více tenanty `issuer` hodnotu v tokenu. Aplikace s více tenanty musí logiku pro rozhodování vystavitele hodnot, které jsou platné a která nejsou založena na část hodnotu issuer pro ID tenanta. 

Například pokud aplikace s více tenanty umožňuje pouze přihlášení od konkrétních klientů, kteří si zaregistrovali ke svým službám, se musí zkontrolujte hodnotu issuer nebo `tid` hodnoty v tokenu k zajištění tohoto tenanta do svého seznamu odběratelů deklarace identity. Pokud aplikaci s více tenanty pouze jednotlivce se zabývá a nevyužívá rozhodnutích přístup podle tenantů, pak ji můžete ignorovat hodnotu issuer úplně se vynechá.

V [samples víceklientských][AAD-Samples-MT], ověření vystavitele je zakázané umožňující žádného tenanta Azure AD pro přihlášení.

## <a name="understand-user-and-admin-consent"></a>Principy souhlasu uživatele a správce

Pro uživatele k přihlášení k aplikaci ve službě Azure AD musí být zastoupen aplikaci v tenantovi uživatele. To umožňuje organizaci třeba jedinečné zásady použít, když uživatelé z jejich tenanta přihlásí k aplikaci. Pro jednoho tenanta aplikaci tato registrace je jednoduché. To je ten, který se stane při registraci aplikace v [webu Azure portal][AZURE-portal].

Pro aplikaci s více tenanty počáteční registraci aplikace se nachází v tenantovi Azure AD použít vývojářem. Když do aplikace při prvním přihlášení uživatele z jiného tenanta, Azure AD je požádá o oprávnění požadovaná aplikací vyjádřit souhlas. Pokud se vyjádřit souhlas, pak reprezentace aplikace volá *instanční objekt služby* se vytvoří v tenantovi uživatele a přihlášení může pokračovat. Delegování se také vytvoří v adresáři, který zaznamenává souhlasu uživatele k aplikaci. Podrobnosti o vaší aplikace Application a ServicePrincipal objektů a jejich vzájemné vztahy mezi sebou, naleznete v tématu [objekty aplikací a instanční objekty][AAD-App-SP-Objects].

![Souhlas s jednovrstvou aplikaci][Consent-Single-Tier] 

Toto prostředí pro vyjádření souhlasu je ovlivněna oprávnění požadovaná aplikací. Azure AD podporuje dva typy jen pro aplikace a delegovaná oprávnění.

* Delegovaná oprávnění neuděluje aplikaci, kterou můžete provést možnost tak, aby fungoval jako přihlášený uživatel pro podmnožinu akcí uživatele. Můžete například udělit aplikaci delegovaná oprávnění ke čtení kalendáře přihlášeného uživatele.
* Přímo k identifikaci aplikace je povoleno na oprávnění jen pro aplikace. Aplikace například můžete udělit oprávnění jen pro aplikace ke čtení seznamu uživatelů v tenantovi, bez ohledu na to, kdo je přihlášený k aplikaci.

Některá oprávnění lze vyjádřit souhlas podle běžného uživatele, zatímco jiné vyžadují souhlas správce tenanta. 

### <a name="admin-consent"></a>Souhlas správce

Oprávnění jen pro aplikace vždy vyžadují souhlas správce tenanta. Pokud vaše aplikace požaduje na oprávnění jen pro aplikace a uživatel se pokusí přihlásit k aplikaci, zobrazí se chybová zpráva o tom, že uživatel není možné vyjádřit souhlas.

Také určité delegovaná oprávnění vyžadují souhlas správce tenanta. Například možnost ke zpětnému zápisu do služby Azure AD jako přihlášený uživatel vyžaduje souhlas správce tenanta. Jako oprávnění jen pro aplikace Pokud jako běžný uživatel se pokusí přihlásit k aplikaci, která požaduje delegovaná oprávnění, která vyžaduje souhlas správce, vaše aplikace obdrží chybu. Určuje, zda oprávnění vyžaduje souhlas správce je určená pro vývojáře, publikovat prostředek, který najdete v dokumentaci k prostředku. V dokumentaci oprávnění [Azure AD Graph API] [ AAD-Graph-Perm-Scopes] a [Microsoft Graph API] [ MSFT-Graph-permission-scopes] označení, vyžadují oprávnění správce vyjádření souhlasu.

Pokud vaše aplikace používá oprávnění, která vyžadují souhlas správce, musíte mít gesta jako tlačítko nebo odkaz, kde může správce zahájit akci. Žádosti o vaše aplikace odešle tato akce je obvykle autorizace požadavku OAuth2 nebo OpenID Connect, která zahrnuje i `prompt=admin_consent` parametr řetězce dotazu. Jakmile správce schválil a instanční objekt je vytvořen v tenantovi zákazníka, není nutné následných žádostí o přihlášení `prompt=admin_consent` parametru. Vzhledem k tomu, že správce rozhodl, že požadovaná oprávnění jsou přijatelné, žádní jiní uživatelé v tenantovi požádejte ho o souhlas od tohoto okamžiku.

Správce tenanta můžete zakázat možnost pro pravidelné uživatelům udělit souhlas s aplikací. Pokud tato možnost je zakázaná, je vždy vyžadována pro aplikace pro použití v tenantovi souhlas správce. Pokud chcete otestovat aplikaci s souhlas koncového uživatele s zakázaná, můžete najít konfigurační přepínač v [webu Azure portal] [ AZURE-portal] v **[uživatelská nastavení](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/UserSettings/menuId/)** části **podnikové aplikace**.

`prompt=admin_consent` Parametr je také možné aplikacemi, které požádat o oprávnění, které nevyžadují souhlas správce. Příkladem, kdy to bude používat je, pokud aplikace vyžaduje prostředí, kde správce tenanta "zaregistruje" jeden čas a žádné jiné uživatele požádejte ho o souhlas od této chvíle.

Pokud aplikace vyžaduje souhlas správce a správce přihlášení bez `prompt=admin_consent` parametru odeslaného, když správce úspěšně souhlasí pro aplikaci, bude se vztahovat **pouze ke svému uživatelskému účtu**. Běžní uživatelé nebudou moct dál přihlášení nebo aplikaci vyjádřit souhlas. Tato funkce je užitečná, pokud chcete poskytnout možnost Procházet aplikace před povolením přístupu dalších uživatelů správce klienta.

> [!NOTE]
> Některé aplikace mají prostředí, kde běžní uživatelé budou moct zpočátku souhlas, a později aplikace může zahrnovat oprávnění správce a požádejte, které vyžadují souhlas správce. Neexistuje žádný způsob, jak to udělat pomocí verze 1.0 registrace aplikace Azure AD v současné době; však koncovým bodem v2.0 pomocí umožňuje aplikacím a požádat o oprávnění za běhu místo v době registrace, která umožňuje tento scénář. Další informace najdete v tématu [koncového bodu v2.0][AAD-V2-Dev-Guide].

### <a name="consent-and-multi-tier-applications"></a>Vyjádření souhlasu a vícevrstvé aplikace

Vaše aplikace může mít několik vrstev, každou reprezentovanou vlastní registraci ve službě Azure AD. Například nativní aplikaci, která volá webové rozhraní API nebo webovou aplikaci, která volá webové rozhraní API. V obou těchto případech klient (nativní aplikace nebo webové aplikace) požaduje oprávnění pro volání prostředku (webové rozhraní API). Aby klient mohl být úspěšně odsouhlasený. do tenanta zákazníka všechny prostředky, u kterých požaduje oprávnění již musí existovat v tenantovi zákazníka. Pokud tato podmínka není splněna, Azure AD chybovou zprávu, že musíte prostředek nejprve přidat.

**Několik vrstev v jednom tenantovi**

To může být problém, pokud vaše logické aplikace se skládá ze dvou nebo více registrace aplikací, například klienta a prostředků. Jak můžete získat prostředek do tenanta zákazníka první? Azure AD zahrnuje tento případ tím, že klient a zdroj souhlas v jediném kroku. Uživateli se zobrazí celkový součet oprávnění požadovaná klientem a prostředků na stránka pro odsouhlasení podmínek. Pokud chcete povolit toto chování, registrace aplikace prostředku musí obsahovat ID klienta aplikace jako `knownClientApplications` v jeho [manifest aplikace][AAD-App-Manifest]. Příklad:

    knownClientApplications": ["94da0930-763f-45c7-8d26-04d5938baab2"]

To je patrné vícevrstvé nativního klienta volání v ukázkové webové rozhraní API [související obsah](#related-content) oddílu na konci tohoto článku. Následující diagram představuje přehled o souhlasu pro vícevrstvou aplikaci registrovány v jednom tenantovi.

![Souhlas s známé klienta vícevrstvé aplikace][Consent-Multi-Tier-Known-Client] 

**Několik vrstev v několika tenantech**

Podobně jako případ se stane, když různé vrstvy aplikace jsou zaregistrované v různých tenantech. Představte si třeba v případě vytváření nativní klientskou aplikaci, která volá Online rozhraní API pro Office 365 Exchange. Pro vývoj nativních aplikací a dále nativní aplikaci, aby běžela v tenantovi zákazníka, musí být instanční objekt Exchange Online. V takovém případě se pro vývojáře a zákazník musíte koupit Exchange Online pro služby, které byly vytvořeny v svým klientům. 

V případě rozhraní API vytvořené organizací, než je Microsoft musí vývojář rozhraní API umožňují zákazníkům souhlas aplikaci do klientů svým zákazníkům. Doporučený návrh je pro vývojáře třetích stran k vytvoření rozhraní API tak, že můžete také fungovat jako webový klient k provedení registrace. Použijte následující postup:

1. Postupujte podle předchozích částech k zajištění, že rozhraní API implementuje požadavky na registraci nebo kódu aplikace s více tenanty.
2. Kromě zpřístupnění rozhraní API obory nebo role, ujistěte se, že obsahuje registraci "přihlášení a čtení profilu uživatele" oprávnění Azure AD (zadáno ve výchozím nastavení).
3. Implementace sign v nebo odhlášením stránku ve webovém klientovi a postupujte podle [souhlas správce](#admin-consent) pokyny.
4. Jakmile uživatel vyjádří souhlas aplikace, služby vazby delegování instanční objekt a vyjádření souhlasu se vytvoří v rámci jejich tenanta a nativní aplikace mohou získat tokeny pro rozhraní API.

Následující diagram představuje přehled o souhlasu pro vícevrstvou aplikaci registrovány v různých tenantech.

![Souhlas s více stran vícevrstvé aplikace][Consent-Multi-Tier-Multi-Party] 

### <a name="revoking-consent"></a>Odvolání souhlasu

Uživatelé a správci můžou odvolání souhlasu pro vaši aplikaci v každém okamžiku:

* Uživatelé odvolat přístup k jednotlivým aplikacím tak, že odeberete z jejich [aplikace na přístupovém panelu] [ AAD-Access-Panel] seznamu.
* Správci odvolat přístup k aplikacím tak, že odeberete z Azure AD používat [podnikové aplikace](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps) část [webu Azure portal][AZURE-portal].

Pokud správce souhlasí pro aplikaci pro všechny uživatele v tenantovi, uživatelé nejde odvolat přístup jednotlivě. Pouze správce můžete odvolat přístup a pouze pro celou aplikaci.

## <a name="multi-tenant-applications-and-caching-access-tokens"></a>Aplikace s více tenanty a ukládání přístupových tokenů

Aplikace s více tenanty můžete také získat přístupové tokeny pro volání rozhraní API, které jsou chráněné službou Azure AD. Běžnou chybou při použití Active Directory Authentication Library (ADAL) pomocí aplikace s více tenanty je zpočátku požádat o token pro uživatele s využitím/Common, neobdrží odpověď a požádat o další token pro tomuto uživateli také pomocí/Common. Protože odpověď ze služby Azure AD pochází z klienta, nikoli/common, ADAL ukládá do mezipaměti token jako pocházející z tenanta. Položka mezipaměti výpadky následných volání/Common získat přístupový token pro uživatele a uživatel je vyzván k znovu se přihlaste. Pokud chcete vyhnout se chybějícím mezipaměti, zkontroluje, že koncový bod vašeho tenanta se provedly následných volání již přihlášeného uživatele.

## <a name="next-steps"></a>Další postup

V tomto článku jste zjistili, jak vytvořit aplikaci, která dokáže přihlásit uživatele z žádného tenanta Azure AD. Po povolení jednotné přihlašování (SSO) mezi vaší aplikací a službou Azure AD, můžete také aktualizovat aplikaci pro přístup k rozhraním API zpřístupněné nástrojem Microsoft prostředkům, jako je Office 365. Díky tomu můžete nabídnout individuální prostředí ve vaší aplikaci, jako je například zobrazující kontextové informace pro uživatele, jako je jejich profilový obrázek nebo jejich další událost z kalendáře. Další informace o tom, že rozhraní API volá do služby Azure AD a Office 365 služeb jako Exchange, SharePoint, OneDrive, OneNote a další, navštivte [Microsoft Graph API][MSFT-Graph-overview].

## <a name="related-content"></a>Související obsah

* [Ukázky aplikace s více tenanty][AAD-Samples-MT]
* [Pokyny pro aplikace pro Branding][AAD-App-Branding]
* [Objekty aplikací a instanční objekty][AAD-App-SP-Objects]
* [Integrace aplikací s Azure Active Directory][AAD-Integrating-Apps]
* [Přehled rozhraní pro udělování souhlasu][AAD-Consent-Overview]
* [Obory oprávnění rozhraní Microsoft Graph API][MSFT-Graph-permission-scopes]
* [Obory oprávnění Azure AD Graph API][AAD-Graph-Perm-Scopes]

<!--Reference style links IN USE -->
[AAD-Access-Panel]:  https://myapps.microsoft.com
[AAD-App-Branding]:howto-add-branding-in-azure-ad-apps.md
[AAD-App-Manifest]:reference-azure-ad-app-manifest.md
[AAD-App-SP-Objects]:app-objects-and-service-principals.md
[AAD-Auth-Scenarios]:authentication-scenarios.md
[AAD-Consent-Overview]:consent-framework.md
[AAD-Dev-Guide]:azure-ad-developers-guide.md
[AAD-Graph-Overview]: https://azure.microsoft.com/documentation/articles/active-directory-graph-api/
[AAD-Graph-Perm-Scopes]: https://msdn.microsoft.com/library/azure/ad/graph/howto/azure-ad-graph-api-permission-scopes
[AAD-Integrating-Apps]:quickstart-v1-integrate-apps-with-azure-ad.md
[AAD-Samples-MT]: https://azure.microsoft.com/documentation/samples/?service=active-directory&term=multitenant
[AAD-Why-To-Integrate]: ./active-directory-how-to-integrate.md
[AZURE-portal]: https://portal.azure.com
[MSFT-Graph-overview]: https://developer.microsoft.com/graph/docs/overview/overview
[MSFT-Graph-permission-scopes]: https://developer.microsoft.com/graph/docs/concepts/permissions_reference

<!--Image references-->
[AAD-Sign-In]: ./media/active-directory-devhowto-multi-tenant-overview/sign-in-with-microsoft-light.png
[Consent-Single-Tier]: ./media/active-directory-devhowto-multi-tenant-overview/consent-flow-single-tier.png
[Consent-Multi-Tier-Known-Client]: ./media/active-directory-devhowto-multi-tenant-overview/consent-flow-multi-tier-known-clients.png
[Consent-Multi-Tier-Multi-Party]: ./media/active-directory-devhowto-multi-tenant-overview/consent-flow-multi-tier-multi-party.png

<!--Reference style links -->
[AAD-App-Manifest]:reference-azure-ad-app-manifest.md
[AAD-App-SP-Objects]:app-objects-and-service-principals.md
[AAD-Auth-Scenarios]:authentication-scenarios.md
[AAD-Integrating-Apps]:quickstart-v1-integrate-apps-with-azure-ad.md
[AAD-Dev-Guide]:azure-ad-developers-guide.md
[AAD-Graph-Perm-Scopes]: https://msdn.microsoft.com/library/azure/ad/graph/howto/azure-ad-graph-api-permission-scopes
[AAD-Graph-App-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#application-entity
[AAD-Graph-Sp-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#serviceprincipal-entity
[AAD-Graph-User-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#user-entity
[AAD-How-To-Integrate]: ./active-directory-how-to-integrate.md
[AAD-Security-Token-Claims]: ./active-directory-authentication-scenarios/#claims-in-azure-ad-security-tokens
[AAD-Tokens-Claims]:access-tokens.md
[AAD-V2-Dev-Guide]: v2-overview.md
[AZURE-portal]: https://portal.azure.com
[Duyshant-Role-Blog]: http://www.dushyantgill.com/blog/2014/12/10/roles-based-access-control-in-cloud-applications-using-azure-ad/
[JWT]: https://tools.ietf.org/html/draft-ietf-oauth-json-web-token-32
[O365-Perm-Ref]: https://msdn.microsoft.com/office/office365/howto/application-manifest
[OAuth2-Access-Token-Scopes]: https://tools.ietf.org/html/rfc6749#section-3.3
[OAuth2-AuthZ-Code-Grant-Flow]: https://msdn.microsoft.com/library/azure/dn645542.aspx
[OAuth2-AuthZ-Grant-Types]: https://tools.ietf.org/html/rfc6749#section-1.3 
[OAuth2-Client-Types]: https://tools.ietf.org/html/rfc6749#section-2.1
[OAuth2-Role-Def]: https://tools.ietf.org/html/rfc6749#page-6
[OpenIDConnect]: https://openid.net/specs/openid-connect-core-1_0.html
[OpenIDConnect-ID-Token]: https://openid.net/specs/openid-connect-core-1_0.html#IDToken
