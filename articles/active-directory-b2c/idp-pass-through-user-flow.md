---
title: Předání přístupového tokenu prostřednictvím toku uživatele do aplikace
titleSuffix: Azure AD B2C
description: Naučte se předat přístupový token pro zprostředkovatele identity OAuth 2,0 jako deklaraci identity v toku uživatele v Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 08/17/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 5b834dda926b7da1241a325e1453143eccafaf30
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87488767"
---
# <a name="pass-an-access-token-through-a-user-flow-to-your-application-in-azure-active-directory-b2c"></a>Předání přístupového tokenu prostřednictvím toku uživatele do aplikace v Azure Active Directory B2C

[Tok uživatele](user-flow-overview.md) v Azure Active Directory B2C (Azure AD B2C) poskytuje uživatelům vaší aplikace možnost registrace nebo přihlášení pomocí poskytovatele identity. Po spuštění cesty Azure AD B2C obdrží od poskytovatele identity [přístupový token](tokens-overview.md) . Azure AD B2C používá tento token k načtení informací o uživateli. V toku uživatele povolíte deklaraci identity, abyste mohli token předat do aplikací, které zaregistrujete v Azure AD B2C.

Azure AD B2C aktuálně podporuje pouze předávání přístupového tokenu zprostředkovatelů identity [OAuth 2,0](authorization-code-flow.md) , které zahrnují [Facebook](identity-provider-facebook.md) a [Google](identity-provider-google.md). U všech ostatních zprostředkovatelů identity se deklarace identity vrátí jako prázdná.

## <a name="prerequisites"></a>Požadavky

* Vaše aplikace musí používat [doporučený tok uživatele](user-flow-versions.md).
* Tok uživatele má nakonfigurovaný poskytovatele identity OAuth 2,0.

## <a name="enable-the-claim"></a>Povolit deklaraci identity

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/) jako globální správce vašeho tenanta Azure AD B2C.
2. Ujistěte se, že používáte adresář, který obsahuje vašeho tenanta Azure AD B2C. V horní nabídce vyberte filtr **adresář + odběr** a zvolte adresář, který obsahuje vašeho tenanta.
3. Zvolte **Všechny služby** v levém horním rohu portálu Azure Portal a vyhledejte a vyberte **Azure AD B2C**.
4. Vyberte **toky uživatelů (zásady)** a pak vyberte tok uživatele. Například **B2C_1_signupsignin1**.
5. Vyberte **Deklarace identit aplikace**.
6. Povolte deklaraci identity **přístupového tokenu zprostředkovatele identity** .

    ![Povolit deklaraci identity přístupového tokenu zprostředkovatele identity](./media/idp-pass-through-user-flow/idp-pass-through-user-flow-app-claim.png)

7. Kliknutím na **Uložit** uložte tok uživatele.

## <a name="test-the-user-flow"></a>Testování toku uživatele

Při testování aplikací v Azure AD B2C může být užitečné, aby se token Azure AD B2C vrátil na `https://jwt.ms` kontrolu deklarací identity v něm.

1. Na stránce Přehled toku uživatele vyberte **Spustit tok uživatele**.
2. V případě **aplikace**vyberte svou aplikaci, kterou jste předtím zaregistrovali. Pokud chcete token zobrazit v následujícím příkladu, měla by se zobrazit **Adresa URL odpovědi** `https://jwt.ms` .
3. Klikněte na **Spustit tok uživatele**a pak se přihlaste pomocí přihlašovacích údajů k účtu. V deklaraci **idp_access_token** by měl být uveden přístupový token poskytovatele identity.

    Měl by se zobrazit něco podobného jako v následujícím příkladu:

    ![Dekódování tokenu v jwt.ms s zvýrazněným blokem idp_access_token](./media/idp-pass-through-user-flow/idp-pass-through-user-flow-token.PNG)

## <a name="next-steps"></a>Další kroky

Další informace najdete v [přehledu Azure AD B2Cch tokenů](tokens-overview.md).
