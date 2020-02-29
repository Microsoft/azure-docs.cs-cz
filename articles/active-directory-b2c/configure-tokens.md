---
title: Konfigurace tokenů – Azure Active Directory B2C | Microsoft Docs
description: Naučte se konfigurovat životnost tokenu a nastavení kompatibility v Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 160898f8535d3dad4811af016ebca779b7ef43a8
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/29/2020
ms.locfileid: "78189613"
---
# <a name="configure-tokens-in-azure-active-directory-b2c"></a>Konfigurace tokenů v Azure Active Directory B2C

V tomto článku se dozvíte, jak nakonfigurovat [životnost a kompatibilitu tokenu](tokens-overview.md) v Azure Active Directory B2C (Azure AD B2C).

## <a name="prerequisites"></a>Předpoklady

[Vytvořte uživatelský tok](tutorial-create-user-flows.md) , který uživatelům umožní přihlásit se k aplikaci a přihlásit se k ní.

## <a name="configure-token-lifetime"></a>Konfigurace životnosti tokenů

Životnost tokenu můžete nakonfigurovat pro libovolný tok uživatele.

1. Přihlaste se na web [Azure Portal ](https://portal.azure.com).
2. Ujistěte se, že používáte adresář, který obsahuje vašeho tenanta Azure AD B2C. V horní nabídce vyberte filtr **adresář + odběr** a zvolte adresář, který obsahuje vašeho tenanta Azure AD B2C.
3. V levém horním rohu Azure Portal vyberte **všechny služby** a pak vyhledejte a vyberte **Azure AD B2C**.
4. Vyberte **toky uživatelů (zásady)** .
5. Otevřete uživatelský tok, který jste vytvořili dříve.
6. Vyberte **Vlastnosti**.
7. V části **životnost tokenu**upravte následující vlastnosti tak, aby vyhovovaly potřebám vaší aplikace:

    ![Nastavení vlastností životnosti tokenu v Azure Portal](./media/configure-tokens/token-lifetime.png)

8. Klikněte na možnost **Uložit**.

## <a name="configure-token-compatibility"></a>Konfigurace kompatibility tokenů

1. Vyberte **toky uživatelů (zásady)** .
2. Otevřete uživatelský tok, který jste vytvořili dříve.
3. Vyberte **Vlastnosti**.
4. V části **nastavení kompatibility tokenů**upravte následující vlastnosti tak, aby vyhovovaly potřebám vaší aplikace:

    ![Nastavení vlastností kompatibility tokenu v Azure Portal](./media/configure-tokens/token-compatibility.png)

5. Klikněte na možnost **Uložit**.

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o [použití přístupových tokenů](access-tokens.md).



