---
title: Začínáme s vlastními zásadami v Azure Active Directory B2C | Dokumentace Microsoftu
description: Jak začít s Azure Active Directory B2C vlastními zásadami.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/04/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 36fad697758273246d567dfa1010f0e6bfc68939
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/31/2018
ms.locfileid: "43344558"
---
# <a name="azure-active-directory-b2c-get-started-with-custom-policies"></a>Azure Active Directory B2C: Začínáme s vlastními zásadami

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Po dokončení kroků v tomto článku se bude podporovat vaše vlastní zásada vypadat "místní účet" registrace nebo přihlašování prostřednictvím e-mailovou adresu a heslo. Bude také Příprava prostředí pro přidání poskytovatele identity (jako je Facebook nebo Azure Active Directory). Doporučujeme, abyste mohli provést tyto kroky předtím, než výklad o dalších možnostech použití rozhraní prostředí pro Identity Azure Active Directory (Azure AD) B2C.

## <a name="prerequisites"></a>Požadavky

Než budete pokračovat, ujistěte se, že máte tenanta služby Azure AD B2C, což je kontejner pro všechny uživatele, aplikace, zásady a další. Pokud je nemáte, budete muset [vytvoření tenanta Azure AD B2C](active-directory-b2c-get-started.md). Důrazně doporučujeme všem vývojářům dokončili názorné postupy integrované zásady Azure AD B2C a konfigurace aplikací pomocí integrovaných zásad, než budete pokračovat. Vaše aplikace bude fungovat s oběma typy zásad po provést drobnou změnu k názvu zásady se vyvolat vlastní zásady.

>[!NOTE]
>Pro přístup k vlastní zásady pro úpravy, budete potřebovat platné předplatné Azure propojené s vašeho tenanta. Pokud jste tak dosud [propojený vašeho tenanta Azure AD B2C s předplatným Azure](active-directory-b2c-how-to-enable-billing.md) nebo předplatného Azure je zakázaná, nebude tlačítko architekturu rozhraní identit k dispozici.

## <a name="add-signing-and-encryption-keys-to-your-b2c-tenant-for-use-by-custom-policies"></a>Přidat podepisování a šifrování klíče tenanta B2C pro použití vlastních zásad

1. Otevřít **architekturu rozhraní identit** okna nastavení tenanta Azure AD B2C.
2. Vyberte **klíče zásad** zobrazíte klíče, které jsou k dispozici ve vašem tenantovi.
3. Pokud neexistuje, vytvořte B2C_1A_TokenSigningKeyContainer:<br>
    a. Vyberte **Přidat**. <br>
    b. Vyberte **generovat**.<br>
    c. Pro **název**, použijte `TokenSigningKeyContainer`. <br> 
    Předpona, která `B2C_1A_` může být automaticky přidán.<br>
    d. Pro **typ klíče**, použijte **RSA**.<br>
    e. Pro **data**, ponechte výchozí nastavení. <br>
    f. Pro **použití klíče**, použijte **podpis**.<br>
    g. Vyberte **Vytvořit**.<br>
4. Pokud neexistuje, vytvořte B2C_1A_TokenEncryptionKeyContainer:<br>
 a. Vyberte **Přidat**.<br>
 b. Vyberte **generovat**.<br>
 c. Pro **název**, použijte `TokenEncryptionKeyContainer`. <br>
   Předpona, která `B2C_1A`_, mohou být přidány automaticky.<br>
 d. Pro **typ klíče**, použijte **RSA**.<br>
 e. Pro **data**, ponechte výchozí nastavení.<br>
 f. Pro **použití klíče**, použijte **šifrování**.<br>
 g. Vyberte **Vytvořit**.<br>
