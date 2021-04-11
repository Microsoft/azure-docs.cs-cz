---
title: Kurz – vytvoření uživatelských toků a vlastních zásad – Azure Active Directory B2C
description: V tomto kurzu se dozvíte, jak pomocí Azure Portal vytvořit uživatelské toky a vlastní zásady, které umožňují registraci, přihlašování a upravování uživatelských profilů pro vaše aplikace v Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: tutorial
ms.date: 04/08/2021
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: e41c1e74dbe428ee38d4480a1587050b7f96a55f
ms.sourcegitcommit: b28e9f4d34abcb6f5ccbf112206926d5434bd0da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/09/2021
ms.locfileid: "107226222"
---
# <a name="tutorial-create-user-flows-in-azure-active-directory-b2c"></a>Kurz: vytvoření toků uživatelů v Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

Ve vašich aplikacích můžete mít uživatelské toky, které uživatelům umožňují registraci, přihlašování a správu svého profilu. V tenantovi Azure Active Directory B2C (Azure AD B2C) můžete vytvořit více uživatelských toků různých typů a podle potřeby je ve svých aplikacích používat. Toky uživatelů se dají znovu použít napříč aplikacemi.

::: zone pivot="b2c-user-flow"
Tok uživatele umožňuje určit, jak uživatelé budou pracovat s vaší aplikací, když provádějí věci, jako je přihlášení, registrace, úprava profilu nebo resetování hesla. V tomto článku získáte informace o těchto tématech:
::: zone-end

::: zone pivot="b2c-custom-policy"
[Vlastní zásady](custom-policy-overview.md) jsou konfigurační soubory, které definují chování klienta Azure Active Directory B2C (Azure AD B2C). V tomto článku získáte informace o těchto tématech:
::: zone-end

> [!div class="checklist"]
> * Vytvoření uživatelského toku pro registraci a přihlašování
> * Povolení samoobslužného resetování hesel
> * Vytvoření toku uživatele upravujícího profil

::: zone pivot="b2c-user-flow"
> [!IMPORTANT]
> Změnili jsme způsob, jakým označujeme verze toků uživatelů. Dříve jsme nabízeli verze v1 (připravené pro produkční prostředí) a verze v1.1 a v2 (Preview). Nyní jsme konsolidované toky uživatelů **doporučili doporučeným** (ve verzi Preview nové generace) a **standardní** (všeobecně dostupné) verze. Od **1. srpna 2021** se toky všech uživatelských toků verze Preview a v2 starší verze nacházejí v cestě k vyřazení. Podrobnosti najdete v tématu [verze toku uživatele v Azure AD B2C](user-flow-versions.md).
::: zone-end

## <a name="prerequisites"></a>Požadavky

::: zone pivot="b2c-user-flow"
- Pokud ho ještě nemáte, [Vytvořte klienta Azure AD B2C](tutorial-create-tenant.md) , který je propojený s vaším předplatným Azure.
- [Zaregistrujte svoji aplikaci](tutorial-register-applications.md) v tenantovi, kterou jste vytvořili, aby mohla komunikovat s Azure AD B2C.
::: zone-end

::: zone pivot="b2c-custom-policy"
- Pokud ho ještě nemáte, [Vytvořte klienta Azure AD B2C](tutorial-create-tenant.md) , který je propojený s vaším předplatným Azure.
- [Zaregistrujte svoji aplikaci](tutorial-register-applications.md) v tenantovi, kterou jste vytvořili, aby mohla komunikovat s Azure AD B2C.
- Abyste mohli konfigurovat facebookovou aplikaci, proveďte kroky v části [Nastavení registrace a přihlášení pomocí účtu Facebook](identity-provider-facebook.md) . I když se aplikace Facebook nevyžaduje pro použití vlastních zásad, používá se v tomto návodu k předvedení povolení sociálního přihlášení ve vlastních zásadách.
::: zone-end

::: zone pivot="b2c-user-flow"
## <a name="create-a-sign-up-and-sign-in-user-flow"></a>Vytvoření uživatelského toku pro registraci a přihlašování

