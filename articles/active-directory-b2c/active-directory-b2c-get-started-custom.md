---
title: Začínáme s vlastními zásadami – Azure Active Directory B2C | Dokumentace Microsoftu
description: Zjistěte, jak začít pracovat s vlastními zásadami v Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 05/16/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 2929c033b4744ea89f8e3d711a5e2e0df6301c14
ms.sourcegitcommit: 1aefdf876c95bf6c07b12eb8c5fab98e92948000
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/06/2019
ms.locfileid: "66730016"
---
# <a name="get-started-with-custom-policies-in-azure-active-directory-b2c"></a>Začínáme s vlastními zásadami v Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

[Vlastní zásady](active-directory-b2c-overview-custom.md) jsou konfigurační soubory, které definují chování vašeho tenanta Azure Active Directory (Azure AD) B2C. V tomto článku vytvoříte vlastní zásadu, která podporuje místní účet registrace nebo přihlášení pomocí e-mailovou adresu a heslo. Také Příprava prostředí pro přidání poskytovatele identity.

## <a name="prerequisites"></a>Požadavky

- Pokud je nemáte, budete muset [vytvoření tenanta Azure AD B2C](tutorial-create-tenant.md) , který je propojený s předplatným Azure.
- [Zaregistrujte si vaši aplikaci](tutorial-register-applications.md) v tenantovi, který jste vytvořili, tak, aby mohl komunikovat s Azure AD B2C.

## <a name="add-signing-and-encryption-keys"></a>Přidejte klíče pro podepisování a šifrování

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/) jako globální správce vašeho tenanta Azure AD B2C.
2. Ujistěte se, že používáte adresáře, který obsahuje vašeho tenanta Azure AD B2C. Klikněte na tlačítko **filtr adresářů a předplatných** v horní nabídce a vyberte adresář, který obsahuje váš tenant. 
3. Zvolte **Všechny služby** v levém horním rohu portálu Azure Portal a vyhledejte a vyberte **Azure AD B2C**.
4. Na stránce s přehledem, vyberte **architekturu rozhraní identit**.

### <a name="create-the-signing-key"></a>Vytvoření podpisového klíče

1. Vyberte **klíče zásad** a pak vyberte **přidat**.
2. Pro **možnosti**, zvolte `Generate`.
3. V **název**, zadejte `TokenSigningKeyContainer`. Předpona, která `B2C_1A_` může být automaticky přidán.
4. Pro **typ klíče**vyberte **RSA**.
5. Pro **použití klíče**vyberte **podpis**.
6. Klikněte na možnost **Vytvořit**.

### <a name="create-the-encryption-key"></a>Vytvořit šifrovací klíč

1. Vyberte **klíče zásad** a pak vyberte **přidat**.
2. Pro **možnosti**, zvolte `Generate`.
3. V **název**, zadejte `TokenEncryptionKeyContainer`. Předpona, která `B2C_1A`_, mohou být přidány automaticky.
4. Pro **typ klíče**vyberte **RSA**.
5. Pro **použití klíče**vyberte **šifrování**.
6. Klikněte na možnost **Vytvořit**.

### <a name="create-the-facebook-key"></a>Vytvoření klíče služby Facebook

Pokud už máte [tajný klíč aplikace pro Facebook](active-directory-b2c-setup-fb-app.md), přidejte jako klíče zásad do vašeho tenanta. V opačném případě vytvořte klíč s zástupnou hodnotu tak, aby vaše zásady projít ověřením.

1. Vyberte **klíče zásad** a pak vyberte **přidat**.
2. Pro **možnosti**, zvolte `Manual`.
3. Pro **název**, zadejte `FacebookSecret`. Předpona, která `B2C_1A_` může být automaticky přidán.
4. V **tajný kód**, zadejte tajný klíč pro Facebook z developers.facebook.com nebo `0` jako zástupný symbol. Tato hodnota je tajný kód, nikoli ID aplikace.
5. Pro **použití klíče**vyberte **podpis**.
6. Klikněte na možnost **Vytvořit**.

