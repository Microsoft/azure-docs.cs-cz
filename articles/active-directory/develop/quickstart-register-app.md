---
title: Registrace aplikace na platformě Microsoft Identity Platform (Preview) | Azure
description: Zjistěte, jak přidat a zaregistrovat aplikaci na platformě Microsoft Identity Platform.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/02/2018
ms.author: celested
ms.custom: aaddev
ms.reviewer: lenalepa, sureshja
ms.openlocfilehash: 1809e7f72afd99de38387317daf2ecb92dbe3cf0
ms.sourcegitcommit: d372d75558fc7be78b1a4b42b4245f40f213018c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/09/2018
ms.locfileid: "51298717"
---
# <a name="quickstart-register-an-application-with-the-microsoft-identity-platform-preview"></a>Rychlý start: Registrace aplikace na platformě Microsoft Identity Platform (Preview)

Podnikoví vývojáři a poskytovatelé softwaru jako služby (SaaS) můžou vyvíjet komerční cloudové služby nebo obchodní aplikace s možností integrace s platformou Microsoft Identity Platform za účelem zajištění zabezpečeného přihlašování a autorizace pro služby.

V tomto rychlém startu se dozvíte, jak pomocí prostředí **Registrace aplikací (Preview)** na webu Azure Portal přidat a zaregistrovat aplikaci, aby ji bylo možné integrovat s platformou Microsoft Identity Platform. Další informace o nové funkce a vylepšení v novém prostředí registrace aplikací, najdete v článku [tento příspěvek na blogu](https://developer.microsoft.com/graph/blogs/new-app-registration/). 

## <a name="prerequisite"></a>Požadavek

Abyste mohli začít, musíte se na webu Azure Portal přihlásit k prostředí pro registrace aplikací ve verzi Preview. Kroky v tomto rychlém startu odpovídají novému uživatelskému rozhraní a fungují pouze v případě, že jste se přihlásili k prostředí ve verzi Preview.

## <a name="register-a-new-application-using-the-azure-portal"></a>Registrace nové aplikace pomocí portálu Azure Portal

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účtu Microsoft.
1. Pokud váš účet umožňuje přístup k více tenantům, vyberte svůj účet v pravém horním rohu a nastavte relaci portálu na požadovaného tenanta Azure AD.
1. V levém navigačním podokně vyberte službu **Azure Active Directory** a pak **Registrace aplikací (Preview) > Nová registrace**.
1. Když se zobrazí stránka **Registrace aplikace**, zadejte registrační informace vaší aplikace:

    - **Název** – Zadejte smysluplný název aplikace, který se zobrazí uživatelům aplikace.
    - **Podporované typy účtu** – Vyberte účty, které chcete, aby vaše aplikace podporovala.

        | Podporované typy účtu | Popis |
        |-------------------------|-------------|
        | **Účty jen v tomto organizačním adresáři** | Tuto možnost vyberte, pokud vytváříte obchodní aplikaci. Tato možnost není dostupná, pokud neregistrujete aplikaci v adresáři.<br><br>Tato možnost se mapuje pouze na účty Azure AD s jedním tenantem.<br><br>Tato možnost je výchozí, pokud neregistrujete aplikaci mimo adresář. V případech, kdy je aplikace zaregistrovaná mimo adresář, jsou výchozí možností účty Azure AD s více tenanty a osobní účty Microsoft. |
        | **Účty v libovolném organizačním adresáři** | Tuto možnost vyberte, pokud chcete cílit na všechny zákazníky z řad firem a vzdělávacích institucí.<br><br>Tato možnost se mapuje pouze na účty Azure AD s více tenanty.<br><br>Pokud jste při registraci aplikace použili možnost s účty Azure AD pouze s jedním tenantem, v okně **Ověřování** ji můžete převést na účet Azure AD s více tenanty a zpět na účet Azure AD s jedním tenantem. |
        | **Účty v libovolném organizačním adresáři a osobní účty Microsoft** | Tuto možnost vyberte, pokud chcete cílit na co nejširší okruh zákazníků.<br><br>Tato možnost se mapuje na účty Azure AD s více tenanty a osobní účty Microsoft.<br><br>Pokud jste při registraci aplikace použili možnost s účty Azure AD s více tenanty a osobní účty Microsoft, nemůžete to v uživatelském rozhraní změnit. Místo toho musíte ke změně podporovaných typů účtu použít editor manifestu aplikace. |

    - **Identifikátor URI pro přesměrování (volitelné)** – Vyberte typ vytvářené aplikace: **Web** nebo **Veřejný klient (mobilní a desktopová zařízení)**. Pak pro vaši aplikaci zadejte identifikátor URI pro přesměrování (neboli adresu URL odpovědi).
        - V případě webových aplikací zadejte základní adresu URL vaší aplikace. Například `http://localhost:31544` může být adresa URL pro webovou aplikaci spuštěnou na místním počítači. Uživatelé by se pomocí této adresy URL přihlašovali k webové klientské aplikaci.
        - V případě veřejných klientských aplikací zadejte identifikátor URI, který Azure AD použije k vrácení odpovědí týkajících se tokenu. Zadejte konkrétní hodnotu pro vaši aplikaci, například `myapp://auth`.

    Konkrétní příklady webových nebo nativních aplikací najdete v našich [rychlých startech](https://docs.microsoft.com/azure/active-directory/develop/#quickstarts).

1. Až budete hotovi, vyberte **Zaregistrovat**.

    [![Registrace nové aplikace na webu Azure Portal](./media/quickstart-add-azure-ad-app-preview/new-app-registration-expanded.png)](./media/quickstart-add-azure-ad-app-preview/new-app-registration-expanded.png#lightbox)

Azure AD vaší aplikaci přiřadí jedinečné ID aplikace (klienta) a přesměruje vás na stránku **Přehled** vaší aplikace. Pokud chcete do své aplikace přidat další funkce, můžete vybrat další možnosti konfigurace, jako jsou branding, certifikáty a tajné klíče, oprávnění rozhraní API a další.

[![Stránka Přehled nově zaregistrované aplikace](./media/quickstart-add-azure-ad-app-preview/new-app-overview-page-expanded.png)](./media/quickstart-add-azure-ad-app-preview/new-app-overview-page-expanded.png#lightbox)

## <a name="next-steps"></a>Další postup

- Přečtěte si o [oprávněních a souhlasu](v2-permissions-and-consent.md).
- Pokud chcete povolit další funkce konfigurace v registraci aplikace, jako jsou přihlašovací údaje a oprávnění, a povolit přihlašování uživatelů z jiných tenantů, projděte si rychlé starty:
    - [Konfigurace klientské aplikace pro přístup k webovým rozhraním API](quickstart-configure-app-access-web-apis.md)
    - [Konfigurace aplikace pro zveřejnění webových rozhraní API](quickstart-configure-app-expose-web-apis.md)
    - [Úprava účtů podporovaných aplikací](quickstart-modify-supported-accounts.md)
- Vyberte si [rychlý start](https://docs.microsoft.com/azure/active-directory/develop/#quickstarts), který vám pomůže rychle vytvořit aplikaci a přidat funkce, jako jsou získání tokenů, aktualizace tokenů, přihlášení uživatele, zobrazení některých informací o uživateli a další.
- Další informace o dvou objektech Azure AD, které představují zaregistrovanou aplikaci, a vztahu mezi nimi, najdete v článku o [objektech aplikací a instančních objektech](app-objects-and-service-principals.md).
- Další informace o pokynech pro branding, kterými byste se měli řídit při vývoji aplikací, najdete v článku [Pokyny pro branding aplikací](howto-add-branding-in-azure-ad-apps.md).
