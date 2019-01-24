---
title: Začínáme s vlastními zásadami v Azure Active Directory B2C | Dokumentace Microsoftu
description: Jak začít s Azure Active Directory B2C vlastními zásadami.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/17/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 235b72393801717bb5d7258d6492dc4c943fe232
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2019
ms.locfileid: "54852299"
---
# <a name="get-started-with-custom-policies-in-azure-active-directory-b2c"></a>Začínáme s vlastními zásadami v Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

[Vlastní zásady](active-directory-b2c-overview-custom.md) jsou konfigurační soubory, které definují chování vašeho tenanta Azure Active Directory (Azure AD) B2C. V tomto článku vytvoříte vlastní zásadu, která podporuje místní účet registrace nebo přihlášení pomocí e-mailovou adresu a heslo. Také Příprava prostředí pro přidání poskytovatelů identit, jako je Facebook nebo Azure Active Directory.

## <a name="prerequisites"></a>Požadavky

Pokud je nemáte, budete muset [vytvoření tenanta Azure AD B2C](tutorial-create-tenant.md) , který je propojený s předplatným Azure.

## <a name="add-signing-and-encryption-keys"></a>Přidejte klíče pro podepisování a šifrování

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/) jako globální správce vašeho tenanta Azure AD B2C.
2. Ujistěte se, že používáte adresáře, který obsahuje vašeho tenanta Azure AD B2C kliknutím **filtr adresářů a předplatných** v horní nabídce a výběrem adresáře, který obsahuje váš tenant. 

    ![Přepnutí na tenanta Azure AD B2C](./media/active-directory-b2c-setup-fb-app/switch-directories.png)

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

## <a name="register-applications"></a>Registrace aplikace

Azure AD B2C, musíte k registraci dvě aplikace, které se používají k registraci a přihlašování uživatelů: IdentityExperienceFramework (webová aplikace) a delegovaná oprávnění z aplikace IdentityExperienceFramework ProxyIdentityExperienceFramework (nativní aplikace). Místní účty existují pouze ve vašem tenantovi. Vaši uživatelé zaregistrovat pomocí kombinace jedinečnou e-mailovou adresu a heslo pro přístup k aplikacím vašeho tenanta zaregistrované.

### <a name="register-the-identityexperienceframework-application"></a>Registrace aplikace IdentityExperienceFramework

1. Zvolte **všechny služby** v levém horním rohu webu Azure portal, vyhledejte a vyberte **Azure Active Directory**a pak vyberte **registrace aplikací**.
2. Vyberte **Registrace nové aplikace**.
3. Pro **název**, zadejte `IdentityExperienceFramework`.
4. Pro **typ aplikace**, zvolte **webové aplikace nebo rozhraní API**.
5. Pro **přihlašovací adresa URL**, zadejte `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com`, kde `your-tenant-name` je název domény tenanta Azure AD B2C.
6. Klikněte na možnost **Vytvořit**. 
7. Po jeho vytvoření, zkopírujte ID aplikace a uložte ho pro pozdější použití.

### <a name="register-the-proxyidentityexperienceframework-application"></a>Registrace aplikace ProxyIdentityExperienceFramework

1. Vyberte **registrace aplikací**a pak vyberte **registrace nové aplikace**.
2. Pro **název**, zadejte `ProxyIdentityExperienceFramework`.
3. Pro **typ aplikace**, zvolte **nativní**.
4. Pro **identifikátor URI pro přesměrování**, zadejte `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com`, kde `yourtenant` je váš tenant Azure AD B2C.
5. Klikněte na možnost **Vytvořit**. Po jeho vytvoření, zkopírujte ID aplikace a uložte ho pro pozdější použití.
6. Na stránce Nastavení vyberte **požadovaná oprávnění**a pak vyberte **přidat**.
7. Vyberte **Výběr rozhraní API**.
8. Vyhledejte a vyberte **IdentityExperienceFramework**a potom klikněte na tlačítko **vyberte**.
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
* Soubor rozšíření.  Tento soubor je provedena většinu změn konfigurace.
* Předávající strana soubory. Soubory specifické úkoly v aplikaci.

>[!NOTE]
>Pokud XML editor podporuje ověřování, ověřte soubory proti schématu TrustFrameworkPolicy_0.3.0.0.xsd XML, který se nachází v kořenovém adresáři starter pack. Ověření schématu XML identifikuje chyby před odesláním.

1. [Stáhněte si soubor .zip](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/archive/master.zip) nebo spustit:

    ```console
    git clone https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack
    ```

2. Ve složce SocialAndLocalAccounts upravovat všechny soubory nahrazení `yourtenant.onmicrosoft.com` s názvem pro vašeho tenanta. Například, `contosoTenant.onmicrosoft.com`. Pokud potřebujete XML editor [nabídne Visual Studio Code](https://code.visualstudio.com/download), jednoduchý editor napříč platformami.

### <a name="add-application-ids-to-the-custom-policy"></a>ID aplikace přidat do vlastní zásady

ID aplikace přidejte do souboru rozšíření *TrustFrameworkExtensions.xml*.

1. Otevřít *TrustFrameworkExtensions.xml* souboru a najděte element `<TechnicalProfile Id="login-NonInteractive">`.
2. Nahraďte oba výskyty `IdentityExperienceFrameworkAppId` s ID aplikace, architekturu rozhraní identit aplikace, kterou jste vytvořili dříve. Nahraďte oba výskyty `ProxyIdentityExperienceFrameworkAppId` s ID aplikace, architekturu rozhraní identit Proxy aplikace, kterou jste vytvořili dříve. Následující příklad ukazuje **neinteraktivní přihlášení** technický profil po změně:

    ![ID aplikace](./media/active-directory-b2c-get-started-custom/login-NonInteractive.png)

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

## <a name="next-steps"></a>Další kroky

- Přidáte Azure Active Directory jako zprostředkovatele identity. Základní soubor už používá v tato příručka Začínáme obsahuje některé obsahu, který je nutné pro přidání dalších poskytovatelů identit. Informace o nastavení přihlášení, najdete v článku [nastavení registrace a přihlášení pomocí účtu služby Azure Active Directory pomocí vlastních zásad Active Directory B2C](active-directory-b2c-setup-aad-custom.md) článku.
