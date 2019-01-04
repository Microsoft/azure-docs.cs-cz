---
title: Předat token přístupu přes tok uživatele do vaší aplikace v Azure Active Directory B2C | Dokumentace Microsoftu
description: Zjistěte, jak lze předat prostřednictvím přístupového tokenu pro zprostředkovatele identity OAuth 2.0 jako deklarace identity v toku uživatele v Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/28/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: b26605bd0b436d948fb1f62cbf32a17ea4f386d0
ms.sourcegitcommit: 4eeeb520acf8b2419bcc73d8fcc81a075b81663a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/19/2018
ms.locfileid: "53602252"
---
# <a name="pass-an-access-token-through-a-user-flow-to-your-application-in-azure-active-directory-b2c"></a>Předat token přístupu přes tok uživatele do vaší aplikace v Azure Active Directory B2C

> [!NOTE]
> Tato funkce je aktuálně ve verzi public preview.

> [!Important]
> Tato funkce ve verzi public preview je dočasně nedostupný.

A [tok uživatele](active-directory-b2c-reference-policies.md) v Azure Active Directory (Azure AD) B2C poskytuje uživatelům vaší aplikace příležitost k registraci nebo přihlášení pomocí zprostředkovatele identity. Při spuštění cesty, obdrží Azure AD B2C [přístupový token](active-directory-b2c-reference-tokens.md) od zprostředkovatele identity. Azure AD B2C používá tento token k načtení informací o uživateli. Povolte deklaraci v toku uživatele a předat token prostřednictvím aplikace, které zaregistrujete v Azure AD B2C.

Azure AD B2C momentálně podporuje pouze předávání přístupový token [OAuth 2.0](active-directory-b2c-reference-oauth-code.md) zprostředkovatelů identity, mezi které patří [Facebook](active-directory-b2c-setup-fb-app.md) a [Google](active-directory-b2c-setup-goog-app.md). Pro všechny ostatní poskytovatele identity se vrátí prázdná deklarace identity.

## <a name="prerequisites"></a>Požadavky

- Aplikace musí používat [tok uživatele v2](user-flow-versions.md).
- Váš tok uživatele je nakonfigurovaný pomocí zprostředkovatele identity OAuth 2.0.

## <a name="enable-the-claim"></a>Povolit deklarace identity

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/) jako globální správce vašeho tenanta Azure AD B2C.
2. Ujistěte se, že používáte adresáře, který obsahuje vašeho tenanta Azure AD B2C kliknutím **filtr adresářů a předplatných** v horní nabídce a výběrem adresáře, který obsahuje váš tenant.
3. Zvolte **Všechny služby** v levém horním rohu portálu Azure Portal a vyhledejte a vyberte **Azure AD B2C**.
4. Vyberte **toky uživatelů**a pak vyberte tok uživatele. Například **B2C_1_SignupSignIn**.
5. Vyberte **Deklarace identit aplikace**.
6. Povolit **přístupový Token zprostředkovatele Identity**.

    ![Deklarace identity aplikace](./media/idp-pass-through-user-flow/idp-pass-through-user-flow-app-claim.png)

7. Klikněte na tlačítko **Uložit** uložit tok uživatele.

## <a name="test-the-user-flow"></a>Testování tohoto toku uživatele

Při testování aplikací v Azure AD B2C, může být užitečné mít tokenu Azure AD B2C vrátí `https://jwt.ms` zkontrolovat deklarace identity v ní.

1. Na stránce Přehled toku uživatele, vyberte **spustit tok uživatele**.
2. Pro **aplikace**, vyberte aplikace, které jste dříve zaregistrovali. Chcete-li zobrazit token v následujícím příkladu **adresy URL odpovědi** by se zobrazit `https://jwt.ms`.
3. Klikněte na tlačítko **spustit tok uživatele**a pak se přihlaste pomocí přihlašovacích údajů k účtu. Měli byste vidět zprostředkovatele identity v tokenu přístupu **idp_access_token** deklarací identity.

    By měl vypadat podobně jako v následujícím příkladu:

    ![Dekódovaný token](./media/idp-pass-through-user-flow/idp-pass-through-user-flow-token.png)

## <a name="next-steps"></a>Další postup

Další informace o tokeny v [odkaz tokenu Azure Active Directory](active-directory-b2c-reference-tokens.md).