5. Vytvoření B2C_1A_FacebookSecret. <br>
Pokud už máte tajný klíč aplikace Facebook, přidejte ho jako klíč zásad do vašeho tenanta. V opačném případě vytvořte klíč s zástupnou hodnotu tak, aby vaše zásady projít ověřením.<br>
 a. Vyberte **Přidat**.<br>
 b. Pro **možnosti**, použijte **ruční**.<br>
 c. Pro **název**, použijte `FacebookSecret`. <br>
 Předpona, která `B2C_1A_` může být automaticky přidán.<br>
 d. V **tajný kód** zadejte vaše FacebookSecret z developers.facebook.com nebo `0` jako zástupný symbol. *Toto není vaše ID aplikace sítě Facebook.* <br>
 e. Pro **použití klíče**, použijte **podpis**. <br>
 f. Vyberte **vytvořit** a ověřte vytvoření.

## <a name="register-identity-experience-framework-applications"></a>Registrovat architekturu rozhraní identit aplikace

Azure AD B2C, musíte zaregistrovat dva další aplikace, které se používají modulem pro registraci a přihlašování uživatelů.

>[!NOTE]
>Musíte vytvořit dvě aplikace tento povolit přihlášení pomocí místních účtů: IdentityExperienceFramework (webová aplikace) a ProxyIdentityExperienceFramework (nativní aplikace) s delegovaná oprávnění z aplikace IdentityExperienceFramework. Místní účty existují pouze ve vašem tenantovi. Vaši uživatelé zaregistrovat pomocí kombinace jedinečnou e-mailovou adresu a heslo pro přístup k aplikacím vašeho tenanta zaregistrované.

### <a name="create-the-identityexperienceframework-application"></a>Vytvoření aplikace IdentityExperienceFramework

