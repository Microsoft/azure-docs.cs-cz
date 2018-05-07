---
title: Jak sestavit aplikaci, která se můžete přihlásit žádné uživatele Azure AD
description: Ukazuje, jak sestavit aplikaci více klientů, které se můžete zaregistrovat v uživatel jakéhokoli klienta Azure Active Directory.
services: active-directory
documentationcenter: ''
author: celestedg
manager: mtillman
editor: ''
ms.assetid: 35af95cb-ced3-46ad-b01d-5d2f6fd064a3
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/27/2018
ms.author: celested
ms.custom: aaddev
ms.openlocfilehash: f31ef7285e07467fe233d5e10534340bc912ed1c
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/04/2018
---
# <a name="how-to-sign-in-any-azure-active-directory-user-using-the-multi-tenant-application-pattern"></a>Jak se přihlásit žádné uživatele Azure Active Directory pomocí vzoru víceklientské aplikace
Pokud nabízíte softwaru jako aplikace služby k mnoha organizacích, můžete nakonfigurovat aplikaci tak, aby přijímal přihlášení jakéhokoli klienta Azure Active Directory (AD). Tato konfigurace se nazývá provedení víceklientské vaší aplikace. Uživatelé v jakékoli klientovi Azure AD bude moci přihlásit k aplikaci po souhlas používat svůj účet s vaší aplikací.  

Pokud máte existující aplikaci, která má svůj vlastní účet systému nebo jiných druhů přihlášení z jiných poskytovatelů cloudu, podporuje, klepněte přidání Azure AD přihlášení jakéhokoli klienta je jednoduché. Právě svou aplikaci zaregistrovat, přidejte kód přihlášení přes OAuth2, OpenID Connect nebo SAML a umístí [tlačítko "Sign In with Microsoft"] [ AAD-App-Branding] ve vaší aplikaci.

> [!NOTE] 
> Tento článek předpokládá, že jste již obeznámeni s vytvoření jednoho klienta aplikace pro Azure AD. Pokud si nejste, měli byste začít s jedním z – elementy QuickStart na [domovské stránce průvodce vývojáře][AAD-Dev-Guide].

Existují čtyři jednoduché kroky k převedení aplikace do více klientů aplikace Azure AD:

