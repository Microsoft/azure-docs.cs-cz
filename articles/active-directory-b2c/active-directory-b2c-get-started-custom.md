---
title: 'Azure Active Directory B2C: Začínáme s vlastními zásadami | Microsoft Docs'
description: Jak začít pracovat s Azure Active Directory B2C vlastní zásady
services: active-directory-b2c
documentationcenter: ''
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 08/04/2017
ms.author: davidmu
ms.openlocfilehash: 502f88bd08c05f71988763fedeba4d596aa409b6
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
---
# <a name="azure-active-directory-b2c-get-started-with-custom-policies"></a>Azure Active Directory B2C: Začínáme s vlastní zásady

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Po dokončení kroků v tomto článku, bude vaše vlastní zásada podporovat "místní účet" registrace nebo přihlášení pomocí služby e-mailovou adresu a heslo. Bude také Příprava prostředí pro přidání zprostředkovatelů identity (jako je Facebook nebo Azure Active Directory). Doporučujeme vám k provedení těchto kroků před výklad o dalších používá rozhraní Azure Active Directory (Azure AD) B2C Identity prostředí.

## <a name="prerequisites"></a>Požadavky

Než budete pokračovat, ujistěte se, že máte klienta Azure AD B2C, což je kontejner pro všechny uživatele, aplikace, zásady a další. Pokud nemáte již, budete muset [vytvoření klienta Azure AD B2C](active-directory-b2c-get-started.md). Důrazně doporučujeme všechny vývojářům dokončili názorné postupy, Azure AD B2C předdefinovaných zásad a konfigurace aplikací pomocí předdefinovaných zásad než budete pokračovat. Vaše aplikace bude fungovat s oběma typy zásad po provedení menší změny zásad název vlastní zásady.

>[!NOTE]
>Chcete-li získat přístup k vlastní zásady pro úpravy, musíte platné předplatné Azure, propojené ke klientovi. Pokud jste to ještě [propojené vašeho klienta Azure AD B2C k předplatnému Azure](active-directory-b2c-how-to-enable-billing.md) nebo předplatné Azure je zakázáno, nebudete mít k dispozici tlačítko Identity Framework prostředí.

## <a name="add-signing-and-encryption-keys-to-your-b2c-tenant-for-use-by-custom-policies"></a>Přidejte klíče pro podepisování a šifrování pro vašeho klienta B2C pro použití vlastní zásady

1. Otevřete **Identity rozhraní Framework** okno v nastaveních klienta Azure AD B2C.
2. Vyberte **zásad klíče** zobrazíte klíče, které jsou k dispozici ve vašem klientovi.
3. Pokud neexistuje, vytvořte B2C_1A_TokenSigningKeyContainer:<br>
    a. Vyberte **Přidat**. <br>
    b. Vyberte **generovat**.<br>
    c. Pro **název**, použijte `TokenSigningKeyContainer`. <br> 
    Předpona `B2C_1A_` může automaticky přidat.<br>
    d. Pro **typ klíče**, použijte **RSA**.<br>
    e. Pro **data**, použijte výchozí hodnoty. <br>
    f. Pro **použití klíče**, použijte **podpis**.<br>
    g. Vyberte **Vytvořit**.<br>
4. Pokud neexistuje, vytvořte B2C_1A_TokenEncryptionKeyContainer:<br>
 a. Vyberte **Přidat**.<br>
 b. Vyberte **generovat**.<br>
 c. Pro **název**, použijte `TokenEncryptionKeyContainer`. <br>
   Předpona `B2C_1A`_ může automaticky přidat.<br>
 d. Pro **typ klíče**, použijte **RSA**.<br>
 e. Pro **data**, použijte výchozí hodnoty.<br>
 f. Pro **použití klíče**, použijte **šifrování**.<br>
 g. Vyberte **Vytvořit**.<br>
