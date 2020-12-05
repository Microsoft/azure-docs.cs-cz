---
title: 'Kurz: Přidání poskytovatelů identit do aplikací'
titleSuffix: Azure AD B2C
description: V tomto kurzu se dozvíte, jak přidat zprostředkovatele identity k vašim aplikacím v Azure Active Directory B2C pomocí Azure Portal.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: tutorial
ms.date: 07/30/2020
ms.author: mimart
ms.subservice: B2C
ms.custom: fasttrack-edit
ms.openlocfilehash: 166bdb7a2cf15a84e1b826a9a798042c568bb227
ms.sourcegitcommit: 4c89d9ea4b834d1963c4818a965eaaaa288194eb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2020
ms.locfileid: "96608227"
---
# <a name="tutorial-add-identity-providers-to-your-applications-in-azure-active-directory-b2c"></a>Kurz: Přidání zprostředkovatelů identit do aplikací v Azure Active Directory B2C

V aplikacích můžete chtít povolit uživatelům, aby se přihlásili pomocí různých zprostředkovatelů identity. *Zprostředkovatel identity* vytváří, uchovává a spravuje informace o identitě při poskytování služeb ověřování aplikacím. Můžete přidat zprostředkovatele identity, které jsou podporovány aplikací Azure Active Directory B2C (Azure AD B2C), do [toků uživatelů](user-flow-overview.md) pomocí Azure Portal.

V tomto článku získáte informace o těchto tématech:

> [!div class="checklist"]
> * Vytvoření aplikací zprostředkovatele identity
> * Přidejte do svého tenanta poskytovatele identity – jak v Facebooku, tak v Azure Active Directory
> * Přidání zprostředkovatelů identit do toku uživatele

Ve svých aplikacích obvykle používáte pouze jednoho poskytovatele identity, ale máte možnost přidat další. V tomto kurzu se dozvíte, jak do vaší aplikace přidat poskytovatele identity Azure AD a poskytovatele identity Facebooku. Přidání obou těchto poskytovatelů identity do aplikace je volitelné. Můžete také přidat další zprostředkovatele identity, jako je [Amazon](identity-provider-amazon.md), [GitHub](identity-provider-github.md), [Google](identity-provider-google.md), [LinkedIn](identity-provider-linkedin.md), [Microsoft](identity-provider-microsoft-account.md)nebo [Twitter](identity-provider-twitter.md).

Pokud ještě nemáte předplatné Azure, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Předpoklady

[Vytvořte uživatelský tok](tutorial-create-user-flows.md), který uživatelům umožní registraci a přihlášení do vaší aplikace.

## <a name="create-applications"></a>Vytváření aplikací

Aplikace zprostředkovatele identity poskytují identifikátor a klíč, který umožňuje komunikaci s vaším klientem Azure AD B2C. V této části kurzu vytvoříte aplikaci Azure AD a aplikaci Facebook, ze které získáte identifikátory a klíče, pomocí kterých přidáte poskytovatele identity do svého tenanta. Pokud přidáváte pouze jednoho poskytovatele identity, stačí vytvořit aplikaci pro tohoto poskytovatele.

### <a name="create-an-azure-active-directory-application"></a>Vytvoření aplikace Azure Active Directory

Pokud chcete povolit přihlášení pro uživatele ze služby Azure AD, musíte zaregistrovat aplikaci v tenantovi Azure AD. Tenant Azure AD není stejný jako váš tenant Azure AD B2C.

