---
title: Nastavení přihlášení pro organizaci Azure AD
titleSuffix: Azure AD B2C
description: Nastavte přihlášení pro konkrétní Azure Active Directory organizaci v Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: marsma
ms.subservice: B2C
ms.custom: fasttrack-edit
ms.openlocfilehash: b0ff1c2d913d0a4402b491f3c84ce0d35cd081df
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2020
ms.locfileid: "76847586"
---
# <a name="set-up-sign-in-for-a-specific-azure-active-directory-organization-in-azure-active-directory-b2c"></a>Nastavení přihlášení pro konkrétní organizaci Azure Active Directory v Azure Active Directory B2C

Pokud chcete jako [poskytovatele identity](authorization-code-flow.md) v Azure AD B2C použít Azure Active Directory (Azure AD), musíte vytvořit aplikaci, která ji představuje. V tomto článku se dozvíte, jak povolit přihlášení pro uživatele z konkrétní organizace Azure AD pomocí toku uživatele v Azure AD B2C.

## <a name="create-an-azure-ad-app"></a>Vytvoření aplikace Azure AD

Pokud chcete povolit přihlašování pro uživatele z konkrétní organizace Azure AD, musíte zaregistrovat aplikaci v tenantovi organizace Azure AD, která není stejná jako váš tenant Azure AD B2C.

1. Přihlaste se na web [Azure Portal](https://portal.azure.com).
2. Ujistěte se, že používáte adresář, který obsahuje vašeho tenanta Azure AD. V horní nabídce vyberte filtr **adresář + odběr** a zvolte adresář, který obsahuje vašeho TENANTA Azure AD. Toto není stejný tenant jako tenant Azure AD B2C.
3. V levém horním rohu Azure Portal vyberte **všechny služby** a pak vyhledejte a vyberte **Registrace aplikací**.
4. Vyberte **Nová registrace**.
5. Zadejte název své aplikace. Například, `Azure AD B2C App`.
6. Přijměte výběr **účtů v tomto organizačním adresáři pouze** pro tuto aplikaci.
7. Pro **identifikátor URI přesměrování**přijměte hodnotu **Web**a zadejte následující adresu URL do všech malých písmen, kde se `your-B2C-tenant-name` nahradí názvem vašeho tenanta Azure AD B2C. Například `https://fabrikam.b2clogin.com/fabrikam.onmicrosoft.com/oauth2/authresp`:

    ```
    https://your-B2C-tenant-name.b2clogin.com/your-B2C-tenant-name.onmicrosoft.com/oauth2/authresp
    ```

    Všechny adresy URL by teď měly používat [b2clogin.com](b2clogin.md).

8. Klikněte na **zaregistrovat**. Zkopírujte **ID aplikace (klienta)** , které se má použít později.
9. V nabídce aplikace vyberte **certifikáty & tajné klíče** a pak vyberte **nový tajný klíč klienta**.
10. Zadejte název tajného klíče klienta. Například, `Azure AD B2C App Secret`.
11. Vyberte období vypršení platnosti. Pro tuto aplikaci přijměte výběr **v 1 roce**.
12. Vyberte **Přidat** a zkopírujte hodnotu nového tajného klíče klienta, který se zobrazí, aby se použil později.

## <a name="configure-azure-ad-as-an-identity-provider"></a>Konfigurace Azure AD jako zprostředkovatele identity

1. Ujistěte se, že používáte adresář, který obsahuje Azure AD B2C tenanta. V horní nabídce vyberte filtr **adresář + odběr** a zvolte adresář, který obsahuje vašeho tenanta Azure AD B2C.
1. V levém horním rohu Azure Portal vyberte **všechny služby** a pak vyhledejte a vyberte **Azure AD B2C**.
1. Vyberte **Zprostředkovatelé identity**a potom vyberte **Nový poskytovatel OpenID Connect**.
1. Zadejte **název**. Zadejte například *Contoso Azure AD*.
1. V poli **Adresa URL metadat**zadejte následující adresu URL, která nahrazuje `your-AD-tenant-domain` názvem domény vašeho TENANTA Azure AD:

    ```
    https://login.microsoftonline.com/your-AD-tenant-domain/.well-known/openid-configuration
    ```

    Například, `https://login.microsoftonline.com/contoso.onmicrosoft.com/.well-known/openid-configuration`.

    **Nepoužívejte koncový** bod METADAT Azure AD v 2.0, například `https://login.microsoftonline.com/contoso.onmicrosoft.com/v2.0/.well-known/openid-configuration`. Výsledkem je, že při pokusu o přihlášení dojde k chybě, která je podobná `AADB2C: A claim with id 'UserId' was not found, which is required by ClaimsTransformation 'CreateAlternativeSecurityId' with id 'CreateAlternativeSecurityId' in policy 'B2C_1_SignUpOrIn' of tenant 'contoso.onmicrosoft.com'`.

1. Jako **ID klienta**zadejte ID aplikace, které jste si poznamenali dříve.
1. Jako **tajný klíč klienta**zadejte tajný klíč klienta, který jste předtím nahráli.
1. Ponechte výchozí hodnoty **Rozsah**, **typ odpovědi**a **režim odezvy**.
1. Volitelné Zadejte hodnotu pro **Domain_hint**. Například *ContosoAD*. Toto je hodnota, která se má použít při odkazování na tohoto zprostředkovatele identity pomocí *domain_hint* v žádosti.
1. V části **mapování deklarací identity zprostředkovatele identity**zadejte následující hodnoty mapování deklarací identity:

    * **ID uživatele**: *OID*
    * **Zobrazovaný název**: *název*
    * **Křestní jméno**: *given_name*
    * **Příjmení**: *family_name*
    * **E-mail**: *unique_name*

1. Vyberte **Uložit**.
