---
title: Začínáme s vlastními zásadami – Azure Active Directory B2C
description: Naučte se, jak začít pracovat s vlastními zásadami v Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/26/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 8e858869d742120138e7997ce21d9e4cca93ed9b
ms.sourcegitcommit: 3f22ae300425fb30be47992c7e46f0abc2e68478
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2019
ms.locfileid: "71264368"
---
# <a name="get-started-with-custom-policies-in-azure-active-directory-b2c"></a>Začínáme s vlastními zásadami v Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

[Vlastní zásady](active-directory-b2c-overview-custom.md) jsou konfigurační soubory, které definují chování klienta Azure Active Directory B2C (Azure AD B2C). V tomto článku vytvoříte vlastní zásadu, která podporuje registraci nebo přihlášení k místnímu účtu pomocí e-mailové adresy a hesla. Připravuje se také prostředí pro přidávání zprostředkovatelů identity.

## <a name="prerequisites"></a>Požadavky

- Pokud ho ještě nemáte, [Vytvořte klienta Azure AD B2C](tutorial-create-tenant.md) , který je propojený s vaším předplatným Azure.
- [Zaregistrujte svoji aplikaci](tutorial-register-applications.md) v tenantovi, kterou jste vytvořili, aby mohla komunikovat s Azure AD B2C.
- Abyste mohli konfigurovat facebookovou aplikaci, proveďte kroky v části [Nastavení registrace a přihlášení pomocí účtu Facebook](active-directory-b2c-setup-fb-app.md) .

## <a name="add-signing-and-encryption-keys"></a>Přidat podpisové a šifrovací klíče

