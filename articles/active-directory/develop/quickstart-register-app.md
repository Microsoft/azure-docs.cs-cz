---
title: Registrace aplikace na platformě Microsoft Identity Platform – Microsoft Identity Platform
description: Zjistěte, jak přidat a zaregistrovat aplikaci na platformě Microsoft Identity Platform.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/09/2019
ms.author: ryanwi
ms.custom: aaddev, identityplatformtop40
ms.reviewer: aragra, lenalepa, sureshja
ms.collection: M365-identity-device-management
ms.openlocfilehash: 329e4b262731ae1b23fb94da7b937c9c1549185f
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73473621"
---
# <a name="quickstart-register-an-application-with-the-microsoft-identity-platform"></a>Rychlý Start: registrace aplikace s platformou Microsoft identity

Podnikoví vývojáři a poskytovatelé softwaru jako služby (SaaS) můžou vyvíjet komerční cloudové služby nebo obchodní aplikace s možností integrace s platformou Microsoft Identity Platform za účelem zajištění zabezpečeného přihlašování a autorizace pro služby.

V tomto rychlém startu se dozvíte, jak přidat a zaregistrovat aplikaci pomocí **registrace aplikacího** prostředí v Azure Portal tak, aby bylo možné aplikaci integrovat s platformou Microsoft identity. Další informace o nových funkcích a vylepšeních prostředí pro registraci nových aplikací najdete v [tomto blogovém příspěvku](https://developer.microsoft.com/graph/blogs/new-app-registration/).

## <a name="register-a-new-application-using-the-azure-portal"></a>Registrace nové aplikace pomocí portálu Azure Portal

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účtu Microsoft.
1. Pokud váš účet umožňuje přístup k více tenantům, vyberte svůj účet v pravém horním rohu a nastavte relaci portálu na požadovaného tenanta Azure AD.
1. Vyhledejte a vyberte **Azure Active Directory**. Na stránce **Služba Active Directory** vyberte možnost **Registrace aplikací** a pak vyberte možnost **Nová registrace**.
1. Když se zobrazí stránka **Registrace aplikace**, zadejte registrační informace vaší aplikace:

   - **Název** – Zadejte smysluplný název aplikace, který se zobrazí uživatelům aplikace.
   - **Podporované typy účtu** – Vyberte účty, které chcete, aby vaše aplikace podporovala.

       | Podporované typy účtu | Popis |
       |-------------------------|-------------|
       | **Účty jen v tomto organizačním adresáři** | Tuto možnost vyberte, pokud vytváříte obchodní aplikaci. Tato možnost není dostupná, pokud neregistrujete aplikaci v adresáři.<br><br>Tato možnost se mapuje pouze na účty Azure AD s jedním tenantem.<br><br>Tato možnost je výchozí, pokud neregistrujete aplikaci mimo adresář. V případech, kdy je aplikace zaregistrovaná mimo adresář, jsou výchozí možností účty Azure AD s více tenanty a osobní účty Microsoft. |
       | **Účty v libovolném organizačním adresáři** | Tuto možnost vyberte, pokud chcete cílit na všechny zákazníky z řad firem a vzdělávacích institucí.<br><br>Tato možnost se mapuje pouze na účty Azure AD s více tenanty.<br><br>Pokud jste při registraci aplikace použili možnost s účty Azure AD pouze s jedním tenantem, v okně **Ověřování** ji můžete převést na účet Azure AD s více tenanty a zpět na účet Azure AD s jedním tenantem. |
       | **Účty v libovolném organizačním adresáři a osobní účty Microsoft** | Tuto možnost vyberte, pokud chcete cílit na co nejširší okruh zákazníků.<br><br>Tato možnost se mapuje na účty Azure AD s více tenanty a osobní účty Microsoft.<br><br>Pokud jste při registraci aplikace použili možnost s účty Azure AD s více tenanty a osobní účty Microsoft, nemůžete to v uživatelském rozhraní změnit. Místo toho musíte ke změně podporovaných typů účtu použít editor manifestu aplikace. |

   - **Identifikátor URI pro přesměrování (volitelné)** – Vyberte typ vytvářené aplikace: **Web** nebo **Veřejný klient (mobilní a desktopová zařízení)** . Pak pro vaši aplikaci zadejte identifikátor URI pro přesměrování (neboli adresu URL odpovědi).
       - V případě webových aplikací zadejte základní adresu URL vaší aplikace. Například `https://localhost:31544` může být adresa URL pro webovou aplikaci spuštěnou na místním počítači. Uživatelé by se pomocí této adresy URL přihlašovali k webové klientské aplikaci.
       - V případě veřejných klientských aplikací zadejte identifikátor URI, který Azure AD použije k vrácení odpovědí týkajících se tokenu. Zadejte konkrétní hodnotu pro vaši aplikaci, například `myapp://auth`.

     Konkrétní příklady webových nebo nativních aplikací najdete v našich [rychlých startech](https://docs.microsoft.com/azure/active-directory/develop).

1. Až budete hotovi, vyberte **Zaregistrovat**.

    [![zobrazuje obrazovku pro registraci nové aplikace v Azure Portal](./media/quickstart-add-azure-ad-app-preview/new-app-registration-expanded.png)](./media/quickstart-add-azure-ad-app-preview/new-app-registration-expanded.png#lightbox)

Azure AD vaší aplikaci přiřadí jedinečné ID aplikace (klienta) a přesměruje vás na stránku **Přehled** vaší aplikace. Pokud chcete do své aplikace přidat další funkce, můžete vybrat další možnosti konfigurace, jako jsou branding, certifikáty a tajné klíče, oprávnění rozhraní API a další.

[![příklad stránky s přehledem nově registrované aplikace](./media/quickstart-add-azure-ad-app-preview/new-app-overview-page-expanded.png)](./media/quickstart-add-azure-ad-app-preview/new-app-overview-page-expanded.png#lightbox)

## <a name="next-steps"></a>Další kroky

- Přečtěte si o [oprávněních a souhlasu](v2-permissions-and-consent.md).
- Pokud chcete povolit další funkce konfigurace v registraci aplikace, jako jsou přihlašovací údaje a oprávnění, a povolit přihlašování uživatelů z jiných tenantů, projděte si rychlé starty:
    - [Konfigurace klientské aplikace pro přístup k webovým rozhraním API](quickstart-configure-app-access-web-apis.md)
    - [Konfigurace aplikace pro zveřejnění webových rozhraní API](quickstart-configure-app-expose-web-apis.md)
    - [Úprava účtů podporovaných aplikací](quickstart-modify-supported-accounts.md)
- Vyberte si [rychlý start](https://docs.microsoft.com/azure/active-directory/develop), který vám pomůže rychle vytvořit aplikaci a přidat funkce, jako jsou získání tokenů, aktualizace tokenů, přihlášení uživatele, zobrazení některých informací o uživateli a další.
- Další informace o dvou objektech Azure AD, které představují zaregistrovanou aplikaci, a vztahu mezi nimi, najdete v článku o [objektech aplikací a instančních objektech](app-objects-and-service-principals.md).
- Další informace o pokynech pro branding, kterými byste se měli řídit při vývoji aplikací, najdete v článku [Pokyny pro branding aplikací](howto-add-branding-in-azure-ad-apps.md).
