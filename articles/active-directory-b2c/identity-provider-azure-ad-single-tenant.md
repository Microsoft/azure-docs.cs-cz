---
title: Nastavení přihlášení pro organizaci Azure AD
titleSuffix: Azure AD B2C
description: Nastavte přihlášení pro konkrétní Azure Active Directory organizaci v Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 04/20/2020
ms.author: mimart
ms.subservice: B2C
ms.custom: fasttrack-edit
ms.openlocfilehash: e4fa4b64c6519df90d5883e8c5760b3ed2ce0337
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "90004455"
---
# <a name="set-up-sign-in-for-a-specific-azure-active-directory-organization-in-azure-active-directory-b2c"></a>Nastavení přihlášení pro konkrétní organizaci Azure Active Directory v Azure Active Directory B2C

Pokud chcete jako [poskytovatele identity](authorization-code-flow.md) v Azure AD B2C použít Azure Active Directory (Azure AD), musíte vytvořit aplikaci, která ji představuje. V tomto článku se dozvíte, jak povolit přihlášení pro uživatele z konkrétní organizace Azure AD pomocí toku uživatele v Azure AD B2C.

[!INCLUDE [active-directory-b2c-identity-provider-azure-ad](../../includes/active-directory-b2c-identity-provider-azure-ad.md)]

## <a name="configure-azure-ad-as-an-identity-provider"></a>Konfigurace Azure AD jako zprostředkovatele identity

1. Ujistěte se, že používáte adresář, který obsahuje Azure AD B2C tenanta. V horní nabídce vyberte filtr **adresář + odběr** a zvolte adresář, který obsahuje vašeho tenanta Azure AD B2C.
1. V levém horním rohu Azure Portal vyberte **všechny služby** a pak vyhledejte a vyberte **Azure AD B2C**.
1. Vyberte **Zprostředkovatelé identity**a potom vyberte **Nový poskytovatel OpenID Connect**.
1. Zadejte **název**. Zadejte například *Contoso Azure AD*.
1. V poli **Adresa URL metadat**zadejte následující adresu URL, která nahrazuje `{tenant}` název domény vašeho tenanta Azure AD:

    ```
    https://login.microsoftonline.com/{tenant}/v2.0/.well-known/openid-configuration
    ```

    Například, `https://login.microsoftonline.com/contoso.onmicrosoft.com/v2.0/.well-known/openid-configuration`.
    Například, `https://login.microsoftonline.com/contoso.com/v2.0/.well-known/openid-configuration`.

1. Jako **ID klienta**zadejte ID aplikace, které jste si poznamenali dříve.
1. Jako **tajný klíč klienta**zadejte tajný klíč klienta, který jste předtím nahráli.
1. Pro **Rozsah**zadejte `openid profile` .
1. Ponechte výchozí hodnoty pro **typ odpovědi**a **režim odezvy**.
1. Volitelné Jako **nápovědu k doméně**zadejte `contoso.com` . Další informace najdete v tématu [Nastavení přímého přihlašování pomocí Azure Active Directory B2C](direct-signin.md#redirect-sign-in-to-a-social-provider).
1. V části **mapování deklarací identity zprostředkovatele identity**vyberte následující deklarace identity:

    * **ID uživatele**: *OID*
    * **Zobrazovaný název**: *název*
    * **Křestní jméno**: *given_name*
    * **Příjmení**: *family_name*
    * **E-mail**: *unique_name*

1. Vyberte **Uložit**.
