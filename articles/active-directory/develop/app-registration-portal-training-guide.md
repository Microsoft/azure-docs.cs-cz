---
title: Nové prostředí registrace aplikací na webu Azure Portal
titleSuffix: Microsoft identity platform
description: Úvod do nového prostředí pro registraci aplikací na webu Azure Portal
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 11/8/2019
ms.author: marsma
ms.reviewer: lenalepa, alamaral
ms.custom: aaddev
ms.openlocfilehash: 50c88dd1785bd9177219054fed3800ca725a5274
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80154589"
---
# <a name="the-new-azure-portal-app-registration-experience"></a>Nové prostředí registrace aplikace portálu Azure

Existuje mnoho vylepšení v novém prostředí [registrace aplikací](https://go.microsoft.com/fwlink/?linkid=2083908) na webu Azure Portal. Pokud jste více obeznámeni s portálem registrace aplikací (apps.dev.microsoft.com) zkušenosti pro registraci nebo správu konvergovaných aplikací, označované jako staré prostředí, tento průvodce školení vám pomůže začít používat nové prostředí.

## <a name="whats-not-changing"></a>Co se nemění?

- Vaše aplikace a související konfigurace lze nalézt jako-je v novém prostředí. Aplikace není nutné znovu registrovat a uživatelé vašich aplikací se nebudou muset znovu přihlašovat.

    > [!NOTE]
    > Musíte se přihlásit pomocí účtu, který jste použili k registraci aplikací, abyste je našli na webu Azure Portal. Doporučujeme zkontrolovat, zda se přihlášený uživatel na portálu Azure shoduje s uživatelem, který byl přihlášen k portálu pro registraci aplikací porovnáním e-mailové adresy z vašeho profilu.
    >
    > V některých případech, zejména když se přihlásíte pomocí osobních účtů Microsoft (např. Outlook, Live, Xbox atd.) s e-mailovou adresou Azure AD, zjistili jsme, že když jdete na portál Azure ze starého prostředí, podepíše vás na jiný účet se stejným e-mailu v tenantovi Azure AD. Pokud se stále domníváte, že vaše žádosti chybí, odhlaste se a přihlaste se pomocí správného účtu.

- Živé aplikace Sady SDK vytvořené pomocí osobních účtů Microsoft ještě nejsou na webu Azure Portal podporovány a v blízké budoucnosti zůstanou ve starém prostředí.

## <a name="key-changes"></a>Klíčové změny

-   Ve starém prostředí byly aplikace ve výchozím nastavení registrovány jako konvergované aplikace podporující všechny účty organizace (více klientů) i osobní účty Microsoft. To nelze změnit prostřednictvím staré prostředí, takže je obtížné vytvořit aplikace, které podporují pouze účty organizace (víceklientské nebo jednoho klienta).
    Nové prostředí umožňuje zaregistrovat aplikace podporující všechny tyto možnosti. [Přečtěte si další informace o typech aplikací](active-directory-v2-registration-portal.md).

-   V novém prostředí, pokud váš osobní účet Microsoft je také v tenantovi Azure AD, uvidíte tři karty – všechny aplikace v tenantovi, vlastněné aplikace v tenantovi, stejně jako aplikace z vašeho osobního účtu. Pokud se tedy domníváte, že aplikace registrované u vašeho osobního účtu Microsoft chybí, podívejte se na kartu Aplikace na kartě **osobního účtu.**

-   V novém prostředí můžete snadno přepínat mezi klienty přechodem na svůj profil a výběrem přepnout adresář.

## <a name="list-of-applications"></a>Seznam žádostí

-   Nový seznam aplikací zobrazuje aplikace, které byly registrované prostřednictvím starších verzí prostředí pro registrace aplikací na webu Azure Portal (aplikace, které se přihlašují jenom k účtům Azure AD) a také aplikace registrované prostřednictvím [portálu pro registraci aplikací](https://apps.dev.microsoft.com/) (aplikace, které se přihlašují v Azure AD i osobních účtech Microsoftu).

-   Nový seznam aplikací má dva další sloupce: **Vytvořeno ve** sloupci a certifikáty & sloupce **tajných kódů,** který zobrazuje stav (aktuální, brzy vyprší nebo vypršela jeho platnost) přihlašovacích údajů, které byly v aplikaci zaregistrovány.

## <a name="new-app-registration"></a>Registrace nové aplikace

Ve starém prostředí, chcete-li zaregistrovat konvergované aplikace jste byli povinni zadat pouze název. Aplikace, které byly vytvořeny byly registrovány jako konvergované aplikace podporující všechny organizační adresáře (víceklientů) i osobní účty Microsoft.  To nelze změnit prostřednictvím staré prostředí, takže je obtížné vytvořit aplikace, které podporují pouze účty organizace (víceklientské nebo jednoho klienta). [Další informace o podporovaných typech účtů](v2-supported-account-types.md)

V novém prostředí je nutné zadat název aplikace a zvolit podporované typy účtů. Volitelně můžete zadat identifikátor URI přesměrování.
Pokud zadáte identifikátor URI přesměrování, budete muset určit, jestli je web/public (nativní/mobilní a desktopové). Další informace o tom, jak zaregistrovat aplikaci pomocí nového prostředí pro registrace aplikací, najdete [v tomto rychlém startu](quickstart-register-app.md).

## <a name="app-management-page"></a>Stránka správy aplikací

Staré prostředí mělo jednu stránku správy aplikací pro konvergované aplikace s následujícími částmi: Vlastnosti, tajné kódy aplikací, platformy, vlastníci, oprávnění microsoft graphu, profil a rozšířené možnosti.

Nové prostředí na webu Azure Portal představuje tyto funkce do samostatných stránek. Zde najdete ekvivalentní funkce:

-   Vlastnosti – ID názvu a aplikace je na stránce Přehled.

-   Tajné klíče aplikací jsou na stránce & tajných kódů certifikátů

-   Konfigurace platforem je na stránce Ověřování

-   Oprávnění aplikace Microsoft Graph jsou na stránce oprávnění rozhraní API spolu s dalšími oprávněními

-   Profil je na stránce Branding

-   Rozšířená možnost – podpora sady Live SDK je na stránce Ověřování.

## <a name="application-secretscertificates--secrets"></a>Tajné klíče aplikace/certifikáty & tajných kódů

V novém prostředí byly **tajné klíče aplikací** přejmenovány na **certifikáty & tajných kódů**. Kromě toho **veřejné klíče** jsou označovány jako certifikáty a **hesla** jsou **označovány** jako **tajné klíče klienta**. Rozhodli jsme se nepřinášet tuto funkci v novém prostředí z bezpečnostních důvodů, proto již nelze generovat nový pár klíčů.

## <a name="platformsauthentication-reply-urlsredirect-uris"></a>Platformy/ověřování: Odpovědi na adresy URL/identifikátory URI přesměrování
Ve starém prostředí měla aplikace oddíl Platformy pro webové, nativní a webové rozhraní API pro konfiguraci adres URL přesměrování, adresy URL odhlášení a implicitního toku.

V novém prostředí lze adresy URL odpovědí nalézt\'v části Ověřování aplikace. Kromě toho jsou označovány jako přesměrování identifikátorů URI a změnil se formát pro přesměrování identifikátorů URI. Musí být přidruženy k typu aplikace (web nebo veřejný klient – mobilní a stolní). [Další informace](quickstart-configure-app-access-web-apis.md#add-redirect-uris-to-your-application)

Webová rozhraní API jsou konfigurována na stránce Vystavit rozhraní API.

> [!NOTE]
> Vyzkoušejte nové prostředí nastavení ověřování, kde můžete nakonfigurovat nastavení aplikace na základě platformy nebo zařízení, na které chcete cílit. [Další informace](quickstart-configure-app-access-web-apis.md#configure-platform-settings-for-your-application)

## <a name="microsoft-graph-permissionsapi-permissions"></a>Oprávnění/rozhraní API aplikace Microsoft Graph

-   Při výběru rozhraní API ve starém prostředí, můžete si vybrat pouze z rozhraní API aplikace Microsoft Graph. V novém prostředí si můžete vybrat z mnoha rozhraní API Společnosti Microsoft, včetně Microsoft Graphu, rozhraní API z vaší organizace a rozhraní API, zobrazí se na třech kartách: rozhraní MICROSOFT API, rozhraní API, moje organizace používá nebo moje rozhraní API. Panel hledání na api moje organizace používá kartu vyhledávání prostřednictvím instančních objektů v tenantovi.

    > [!NOTE]
    > Tuto kartu se nezobrazí, pokud vaše aplikace není přidružena k tenantovi. Další informace o tom, jak požádat o oprávnění pomocí nového prostředí, naleznete [v tomto rychlém startu](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/active-directory/develop/quickstart-configure-app-access-web-apis.md).

-   Staré prostředí nemělo tlačítko **Oprávnění grantu.** V novém prostředí je část Udělit souhlas s tlačítkem **Udělit souhlas správce** v části oprávnění rozhraní API aplikace. Souhlas může udělit pouze správce a toto tlačítko je povoleno pouze pro správce. Když správce vybere tlačítko **Udělit souhlas správce,** souhlas správce se udělí všem požadovaným oprávněním.

## <a name="profile"></a>Profil
Ve starých zkušenostech měl profil logo, adresu URL domovské stránky, adresu URL smluvních podmínek služby a konfiguraci adresy URL prohlášení o zásadách ochrany osobních údajů. V novém prostředí je lze nalézt na stránce Branding.

## <a name="application-manifest"></a>Manifest aplikace
V novém prostředí umožňuje stránka Manifest upravovat a aktualizovat atributy aplikace. Další informace naleznete v tématu [Manifest aplikace](reference-app-manifest.md).

## <a name="new-ui"></a>Nové ui
Je nové rozhraní pro vlastnosti, které by mohly být dříve nastaveny pouze pomocí editoru manifestu nebo rozhraní API nebo neexistovaly.

-   Implicitní tok grantů (oauth2AllowImplicitFlow) lze nalézt na stránce Ověřování. Na rozdíl od staré zkušenosti můžete povolit přístupové tokeny nebo Tokeny ID nebo obojí.

-   Obory definované tímto rozhraním API (oauth2Permissions) a autorizované klientské aplikace (preAuthorizedApplications) lze nakonfigurovat prostřednictvím stránky Vystavit rozhraní API. Další informace o tom, jak nakonfigurovat aplikaci jako webové rozhraní API a vystavit oprávnění nebo obory, naleznete [v tomto rychlém startu](quickstart-configure-app-expose-web-apis.md).

-   Doménu aplikace Publisher (která se uživatelům zobrazuje na [výzvu k\'souhlasu aplikace)](application-consent-experience.md)naleznete na stránce příkazu Branding. Další informace o konfiguraci domény vydavatele naleznete v [tomto návodu](howto-configure-publisher-domain.md).

## <a name="limitations"></a>Omezení

Nové prostředí má následující omezení:

-   Nové prostředí ještě nepodporuje registrace aplikací pro klienty Azure AD B2C.

-   Nové prostředí zatím nepodporuje aplikace Live SDK vytvořené pomocí osobních účtů Microsoft.

-   Změna hodnoty podporovaných účtů není v uživatelském uživatelském uživatelském mase podporována. Musíte použít manifest aplikace,\'pokud znovu přepínámezi Azure AD jednoho tenanta a více klienta.

   > [!NOTE]
   > Pokud jste osobní uživatel účtu Microsoft v tenantovi Azure AD a správce tenanta má omezený přístup k portálu Azure, může se vám přístup odepřít. Pokud však zástupcem zadáte zadáním registrace aplikací na vyhledávací panel nebo ji připnete, budete mít přístup k novému prostředí.
