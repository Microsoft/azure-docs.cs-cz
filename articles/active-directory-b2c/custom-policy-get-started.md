---
title: Začínáme s vlastními zásadami
titleSuffix: Azure AD B2C
description: Přečtěte si, jak začít s vlastními zásadami ve službě Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/28/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 856bd6c2a3546a438293e89a0b576e1392d9c6a5
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81407294"
---
# <a name="get-started-with-custom-policies-in-azure-active-directory-b2c"></a>Začínáme s vlastními zásadami ve službě Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

[Vlastní zásady](custom-policy-overview.md) jsou konfigurační soubory, které definují chování vašeho klienta Azure Active Directory B2C (Azure AD B2C). V tomto článku vytvoříte vlastní zásady, které podporují registraci nebo přihlášení místního účtu pomocí e-mailové adresy a hesla. Můžete také připravit prostředí pro přidání zprostředkovatelů identity.

## <a name="prerequisites"></a>Požadavky

- Pokud ještě nemáte, [vytvořte klienta Azure AD B2C,](tutorial-create-tenant.md) který je propojený s vaším předplatným Azure.
- [Zaregistrujte svou aplikaci](tutorial-register-applications.md) v tenantovi, který jste vytvořili, aby mohla komunikovat s Azure AD B2C.
- Pro konfigurací facebookové aplikace proveďte postup v části [Nastavení registrace a přihlášení pomocí účtu na Facebooku.](identity-provider-facebook.md) I když aplikace Facebook není vyžadována pro použití vlastních zásad, používá se v tomto návodu k prokázání povolení přihlášení k sociální síti ve vlastních zásadách.

## <a name="add-signing-and-encryption-keys"></a>Přidání podpisových a šifrovacích klíčů

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
1. Vyberte ikonu **Directory + Subscription** na panelu nástrojů portálu a pak vyberte adresář, který obsahuje vašeho klienta Azure AD B2C.
1. Na webu Azure Portal vyhledejte a vyberte **Azure AD B2C**.
1. Na stránce s přehledem vyberte v části **Zásady** **položku Architektura prostředí identity**.

### <a name="create-the-signing-key"></a>Vytvoření podpisového klíče

1. Vyberte **klávesy zásad** a pak vyberte **Přidat**.
1. V části `Generate` **Možnosti**zvolte .
1. Do **pole** `TokenSigningKeyContainer`Název zadejte . Předpona `B2C_1A_` může být přidána automaticky.
1. V **popřípadě vyberte**možnost **RSA**.
1. V **části Použití klíče**vyberte možnost **Podpis**.
1. Vyberte **Vytvořit**.

### <a name="create-the-encryption-key"></a>Vytvoření šifrovacího klíče

1. Vyberte **klávesy zásad** a pak vyberte **Přidat**.
1. V části `Generate` **Možnosti**zvolte .
1. Do **pole** `TokenEncryptionKeyContainer`Název zadejte . Předpona `B2C_1A`_ může být přidána automaticky.
1. V **popřípadě vyberte**možnost **RSA**.
1. V **případě použití klíče**vyberte možnost **Šifrování**.
1. Vyberte **Vytvořit**.

### <a name="create-the-facebook-key"></a>Vytvoření facebookového klíče

Jako klíč zásad přidejte [tajný klíč aplikace Facebook.](identity-provider-facebook.md) Tajný klíč aplikace aplikace, kterou jste vytvořili, můžete použít jako součást požadavků tohoto článku.

1. Vyberte **klávesy zásad** a pak vyberte **Přidat**.
1. V části `Manual` **Možnosti**zvolte .
1. Do **pole** `FacebookSecret`Název zadejte . Předpona `B2C_1A_` může být přidána automaticky.
1. V **tajném**, zadejte aplikace *Facebook App Secret* z developers.facebook.com. Tato hodnota je tajný klíč, nikoli ID aplikace.
1. V **části Použití klíče**vyberte možnost **Podpis**.
1. Vyberte **Vytvořit**.

## <a name="register-identity-experience-framework-applications"></a>Registrace aplikací rozhraní Identity Experience Framework

Azure AD B2C vyžaduje, abyste zaregistrovat dvě aplikace, které používá k registraci a přihlášení uživatelů s místními účty: *IdentityExperienceFramework*, webové rozhraní API a *ProxyIdentityExperienceFramework*, nativní aplikace s delegovaným oprávněním k aplikaci IdentityExperienceFramework. Uživatelé se mohou zaregistrovat pomocí e-mailové adresy nebo uživatelského jména a hesla pro přístup k aplikacím registrovaným na tenanta, které vytvoří "místní účet". Místní účty existují jenom ve vašem tenantovi Azure AD B2C.