## <a name="register-identity-experience-framework-applications"></a>Registrovat architekturu rozhraní identit aplikace

Azure AD B2C, musíte k registraci dvě aplikace, které se používají k registraci a přihlašování uživatelů: IdentityExperienceFramework (webová aplikace) a delegovaná oprávnění z aplikace IdentityExperienceFramework ProxyIdentityExperienceFramework (nativní aplikace). Místní účty existují pouze ve vašem tenantovi. Vaši uživatelé zaregistrovat pomocí kombinace jedinečnou e-mailovou adresu a heslo pro přístup k aplikacím vašeho tenanta zaregistrované.

### <a name="register-the-identityexperienceframework-application"></a>Registrace aplikace IdentityExperienceFramework

1. Zvolte **všechny služby** v levém horním rohu webu Azure portal, vyhledejte a vyberte **Azure Active Directory**.
2. V nabídce vyberte **registrace aplikací (starší verze)** .
3. Vyberte **Registrace nové aplikace**.
4. Pro **název**, zadejte `IdentityExperienceFramework`.
5. Pro **typ aplikace**, zvolte **webové aplikace nebo rozhraní API**.
6. Pro **přihlašovací adresa URL**, zadejte `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com`, kde `your-tenant-name` je název domény tenanta Azure AD B2C. Všechny adresy URL by měla nyní být pomocí [b2clogin.com](b2clogin.md).
7. Klikněte na možnost **Vytvořit**. Po jeho vytvoření, zkopírujte ID aplikace a uložte ho pro pozdější použití.

### <a name="register-the-proxyidentityexperienceframework-application"></a>Registrace aplikace ProxyIdentityExperienceFramework

1. V **registrace aplikací (starší verze)** vyberte **registrace nové aplikace**.
2. Pro **název**, zadejte `ProxyIdentityExperienceFramework`.
3. Pro **typ aplikace**, zvolte **nativní**.
4. Pro **identifikátor URI pro přesměrování**, zadejte `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com`, kde `your-tenant-name` je váš tenant Azure AD B2C.
5. Klikněte na možnost **Vytvořit**. Po jeho vytvoření, zkopírujte ID aplikace a uložte ho pro pozdější použití.
6. Na stránce Nastavení vyberte **požadovaná oprávnění**a pak vyberte **přidat**.
7. Zvolte **vyberte rozhraní API**, vyhledejte a vyberte **IdentityExperienceFramework**a potom klikněte na tlačítko **vyberte**.
9. Zaškrtněte políčko vedle položky **přístup IdentityExperienceFramework**, klikněte na tlačítko **vyberte**a potom klikněte na tlačítko **provádí**.
10. Vyberte **udělit oprávnění**a potvrďte tak, že vyberete **Ano**.

## <a name="download-starter-pack-and-modify-policies"></a>Úvodní sada stáhnout a upravit zásady

Vlastní zásady jsou sada souborů XML, které je třeba nahrát do vašeho tenanta Azure AD B2C. Sady Starter souborů jsou k dispozici pro zajištění rychlé zahájení práce. Každý starter pack v následujícím seznamu obsahuje nejmenší počet technické profily a jízdy uživatelů potřebné k dosažení popsané scénáře:

- LocalAccounts – umožňuje použití pouze místní účty.
- SocialAccounts – umožňuje použití pouze účty sociálních sítí (nebo federované).
- SocialAndLocalAccounts – umožňuje použití místních účtů a účtů v sociálních sítích.
- SocialAndLocalAccountsWithMFA - umožňuje sociální sítě, místní a možnosti ověřování službou Multi-Factor Authentication.

Každý starter pack obsahuje:

- Základní soubor. Několik změn nutných k základní třídě.
- Soubor rozšíření.  Tento soubor je provedena většinu změn konfigurace.
- Předávající strana soubory. Soubory specifické úkoly v aplikaci.

