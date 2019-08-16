---
title: Kurz – přidání zprostředkovatelů identit do aplikací – Azure Active Directory B2C
description: Naučte se přidávat zprostředkovatele identit do aplikací v Azure Active Directory B2C pomocí Azure Portal.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 07/08/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: eee881e6d4e446e07867261545a90dfacaa93712
ms.sourcegitcommit: 0e59368513a495af0a93a5b8855fd65ef1c44aac
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/15/2019
ms.locfileid: "69512213"
---
# <a name="tutorial-add-identity-providers-to-your-applications-in-azure-active-directory-b2c"></a>Kurz: Přidejte do svých aplikací zprostředkovatele identity v Azure Active Directory B2C

V aplikacích můžete chtít povolit uživatelům, aby se přihlásili pomocí různých zprostředkovatelů identity. *Zprostředkovatel identity* vytváří, uchovává a spravuje informace o identitě při poskytování služeb ověřování aplikacím. Zprostředkovatele identity, které jsou podporované službou Azure Active Directory (Azure AD) B2C, můžete přidat k [](active-directory-b2c-reference-policies.md) vašim uživatelským tokům pomocí Azure Portal.

V tomto článku získáte informace o těchto tématech:

> [!div class="checklist"]
> * Vytvoření aplikací zprostředkovatele identity
> * Přidejte zprostředkovatele identity do svého tenanta.
> * Přidání zprostředkovatelů identit do toku uživatele

Ve svých aplikacích obvykle používáte pouze jednoho poskytovatele identity, ale máte možnost přidat další. V tomto kurzu se dozvíte, jak do vaší aplikace přidat poskytovatele identity Azure AD a poskytovatele identity Facebooku. Přidání obou těchto poskytovatelů identity do aplikace je volitelné. Můžete také přidat další zprostředkovatele identity, jako je [Amazon](active-directory-b2c-setup-amzn-app.md), [GitHub](active-directory-b2c-setup-github-app.md), [Google](active-directory-b2c-setup-goog-app.md), [LinkedIn](active-directory-b2c-setup-li-app.md), [Microsoft](active-directory-b2c-setup-msa-app.md)nebo [Twitter](active-directory-b2c-setup-twitter-app.md).

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

[Vytvořte uživatelský tok](tutorial-create-user-flows.md) , který uživatelům umožní přihlásit se k aplikaci a přihlásit se k ní.

## <a name="create-applications"></a>Vytváření aplikací

Aplikace zprostředkovatele identity poskytují identifikátor a klíč, který umožňuje komunikaci s vaším klientem Azure AD B2C. V této části kurzu vytvoříte aplikaci Azure AD a aplikaci Facebook, ze které získáte identifikátory a klíče, pomocí kterých přidáte poskytovatele identity do svého tenanta. Pokud přidáváte pouze jednoho poskytovatele identity, stačí vytvořit aplikaci pro tohoto poskytovatele.

### <a name="create-an-azure-active-directory-application"></a>Vytvoření aplikace Azure Active Directory

Pokud chcete povolit přihlášení pro uživatele ze služby Azure AD, musíte zaregistrovat aplikaci v tenantovi Azure AD. Tenant Azure AD není stejný jako váš tenant Azure AD B2C.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
1. Ujistěte se, že používáte adresář, který obsahuje vašeho tenanta Azure AD, a to tak, že v horní nabídce kliknete na **Filtr adresář a odběr** a zvolíte adresář, který obsahuje vašeho TENANTA Azure AD.
1. V levém horním rohu Azure Portal vyberte **všechny služby** a pak vyhledejte a vyberte **Registrace aplikací**.
1. Vyberte **Nová registrace**.
1. Zadejte název své aplikace. Například, `Azure AD B2C App`.
1. Přijměte výběr **účtů v tomto organizačním adresáři pouze** pro tuto aplikaci.
1. Pro **identifikátor URI přesměrování**přijměte hodnotu **Web** a zadejte následující adresu URL na všechna malá písmena, nahraďte `your-B2C-tenant-name` názvem vašeho tenanta Azure AD B2C.

    ```
    https://your-B2C-tenant-name.b2clogin.com/your-B2C-tenant-name.onmicrosoft.com/oauth2/authresp
    ```

    Například, `https://contoso.b2clogin.com/contoso.onmicrosoft.com/oauth2/authresp`.

    Všechny adresy URL by teď měly používat [b2clogin.com](b2clogin.md).

1. Vyberte **Registrovat**a pak zaznamenejte **ID aplikace (klienta)** , kterou použijete v pozdějším kroku.
1. V nabídce **Spravovat** v nabídce aplikace vyberte **certifikáty & tajných**kódů a pak vyberte **nový tajný klíč klienta**.
1. Zadejte **Popis** tajného klíče klienta. Například, `Azure AD B2C App Secret`.
1. Vyberte období vypršení platnosti. Pro tuto aplikaci přijměte výběr **v 1 roce**.
1. Vyberte **Přidat**a potom zaznamenejte hodnotu nového tajného klíče klienta, který použijete v pozdějším kroku.

