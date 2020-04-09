---
title: Vytváření aplikací, které přihlašují uživatele Azure AD
titleSuffix: Microsoft identity platform
description: Ukazuje, jak vytvořit víceklientské aplikace, které můžete přihlásit uživatele z libovolného klienta Služby Azure Active Directory.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 03/17/2020
ms.author: ryanwi
ms.reviewer: jmprieur, lenalepa, sureshja, kkrishna
ms.custom: aaddev
ms.openlocfilehash: f22ecb13284eaf6fb2a833791b5563351ca19147
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2020
ms.locfileid: "80884082"
---
# <a name="how-to-sign-in-any-azure-active-directory-user-using-the-multi-tenant-application-pattern"></a>Postup: Přihlášení libovolného uživatele služby Azure Active Directory pomocí vzoru víceklientské aplikace

Pokud mnoha organizacím nabízíte aplikaci Software as a Service (SaaS), můžete aplikaci nakonfigurovat tak, aby přijímala přihlášení z libovolného klienta Azure Active Directory (Azure AD). Tato konfigurace se nazývá *vytvoření aplikace s více klienty*. Uživatelé v libovolném tenantovi Azure AD se budou moct přihlásit k vaší aplikaci po souhlasu s použitím svého účtu s vaší aplikací.

Pokud máte existující aplikaci, která má svůj vlastní systém účtů nebo podporuje jiné druhy přihlášení od jiných poskytovatelů cloudu, přidání přihlášení azure ad z libovolného klienta je jednoduché. Stačí zaregistrovat aplikaci, přidat přihlašovací kód přes OAuth2, OpenID Connect nebo SAML a vložit do aplikace [tlačítko "Přihlásit se s Microsoftem".][AAD-App-Branding]

> [!NOTE]
> Tento článek předpokládá, že jste již obeznámeni s vytvářením aplikace jednoho klienta pro Azure AD. Pokud nejste, začněte s jedním z rychlých startů na [domovské stránce průvodce vývojářem][AAD-Dev-Guide].

Existují čtyři jednoduché kroky k převodu aplikace do víceklientské aplikace Azure AD:

