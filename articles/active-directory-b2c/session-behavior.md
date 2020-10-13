---
title: Konfigurace chování relace – Azure Active Directory B2C | Microsoft Docs
description: Naučte se konfigurovat chování relace v Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 10/12/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 091704fabb7b50a0c83625c6ae46d9a807f01ffc
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/13/2020
ms.locfileid: "91961030"
---
# <a name="configure-session-behavior-in-azure-active-directory-b2c"></a>Konfigurace chování relace v Azure Active Directory B2C

Správa [relací jednotného přihlašování (SSO)](session-overview.md) v Azure Active Directory B2C (Azure AD B2C) umožňuje správci řídit interakci s uživatelem poté, co uživatel již ověřený. Správce může například řídit, jestli se zobrazuje výběr zprostředkovatelů identity, nebo jestli je potřeba zadat podrobnosti o účtu znovu. Tento článek popisuje, jak nakonfigurovat nastavení jednotného přihlašování pro Azure AD B2C.

## <a name="session-behavior-properties"></a>Vlastnosti chování relace

Pomocí následujících vlastností můžete spravovat relace webových aplikací:

- **Doba života relace webové aplikace (minuty)** – doba života souboru cookie relace Azure AD B2C's, která se po úspěšném ověření uloží v prohlížeči uživatele.
    - Výchozí hodnota = 1440 minut.
    - Minimální (včetně) = 15 minut.
    - Maximum (včetně) = 1440 minut.
- **Časový limit relace webové aplikace** – [Typ vypršení platnosti relace](session-overview.md#session-expiry-type), *návratová*nebo *absolutní*. 
- **Konfigurace jednotného přihlašování** – [rozsah relace](session-overview.md#session-scope) chování jednotného přihlašování (SSO) v rámci více aplikací a toků uživatelů ve vašem Azure AD B2C klientovi.


## <a name="configure-the-properties"></a>Konfigurace vlastností

1. Přihlaste se k [portálu Azure Portal](https://portal.azure.com).
2. Ujistěte se, že používáte adresář, který obsahuje Azure AD B2C tenanta, a to tak, že v horní nabídce vyberete adresář a filtr **předplatného** a vyberete adresář, který obsahuje vašeho Azure AD B2Cho tenanta.
3. V levém horním rohu Azure Portal vyberte **všechny služby** a pak vyhledejte a vyberte **Azure AD B2C**.
4. Vyberte **toky uživatelů**.
5. Otevřete uživatelský tok, který jste vytvořili dříve.
6. Vyberte **Vlastnosti**.
7. Nakonfigurujte **dobu života relace webové aplikace (minuty)**, **časový limit relace webové aplikace**, **konfiguraci jednotného přihlašování**a **v případě potřeby vyžadovat token ID v žádostech o odhlášení** .

    ![Nastavení vlastností chování relace v Azure Portal](./media/session-behavior/session-behavior.png)

8. Klikněte na **Uložit**.

## <a name="configure-sign-out-behavior"></a>Konfigurace chování při odhlašování

### <a name="secure-your-logout-redirect"></a>Zabezpečení přesměrování odhlášení

Po odhlášení se uživatel přesměruje na identifikátor URI zadaný v `post_logout_redirect_uri` parametru, a to bez ohledu na adresy URL odpovědi, které byly pro aplikaci zadané. Pokud je ale `id_token_hint` předána platná a **token vyžadovat ID v žádostech o odhlášení** je zapnutý, Azure AD B2C ověří, že hodnota `post_logout_redirect_uri` odpovídá jednomu z nakonfigurovaných identifikátorů URI pro přesměrování aplikace před provedením přesměrování. Pokud pro aplikaci nebyla nakonfigurovaná žádná odpovídající adresa URL odpovědi, zobrazí se chybová zpráva a uživatel nebude přesměrován. Vyžadování tokenu ID v žádostech o odhlášení:

1. Přihlaste se k [portálu Azure Portal](https://portal.azure.com).
1. Ujistěte se, že používáte adresář, který obsahuje Azure AD B2C tenanta, a to tak, že v horní nabídce vyberete adresář a filtr **předplatného** a vyberete adresář, který obsahuje vašeho Azure AD B2Cho tenanta.
1. V levém horním rohu Azure Portal vyberte **všechny služby** a pak vyhledejte a vyberte **Azure AD B2C**.
1. Vyberte **toky uživatelů**.
1. Otevřete uživatelský tok, který jste vytvořili dříve.
1. Vyberte **Vlastnosti**.
1. **V žádostech o odhlášení povolte token vyžadovat ID**.
1. Vraťte se na  **Azure AD B2C**.
1. Vyberte **Registrace aplikací**a pak vyberte svou aplikaci.
1. Vyberte **Ověřování**.
1. Do textového pole **odhlašovací adresa URL** zadejte identifikátor URI přesměrování po odhlášení a pak vyberte **Uložit**.

## <a name="next-steps"></a>Další kroky

- Přečtěte si další informace o [Azure AD B2C relaci](session-overview.md).
