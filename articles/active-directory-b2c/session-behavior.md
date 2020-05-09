---
title: Konfigurace chování relace – Azure Active Directory B2C | Microsoft Docs
description: Nakonfigurujte chování relace v Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 05/07/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: f5400b47c1e0b4657e40d2c57f8212711bbdaf3f
ms.sourcegitcommit: a6d477eb3cb9faebb15ed1bf7334ed0611c72053
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/08/2020
ms.locfileid: "82927067"
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

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Ujistěte se, že používáte adresář, který obsahuje Azure AD B2C tenanta, a to tak, že v horní nabídce vyberete adresář a filtr **předplatného** a vyberete adresář, který obsahuje vašeho Azure AD B2Cho tenanta.
3. V levém horním rohu Azure Portal vyberte **všechny služby** a pak vyhledejte a vyberte **Azure AD B2C**.
4. Vyberte **toky uživatelů (zásady)**.
5. Otevřete uživatelský tok, který jste vytvořili dříve.
6. Vyberte **Vlastnosti**.
7. Nakonfigurujte **dobu života relace webové aplikace (minuty)**, **časový limit relace webové aplikace**, **konfiguraci jednotného přihlašování**a **v případě potřeby vyžadovat token ID v žádostech o odhlášení** .

    ![Nastavení vlastností chování relace v Azure Portal](./media/session-behavior/session-behavior.png)

8. Klikněte na **Uložit**.

## <a name="next-steps"></a>Další kroky

- Přečtěte si další informace o [Azure AD B2C relaci](session-overview.md).