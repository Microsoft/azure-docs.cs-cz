---
title: Nový průvodce školením registrace aplikací na webu Azure Portal
description: Představuje nové prostředí registrace aplikací portálu Azure
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: marsma
ms.reviewer: lenalepa, keyam
ms.custom: aaddev
ms.openlocfilehash: a437d54dac50be7ddaad899a1cf0a3e93aade8f5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80154572"
---
# <a name="new-azure-portal-app-registration-training-guide"></a>Nový průvodce školením registrace aplikací na webu Azure Portal

V novém prostředí pro [registrace aplikací](https://go.microsoft.com/fwlink/?linkid=2083908) najdete mnoho vylepšení na webu Azure Portal. Pokud jste obeznámeni s registrací aplikací (starší) zkušenosti na webu Azure Portal, použijte tento průvodce školením a začít používat nové prostředí.

Ve službě Azure Active Directory je obecně dostupné (GA) nové prostředí pro registraci aplikací popsané v tomto dokumentu. Ve službě Azure Active Directory B2C (Azure AD B2C) je toto prostředí ve verzi Preview.

## <a name="key-changes"></a>Klíčové změny

- Registrace aplikací se neomezují jen na *webovou aplikaci nebo rozhraní API* nebo *nativní* aplikaci. Stejnou registraci aplikace můžete použít pro všechny tyto aplikace registrací příslušných identifikátorů URI přesměrování.

- Starší verze prostředí podporované aplikace, které se přihlašují pomocí účtu organizace (Azure AD) jenom. Aplikace byly zaregistrovány jako jeden tenant. Aplikace podporovaly pouze účty organizace z adresáře, ve které byla aplikace zaregistrována. Aplikace mohou být upraveny tak, aby byly víceklientské a podporovaly všechny účty organizace. Nové prostředí umožňuje zaregistrovat aplikace, které mohou podporovat obě tyto možnosti, stejně jako třetí možnost: všechny účty organizace i osobní účty Microsoft.

- Starší verze prostředí bylo k dispozici pouze při přihlášení k portálu Azure pomocí účtu organizace. Díky novému prostředí můžete používat osobní účty Microsoft, které nejsou přidruženy k adresáři.

## <a name="list-of-applications"></a>Seznam žádostí

Nový seznam aplikací zobrazuje aplikace, které byly registrované prostřednictvím starších verzí prostředí pro registrace aplikací na webu Azure Portal. Tyto aplikace se přihlašují pomocí účtů Azure AD. Nový seznam aplikací také zobrazuje aplikace registrované přes portál registrace aplikací. Tyto aplikace se přihlašují pomocí Azure AD a osobních účtů Microsoft.

>[!NOTE]
>Portál registrace aplikací byl zastaral.

Nový seznam aplikací nemá sloupec **typu aplikace,** protože registrace jedné aplikace může být několik typů. Seznam má dva další sloupce: **Vytvořeno a** **Certifikáty & tajných kódů**. **Certifikáty & tajných kódů** zobrazuje stav přihlašovacích údajů, které byly v aplikaci zaregistrovány. Stavy zahrnují **aktuální** **, brzy končící**a **vypršela .**

## <a name="new-app-registration"></a>Registrace nové aplikace

Chcete-li zaregistrovat aplikaci, kterou jste museli zadat: Name , Application type a Sign-on URL/Redirect URI , abyste zaregistrovali aplikaci, kterou jste museli zadat: **Name**, **Application type**a **Sign-on URL/Redirect URI**. Aplikace, které byly vytvořeny byly Azure AD pouze jednoklientské aplikace. Podporovali pouze účty organizace z adresáře, ve které byla aplikace zaregistrována.

V novém prostředí musíte zadat **název** aplikace a zvolit **podporované typy účtů**. Volitelně můžete zadat **identifikátor URI přesměrování**. Pokud zadáte identifikátor URI přesměrování, budete muset určit, zda je web/veřejné (mobilní a stolní). Další informace naleznete [v tématu Úvodní příručka: Registrace aplikace na platformě identit společnosti Microsoft](quickstart-register-app.md). V případě Azure AD B2C najdete [v tématu Registrace aplikace ve službě Azure Active Directory B2C](../../active-directory-b2c/tutorial-register-applications.md).

## <a name="differences-between-the-application-registration-portal-and-app-registrations-page"></a>Rozdíly mezi portálem registrace aplikací a registrační stránkou aplikací

### <a name="the-legacy-properties-page"></a>Stránka Starší vlastnosti

Starší verze prostředí měla stránku **Vlastnosti.** **Vlastnosti** měly následující pole:

- **Název**
- **ID objektu**
- **ID aplikace**
- **Identifikátor URI ID aplikace**
- **Logo**
- **Adresa URL domovské stránky**
- **Adresa URL odhlášení**
- **Podmínky adresy URL služby**
- **Adresa URL prohlášení o zásadách ochrany osobních údajů**
- **Typ aplikace**
- **Víceklientů**

Nové prostředí nemá tuto stránku. Zde najdete ekvivalentní funkce:

- **Název**, **logo**, **adresa URL domovské stránky**, adresa URL **smluvních podmínek služby**a **adresa URL prohlášení o zásadách ochrany osobních údajů** jsou nyní na stránce **branding** aplikace.
- **ID objektu** a **ID aplikace (klienta)** jsou na stránce **Přehled.**
- Funkce řízené **víceklientské** přepnout ve starší verzi prostředí byla nahrazena **podporované typy účtů** na stránce **ověřování.** Další informace naleznete [v tématu Úvodní příručka: Úprava účtů podporovaných aplikací](quickstart-modify-supported-accounts.md).
- **Adresa URL odhlášení** je nyní na stránce **Ověřování.**
- **Typ aplikace** již není platným polem. Místo toho přesměrovat identifikátory URI, které najdete na stránce **Ověřování,** určete, které typy aplikací jsou podporované.
- **Identifikátor URI ID aplikace** se nyní nazývá **IDENTIFIKÁTOR IDENTIFIKÁTOR UZA** a najdete ho na vystavit rozhraní **API**. Ve staršíverzi prostředí tato vlastnost byla automaticky `https://{tenantdomain}/{appID}`registrována pomocí `https://microsoft.onmicrosoft.com/492439af-3282-44c3-b297-45463339544b`následujícího formátu: , například . V novém prostředí je automaticky generovánjako `api://{appID}`, ale je třeba explicitně uložit. V tenantech Azure AD B2C se `https://{tenantdomain}/{appID}` formát stále používá.

### <a name="reply-urlsredirect-urls"></a>Adresy URL/přesměrování adres URL odpovědí

Ve starších verzích prostředí měla aplikace stránku **Adresy URL odpovědí.** V novém prostředí lze adresy URL odpovědí nalézt na stránce **Ověřování** aplikace. Nyní se označuje jako **Přesměrování identifikátorů URI**.

Formát pro přesměrování identifikátorů URI se změnil. Musí být přidruženy k typu aplikace, ať už webovému nebo veřejnému. Z bezpečnostních důvodů nejsou `http://` zástupné znaky a schémata podporovány, s výjimkou . *http://localhost*

### <a name="keyscertificates--secrets"></a>Klíče/certifikáty & tajných kódů

Ve starších verzích aplikace měla stránku **Keys.** V novém prostředí byl přejmenován na **certifikáty & tajných kódů**.

**Veřejné klíče** jsou nyní označovány jako **certifikáty**. **Hesla** jsou nyní označovány jako **tajné klíče klienta**.

### <a name="required-permissionsapi-permissions"></a>Požadovaná oprávnění/oprávnění rozhraní API

Ve staršíverzi prostředí aplikace měla požadovaná **oprávnění** stránku. V novém prostředí byla přejmenována na **oprávnění rozhraní API**.

Když jste vybrali rozhraní API ve starší verzi prostředí, můžete si vybrat z malého seznamu rozhraní API Microsoftu. Můžete také prohledávat instanční objekty v tenantovi. V novém prostředí si můžete vybrat z několika karet: **Microsoft API**, **API, moje organizace používá**nebo Moje rozhraní **API**. Panel hledání na **api moje organizace** používá kartu vyhledávání prostřednictvím instančních objektů v tenantovi.

> [!NOTE]
> Tuto kartu se nezobrazí, pokud vaše aplikace není přidružena k tenantovi. Další informace o tom, jak požádat o oprávnění, naleznete [v tématu Úvodní příručka: Konfigurace klientské aplikace pro přístup k webovým rozhraním API](quickstart-configure-app-access-web-apis.md).

Starší verze prostředí mělo tlačítko **Oprávnění Grant** v horní části stránky **Požadovaná oprávnění.** V novém prostředí má stránka **Udělení souhlasu** tlačítko **Udělit souhlas správce** v části oprávnění rozhraní **API** aplikace. Existují také některé rozdíly ve způsobech, jakými tlačítka fungují.

Ve staršíverzi prostředí logika se lišila v závislosti na přihlášeného uživatele a požadovaných oprávněních. Logika byla:

- Pokud byla požadována pouze oprávnění s oprávněními uživatele s oprávněním k souhlasu a přihlášený uživatel nebyl správcem, uživatel by mohl udělit souhlas uživatele s požadovanými oprávněními.
- Pokud bylo požadováno alespoň jedno oprávnění, které vyžaduje souhlas správce a přihlášený uživatel nebyl správcem, došlo při pokusu o udělení souhlasu k chybě.
- Pokud byl přihlášený uživatel správcem, byl udělen souhlas správce pro všechna požadovaná oprávnění.

V novém prostředí může souhlas udělit pouze správce. Když správce vybere **Udělit souhlas správce**, souhlas správce se udělí všem požadovaným oprávněním.

## <a name="deleting-an-app-registration"></a>Odstranění registrace aplikace

Ve starších verzích prostředí můžete odstranit pouze aplikace s jedním tenantem. Tlačítko odstranit bylo zakázáno pro aplikace s více klienty. V novém prostředí můžete odstranit aplikace v libovolném stavu, ale musíte akci potvrdit. Další informace naleznete [v tématu Úvodní příručka: Odebrání aplikace registrované na platformě identit společnosti Microsoft](quickstart-remove-app.md).

## <a name="application-manifest"></a>Manifest aplikace

Starší a nové prostředí používají různé verze pro formát JSON v editoru manifestu. Další informace najdete v [tématu Manifest aplikace Azure Active Directory](reference-app-manifest.md).

## <a name="new-ui"></a>Nové ui

Nové prostředí přidá ovládací prvky uživatelského rozhraní pro následující vlastnosti:

- Ověřovací **Authentication** stránka obsahuje **implicitní tok udělení** (`oauth2AllowImplicitFlow`). Na rozdíl od staršíverze prostředí můžete povolit **tokeny aplikace Access** nebo **tokeny ID**nebo obojí.
- Stránka **Vystavit rozhraní API** obsahuje **obory definované tímto rozhraním API** (`oauth2Permissions`) a **autorizované klientské aplikace** (`preAuthorizedApplications`). Další informace o konfiguraci aplikace jako webového rozhraní API a vystavení oprávnění/oborů naleznete v [tématu Úvodní příručka: Konfigurace aplikace pro vystavení webových rozhraní API](quickstart-configure-app-expose-web-apis.md).
- Stránka **Branding** obsahuje **doménu Publisher**. Doména vydavatele se uživatelům zobrazí na výzvu k [souhlasu aplikace](application-consent-experience.md). Další informace naleznete v [tématu Postup: Konfigurace domény vydavatele aplikace](howto-configure-publisher-domain.md).

## <a name="limitations"></a>Omezení

Nové prostředí má následující omezení:

- Formát tajných kódů klienta (hesla aplikací) se liší od starších verzí prostředí a může přerušit rozhraní příkazového příkazu.
- Změna hodnoty podporovaných účtů není v uživatelském uživatelském uživatelském mase podporována. Manifest aplikace musíte použít, pokud nepřepínáte mezi azure ad single tenant a multi-tenant.