>[!NOTE]
>Pokud XML editor podporuje ověřování, ověřte soubory proti schématu TrustFrameworkPolicy_0.3.0.0.xsd XML, který se nachází v kořenovém adresáři starter pack. Ověření schématu XML identifikuje chyby před odesláním.

1. [Stáhněte si soubor .zip](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/archive/master.zip) nebo spustit:

    ```console
    git clone https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack
    ```

2. Ve složce SocialAndLocalAccounts upravovat všechny soubory nahrazení `yourtenant` s názvem pro vašeho tenanta. Například, `contosoTenant.onmicrosoft.com`. Pokud potřebujete XML editor [nabídne Visual Studio Code](https://code.visualstudio.com/download), jednoduchý editor napříč platformami.

### <a name="add-application-ids-to-the-custom-policy"></a>ID aplikace přidat do vlastní zásady

ID aplikace přidejte do souboru rozšíření *TrustFrameworkExtensions.xml*.

1. Otevřít *TrustFrameworkExtensions.xml* souboru a najděte element `<TechnicalProfile Id="login-NonInteractive">`.
2. Nahraďte oba výskyty `IdentityExperienceFrameworkAppId` s ID aplikace, architekturu rozhraní identit aplikace, kterou jste vytvořili dříve. Nahraďte oba výskyty `ProxyIdentityExperienceFrameworkAppId` s ID aplikace, architekturu rozhraní identit Proxy aplikace, kterou jste vytvořili dříve.
3. Uložte soubor rozšíření.

## <a name="upload-the-policies"></a>Nahrát zásady

1. Na stránce vlastní zásady architekturu rozhraní identit vyberte **nahrát zásady**.
1. V tomto pořadí, nahrajte *TrustFrameworkBase.xml*, *TrustFrameworkExtensions.xml*, *SignUpOrSignin.xml*, *ProfileEdit.xml* , a *PasswordReset.xml*. Když se nahraje soubor, název souboru zásad začíná `B2C_1A_`.

## <a name="test-the-custom-policy"></a>Testování vlastní zásady

1. Na stránce vlastní zásady vyberte **B2C_1A_signup_signin**.
2. Pro **vyberte aplikaci** na stránce Přehled vlastních zásad, vyberte webovou aplikaci s názvem *webapp1* , které jste dříve zaregistrovali. Ujistěte se, že **adresy URL odpovědi** je `https://jwt.ms`.
3. Vyberte **spustit nyní**.
4. Měli byste být schopni registrace pomocí e-mailovou adresu.
5. Přihlaste se pomocí stejného účtu pro potvrzení, že máte správnou konfiguraci.

## <a name="add-facebook-as-an-identity-provider"></a>Přidat jako zprostředkovatele identity Facebook

1. Konfigurace [aplikace Facebook](active-directory-b2c-setup-fb-app.md).
2. V *TrustFrameworkExtensions.xml* souboru, nahraďte hodnotu `client_id` s ID aplikace pro Facebook:

   ```xml
   <TechnicalProfile Id="Facebook-OAUTH">
     <Metadata>
     <!--Replace the value of client_id in this technical profile with the Facebook app ID"-->
       <Item Key="client_id">00000000000000</Item>
   ```
3. Nahrát *TrustFrameworkExtensions.xml* soubor do svého tenanta.
4. Test s použitím **spustit nyní** nebo vyvoláním zásad přímo z registraci aplikace.

## <a name="next-steps"></a>Další postup

- Přidáte Azure Active Directory jako zprostředkovatele identity. Základní soubor už používá v tato příručka Začínáme obsahuje některé obsahu, který je nutné pro přidání dalších poskytovatelů identit. Informace o nastavení přihlášení, najdete v článku [nastavení registrace a přihlášení pomocí účtu služby Azure Active Directory pomocí vlastních zásad Active Directory B2C](active-directory-b2c-setup-aad-custom.md) článku.
