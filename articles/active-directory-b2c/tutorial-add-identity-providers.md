---
title: Kurz – přidání zprostředkovatele identity do vašich aplikací – Azure Active Directory B2C
description: Zjistěte, jak přidat zprostředkovatelů identity pro vaše aplikace v Azure Active Directory B2C na webu Azure portal.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 07/08/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 33f595dd36ac9448cc1276647f9943326b0b74c1
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/08/2019
ms.locfileid: "67655226"
---
# <a name="tutorial-add-identity-providers-to-your-applications-in-azure-active-directory-b2c"></a>Kurz: Přidat zprostředkovatele identity pro vaše aplikace v Azure Active Directory B2C

Ve svých aplikacích můžete umožnit uživatelům přihlášení pomocí poskytovatelů jiná identita. *Zprostředkovatele identity* vytváří, udržuje a spravuje informace o identitě současně ověřovací služby do aplikací. Můžete přidat zprostředkovatelů identity, které jsou podporovány službou Azure Active Directory (Azure AD) B2C k vaší [toky uživatelů](active-directory-b2c-reference-policies.md) pomocí webu Azure portal.

V tomto článku získáte informace o těchto tématech:

> [!div class="checklist"]
> * Vytváření aplikací zprostředkovatele identity
> * Přidat zprostředkovatele identity do vašeho tenanta
> * Přidat zprostředkovatele identity do vašeho toku uživatele

Obvykle používají pouze jeden poskytovatel identity ve svých aplikacích, ale máte možnost pro přidání dalších. V tomto kurzu se dozvíte, jak přidat do svojí aplikace zprostředkovatele identity Azure AD a zprostředkovatele identity Facebook. Přidání oba z těchto zprostředkovatelů identity pro vaše aplikace je nepovinné. Můžete také přidat jiných zprostředkovatelů identity, jako například [Amazon](active-directory-b2c-setup-amzn-app.md), [Githubu](active-directory-b2c-setup-github-app.md), [Google](active-directory-b2c-setup-goog-app.md), [LinkedIn](active-directory-b2c-setup-li-app.md), [Microsoft](active-directory-b2c-setup-msa-app.md), nebo [Twitter](active-directory-b2c-setup-twitter-app.md).

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

[Vytvořit tok uživatele](tutorial-create-user-flows.md) umožňují uživatelům registraci a přihlášení do vaší aplikace.

## <a name="create-applications"></a>Vytváření aplikací

Aplikace zprostředkovatele identity poskytují identifikátor a klíč k povolení komunikace pomocí vašeho tenanta Azure AD B2C. V této části kurzu vytvoříte aplikaci Azure AD a aplikace Facebook, ze kterého získáte identifikátory a klíče pro přidání poskytovatele identit pro vašeho tenanta. Pokud přidáváte některou ze zprostředkovatelů identity, stačí vytvořit aplikaci pro daného poskytovatele.

### <a name="create-an-azure-active-directory-application"></a>Vytvoření aplikace Azure Active Directory

Pokud chcete povolit přihlášení pro uživatele ze služby Azure AD, budete muset zaregistrovat aplikaci v tenantovi Azure AD. Tenant Azure AD není stejný jako vašeho tenanta Azure AD B2C.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
1. Ujistěte se, že používáte adresáře, který obsahuje váš tenant Azure AD kliknutím **filtr adresářů a předplatných** v horní nabídce a výběrem adresáře, který obsahuje váš tenant Azure AD.
1. Zvolte **všechny služby** v horním levém horním rohu webu Azure portal a poté vyhledejte a vyberte **registrace aplikací**.
1. Vyberte **registrace nové**.
1. Zadejte název své aplikace. Například, `Azure AD B2C App`.
1. Přijmout výběr **účty v tomto adresáři organizace jenom** pro tuto aplikaci.
1. Pro **identifikátor URI pro přesměrování**, přijměte hodnotu **webové** a zadejte následující adresu URL malými písmeny, nahrazení `your-B2C-tenant-name` s názvem vašeho tenanta Azure AD B2C.

    ```
    https://your-B2C-tenant-name.b2clogin.com/your-B2C-tenant-name.onmicrosoft.com/oauth2/authresp
    ```

    Například, `https://contoso.b2clogin.com/contoso.onmicrosoft.com/oauth2/authresp`.

    Všechny adresy URL by měla nyní být pomocí [b2clogin.com](b2clogin.md).

