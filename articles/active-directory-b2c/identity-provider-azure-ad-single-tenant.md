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
ms.date: 08/08/2019
ms.author: mimart
ms.subservice: B2C
ms.custom: fasttrack-edit
ms.openlocfilehash: 35fc4e1d64fa7df392fa878db14c0464da7dccf4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78188303"
---
# <a name="set-up-sign-in-for-a-specific-azure-active-directory-organization-in-azure-active-directory-b2c"></a>Nastavení přihlášení pro konkrétní organizaci Služby Azure Active Directory ve službě Azure Active Directory B2C

Chcete-li použít Azure Active Directory (Azure AD) jako [zprostředkovatele identity](authorization-code-flow.md) v Azure AD B2C, musíte vytvořit aplikaci, která ji představuje. Tento článek ukazuje, jak povolit přihlášení pro uživatele z konkrétní organizace Azure AD pomocí toku uživatelů v Azure AD B2C.

## <a name="create-an-azure-ad-app"></a>Vytvoření aplikace Azure AD

Chcete-li povolit přihlášení pro uživatele z konkrétní organizace Azure AD, musíte zaregistrovat aplikaci v rámci klienta Azure AD organizace, která není stejná jako váš klient Azure AD B2C.

1. Přihlaste se k [portálu Azure](https://portal.azure.com).
2. Ujistěte se, že používáte adresář, který obsahuje vašeho klienta Azure AD. V horní nabídce vyberte filtr **Directory + předplatného** a zvolte adresář, který obsahuje vašeho klienta Azure AD. Toto není stejný tenant jako váš klient Azure AD B2C.
3. V levém horním rohu portálu Azure zvolte **Všechny služby** a pak **vyhledejte**a vyberte registrace aplikací .
4. Vyberte **možnost Nová registrace**.
5. Zadejte název své aplikace. Například, `Azure AD B2C App`.
6. Přijměte výběr **účtů v tomto organizačním adresáři pouze** pro tuto aplikaci.
7. Pro **identifikátor URI přesměrování**přijměte hodnotu **webu**a zadejte následující `your-B2C-tenant-name` adresu URL ve všech malých písmenech, kde je nahrazen názvem klienta Azure AD B2C. Například: `https://fabrikam.b2clogin.com/fabrikam.onmicrosoft.com/oauth2/authresp`

    ```
    https://your-B2C-tenant-name.b2clogin.com/your-B2C-tenant-name.onmicrosoft.com/oauth2/authresp
    ```

    Všechny adresy URL by nyní měly používat [b2clogin.com](b2clogin.md).

8. Klepněte na tlačítko **Registrovat**. Zkopírujte **ID aplikace (klienta),** které bude použito později.
9. V nabídce aplikace **vyberte Možnost Certifikáty & tajných kódů** a potom vyberte **Nový tajný klíč klienta**.
10. Zadejte název tajného klíče klienta. Například, `Azure AD B2C App Secret`.
11. Vyberte dobu vypršení platnosti. Pro tuto žádost, přijmout výběr **Do 1 roku**.
12. Vyberte **Přidat** a zkopírujte hodnotu nového tajného klíče klienta, který se zobrazí, aby byl použit později.

## <a name="configure-azure-ad-as-an-identity-provider"></a>Konfigurace Azure AD jako zprostředkovatele identity

1. Ujistěte se, že používáte adresář, který obsahuje klienta Azure AD B2C. V horní nabídce vyberte filtr **Directory + subscription** a zvolte adresář, který obsahuje vašeho klienta Azure AD B2C.
1. V levém horním rohu portálu Azure zvolte **Všechny služby** a pak vyhledejte a vyberte **Azure AD B2C**.
1. Vyberte **Zprostředkovatelé identity**a pak vyberte **Nový zprostředkovatel EOpenID Connect**.
1. Zadejte **název**. Zadejte například *Contoso Azure AD*.
1. Do **adresy URL metadat**zadejte `your-AD-tenant-domain` následující adresu URL, která nahradí název domény vašeho klienta Azure AD:

    ```
    https://login.microsoftonline.com/your-AD-tenant-domain/.well-known/openid-configuration
    ```

    Například, `https://login.microsoftonline.com/contoso.onmicrosoft.com/.well-known/openid-configuration`.

    **Nepoužívejte** koncový bod metadat Azure AD v2.0, například `https://login.microsoftonline.com/contoso.onmicrosoft.com/v2.0/.well-known/openid-configuration`. Výsledkem je chyba podobná `AADB2C: A claim with id 'UserId' was not found, which is required by ClaimsTransformation 'CreateAlternativeSecurityId' with id 'CreateAlternativeSecurityId' in policy 'B2C_1_SignUpOrIn' of tenant 'contoso.onmicrosoft.com'` při pokusu o přihlášení.

1. Pro **ID klienta**zadejte ID aplikace, které jste dříve zaznamenali.
1. U **tajného klíče klienta**zadejte tajný klíč klienta, který jste dříve zaznamenali.
1. Ponechte výchozí hodnoty pro **obor**, **typ odpovědi**a **režim odezvy**.
1. (Nepovinné) Zadejte hodnotu pro **Domain_hint**. Například *ContosoAD*. Toto je hodnota, která se má použít při odkazování na tohoto zprostředkovatele identity pomocí *domain_hint* v požadavku.
1. V části **Mapování deklarací identity**zadejte následující hodnoty mapování deklarací identity:

    * **ID uživatele**: *oid*
    * **Zobrazovaný název**: *jméno*
    * **Křestní jméno**: *given_name*
    * **Příjmení**: *family_name*
    * **E-mail**: *unique_name*

1. Vyberte **Uložit**.
