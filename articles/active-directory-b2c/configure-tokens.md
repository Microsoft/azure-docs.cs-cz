---
title: Konfigurace tokenů – Azure Active Directory B2C | Microsoft Docs
description: Naučte se konfigurovat životnost tokenu a nastavení kompatibility v Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 2ed1426bea920d1ef30fb28ff4a975d1268c12ff
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/12/2019
ms.locfileid: "67846932"
---
# <a name="configure-tokens-in-azure-active-directory-b2c"></a>Konfigurace tokenů v Azure Active Directory B2C

V tomto článku se dozvíte, jak nakonfigurovat [životnost a kompatibilitu tokenu](active-directory-b2c-reference-tokens.md) v Azure Active Directory (Azure AD) B2C.

## <a name="prerequisites"></a>Požadavky

[Vytvořte uživatelský tok](tutorial-create-user-flows.md) , který uživatelům umožní přihlásit se k aplikaci a přihlásit se k ní.

## <a name="configure-token-lifetime"></a>Konfigurace životnosti tokenů

Životnost tokenu můžete nakonfigurovat pro libovolný tok uživatele.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Ujistěte se, že používáte adresář, který obsahuje vašeho tenanta Azure AD B2C. V horní nabídce vyberte **adresář a filtr** předplatného a zvolte adresář, který obsahuje vašeho tenanta Azure AD B2C.
3. V levém horním rohu Azure Portal vyberte **všechny služby** a pak vyhledejte a vyberte **Azure AD B2C**.
4. Vyberte **toky uživatelů (zásady)** .
5. Otevřete uživatelský tok, který jste vytvořili dříve.
6. Vyberte **vlastnosti**.
7. V části **životnost tokenu**upravte následující vlastnosti tak, aby vyhovovaly potřebám vaší aplikace:

    ![Nastavení vlastností životnosti tokenu v Azure Portal](./media/configure-tokens/token-lifetime.png)

8. Klikněte na **Uložit**.

## <a name="configure-token-compatibility"></a>Konfigurace kompatibility tokenů

1. Vyberte **toky uživatelů (zásady)** .
2. Otevřete uživatelský tok, který jste vytvořili dříve.
3. Vyberte **vlastnosti**.
4. V části **nastavení kompatibility tokenů**upravte následující vlastnosti tak, aby vyhovovaly potřebám vaší aplikace:

    ![Nastavení vlastností kompatibility tokenu v Azure Portal](./media/configure-tokens/token-compatibility.png)

5. Klikněte na **Uložit**.

## <a name="next-steps"></a>Další postup

Přečtěte si další informace o [použití přístupových tokenů](active-directory-b2c-access-tokens.md).