5. Create B2C_1A_FacebookSecret. <br>
Pokud již máte tajný klíč aplikace Facebook, přidejte ji jako klíč zásad klienta. Jinak musí vytvořit klíč s hodnotu zástupného symbolu, tak, aby vaše zásady projít ověřením.<br>
 a. Vyberte **Přidat**.<br>
 b. Pro **možnosti**, použijte **ruční**.<br>
 c. Pro **název**, použijte `FacebookSecret`. <br>
 Předpona `B2C_1A_` může automaticky přidat.<br>
 d. V **tajný klíč** zadejte vaše FacebookSecret z developers.facebook.com nebo `0` jako zástupný znak. *Toto není vaše ID aplikace sítě Facebook* <br>
 e. Pro **použití klíče**, použijte **podpis**. <br>
 f. Vyberte **vytvořit** a potvrďte vytvoření.

## <a name="register-identity-experience-framework-applications"></a>Registrace aplikací Identity rozhraní Framework

Azure AD B2C musíte zaregistrovat dva další aplikace, které jsou používány modul registrace a přihlašování uživatelů.

>[!NOTE]
>Je nutné vytvořit dvě aplikace této povolit přihlášení pomocí místních účtů: IdentityExperienceFramework (webové aplikace) a ProxyIdentityExperienceFramework (nativní aplikaci) s delegovaná oprávnění z aplikace IdentityExperienceFramework. Místní účty existovat pouze ve vašem klientovi. Vaši uživatelé zaregistrovat kombinaci jedinečnou e-mailovou adresu a heslo pro přístup k vaší aplikace klient zaregistrovaný.

### <a name="create-the-identityexperienceframework-application"></a>Vytvoření aplikace IdentityExperienceFramework