1. [Aktualizace registrace aplikace jako víceklientská](#update-registration-to-be-multi-tenant)
2. [Aktualizace kódu pro odesílání požadavků do /common koncového bodu](#update-your-code-to-send-requests-to-common)
3. [Aktualizace kódu pro zpracování více hodnot vystavittele](#update-your-code-to-handle-multiple-issuer-values)
4. [Pochopení souhlasu uživatelů a správců a provádění příslušných změn kódu](#understand-user-and-admin-consent)

Podívejme se na každý krok podrobně. Můžete také přejít přímo na ukázku [Sestavení víceklientské webové aplikace SaaS, která volá Microsoft Graph pomocí Azure AD a OpenID Connect](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/master/2-WebApp-graph-user/2-3-Multi-Tenant/README.md).

## <a name="update-registration-to-be-multi-tenant"></a>Aktualizovat registraci na víceklientské

Ve výchozím nastavení jsou registrace webových aplikací nebo rozhraní API ve službě Azure AD jeden tenant. Registraci můžete nastavit pro více klientů tak, že najdete **přepínač Podporovaných typů účtů** v podokně **Ověřování** registrace aplikace na webu [Azure Portal][AZURE-portal] a nastavíte ji na účty **v libovolném organizačním adresáři**.

Před vytvořením aplikace s více klienty vyžaduje Azure AD, aby identifikátor URI ID aplikace byl globálně jedinečný. Identifikátor URI ID aplikace je jedním ze způsobů, kterými se může aplikace ve zprávách protokolu identifikovat. U aplikace s jedním tenantem stačí, když bude identifikátor URI ID aplikace jedinečný v rámci daného tenanta. U aplikace s více tenanty musí být globálně jedinečný, aby služba Azure AD aplikaci našla mezi všemi tenanty. Globální jedinečnost se vynucuje požadavkem, aby Identifikátor URI ID aplikace obsahoval název hostitele, který odpovídá ověřené doméně tenanta Azure AD.

Ve výchozím nastavení mají aplikace vytvořené prostřednictvím portálu Azure globálně jedinečné identifikátory URI aplikace nastavené při vytváření aplikací, ale tuto hodnotu můžete změnit. Pokud byl například název vašeho klienta contoso.onmicrosoft.com, bude platný `https://contoso.onmicrosoft.com/myapp`identifikátor URI ID aplikace . Pokud váš tenant měl `contoso.com`ověřenou doménu , pak by `https://contoso.com/myapp`byl platný identifikátor URI ID aplikace . Pokud identifikátor URI ID aplikace nepoužívá tento vzor, nastavení aplikace jako aplikace s více tenanty se nezdaří.

> [!NOTE]
> Nativní registrace klientů, stejně jako [aplikace platformy identit Microsoftu](./active-directory-appmodel-v2-overview.md) jsou ve výchozím nastavení víceklientské. Není nutné provádět žádné akce, aby se tyto registrace aplikace více klienta.

## <a name="update-your-code-to-send-requests-to-common"></a>Aktualizace kódu pro odesílání požadavků na /common

V aplikaci jednoho klienta jsou požadavky na přihlášení odeslány do koncového bodu přihlášení klienta. Například pro contoso.onmicrosoft.com koncový bod `https://login.microsoftonline.com/contoso.onmicrosoft.com`by byl: . Požadavky odeslané do koncového bodu klienta můžete přihlásit uživatele (nebo hosty) v tomto tenantovi k aplikacím v tomto tenantovi.

S víceklientské aplikace aplikace neví předem, co klient uživatel je z, takže nelze odesílat požadavky na koncového bodu klienta. Místo toho se požadavky odesílají do koncového bodu, který multiplexy napříč všemi klienty Azure AD:`https://login.microsoftonline.com/common`

Když platforma identit microsoftu obdrží požadavek na /common koncového bodu, přihlásí uživatele a v důsledku toho zjistí, z kterého klienta uživatel pochází. Koncový bod /common funguje se všemi ověřovacími protokoly podporovanými službou Azure AD: OpenID Connect, OAuth 2.0, SAML 2.0 a WS-Federation.

Odpověď přihlášení k aplikaci pak obsahuje token představující uživatele. Hodnota vystavittele v tokenu říká aplikaci, z jakého klienta uživatel pochází. Když se vrátí odpověď z /common koncového bodu, hodnota vystavittele v tokenu odpovídá klientovi uživatele.

> [!IMPORTANT]
> /common koncový bod není tenant a není vystavitel, je to jen multiplexer. Při použití /common, logiku ve vaší aplikaci k ověření tokeny je třeba aktualizovat, aby to vzít v úvahu.

## <a name="update-your-code-to-handle-multiple-issuer-values"></a>Aktualizace kódu pro zpracování více hodnot vystavittele

Webové aplikace a webová rozhraní API přijímají a ověřují tokeny z platformy microsoft identit.

> [!NOTE]
> Zatímco nativní klientské aplikace vyžadují a přijímají tokeny z platformy identit microsoftu, dělají to tak, aby je odeslaly do rozhraní API, kde jsou ověřeny. Nativní aplikace neověřují tokeny a musí s nimi zacházet jako s neprůhlednými.

Podívejme se na to, jak aplikace ověřuje tokeny, které obdrží z platformy identit microsoftu. Aplikace jednoho klienta obvykle přebírá hodnotu koncového bodu, jako je:

    https://login.microsoftonline.com/contoso.onmicrosoft.com

a používá ji k vytvoření adresy URL metadat (v tomto případě OpenID Connect) jako:

    https://login.microsoftonline.com/contoso.onmicrosoft.com/.well-known/openid-configuration

chcete-li stáhnout dvě důležité informace, které se používají k ověření tokenů: podpisové klíče klienta a hodnota vystavittele. Každý klient Azure AD má jedinečnou hodnotu vystavittele formuláře:

    https://sts.windows.net/31537af4-6d77-4bb9-a681-d2394888ea26/

kde hodnota GUID je přejmenování bezpečné verze ID klienta klienta. Pokud vyberete předchozí odkaz metadata pro `contoso.onmicrosoft.com`, zobrazí se tato hodnota vystavittele v dokumentu.

Když aplikace jednoho klienta ověří token, zkontroluje podpis tokenu proti podpisovým klíčům z dokumentu metadat. Tento test umožňuje ujistěte se, že hodnota vystavittele v tokenu odpovídá ten, který byl nalezen v dokumentu metadat.

Vzhledem k tomu, že /common koncový bod neodpovídá tenantovi a není vystavitelem, při kontrole hodnoty vystavittele v metadatech /common má šablonovou adresu URL namísto skutečné hodnoty:

    https://sts.windows.net/{tenantid}/

Proto víceklientské aplikace nelze ověřit tokeny pouze porovnáním hodnoty vystavittele v `issuer` metadatech s hodnotou v tokenu. Víceklientská aplikace potřebuje logiku k rozhodnutí, které hodnoty vystavitejsou platné a které nejsou založeny na části id klienta hodnoty vystavittele. 

Například pokud víceklientské aplikace umožňuje pouze přihlášení od konkrétních klientů, kteří se zaregistrovali k jejich služby, pak musí zkontrolovat hodnotu vystavitela nebo hodnotu `tid` deklarace v tokenu, aby se ujistil, že tenant je v jejich seznamu předplatitelů. Pokud víceklientská aplikace se zabývá pouze jednotlivci a neprovede žádná rozhodnutí o přístupu na základě klientů, pak může hodnotu vystavittele úplně ignorovat.

Ve [víceklientských ukázkách][AAD-Samples-MT]je ověřování vystavittele zakázáno, aby se mohl přihlásit libovolný klient Azure AD.

## <a name="understand-user-and-admin-consent"></a>Principy souhlasu uživatelů a správců

Aby se uživatel mohl přihlásit k aplikaci ve službě Azure AD, musí být aplikace reprezentována v tenantovi uživatele. To umožňuje organizaci dělat věci, jako je použití jedinečných zásad, když se uživatelé ze svého klienta přihlásí k aplikaci. Pro aplikaci jednoho klienta je tato registrace jednoduchá; je to ten, který se stane, když zaregistrujete aplikaci na [webu Azure Portal][AZURE-portal].

Pro víceklientské aplikace počáteční registrace pro aplikaci žije v tenantovi Azure AD používá vývojář. Když se uživatel z jiného klienta přihlásí k aplikaci poprvé, Azure AD ho požádá o souhlas s oprávněními požadovanými aplikací. Pokud souhlasí, pak je v tenantovi uživatele vytvořena reprezentace aplikace s názvem *Instanční objekt* a přihlášení může pokračovat. Delegování je také vytvořeno v adresáři, který zaznamenává souhlas uživatele s aplikací. Podrobnosti o application a ServicePrincipal objekty a jak se vztahují k sobě navzájem, naleznete v [tématu Application objekty a objekty instančního objektu][AAD-App-SP-Objects].

![Ilustruje souhlas s jednovrstvou aplikací.][Consent-Single-Tier]

Toto prostředí se souhlasem je ovlivněno oprávněními požadovanými aplikací. Platforma identit Microsoftu podporuje dva druhy oprávnění, pouze pro aplikace a delegované.

* Delegované oprávnění uděluje aplikaci možnost jednat jako přihlášený uživatel pro podmnožinu věcí, které může uživatel dělat. Můžete například udělit aplikaci delegované oprávnění ke čtení podepsaného kalendáře uživatele.
* Oprávnění pouze pro aplikaci je uděleno přímo identitě aplikace. Můžete například udělit aplikaci oprávnění pouze pro aplikaci ke čtení seznamu uživatelů v tenantovi, bez ohledu na to, kdo je přihlášen k aplikaci.

Některá oprávnění mohou být schválena běžným uživatelem, zatímco jiná vyžadují souhlas správce klienta. 

### <a name="admin-consent"></a>Souhlas správce

Oprávnění pouze pro aplikace vždy vyžadují souhlas správce klienta. Pokud vaše aplikace požaduje oprávnění pouze pro aplikaci a uživatel se pokusí přihlásit k aplikaci, zobrazí se chybová zpráva, že uživatel nemůže souhlasit.

Některá delegovaná oprávnění také vyžadují souhlas správce klienta. Například možnost odepsat zpět do Služby Azure AD jako přihlášený uživatel vyžaduje souhlas správce klienta. Stejně jako oprávnění pouze pro aplikace, pokud běžný uživatel pokusí přihlásit k aplikaci, která požaduje delegované oprávnění, které vyžaduje souhlas správce, aplikace obdrží chybu. To, zda oprávnění vyžaduje souhlas správce, určuje vývojář, který prostředek publikoval, a lze jej nalézt v dokumentaci k prostředku. Dokumentace k oprávnění pro [rozhraní Microsoft Graph API][MSFT-Graph-permission-scopes] označuje, která oprávnění vyžadují souhlas správce.

Pokud vaše aplikace používá oprávnění, která vyžadují souhlas správce, musíte mít gesto, jako je tlačítko nebo odkaz, kde správce může zahájit akci. Požadavek, který aplikace odešle pro tuto akci, je obvyklý požadavek na `prompt=admin_consent` autorizaci OAuth2/OpenID Connect, který obsahuje také parametr řetězce dotazu. Jakmile správce souhlasil a instanční objekt je vytvořen v tenantovi zákazníka, následné `prompt=admin_consent` požadavky na přihlášení nepotřebují parametr. Vzhledem k tomu, že správce rozhodl, že požadovaná oprávnění jsou přijatelná, žádní další uživatelé v tenantovi nejsou od tohoto okamžiku vyzváni k udělení souhlasu.

Správce klienta může zakázat možnost běžných uživatelů souhlasit s aplikacemi. Pokud je tato funkce zakázána, je vždy vyžadován souhlas správce, aby se aplikace používala v tenantovi. Pokud chcete aplikaci otestovat se zakázaným souhlasem koncového uživatele, najdete konfigurační přepínač na [webu Azure Portal][AZURE-portal] v části Nastavení **[uživatelů](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/UserSettings/menuId/)** v části **Podnikové aplikace**.

Parametr `prompt=admin_consent` lze také použít aplikace, které požadují oprávnění, které nevyžadují souhlas správce. Příkladem, kdy by to použít, je, pokud aplikace vyžaduje prostředí, kde správce klienta "zaregistruje" jednou a žádní jiní uživatelé jsou vyzváni k souhlasu od tohoto okamžiku.

Pokud aplikace vyžaduje souhlas správce a admin `prompt=admin_consent` se přihlásí bez odeslání parametru, když správce úspěšně souhlasí s aplikací, bude se vztahovat **pouze na jejich uživatelský účet**. Běžní uživatelé se stále nebudou moci přihlásit nebo k ní souhlasit. Tato funkce je užitečná, pokud chcete dát správci klienta možnost prozkoumat vaši aplikaci před povolením přístupu ostatním uživatelům.

> [!NOTE]
> Některé aplikace vyžadují prostředí, kde běžní uživatelé mohou zpočátku souhlasit a později aplikace může zahrnovat správce a požádat o oprávnění, která vyžadují souhlas správce. Neexistuje žádný způsob, jak to udělat s registrací aplikace v1.0 ve službě Azure AD dnes; však pomocí platformy identit y Microsoft (v2.0) koncový bod umožňuje aplikacím požadovat oprávnění za běhu namísto v době registrace, což umožňuje tento scénář. Další informace naleznete v [tématu Koncový bod platformy identit microsoftu][AAD-V2-Dev-Guide].

### <a name="consent-and-multi-tier-applications"></a>Souhlas a vícevrstvé aplikace

Vaše aplikace může mít více úrovní, z nichž každá je reprezentována vlastní registrací ve službě Azure AD. Například nativní aplikace, která volá webové rozhraní API, nebo webová aplikace, která volá webové rozhraní API. V obou těchto případech klient (nativní aplikace nebo webová aplikace) požaduje oprávnění k volání prostředku (webové rozhraní API). Aby byl klient úspěšně odsouhlasen do klienta zákazníka, musí již v tenantovi zákazníka existovat všechny prostředky, na které požaduje oprávnění. Pokud tato podmínka není splněna, Azure AD vrátí chybu, která musí být přidána jako první.

#### <a name="multiple-tiers-in-a-single-tenant"></a>Více úrovní v jednom tenantovi

To může být problém, pokud vaše logická aplikace se skládá ze dvou nebo více registrací aplikace, například samostatný klient a prostředek. Jak získat prostředek do klienta zákazníka jako první? Azure AD pokrývá tento případ tím, že umožňuje klienta a prostředek, které mají být schváleny v jednom kroku. Uživatel i suma oprávnění požadovaných klientem i prostředkem na stránce souhlasu vidí součet oprávnění požadovaných klientem i zdrojem. Chcete-li povolit toto chování, registrace aplikace prostředku musí obsahovat ID aplikace klienta jako v `knownClientApplications` [manifestu aplikace][AAD-App-Manifest]. Příklad:

    knownClientApplications": ["94da0930-763f-45c7-8d26-04d5938baab2"]

To je znázorněno ve vícevrstvé množiny volající webové rozhraní API ukázka v části [Související obsah](#related-content) na konci tohoto článku. Následující diagram obsahuje přehled souhlasu pro vícevrstvou aplikaci registrovanou v jednom tenantovi.

![Ilustruje souhlas s vícevrstvou známou klientskou aplikací.][Consent-Multi-Tier-Known-Client]

#### <a name="multiple-tiers-in-multiple-tenants"></a>Více vrstev ve více klientech

Podobný případ se stane, pokud různé vrstvy aplikace jsou registrovány v různých tenantů. Zvažte například případ vytvoření nativní klientské aplikace, která volá rozhraní API Office 365 Exchange Online. Chcete-li vyvinout nativní aplikace a později pro nativní aplikace spustit v tenantovi zákazníka, musí být instanční objekt služby Exchange Online k dispozici. V takovém případě musí vývojář a zákazník zakoupit Exchange Online, aby byl instanční objekt vytvořen v jejich tenantech.

Pokud se jedná o rozhraní API vytvořené jinou organizací než microsoftem, vývojář rozhraní API musí svým zákazníkům poskytnout způsob, jak udělit souhlas s aplikací do tenantů svých zákazníků. Doporučený návrh je pro vývojáře třetí strany k vytvoření rozhraní API tak, aby může také fungovat jako webový klient k implementaci registrace. Použijte následující postup:

1. Postupujte podle předchozích částí a ujistěte se, že rozhraní API implementuje požadavky na registraci nebo kód víceklientské aplikace.
2. Kromě vystavení oborů nebo rolí rozhraní API se ujistěte, že registrace obsahuje oprávnění "Přihlásit se a číst profil uživatele" (poskytované ve výchozím nastavení).
3. Implementujte stránku přihlášení a registrace ve webovém klientovi a postupujte podle pokynů pro [souhlas správce.](#admin-consent)
4. Jakmile uživatel souhlasí s aplikací, instanční objekt a propojení delegování souhlasu jsou vytvořeny v jejich tenanta a nativní aplikace můžete získat tokeny pro rozhraní API.

Následující diagram obsahuje přehled souhlasu pro vícevrstvou aplikaci registrovanou v různých tenantech.

![Ilustruje souhlas s vícevrstvou aplikací pro více stran.][Consent-Multi-Tier-Multi-Party]

### <a name="revoking-consent"></a>Odvolání souhlasu

Uživatelé a správci mohou souhlas s vaší aplikací kdykoli odvolat:

* Uživatelé odvolávají přístup k jednotlivým aplikacím jejich odebráním ze seznamu [aplikací přístupového panelu.][AAD-Access-Panel]
* Správci odvolávají přístup k aplikacím jejich odebráním pomocí části [Podnikové aplikace](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps) na webu [Azure Portal][AZURE-portal].

Pokud správce souhlasí s aplikací pro všechny uživatele v tenantovi, uživatelé nemohou odvolat přístup jednotlivě. Přístup může odvolat pouze správce a pouze pro celou aplikaci.

## <a name="multi-tenant-applications-and-caching-access-tokens"></a>Víceklientské aplikace a přístupové tokeny pro ukládání do mezipaměti

Víceklientské aplikace můžete také získat přístupové tokeny pro volání api, které jsou chráněné Azure AD. Běžnou chybou při použití knihovny autentizace služby Active Directory (ADAL) s víceklientskou aplikací je nejprve požádat o token pro uživatele, který používá /common, obdrží odpověď a poté požádá o následný token pro stejného uživatele, který také používá /common. Vzhledem k tomu, že odpověď z Azure AD pochází z tenanta, není /common, ADAL ukládá token jako bytí z tenanta. Následné volání /common získat přístupový token pro uživatele chybí položka mezipaměti a uživatel je vyzván k přihlášení znovu. Chcete-li zabránit chybějící mezipaměti, ujistěte se, že následné volání již přihlášeného uživatele jsou provedeny koncového bodu klienta.

## <a name="next-steps"></a>Další kroky

V tomto článku jste se dozvěděli, jak vytvořit aplikaci, která můžete přihlásit uživatele z libovolného klienta Azure AD. Po povolení jednotného přihlašování (SSO) mezi vaší aplikací a Azure AD, můžete také aktualizovat aplikace pro přístup k rozhraní API vystavené prostředky Microsoftu, jako je Office 365. To vám umožní nabídnout přizpůsobené prostředí ve vaší aplikaci, jako je například zobrazení kontextových informací uživatelům, jako je jejich profilový obrázek nebo jejich další událost v kalendáři. Další informace o volání rozhraní API do služby Azure AD a Office 365, jako je Exchange, SharePoint, OneDrive, OneNote a další, navštivte [Microsoft Graph API][MSFT-Graph-overview].

## <a name="related-content"></a>Související obsah

* [Ukázka víceklientské aplikace](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/blob/master/2-WebApp-graph-user/2-3-Multi-Tenant/README.md)
* [Pokyny pro branding aplikací][AAD-App-Branding]
* [Aplikační objekty a objekty instančního objektu][AAD-App-SP-Objects]
* [Integrace aplikací se službou Azure Active Directory][AAD-Integrating-Apps]
* [Přehled rámce pro souhlas][AAD-Consent-Overview]
* [Obory oprávnění rozhraní API rozhraní Microsoft Graph][MSFT-Graph-permission-scopes]

<!--Reference style links IN USE -->
[AAD-Access-Panel]:  https://myapps.microsoft.com
[AAD-App-Branding]:howto-add-branding-in-azure-ad-apps.md
[AAD-App-Manifest]:reference-azure-ad-app-manifest.md
[AAD-App-SP-Objects]:app-objects-and-service-principals.md
[AAD-Auth-Scenarios]:authentication-scenarios.md
[AAD-Consent-Overview]:consent-framework.md
[AAD-Dev-Guide]:azure-ad-developers-guide.md
[AAD-Integrating-Apps]:quickstart-v1-integrate-apps-with-azure-ad.md
[AAD-Samples-MT]: https://docs.microsoft.com/samples/browse/?products=azure-active-directory
[AAD-Why-To-Integrate]: ./active-directory-how-to-integrate.md
[AZURE-portal]: https://portal.azure.com
[MSFT-Graph-overview]: https://developer.microsoft.com/graph/docs/overview/overview
[MSFT-Graph-permission-scopes]: https://developer.microsoft.com/graph/docs/concepts/permissions_reference

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
[O365-Perm-Ref]: https://msdn.microsoft.com/office/office365/howto/application-manifest
[OAuth2-Access-Token-Scopes]: https://tools.ietf.org/html/rfc6749#section-3.3
[OAuth2-AuthZ-Code-Grant-Flow]: https://msdn.microsoft.com/library/azure/dn645542.aspx
[OAuth2-AuthZ-Grant-Types]: https://tools.ietf.org/html/rfc6749#section-1.3 
[OAuth2-Client-Types]: https://tools.ietf.org/html/rfc6749#section-2.1
[OAuth2-Role-Def]: https://tools.ietf.org/html/rfc6749#page-6
[OpenIDConnect]: https://openid.net/specs/openid-connect-core-1_0.html
[OpenIDConnect-ID-Token]: https://openid.net/specs/openid-connect-core-1_0.html#IDToken
