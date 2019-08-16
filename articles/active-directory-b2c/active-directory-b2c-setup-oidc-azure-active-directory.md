---
title: Nastavení přihlášení pro Azure Active Directory organizaci – Azure Active Directory B2C
description: Nastavte přihlášení pro konkrétní Azure Active Directory organizaci v Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/06/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 46755cb8d0383d166b10d50854eb476b676a6ee4
ms.sourcegitcommit: 0e59368513a495af0a93a5b8855fd65ef1c44aac
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/15/2019
ms.locfileid: "69509743"
---
# <a name="set-up-sign-in-for-a-specific-azure-active-directory-organization-in-azure-active-directory-b2c"></a>Nastavení přihlášení pro konkrétní organizaci Azure Active Directory v Azure Active Directory B2C

Pokud chcete jako [poskytovatele identity](active-directory-b2c-reference-oauth-code.md) v Azure AD B2C použít Azure Active Directory (Azure AD), musíte vytvořit aplikaci, která ji představuje. V tomto článku se dozvíte, jak povolit přihlášení pro uživatele z konkrétní organizace Azure AD pomocí toku uživatele v Azure AD B2C.

## <a name="create-an-azure-ad-app"></a>Vytvoření aplikace Azure AD

Pokud chcete povolit přihlašování pro uživatele z konkrétní organizace Azure AD, musíte zaregistrovat aplikaci v tenantovi organizace Azure AD, která není stejná jako váš tenant Azure AD B2C.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Ujistěte se, že používáte adresář, který obsahuje vašeho tenanta Azure AD. V horní nabídce vyberte **adresář a filtr** předplatného a zvolte adresář, který obsahuje vašeho TENANTA Azure AD. Toto není stejný tenant jako tenant Azure AD B2C.
3. V levém horním rohu Azure Portal vyberte **všechny služby** a pak vyhledejte a vyberte **Registrace aplikací**.
4. Vyberte **Nová registrace**.
5. Zadejte název své aplikace. Například, `Azure AD B2C App`.
6. Přijměte výběr **účtů v tomto organizačním adresáři pouze** pro tuto aplikaci.
7. Pro **identifikátor URI přesměrování**přijměte hodnotu **Web**a zadejte následující adresu URL na všechna malá písmena, kde `your-B2C-tenant-name` se nahradí názvem vašeho tenanta Azure AD B2C. Například `https://fabrikam.b2clogin.com/fabrikam.onmicrosoft.com/oauth2/authresp`:

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

1. Ujistěte se, že používáte adresář, který obsahuje Azure AD B2C tenanta. V horní nabídce vyberte **adresář a filtr** předplatného a zvolte adresář, který obsahuje vašeho tenanta Azure AD B2C.
2. V levém horním rohu Azure Portal vyberte **všechny služby** a pak vyhledejte a vyberte **Azure AD B2C**.
3. Vyberte **zprostředkovatelé Identity**a pak vyberte **přidat**.
4. Zadejte **název**. Zadejte například `Contoso Azure AD`.
5. Vyberte **typ zprostředkovatele identity**, vyberte **OpenID připojit**a pak klikněte na **OK**.
6. Vyberte **nastavit tohoto zprostředkovatele identity** .
7. V poli **Adresa URL metadat**zadejte následující adresu URL `your-AD-tenant-domain` , která nahrazuje název domény vašeho tenanta Azure AD. Například `https://login.microsoftonline.com/contoso.onmicrosoft.com/.well-known/openid-configuration`:

    ```
    https://login.microsoftonline.com/your-AD-tenant-domain/.well-known/openid-configuration
    ```

8. Jako **ID klienta**zadejte ID aplikace, které jste předtím nahráli, a pro **tajný klíč klienta**zadejte tajný klíč klienta, který jste předtím nahráli.
9. V případě potřeby zadejte hodnotu pro **Domain_hint**. Například, `ContosoAD`. Toto je hodnota, která se má použít při odkazování na tohoto zprostředkovatele identity pomocí *domain_hint* v žádosti.
10. Klikněte na **OK**.
11. Vyberte **mapovat deklarace identity zprostředkovatele identity** a nastavte následující deklarace identity:

    - Jako **ID uživatele**zadejte `oid`.
    - Jako **Zobrazovaný název**zadejte `name`.
    - Pro **křestní jméno**zadejte `given_name`.
    - Jako **příjmení**zadejte `family_name`.
    - Do **e-mailu**zadejte `unique_name`.

12. Klikněte na tlačítko **OK**a potom kliknutím na tlačítko **vytvořit** uložte konfiguraci.