1. V [portál Azure](https://portal.azure.com), přepněte do [kontextu klienta služby Azure AD B2C](active-directory-b2c-navigate-to-b2c-context.md).
2. Otevřete **Azure Active Directory** okno (ne **Azure AD B2C** okno). Možná budete muset vybrat možnost **více služeb** se ho najít.
3. Vyberte **registrace aplikace**.
4. Vyberte **nové registrace aplikace**.
   * Pro **název**, použijte `IdentityExperienceFramework`.
   * Pro **typ aplikace**, použijte **webové aplikace nebo rozhraní API**.
   * Pro **přihlašovací adresa URL**, použijte `https://login.microsoftonline.com/yourtenant.onmicrosoft.com`, kde `yourtenant` je název domény klienta Azure AD B2C.
5. Vyberte **Vytvořit**.
6. Jakmile je vytvořen, vyberte nově vytvořenou aplikaci **IdentityExperienceFramework**.<br>
   * Vyberte **vlastnosti**.<br>
   * Zkopírujte ID aplikace a uložit pro pozdější použití.

### <a name="create-the-proxyidentityexperienceframework-application"></a>Vytvoření aplikace ProxyIdentityExperienceFramework

1. Vyberte **registrace aplikace**.
1. Vyberte **nové registrace aplikace**.
   * Pro **název**, použijte `ProxyIdentityExperienceFramework`.
   * Pro **typ aplikace**, použijte **nativní**.
   * Pro **identifikátor URI pro přesměrování**, použijte `https://login.microsoftonline.com/yourtenant.onmicrosoft.com`, kde `yourtenant` je váš klient Azure AD B2C.
1. Vyberte **Vytvořit**.
1. Po vytvoření, vyberte aplikaci **ProxyIdentityExperienceFramework**.<br>
   * Vyberte **vlastnosti**. <br>
   * Zkopírujte ID aplikace a uložit pro pozdější použití.
1. Vyberte **požadovaná oprávnění**.
1. Vyberte **Přidat**.
1. Vyberte **vybrat rozhraní API**.
1. Vyhledejte název IdentityExperienceFramework. Vyberte **IdentityExperienceFramework** výsledků a pak klikněte na **vyberte**.
1. Zaškrtněte políčko vedle **přístup IdentityExperienceFramework**a potom klikněte na **vyberte**.
1. Vyberte **provádí**.
1. Vyberte **udělit oprávnění**a pak potvrďte výběrem **Ano**.

## <a name="download-starter-pack-and-modify-policies"></a>Stáhnout starter pack a úpravám zásad

Vlastní zásady jsou sada souborů XML, které musí být nahrán do vašeho klienta Azure AD B2C. Poskytujeme starter Pack si budete rychle. Jednotlivé sady starter v následujícím seznamu obsahuje nejmenší počet technické profily a cesty uživatele potřebná k dosažení popsané scénáře:
 * LocalAccounts. Umožňuje použití jenom místní účty.
 * SocialAccounts. Umožňuje využívat jenom účty sociálních (nebo federované).
 * **SocialAndLocalAccounts**. V tomto návodu použijeme tento soubor.
 * SocialAndLocalAccountsWithMFA. Možnosti sociálního, místní a multi-Factor Authentication jsou zde zahrnuty.

Jednotlivé sady starter obsahuje:

* [Základního souboru](active-directory-b2c-overview-custom.md#policy-files) zásad. Několik změn je potřeba ve znalostní bázi.
* [Souboru rozšíření bylo](active-directory-b2c-overview-custom.md#policy-files) zásad.  Tento soubor je, kde jsou vytvářeny většinu změn konfigurace.
* [Předávající strany soubory](active-directory-b2c-overview-custom.md#policy-files) jsou specifické pro úlohy soubory aplikací.

>[!NOTE]
>Pokud XML editor podporuje ověřování, ověřte soubory pro schéma TrustFrameworkPolicy_0.3.0.0.xsd XML, který je umístěný v kořenovém adresáři balíčku starter. Ověření schématu XML označuje chyby před nahráním.

 Můžeme začít:

1. Stáhněte si active-directory-b2c-custom-policy-starterpack z Githubu. [Stáhněte si soubor .zip](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/archive/master.zip) nebo spustit

    ```console
    git clone https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack
    ```
2. Otevřete složku SocialAndLocalAccounts.  Základního souboru (TrustFrameworkBase.xml) v této složce obsahuje obsah potřebné pro místní i sociálního nebo podnikové účty. Sociální obsah není v konfliktu s kroky pro místní účty zprovoznění.
3. Open TrustFrameworkBase.xml. Pokud potřebujete editoru XML [zkuste Visual Studio Code](https://code.visualstudio.com/download), lightweight editor napříč platformami.
4. V kořenovém `TrustFrameworkPolicy` element, aktualizovat `TenantId` a `PublicPolicyUri` atributy, nahraďte `yourtenant.onmicrosoft.com` s názvem domény klienta služby Azure AD B2C:
   ```xml
    <TrustFrameworkPolicy
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
    xmlns:xsd="http://www.w3.org/2001/XMLSchema"
    xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
    PolicySchemaVersion="0.3.0.0"
    TenantId="yourtenant.onmicrosoft.com"
    PolicyId="B2C_1A_TrustFrameworkBase"
    PublicPolicyUri="http://yourtenant.onmicrosoft.com">
    ```
   >[!NOTE]
   >`PolicyId` je název zásady, které vidíte na portálu a název, kterým je tento soubor zásad odkazují jiné soubory zásad.

5. Uložte soubor.
6. Open TrustFrameworkExtensions.xml. Provést stejné změny dva nahrazením `yourtenant.onmicrosoft.com` s vašeho klienta Azure AD B2C. Ujistěte se, stejné náhrada v `<TenantId>` element celkem tři změny. Uložte soubor.
7. Open SignUpOrSignIn.xml. Provést stejné změny nahrazením `yourtenant.onmicrosoft.com` s vašeho klienta Azure AD B2C na třech místech. Uložte soubor.
8. Otevřete resetování hesla a úpravy souborů profilu. Provést stejné změny nahrazením `yourtenant.onmicrosoft.com` s vašeho klienta Azure AD B2C na třech místech do každého souboru. Uložte soubory.

### <a name="add-the-application-ids-to-your-custom-policy"></a>Přidat ID aplikace do vlastních zásad
Přidat ID aplikace souboru rozšíření (`TrustFrameworkExtensions.xml`):

1. V souboru rozšíření (TrustFrameworkExtensions.xml) nalezen element `<TechnicalProfile Id="login-NonInteractive">`.
2. Nahraďte obou instancí `IdentityExperienceFrameworkAppId` s ID aplikace Framework prostředí Identity aplikace, kterou jste vytvořili dříve. Zde naleznete příklad:

   ```xml
   <Item Key="IdTokenAudience">8322dedc-cbf4-43bc-8bb6-141d16f0f489</Item>
   ```
3. Nahraďte obou instancí `ProxyIdentityExperienceFrameworkAppId` s ID aplikace Framework prostředí Identity Proxy aplikace, kterou jste vytvořili dříve.
4. Uložte soubor rozšíření.

## <a name="upload-the-policies-to-your-tenant"></a>Nahrát zásady klienta

1. V [portál Azure](https://portal.azure.com), přepněte do [kontextu klienta služby Azure AD B2C](active-directory-b2c-navigate-to-b2c-context.md)a otevřete **Azure AD B2C** okno.
2. Vyberte **Identity rozhraní Framework**.
3. Vyberte **nahrát zásady**.

    >[!WARNING]
    >Vlastní zásady pro soubory, musí se nahrát v následujícím pořadí:

1. Upload TrustFrameworkBase.xml.
2. Nahrajte TrustFrameworkExtensions.xml.
3. Nahrajte SignUpOrSignin.xml.
4. Odešlete další soubory zásad.

Při odeslání souboru, název souboru zásady, se přidá jako předpona s `B2C_1A_`.

## <a name="test-the-custom-policy-by-using-run-now"></a>Otestovat vlastní zásady pomocí spustit nyní

1. Otevřete **nastavení Azure AD B2C** a přejděte na **Identity rozhraní Framework**.

   >[!NOTE]
   >**Spustit nyní** vyžaduje alespoň jedné aplikace do být preregistered u klienta. Aplikace musí být zaregistrovaný v klienta B2C, buď pomocí **aplikace** výběr nabídky v Azure AD B2C, nebo pomocí rozhraní prostředí Identity k vyvolání předdefinované a vlastní zásady. Je potřeba pouze jedna registrace na aplikaci.<br><br>
   Další postup registrace aplikace najdete v tématu Azure AD B2C [Začínáme](active-directory-b2c-get-started.md) článek nebo [registrace aplikace](active-directory-b2c-app-registration.md) článku.  

2. Otevřete B2C_1A_signup_signin, předávající stranu vlastních zásad, který jste nahráli. Vyberte **spustit nyní**.

3. Nyní byste měli mít zaregistrovat pomocí e-mailovou adresu.

4. Přihlaste se pomocí stejného účtu pro potvrzení, že máte správnou konfiguraci.

>[!NOTE]
>Obvyklou příčinou selhání přihlášení je nesprávně nakonfigurovaný IdentityExperienceFramework aplikace.


## <a name="next-steps"></a>Další postup

### <a name="add-facebook-as-an-identity-provider"></a>Přidat Facebook jako zprostředkovatele identity
Nastavení sítě Facebook:
1. [Konfigurace aplikace Facebook v developers.facebook.com](active-directory-b2c-setup-fb-app.md).
2. [Přidat tajný klíč aplikace Facebook ke klientovi Azure AD B2C](#add-signing-and-encryption-keys-to-your-b2c-tenant-for-use-by-custom-policies).
3. V souboru zásad TrustFrameworkExtensions nahraďte hodnotu `client_id` s ID aplikace pro Facebook:

   ```xml
   <TechnicalProfile Id="Facebook-OAUTH">
     <Metadata>
     <!--Replace the value of client_id in this technical profile with the Facebook app ID"-->
       <Item Key="client_id">00000000000000</Item>
   ```
4. Nahrajte soubor TrustFrameworkExtensions.xml zásad klienta.
5. Test pomocí **spustit nyní** nebo vyvoláním zásad přímo z zaregistrovanou aplikaci.

### <a name="add-azure-active-directory-as-an-identity-provider"></a>Přidat jako zprostředkovatele identity Azure Active Directory
Základního souboru již používán Tato příručka Začínáme obsahuje část obsahu, které potřebujete pro přidání jiných poskytovatelů identit. Informace o nastavení přihlášení najdete v tématu [Azure Active Directory B2C: Přihlaste se pomocí účtů služby Azure AD](active-directory-b2c-setup-aad-custom.md) článku.

Přehled vlastní zásady v Azure AD B2C, které používají rozhraní prostředí Identity, najdete [Azure Active Directory B2C: vlastní zásady](active-directory-b2c-overview-custom.md) článku. 
