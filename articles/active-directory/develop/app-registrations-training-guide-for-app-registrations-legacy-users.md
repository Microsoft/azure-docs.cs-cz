---
title: Příručka pro školení k registraci aplikací Azure Portal
description: Zavádí nové prostředí pro registraci Azure Portal aplikací.
services: active-directory
author: archieag
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: aragra
ms.reviewer: lenalepa, keyam
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3726ebbe0ebc3725a885c847f33760ebd7e2e8b0
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75424666"
---
# <a name="new-azure-portal-app-registration-training-guide"></a>Příručka pro školení k registraci aplikací Azure Portal

Mnoho vylepšení v novém prostředí [Registrace aplikací](https://go.microsoft.com/fwlink/?linkid=2083908) v Azure Portal najdete v části. Pokud jste obeznámeni s Registrace aplikacím (starším) prostředím Azure Portal, využijte tento průvodce školením, abyste mohli začít používat nové prostředí.

V Azure Active Directory nové prostředí pro registraci aplikací popsané tady je všeobecně dostupné (GA). V Azure Active Directory B2C (Azure AD B2C) je toto prostředí ve verzi Preview.

## <a name="key-changes"></a>Změny klíčů

- Registrace aplikací nejsou omezené buď na webovou aplikaci, nebo na *rozhraní API* , nebo na *nativní* aplikaci. Stejnou registraci aplikace můžete použít pro všechny tyto aplikace tak, že zaregistrujete příslušné identifikátory URI pro přesměrování.

- Starší verze podporovaných aplikací, které se přihlásily pomocí účtů organizace (Azure AD). Aplikace se zaregistrovaly jako jeden tenant. Aplikace podporovaly jenom organizační účty z adresáře, ve kterém se aplikace zaregistrovala. Aplikace je možné upravit tak, aby měly více tenantů a podporovaly všechny účty organizace. Nové prostředí umožňuje registrovat aplikace, které podporují obě tyto možnosti, i třetí možnost: všechny účty organizace a také osobní účty Microsoft.

- Starší verze prostředí byla k dispozici jenom v případě, že jste se k Azure Portal přihlásili pomocí účtu organizace. Díky novému prostředí můžete používat osobní účty Microsoft, které nejsou přidružené k adresáři.

## <a name="list-of-applications"></a>Seznam aplikací

V seznamu nový seznam aplikací se zobrazí aplikace, které byly zaregistrovány prostřednictvím prostředí pro registraci starší verze aplikace v Azure Portal. Tyto aplikace se přihlásí pomocí účtů Azure AD. V seznamu nový aplikace se zobrazují také aplikace zaregistrované i na portálu pro registraci aplikací. Tyto aplikace se přihlásí pomocí Azure AD a osobních účtů Microsoft.

>[!NOTE]
>Portál pro registraci aplikací se už nepoužívá.

Nový seznam aplikací nemá sloupec **typu aplikace** , protože registrace jedné aplikace může být několik typů. Seznam obsahuje dva další sloupce: **Vytvořeno na** a **certifikáty & tajných**kódů. **Certifikáty & tajných** kódů zobrazuje stav přihlašovacích údajů, které byly zaregistrovány v aplikaci. Stavy zahrnují **aktuální**, **vypršení platnosti** **a vypršení platnosti.**

## <a name="new-app-registration"></a>Registrace nové aplikace

V části starší verze můžete zaregistrovat aplikaci, kterou jste museli zadat: **název**, **Typ aplikace**a **Adresa URL pro přihlášení nebo identifikátor URI pro přesměrování**. Vytvořené aplikace byly jenom jednou tenantů aplikacemi Azure AD. Podporují jenom organizační účty z adresáře, ve kterém se aplikace zaregistrovala.

V novém prostředí musíte zadat **název** aplikace a vybrat **podporované typy účtů**. Volitelně můžete zadat **identifikátor URI pro přesměrování**. Pokud zadáte identifikátor URI pro přesměrování, budete muset určit, jestli je to Web/Public (mobilní aplikace a Desktop). Další informace najdete v tématu [rychlý Start: registrace aplikace s platformou Microsoft Identity](quickstart-register-app.md). Azure AD B2C najdete v tématu [Registrace aplikace v Azure Active Directory B2C](../../active-directory-b2c/tutorial-register-applications.md).

## <a name="differences-between-the-application-registration-portal-and-app-registrations-page"></a>Rozdíly mezi portálem pro registraci aplikací a Registrace aplikací stránkou

### <a name="the-legacy-properties-page"></a>Stránka vlastností starší verze

Starší verze prostředí měly stránku **vlastností** . **Vlastnosti** měly následující pole:

- **Název**
- **ID objektu**
- **ID aplikace**
- **Identifikátor URI ID aplikace**
- **Logo**
- **Adresa URL domovské stránky**
- **Odhlašovací adresa URL**
- **Adresa URL podmínek služby**
- **Adresa URL prohlášení o zásadách ochrany osobních údajů**
- **Typ aplikace**
- **Více tenantů**

Nové prostředí nemá tuto stránku. Můžete najít ekvivalentní funkce:

- **Název**, **logo**, **Adresa URL domovské stránky**, adresa **URL podmínek služby**a **Adresa URL prohlášení o zásadách ochrany osobních údajů** jsou teď na stránce **brandingu** aplikace.
- ID **objektu** a **ID aplikace (klienta)** jsou na stránce **Přehled** .
- Funkce řízená přepínačem **více tenantů** ve starších verzích se nahradily **podporovanými typy účtů** na stránce **ověřování** . Další informace najdete v tématu [rychlý Start: Změna účtů podporovaných aplikací](quickstart-modify-supported-accounts.md).
- **Adresa URL pro odhlášení** se teď nachází na stránce **ověřování** .
- **Typ aplikace** již není platným polem. Místo toho přesměrujte identifikátory URI, které najdete na stránce **ověřování** , a určete, které typy aplikací se podporují.
- **Identifikátor URI ID** aplikace se teď nazývá **identifikátor URI ID aplikace** a můžete ho najít na **vystavení rozhraní API**. Ve starší verzi se tato vlastnost zaregistrovala pomocí následujícího formátu: `https://{tenantdomain}/{appID}`, například `https://microsoft.onmicrosoft.com/492439af-3282-44c3-b297-45463339544b`. V novém prostředí se automaticky generuje jako `api://{appID}`, ale je potřeba ho explicitně uložit. Ve Azure AD B2C tenantech se stále používá formát `https://{tenantdomain}/{appID}`.

### <a name="reply-urlsredirect-urls"></a>Adresy URL odpovědí/adresy URL pro přesměrování

Ve starší verzi prostředí měla aplikace na stránce **adresy URL odpovědi** . V novém prostředí se adresy URL odpovědí dají najít na stránce **ověřování** aplikace. Nyní se označují jako **identifikátory URI přesměrování**.

Formát identifikátorů URI pro přesměrování se změnil. Je nutné, aby byly přidruženy k typu aplikace, a to na webu nebo na veřejném. Z bezpečnostních důvodů nejsou podporované zástupné znaky a `http://`, s výjimkou *http://localhost* .

### <a name="keyscertificates--secrets"></a>Klíče a certifikáty & tajných kódů

Ve starší verzi se na stránce aplikace obsahovaly **klíče** . Nové prostředí bylo přejmenováno na **certifikáty & tajných**kódů.

**Veřejné klíče** se teď označují jako **certifikáty**. **Hesla** se teď označují jako **tajné klíče klienta**.

### <a name="required-permissionsapi-permissions"></a>Požadovaná oprávnění/oprávnění rozhraní API

Ve starší verzi se měla aplikace nacházet na stránce **požadovaná oprávnění** . Nové prostředí bylo přejmenováno na **oprávnění rozhraní API**.

Když jste v prostředí starší verze vybrali rozhraní API, můžete si vybrat z malého seznamu rozhraní API Microsoftu. Můžete také prohledávat objekty služby v tenantovi. V novém prostředí si můžete vybrat z několika karet: **rozhraní API Microsoftu**, **rozhraní API moje organizace používá**nebo **Moje rozhraní API**. Panel hledání na **rozhraních API moje organizace** používá hledání na kartách prostřednictvím instančních objektů v tenantovi.

> [!NOTE]
> Tato karta se nezobrazí, pokud aplikace není přidružena k tenantovi. Další informace o tom, jak požádat o oprávnění, najdete v tématu [rychlý Start: Konfigurace klientské aplikace pro přístup k webovým rozhraním API](quickstart-configure-app-access-web-apis.md).

V horní části stránky **požadovaná oprávnění** měla starší verze prostředí tlačítko **udělení oprávnění** . Na novém prostředí má stránka **souhlasu s grantem** udělení **souhlasu správce grantu** v sekci **oprávnění API** aplikace. V různých způsobech funkcí tlačítek jsou také některé rozdíly.

Ve starší verzi je logika proměnlivá v závislosti na přihlášeném uživateli a na požadovaných oprávněních. Logika:

- Pokud jste požadovali jenom oprávnění, která se přihlásila uživateli a uživatel, který není správcem, může uživateli udělit souhlas s uživateli pro požadovaná oprávnění.
- Pokud bylo vyžádáno alespoň jedno oprávnění, které vyžaduje souhlas správce, ale uživatel, který nebyl přihlášený jako správce, při pokusu o udělení souhlasu objevil chybu.
- Pokud byl přihlášený uživatel správcem, byl udělen souhlas správce pro všechna požadovaná oprávnění.

V novém prostředí může udělit souhlas jenom správce. Když správce vybere **udělení souhlasu správce**, udělí se mu souhlas správce pro všechna požadovaná oprávnění.

## <a name="deleting-an-app-registration"></a>Odstranění registrace aplikace

Ve starší verzi můžete odstraňovat jenom aplikace pro jednoho tenanta. Tlačítko Odstranit bylo pro více tenantů aplikací zakázané. V novém prostředí můžete aplikace odstraňovat v libovolném stavu, ale musíte tuto akci potvrdit. Další informace najdete v tématu [rychlý Start: odebrání aplikace registrované na platformě Microsoft Identity](quickstart-remove-app.md).

## <a name="application-manifest"></a>Manifest aplikace

Starší a nové prostředí používají různé verze formátu JSON v editoru manifestu. Další informace najdete v tématu [Azure Active Directory manifestu aplikace](reference-app-manifest.md).

## <a name="new-ui"></a>Nové uživatelské rozhraní

Nové prostředí přidává ovládací prvky uživatelského rozhraní pro následující vlastnosti:

- Stránka **ověřování** má **implicitní tok udělení** (`oauth2AllowImplicitFlow`). Na rozdíl od starší verze prostředí můžete povolit **přístupové tokeny** nebo **tokeny ID**nebo obojí.
- Stránka **vystavení rozhraní API** obsahuje **obory definované tímto rozhraním API** (`oauth2Permissions`) a **autorizované klientské aplikace** (`preAuthorizedApplications`). Další informace o tom, jak nakonfigurovat aplikaci jako webové rozhraní API a vystavit oprávnění nebo obory, najdete v tématu [rychlý Start: Konfigurace aplikace k vystavení webových rozhraní API](quickstart-configure-app-expose-web-apis.md).
- Stránka **branding** obsahuje **doménu vydavatele**. Doména vydavatele se zobrazí uživatelům na [příkazovém řádku aplikace pro vyjádření souhlasu](application-consent-experience.md). Další informace najdete v tématu [Postup: Konfigurace domény vydavatele aplikace](howto-configure-publisher-domain.md).

## <a name="limitations"></a>Omezení

Nové prostředí má následující omezení:

- Formát tajných kódů klienta (hesla aplikací) se liší od starší verze a může přerušit rozhraní příkazového řádku.
- Změna hodnoty pro podporované účty není v uživatelském rozhraní podporována. Je nutné použít manifest aplikace, pokud neprovádíte přepínání mezi jedním klientem Azure AD a více klienty.
