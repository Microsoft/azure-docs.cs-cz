---
title: Úprava účtů podporovaných aplikací zaregistrované na platformě Microsoft Identity Platform |Azure
description: Konfigurací aplikace zaregistrované na platformě Microsoft Identity Platform můžete změnit, kdo (jaké účty) může k aplikaci přistupovat.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/25/2018
ms.author: celested
ms.custom: aaddev
ms.reviewer: lenalepa, sureshja
ms.openlocfilehash: 2d10dcde0a0e35a3e717cde34048437b6119fbf6
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/28/2019
ms.locfileid: "55101224"
---
# <a name="quickstart-modify-the-accounts-supported-by-an-application-preview"></a>Rychlý start: Úpravy účtů, které podporuje aplikace (Preview)

Při registraci aplikace na platformě Microsoft Identity Platform možná budete chtít svou aplikaci zpřístupnit pouze uživatelům ve vaší organizaci. Alternativně můžete chtít aplikaci zpřístupnit také uživatelům v externích organizacích nebo uživatelům v externích organizacích i uživatelům, kteří nutně nejsou součástí žádné organizace (osobní účty).

V tomto rychlém startu se dozvíte, jak úpravou konfigurace vaší aplikace změnit, kdo (jaké účty) může k aplikaci přistupovat.

## <a name="prerequisites"></a>Požadavky

Než začnete, musíte splnit následující požadavky:

* Přečtěte si o podporovaných [oprávněních a souhlasu](v2-permissions-and-consent.md), kterým je důležité rozumět při vytváření aplikací, které budou používat jiní uživatelé či jiné aplikace.
* Máte tenanta, ke kterému jsou zaregistrované aplikace.
  * Pokud nemáte žádné zaregistrované aplikace, [přečtěte si o registraci aplikací na platformě Microsoft Identity Platform](quickstart-register-app.md).
* Na webu Azure Portal jste se přihlásili k prostředí pro registrace aplikací ve verzi Preview. Kroky v tomto rychlém startu odpovídají novému uživatelskému rozhraní a fungují pouze v případě, že jste se přihlásili k prostředí ve verzi Preview.

## <a name="sign-in-to-the-azure-portal-and-select-the-app"></a>Přihlášení k webu Azure Portal a výběr aplikace