Tyto dvě aplikace je třeba zaregistrovat v tenantovi Azure AD B2C pouze jednou.

### <a name="register-the-identityexperienceframework-application"></a>Registrace aplikace IdentityExperienceFramework

Chcete-li zaregistrovat aplikaci ve vašem tenantovi Azure AD B2C, můžete použít **prostředí registrace aplikací (Legacy)** nebo naše nové jednotné **registrace aplikací (Preview)** prostředí. [Další informace o novém prostředí](https://aka.ms/b2cappregintro).

#### <a name="applications"></a>[Aplikace](#tab/applications/)

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
1. Na webu Azure Portal vyhledejte a vyberte **Službu Azure Active Directory**.
1. V nabídce Přehled **služby Azure Active Directory** vyberte v části **Správa**registrace **aplikací (starší verze).**
1. Vyberte **Registrace nové aplikace**.
1. Do **pole** `IdentityExperienceFramework`Název zadejte .
1. Pro **typ aplikace**zvolte **Webová aplikace/ROZHRANÍ API**.
1. Do **adresy URL pro přihlášení**zadejte `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com`, kde `your-tenant-name` je název domény klienta Azure AD B2C. Všechny adresy URL by nyní měly používat [b2clogin.com](b2clogin.md).
1. Vyberte **Vytvořit**. Po vytvoření zkopírujte ID aplikace a uložte ho, aby se později použilo.

#### <a name="app-registrations-preview"></a>[Registrace aplikací (náhled)](#tab/app-reg-preview/)

1. Vyberte **Registrace aplikací (preview)** a pak vyberte **Nová registrace**.
1. Do **pole** `IdentityExperienceFramework`Název zadejte .
1. V části **Podporované typy účtů**vyberte možnost Účty pouze v tomto **organizačním adresáři**.
1. V části **Přesměrování identifikátoru URI** `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com`vyberte `your-tenant-name` **web**a zadejte , kde je název domény klienta Azure AD B2C.
1. V **části Oprávnění**zaškrtněte políčko *Udělit oprávnění správce k oprávněním openid a offline_access.*
1. Vyberte **Zaregistrovat**.
1. Zaznamenejte **ID aplikace (klienta)** pro pozdější krok.

Dále vystavit rozhraní API přidáním oboru:

1. V části **Manage**vyberte **Vystavit rozhraní API**.
1. Vyberte **Přidat obor**, pak vyberte **Uložit a pokračujte v** přijímání výchozího identifikátoru URI id aplikace.
1. Zadejte následující hodnoty pro vytvoření oboru, který umožňuje vlastní spuštění zásad v tenantovi Azure AD B2C:
    * **Název oboru**:`user_impersonation`
    * **Zobrazovaný název souhlasu správce**:`Access IdentityExperienceFramework`
    * **Popis souhlasu správce**:`Allow the application to access IdentityExperienceFramework on behalf of the signed-in user.`
1. Vybrat **přidat obor**

* * *

### <a name="register-the-proxyidentityexperienceframework-application"></a>Registrace aplikace ProxyIdentityExperienceFramework

#### <a name="applications"></a>[Aplikace](#tab/applications/)

1. V **registraci aplikací (starší verze)** vyberte **Nová registrace aplikace**.
1. Do **pole** `ProxyIdentityExperienceFramework`Název zadejte .
1. Pro **typ aplikace**zvolte **Nativní**.
1. Do **identifikátoru URI přesměrování**zadejte `myapp://auth`.
1. Vyberte **Vytvořit**. Po vytvoření zkopírujte ID aplikace a uložte ho, aby se později použilo.
1. Vyberte **Nastavení**, pak vyberte **Požadovaná oprávnění**a pak vyberte **Přidat**.
1. Zvolte **Vybrat rozhraní API**, vyhledejte a vyberte **IdentityExperienceFramework**a klepněte na tlačítko **Vybrat**.
1. Zaškrtněte políčko vedle **aplikace Access IdentityExperienceFramework**, klepněte na tlačítko **Vybrat**a potom klepněte na tlačítko **Hotovo**.
1. Vyberte **Udělit oprávnění**a potvrďte výběrem možnosti **Ano**.

#### <a name="app-registrations-preview"></a>[Registrace aplikací (náhled)](#tab/app-reg-preview/)

1. Vyberte **Registrace aplikací (preview)** a pak vyberte **Nová registrace**.
1. Do **pole** `ProxyIdentityExperienceFramework`Název zadejte .
1. V části **Podporované typy účtů**vyberte možnost Účty pouze v tomto **organizačním adresáři**.
1. V části **Identifikátor URI přesměrování**vyberte pomocí rozevíracího seznamu Veřejný **klient/nativní (mobilní & plochy).**
1. Do **identifikátoru URI přesměrování**zadejte `myapp://auth`.
1. V **části Oprávnění**zaškrtněte políčko *Udělit oprávnění správce k oprávněním openid a offline_access.*
1. Vyberte **Zaregistrovat**.
1. Zaznamenejte **ID aplikace (klienta)** pro pozdější krok.

Dále určete, že aplikace by měla být považována za veřejného klienta:

1. V části **Manage**vyberte **Authentication**.
1. Vyberte **Vyzkoušet nové prostředí** (pokud je zobrazeno).
1. V části **Upřesnit nastavení**povolte **Treat application as a public client** (vyberte **Yes**).
1. Vyberte **Uložit**.

Nyní udělte oprávnění k oboru rozhraní API, který jste dříve vystavili v registraci *IdentityExperienceFramework:*

1. V části **Manage**vyberte **oprávnění rozhraní API**.
1. V části **Nakonfigurovaná oprávnění**vyberte **Přidat oprávnění**.
1. Vyberte kartu **Moje rozhraní API** a pak vyberte aplikaci **IdentityExperienceFramework.**
1. V části **Oprávnění**vyberte **user_impersonation** oboru, který jste definovali dříve.
1. Vyberte **Přidat oprávnění**. Podle pokynů počkejte několik minut, než přejdete k dalšímu kroku.
1. Vyberte **Udělit souhlas správce (název vašeho klienta).**
1. Vyberte aktuálně přihlášený účet správce nebo se přihlaste pomocí účtu v tenantovi Azure AD B2C, kterému byla přiřazena alespoň role *správce cloudových aplikací.*
1. Vyberte **Přijmout**.
1. Vyberte **aktualizovat**a ověřte, zda je povoleno "Uděleno pro ...". zobrazí v části **Stav** pro oba obory. Může trvat několik minut, než se oprávnění rozšíří.

* * *

## <a name="custom-policy-starter-pack"></a>Počáteční balíček vlastních zásad

Vlastní zásady jsou sada souborů XML, které nahrajete do klienta Azure AD B2C k definování technických profilů a cest uživatelů. Poskytujeme startovací balíčky s několika předem nastavenými zásadami, které vám pomohou rychle začít. Každá z těchto startovních sad obsahuje nejmenší počet technických profilů a cest uživatelů potřebných k dosažení popsaných scénářů:

- **LocalAccounts -** Umožňuje použití pouze místní účty.
- **SocialAccounts -** Umožňuje použití pouze sociálních (nebo federovaných) účtů.
- **SocialAndLocalAccounts** - Umožňuje použití místních i sociálních účtů.
- **SocialAndLocalAccountsWithMFA** – Umožňuje možnosti sociálního, místního a vícefaktorového ověřování.

Každé startovací balení obsahuje:

- **Základní soubor** - Několik úprav jsou vyžadovány na základní. Příklad: *TrustFrameworkBase.xml*
- **Přípona -** Tento soubor je místo, kde se provádí většina změn konfigurace. Příklad: *TrustFrameworkExtensions.xml*
- **Předávající strany soubory** - soubory specifické pro úlohy volané vaší aplikací. Příklady: *SignUpOrSignin.xml*, *ProfileEdit.xml*, *PasswordReset.xml*

V tomto článku upravíte soubory vlastních zásad XML ve startovacím balíčku **SocialAndLocalAccounts.** Pokud potřebujete editor XML, zkuste [Visual Studio Kód](https://code.visualstudio.com/download), lehký editor pro více platforem.

### <a name="get-the-starter-pack"></a>Získejte startovní balíček

Získejte vlastní zásady starter pack z GitHubu, pak aktualizovat soubory XML v SocialAndLocalAccounts startovací balíček s názvem klienta Azure AD B2C.

1. [Stáhněte si soubor ZIP](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/archive/master.zip) nebo naklonujte úložiště:

    ```console
    git clone https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack
    ```

1. Ve všech souborech v adresáři **SocialAndLocalAccounts** nahraďte řetězec `yourtenant` názvem vašeho klienta Azure AD B2C.

    Například pokud název klienta B2C je *contosotenant*, `yourtenant.onmicrosoft.com` `contosotenant.onmicrosoft.com`všechny instance become .

### <a name="add-application-ids-to-the-custom-policy"></a>Přidání ID aplikace do vlastní chodnících

Přidejte ID aplikace do souboru přípony *TrustFrameworkExtensions.xml*.

1. Otevřete `SocialAndLocalAccounts/` **`TrustFrameworkExtensions.xml`** a `<TechnicalProfile Id="login-NonInteractive">`vyhledejte prvek .
1. Nahraďte obě `IdentityExperienceFrameworkAppId` instance ID aplikace identityExperienceFramework, kterou jste vytvořili dříve.
1. Nahraďte obě `ProxyIdentityExperienceFrameworkAppId` instance ID aplikace proxyIdentityExperienceFramework, kterou jste vytvořili dříve.
1. Uložte soubor.

## <a name="upload-the-policies"></a>Nahrát zásady

1. Vyberte položku nabídky **rozhraní Identity Experience Framework** ve vašem tenantovi B2C na webu Azure Portal.
1. Vyberte **Nahrát vlastní zásady**.
1. V tomto pořadí nahrajte soubory zásad:
    1. *Soubor TrustFrameworkBase.xml*
    1. *TrustFrameworkExtensions.xml*
    1. *Soubor SignUpOrSignin.xml*
    1. *ProfileEdit.xml*
    1. *PasswordReset.xml*

Při nahrávání souborů Azure přidá předponu `B2C_1A_` do každého.

> [!TIP]
> Pokud váš editor XML podporuje ověření, `TrustFrameworkPolicy_0.3.0.0.xsd` ověřte soubory proti schématu XML, které je umístěno v kořenovém adresáři počátečního balíčku. Ověření schématu XML identifikuje chyby před odesláním.

## <a name="test-the-custom-policy"></a>Testování vlastních zásad

1. V části **Vlastní zásady**vyberte **B2C_1A_signup_signin**.
1. V **části Vybrat aplikaci** na stránce s přehledem vlastních zásad vyberte webovou aplikaci s názvem *webapp1,* kterou jste dříve zaregistrovali.
1. Zkontrolujte, **Reply URL** zda je `https://jwt.ms`adresa URL odpovědi .
1. Vyberte **Spustit nyní**.
1. Zaregistrujte se pomocí e-mailové adresy.
1. Vyberte **Spustit nyní** znovu.
1. Přihlaste se pomocí stejného účtu a ověřte, zda máte správnou konfiguraci.

## <a name="add-facebook-as-an-identity-provider"></a>Přidání Facebooku jako poskytovatele identity

Jak je uvedeno v [požadavky](#prerequisites), Facebook *není* nutné pro použití vlastní zásady, ale slouží zde k prokázání, jak můžete povolit federované sociální přihlášení ve vlastní zásady.

1. V `SocialAndLocalAccounts/` **`TrustFrameworkExtensions.xml`** souboru nahraďte `client_id` hodnotu ID aplikace Facebook:

   ```xml
   <TechnicalProfile Id="Facebook-OAUTH">
     <Metadata>
     <!--Replace the value of client_id in this technical profile with the Facebook app ID"-->
       <Item Key="client_id">00000000000000</Item>
   ```

1. Nahrajte soubor *TrustFrameworkExtensions.xml* do svého tenanta.
1. V části **Vlastní zásady**vyberte **B2C_1A_signup_signin**.
1. Vyberte **Spustit a** vyberte Facebook, chcete-li se přihlásit přes Facebook a otestovat vlastní zásady.

## <a name="next-steps"></a>Další kroky

Dále zkuste přidat Azure Active Directory (Azure AD) jako zprostředkovatele identity. Základní soubor použitý v tomto příručce Začínáme již obsahuje část obsahu, který potřebujete pro přidání dalších poskytovatelů identit, jako je Azure AD.

Informace o nastavení Azure AD jako poskytovatele identity najdete v tématu [Nastavení registrace a přihlášení pomocí účtu Azure Active Directory pomocí vlastních zásad služby Active Directory B2C](identity-provider-azure-ad-single-tenant-custom.md).
