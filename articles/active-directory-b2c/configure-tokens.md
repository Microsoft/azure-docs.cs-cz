---
title: Konfigurace tokenů – Azure Active Directory B2C | Dokumentace Microsoftu
description: Zjistěte, jak nakonfigurovat nastavení token doby života a kompatibility v Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/16/2019
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 69a6284ea7b8905bb5efdb1f4c9f26027bd6f9d9
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2019
ms.locfileid: "64689616"
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