1. Přihlaste se k portálu [Azure Portal](https://portal.azure.com).
1. Pomocí filtru **adresář a odběr** v horní nabídce vyberte adresář, který obsahuje vašeho tenanta Azure AD B2C.
1. V nabídce vlevo vyberte **Azure AD B2C**. Případně vyberte **všechny služby** a vyhledejte a vyberte **Azure AD B2C**.
1. Na stránce Přehled vyberte možnost **Architektura prostředí identity**.

### <a name="create-the-signing-key"></a>Vytvoření podpisového klíče

1. Vyberte **klíče zásad** a pak vyberte **Přidat**.
1. Pro **Možnosti**vyberte `Generate`možnost.
1. Do **název**zadejte `TokenSigningKeyContainer`. Předponu `B2C_1A_` lze přidat automaticky.
1. Jako **typ klíče**vyberte **RSA**.
1. V případě **použití klíče**vyberte možnost **podpis**.
1. Vyberte **Vytvořit**.

### <a name="create-the-encryption-key"></a>Vytvoření šifrovacího klíče

1. Vyberte **klíče zásad** a pak vyberte **Přidat**.
1. Pro **Možnosti**vyberte `Generate`možnost.
1. Do **název**zadejte `TokenEncryptionKeyContainer`. Předponu `B2C_1A`_ lze přidat automaticky.
1. Jako **typ klíče**vyberte **RSA**.
1. V případě **použití klíče**vyberte **šifrování**.
1. Vyberte **Vytvořit**.

### <a name="create-the-facebook-key"></a>Vytvořit facebookový klíč

Přidejte [tajný klíč aplikace](active-directory-b2c-setup-fb-app.md) vaší aplikace Facebook jako klíč zásad. Můžete použít tajný klíč aplikace, který jste vytvořili v rámci požadavků tohoto článku.

1. Vyberte **klíče zásad** a pak vyberte **Přidat**.
1. Pro **Možnosti**vyberte `Manual`možnost.
1. Jako **název**zadejte `FacebookSecret`. Předponu `B2C_1A_` lze přidat automaticky.
1. V **tajnosti**zadejte *tajný kód aplikace* vaší aplikace na Facebooku z Developers.Facebook.com. Tato hodnota je tajný kód, nikoli ID aplikace.
1. V případě **použití klíče**vyberte možnost **podpis**.
1. Vyberte **Vytvořit**.

## <a name="register-identity-experience-framework-applications"></a>Registrovat aplikace architektury identity Experience Framework

Azure AD B2C vyžaduje, abyste zaregistrovali dvě aplikace, které se používají k registraci a přihlašování uživatelů: IdentityExperienceFramework (webová aplikace) a ProxyIdentityExperienceFramework (nativní aplikace) s delegovaným oprávněním z aplikace IdentityExperienceFramework. Místní účty existují jenom ve vašem tenantovi. Uživatelé si zaregistrují jedinečnou kombinaci e-mailové adresy a hesla pro přístup k aplikacím registrovaným v tenantovi.

### <a name="register-the-identityexperienceframework-application"></a>Registrace aplikace IdentityExperienceFramework

1. V levém horním rohu Azure Portal vyberte **všechny služby** .
1. Do vyhledávacího pole zadejte `Azure Active Directory`.
1. Ve výsledcích hledání vyberte **Azure Active Directory** .
1. V části **Spravovat** v nabídce na levé straně vyberte **Registrace aplikací (starší verze)** .
1. Vyberte **Registrace nové aplikace**.
1. Jako **název**zadejte `IdentityExperienceFramework`.
1. Jako **Typ aplikace**vyberte **Webová aplikace/rozhraní API**.
1. Pro **přihlašovací adresu URL**zadejte `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com`, kde `your-tenant-name` je váš Azure AD B2C název domény tenanta. Všechny adresy URL by teď měly používat [b2clogin.com](b2clogin.md).
1. Vyberte **Vytvořit**. Po vytvoření zkopírujte ID aplikace a uložte ho pro pozdější použití.

### <a name="register-the-proxyidentityexperienceframework-application"></a>Registrace aplikace ProxyIdentityExperienceFramework

1. V **Registrace aplikací (starší verze)** vyberte možnost **Registrace nové aplikace**.
1. Jako **název**zadejte `ProxyIdentityExperienceFramework`.
1. Jako **Typ aplikace**vyberte možnost **nativní**.
1. Jako **identifikátor URI přesměrování**zadejte `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com`, kde `your-tenant-name` je váš Azure AD B2C tenant.
1. Vyberte **Vytvořit**. Po vytvoření zkopírujte ID aplikace a uložte ho pro pozdější použití.
1. Vyberte **Nastavení**, pak vyberte **požadovaná oprávnění**a pak vyberte **Přidat**.
1. Zvolte **Vybrat rozhraní API**, vyhledejte a vyberte **IdentityExperienceFramework**a potom klikněte na **Vybrat**.
1. Zaškrtněte políčko vedle **přístupu k IdentityExperienceFramework**, klikněte na **Vybrat**a potom na Hotovo.
1. Vyberte **udělit oprávnění**a pak potvrďte výběrem možnosti **Ano**.

## <a name="custom-policy-starter-pack"></a>Startovní sada vlastních zásad

Vlastní zásady jsou sada souborů XML, které nahrajete do svého tenanta Azure AD B2C, abyste mohli definovat technické profily a cesty uživatelů. Poskytujeme úvodní balíčky s několika předem sestavenými zásadami, abyste mohli rychle začít. Každá z těchto startovních sad obsahuje nejmenší počet technických profilů a cest uživatelů potřebných k dosažení popsaných scénářů:

- **LocalAccounts** – povoluje pouze použití místních účtů.
- **SocialAccounts** – povoluje pouze použití sociálních (nebo federovaných) účtů.
- **SocialAndLocalAccounts** – povolí použití místních i sociálních účtů.
- **SocialAndLocalAccountsWithMFA** – povolí možnosti pro sociální, místní a Multi-Factor Authentication.

Každá počáteční sada obsahuje:

- **Základní soubor** – pro základní se vyžaduje několik úprav. Příklad: *TrustFrameworkBase. XML*
- **Soubor rozšíření** – tento soubor je tam, kde se provádí většina změn konfigurace. Příklad: *TrustFrameworkExtensions. XML*
- **Soubory předávající strany** – soubory specifické pro úlohy, které jsou volány vaší aplikací. Příklady: *SignUpOrSignin. XML*, *ProfileEdit. XML*, *PasswordReset. XML*

V tomto článku upravíte vlastní soubory zásad XML v **SocialAndLocalAccounts** Starter Pack. Pokud potřebujete editor XML, zkuste [Visual Studio Code](https://code.visualstudio.com/download), zjednodušený editor pro více platforem.

### <a name="get-the-starter-pack"></a>Získat Startovní sadu

Získejte vlastní úvodní sady zásad z GitHubu a pak aktualizujte soubory XML v SocialAndLocalAccounts Starter Pack pomocí názvu klienta Azure AD B2C.

1. [Stáhněte si soubor. zip](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/archive/master.zip) nebo naklonujte úložiště:

    ```console
    git clone https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack
    ```

1. Ve všech souborech v adresáři **SocialAndLocalAccounts** nahraďte řetězec `yourtenant` názvem vašeho tenanta Azure AD B2C.

    Například pokud je název vašeho tenanta B2C *contosotenant*, všechny instance `yourtenant.onmicrosoft.com` se stanou `contosotenant.onmicrosoft.com`.

### <a name="add-application-ids-to-the-custom-policy"></a>Přidání ID aplikace do vlastních zásad

Přidejte ID aplikace do souboru rozšíření *TrustFrameworkExtensions. XML*.

1. Otevřete `SocialAndLocalAccounts/` `<TechnicalProfile Id="login-NonInteractive">`a **vyhledejteelement.`TrustFrameworkExtensions.xml`**
1. Nahraďte obě instance `IdentityExperienceFrameworkAppId` s ID aplikace aplikace IdentityExperienceFramework, kterou jste vytvořili dříve.
1. Nahraďte obě instance `ProxyIdentityExperienceFrameworkAppId` s ID aplikace aplikace ProxyIdentityExperienceFramework, kterou jste vytvořili dříve.
1. Uložte soubor.

## <a name="upload-the-policies"></a>Nahrajte zásady.

1. V Azure Portal vyberte položku nabídky **rozhraní identity Experience Framework** v tenantovi B2C.
1. Vyberte **Odeslat vlastní zásadu**.
1. V tomto pořadí nahrajte soubory zásad:
    1. *TrustFrameworkBase. XML*
    1. *TrustFrameworkExtensions. XML*
    1. *SignUpOrSignin. XML*
    1. *ProfileEdit. XML*
    1. *PasswordReset. XML*

Při nahrávání souborů Azure přidá předponu `B2C_1A_` do každého.

> [!TIP]
> Pokud editor XML podporuje ověřování, ověřte soubory oproti `TrustFrameworkPolicy_0.3.0.0.xsd` schématu XML, které se nachází v kořenovém adresáři úvodní sady. Ověřování schématu XML identifikuje chyby před odesláním.

## <a name="test-the-custom-policy"></a>Testování vlastních zásad

1. V části **vlastní zásady**vyberte **B2C_1A_signup_signin**.
1. Pro **možnost vybrat aplikaci** na stránce Přehled v části vlastní zásady vyberte webovou aplikaci s názvem *WebApp1* , kterou jste předtím zaregistrovali.
1. Ujistěte se, že **Adresa URL odpovědi** je `https://jwt.ms`.
1. Vyberte **Spustit nyní**.
1. Zaregistrujte se pomocí e-mailové adresy.
1. Znovu vyberte **Spustit** .
1. Přihlaste se pomocí stejného účtu, abyste měli jistotu, že máte správnou konfiguraci.

## <a name="add-facebook-as-an-identity-provider"></a>Přidat Facebook jako zprostředkovatele identity

1. V souboru nahraďte hodnotu hodnotou `client_id` ID aplikace Facebook: **`TrustFrameworkExtensions.xml`** `SocialAndLocalAccounts/`

   ```xml
   <TechnicalProfile Id="Facebook-OAUTH">
     <Metadata>
     <!--Replace the value of client_id in this technical profile with the Facebook app ID"-->
       <Item Key="client_id">00000000000000</Item>
   ```

1. Nahrajte soubor *TrustFrameworkExtensions. XML* do svého tenanta.
1. V části **vlastní zásady**vyberte **B2C_1A_signup_signin**.
1. Vyberte **spustit hned** a vyberte Facebook pro přihlášení pomocí Facebooku a otestujte vlastní zásady.

## <a name="next-steps"></a>Další kroky

Potom zkuste přidat Azure Active Directory (Azure AD) jako zprostředkovatele identity. Základní soubor použitý v této příručce Začínáme už obsahuje nějaký obsah, který potřebujete pro přidání dalších poskytovatelů identity, jako je Azure AD.

Informace o nastavení služby Azure AD jako zprostředkovatele identity najdete v tématu [Nastavení registrace a přihlášení pomocí účtu Azure Active Directory s využitím Active Directory B2C vlastních zásad](active-directory-b2c-setup-aad-custom.md).