1. V [webu Azure portal](https://portal.azure.com), přepněte do [kontextu vašeho tenanta Azure AD B2C](active-directory-b2c-navigate-to-b2c-context.md).
2. Otevřít **Azure Active Directory** okno (ne **Azure AD B2C** okno). Musíte vybrat **další služby** ho najít.
3. Vyberte **Registrace aplikací**.
4. Vyberte **Registrace nové aplikace**.
   * Pro **název**, použijte `IdentityExperienceFramework`.
   * Pro **typ aplikace**, použijte **webové aplikace nebo rozhraní API**.
   * Pro **přihlašovací adresa URL**, použijte `https://yourtenant.b2clogin.com/yourtenant.onmicrosoft.com`, kde `yourtenant` je název domény tenanta Azure AD B2C.
5. Vyberte **Vytvořit**.
6. Jakmile ho vytvoříte, vyberte nově vytvořenou aplikaci **IdentityExperienceFramework**.<br>
   * Vyberte **vlastnosti**.<br>
   * Zkopírujte ID aplikace a uložit pro pozdější použití.

### <a name="create-the-proxyidentityexperienceframework-application"></a>Vytvoření aplikace ProxyIdentityExperienceFramework

1. Vyberte **Registrace aplikací**.
1. Vyberte **Registrace nové aplikace**.
   * Pro **název**, použijte `ProxyIdentityExperienceFramework`.
   * Pro **typ aplikace**, použijte **nativní**.
   * Pro **identifikátor URI pro přesměrování**, použijte `https://yourtenant.b2clogin.com/yourtenant.onmicrosoft.com`, kde `yourtenant` je váš tenant Azure AD B2C.
1. Vyberte **Vytvořit**.
1. Po jeho vytvoření, vyberte aplikaci, kterou **ProxyIdentityExperienceFramework**.<br>
   * Vyberte **vlastnosti**. <br>
   * Zkopírujte ID aplikace a uložit pro pozdější použití.
1. Vyberte **požadovaná oprávnění**.
1. Vyberte **Přidat**.
1. Vyberte **Výběr rozhraní API**.
1. Hledat název IdentityExperienceFramework. Vyberte **IdentityExperienceFramework** výsledky a pak klikněte na **vyberte**.
1. Zaškrtněte políčko vedle položky **přístup IdentityExperienceFramework**a potom klikněte na tlačítko **vyberte**.
1. Vyberte **Done** (Hotovo).
1. Vyberte **udělit oprávnění**a potvrďte tak, že vyberete **Ano**.

## <a name="download-starter-pack-and-modify-policies"></a>Úvodní sada stáhnout a upravit zásady

Vlastní zásady jsou sada souborů XML, které je třeba nahrát do vašeho tenanta Azure AD B2C. Poskytujeme sady starter Pack pro zajištění rychlé zahájení práce. Každý starter pack v následujícím seznamu obsahuje nejmenší počet technické profily a jízdy uživatelů potřebné k dosažení popsané scénáře:
 * LocalAccounts. Umožňuje použití pouze místní účty.
 * SocialAccounts. Umožňuje použití pouze účty sociálních sítí (nebo federované).
 * **SocialAndLocalAccounts**. V tomto návodu používáme tento soubor.
 * SocialAndLocalAccountsWithMFA. Možnosti sociální sítě, místní a ověřování službou Multi-Factor Authentication jsou zde zahrnuty.

Každý starter pack obsahuje:

* [Základního souboru](active-directory-b2c-overview-custom.md#policy-files) zásad. Několik změn nutných k základní třídě.
* [Soubor rozšíření](active-directory-b2c-overview-custom.md#policy-files) zásad.  Tento soubor je provedena většinu změn konfigurace.
* [Předávající strana soubory](active-directory-b2c-overview-custom.md#policy-files) jsou soubory specifické úkoly v aplikaci.

>[!NOTE]
>Pokud XML editor podporuje ověřování, ověřte soubory proti schématu TrustFrameworkPolicy_0.3.0.0.xsd XML, který se nachází v kořenovém adresáři starter pack. Ověření schématu XML identifikuje chyby před odesláním.

 Můžeme začít:

1. Stáhněte si active-directory-b2c-custom-policy-starterpack z Githubu. [Stáhněte si soubor .zip](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/archive/master.zip) nebo spuštění

    ```console
    git clone https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack
    ```
2. Otevřete složku SocialAndLocalAccounts.  Základní soubor (TrustFrameworkBase.xml) v této složce obsahuje obsah potřebný pro místní a sociální sítě nebo podnikových účtů. Sociální obsah nebude v konfliktu s kroky pro místní účty zprovoznění.
3. Open TrustFrameworkBase.xml. Pokud potřebujete XML editor [nabídne Visual Studio Code](https://code.visualstudio.com/download), jednoduchý editor napříč platformami.
4. V kořenovém adresáři `TrustFrameworkPolicy` elementu, aktualizace `TenantId` a `PublicPolicyUri` atributy nahrazení `yourtenant.onmicrosoft.com` s názvem domény vašeho tenanta Azure AD B2C:
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
   >`PolicyId` je název zásady, které vidíte na portálu a název, podle kterého se tento soubor zásad odkazují jiné soubory zásad.

5. Uložte soubor.
6. Otevřete TrustFrameworkExtensions.xml. Provést stejné změny dvě nahrazením `yourtenant.onmicrosoft.com` v tenantu Azure AD B2C. Ujistěte se, stejné nahrazení v `<TenantId>` – element pro celkem tři změny. Uložte soubor.
7. Otevřete SignUpOrSignIn.xml. Provést stejné změny tak, že nahradíte `yourtenant.onmicrosoft.com` v tenantu Azure AD B2C na třech místech. Uložte soubor.
8. Otevřete resetování hesla a Profilovat upravit soubory. Provést stejné změny tak, že nahradíte `yourtenant.onmicrosoft.com` v tenantu Azure AD B2C na třech místech v každém souboru. Uložte soubory.

### <a name="add-the-application-ids-to-your-custom-policy"></a>ID aplikace přidat do vlastní zásady
Přidejte do souboru rozšíření ID aplikace (`TrustFrameworkExtensions.xml`):

1. V souboru rozšíření (TrustFrameworkExtensions.xml) najděte prvek `<TechnicalProfile Id="login-NonInteractive">`.
2. Nahraďte oba výskyty `IdentityExperienceFrameworkAppId` s ID aplikace, architekturu rozhraní identit aplikace, kterou jste vytvořili dříve. Zde naleznete příklad:

   ```xml
   <Item Key="IdTokenAudience">8322dedc-cbf4-43bc-8bb6-141d16f0f489</Item>
   ```
3. Nahraďte oba výskyty `ProxyIdentityExperienceFrameworkAppId` s ID aplikace, architekturu rozhraní identit Proxy aplikace, kterou jste vytvořili dříve.
4. Uložte soubor rozšíření.

## <a name="upload-the-policies-to-your-tenant"></a>Odeslání zásady do vašeho tenanta

1. V [webu Azure portal](https://portal.azure.com), přepněte do [kontextu vašeho tenanta Azure AD B2C](active-directory-b2c-navigate-to-b2c-context.md)a otevřete **Azure AD B2C** okno.
2. Vyberte **architekturu rozhraní identit**.
3. Vyberte **nahrát zásady**.

    >[!WARNING]
    >Musí se nahrát vlastní zásady pro soubory v následujícím pořadí:

1. Nahrajte TrustFrameworkBase.xml.
2. Nahrajte TrustFrameworkExtensions.xml.
3. Nahrajte SignUpOrSignin.xml.
4. Odeslání dalších zásad souborů.

Když se nahraje soubor, název souboru zásad začíná `B2C_1A_`.

## <a name="test-the-custom-policy-by-using-run-now"></a>Testování vlastní zásady pomocí možnosti spustit hned

1. Otevřít **nastavení Azure AD B2C** a přejděte na **architekturu rozhraní identit**.

   >[!NOTE]
   >**Spustit nyní** vyžaduje aspoň jednu aplikaci do být registrované u klienta. Aplikace musí být zaregistrovaný v rámci tenanta B2C, buď pomocí **aplikací** výběr nabídky v Azure AD B2C nebo použijete architekturu rozhraní identit k vyvolání předdefinované a vlastní zásady. Je potřeba jen jednu registraci na aplikaci.<br><br>
   Informace o postupu registrace aplikací, najdete v tématu Azure AD B2C [Začínáme](active-directory-b2c-get-started.md) článku nebo [registrace aplikace](active-directory-b2c-app-registration.md) článku.  

2. Otevřete B2C_1A_signup_signin předávající stranu vlastní zásady, které jste nahráli. Vyberte **spustit nyní**.

3. Měli byste být schopni registrace pomocí e-mailovou adresu.

4. Přihlaste se pomocí stejného účtu pro potvrzení, že máte správnou konfiguraci.

>[!NOTE]
>Obvyklou příčinou selhání přihlášení je nesprávně nakonfigurovaný IdentityExperienceFramework aplikace.


## <a name="next-steps"></a>Další postup

### <a name="add-facebook-as-an-identity-provider"></a>Přidat jako zprostředkovatele identity Facebook
Nastavení Facebooku:
1. [Konfigurace aplikace Facebook v developers.facebook.com](active-directory-b2c-setup-fb-app.md).
2. [Přidat tajný klíč aplikace sítě Facebook pro vašeho tenanta Azure AD B2C](#add-signing-and-encryption-keys-to-your-b2c-tenant-for-use-by-custom-policies).
3. V souboru zásad TrustFrameworkExtensions nahraďte hodnotu `client_id` s ID aplikace pro Facebook:

   ```xml
   <TechnicalProfile Id="Facebook-OAUTH">
     <Metadata>
     <!--Replace the value of client_id in this technical profile with the Facebook app ID"-->
       <Item Key="client_id">00000000000000</Item>
   ```
4. Nahrajte soubor zásad TrustFrameworkExtensions.xml do vašeho tenanta.
5. Test s použitím **spustit nyní** nebo vyvoláním zásad přímo z registraci aplikace.

### <a name="add-azure-active-directory-as-an-identity-provider"></a>Přidat Azure Active Directory jako zprostředkovatele identity
Základní soubor už používá v tato příručka Začínáme obsahuje některé obsahu, který je nutné pro přidání dalších poskytovatelů identit. Informace o nastavení přihlášení, najdete v článku [Azure Active Directory B2C: Přihlaste se pomocí účtů v Azure AD](active-directory-b2c-setup-aad-custom.md) článku.

Přehled vlastních zásad v Azure AD B2C, které používají architekturu rozhraní identit najdete v tématu [Azure Active Directory B2C: vlastní zásady](active-directory-b2c-overview-custom.md) článku. 
