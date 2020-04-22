---
title: Nastavení přihlášení pro organizaci Azure AD
titleSuffix: Azure AD B2C
description: Nastavte přihlášení pro konkrétní organizaci Azure Active Directory ve službě Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/20/2020
ms.author: mimart
ms.subservice: B2C
ms.custom: fasttrack-edit
ms.openlocfilehash: 5b21fcd2d3ec5560b01352b112e9ed1bb2404766
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2020
ms.locfileid: "81678044"
---
# <a name="set-up-sign-in-for-a-specific-azure-active-directory-organization-in-azure-active-directory-b2c"></a>Nastavení přihlášení pro konkrétní organizaci Služby Azure Active Directory ve službě Azure Active Directory B2C

Chcete-li použít Azure Active Directory (Azure AD) jako [zprostředkovatele identity](authorization-code-flow.md) v Azure AD B2C, musíte vytvořit aplikaci, která ji představuje. Tento článek ukazuje, jak povolit přihlášení pro uživatele z konkrétní organizace Azure AD pomocí toku uživatelů v Azure AD B2C.

[!INCLUDE [active-directory-b2c-identity-provider-azure-ad](../../includes/active-directory-b2c-identity-provider-azure-ad.md)]

## <a name="configure-azure-ad-as-an-identity-provider"></a>Konfigurace Azure AD jako zprostředkovatele identity

1. Ujistěte se, že používáte adresář, který obsahuje klienta Azure AD B2C. V horní nabídce vyberte filtr **Directory + subscription** a zvolte adresář, který obsahuje vašeho klienta Azure AD B2C.
1. V levém horním rohu portálu Azure zvolte **Všechny služby** a pak vyhledejte a vyberte **Azure AD B2C**.
1. Vyberte **Zprostředkovatelé identity**a pak vyberte **Nový zprostředkovatel EOpenID Connect**.
1. Zadejte **název**. Zadejte například *Contoso Azure AD*.
1. Do **adresy URL metadat**zadejte `{tenant}` následující adresu URL, která nahradí název domény vašeho klienta Azure AD:

    ```
    https://login.microsoftonline.com/{tenant}/v2.0/.well-known/openid-configuration
    ```

    Například, `https://login.microsoftonline.com/contoso.onmicrosoft.com/v2.0/.well-known/openid-configuration`.

1. Pro **ID klienta**zadejte ID aplikace, které jste dříve zaznamenali.
1. U **tajného klíče klienta**zadejte tajný klíč klienta, který jste dříve zaznamenali.
1. Obor **zadejte** `openid profile`.
1. Ponechte výchozí hodnoty pro **typ odezvy**a **režim odezvy**.
1. (Nepovinné) Pro **nápovědu k doméně**zadejte `contoso.com`. Další informace najdete [v tématu Nastavení přímého přihlášení pomocí služby Azure Active Directory B2C](direct-signin.md#redirect-sign-in-to-a-social-provider).
1. V části **Mapování deklarací identity**vyberte následující deklarace identity:

    * **ID uživatele**: *oid*
    * **Zobrazovaný název**: *jméno*
    * **Křestní jméno**: *given_name*
    * **Příjmení**: *family_name*
    * **E-mail**: *unique_name*

1. Vyberte **Uložit**.