Tok uživatelů registrace a přihlašování zpracovává jak registraci, tak i přihlašovací prostředí s jedinou konfigurací. Uživatelé vaší aplikace jsou ve správném umístění v závislosti na kontextu.

1. Přihlaste se na [Azure Portal](https://portal.azure.com).
1. Na panelu nástrojů na portálu vyberte ikonu **adresář + předplatné** a pak vyberte adresář, který obsahuje vašeho tenanta Azure AD B2C.

    ![B2C, okno klienta, adresáře a předplatného, Azure Portal](./media/tutorial-create-user-flows/directory-subscription-pane.png)

1. V Azure Portal vyhledejte a vyberte **Azure AD B2C**.
1. V části **zásady** vyberte **toky uživatelů** a pak vyberte **Nový tok uživatele**.

    ![Stránka toky uživatelů na portálu s zvýrazněným tlačítkem nový uživatelský tok](./media/tutorial-create-user-flows/signup-signin-user-flow.png)

1. Na stránce **vytvořit tok uživatele** vyberte možnost **registrace a přihlášení** uživatele.

    ![Výběr stránky toku uživatelů pomocí registrace a zvýrazněného toku pro přihlášení](./media/tutorial-create-user-flows/select-user-flow-type.png)

1. V části **vybrat verzi** vyberte **doporučeno** a pak vyberte **vytvořit**. (Další[informace](user-flow-versions.md) o verzích toku uživatele)

    ![Stránka vytvoření toku uživatele v Azure Portal se zvýrazněnými vlastnostmi](./media/tutorial-create-user-flows/select-version.png)

1. Zadejte **název** toku uživatele. Například *signupsignin1*.
1. V případě **zprostředkovatelů identity** vyberte **registrace e-mailu**.
1. V případě **atributů a deklarací uživatelů** vyberte deklarace identity a atributy, které chcete shromáždit a odeslat uživateli během registrace. Vyberte například možnost **Zobrazit více** a pak zvolte možnost atributy a deklarace pro **zemi/oblast**, **zobrazované jméno** a **poštovní směrovací číslo**. Klikněte na **OK**.

    ![Stránka pro výběr atributů a deklarací se třemi vybranými deklaracemi](./media/tutorial-create-user-flows/signup-signin-attributes.png)

1. Kliknutím na **vytvořit** přidejte tok uživatele. Předpona *B2C_1* je automaticky Předpona názvu.

### <a name="test-the-user-flow"></a>Testování toku uživatele

1. Vyberte tok uživatele, který jste vytvořili, a otevřete jeho stránku Přehled a pak vyberte **Spustit tok uživatele**.
1. V poli **aplikace** vyberte webovou aplikaci s názvem *WebApp1* , kterou jste předtím zaregistrovali. Měla by se zobrazit **Adresa URL odpovědi** `https://jwt.ms` .
1. Klikněte na **Spustit tok uživatele** a pak vyberte **zaregistrovat se hned**.

    ![Stránka spustit tok uživatele na portálu s zvýrazněným tlačítkem Spustit uživatele toku](./media/tutorial-create-user-flows/signup-signin-run-now.PNG)

1. Zadejte platnou e-mailovou adresu, klikněte na **Odeslat ověřovací kód**, zadejte ověřovací kód, který jste obdrželi, a pak vyberte **ověřit kód**.
1. Zadejte nové heslo a potvrďte ho.
1. Vyberte zemi a oblast, zadejte název, který chcete zobrazit, zadejte poštovní kód a potom klikněte na **vytvořit**. Token se vrátí do `https://jwt.ms` a měl by se vám zobrazovat.
1. Nyní můžete tok uživatele spustit znovu a měli byste být schopni se přihlásit pomocí účtu, který jste vytvořili. Vrácený token obsahuje deklarace identity, které jste vybrali v poli Země/oblast, jméno a PSČ.

> [!NOTE]
> Prostředí "spuštění toku uživatele" není aktuálně kompatibilní s typem adresy URL odpovědi zabezpečeného hesla pomocí toku autorizačního kódu. Pokud chcete pro tyto typy aplikací používat zkušenosti s uživatelským tokem, zaregistrujte adresu URL odpovědi typu web a povolte implicitní tok, jak je popsáno [zde](tutorial-register-spa.md).

## <a name="enable-self-service-password-reset"></a>Povolení samoobslužného resetování hesel

Povolení [samoobslužného resetování hesla](add-password-reset-policy.md) pro uživatelský tok registrace nebo přihlašování:

1. Vyberte uživatelský tok registrace nebo přihlašování, který jste vytvořili.
1. V části **Nastavení** v levé nabídce vyberte **vlastnosti**.
1. V části **složitost hesla** vyberte **Samoobslužné resetování hesla**.
1. Vyberte **Uložit**.

### <a name="test-the-user-flow"></a>Testování toku uživatele

1. Vyberte tok uživatele, který jste vytvořili, a otevřete jeho stránku Přehled a pak vyberte **Spustit tok uživatele**.
1. V poli **aplikace** vyberte webovou aplikaci s názvem *WebApp1* , kterou jste předtím zaregistrovali. Měla by se zobrazit **Adresa URL odpovědi** `https://jwt.ms` .
1. Vyberte **Spustit tok uživatele**.
1. Na stránce registrace nebo přihlášení vyberte **zapomenuté heslo?**.
1. Ověřte e-mailovou adresu účtu, který jste vytvořili dříve, a pak vyberte **pokračovat**.
1. Teď máte možnost změnit heslo pro uživatele. Změňte heslo a vyberte **pokračovat**. Token se vrátí do `https://jwt.ms` a měl by se vám zobrazovat.

## <a name="create-a-profile-editing-user-flow"></a>Vytvoření toku uživatele upravujícího profil

Pokud chcete uživatelům umožnit úpravy svého profilu v aplikaci, použijte uživatelský tok upravující profil.

1. V nabídce na stránce Přehled klienta Azure AD B2C vyberte **toky uživatelů** a pak vyberte **Nový tok uživatele**.
1. Na stránce **vytvořit tok uživatele** vyberte uživatelský tok **upravující profil** . 
1. V části **vybrat verzi** vyberte **doporučeno** a pak vyberte **vytvořit**.
1. Zadejte **název** toku uživatele. Například *profileediting1*.
1. V případě **zprostředkovatelů identity** vyberte **přihlášení k místnímu účtu**.
2. V případě **atributů uživatele** vyberte atributy, které má zákazník ve svém profilu upravovat. Vyberte například **Zobrazit více** a potom zvolte atributy i deklarace identity pro **zobrazované jméno** a **název úlohy**. Klikněte na **OK**.
3. Kliknutím na **vytvořit** přidejte tok uživatele. K názvu se automaticky připojí předpona *B2C_1* .

### <a name="test-the-user-flow"></a>Testování toku uživatele

1. Vyberte tok uživatele, který jste vytvořili, a otevřete jeho stránku Přehled a pak vyberte **Spustit tok uživatele**.
1. V poli **aplikace** vyberte webovou aplikaci s názvem *WebApp1* , kterou jste předtím zaregistrovali. Měla by se zobrazit **Adresa URL odpovědi** `https://jwt.ms` .
1. Klikněte na **Spustit tok uživatele** a pak se přihlaste pomocí účtu, který jste vytvořili dříve.
1. Teď máte příležitost změnit zobrazované jméno a název úlohy pro uživatele. Klikněte na **Pokračovat**. Token se vrátí do `https://jwt.ms` a měl by se vám zobrazovat.
::: zone-end

::: zone pivot="b2c-custom-policy"
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

::: zone-end
## <a name="next-steps"></a>Další kroky

V tomto článku jste zjistili, jak:

> [!div class="checklist"]
> * Vytvoření uživatelského toku pro registraci a přihlašování
> * Vytvoření toku uživatele upravujícího profil
> * Vytvoření toku uživatele pro resetování hesla

V dalším kroku se dozvíte, jak používat Azure AD B2C k přihlášení a registraci uživatelů v aplikaci. Postupujte podle webové aplikace ASP.NET, která je propojena níže, nebo v obsahu v části **ověřování uživatelů** přejděte do jiné aplikace.

> [!div class="nextstepaction"]
> [Kurz: povolení ověřování ve webové aplikaci pomocí Azure AD B2C >](tutorial-web-app-dotnet.md)
