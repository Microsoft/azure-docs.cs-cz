---
title: 'Kurz: Přidání poskytovatelů identit do svých aplikací'
titleSuffix: Azure AD B2C
description: Zjistěte, jak přidat poskytovatele identit do vašich aplikací ve službě Azure Active Directory B2C pomocí webu Azure Portal.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 07/08/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 1f49061210ca8e3c106b0569f77a67d1f10757a1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78183512"
---
# <a name="tutorial-add-identity-providers-to-your-applications-in-azure-active-directory-b2c"></a>Kurz: Přidání poskytovatelů identit do aplikací ve službě Azure Active Directory B2C

Ve vašich aplikacích můžete povolit uživatelům přihlásit se pomocí různých poskytovatelů identity. *Zprostředkovatel identity* vytváří, udržuje a spravuje informace o identitě při poskytování ověřovacích služeb aplikacím. Pomocí portálu Azure Portal můžete přidat zprostředkovatele identit, které podporuje Azure Active Directory B2C (Azure AD B2C) do [svých uživatelských toků.](user-flow-overview.md)

V tomto článku získáte informace o těchto tématech:

> [!div class="checklist"]
> * Vytvoření aplikací zprostředkovatele identity
> * Přidání poskytovatelů identit do tenanta
> * Přidání poskytovatelů identit do toku uživatelů

Ve svých aplikacích obvykle používáte pouze jednoho zprostředkovatele identity, ale máte možnost přidat další. Tento kurz ukazuje, jak do vaší aplikace přidat poskytovatele identity Azure AD a poskytovatele identity Facebooku. Přidání obou těchto zprostředkovatelů identity do vaší aplikace je volitelné. Můžete také přidat další poskytovatele identit, například [Amazon](identity-provider-amazon.md), [GitHub](identity-provider-github.md), [Google](identity-provider-google.md), [LinkedIn](identity-provider-linkedin.md), [Microsoft](identity-provider-microsoft-account.md)nebo [Twitter](identity-provider-twitter.md).

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete.

## <a name="prerequisites"></a>Požadavky

[Vytvořte tok uživatelů,](tutorial-create-user-flows.md) který uživatelům umožní přihlásit se a přihlásit se k vaší aplikaci.

## <a name="create-applications"></a>Vytváření aplikací

Aplikace zprostředkovatele identity poskytují identifikátor a klíč, které umožňují komunikaci s klientem Azure AD B2C. V této části kurzu vytvoříte aplikaci Azure AD a aplikaci Facebook, ze které získáte identifikátory a klíče pro přidání zprostředkovatelů identit do vašeho tenanta. Pokud přidáváte pouze jednoho z poskytovatelů identity, stačí vytvořit aplikaci pro tohoto zprostředkovatele.

### <a name="create-an-azure-active-directory-application"></a>Vytvoření aplikace Azure Active Directory

Chcete-li povolit přihlášení pro uživatele z Azure AD, musíte zaregistrovat aplikaci v rámci klienta Azure AD. Tenant Azure AD není stejný jako váš klient Azure AD B2C.

