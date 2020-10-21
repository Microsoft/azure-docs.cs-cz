---
title: Konfigurace tokenů – Azure Active Directory B2C | Microsoft Docs
description: Naučte se konfigurovat životnost tokenu a nastavení kompatibility v Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 10/15/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 67bc9d6b35d4841999721a00592a6bbe23bff10f
ms.sourcegitcommit: f88074c00f13bcb52eaa5416c61adc1259826ce7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/21/2020
ms.locfileid: "92340219"
---
# <a name="configure-tokens-in-azure-active-directory-b2c"></a>Konfigurace tokenů v Azure Active Directory B2C

V tomto článku se dozvíte, jak nakonfigurovat [životnost a kompatibilitu tokenu](tokens-overview.md) v Azure Active Directory B2C (Azure AD B2C).

## <a name="prerequisites"></a>Předpoklady

[Vytvořte uživatelský tok](tutorial-create-user-flows.md), který uživatelům umožní registraci a přihlášení do vaší aplikace.

## <a name="configure-jwt-token-lifetime"></a>Konfigurace životnosti tokenu JWT

Životnost tokenu můžete nakonfigurovat pro libovolný tok uživatele.

1. Přihlaste se na [Azure Portal](https://portal.azure.com).
2. Ujistěte se, že používáte adresář, který obsahuje vašeho tenanta Azure AD B2C. V horní nabídce vyberte filtr **adresář + odběr** a zvolte adresář, který obsahuje vašeho tenanta Azure AD B2C.
3. V levém horním rohu Azure Portal vyberte **všechny služby** a pak vyhledejte a vyberte **Azure AD B2C**.
4. Vyberte **toky uživatelů (zásady)**.
5. Otevřete uživatelský tok, který jste vytvořili dříve.
6. Vyberte **Vlastnosti**.
7. V části **životnost tokenu**upravte následující vlastnosti tak, aby vyhovovaly potřebám vaší aplikace:

    ![Nastavení vlastností životnosti tokenu v Azure Portal](./media/configure-tokens/token-lifetime.png)

8. Klikněte na **Uložit**.

> [!NOTE]
> Jednostránkové aplikace používající tok autorizačního kódu s PKCE vždy mají dobu životnosti obnovovacího tokenu 24 hodin. [Přečtěte si další informace o dopadech aktualizace tokenů v prohlížeči na zabezpečení](../active-directory/develop/reference-third-party-cookies-spas.md#security-implications-of-refresh-tokens-in-the-browser).

## <a name="configure-jwt-token-compatibility"></a>Konfigurace kompatibility tokenů JWT

1. Vyberte **toky uživatelů (zásady)**.
2. Otevřete uživatelský tok, který jste vytvořili dříve.
3. Vyberte **Vlastnosti**.
4. V části **nastavení kompatibility tokenů**upravte následující vlastnosti tak, aby vyhovovaly potřebám vaší aplikace:

    ![Nastavení vlastností kompatibility tokenu v Azure Portal](./media/configure-tokens/token-compatibility.png)

5. Klikněte na **Uložit**.

## <a name="provide-optional-claims-to-your-app"></a>Poskytněte do aplikace volitelné deklarace identity.

Deklarace identity aplikace jsou hodnoty, které se vrátí do aplikace. Aktualizujte tok uživatele tak, aby obsahoval požadované deklarace identity.

1. Vyberte **toky uživatelů (zásady)**.
1. Otevřete uživatelský tok, který jste vytvořili dříve.
1. Vyberte **Deklarace identit aplikace**.
1. Vyberte deklarace identity a atributy, které chcete poslat zpátky do své aplikace.
1. Klikněte na **Uložit**.


## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o tom, jak [vyžádat přístupové tokeny](access-tokens.md).



