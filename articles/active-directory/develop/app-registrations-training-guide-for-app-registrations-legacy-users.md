---
title: Průvodce výukou pro registraci aplikací Azure Portal (starší verze) – Azure
description: Úvod do nového prostředí pro registraci aplikací v platformě Microsoft identity.
services: active-directory
documentationcenter: ''
author: archieag
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/26/2019
ms.author: aragra
ms.reviewer: lenalepa, keyam
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: bbdb6a2e17ab867b4938e94ae5a20a95cc352daa
ms.sourcegitcommit: bc193bc4df4b85d3f05538b5e7274df2138a4574
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/10/2019
ms.locfileid: "73905391"
---
# <a name="training-guide-app-registrations-in-the-azure-portal-legacy"></a>Průvodce školením: Registrace aplikací v Azure Portal (starší verze)

V novém prostředí [Registrace aplikací](https://go.microsoft.com/fwlink/?linkid=2083908) v Azure Portal najdete řadu vylepšení. Pokud jste se starší verzí prostředí seznámili, pomocí tohoto průvodce školením můžete začít používat nové prostředí.

V Azure Active Directory nové prostředí pro registraci aplikací popsané tady je všeobecně dostupné (GA). V Azure Active Directory B2C (Azure AD B2C) je toto prostředí ve verzi Preview.

## <a name="key-changes"></a>Změny klíčů

- Registrace aplikací nejsou omezené buď na webovou aplikaci, nebo na **rozhraní API** , nebo na **nativní** aplikaci. Stejnou registraci aplikace můžete použít pro všechny tyto registrace tím, že zaregistrujete příslušné identifikátory URI pro přesměrování.
- Služba Legacy App podporuje jenom aplikace, které přihlásí jenom účty organizace (Azure AD). Aplikace se zaregistrovaly jako jeden tenant (podporují se jenom účty organizace z adresáře, ve kterém se aplikace zaregistrovala) a dají se upravit tak, aby byla více tenantů (podpora všech účtů organizace). Nové prostředí umožňuje registrovat aplikace, které podporují obě tyto možnosti, i třetí možnost: všechny účty organizace a také osobní účty Microsoft.
- Starší verze prostředí byla k dispozici jenom v případě, že jste se k Azure Portal přihlásili pomocí účtu organizace. S novým prostředím můžete použít osobní účty Microsoft, které nejsou přidružené k adresáři.

## <a name="list-of-applications"></a>Seznam aplikací

- V seznamu nový seznam aplikací se zobrazí aplikace, které byly zaregistrovány v prostředí pro registraci starší verze aplikace v Azure Portal (aplikace, které připisují účty Azure AD), i aplikace zaregistrované i na [portálu pro registraci aplikací](https://apps.dev.microsoft.com/) (aplikace, které se přihlásí Účty Azure AD a osobní účty Microsoft).
- Nový seznam aplikací nemá sloupec **typu aplikace** (protože registrace jedné aplikace může být několik typů) a obsahuje dva další sloupce: **Vytvořeno ve** sloupci a **certifikáty & tajných** kódů, které zobrazují stav (aktuální, vypršení platnosti nebo vypršení platnosti přihlašovacích údajů, které byly zaregistrovány v aplikaci.

## <a name="new-app-registration"></a>Registrace nové aplikace

V části starší verze můžete zaregistrovat aplikaci, kterou jste museli zadat: **název**, **Typ aplikace**a **Adresa URL pro přihlášení nebo identifikátor URI pro přesměrování**. Vytvořené aplikace byly jenom pro jediné klientské aplikace, což znamená, že v adresáři, ve kterém se aplikace zaregistrovala, jsou jenom ty, které podporují jenom organizační účty.

V novém prostředí musíte zadat **název** aplikace a vybrat **podporované typy účtů**. Volitelně můžete zadat **identifikátor URI pro přesměrování**. Pokud zadáte identifikátor URI pro přesměrování, budete muset určit, jestli je to Web/Public (mobilní aplikace a Desktop). Další informace o tom, jak zaregistrovat aplikaci s využitím nových možností registrace aplikací, najdete v tématu [Registrace aplikace s platformou Microsoft Identity](quickstart-register-app.md). Azure AD B2C najdete v tématu [Registrace aplikace v Azure Active Directory B2C](../../active-directory-b2c/tutorial-register-applications.md).

## <a name="the-legacy-properties-page"></a>Stránka vlastností starší verze

Starší verze prostředí měly stránku **vlastností** , která nemá nové prostředí. Okno **vlastnosti** má následující pole: **název**, **ID objektu**, **ID aplikace**, **identifikátor URI ID aplikace**, **logo**, **Adresa URL domovské stránky**, adresa URL pro **odhlášení**, adresa **URL podmínek služby**, **prohlášení o zásadách ochrany osobních údajů Adresa URL**, **Typ aplikace**a **Vícenásobný tenant.**

V novém prostředí můžete najít ekvivalentní funkce:

- **Název**, **logo**, **Adresa URL domovské stránky**, adresa **URL podmínek služby**a **Adresa URL prohlášení o zásadách ochrany osobních údajů** se teď nachází na stránce **brandingu** aplikace.
- ID **objektu** a **ID aplikace (klienta)** se nachází na stránce **Přehled** .
- Funkce řízená přepínačem **více tenantů** ve starších verzích se nahradily **podporovanými typy účtů** na stránce **ověřování** . Další informace o tom, jak se více tenantů mapuje na podporované možnosti typu účtu, najdete v [tomto rychlém](quickstart-modify-supported-accounts.md)startu.
- **Adresa URL pro odhlášení** se teď nachází na stránce **ověřování** .
- **Typ aplikace** již není platným polem. Místo toho je třeba přesměrovat identifikátory URI (které můžete najít na stránce **ověřování** ) a určit, které typy aplikací se podporují.
- **Identifikátor URI ID** aplikace se teď nazývá **identifikátor URI ID aplikace** a najdete ho v okně **vystavení rozhraní API** . Ve starší verzi se tato vlastnost zaregistrovala automaticky v následujícím formátu: `https://{tenantdomain}/{appID}` (například `https://microsoft.onmicrosoft.com/aeb4be67-a634-4f20-9a46-e0d4d4f1f96d`). V novém prostředí se automaticky generuje jako `api://{appID}`, ale je potřeba ho explicitně uložit. Ve Azure AD B2C tenantech se stále používá formát `https://{tenantdomain}/{appID}`.

## <a name="reply-urlsredirect-urls"></a>Adresy URL odpovědí/adresy URL pro přesměrování

Ve starší verzi prostředí měla aplikace na stránce **adresy URL odpovědi** . V novém prostředí se adresy URL odpovědí dají najít v části **ověřování** aplikace. Kromě toho se označují jako **identifikátory URI přesměrování**. Kromě toho se změnil formát identifikátorů URI pro přesměrování. Musí být přidružené k typu aplikace (web nebo Public). Z bezpečnostních důvodů nejsou podporované zástupné znaky a http://schémata (s výjimkou http://localhost).

## <a name="keyscertificates--secrets"></a>Klíče a certifikáty & tajných kódů

Ve starší verzi se na stránce aplikace obsahovaly **klíče** . Nové prostředí bylo přejmenováno na **certifikáty & tajných**kódů. **Veřejné klíče** se navíc označují jako **certifikáty** a **hesla** se označují jako **tajné klíče klienta**.

## <a name="required-permissionsapi-permissions"></a>Požadovaná oprávnění/oprávnění rozhraní API

- Ve starší verzi se měla aplikace nacházet na stránce **požadovaná oprávnění** . Nové prostředí bylo přejmenováno na **oprávnění rozhraní API**.
- Když vybíráte rozhraní API ve starší verzi, můžete zvolit malý seznam rozhraní Microsoft API nebo Hledat v instančních objektech v tenantovi. V novém prostředí si můžete vybrat z několika karet: **rozhraní API Microsoftu**, **rozhraní API moje organizace používá**nebo **Moje rozhraní API**. Panel hledání na **rozhraních API moje organizace** používá hledání na kartách prostřednictvím instančních objektů v tenantovi.

   > [!NOTE]
   > Tato karta se nezobrazí, pokud aplikace není přidružena k tenantovi. Další informace o tom, jak požádat o oprávnění pomocí nového prostředí, najdete v [tomto rychlém](quickstart-configure-app-access-web-apis.md)startu.

- V horní části stránky **požadovaná oprávnění** měla starší verze prostředí tlačítko **udělení oprávnění** . V novém prostředí obsahuje oddíl **souhlasu** s udělením souhlasu správce s oprávněním **udělit správcům** **oprávnění k rozhraní API** aplikace. Kromě toho jsou v různých způsobech funkcí tlačítek tyto rozdíly:
   - Ve starší verzi je logika proměnlivá v závislosti na přihlášeném uživateli a na požadovaných oprávněních. Logika:
      - Pokud se požaduje jenom oprávnění, která se dají udělit, a přihlášený uživatel není správce, mohl by uživateli udělit souhlas s uživateli pro požadovaná oprávnění.
      - Pokud se požaduje aspoň jedno oprávnění, které vyžaduje souhlas správce, ale přihlášený uživatel není správcem, při pokusu o udělení souhlasu se uživateli stala chyba.
      - Pokud byl přihlášený uživatel správcem, byl udělen souhlas správce pro všechna požadovaná oprávnění.
   - V novém prostředí může udělit souhlas jenom správce. Když správce vybere tlačítko **udělení souhlasu správce** , udělí se mu souhlasu správce pro všechna požadovaná oprávnění.

## <a name="deleting-an-app-registration"></a>Odstranění registrace aplikace

Ve starší verzi aplikace musel být jeden tenant, který se má odstranit. Tlačítko Odstranit bylo pro více tenantů aplikací zakázané. V novém prostředí je možné aplikace odstranit v libovolném stavu, ale je nutné ji potvrdit. Další informace o odstraňování registrací aplikací najdete v [tomto rychlém](quickstart-remove-app.md)startu.

## <a name="application-manifest"></a>Manifest aplikace

Starší a nové prostředí používají různé verze formátu JSON v editoru manifestu. Další informace naleznete v tématu [manifest aplikace](reference-app-manifest.md).

## <a name="new-ui"></a>Nové uživatelské rozhraní

K dispozici je nové uživatelské rozhraní pro vlastnosti, které se dřív daly nastavit pomocí editoru manifestu nebo rozhraní API, nebo jestli neexistuje.

- **Implicitní tok udělení** (oauth2AllowImplicitFlow) najdete na stránce **ověřování** . Na rozdíl od starší verze prostředí můžete povolit **přístupové tokeny** nebo **tokeny ID**nebo obojí.
- **Obory definované tímto rozhraním API** (oauth2Permissions) a **autorizované klientské aplikace** (preAuthorizedApplications) je možné konfigurovat prostřednictvím stránky **vystavení rozhraní API** . Další informace o tom, jak nakonfigurovat aplikaci jako webové rozhraní API a jak zveřejnit oprávnění nebo obory, najdete v [tomto rychlém](quickstart-configure-app-expose-web-apis.md)startu.
- **Doména vydavatele** (která se uživatelům zobrazí v [příkazovém řádku pro vyjádření souhlasu aplikace](application-consent-experience.md)) najdete na stránce s **informacemi o značce** . Další informace o tom, jak nakonfigurovat doménu vydavatele, najdete v [tomto postupu](howto-configure-publisher-domain.md).

## <a name="limitations"></a>Omezení

Nové prostředí má následující omezení:

- Formát tajných kódů klienta (hesla aplikací) se liší od starší verze a může přerušit rozhraní příkazového řádku.
- Změna hodnoty pro podporované účty není v uživatelském rozhraní podporována. Je nutné použít manifest aplikace, pokud neprovádíte přepínání mezi jedním klientem Azure AD a více klienty.