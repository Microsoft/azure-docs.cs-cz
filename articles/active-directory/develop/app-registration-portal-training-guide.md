---
title: Nové prostředí registrace aplikací na webu Azure Portal
titleSuffix: Microsoft identity platform
description: Úvod k novému prostředí pro registraci aplikací v Azure Portal
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: how-to
ms.date: 11/8/2019
ms.author: marsma
ms.reviewer: lenalepa, alamaral
ms.custom: aaddev
ms.openlocfilehash: 13242a41e1d10b0df031bf10fd646d9ec3cf47c3
ms.sourcegitcommit: bf1340bb706cf31bb002128e272b8322f37d53dd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/03/2020
ms.locfileid: "89437764"
---
# <a name="the-new-azure-portal-app-registration-experience"></a>Nové prostředí pro registraci aplikací Azure Portal

Nové prostředí [Registrace aplikací](https://go.microsoft.com/fwlink/?linkid=2083908) v Azure Portal přináší mnoho vylepšení. Pokud znáte portál pro registraci aplikací (apps.dev.microsoft.com) pro registraci nebo správu aplikací, které se tady označují jako "staré prostředí", tato příručka vám pomůže začít používat nové prostředí.

## <a name="whats-not-changing"></a>Co se nemění?

- Vaše aplikace a související konfigurace se dají najít tak, jak jsou, v novém prostředí. Nemusíte znovu registrovat aplikace a uživatelé vašich aplikací se nemusí znovu přihlašovat.

    > [!NOTE]
    > Musíte se přihlásit pomocí účtu, který jste použili k registraci aplikací, abyste je našli v Azure Portal. Doporučujeme, abyste zkontrolovali, že přihlášený uživatel v Azure Portal odpovídá uživateli, který se přihlásil k portálu pro registraci aplikací porovnáním e-mailové adresy z vašeho profilu.
    >
    > V některých případech, zejména když se přihlašujete pomocí osobních účtů Microsoft (např. Outlook, Live, Xbox atd.) pomocí e-mailové adresy Azure AD, jsme zjistili, že když přejdete na Azure Portal ze starého prostředí, přihlásíte se k jinému účtu se stejným e-mailem v tenantovi Azure AD. Pokud stále nevíte, že vaše aplikace chybí, odhlaste se a přihlaste se pomocí správného účtu.

- Živé aplikace sady SDK vytvořené pomocí osobních účtů Microsoft se v Azure Portal ještě nepodporují a ve starém prostředí se v blízké budoucnosti nadále zůstanou.

## <a name="key-changes"></a>Změny klíčů

-   Ve starém prostředí byly aplikace standardně registrované jako *sblížené* aplikace – aplikace, které podporují všechny účty organizace (víceklientské) a také osobní účty Microsoft. Tato možnost se nedá změnit starým prostředím, takže je obtížné vytvářet aplikace, které podporují jenom účty organizace (ve víceklientské nebo jediném tenantovi).
    Nové prostředí umožňuje registrovat aplikace podporující všechny tyto možnosti. [Přečtěte si další informace o typech aplikací](active-directory-v2-registration-portal.md).

-   Pokud je vaše osobní účet Microsoft také v tenantovi služby Azure AD, zobrazí se v novém prostředí tři karty – všechny aplikace v tenantovi, vlastněné aplikace v tenantovi i aplikace z vašeho osobního účtu. Pokud se tedy domníváte, že aplikace zaregistrované ve vašich osobních účet Microsoft chybí, podívejte se na kartu **aplikace na osobním účtu** .

-   V novém prostředí můžete mezi klienty snadno přepínat tak, že přejdete do svého profilu a zvolíte přepínač adresář.

## <a name="list-of-applications"></a>Seznam aplikací

-   V seznamu nový seznam aplikací se zobrazí aplikace, které byly zaregistrovány v prostředí pro registraci starší verze aplikace v Azure Portal (aplikace, které se přihlásí pouze k účtům Azure AD), i aplikace zaregistrované i v případě, že [portál pro registraci aplikací](https://apps.dev.microsoft.com/) (aplikace, které se přihlásí k účtům Microsoft Azure AD a osobní

-   Nový seznam aplikací obsahuje dva další sloupce: **Vytvořeno na** sloupec a **certifikáty & sloupci tajné klíče** , který zobrazuje stav (aktuální, vypršení platnosti nebo vypršení platnosti) přihlašovacích údajů registrovaných v aplikaci.

## <a name="new-app-registration"></a>Registrace nové aplikace

Ve starém prostředí můžete zaregistrovat aplikaci, kterou jste zadali jenom k zadání názvu. Vytvořené aplikace byly zaregistrované jako *sblížené* aplikace – aplikace podporující všechny adresáře organizace (víceklientské) a také osobní účty Microsoft.  Tato možnost se nedá změnit starým prostředím, takže je obtížné vytvářet aplikace, které podporují jenom účty organizace (jeden nebo víc tenantů). [Další informace o podporovaných typech účtů](v2-supported-account-types.md)

V novém prostředí musíte zadat název aplikace a vybrat podporované typy účtů. Volitelně můžete zadat identifikátor URI pro přesměrování.
Pokud zadáte identifikátor URI pro přesměrování, budete muset určit, jestli je to Web/Public (nativní/mobilní a Desktop). Další informace o tom, jak zaregistrovat aplikaci s využitím nových možností registrace aplikací, najdete v [tomto rychlém](quickstart-register-app.md)startu.

## <a name="app-management-page"></a>Stránka správy aplikací

Původní prostředí obsahovalo jednu stránku správy aplikací pro aplikace s následujícími oddíly: vlastnosti, tajné klíče aplikace, platformy, vlastníci, Microsoft Graph oprávnění, profil a rozšířené možnosti.

Nové prostředí v Azure Portal prezentuje tyto funkce na samostatných stránkách. Můžete najít ekvivalentní funkce:

- Vlastnosti – název a ID aplikace se nachází na stránce Přehled.
- Tajné klíče aplikace se nachází na stránce certifikáty & tajných kódů
- Konfigurace platforem na stránce ověřování
- Oprávnění Microsoft Graph se nachází na stránce oprávnění rozhraní API spolu s dalšími oprávněními.
- Profil je na stránce brandingu.
- Rozšířená možnost – Podpora sady Live SDK je na stránce ověřování.

## <a name="application-secretscertificates--secrets"></a>Tajné klíče aplikace/certifikáty & tajné klíče

V novém prostředí byly **tajné klíče aplikace** přejmenovány na **certifikáty & tajných**kódů. **Veřejné klíče** se navíc označují jako **certifikáty** a **hesla** se označují jako **tajné klíče klienta**. Tuto funkci jsme se rozhodli z důvodu zabezpečení nepoužívat společně s novým prostředím, takže už nemůžete vygenerovat nový pár klíčů.

## <a name="platformsauthentication-reply-urlsredirect-uris"></a>Platformy/ověřování: adresy URL odpovědí/identifikátory URI pro přesměrování
Ve starém prostředí obsahovala aplikace platformy pro web, nativní a webové rozhraní API ke konfiguraci adres URL pro přesměrování, adresy URL pro odhlášení a implicitního toku.

V novém prostředí se adresy URL odpovědí dají najít v \' části ověřování aplikace. Kromě toho se označují jako identifikátory URI přesměrování a změna formátu identifikátorů URI pro přesměrování se změnila. Musí být přidružené k typu aplikace (webový nebo veřejný klient – mobilní zařízení a Desktop). [Další informace](quickstart-register-app.md#add-a-redirect-uri)

Webová rozhraní API jsou nakonfigurovaná na stránce vystavení rozhraní API.

> [!NOTE]
> Vyzkoušejte si nové prostředí nastavení ověřování, kde můžete nakonfigurovat nastavení pro svou aplikaci na základě platformy nebo zařízení, na které chcete cílit. [Další informace](quickstart-register-app.md#configure-platform-settings)

## <a name="microsoft-graph-permissionsapi-permissions"></a>Oprávnění Microsoft Graph/oprávnění rozhraní API

-   Když vybíráte rozhraní API ve starém prostředí, můžete zvolit jenom Microsoft Graph rozhraní API. V novém prostředí si můžete vybrat z mnoha rozhraní Microsoft API, včetně Microsoft Graph, rozhraní API z vaší organizace a vašich rozhraní API, které se zobrazí na třech kartách: rozhraní API Microsoftu, rozhraní API moje organizace používá nebo moje rozhraní API. Panel hledání na rozhraních API moje organizace používá hledání na kartách prostřednictvím instančních objektů v tenantovi.

    > [!NOTE]
    > Tato karta se nezobrazí, pokud aplikace není přidružena k tenantovi. Další informace o tom, jak požádat o oprávnění pomocí nového prostředí, najdete v [tomto rychlém](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/active-directory/develop/quickstart-configure-app-access-web-apis.md)startu.

-   Původní prostředí neobsahovalo tlačítko **udělení oprávnění** . V novém prostředí obsahuje oddíl souhlasu s udělením souhlasu správce s oprávněním **udělit správcům** oprávnění k rozhraní API aplikace. Pouze správce může udělit souhlas a toto tlačítko je povoleno pouze správcům. Když správce vybere tlačítko **udělení souhlasu správce** , udělí se mu souhlasu správce pro všechna požadovaná oprávnění.

## <a name="profile"></a>Profil
Ve starém prostředí měl profil logo, URL domovské stránky, podmínky adresy URL služby a prohlášení o zásadách ochrany osobních údajů v adrese URL. V novém prostředí se dají najít na stránce branding.

## <a name="application-manifest"></a>Manifest aplikace
Na stránce nové prostředí můžete pomocí stránky manifest upravit a aktualizovat atributy aplikace. Další informace naleznete v tématu [manifest aplikace](reference-app-manifest.md).

## <a name="new-ui"></a>Nové uživatelské rozhraní
K dispozici je nové uživatelské rozhraní pro vlastnosti, které se dřív daly nastavit pomocí editoru manifestu nebo rozhraní API, nebo jestli neexistuje.

-   Implicitní tok udělení (oauth2AllowImplicitFlow) najdete na stránce ověřování. Na rozdíl od starého prostředí můžete povolit přístupové tokeny nebo tokeny ID nebo obojí.

-   Obory definované tímto rozhraním API (oauth2Permissions) a autorizované klientské aplikace (preAuthorizedApplications) je možné konfigurovat prostřednictvím stránky vystavení rozhraní API. Další informace o tom, jak nakonfigurovat aplikaci jako webové rozhraní API a jak zveřejnit oprávnění nebo obory, najdete v [tomto rychlém](quickstart-configure-app-expose-web-apis.md)startu.

-   Doména vydavatele (která se zobrazí uživatelům v [ \' příkazovém řádku s informacemi o souhlasu s aplikací](application-consent-experience.md)), najdete na stránce branding. Další informace o tom, jak nakonfigurovat doménu vydavatele, najdete v [tomto postupu](howto-configure-publisher-domain.md).

## <a name="limitations"></a>Omezení

Nové prostředí má následující omezení:

-   Nové prostředí zatím nepodporuje Registrace aplikací pro klienty Azure AD B2C.

-   Nové prostředí zatím nepodporuje živé aplikace sady SDK vytvořené pomocí osobních účtů Microsoft.

-   Změna hodnoty pro podporované účty není v uživatelském rozhraní podporována. Pokud \' přepínáte mezi jedním klientem Azure AD a více klienty, je nutné použít manifest aplikace.

   > [!NOTE]
   > Pokud jste osobní účet Microsoft uživatele v tenantovi Azure AD a správce tenanta má omezený přístup k Azure Portal, můžete získat přístup odepřený. Pokud však provedete zástupcem zadáním Registrace aplikací na panelu hledání nebo připnete ho, budete mít přístup k novému prostředí.

## <a name="next-steps"></a>Další kroky

Pokud chcete začít s novým prostředím pro registraci aplikací, přečtěte si téma [rychlý Start: registrace aplikace s platformou Microsoft Identity](quickstart-register-app.md).
