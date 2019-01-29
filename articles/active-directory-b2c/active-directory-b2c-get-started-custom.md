---
title: Začínáme s vlastními zásadami v Azure Active Directory B2C | Dokumentace Microsoftu
description: Jak začít s Azure Active Directory B2C vlastními zásadami.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 01/25/2019
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 2d5cc846b6ca2eadacfcc8223e4ba3932e961ece
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55173594"
---
# <a name="get-started-with-custom-policies-in-azure-active-directory-b2c"></a>Začínáme s vlastními zásadami v Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

[Vlastní zásady](active-directory-b2c-overview-custom.md) jsou konfigurační soubory, které definují chování vašeho tenanta Azure Active Directory (Azure AD) B2C. V tomto článku vytvoříte vlastní zásadu, která podporuje místní účet registrace nebo přihlášení pomocí e-mailovou adresu a heslo. Také Příprava prostředí pro přidání poskytovatelů identit, jako je Facebook.

## <a name="prerequisites"></a>Požadavky

Pokud je nemáte, budete muset [vytvoření tenanta Azure AD B2C](tutorial-create-tenant.md) , který je propojený s předplatným Azure.

## <a name="add-signing-and-encryption-keys"></a>Přidejte klíče pro podepisování a šifrování

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/) jako globální správce vašeho tenanta Azure AD B2C.
2. Ujistěte se, že používáte adresáře, který obsahuje vašeho tenanta Azure AD B2C kliknutím **filtr adresářů a předplatných** v horní nabídce a výběrem adresáře, který obsahuje váš tenant. 
3. Zvolte **Všechny služby** v levém horním rohu portálu Azure Portal a vyhledejte a vyberte **Azure AD B2C**.
4. Na stránce s přehledem, vyberte **architekturu rozhraní identit - PREVIEW**.

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
4. V **tajný kód**, zadejte tajný klíč pro Facebook z developers.facebook.com nebo `0` jako zástupný symbol. Toto je tajný kód, nikoli ID aplikace.
5. Pro **použití klíče**vyberte **podpis**.
6. Klikněte na možnost **Vytvořit**.

## <a name="register-an-application"></a>Registrace aplikace

Aplikace je zaregistrované v Azure Active Directory (Azure AD) B2C a povolit tak uživateli se zaregistrovat a přihlaste se pomocí místního účtu, který existuje ve vašem tenantovi. Vaši uživatelé zaregistrovat pomocí jedinečnou e-mailovou adresu a heslo pro přístup k registrované aplikaci.

1. Zvolte **všechny služby** v levém horním rohu webu Azure portal, vyhledejte a vyberte **registrace aplikací**.
2. Vyberte **Registrace nové aplikace**.
3. Pro **název**, zadejte `ProxyIdentityExperienceFramework`.
4. Pro **typ aplikace**, zvolte **nativní**.
5. Pro **identifikátor URI pro přesměrování**, zadejte `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com`, kde `your-tenant-name` je název vašeho tenanta Azure AD B2C.
6. Klikněte na možnost **Vytvořit**. Po jeho vytvoření, zkopírujte ID aplikace a uložte ho pro pozdější použití.
7. Vyberte **udělit oprávnění**a potvrďte tak, že vyberete **Ano**.

## <a name="download-starter-pack-and-modify-policies"></a>Úvodní sada stáhnout a upravit zásady

Vlastní zásady jsou sada souborů XML, které je třeba nahrát do vašeho tenanta Azure AD B2C. Sady Starter souborů jsou k dispozici pro zajištění rychlé zahájení práce. Každý starter pack v následujícím seznamu obsahuje nejmenší počet technické profily a jízdy uživatelů potřebné k dosažení popsané scénáře:

- LocalAccounts – umožňuje použití pouze místní účty.
- SocialAccounts – umožňuje použití pouze účty sociálních sítí (nebo federované).
- SocialAndLocalAccounts – umožňuje použití místních účtů a účtů v sociálních sítích.
- SocialAndLocalAccountsWithMFA - umožňuje sociální sítě, místní a možnosti ověřování službou Multi-Factor Authentication.

Každý starter pack obsahuje:

- Základní soubor. Několik změn nutných k základní třídě.
* Soubor rozšíření.  Tento soubor je provedena většinu změn konfigurace.
* Předávající strana soubory. Soubory specifické úkoly v aplikaci.

>[!NOTE]
>Pokud XML editor podporuje ověřování, ověřte soubory proti schématu TrustFrameworkPolicy_0.3.0.0.xsd XML, který se nachází v kořenovém adresáři starter pack. Ověření schématu XML identifikuje chyby před odesláním.

1. [Stáhněte si soubor .zip](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/archive/master.zip) nebo spustit:

    ```console
    git clone https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack
    ```

2. Ve složce SocialAndLocalAccounts upravovat všechny soubory nahrazení `yourtenant` s názvem pro vašeho tenanta. Například, `contosoTenant.onmicrosoft.com`. Pokud potřebujete XML editor [nabídne Visual Studio Code](https://code.visualstudio.com/download), jednoduchý editor napříč platformami.

### <a name="add-application-ids-to-the-custom-policy"></a>ID aplikace přidat do vlastní zásady

Přidat ID aplikace do souboru rozšíření *TrustFrameworkExtensions.xml*.

1. Otevřít *TrustFrameworkExtensions.xml* souboru a najděte element `<TechnicalProfile Id="login-NonInteractive">`.
2. Nahraďte hodnotu z `client_id` a `resource_id` s ID aplikace ProxyIdentityExperienceFramework aplikace, kterou jste vytvořili dříve.
3. Uložte soubor rozšíření.

## <a name="upload-the-policies"></a>Nahrát zásady

1. Na stránce vlastní zásady architekturu rozhraní identit vyberte **nahrát zásady**.
1. V tomto pořadí, nahrajte *TrustFrameworkBase.xml*, *TrustFrameworkExtensions.xml*, *SignUpOrSignin.xml*, *ProfileEdit.xml* , a *PasswordReset.xml*. Když se nahraje soubor, název souboru zásad začíná `B2C_1A_`.

## <a name="test-the-custom-policy"></a>Testování vlastní zásady

1. Na stránce vlastní zásady vyberte **B2C_1A_signup_signin**. 
2. Vyberte **spustit nyní**.

3. Měli byste být schopni registrace pomocí e-mailovou adresu.

4. Přihlaste se pomocí stejného účtu pro potvrzení, že máte správnou konfiguraci.

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
