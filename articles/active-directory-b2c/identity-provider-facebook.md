---
title: Nastavení registrace a přihlášení pomocí účtu Facebook
titleSuffix: Azure AD B2C
description: Poskytněte zákazníkům služby pro registraci a přihlašování k aplikacím Facebook v aplikacích pomocí Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 01/19/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 64a4404fa881181f92d442a73e5da4c16ae87ae3
ms.sourcegitcommit: fc401c220eaa40f6b3c8344db84b801aa9ff7185
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/20/2021
ms.locfileid: "98598880"
---
# <a name="set-up-sign-up-and-sign-in-with-a-facebook-account-using-azure-active-directory-b2c"></a>Nastavení registrace a přihlášení pomocí účtu Facebook pomocí Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-custom-policy"

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

::: zone-end

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="create-a-facebook-application"></a>Vytvoření aplikace na Facebooku

Pokud chcete povolit přihlášení pro uživatele s účtem Facebook v Azure Active Directory B2C (Azure AD B2C), musíte na [řídicím panelu aplikace pro Facebook](https://developers.facebook.com/)vytvořit aplikaci. Další informace najdete v tématu [vývoj aplikací](https://developers.facebook.com/docs/development). Pokud ještě nemáte účet Facebook, můžete se zaregistrovat [https://www.facebook.com/](https://www.facebook.com/) .

1. Přihlaste se ke [službě Facebook pro vývojáře](https://developers.facebook.com/) s přihlašovacími údaji k účtu Facebook.
1. Pokud jste to ještě neudělali, musíte se zaregistrovat jako vývojář Facebooku. Provedete to tak, **že vyberete Začínáme** v pravém horním rohu stránky, přijmete zásady Facebooku a dokončíte registrační kroky.
1. Vyberte **Moje aplikace** a pak **vytvořit aplikaci**.
1. Vyberte možnost **sestavit připojené prostředí**.
1. Zadejte **Zobrazovaný název** a platný **kontaktní e-mail**.
1. Vyberte **vytvořit ID aplikace**. To může vyžadovat, abyste přijali zásady platformy Facebook a dokončili online kontrolu zabezpečení.
1. Vyberte **Nastavení**  >  **základní**.
    1. Vyberte **kategorii**, například `Business and Pages` . Tuto hodnotu vyžaduje Facebook, ale nepoužívá se pro Azure AD B2C.
    1. Zadejte adresu URL podmínek pro **adresu URL služby**, například `http://www.contoso.com/tos` . Adresa URL zásad je stránka, kterou udržujete pro poskytování podmínek a ujednání pro vaši aplikaci.
    1. Zadejte adresu URL pro **zásadu ochrany osobních údajů**, například `http://www.contoso.com/privacy` . Adresa URL zásad je stránka, kterou udržujete pro poskytování informací o ochraně osobních údajů pro vaši aplikaci.
1. V dolní části stránky vyberte **Přidat platformu** a pak vyberte **Web**.
1. Do pole **Adresa URL webu** zadejte adresu vašeho webu, například `https://contoso.com` . 
1. Vyberte **Uložit změny**.
1. V horní části stránky zkopírujte hodnotu **ID aplikace**.
1. Vyberte **Zobrazit** a zkopírujte hodnotu **tajného kódu aplikace**. Pomocí obou z nich můžete nakonfigurovat Facebook jako poskytovatele identity ve vašem tenantovi. **Tajný klíč aplikace** je důležité bezpečnostní pověření.
1. V nabídce vyberte symbol **plus** vedle nabídky **produkty**. V části **Přidat produkty do aplikace** vyberte možnost **nastavit** v části **přihlášení do Facebooku**.
1. V nabídce vyberte **přihlašovací jméno na Facebooku** a pak vyberte **Nastavení**.
1. Do **platného identifikátorů URI pro přesměrování OAuth** zadejte `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` . Nahraďte `your-tenant-name` názvem vašeho tenanta. V dolní části stránky vyberte **Uložit změny** .
1. Chcete-li zpřístupnit aplikaci Facebook Azure AD B2C, vyberte selektor **stavu v pravém** horním rohu stránky a zapněte ji tak, aby byla aplikace veřejná, a pak vyberte **přepínač režim**.  V tomto okamžiku se stav změní z **vývoje** na **Live**.

::: zone pivot="b2c-user-flow"

## <a name="configure-facebook-as-an-identity-provider"></a>Konfigurace Facebooku jako zprostředkovatele identity

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/) jako globální správce vašeho tenanta Azure AD B2C.
1. Ujistěte se, že používáte adresář, který obsahuje Azure AD B2C tenanta, a to tak, že v horní nabídce vyberete filtr **adresář + předplatné** a zvolíte adresář, který obsahuje vašeho tenanta.
1. Zvolte **Všechny služby** v levém horním rohu portálu Azure Portal a vyhledejte a vyberte **Azure AD B2C**.
1. Vyberte **Zprostředkovatelé identity** a pak vyberte **Facebook**.
1. Zadejte **název**. Například *Facebook*.
1. Jako **ID klienta** zadejte ID aplikace Facebook, kterou jste vytvořili dříve.
1. Pro **tajný klíč klienta** zadejte tajný kód aplikace, který jste si poznamenali.
1. Vyberte **Uložit**.

## <a name="add-facebook-identity-provider-to-a-user-flow"></a>Přidání poskytovatele identity Facebooku do toku uživatele 

1. Ve vašem tenantovi Azure AD B2C vyberte **toky uživatelů**.
1. Klikněte na tok uživatele, ke kterému chcete přidat poskytovatele identity Facebooku.
1. V části **Zprostředkovatelé sociální identity** vyberte **Facebook**.
1. Vyberte **Uložit**.
1. Pokud chcete zásady testovat, vyberte **Spustit tok uživatele**.
1. V poli **aplikace** vyberte webovou aplikaci s názvem *testapp1* , kterou jste předtím zaregistrovali. Měla by se zobrazit **Adresa URL odpovědi** `https://jwt.ms` .
1. Klikněte na **Spustit tok uživatele** .

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="configure-a-facebook-account-as-an-identity-provider"></a>Konfigurace účtu Facebook jako zprostředkovatele identity

1. V `SocialAndLocalAccounts/` **`TrustFrameworkExtensions.xml`** souboru nahraďte hodnotu hodnotou `client_id` ID aplikace Facebook:

   ```xml
   <TechnicalProfile Id="Facebook-OAUTH">
     <Metadata>
     <!--Replace the value of client_id in this technical profile with the Facebook app ID"-->
       <Item Key="client_id">00000000000000</Item>
   ```

## <a name="upload-and-test-the-policy"></a>Nahrávání a testování zásad

Aktualizujte soubor předávající strany (RP), který iniciuje cestu uživatele, kterou jste vytvořili.

1. Nahrajte soubor *TrustFrameworkExtensions.xml* do svého tenanta.
1. V části **vlastní zásady** vyberte **B2C_1A_signup_signin**.
1. V části **Vybrat aplikaci** vyberte webovou aplikaci s názvem *testapp1* , kterou jste předtím zaregistrovali. Měla by se zobrazit **Adresa URL odpovědi** `https://jwt.ms` .
1. Vyberte **spustit hned** a vyberte Facebook pro přihlášení pomocí Facebooku a otestujte vlastní zásady.

::: zone-end

## <a name="next-steps"></a>Další kroky

Přečtěte si, jak [předat token Facebooku do vaší aplikace](idp-pass-through-user-flow.md).