1. Vyberte **zaregistrovat**, pak záznam **ID aplikace (klient)** který můžete použít v pozdějším kroku.
1. V části **spravovat** v nabídce aplikace vyberte **certifikáty a tajné kódy**a pak vyberte **nový tajný kód klienta**.
1. Zadejte **popis** tajný kód klienta. Například, `Azure AD B2C App Secret`.
1. Vyberte dobu platnosti. Pro tuto aplikaci přijmout výběr **v 1 rok**.
1. Vyberte **přidat**, potom si poznamenejte hodnotu nový tajný kód klienta, který můžete použít v pozdějším kroku.

### <a name="create-a-facebook-application"></a>Vytvořit aplikaci pro Facebook

Použití účtu sítě Facebook jako zprostředkovatele identity v Azure AD B2C, budete muset vytvořit aplikaci na Facebooku. Pokud ještě nemáte účet na Facebooku, získáte ji na [ https://www.facebook.com/ ](https://www.facebook.com/).

1. Přihlaste se k [Facebook pro vývojáře](https://developers.facebook.com/) pomocí svých přihlašovacích údajů účtu sítě Facebook.
1. Pokud jste tak již neučinili, musíte zaregistrovat jako vývojář sítě Facebook. Chcete-li to provést, vyberte **Začínáme** v pravém horním rohu stránky, přijmout zásady na Facebooku a dokončete registraci.
1. Vyberte **Moje aplikace** a potom **vytvoření aplikace**.
1. Zadejte **zobrazovaný název** a platný **E-mail kontaktu**.
1. Klikněte na tlačítko **vytvoření ID aplikace**. To může vyžadovat přijmout zásady platformy Facebooku a proveďte kontrolu zabezpečení online.
1. Vyberte **nastavení** > **základní**.
1. Zvolte **kategorie**, například `Business and Pages`. Tato hodnota vyžaduje Facebooku, ale nepoužívají Azure AD B2C.
1. V dolní části stránky vyberte **přidat platformy**a pak vyberte **webu**.
1. V **adresa URL webu**, zadejte `https://your-tenant-name.b2clogin.com/` nahrazení `your-tenant-name` s názvem vašeho tenanta.
1. Zadejte adresu URL, **adresa URL zásad ochrany osobních údajů**, například `http://www.contoso.com/`. Adresa URL zásad ochrany osobních údajů je stránka, které spravujete, k poskytnutí informací o ochraně osobních údajů pro vaši aplikaci.
1. Vyberte **uložit změny**.
1. V horní části stránky zaznamenaná hodnota **ID aplikace**.
1. Vedle položky **tajný kód aplikace**vyberte **zobrazit** a její hodnotu si poznamenejte. ID aplikace a tajný kód aplikace použijete ke konfiguraci sítě Facebook jako zprostředkovatele identity ve vašem tenantovi. **Tajný kód aplikace** je důležitým bezpečnostním pověřením, které byste měli bezpečně uložit.
1. Vyberte znaménko plus vedle **produkty**, pak v oblasti **přihlášení k Facebooku**vyberte **nastavit**.
1. V části **přihlášení k Facebooku** v nabídce vlevo vyberte **nastavení**.
1. V **identifikátory URI pro přesměrování OAuth platný**, zadejte `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp`. Nahraďte `your-tenant-name` s názvem vašeho tenanta. Vyberte **uložit změny** v dolní části stránky.
1. K zajištění dostupnosti sítě Facebook aplikace Azure AD B2C, klikněte na tlačítko **stav** selektoru v horní části stránky a znovu je zapnout **na** zveřejnit aplikaci, a potom klikněte na **potvrzení** . V tomto okamžiku by měl stav změnit z **vývoj** k **Live**.

## <a name="add-the-identity-providers"></a>Přidat zprostředkovatele identity

Po vytvoření aplikace pro zprostředkovatele identity, které chcete přidat, přidejte zprostředkovatele identity do vašeho tenanta.

### <a name="add-the-azure-active-directory-identity-provider"></a>Přidat zprostředkovatele identity Azure Active Directory

1. Ujistěte se, že používáte adresáře, který obsahuje vašeho tenanta Azure AD B2C kliknutím **filtr adresářů a předplatných** v horní nabídce a výběrem adresáře, který obsahuje vašeho tenanta Azure AD B2C.
1. Zvolte **všechny služby** v horním levém horním rohu webu Azure portal a poté vyhledejte a vyberte **Azure AD B2C**.
1. Vyberte **zprostředkovatelé Identity**a pak vyberte **přidat**.
1. Zadejte **název**. Zadejte například *Contoso Azure AD*.
1. Vyberte **typ zprostředkovatele identit**vyberte **Open ID Connect (verze Preview)** a potom klikněte na tlačítko **OK**.
1. Klikněte na tlačítko **nastavit tohoto zprostředkovatele identity**
1. Pro **adresa url metadat**, zadejte následující adresu URL, nahrazení `your-AD-tenant-domain` s názvem domény vašeho tenanta Azure AD.

    ```
    https://login.microsoftonline.com/your-AD-tenant-domain/.well-known/openid-configuration
    ```

    Například, `https://login.microsoftonline.com/contoso.onmicrosoft.com/.well-known/openid-configuration`.

1. Pro **ID klienta**, zadejte *ID aplikace (klient)* , který jste si předtím poznamenali.
1. Pro **tajný kód klienta**, zadejte *tajný kód klienta* hodnotu, která jste si předtím poznamenali.
1. Volitelně můžete zadat hodnotu pro **Domain_hint**. Například, `ContosoAD`. [Pomocné parametry domény](../active-directory/manage-apps/configure-authentication-for-federated-users-portal.md) jsou direktivy, které jsou zahrnuty v žádosti o ověření z aplikace. Umožňuje zrychlit uživatele na federované IdP přihlašovací stránku. Nebo můžete být používají v aplikaci s více tenanty zrychlí uživatele přímo obchodní značku služby Azure AD přihlašovací stránku pro svého tenanta.
1. Vyberte **OK**.
1. Vyberte **mapování deklarací od zprostředkovatele identity** a nastavte následující deklarace:

    - Pro **ID uživatele**, zadejte `oid`.
    - Pro **zobrazovaný název**, zadejte `name`.
    - Pro **křestní jméno**, zadejte `given_name`.
    - Pro **příjmení**, zadejte `family_name`.
    - Pro **e-mailu**, zadejte `unique_name`.

1. Vyberte **OK**a pak vyberte **vytvořit** uložte konfiguraci.

### <a name="add-the-facebook-identity-provider"></a>Přidat zprostředkovatele identity Facebook

1. Vyberte **zprostředkovatelé Identity**a pak vyberte **přidat**.
1. Zadejte **název**. Zadejte například *Facebook*.
1. Vyberte **typ zprostředkovatele identit**vyberte **Facebook**a pak vyberte **OK**.
1. Vyberte **nastavit tohoto zprostředkovatele identity** a zadejte *ID aplikace* , který jste si poznamenali dříve, jako **ID klienta**.
1. Zadejte *tajný kód aplikace* , který jste si poznamenali jako **tajný kód klienta**.
1. Vyberte **OK** a pak vyberte **vytvořit** uložte konfiguraci sítě Facebook.

## <a name="update-the-user-flow"></a>Aktualizovat tok uživatele.

V tomto kurzu jste dokončili jako součást požadavků vytvořili tok uživatele pro registraci a přihlašování s názvem *B2C_1_signupsignin1*. V této části přidáte zprostředkovatelů identity pro *B2C_1_signupsignin1* tok uživatele.

1. Vyberte **toky uživatelů (zásady)** a pak vyberte *B2C_1_signupsignin1* tok uživatele.
2. Vyberte **zprostředkovatelé Identity**, vyberte **Facebook** a **Contoso Azure AD** zprostředkovatelů identity, které jste přidali.
3. Vyberte **Uložit**.

## <a name="test-the-user-flow"></a>Testování tohoto toku uživatele

1. Na stránce Přehled toku uživatele, který jste vytvořili, vyberte **spustit tok uživatele**.
1. Pro **aplikace**, vyberte webovou aplikaci s názvem *webapp1* , které jste dříve zaregistrovali. **Adresy URL odpovědi** by se zobrazit `https://jwt.ms`.
1. Vyberte **spustit tok uživatele**a potom Přihlaste se pomocí zprostředkovatele identity, který jste přidali dříve.
1. Opakujte kroky 1 až 3 pro ostatní poskytovatele identit, které jste přidali.

Pokud je přihlášení operace úspěšná, budete přesměrováni na `https://jwt.ms` zobrazuje dekódovat Token, podobně jako:

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

## <a name="next-steps"></a>Další postup

V tomto článku jste zjistili, jak:

> [!div class="checklist"]
> * Vytváření aplikací zprostředkovatele identity
> * Přidat zprostředkovatele identity do vašeho tenanta
> * Přidat zprostředkovatele identity do vašeho toku uživatele

Dále se naučíte k přizpůsobení uživatelského rozhraní stránky, který se uživatelům zobrazí jako součást jejich prostředí identit ve vašich aplikacích:

> [!div class="nextstepaction"]
> [Přizpůsobení uživatelského rozhraní svých aplikací v Azure Active Directory B2C](tutorial-customize-ui.md)