Než budete moct nakonfigurovat aplikaci, postupujte podle těchto kroků:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účtu Microsoft.
1. Pokud váš účet umožňuje přístup k více tenantům, vyberte svůj účet v pravém horním rohu a nastavte relaci portálu na požadovaného tenanta Azure AD.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** a pak **Registrace aplikací (Preview)**.
1. Vyhledejte a vyberte aplikaci, kterou chcete nakonfigurovat. Jakmile vyberete aplikaci, zobrazí se stránka **Přehled** neboli hlavní stránka registrace dané aplikace.
1. Postupujte podle pokynů a [změňte registraci aplikace tak, aby podporovala různé účty](#change-the-application-registration-to-support-different-accounts).
1. Pokud máte jednostránkovou aplikaci, [povolte implicitní udělení OAuth 2.0](#enable-oauth-20-implicit-grant-for-single-page-applications).

## <a name="change-the-application-registration-to-support-different-accounts"></a>Změna registrace aplikace pro podporu různých účtů

Pokud píšete aplikaci, kterou chcete zpřístupnit zákazníkům nebo partnerům mimo organizaci, musíte na webu Azure Portal aktualizovat definici aplikace.

> [!IMPORTANT]
> Azure AD vyžaduje, aby identifikátor URI ID aplikací s více tenanty byl globálně jedinečný. Identifikátor URI ID aplikace je jedním ze způsobů, kterými se může aplikace ve zprávách protokolu identifikovat. U aplikace s jedním tenantem stačí, když bude identifikátor URI ID aplikace jedinečný v rámci daného tenanta. U aplikace s více tenanty musí být globálně jedinečný, aby služba Azure AD aplikaci našla mezi všemi tenanty. Globální jedinečnost se vynucuje požadavkem, aby Identifikátor URI ID aplikace obsahoval název hostitele, který odpovídá ověřené doméně tenanta Azure AD. Když je například název tenanta contoso.onmicrosoft.com, pak by platným identifikátorem URI ID aplikace byl https://contoso.onmicrosoft.com/myapp. Pokud má tenant ověřenou doménu contoso.com, pak by platným identifikátorem URI ID aplikace byl i https://contoso.com/myapp. Pokud identifikátor URI ID aplikace nepoužívá tento vzor, nastavení aplikace jako aplikace s více tenanty se nezdaří.

### <a name="to-change-who-can-access-your-application"></a>Změna toho, kdo má přístup k aplikaci

1. Na stránce **Přehled** aplikace vyberte část **Ověřování** a změňte vybranou hodnotu v části **Podporované typy účtu**.
    * Pokud vytváříte obchodní aplikaci, vyberte **Účty jen v tomto adresáři**. Tato možnost není dostupná, pokud aplikace není zaregistrovaná v adresáři.
    * Pokud chcete cílit na všechny zákazníky z řad firem a vzdělávacích institucí, vyberte **Účty v libovolném organizačním adresáři**.
    * Pokud chcete cílit na co nejširší okruh zákazníků, vyberte **Účty v libovolném organizačním adresáři a osobní účty Microsoft**.
1. Vyberte **Uložit**.

## <a name="enable-oauth-20-implicit-grant-for-single-page-applications"></a>Povolení implicitního udělení OAuth 2.0 u jednostránkových aplikací

Jednostránkové aplikace (SPA) mají obvykle strukturu front-endu se spoustou JavaScriptu běžícího v prohlížeči, který volá back-end aplikace webového rozhraní API, aby provedl obchodní logiku. U jednostránkových aplikací hostovaných v Azure AD se používá implicitní udělení OAuth 2.0 k ověření uživatele pomocí Azure AD a získání tokenu, který můžete použít k zabezpečení volání z javascriptového klienta aplikace do back-endu webového rozhraní API.

Jakmile uživatel udělí souhlas, může se stejný protokol ověřování použít k získání tokenů, aby se zabezpečila volání mezi klientem a jinými prostředky webového rozhraní API nakonfigurovanými pro danou aplikaci. Další informace o implicitním udělení autorizace a pomoc s rozhodnutím, zda je implicitní udělení autorizace pro váš scénář vhodné, najdete v článcích o toku implicitního udělení OAuth 2.0 v Azure AD [v1.0](v1-oauth2-implicit-grant-flow.md) a [v2.0](v2-oauth2-implicit-grant-flow.md).

Ve výchozím nastavení je implicitní udělení OAuth 2.0 u aplikací zakázané. Implicitní udělení OAuth 2.0 můžete u své aplikace povolit podle níže uvedeného postupu.

### <a name="to-enable-oauth-20-implicit-grant"></a>Povolení implicitního udělení OAuth 2.0

1. Na stránce **Přehled** aplikace vyberte část **Ověřování**.
1. V části **Upřesnit nastavení** vyhledejte část **Implicitní udělení**.
1. Vyberte **Tokeny ID**, **Přístupové tokeny** nebo obojí.
1. Vyberte **Uložit**.

## <a name="next-steps"></a>Další postup

Další informace najdete v těchto rychlých startech souvisejících se správou aplikací:

* [Registrace aplikace na platformě Microsoft Identity Platform](quickstart-register-app.md)
* [Konfigurace klientské aplikace pro přístup k webovým rozhraním API](quickstart-configure-app-access-web-apis.md)
* [Konfigurace aplikace pro zveřejnění webových rozhraní API](quickstart-configure-app-expose-web-apis.md)
* [Odebrání aplikace zaregistrované na platformě Microsoft Identity Platform](quickstart-remove-app.md)

Další informace o dvou objektech Azure AD, které představují zaregistrovanou aplikaci, a vztahu mezi nimi, najdete v článku o [objektech aplikací a instančních objektech](app-objects-and-service-principals.md).

Další informace o pokynech pro branding, kterými byste se měli řídit při vývoji aplikací s využitím Azure Active Directory, najdete v článku [Pokyny pro branding aplikací](howto-add-branding-in-azure-ad-apps.md).