1. Přihlaste se k [portálu Azure](https://portal.azure.com).
1. Ujistěte se, že používáte adresář, který obsahuje vašeho klienta Azure AD výběrem filtru **directory + předplatné** v horní nabídce a výběrem adresáře, který obsahuje vašeho klienta Azure AD.
1. V levém horním rohu portálu Azure zvolte **Všechny služby** a pak **vyhledejte**a vyberte registrace aplikací .
1. Vyberte **možnost Nová registrace**.
1. Zadejte název své aplikace. Například, `Azure AD B2C App`.
1. Přijměte výběr **účtů v tomto organizačním adresáři pouze** pro tuto aplikaci.
1. Pro **identifikátor URI přesměrování**přijměte hodnotu **webu** a zadejte následující `your-B2C-tenant-name` adresu URL ve všech malých písmenech a nahraďte jej názvem klienta Azure AD B2C.

    ```
    https://your-B2C-tenant-name.b2clogin.com/your-B2C-tenant-name.onmicrosoft.com/oauth2/authresp
    ```

    Například, `https://contoso.b2clogin.com/contoso.onmicrosoft.com/oauth2/authresp`.

    Všechny adresy URL by nyní měly používat [b2clogin.com](b2clogin.md).

1. Vyberte **Registrovat**, pak zaznamenejte **ID aplikace (klienta),** které použijete v pozdějším kroku.
1. V části **Spravovat** v nabídce aplikace vyberte **Certifikáty & tajných kódů**a pak vyberte **Nový tajný klíč klienta**.
1. Zadejte **popis** tajného klíče klienta. Například, `Azure AD B2C App Secret`.
1. Vyberte dobu vypršení platnosti. Pro tuto žádost, přijmout výběr **Do 1 roku**.
1. Vyberte **Přidat**a zaznamenejte hodnotu nového tajného klíče klienta, který použijete v pozdějším kroku.

### <a name="create-a-facebook-application"></a>Vytvoření aplikace pro Facebook

Pokud chcete ve Službě Azure AD B2C používat facebookový účet jako poskytovatele identity, musíte na Facebooku vytvořit aplikaci. Pokud ještě nemáte facebookový účet, můžete ho [https://www.facebook.com/](https://www.facebook.com/)získat na webu .

1. Přihlaste se na [Facebook pro vývojáře](https://developers.facebook.com/) s přihlašovacími údaji svého účtu na Facebooku.
1. Pokud jste tak ještě neučinili, musíte se zaregistrovat jako vývojář facebooku. Chcete-li to provést, vyberte **Možnost Začínáme** v pravém horním rohu stránky, přijměte zásady Facebooku a proveďte kroky registrace.
1. Vyberte **Moje aplikace** a potom **Vytvořte aplikaci**.
1. Zadejte **zobrazované jméno** a platný **kontaktní e-mail**.
1. Klikněte na **Vytvořit ID aplikace**. To může vyžadovat, abyste přijali zásady platformy Facebook a dokončili online kontrolu zabezpečení.
1. Vyberte **základní nastavení** > **.**
1. Zvolte **Category**například `Business and Pages`kategorii . Tato hodnota je vyžadována Facebookem, ale nepoužívá azure ad B2C.
1. V dolní části stránky vyberte **Přidat platformu**a pak vyberte **Web**.
1. Do pole Adresa `https://your-tenant-name.b2clogin.com/` URL `your-tenant-name` **webu**zadejte nahrazení názvem vašeho tenanta.
1. Zadejte adresu URL **adresy URL zásad ochrany osobních údajů**, například `http://www.contoso.com/`. Adresa URL zásad ochrany osobních údajů je stránka, kterou spravujete za účelem poskytnutí informací o ochraně osobních údajů pro vaši aplikaci.
1. Vyberte **Uložit změny**.
1. V horní části stránky zaznamenejte hodnotu **ID aplikace**.
1. Vedle **položky Tajný klíč aplikace**vyberte **Zobrazit** a zaznamenejte jeho hodnotu. ID aplikace i tajný klíč aplikace slouží ke konfiguraci Facebooku jako poskytovatele identity ve vašem tenantovi. **App Secret** je důležité bezpečnostní pověření, které byste měli bezpečně ukládat.
1. Vyberte znaménko plus vedle **položky PRODUKTY**, pak v části **Facebook Přihlášení**vyberte **Nastavit**.
1. V části **Facebook Přihlášení** v levé nabídce vyberte **Nastavení**.
1. V platném rozhraní **URI přesměrování OAuth**zadejte `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp`. Nahraďte `your-tenant-name` se jménem svého tenanta. V dolní části stránky vyberte **Uložit změny.**
1. Pokud chcete, aby vaše facebooková aplikace byla dostupná pro Azure AD B2C, klikněte na volič **stavu** v pravém horním rohu stránky a **zapněte** ho, aby byla aplikace veřejná, a potom klikněte na **Potvrdit**. V tomto okamžiku stav by měl změnit z **vývoje** na **Live**.

## <a name="add-the-identity-providers"></a>Přidání zprostředkovatelů identit

Po vytvoření aplikace pro zprostředkovatele identity, který chcete přidat, přidáte zprostředkovatele identity do tenanta.

### <a name="add-the-azure-active-directory-identity-provider"></a>Přidání zprostředkovatele identity služby Azure Active Directory

1. Ujistěte se, že používáte adresář, který obsahuje klienta Azure AD B2C. V horní nabídce vyberte filtr **Directory + subscription** a zvolte adresář, který obsahuje vašeho klienta Azure AD B2C.
1. V levém horním rohu portálu Azure zvolte **Všechny služby** a pak vyhledejte a vyberte **Azure AD B2C**.
1. Vyberte **Zprostředkovatelé identity**a pak vyberte **Nový zprostředkovatel EOpenID Connect**.
1. Zadejte **název**. Zadejte například *Contoso Azure AD*.
1. Do **adresy URL metadat**zadejte `your-AD-tenant-domain` následující adresu URL, která nahradí název domény vašeho klienta Azure AD:

    ```
    https://login.microsoftonline.com/your-AD-tenant-domain/.well-known/openid-configuration
    ```

    Například, `https://login.microsoftonline.com/contoso.onmicrosoft.com/.well-known/openid-configuration`.

1. Pro **ID klienta**zadejte ID aplikace, které jste dříve zaznamenali.
1. U **tajného klíče klienta**zadejte tajný klíč klienta, který jste dříve zaznamenali.
1. Ponechte výchozí hodnoty pro **obor**, **typ odpovědi**a **režim odezvy**.
1. (Nepovinné) Zadejte hodnotu pro **Domain_hint**. Například *ContosoAD*. [Rady při psaní domény](../active-directory/manage-apps/configure-authentication-for-federated-users-portal.md) jsou direktivy, které jsou zahrnuty v požadavku na ověření z aplikace. Lze je použít k urychlení uživatele na federované přihlašovací stránce IdP. Nebo je může použít víceklientská aplikace k urychlení uživatele přímo na značkovou přihlašovací stránku Azure AD pro svého tenanta.
1. V části **Mapování deklarací identity**zadejte následující hodnoty mapování deklarací identity:

    * **ID uživatele**: *oid*
    * **Zobrazovaný název**: *jméno*
    * **Křestní jméno**: *given_name*
    * **Příjmení**: *family_name*
    * **E-mail**: *unique_name*

1. Vyberte **Uložit**.

### <a name="add-the-facebook-identity-provider"></a>Přidání poskytovatele identity na Facebooku

1. Vyberte **Zprostředkovatelé identity**, pak vyberte **Facebook**.
1. Zadejte **název**. Například *Facebook*.
1. Pro **ID klienta**zadejte ID aplikace aplikace Facebook, kterou jste vytvořili dříve.
1. U **tajného klíče klienta**zadejte tajný klíč aplikace, který jste nahráli.
1. Vyberte **Uložit**.

## <a name="update-the-user-flow"></a>Aktualizace toku uživatelů

V kurzu, který jste dokončili jako součást předpokladů, jste vytvořili tok uživatele pro registraci a přihlášení s názvem *B2C_1_signupsignin1*. V této části přidáte zprostředkovatele identity do *toku B2C_1_signupsignin1* uživatele.

1. Vyberte **Toky uživatelů (zásady)** a pak vyberte *B2C_1_signupsignin1* toku uživatele.
2. Vyberte **Zprostředkovatelé identity**, vyberte zprostředkovatele identity **Facebook** a **Contoso Azure AD,** které jste přidali.
3. Vyberte **Uložit**.

## <a name="test-the-user-flow"></a>Testování toku uživatele

1. Na stránce Přehled vytvořeného toku uživatele vyberte **Spustit tok uživatele**.
1. V **aplikaci**vyberte webovou aplikaci s názvem *webapp1,* kterou jste dříve zaregistrovali. Adresa **URL** odpovědi `https://jwt.ms`by se měla zobrazit .
1. Vyberte **Spustit tok uživatele**a přihlaste se pomocí zprostředkovatele identity, který jste dříve přidali.
1. Opakujte kroky 1 až 3 pro ostatní zprostředkovatele identity, které jste přidali.

Pokud je operace přihlášení úspěšná, budete `https://jwt.ms` přesměrováni na který se zobrazí dekódovaný token, podobně jako:

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

V tomto článku jste se naučili:

> [!div class="checklist"]
> * Vytvoření aplikací zprostředkovatele identity
> * Přidání poskytovatelů identit do tenanta
> * Přidání poskytovatelů identit do toku uživatelů

Dále se dozvíte, jak přizpůsobit uživatelské prostředí stránek zobrazených uživatelům jako součást jejich identity prostředí ve vašich aplikacích:

> [!div class="nextstepaction"]
> [Přizpůsobení uživatelského rozhraní aplikací ve službě Azure Active Directory B2C](tutorial-customize-ui.md)
