---
title: Začínáme s vlastními zásadami
titleSuffix: Azure AD B2C
description: Naučte se, jak začít pracovat s vlastními zásadami v Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 02/28/2020
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 2d6c76a15ee62c26e0d0261c6b0d2d1e27443a40
ms.sourcegitcommit: 956dec4650e551bdede45d96507c95ecd7a01ec9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/09/2021
ms.locfileid: "102518038"
---
# <a name="get-started-with-custom-policies-in-azure-active-directory-b2c"></a>Začínáme s vlastními zásadami v Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

[Vlastní zásady](custom-policy-overview.md) jsou konfigurační soubory, které definují chování klienta Azure Active Directory B2C (Azure AD B2C). V tomto článku vytvoříte vlastní zásadu, která podporuje registraci nebo přihlášení k místnímu účtu pomocí e-mailové adresy a hesla. Připravuje se také prostředí pro přidávání zprostředkovatelů identity.

## <a name="prerequisites"></a>Požadavky

- Pokud ho ještě nemáte, [Vytvořte klienta Azure AD B2C](tutorial-create-tenant.md) , který je propojený s vaším předplatným Azure.
- [Zaregistrujte svoji aplikaci](tutorial-register-applications.md) v tenantovi, kterou jste vytvořili, aby mohla komunikovat s Azure AD B2C.
- Abyste mohli konfigurovat facebookovou aplikaci, proveďte kroky v části [Nastavení registrace a přihlášení pomocí účtu Facebook](identity-provider-facebook.md) . I když se aplikace Facebook nevyžaduje pro použití vlastních zásad, používá se v tomto návodu k předvedení povolení sociálního přihlášení ve vlastních zásadách.

