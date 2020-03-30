---
title: Předání přístupového tokenu prostřednictvím toku uživatele do vaší aplikace
titleSuffix: Azure AD B2C
description: Zjistěte, jak předat přístupový token pro poskytovatele identit OAuth 2.0 jako deklaraci identity v toku uživatelů ve službě Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/17/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 312d093548b6e3cf3654f45d7610e8fc474a87b8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78187776"
---
# <a name="pass-an-access-token-through-a-user-flow-to-your-application-in-azure-active-directory-b2c"></a>Předání přístupového tokenu prostřednictvím toku uživatele do vaší aplikace ve službě Azure Active Directory B2C

[Tok uživatelů](user-flow-overview.md) ve službě Azure Active Directory B2C (Azure AD B2C) poskytuje uživatelům vaší aplikace příležitost k registraci nebo přihlášení u poskytovatele identity. Při zahájení cesty Azure AD B2C obdrží [přístupový token](tokens-overview.md) od poskytovatele identity. Azure AD B2C používá tento token k načtení informací o uživateli. Povolíte deklaraci v toku uživatele předat token prostřednictvím aplikací, které zaregistrujete v Azure AD B2C.

Azure AD B2C v současné době podporuje pouze předávání přístupový token [OAuth 2.0](authorization-code-flow.md) zprostředkovatelů identit, které zahrnují [Facebook](identity-provider-facebook.md) a [Google](identity-provider-google.md). Pro všechny ostatní zprostředkovatele identity je deklarace identity vrácena prázdná.

## <a name="prerequisites"></a>Požadavky

* Aplikace musí používat [tok uživatele v2](user-flow-versions.md).
* Tok uživatele je nakonfigurován s poskytovatelem identity OAuth 2.0.

## <a name="enable-the-claim"></a>Povolení deklarace pohledávky

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/) jako globální správce vašeho tenanta Azure AD B2C.
2. Ujistěte se, že používáte adresář, který obsahuje vašeho klienta Azure AD B2C. V horní nabídce vyberte filtr **Directory + odběr** a zvolte adresář, který obsahuje vašeho klienta.
3. Zvolte **Všechny služby** v levém horním rohu portálu Azure Portal a vyhledejte a vyberte **Azure AD B2C**.
4. Vyberte **Toky uživatelů (zásady)** a pak vyberte tok uživatele. Například **B2C_1_signupsignin1**.
5. Vyberte **Deklarace identit aplikace**.
6. Povolte deklaraci **tokenu přístupu zprostředkovatele identity.**

    ![Povolení deklarace přístupového tokenu zprostředkovatele identity](./media/idp-pass-through-user-flow/idp-pass-through-user-flow-app-claim.png)

7. Kliknutím na **Uložit** uložte tok uživatele.

## <a name="test-the-user-flow"></a>Testování toku uživatele

Při testování aplikací v Azure AD B2C, může být užitečné mít token `https://jwt.ms` Azure AD B2C vrácena ke kontrole deklarací identity v něm.

1. Na stránce Přehled toku uživatele vyberte **Spustit tok uživatele**.
2. V **aplikaci**vyberte aplikaci, kterou jste dříve zaregistrovali. Chcete-li zobrazit token v **Reply URL** příkladu níže, měla by se zobrazit `https://jwt.ms`adresa URL odpovědi .
3. Klikněte na **Spustit tok uživatele**a přihlaste se pomocí přihlašovacích údajů účtu. Měli byste vidět přístupový token poskytovatele identity v **deklaraci idp_access_token.**

    Měli byste vidět něco podobného v následujícím příkladu:

    ![Dekódovaný token v jwt.ms se zvýrazněným blokem idp_access_token](./media/idp-pass-through-user-flow/idp-pass-through-user-flow-token.PNG)

## <a name="next-steps"></a>Další kroky

Další informace najdete v [přehledu tokenů Azure AD B2C](tokens-overview.md).