1. Přihlaste se k [portálu Azure Portal](https://portal.azure.com).
1. Ujistěte se, že používáte adresář, který obsahuje vašeho tenanta Azure AD, a to tak, že v horní nabídce vyberete adresář a filtr **předplatného** a zvolíte adresář, který obsahuje vašeho TENANTA Azure AD.
1. V levém horním rohu Azure Portal vyberte **všechny služby** a pak vyhledejte a vyberte **Registrace aplikací**.
1. Vyberte **Nová registrace**.
1. Zadejte název své aplikace. Například, `Azure AD B2C App`.
1. Přijměte výběr **účtů v tomto organizačním adresáři pouze** pro tuto aplikaci.
1. Pro **identifikátor URI přesměrování** přijměte hodnotu **Web** a zadejte následující adresu URL na všechna malá písmena, nahraďte `your-B2C-tenant-name` názvem vašeho tenanta Azure AD B2C.

    ```
    https://your-B2C-tenant-name.b2clogin.com/your-B2C-tenant-name.onmicrosoft.com/oauth2/authresp
    ```

    Například, `https://contoso.b2clogin.com/contoso.onmicrosoft.com/oauth2/authresp`.

    Všechny adresy URL by teď měly používat [b2clogin.com](b2clogin.md).

1. Vyberte **Registrovat** a pak zaznamenejte **ID aplikace (klienta)** , kterou použijete v pozdějším kroku.
1. V nabídce **Spravovat** v nabídce aplikace vyberte **certifikáty & tajných** kódů a pak vyberte **nový tajný klíč klienta**.
1. Zadejte **Popis** tajného klíče klienta. Například, `Azure AD B2C App Secret`.
1. Vyberte období vypršení platnosti. Pro tuto aplikaci přijměte výběr **v 1 roce**.
1. Vyberte **Přidat** a potom zaznamenejte hodnotu nového tajného klíče klienta, který použijete v pozdějším kroku.

### <a name="create-a-facebook-application"></a>Vytvoření aplikace na Facebooku

Chcete-li použít účet Facebook jako poskytovatele identity v Azure AD B2C, je nutné vytvořit aplikaci na Facebooku. Pokud ještě nemáte účet Facebook, můžete ho získat na adrese [https://www.facebook.com/](https://www.facebook.com/) .

1. Přihlaste se ke [službě Facebook pro vývojáře](https://developers.facebook.com/) s přihlašovacími údaji k účtu Facebook.
1. Pokud jste to ještě neudělali, musíte se zaregistrovat jako vývojář Facebooku. Provedete to tak, **že vyberete Začínáme** v pravém horním rohu stránky, přijmete zásady Facebooku a dokončíte registrační kroky.
1. Vyberte **Moje aplikace** a pak **vytvořit aplikaci**.
1. Zadejte **Zobrazovaný název** a platný **kontaktní e-mail**.
1. Klikněte na **vytvořit ID aplikace**. To může vyžadovat, abyste přijali zásady platformy Facebook a dokončili online kontrolu zabezpečení.
1. Vyberte **Nastavení**  >  **základní**.
1. Vyberte **kategorii**, například `Business and Pages` . Tuto hodnotu vyžaduje Facebook, ale nepoužívá ji Azure AD B2C.
1. V dolní části stránky vyberte **Přidat platformu** a pak vyberte **Web**.
1. Do pole **Adresa URL webu** zadejte `https://your-tenant-name.b2clogin.com/` nahraďte `your-tenant-name` názvem vašeho tenanta.
1. Zadejte adresu URL pro **zásadu ochrany osobních údajů**, například `http://www.contoso.com/` . Adresa URL zásad ochrany osobních údajů je stránka, kterou udržujete, aby poskytovala informace o ochraně osobních údajů pro vaši aplikaci.
1. Vyberte **Uložit změny**.
1. V horní části stránky si poznamenejte hodnotu **ID aplikace**.
1. Vedle **klíčového klíče aplikace** vyberte **Zobrazit** a zaznamenejte jeho hodnotu. K nakonfigurování Facebooku jako poskytovatele identity ve vašem tenantovi použijte ID aplikace i tajný klíč aplikace. **Tajný klíč aplikace** je důležité bezpečnostní pověření, které byste měli bezpečně ukládat.
1. Vyberte znaménko plus vedle položky **produkty** a potom v části **přihlášení na Facebooku** vyberte **nastavit**.
1. V části **přihlášení do Facebooku** v nabídce vlevo vyberte **Nastavení**.
1. Do **platného identifikátorů URI pro přesměrování OAuth** zadejte `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` . Nahraďte `your-tenant-name` názvem vašeho tenanta. V dolní části stránky vyberte **Uložit změny** .
1. Chcete-li zpřístupnit aplikaci Facebook Azure AD B2C, klikněte na selektor **stavu** v pravém horním rohu stránky a zapněte **ji tak** , aby byla aplikace veřejná, a pak klikněte na **Potvrdit**. V tomto okamžiku se stav změní z **vývoje** na **Live**.

## <a name="add-the-identity-providers"></a>Přidat zprostředkovatele identity

Po vytvoření aplikace pro zprostředkovatele identity, který chcete přidat, přidejte poskytovatele identity do svého tenanta.

### <a name="add-the-azure-active-directory-identity-provider"></a>Přidat Azure Active Directory zprostředkovatele identity

1. Ujistěte se, že používáte adresář, který obsahuje Azure AD B2C tenanta. V horní nabídce vyberte filtr **adresář + odběr** a zvolte adresář, který obsahuje vašeho tenanta Azure AD B2C.
1. V levém horním rohu Azure Portal vyberte **všechny služby** a pak vyhledejte a vyberte **Azure AD B2C**.
1. Vyberte **Zprostředkovatelé identity** a potom vyberte **Nový poskytovatel OpenID Connect**.
1. Zadejte **název**. Zadejte například *Contoso Azure AD*.
1. V poli **Adresa URL metadat** zadejte následující adresu URL, která nahrazuje `{tenant}` název domény vašeho tenanta Azure AD:

    ```
    https://login.microsoftonline.com/{tenant}/v2.0/.well-known/openid-configuration
    ```

    Například, `https://login.microsoftonline.com/contoso.onmicrosoft.com/v2.0/.well-known/openid-configuration`.
    Například, `https://login.microsoftonline.com/contoso.com/v2.0/.well-known/openid-configuration`.

1. Jako **ID klienta** zadejte ID aplikace, které jste si poznamenali dříve.
1. Jako **tajný klíč klienta** zadejte tajný klíč klienta, který jste předtím nahráli.
1. Pro **Rozsah** zadejte `openid profile` .
1. Ponechte výchozí hodnoty pro **typ odpovědi** a **režim odezvy**.
1. Volitelné Jako **nápovědu k doméně** zadejte `contoso.com` . Další informace najdete v tématu [Nastavení přímého přihlašování pomocí Azure Active Directory B2C](direct-signin.md#redirect-sign-in-to-a-social-provider).
1. V části **mapování deklarací identity zprostředkovatele identity** vyberte následující deklarace identity:

    * **ID uživatele**: *OID*
    * **Zobrazovaný název**: *název*
    * **Křestní jméno**: *given_name*
    * **Příjmení**: *family_name*
    * **E-mail**: *unique_name*

1. Vyberte **Uložit**.

### <a name="add-the-facebook-identity-provider"></a>Přidat poskytovatele identity Facebooku

1. Vyberte **Zprostředkovatelé identity** a pak vyberte **Facebook**.
1. Zadejte **název**. Například *Facebook*.
1. Jako **ID klienta** zadejte ID aplikace Facebook, kterou jste vytvořili dříve.
1. Pro **tajný klíč klienta** zadejte tajný kód aplikace, který jste si poznamenali.
1. Vyberte **Uložit**.

## <a name="update-the-user-flow"></a>Aktualizace toku uživatele

V kurzu, který jste dokončili v rámci požadavků, jste vytvořili uživatelský tok pro registraci a přihlašování s názvem *B2C_1_signupsignin1*. V této části přidáte zprostředkovatele identity do toku *B2C_1_signupsignin1ho* uživatele.

1. Vyberte **toky uživatelů** a potom vyberte tok uživatele *B2C_1_signupsignin1* .
2. Vyberte **Zprostředkovatelé identity** a vyberte poskytovatele identit **Azure AD** , kterého jste přidali **Facebook** a contoso.
3. Vyberte **Uložit**.

## <a name="test-the-user-flow"></a>Testování toku uživatele

1. Na stránce Přehled toku uživatele, který jste vytvořili, vyberte **Spustit tok uživatele**.
1. V poli **aplikace** vyberte webovou aplikaci s názvem *WebApp1* , kterou jste předtím zaregistrovali. Měla by se zobrazit **Adresa URL odpovědi** `https://jwt.ms` .
1. Vyberte **Spustit tok uživatele** a pak se přihlaste pomocí poskytovatele identity, kterého jste předtím přidali.
1. Opakujte kroky 1 až 3 pro ostatní zprostředkovatele identity, které jste přidali.

Pokud je operace přihlášení úspěšná, budete přesměrováni na to, `https://jwt.ms` které zobrazí Dekódovatelné tokeny, podobně jako:

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
