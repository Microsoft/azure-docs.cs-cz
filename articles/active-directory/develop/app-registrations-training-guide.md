---
title: Registrace aplikace v Průvodci školení pro Azure portal – Azure
description: Vytváření toků vložené a prohlížeče bez ověřování pomocí udělení kódu zařízení.
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
ms.openlocfilehash: 118c6ecb16d325a384246a0b3d9e685f6f6f04ee
ms.sourcegitcommit: e7d4881105ef17e6f10e8e11043a31262cfcf3b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2019
ms.locfileid: "64870114"
---
# <a name="training-guide-app-registrations-in-the-azure-portal"></a>Příručka pro vzdělávání: Registrace aplikací na webu Azure Portal  

Můžete najít četná vylepšení v novém [registrace aplikací](https://go.microsoft.com/fwlink/?linkid=2083908) prostředí na webu Azure Portal. Pokud jste více do hloubky ve starší verzi, použijte tuto příručku školení vám pomůžou začít používat nové prostředí.

## <a name="key-changes"></a>Změní klíč

- Registrace aplikací nejsou omezené na buď **webové aplikace nebo rozhraní API** nebo **nativní** aplikace. Můžete použít stejné registrace aplikace pro všechny z nich tak, že zaregistrujete odpovídající identifikátory URI pro přesměrování.
- Starší verze prostředí podporované aplikace, s možností přihlášení organizace (Azure AD) účtů jenom. Aplikace byla registrována jako jednoho tenanta (podporují pouze účty z adresáře aplikace byl zaregistrován do) a může upravit tak, aby se více tenantů (podporují všechny účty organizace). Nové prostředí umožňuje registrovat aplikace, které může podporovat obě tyto možnosti, stejně jako třetí možnost: všechny účty organizace i osobní účty Microsoft.
- Starší verze prostředí se pouze k dispozici při přihlášení k webu Azure portal pomocí účtu organizace. Pomocí tohoto nového prostředí můžete použít osobní účty Microsoft, které nejsou přiřazeny k adresáři.

## <a name="list-of-applications"></a>Seznam aplikací

- Nový seznam aplikace zobrazí aplikace, které byly zaregistrovány prostřednictvím starší verze aplikace prostředí registrace v portálu Azure portal (aplikace, které v účtech služby Azure AD), stejně jako aplikace zaregistrované, ale [portál pro registraci aplikací](https://apps.dev.microsoft.com/) (aplikace, které ve službě Azure AD a osobních účtů Microsoft).
- Nového seznamu aplikace nemá **typ aplikace** sloupec (protože aplikace s jedním registrace může být několik typů) a obsahuje další dva sloupce: **vytvořené na** sloupce a **certifikáty & tajných kódů** sloupec, který se zobrazuje stav (aktuální, zanedlouho vyprší platnost nebo platnost) přihlašovacích údajů, které jsou zaregistrovány v aplikaci.

## <a name="new-app-registration"></a>Registrace nové aplikace

Ve starší verzi postup pro registraci aplikace budete se musí provést: **Název**, **typ aplikace**, a **přihlašovací adresa URL nebo identifikátor URI pro přesměrování**. Aplikace, které byly vytvořeny byly to znamená, že pouze podporované organizační účty z adresáře, který aplikace byl zaregistrován do aplikace pouze jednoho tenanta Azure AD.

V novém prostředí, je nutné zadat **název** pro aplikaci a zvolte **podporovaných typů účtu**. Volitelně můžete zadat **identifikátor URI pro přesměrování**. Pokud zadáte identifikátor URI přesměrování, bude nutné zadat, pokud je web/veřejný (mobilních a desktopových). Další informace o tom, jak zaregistrovat aplikaci pomocí registrace nové aplikace prostředí najdete v tématu [v tomto rychlém startu](quickstart-register-app.md).

## <a name="the-legacy-properties-page"></a>Starší verze stránky vlastností

Má starší verze prostředí **vlastnosti** stránku, která nemá nové prostředí. **Vlastnosti** okno měl následující pole: **Název**, **ID objektu**, **ID aplikace**, **identifikátor ID URI aplikace**, **Logo**, **adresa URL domovské stránky** , **Odhlašovací adresa URL**, **adresa URL podmínek služby**, **adresa URL prohlášení o ochraně osobních údajů**, **typ aplikace**, a  **Více tenantů.**

Tady je místo, kde najdete ekvivalentní funkce v novém prostředí:

- **Název**, **Logo**, **adresa URL domovské stránky**, **adresa URL podmínek služby**, a **adresa URL prohlášení o ochraně osobních údajů** je teď na aplikace  **Branding** stránky.
- **ID objektu** a **ID aplikace (klient)** nachází **přehled** stránky.
- Řídí funkce **víceklientské** přepínací tlačítko ve starší verzi nahradila ji **podporovaných typů účtu** na **ověřování** stránky. Další informace o způsobu mapování více tenantů pro podporované typy účtů najdete v tématu [v tomto rychlém startu](quickstart-modify-supported-accounts.md).
- **Odhlašovací adresa URL** je teď na **ověřování** stránky.
- **Typ aplikace** už není platné pole. Místo toho identifikátory URI pro přesměrování (což můžete najít na **ověřování** stránky) určit typy, které aplikaci podporují.
- **Identifikátor URI ID aplikace** se teď nazývá **identifikátor URI ID aplikace** a najdete ji na **vystavit rozhraní API** okno. Ve starší verzi této vlastnosti byl automaticky – zaregistrovaný v následujícím formátu: `https://{tenantdomain}/{appID}` (například `https://microsoft.onmicrosoft.com/aeb4be67-a634-4f20-9a46-e0d4d4f1f96d`). V novém formátu, je automaticky generována jako `api://{appID}`, ale je potřeba explicitně uložit.

## <a name="reply-urlsredirect-urls"></a>Adresy URL nebo přesměrovat adresy URL odpovědí

Ve starší verzi aplikace měl **adresy URL odpovědí** stránky. V novém prostředí Odpovědních adres URL můžete najít na vaší aplikace **ověřování** oddílu. Kromě toho, že jsou označovány jako **identifikátory URI přesměrování**. Kromě toho formát pro přesměrování identifikátory URI změnil. Jsou vyžadovány k typu aplikace (web nebo veřejný). Kromě toho z bezpečnostních důvodů nejsou podporované zástupné znaky a http:// schémata (s výjimkou produktů http://localhost).

## <a name="keyscertificates--secrets"></a>Certifikáty klíče a tajné kódy

Ve starší verzi aplikace měl **klíče** stránky. V novém prostředí byl přejmenován na **certifikáty a tajné kódy**. Kromě toho **veřejných klíčů** jsou označovány jako **certifikáty** a **hesla** jsou označovány jako **tajné klíče klienta**.

## <a name="required-permissionsapi-permissions"></a>Požadovaná oprávnění a oprávnění rozhraní API

- Ve starší verzi aplikace měl **požadovaná oprávnění** stránky. V novém prostředí byl přejmenován na **oprávnění k rozhraní API**.
- Při výběru rozhraní API ve starší verzi, může si vybrat z jednom malém seznamu Microsoft APIs nebo prohledejte instanční objekty v tenantovi. V novém prostředí můžete z více karet: **Microsoft APIs**, **naše organizace bude využívat rozhraní API**, nebo **Moje rozhraní API**. Na panelu hledání na **rozhraní API mojí organizace** používá kartu hledá v rámci instanční objekty v tenantovi. 

   > [!NOTE]
   > Na této kartě se nezobrazí, pokud vaše aplikace není přidružené ke klientovi. Další informace o tom, jak požádat o oprávnění pomocí tohoto nového prostředí najdete v tématu [v tomto rychlém startu](quickstart-configure-app-access-web-apis.md).

- Má starší verze prostředí **udělit oprávnění** tlačítko v horní části **požadovaná oprávnění** stránky. V novém prostředí, je **udělit souhlas** části s **udělit souhlas správce** tlačítko v aplikaci prvku **oprávnění k rozhraní API** oddílu. Kromě toho existují určité rozdíly v způsobů, jak funkce tlačítka:
   - Ve starší verzi logiku měnit v závislosti na přihlášeného uživatele a žádá oprávnění. Logika byl:
      - Pokud pouze byly žádá oprávnění možnost pro vyjádření souhlasu uživatele a přihlášený uživatel nebyl správcem, uživatel byl schopen udělit souhlas uživatele požadovaná oprávnění.
      - Pokud se požaduje aspoň jedno oprávnění, která vyžaduje souhlas správce a přihlášeného uživatele nebyl správcem, uživatel je teď k chybě při pokusu o udělení souhlasu.
      - Pokud je přihlášený uživatel správcem, udělení souhlasu správce pro všechna požadovaná oprávnění.
   - V novém prostředí pouze správce může udělit souhlas. Pokud správce vybere **udělit souhlas správce** tlačítko, udělený souhlas správce všechna požadovaná oprávnění.

## <a name="deleting-an-app-registration"></a>Odstraňuje se registrace aplikace

Ve starší verzi aplikace musela být jednoho tenanta, která se má odstranit. Tlačítko pro odstranění byl zakázán pro aplikace s více tenanty. V novém prostředí aplikace můžete odstranit v libovolném stavu, ale musí potvrďte akci. Další informace o odstranění registrace aplikací najdete v tématu [v tomto rychlém startu](quickstart-remove-app.md).

## <a name="application-manifest"></a>Manifest aplikace

Starší verze a nové prostředí používají různé verze formátu JSON v editoru manifestu. Další informace najdete v tématu [manifest aplikace](reference-app-manifest.md).

## <a name="new-ui"></a>Nové uživatelské rozhraní

Není k dispozici nové uživatelské rozhraní pro vlastnosti, které mohl dříve lze nastavit pouze pomocí editoru manifestu nebo rozhraní API nebo tenkrát neexistovaly.

- **Implicitní tok poskytování** (oauth2AllowImplicitFlow) můžete najít na **ověřování** stránky. Na rozdíl od ve starší verzi můžete povolit **přístupové tokeny** nebo **tokeny typu id**, nebo obojí.
- **Obory definované tímto rozhraním API** (oauth2Permissions) a **klientské aplikace autorizované** (preAuthorizedApplications) je možné nakonfigurovat pomocí **vystavit rozhraní API** stránky. Další informace o tom, jak nakonfigurovat aplikaci webového rozhraní API a vystavit oprávnění/obory, naleznete v tématu [v tomto rychlém startu](quickstart-configure-app-expose-web-apis.md).
- **Doména vydavatele** (na které se zobrazí uživatelům [výzva k povolení spuštění aplikace](application-consent-experience.md)) můžete najít na **značky okno** stránky. Další informace o tom, jak nakonfigurovat doménu vydavatele najdete v tématu [tento návod](howto-configure-publisher-domain.md).

## <a name="limitations"></a>Omezení

Nové prostředí má následující omezení:

- Nové prostředí není momentálně k dispozici v Azure AD B2C tenanti.
- Formát klienta tajných kódů (hesla aplikací) je jiná než starší verze prostředí a přeruší rozhraní příkazového řádku.
- Změna hodnoty u podporovaných účtů se nepodporuje v uživatelském rozhraní. Budete muset použít manifest aplikace, pokud přepínáte mezi službami Azure AD jednoho tenanta a více tenantů.
