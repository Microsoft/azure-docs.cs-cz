---
title: Konfigurace tokenů – Služba Azure Active Directory B2C | Dokumenty společnosti Microsoft
description: Zjistěte, jak nakonfigurovat životnost tokenu a nastavení kompatibility ve službě Azure Active Directory B2C.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78189613"
---
# <a name="configure-tokens-in-azure-active-directory-b2c"></a>Konfigurace tokenů ve službě Azure Active Directory B2C

V tomto článku se dozvíte, jak nakonfigurovat [životnost a kompatibilitu tokenu](tokens-overview.md) ve službě Azure Active Directory B2C (Azure AD B2C).

## <a name="prerequisites"></a>Požadavky

[Vytvořte tok uživatelů,](tutorial-create-user-flows.md) který uživatelům umožní přihlásit se a přihlásit se k vaší aplikaci.

## <a name="configure-token-lifetime"></a>Konfigurovat životnost tokenu

Životnost tokenu můžete nakonfigurovat pro libovolný tok uživatele.

1. Přihlaste se k [portálu Azure](https://portal.azure.com).
2. Ujistěte se, že používáte adresář, který obsahuje vašeho klienta Azure AD B2C. V horní nabídce vyberte filtr **Directory + subscription** a zvolte adresář, který obsahuje vašeho klienta Azure AD B2C.
3. V levém horním rohu portálu Azure zvolte **Všechny služby** a pak vyhledejte a vyberte **Azure AD B2C**.
4. Vyberte **toky uživatelů (zásady).**
5. Otevřete tok uživatele, který jste dříve vytvořili.
6. Vyberte **vlastnosti**.
7. V části **Token životnost**upravte následující vlastnosti tak, aby vyhovovaly potřebám vaší aplikace:

    ![Nastavení vlastností tokenu životnost na webu Azure Portal](./media/configure-tokens/token-lifetime.png)

8. Klikněte na **Uložit**.

## <a name="configure-token-compatibility"></a>Konfigurace kompatibility tokenů

1. Vyberte **toky uživatelů (zásady).**
2. Otevřete tok uživatele, který jste dříve vytvořili.
3. Vyberte **vlastnosti**.
4. V části **Nastavení kompatibility tokenů**upravte následující vlastnosti tak, aby vyhovovaly potřebám vaší aplikace:

    ![Nastavení vlastností kompatibility tokenů na webu Azure Portal](./media/configure-tokens/token-compatibility.png)

5. Klikněte na **Uložit**.

## <a name="next-steps"></a>Další kroky

Další informace o [používání přístupových tokenů](access-tokens.md).