> [!TIP]
> Tento článek vysvětluje, jak nastavit klienta ručně. Celý proces můžete automatizovat z tohoto článku. Automatizace nasadí Azure AD B2C [SocialAndLocalAccountsWithMFA Starter Pack](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack), který bude poskytovat registraci a přihlášení, resetování hesla a cestu pro úpravy profilu. Pokud chcete tento návod automatizovat, přejděte na [instalační aplikaci IEF](https://aka.ms/iefsetup) a postupujte podle pokynů.


## <a name="add-signing-and-encryption-keys"></a>Přidat podpisové a šifrovací klíče

1. Přihlaste se na [Azure Portal](https://portal.azure.com).
1. Na panelu nástrojů na portálu vyberte ikonu **adresář + předplatné** a pak vyberte adresář, který obsahuje vašeho tenanta Azure AD B2C.
1. V Azure Portal vyhledejte a vyberte **Azure AD B2C**.
1. Na stránce Přehled v části **zásady** vyberte **Architektura prostředí identity**.

### <a name="create-the-signing-key"></a>Vytvoření podpisového klíče

1. Vyberte **klíče zásad** a pak vyberte **Přidat**.
1. Pro **Možnosti** vyberte možnost `Generate` .
1. Do **název** zadejte `TokenSigningKeyContainer` . Předponu `B2C_1A_` lze přidat automaticky.
1. Jako **typ klíče** vyberte **RSA**.
1. V případě **použití klíče** vyberte možnost **podpis**.
1. Vyberte **Vytvořit**.

### <a name="create-the-encryption-key"></a>Vytvoření šifrovacího klíče

1. Vyberte **klíče zásad** a pak vyberte **Přidat**.
1. Pro **Možnosti** vyberte možnost `Generate` .
1. Do **název** zadejte `TokenEncryptionKeyContainer` . Předponu `B2C_1A` _ lze přidat automaticky.
1. Jako **typ klíče** vyberte **RSA**.
1. V případě **použití klíče** vyberte **šifrování**.
1. Vyberte **Vytvořit**.

### <a name="create-the-facebook-key"></a>Vytvořit facebookový klíč

Přidejte [tajný klíč aplikace](identity-provider-facebook.md) vaší aplikace Facebook jako klíč zásad. Můžete použít tajný klíč aplikace, který jste vytvořili v rámci požadavků tohoto článku.

1. Vyberte **klíče zásad** a pak vyberte **Přidat**.
1. Pro **Možnosti** vyberte možnost `Manual` .
1. Jako **název** zadejte `FacebookSecret` . Předponu `B2C_1A_` lze přidat automaticky.
1. V **tajnosti** zadejte *tajný kód aplikace* vaší aplikace na Facebooku z Developers.Facebook.com. Tato hodnota je tajný kód, nikoli ID aplikace.
1. V případě **použití klíče** vyberte možnost **podpis**.
1. Vyberte **Vytvořit**.

## <a name="register-identity-experience-framework-applications"></a>Registrovat aplikace architektury identity Experience Framework

Azure AD B2C vyžaduje, abyste zaregistrovali dvě aplikace, které používá k registraci a přihlašování uživatelů pomocí místních účtů: *IdentityExperienceFramework*, webové rozhraní API a *ProxyIdentityExperienceFramework*, nativní aplikace s delegovaným oprávněním pro aplikaci IdentityExperienceFramework. Uživatelé se můžou zaregistrovat pomocí e-mailové adresy nebo uživatelského jména a hesla pro přístup k aplikacím registrovaným v tenantovi. tím se vytvoří místní účet. Místní účty existují pouze ve vašem tenantovi Azure AD B2C.

Tyto dvě aplikace je potřeba zaregistrovat ve svém tenantovi Azure AD B2C jenom jednou.

### <a name="register-the-identityexperienceframework-application"></a>Registrace aplikace IdentityExperienceFramework

K registraci aplikace ve vašem tenantovi Azure AD B2C můžete využít **Registrace aplikací** prostředí.

1. Vyberte **Registrace aplikací** a pak vyberte **Nová registrace**.
1. Jako **název** zadejte `IdentityExperienceFramework` .
1. V části **podporované typy účtů** vyberte **účty jenom v tomto organizačním adresáři**.
1. V části **identifikátor URI pro přesměrování** vyberte **Web** a pak zadejte `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com` , kde `your-tenant-name` je váš Azure AD B2C název domény tenanta.
1. V části **oprávnění** zaškrtněte políčko *udělit souhlas správcům oprávnění OpenID a offline_access* .
1. Vyberte **Zaregistrovat**.
1. Poznamenejte si **ID aplikace (klienta)** pro použití v pozdějším kroku.

V dalším kroku vystavte rozhraní API přidáním oboru:

1. V nabídce vlevo v části **Spravovat** vyberte možnost **zveřejnit rozhraní API**.
1. Vyberte **Přidat obor** a pak vyberte **Uložit a pokračovat** a přijměte výchozí identifikátor URI ID aplikace.
1. Zadejte následující hodnoty pro vytvoření oboru, který umožňuje vlastní spuštění zásad ve vašem tenantovi Azure AD B2C:
    * **Název oboru**: `user_impersonation`
    * **Zobrazovaný název souhlasu správce**: `Access IdentityExperienceFramework`
    * **Popis souhlasu správce**: `Allow the application to access IdentityExperienceFramework on behalf of the signed-in user.`
1. Vybrat **Přidat obor**

* * *

### <a name="register-the-proxyidentityexperienceframework-application"></a>Registrace aplikace ProxyIdentityExperienceFramework

1. Vyberte **Registrace aplikací** a pak vyberte **Nová registrace**.
1. Jako **název** zadejte `ProxyIdentityExperienceFramework` .
1. V části **podporované typy účtů** vyberte **účty jenom v tomto organizačním adresáři**.
1. V části **identifikátor URI pro přesměrování** vyberte v rozevíracím seznamu možnost **veřejný klient/nativní (mobilní & Desktop)**.
1. Jako **identifikátor URI přesměrování** zadejte `myapp://auth` .
1. V části **oprávnění** zaškrtněte políčko *udělit souhlas správcům oprávnění OpenID a offline_access* .
1. Vyberte **Zaregistrovat**.
1. Poznamenejte si **ID aplikace (klienta)** pro použití v pozdějším kroku.

Dále určete, že aplikace by měla být považována za veřejného klienta:

1. V nabídce vlevo v části **Spravovat** vyberte **ověřování**.
1. V části **Upřesnit nastavení** v části **Povolit toky veřejného klienta** nastavte **Povolit, aby následující mobilní a desktopové toky byly** na **Ano**. Zajistěte, aby v manifestu aplikace bylo nastaveno **"allowPublicClient": true** . 
1. Vyberte **Uložit**.

Nyní udělte oprávnění k oboru rozhraní API, který jste dříve vystavili v registraci *IdentityExperienceFramework* :

1. V nabídce vlevo v části **Spravovat** vyberte **oprávnění rozhraní API**.
1. V části **konfigurovaná oprávnění** vyberte **Přidat oprávnění**.
1. Vyberte kartu **Moje rozhraní API** a pak vyberte aplikaci **IdentityExperienceFramework** .
1. V části **oprávnění** vyberte obor **user_impersonation** , který jste definovali dříve.
1. Vyberte **Přidat oprávnění**. Jak je směrované, počkejte několik minut, než budete pokračovat k dalšímu kroku.
1. Vyberte **udělit souhlas správce pro (název vašeho tenanta)**.
1. Vyberte aktuálně přihlášený účet správce nebo se přihlaste pomocí účtu v Azure AD B2C tenantovi, kterému byla přiřazena alespoň role *správce cloudové aplikace* .
1. Vyberte **Přijmout**.
1. Vyberte **aktualizovat** a pak ověřte, že "uděleno pro..." zobrazí se pod položkou **stav** pro obory – offline_access, openid a user_impersonation. Rozšíření oprávnění může trvat několik minut.

* * *

## <a name="custom-policy-starter-pack"></a>Startovní sada vlastních zásad

Vlastní zásady jsou sada souborů XML, které nahrajete do svého tenanta Azure AD B2C, abyste mohli definovat technické profily a cesty uživatelů. Poskytujeme úvodní balíčky s několika předem sestavenými zásadami, abyste mohli rychle začít. Každá z těchto startovních sad obsahuje nejmenší počet technických profilů a cest uživatelů potřebných k dosažení popsaných scénářů:

- **LocalAccounts** – povoluje pouze použití místních účtů.
- **SocialAccounts** – povoluje pouze použití sociálních (nebo federovaných) účtů.
- **SocialAndLocalAccounts** – povolí použití místních i sociálních účtů.
- **SocialAndLocalAccountsWithMFA** – povolí možnosti pro sociální, místní a Multi-Factor Authentication.

Každá počáteční sada obsahuje:

- **Základní soubor** – pro základní se vyžaduje několik úprav. Příklad: *TrustFrameworkBase.xml*
- **Soubor rozšíření** – tento soubor je tam, kde se provádí většina změn konfigurace. Příklad: *TrustFrameworkExtensions.xml*
- **Soubory předávající strany** – soubory specifické pro úlohy, které jsou volány vaší aplikací. Příklady: *SignUpOrSignin.xml*, *ProfileEdit.xml* *PasswordReset.xml*

V tomto článku upravíte vlastní soubory zásad XML v **SocialAndLocalAccounts** Starter Pack. Pokud potřebujete editor XML, zkuste [Visual Studio Code](https://code.visualstudio.com/download), zjednodušený editor pro více platforem.

### <a name="get-the-starter-pack"></a>Získat Startovní sadu

Získejte vlastní úvodní sady zásad z GitHubu a pak aktualizujte soubory XML v SocialAndLocalAccounts Starter Pack pomocí názvu klienta Azure AD B2C.

1. [Stáhněte si soubor. zip](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/archive/master.zip) nebo naklonujte úložiště:

    ```console
    git clone https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack
    ```

1. Ve všech souborech v adresáři **SocialAndLocalAccounts** nahraďte řetězec `yourtenant` názvem vašeho tenanta Azure AD B2C.

    Například pokud je název vašeho tenanta B2C *contosotenant*, všechny instance `yourtenant.onmicrosoft.com` se stanou `contosotenant.onmicrosoft.com` .

### <a name="add-application-ids-to-the-custom-policy"></a>Přidání ID aplikace do vlastních zásad

Přidejte ID aplikace do souboru rozšíření *TrustFrameworkExtensions.xml*.

1. Otevřete `SocialAndLocalAccounts/` **`TrustFrameworkExtensions.xml`** a vyhledejte element `<TechnicalProfile Id="login-NonInteractive">` .
1. Nahraďte obě instance `IdentityExperienceFrameworkAppId` s ID aplikace aplikace IdentityExperienceFramework, kterou jste vytvořili dříve.
1. Nahraďte obě instance `ProxyIdentityExperienceFrameworkAppId` s ID aplikace aplikace ProxyIdentityExperienceFramework, kterou jste vytvořili dříve.
1. Soubor uložte.

## <a name="upload-the-policies"></a>Nahrajte zásady.

1. V Azure Portal vyberte položku nabídky **rozhraní identity Experience Framework** v tenantovi B2C.
1. Vyberte **Odeslat vlastní zásadu**.
1. V tomto pořadí nahrajte soubory zásad:
    1. *TrustFrameworkBase.xml*
    1. *TrustFrameworkExtensions.xml*
    1. *SignUpOrSignin.xml*
    1. *ProfileEdit.xml*
    1. *PasswordReset.xml*

Při nahrávání souborů Azure přidá předponu `B2C_1A_` do každého.

> [!TIP]
> Pokud editor XML podporuje ověřování, ověřte soubory oproti `TrustFrameworkPolicy_0.3.0.0.xsd` schématu XML, které se nachází v kořenovém adresáři úvodní sady. Ověřování schématu XML identifikuje chyby před odesláním.

## <a name="test-the-custom-policy"></a>Testování vlastních zásad

1. V části **vlastní zásady** vyberte **B2C_1A_signup_signin**.
1. Pro **možnost vybrat aplikaci** na stránce Přehled v části vlastní zásady vyberte webovou aplikaci s názvem *WebApp1* , kterou jste předtím zaregistrovali.
1. Ujistěte se, že **Adresa URL odpovědi** je `https://jwt.ms` .
1. Vyberte **Spustit nyní**.
1. Zaregistrujte se pomocí e-mailové adresy.
1. Znovu vyberte **Spustit** .
1. Přihlaste se pomocí stejného účtu, abyste měli jistotu, že máte správnou konfiguraci.

## <a name="add-facebook-as-an-identity-provider"></a>Přidat Facebook jako zprostředkovatele identity

Jak je uvedeno v [požadavcích](#prerequisites), Facebook *se nevyžaduje* pro použití vlastních zásad, ale tady se používá k předvedení, jak můžete povolit federované sociální přihlášení ve vlastních zásadách.

1. V `SocialAndLocalAccounts/` **`TrustFrameworkExtensions.xml`** souboru nahraďte hodnotu hodnotou `client_id` ID aplikace Facebook:

   ```xml
   <TechnicalProfile Id="Facebook-OAUTH">
     <Metadata>
     <!--Replace the value of client_id in this technical profile with the Facebook app ID"-->
       <Item Key="client_id">00000000000000</Item>
   ```

1. Nahrajte soubor *TrustFrameworkExtensions.xml* do svého tenanta.
1. V části **vlastní zásady** vyberte **B2C_1A_signup_signin**.
1. Vyberte **spustit hned** a vyberte Facebook pro přihlášení pomocí Facebooku a otestujte vlastní zásady.

## <a name="next-steps"></a>Další kroky

Potom zkuste přidat Azure Active Directory (Azure AD) jako zprostředkovatele identity. Základní soubor použitý v této příručce Začínáme už obsahuje nějaký obsah, který potřebujete pro přidání dalších poskytovatelů identity, jako je Azure AD. Informace o nastavení služby Azure AD jako zprostředkovatele identity najdete v tématu [Nastavení registrace a přihlášení pomocí účtu Azure Active Directory s využitím Active Directory B2C vlastních zásad](identity-provider-azure-ad-single-tenant.md). 

Další informace o tom, jak implementovat integraci ISV pomocí vlastních zásad, najdete v naší [galerii partnerů](partner-gallery.md) . 