1. [Aktualizace registrace vaší aplikace na více klientů](#update-registration-to-be-multi-tenant)
2. [Aktualizujte kód a odesílat požadavky / Common koncový bod](#update-your-code-to-send-requests-to-common)
3. [Aktualizujte kód pro zpracování více hodnot vystavitele](#update-your-code-to-handle-multiple-issuer-values)
4. [Rady pro pochopení souhlasu uživatele a správce a proveďte změny správný kód](#understanding-user-and-admin-consent)

Podívejme se na každý krok podrobně. Můžete také přejít přímo na [tento seznam víceklientské ukázky][AAD-Samples-MT].

## <a name="update-registration-to-be-multi-tenant"></a>Aktualizace registrace, která má být více klientů
Ve výchozím nastavení jsou webové aplikaci nebo API registrace ve službě Azure AD jednoho klienta.  Můžete provést registrace víceklientské tak, že najdete **více nevyužívá dělené tabulky** přepínač na **vlastnosti** registrace vaší aplikace v podokně [portál Azure] [ AZURE-portal] a jeho nastavení na hodnotu **Ano**.

Předtím, než aplikace můžete provedeny víceklientské, Azure AD vyžaduje identifikátor ID URI aplikace aplikace být globálně jedinečný. Identifikátor ID URI aplikace je jedním ze způsobů, které aplikace je definována ve zprávách protokolu. Aplikace pomocí jednoho klienta je dostačující pro identifikátor ID URI aplikace být jedinečný v rámci tohoto klienta. Pro více klientů aplikace musí být globálně jedinečné, Azure AD můžete najít aplikaci přes všechny klienty. Globální jedinečnosti se vynucuje tím, že identifikátor ID URI aplikace tak, aby měl název hostitele, který odpovídá ověřené domény klienta Azure AD. Ve výchozím nastavení globálně jedinečný identifikátor ID URI aplikace nastavte na vytváření aplikace mít aplikace, které jsou vytvořené prostřednictvím portálu Azure, ale tuto hodnotu můžete změnit.

Například, pokud název vašeho klienta se contoso.onmicrosoft.com pak platný identifikátor ID URI aplikace by být `https://contoso.onmicrosoft.com/myapp`.  Pokud váš klient měli ověřené domény `contoso.com`, pak by také být platný identifikátor ID URI aplikace `https://contoso.com/myapp`. Pokud je identifikátor ID URI aplikace není postupujte podle tohoto vzoru, nastavení aplikace, protože víceklientské selže.

> [!NOTE] 
> Registrace nativního klienta a také [v2 aplikace](./active-directory-appmodel-v2-overview.md) jsou víceklientské ve výchozím nastavení.  Není nutné provádět žádnou akci, aby tyto aplikace registrace více klientů.

## <a name="update-your-code-to-send-requests-to-common"></a>Aktualizujte kód a odesílat žádosti do/Common
Žádostí o přihlášení k aplikaci jednoho klienta, se odešlou do klienta přihlášení koncový bod. Například pro contoso.onmicrosoft.com bude koncový bod: `https://login.microsoftonline.com/contoso.onmicrosoft.com`

Požadavky odeslané na koncový bod klienta můžete přihlásit uživatele (nebo Hosté) v něm k aplikacím v něm. S více klienty aplikací aplikace neví, předem jaké klienta uživatele je, aby nemohli odesílat žádosti do koncového bodu klienta.  Místo toho jsou odesílány požadavky na koncový bod, který multiplexes napříč všechny klienty Azure AD: `https://login.microsoftonline.com/common`

Pokud Azure AD přijme požadavek na / Common koncový bod, se uživatel přihlásí a, v důsledku toho zjišťuje klienta, které je uživatel z. / Společný koncový bod funguje se všemi ověřovací protokoly podporovaný službou Azure AD: OpenID Connect, OAuth 2.0, SAML 2.0 a WS-Federation.

Odpověď přihlášení do aplikace se pak obsahuje token reprezentující uživatele. Hodnota vystavitele v tokenu informuje co klienta uživatele je z aplikace. Pokud vrátí odpověď z / Common koncový bod, odpovídá hodnotě vystavitele v tokenu uživatele klienta. 

> [!IMPORTANT]
> / Společný koncový bod není klienta a není vystavitele, je právě multiplexor. Při použití/Common, logiku aplikace k ověření tokeny musí aktualizovat, aby vzít v úvahu. 

## <a name="update-your-code-to-handle-multiple-issuer-values"></a>Aktualizujte kód pro zpracování více hodnot vystavitele
Webové aplikace a webové rozhraní API přijímat a ověřovat tokeny z Azure AD.  

> [!NOTE]
> Nativní klientské aplikace požadovat a přijímat tokeny z Azure AD, se to provést tak o jejich odeslání do rozhraní API, kde se ověří.  Nativní aplikace neověřují tokeny a musí je považovat za neprůhledné.

Podívejme se na tom, jak aplikaci ověří tokeny obdrží z Azure AD.  Jednoho klienta aplikace obvykle přebírá hodnotu koncového bodu jako:

    https://login.microsoftonline.com/contoso.onmicrosoft.com

a používá ho vytvořit adresu URL metadat (v tomto případě OpenID Connect) jako:

    https://login.microsoftonline.com/contoso.onmicrosoft.com/.well-known/openid-configuration

ke stažení dvě zásadní informace používané k ověření tokeny: klient je podepisování klíče a hodnoty vystavitele. Každý klient Azure AD má jedinečný vystavitele hodnotu ve formátu:

    https://sts.windows.net/31537af4-6d77-4bb9-a681-d2394888ea26/

kde je hodnota GUID přejmenování bezpečná verze klienta ID klienta. Pokud jste vybrali na předchozí odkaz metadata `contoso.onmicrosoft.com`, zobrazí se tato hodnota vystavitele v dokumentu.

Pokud aplikace jednoho klienta ověří token, ověří podpis tokenu proti podpisové klíče z dokumentu metadat. Tento test umožňuje Ujistěte se, že hodnota vystavitele v tokenu odpovídá ten, který byl nalezen v dokumentu metadat.

Protože / běžný koncový bod neodpovídá klienta a není vystavitele, při zkontrolujte hodnotu vystavitele metadat pro/běžné má podle šablony URL namísto skutečné hodnoty:

    https://sts.windows.net/{tenantid}/

Proto nelze ověřit tokeny pouhým odpovídající hodnotě vystavitele v metadatech s víceklientské aplikace `issuer` hodnotu v tokenu. Víceklientské aplikace potřebuje logiku rozhodnout, které vystavitele hodnoty jsou platné a které nejsou založené na část hodnoty vystavitele s ID klienta.  

Například víceklientské aplikace umožňuje pouze přihlášení z konkrétní klientů, kteří zaregistrovali pro své služby, potom je nutné zkontrolovat hodnotu vystavitele nebo `tid` hodnoty v tokenu a ujistěte se, že klienta je v jejich seznamu odběratelů deklarace identity. Pokud aplikace víceklientské pouze se zabývá jednotlivce a není rozhodnutí žádné přístupu na základě na klienty, potom ji můžete ignorovat hodnotě vystavitele zcela.

V [víceklientské ukázky][AAD-Samples-MT], vystavitele ověření je zakázané povolit všechny klienta Azure AD pro přihlášení.

## <a name="understanding-user-and-admin-consent"></a>Principy uživatelů a správce souhlasu
Pro uživatele k přihlášení k aplikaci ve službě Azure AD musí být v klientovi uživatele reprezentován aplikace. To umožňuje organizace například použít jedinečné zásady po přihlášení uživatelé z jejich klienta pro aplikaci. Pro jednoho klienta aplikace je tato registrace jednoduché. je ten, který se stane, když zaregistrujete aplikaci v [portál Azure][AZURE-portal].

Aplikace pomocí víceklientské žije počáteční registrace pro aplikaci v klientovi Azure AD, používá vývojář. Když uživatel z jiné klienta přihlásí k aplikaci poprvé, vyzve Azure AD je k souhlas oprávnění požadované aplikací. Pokud budou souhlasit, pak volat znázornění aplikace *instanční objekt* je vytvořen v klientovi uživatele a přihlášení může pokračovat. Delegování je vytvořen také v adresáři, který zaznamenává souhlasu uživatele k aplikaci. Podrobnosti o aplikace aplikace a ServicePrincipal objekty, a jak se vztahují k sobě navzájem, najdete v části [objekty aplikací a hlavní objekty služeb][AAD-App-SP-Objects].

![Souhlas Jednoúrovňové aplikace][Consent-Single-Tier] 

Toto prostředí souhlasu je ovlivňován oprávnění požadované aplikací. Azure AD podporuje dva druhy oprávnění jenom aplikace a delegovaným.

* Delegovaná oprávnění uděluje aplikace, které může provádět možnost tak, aby fungoval jako přihlášeného uživatele pro podmnožinu akcí uživatele. Například můžete udělit aplikaci delegovaná oprávnění ke čtení kalendáře přihlášeného uživatele.
* Jen aplikace je povoleno přímo na identitu aplikace. Například můžete udělit aplikaci jen aplikace oprávnění ke čtení seznamu uživatelů v klientovi, bez ohledu na to, kdo je přihlášený do aplikace.

Některá oprávnění může být souhlas regulární uživatelem, zatímco jiné vyžadují souhlas správce klienta. 

### <a name="admin-consent"></a>Souhlas správce
Oprávnění jen aplikace vždy vyžadovat souhlas správce klienta. Pokud vaše aplikace požaduje oprávnění jen aplikace a uživatel se pokusí přihlásit k aplikaci, se zobrazí chybová zpráva, že uživatel není možné vyjádřit souhlas.

Některé přidělená oprávnění také vyžadovat souhlas správce klienta. Například možnost ke zpětnému zápisu do Azure AD jako přihlášeného uživatele vyžaduje souhlas správce klienta. Jako oprávnění jen pro aplikace Pokud se pokusí přihlásit k aplikaci, která požaduje delegované oprávnění, která vyžaduje souhlas správce běžného uživatele vaší aplikace obdrží chybu. Jestli vyžaduje oprávnění správce souhlasu je dáno vývojáře, které publikují prostředek a naleznete v dokumentaci pro prostředek. V dokumentaci oprávnění [Azure AD Graph API] [ AAD-Graph-Perm-Scopes] a [Microsoft Graph API] [ MSFT-Graph-permision-scopes] znamenat oprávnění, která vyžadují, správce souhlas.

Pokud vaše aplikace používá oprávnění, která vyžadovat souhlas správce, musíte mít gesto například tlačítko nebo odkaz, kde může správce zahájit akci. Vaše aplikace odešle pro tuto akci je obvykle OAuth2/OpenID Connect autorizace požadavek, který také obsahuje požadavek `prompt=admin_consent` parametr řetězce dotazu. Jakmile se dá souhlas správce a instanční objekt se vytvoří v klientovi zákazníka, následných žádostí o přihlášení není nutné `prompt=admin_consent` parametr. Vzhledem k tomu, že správce určil, že požadovaná oprávnění jsou přijatelné, požádejte ho o souhlas od tohoto okamžiku žádné jiných uživatelů v klientovi.

Správce klienta můžete zakázat možnost pro regulární uživatele o souhlas pro aplikace. Pokud tato možnost je vypnuta, je požadován pro aplikace, který se má použít v klientovi vždycky souhlas správce. Pokud chcete k testování aplikace s souhlasu koncového uživatele zakázaná, můžete najít konfigurace přepínač na [portál Azure] [ AZURE-portal] v **[uživatelská nastavení](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/UserSettings/menuId/)** oddílu pod **podnikové aplikace, které**.

`prompt=admin_consent` Parametru lze také aplikace, které žádostí o oprávnění, které nevyžadují souhlas správce. Příklad, kdy to by použila je, pokud aplikace vyžaduje prostředí, kde správce tenanta "zaregistruje" jeden čas a žádné jiné uživatele požádejte ho o souhlas od tohoto okamžiku.

Pokud aplikace vyžaduje souhlas správce a správce přihlášení bez `prompt=admin_consent` parametr odesílány, pokud správce úspěšně souhlasí k aplikaci, bude se vztahovat **pouze ke svému uživatelskému účtu**. Stále běžní uživatelé nebudou moci přihlásit nebo souhlas k aplikaci. Tato funkce je užitečná, pokud chcete poskytnout možnost prozkoumat aplikace před povolením přístupu jiných uživatelů správce klienta.

> [!NOTE]
> Některé aplikace mají prostředí, kde jsou běžní uživatelé moct souhlas původně a později aplikace může zahrnovat oprávnění správce a požadavku, která vyžadovat souhlas správce. Neexistuje žádný způsob, jak to provést pomocí registrace v1 aplikace ve službě Azure AD dnes; ale používá koncový bod v2 umožňuje aplikacím a požádat o oprávnění za běhu místo během registrace, který umožňuje tento scénář. Další informace najdete v tématu [koncový bod v2][AAD-V2-Dev-Guide].

### <a name="consent-and-multi-tier-applications"></a>Souhlasu a vícevrstvé aplikace
Aplikace může mít několik vrstev, každý reprezentován vlastní registraci ve službě Azure AD. Nativní aplikace, která volá webové rozhraní API nebo webovou aplikaci, například volání webového rozhraní API. V obou případech klienta (nativní aplikaci nebo webové aplikace) vyžaduje oprávnění k volání prostředků (webového rozhraní API). Klient se úspěšně dá souhlas, do klienta zákazníka všechny prostředky, ke kterým se vyžaduje oprávnění již musí existovat v klientovi zákazníka. Pokud není tato podmínka splněná, Azure AD vrátí chybu prostředek musí být nejprve přidán.

**Několik vrstev v jednoho klienta**

Může to být problém, pokud vaše logické aplikace se skládá ze dvou nebo více aplikací registrace, například klienta a prostředků. Jak můžete získat prostředek do klienta zákazníka první? Azure AD se popisuje tento případ povolením klienta a prostředku se dá souhlas v jediném kroku. Uživateli se zobrazí celkový součet oprávnění požadovaná klientem a prostředků na stránce souhlasu. Chcete-li toto chování, registrace aplikace prostředku musí obsahovat ID klienta aplikace jako `knownClientApplications` v jeho [manifest aplikace][AAD-App-Manifest]. Příklad:

    knownClientApplications": ["94da0930-763f-45c7-8d26-04d5938baab2"]

Tento postup je znázorněn v vícevrstvé nativního klienta volání ukázkové webové rozhraní API v [související obsah](#related-content) na konci tohoto článku. Následující obrázek poskytuje přehled o souhlasu pro vícevrstvé aplikace s zaregistrovaný v jednoho klienta.

![Souhlas známé klienta vícevrstvé aplikace][Consent-Multi-Tier-Known-Client] 

**Několik vrstev v několika klientech**

Podobný případ se stane, když jsou v jiných klientů zaregistrované různých vrstev aplikace. Představte si třeba v případě vytváření nativní klientskou aplikaci, která volá Online rozhraní API sady Office 365 Exchange. K vývoji nativní aplikaci a novějším pro nativní aplikace spuštěna v klientovi zákazníka, musí být objekt služby Exchange Online. V takovém případě se developer a zákazník musí zakoupit Exchange Online pro službu objektu vytvořeny v svým klientům.  

V případě rozhraní API vytvořené v organizaci než Microsoft musí vývojář rozhraní API poskytují způsob, jak zákazníkům o souhlas aplikaci do klientů svým zákazníkům. Doporučený návrh je pro vývojáře třetích stran k vytvoření rozhraní API, tak, aby také může fungovat jako webový klient k implementaci registrace. Použijte následující postup:

1. Postupujte podle předchozích částech k zajištění, že požadavky na registraci nebo kód víceklientské aplikace implementuje rozhraní API.
2. Kromě vystavení rozhraní API obory nebo role, ujistěte se registrace zahrnuje "přihlášení a čtení profilu uživatele" oprávnění Azure AD (poskytuje se ve výchozím nastavení).
3. Implementace sign v nebo registrace-množství stránky v webovému klientovi, následující [souhlas správce](#admin-consent) pokyny uvedenými výše.
4. Jakmile uživatel souhlasí k aplikaci, vytvoří se odkazy service principal a souhlasu delegování v jejich klienta a nativní aplikace mohou získat tokeny pro rozhraní API.

Následující obrázek poskytuje přehled o souhlasu pro vícevrstvé aplikace s zaregistrovaný v jiných klientů.

![Souhlas vícevrstvé aplikace s více stran][Consent-Multi-Tier-Multi-Party] 

### <a name="revoking-consent"></a>Odvolání souhlasu
Uživatelé a správci můžete odvolat souhlas k vaší aplikaci kdykoli:

* Uživatelé odvolat přístup k jednotlivých aplikací odstraněním těchto z jejich [přístup k aplikacím panely] [ AAD-Access-Panel] seznamu.
* Správci odvolat přístup k aplikacím je odstranit z Azure AD pomocí [podnikové aplikace, které](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps) části [portál Azure][AZURE-portal].

Pokud správce souhlasí k aplikaci pro všechny uživatele v klientovi, uživatelé nelze odvolat přístup jednotlivě. Pouze správce můžete odvolat přístup a jenom pro celou aplikaci.

## <a name="multi-tenant-applications-and-caching-access-tokens"></a>Víceklientské aplikace a ukládání do mezipaměti přístupové tokeny
Víceklientským aplikacím můžete také získat přístupové tokeny k volání rozhraní API, které jsou chráněné službou Azure AD. Běžnou chybou při použití s aplikací víceklientské Active Directory Authentication Library (ADAL) je původně žádosti o token pro uživatele s využitím/Common, obdrží odpověď a požádat o další token pro tomuto uživateli také pomocí/Common. Protože odpověď z Azure AD pochází z klienta, není nebo běžné, ADAL ukládá do mezipaměti token, že je z klienta. Další volání/Common získat přístupový token pro uživatele neúspěšných přístupů do položky mezipaměti a uživatel je vyzván se znovu přihlásit. Abyste se vyhnuli chybí mezipaměti, zkontrolujte, zda koncový bod klienta jsou provedeny následující volání pro již přihlášeného uživatele.

## <a name="next-steps"></a>Další postup
V tomto článku jste zjistili, jak sestavit aplikaci, která se může přihlásit uživatel jakéhokoli klienta Azure AD. Po povolení jednotné přihlašování (SSO) mezi aplikací a Azure AD, můžete také aktualizovat aplikaci pro přístup k rozhraní API vystavené zdrojů společnosti Microsoft, jako je Office 365. Díky tomu můžete nabídnout individuální možnosti ve vaší aplikaci, například zobrazení kontextové informace pro uživatele, jako je jejich profilový obrázek nebo jejich další událost kalendáře. Další informace o tom, jak rozhraní API volání do služby Azure AD a Office 365 služeb jako Exchange, SharePoint, OneDrive, OneNote, Planner, Excel a další, navštivte [Microsoft Graph API][MSFT-Graph-overview].

## <a name="related-content"></a>Související obsah
* [Ukázky víceklientské aplikace][AAD-Samples-MT]
* [Branding pokyny pro aplikace][AAD-App-Branding]
* [Objekty aplikací a hlavní objekty služby][AAD-App-SP-Objects]
* [Integrace aplikací s Azure Active Directory][AAD-Integrating-Apps]
* [Přehled rozhraní souhlasu][AAD-Consent-Overview]
* [Obory oprávnění rozhraní Microsoft Graph API][MSFT-Graph-permision-scopes]
* [Obory oprávnění Azure AD Graph API][AAD-Graph-Perm-Scopes]

<!--Reference style links IN USE -->
[AAD-Access-Panel]:  https://myapps.microsoft.com
[AAD-App-Branding]: ./active-directory-branding-guidelines.md
[AAD-App-Manifest]: ./active-directory-application-manifest.md
[AAD-App-SP-Objects]: ./active-directory-application-objects.md
[AAD-Auth-Scenarios]: ./active-directory-authentication-scenarios.md
[AAD-Consent-Overview]: ./active-directory-integrating-applications.md#overview-of-the-consent-framework
[AAD-Dev-Guide]: ./active-directory-developers-guide.md
[AAD-Graph-Overview]: https://azure.microsoft.com/documentation/articles/active-directory-graph-api/
[AAD-Graph-Perm-Scopes]: https://msdn.microsoft.com/library/azure/ad/graph/howto/azure-ad-graph-api-permission-scopes
[AAD-Integrating-Apps]: ./active-directory-integrating-applications.md
[AAD-Samples-MT]: https://azure.microsoft.com/documentation/samples/?service=active-directory&term=multitenant
[AAD-Why-To-Integrate]: ./active-directory-how-to-integrate.md
[AZURE-portal]: https://portal.azure.com
[MSFT-Graph-overview]: https://graph.microsoft.io/en-us/docs/overview/overview
[MSFT-Graph-permision-scopes]: https://developer.microsoft.com/en-us/graph/docs/concepts/permissions_reference

<!--Image references-->
[AAD-Sign-In]: ./media/active-directory-devhowto-multi-tenant-overview/sign-in-with-microsoft-light.png
[Consent-Single-Tier]: ./media/active-directory-devhowto-multi-tenant-overview/consent-flow-single-tier.png
[Consent-Multi-Tier-Known-Client]: ./media/active-directory-devhowto-multi-tenant-overview/consent-flow-multi-tier-known-clients.png
[Consent-Multi-Tier-Multi-Party]: ./media/active-directory-devhowto-multi-tenant-overview/consent-flow-multi-tier-multi-party.png

<!--Reference style links -->
[AAD-App-Manifest]: ./active-directory-application-manifest.md
[AAD-App-SP-Objects]: ./active-directory-application-objects.md
[AAD-Auth-Scenarios]: ./active-directory-authentication-scenarios.md
[AAD-Integrating-Apps]: ./active-directory-integrating-applications.md
[AAD-Dev-Guide]: ./active-directory-developers-guide.md
[AAD-Graph-Perm-Scopes]: https://msdn.microsoft.com/library/azure/ad/graph/howto/azure-ad-graph-api-permission-scopes
[AAD-Graph-App-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#application-entity
[AAD-Graph-Sp-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#serviceprincipal-entity
[AAD-Graph-User-Entity]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#user-entity
[AAD-How-To-Integrate]: ./active-directory-how-to-integrate.md
[AAD-Security-Token-Claims]: ./active-directory-authentication-scenarios/#claims-in-azure-ad-security-tokens
[AAD-Tokens-Claims]: ./active-directory-token-and-claims.md
[AAD-V2-Dev-Guide]: ../active-directory-appmodel-v2-overview.md
[AZURE-portal]: https://portal.azure.com
[Duyshant-Role-Blog]: http://www.dushyantgill.com/blog/2014/12/10/roles-based-access-control-in-cloud-applications-using-azure-ad/
[JWT]: https://tools.ietf.org/html/draft-ietf-oauth-json-web-token-32
[O365-Perm-Ref]: https://msdn.microsoft.com/office/office365/howto/application-manifest
[OAuth2-Access-Token-Scopes]: https://tools.ietf.org/html/rfc6749#section-3.3
[OAuth2-AuthZ-Code-Grant-Flow]: https://msdn.microsoft.com/library/azure/dn645542.aspx
[OAuth2-AuthZ-Grant-Types]: https://tools.ietf.org/html/rfc6749#section-1.3 
[OAuth2-Client-Types]: https://tools.ietf.org/html/rfc6749#section-2.1
[OAuth2-Role-Def]: https://tools.ietf.org/html/rfc6749#page-6
[OpenIDConnect]: http://openid.net/specs/openid-connect-core-1_0.html
[OpenIDConnect-ID-Token]: http://openid.net/specs/openid-connect-core-1_0.html#IDToken