### <a name="create-a-facebook-application"></a>Vytvoření aplikace na Facebooku

Chcete-li použít účet Facebook jako poskytovatele identity v Azure AD B2C, je nutné vytvořit aplikaci na Facebooku. Pokud ještě nemáte účet Facebook, můžete ho získat na adrese [https://www.facebook.com/](https://www.facebook.com/).

1. Přihlaste se ke [službě Facebook pro vývojáře](https://developers.facebook.com/) s přihlašovacími údaji k účtu Facebook.
1. Pokud jste to ještě neudělali, musíte se zaregistrovat jako vývojář Facebooku. Provedete to tak , že vyberete Začínáme v pravém horním rohu stránky, přijmete zásady Facebooku a dokončíte registrační kroky.
1. Vyberte **Moje aplikace** a pak **vytvořit aplikaci**.
1. Zadejte **Zobrazovaný název** a platný **kontaktní e-mail**.
1. Klikněte na **vytvořit ID aplikace**. To může vyžadovat, abyste přijali zásady platformy Facebook a dokončili online kontrolu zabezpečení.
1. Vyberte **Nastavení** > **základní**.
1. Vyberte **kategorii**, například `Business and Pages`. Tuto hodnotu vyžaduje Facebook, ale nepoužívá ji Azure AD B2C.
1. V dolní části stránky vyberte **Přidat platformu**a pak vyberte **Web**.
1. Do pole **Adresa URL webu** `https://your-tenant-name.b2clogin.com/` zadejte `your-tenant-name` nahraďte názvem vašeho tenanta.
1. Zadejte adresu URL pro **zásadu ochrany osobních údajů**, například `http://www.contoso.com/`. Adresa URL zásad ochrany osobních údajů je stránka, kterou udržujete, aby poskytovala informace o ochraně osobních údajů pro vaši aplikaci.
1. Vyberte **Uložit změny**.
1. V horní části stránky si poznamenejte hodnotu **ID aplikace**.
1. Vedle **klíčového klíče aplikace**vyberte **Zobrazit** a zaznamenejte jeho hodnotu. K nakonfigurování Facebooku jako poskytovatele identity ve vašem tenantovi použijte ID aplikace i tajný klíč aplikace. **Tajný klíč aplikace** je důležité bezpečnostní pověření, které byste měli bezpečně ukládat.
1. Vyberte znaménko plus vedle položky **produkty**a potom v části **přihlášení na Facebooku**vyberte **nastavit**.
1. V části **přihlášení do Facebooku** v nabídce vlevo vyberte **Nastavení**.
1. Do **platného identifikátorů URI pro přesměrování OAuth**zadejte `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp`. Nahraďte `your-tenant-name` názvem vašeho tenanta. V dolní části stránky vyberte **Uložit změny** .
1. Chcete-li zpřístupnit aplikaci Facebook Azure AD B2C, klikněte na selektor **stavu** v pravém horním rohu stránky a zapněte ji tak , aby byla aplikace veřejná, a pak klikněte na **Potvrdit**. V tomto okamžiku se stav změní z **vývoje** na **Live**.

## <a name="add-the-identity-providers"></a>Přidat zprostředkovatele identity

Po vytvoření aplikace pro zprostředkovatele identity, který chcete přidat, přidejte poskytovatele identity do svého tenanta.

### <a name="add-the-azure-active-directory-identity-provider"></a>Přidat Azure Active Directory zprostředkovatele identity

1. Ujistěte se, že používáte adresář, který obsahuje vašeho tenanta Azure AD B2C kliknutím na **Filtr adresář a odběr** v horní nabídce a výběrem adresáře, který obsahuje klienta Azure AD B2C.
1. V levém horním rohu Azure Portal vyberte **všechny služby** a pak vyhledejte a vyberte **Azure AD B2C**.
1. Vyberte **zprostředkovatelé Identity**a pak vyberte **přidat**.
1. Zadejte **název**. Zadejte například *Contoso Azure AD*.
1. Vyberte **typ zprostředkovatele identity**, vyberte **OpenID připojit**a pak klikněte na **OK**.
1. Klikněte na **nastavit tohoto zprostředkovatele identity** .
1. V poli **Adresa URL metadat**zadejte následující adresu URL, `your-AD-tenant-domain` která nahrazuje název domény vašeho tenanta Azure AD.

    ```
    https://login.microsoftonline.com/your-AD-tenant-domain/.well-known/openid-configuration
    ```

    Například, `https://login.microsoftonline.com/contoso.onmicrosoft.com/.well-known/openid-configuration`.

1. Jako **ID klienta**zadejte *ID aplikace (klienta)* , které jste si poznamenali dříve.
1. Jako **tajný klíč klienta**zadejte hodnotu *tajného klíče klienta* , kterou jste předtím nahráli.
1. V případě potřeby zadejte hodnotu pro **Domain_hint**. Například, `ContosoAD`. [Pomocné parametry domény](../active-directory/manage-apps/configure-authentication-for-federated-users-portal.md) jsou direktivy, které jsou zahrnuté v žádosti o ověření z aplikace. Dají se použít k urychlení uživatele na přihlašovací stránku federovaného IdP. Nebo je můžou použít víceklientská aplikace k urychlení uživatele přímo na přihlašovací stránku služby Azure AD, která je označená pro svého tenanta.
1. Vyberte **OK**.
1. Vyberte **mapovat deklarace identity zprostředkovatele identity** a nastavte následující deklarace identity:

    - Jako **ID uživatele**zadejte `oid`.
    - Jako **Zobrazovaný název**zadejte `name`.
    - Pro **křestní jméno**zadejte `given_name`.
    - Jako **příjmení**zadejte `family_name`.
    - Do **e-mailu**zadejte `unique_name`.

1. Vyberte **OK**a pak vyberte **vytvořit** a uložte konfiguraci.

### <a name="add-the-facebook-identity-provider"></a>Přidat poskytovatele identity Facebooku

1. Vyberte **zprostředkovatelé Identity**a pak vyberte **přidat**.
1. Zadejte **název**. Zadejte například *Facebook*.
1. Vyberte **typ zprostředkovatele identity**, vyberte **Facebook**a pak vyberte **OK**.
1. Vyberte **nastavit tohoto zprostředkovatele identity** a jako **ID klienta**zadejte *ID aplikace* , které jste si poznamenali dříve.
1. Zadejte *tajný kód aplikace* , který jste si poznamenali jako **tajný klíč klienta**.
1. Vyberte **OK** a pak vyberte **vytvořit** a uložte svou konfiguraci Facebooku.

## <a name="update-the-user-flow"></a>Aktualizace toku uživatele

V kurzu, který jste dokončili v rámci požadavků, jste vytvořili uživatelský tok pro registraci a přihlašování s názvem *B2C_1_signupsignin1*. V této části přidáte zprostředkovatele identity do toku uživatele *B2C_1_signupsignin1* .

1. Vyberte **toky uživatelů (zásady)** a pak vyberte tok uživatele *B2C_1_signupsignin1* .
2. Vyberte **Zprostředkovatelé identity**a vyberte poskytovatele identit **Azure AD** , kterého jste přidali **Facebook** a contoso.
3. Vyberte **Uložit**.

## <a name="test-the-user-flow"></a>Testování toku uživatele

1. Na stránce Přehled toku uživatele, který jste vytvořili, vyberte **Spustit tok uživatele**.
1. V poli **aplikace**vyberte webovou aplikaci s názvem *WebApp1* , kterou jste předtím zaregistrovali. Měla by se zobrazit `https://jwt.ms` **Adresa URL odpovědi** .
1. Vyberte **Spustit tok uživatele**a pak se přihlaste pomocí poskytovatele identity, kterého jste předtím přidali.
1. Opakujte kroky 1 až 3 pro ostatní zprostředkovatele identity, které jste přidali.

Pokud je operace přihlášení úspěšná, budete přesměrováni na `https://jwt.ms` to, které zobrazí Dekódovatelné tokeny, podobně jako:

```json
{
  "typ": "JWT",
  "alg": "RS256",
  "kid": "<key-ID>"
}.{
  "exp": 1562346892,
  "nbf": 1562343292,
  "ver": "1.0",
  "iss": "https://your-b2c-tenant.b2clogin.com/10000000-0000-0000-0000-000000000000/v2.0/",
  "sub": "20000000-0000-0000-0000-000000000000",
  "aud": "30000000-0000-0000-0000-000000000000",
  "nonce": "defaultNonce",
  "iat": 1562343292,
  "auth_time": 1562343292,
  "name": "User Name",
  "idp": "facebook.com",
  "postalCode": "12345",
  "tfp": "B2C_1_signupsignin1"
}.[Signature]
```

## <a name="next-steps"></a>Další kroky

V tomto článku jste zjistili, jak:

> [!div class="checklist"]
> * Vytvoření aplikací zprostředkovatele identity
> * Přidejte zprostředkovatele identity do svého tenanta.
> * Přidání zprostředkovatelů identit do toku uživatele

V dalším kroku se dozvíte, jak přizpůsobit uživatelské rozhraní stránek zobrazených uživatelům v rámci prostředí identity v aplikacích:

> [!div class="nextstepaction"]
> [Přizpůsobení uživatelského rozhraní aplikací v Azure Active Directory B2C](tutorial-customize-ui.md)
