---
title: Konfigurace tokenů – Azure Active Directory B2C | Dokumentace Microsoftu
description: Zjistěte, jak nakonfigurovat nastavení token doby života a kompatibility v Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 2455b8c12ed042af3a06a158a4e5c60a0aee748b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60384004"
---
# <a name="configure-tokens-in-azure-active-directory-b2c"></a>Konfigurace tokenů v Azure Active Directory B2C

V tomto článku se dozvíte, jak nakonfigurovat [životnost a kompatibility token](active-directory-b2c-reference-tokens.md) v Azure Active Directory (Azure AD) B2C.

## <a name="prerequisites"></a>Požadavky

[Vytvořit tok uživatele](tutorial-create-user-flows.md) umožňují uživatelům registraci a přihlášení do vaší aplikace.

## <a name="configure-token-lifetime"></a>Konfigurace životnosti tokenu

Životnost tokenu můžete nakonfigurovat na jakýkoli tok uživatele.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Ujistěte se, že používáte adresáře, který obsahuje vašeho tenanta Azure AD B2C. Vyberte **filtr adresářů a předplatných** v horní nabídce a vyberte adresář, který obsahuje vašeho tenanta Azure AD B2C.
3. Zvolte **všechny služby** v horním levém horním rohu webu Azure portal a poté vyhledejte a vyberte **Azure AD B2C**.
4. Vyberte **toky uživatelů (zásady)**.
5. Otevřete tok uživatele, který jste předtím vytvořili. 
6. Vyberte **vlastnosti**.
7. V části **Token doby života**, nastavte následující vlastnosti podle potřeb vaší aplikace:

    ![Konfigurace životnosti tokenu](./media/configure-tokens/token-lifetime.png)

8. Klikněte na **Uložit**.

## <a name="configure-token-compatibility"></a>Konfigurace kompatibility tokenů

1. Vyberte **toky uživatelů (zásady)**.
2. Otevřete tok uživatele, který jste předtím vytvořili. 
3. Vyberte **vlastnosti**.
4. V části **Token nastavení kompatibility**, nastavte následující vlastnosti podle potřeb vaší aplikace:

    ![Konfigurace kompatibility tokenů](./media/configure-tokens/token-compatibility.png)

5. Klikněte na **Uložit**.

## <a name="next-steps"></a>Další postup

Další informace o tom, jak [použití přístupových tokenů](active-directory-b2c-access-tokens.md).



