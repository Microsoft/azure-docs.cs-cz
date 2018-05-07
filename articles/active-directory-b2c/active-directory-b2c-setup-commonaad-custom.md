---
title: Přidání poskytovatele identity více klientů Azure AD pomocí vlastních zásad – Azure Active Directory B2C | Microsoft Docs
description: Přidání poskytovatele identity více klientů Azure AD pomocí vlastních zásad – Azure Active Directory B2C
services: active-directory-b2c
documentationcenter: ''
author: parakhj
manager: alexsi
editor: parakhj
ms.assetid: 33c64001-5261-4ed9-8f46-b09839165250
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 04/14/2018
ms.author: parakhj
ms.openlocfilehash: d5e5ab1262a9d33fcf34cce91113f39c8c8936f4
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/04/2018
---
# <a name="azure-active-directory-b2c-allow-users-to-sign-in-to-a-multi-tenant-azure-ad-identity-provider-using-custom-policies"></a>Azure Active Directory B2C: Umožňují uživatelům přihlásit se k poskytovateli identity více klientů Azure AD pomocí vlastních zásad

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Tento článek ukazuje, jak povolit přihlášení pro uživatele, kteří používají víceklientské koncový bod pro Azure Active Directory (Azure AD) prostřednictvím [vlastní zásady](active-directory-b2c-overview-custom.md). Uživatelé mohou od víc klientů Azure AD pro přihlášení do Azure AD B2C bez konfigurace technické zprostředkovatele pro každého klienta. Ale hosta členy v některém z těchto klientů **nikoli** moct přihlásit. Pro tento, budete muset [individuálně konfigurovat každý klient](active-directory-b2c-setup-aad-custom.md).

>[!NOTE]
> Používáme "contoso.com" pro organizační klienta Azure AD a "fabrikamb2c.onmicrosoft.com" jako klienta Azure AD B2C v následujících pokynech.

## <a name="prerequisites"></a>Požadavky

Proveďte kroky v [Začínáme s vlastními zásadami](active-directory-b2c-get-started-custom.md) článku.

K těmto krokům patří:
     
1. Vytvoření Azure Active Directory B2C klienta (Azure AD B2C).
1. Vytvoření aplikace Azure AD B2C.    
1. Probíhá registrace dvě aplikace modul zásad.  
1. Nastavení klíče. 
1. Nastavení úvodní pack.

## <a name="step-1-create-a-multi-tenant-azure-ad-app"></a>Krok 1. Vytvoření aplikace pro více klientů Azure AD

Povolit přihlášení pro uživatele, kteří používají víceklientské koncového bodu Azure AD, musíte mít aplikaci víceklientské zaregistrován v jednom z vašich klientů Azure AD. V tomto článku jsme vám ukáže, jak vytvořit aplikaci víceklientské služby Azure AD ve vašem klientovi Azure AD B2C. Potom povolte přihlášení pro uživatele pomocí tohoto víceklientské aplikace Azure AD.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
1. Na horním panelu vyberte svůj účet. Z **Directory** vyberte klienta Azure AD B2C zaregistrovat aplikaci Azure AD (fabrikamb2c.onmicrosoft.com).
1. Vyberte **další služby** v levém podokně a vyhledejte "Aplikace registrace."
1. Vyberte **Registrace nové aplikace**.
1. Zadejte název pro vaši aplikaci (například `Azure AD B2C App`).
1. Jako typ aplikace vyberte položku **Webová aplikace / webové rozhraní API**.
1. Pro **přihlašovací adresa URL**, zadejte následující adresu URL, kde `yourtenant` je nahrazena název vašeho klienta Azure AD B2C (`fabrikamb2c.onmicrosoft.com`):

    >[!NOTE]
    >Hodnota pro "yourtenant" musí být psaný malými písmeny v **přihlašovací adresa URL**.

    ```
    https://login.microsoftonline.com/te/yourtenant.onmicrosoft.com/oauth2/authresp
    ```

1. Uložit ID aplikace.
1. Vyberte nově vytvořenou aplikaci.
1. V části **nastavení** vyberte **vlastnosti**.
1. Nastavit **nevyužívá dělené tabulky více** k **Ano**.
1. V části **nastavení** vyberte **klíče**.
1. Vytvořte nový klíč a uložte jej. Budete ho používat v krocích v další části.

## <a name="step-2-add-the-azure-ad-key-to-azure-ad-b2c"></a>Krok 2. Přidat klíč Azure AD do Azure AD B2C

Je třeba zaregistrovat klíč aplikace v Azure AD B2C nastavení. Použijte následující postup:

1. Přejděte do nabídky nastavení pro Azure AD B2C
1. Klikněte na **Identity rozhraní Framework** > **zásad klíče**.
1. Vyberte **+ přidat**.
1. Vyberte nebo zadejte tyto možnosti:
   * Vyberte **ruční**.
   * Pro **název**, zvolte název, který odpovídá název vašeho klienta Azure AD (například `AADAppSecret`).  Předpona `B2C_1A_` se automaticky přidá k názvu klíče.
   * Vložte klíč vaší aplikace v **tajný klíč** pole.
   * Vyberte **podpis**.
1. Vyberte **Vytvořit**.
1. Potvrďte, že jste vytvořili klíč `B2C_1A_AADAppSecret`.

## <a name="step-3-add-a-claims-provider-in-your-base-policy"></a>Krok 3. Přidání poskytovatele deklarací identity v základní zásady

Pokud chcete, aby uživatelé přihlásit pomocí služby Azure AD, budete muset definovat Azure AD jako zprostředkovatele deklarací identity. Jinými slovy budete muset zadat koncový bod, který Azure AD B2C bude komunikovat se. Koncový bod poskytne sadu deklarací identity, které používají Azure AD B2C k ověření, že byl ověřen konkrétního uživatele. 

Azure AD jako zprostředkovatele deklarací identity můžete definovat přidáním Azure AD `<ClaimsProvider>` uzlu v souboru rozšíření zásad:

1. Otevřete soubor rozšíření (TrustFrameworkExtensions.xml) z pracovního adresáře.
1. Najít `<ClaimsProviders>` části. Pokud neexistuje, přidejte ji do kořenového uzlu.
1. Přidejte nový `<ClaimsProvider>` uzlu následujícím způsobem:

```XML
<ClaimsProvider>
  <Domain>commonaad</Domain>
  <DisplayName>Common AAD</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="Common-AAD">
      <DisplayName>Multi-Tenant AAD</DisplayName>
      <Protocol Name="OpenIdConnect" />
      <Metadata>
        <!-- Update the Client ID below to the Application ID -->
        <Item Key="client_id">00000000-0000-0000-0000-000000000000</Item>
        <Item Key="UsePolicyInRedirectUri">0</Item>
        <Item Key="METADATA">https://login.microsoftonline.com/common/.well-known/openid-configuration</Item>
        <Item Key="response_types">code</Item>
        <Item Key="scope">openid</Item>
        <Item Key="response_mode">form_post</Item>
        <Item Key="HttpBinding">POST</Item>
        <Item Key="DiscoverMetadataByTokenIssuer">true</Item>
        
        <!-- The key below allows you to specify each of the Azure AD tenants that can be used to sign in. Update the GUIDs below for each tenant. -->
        <Item Key="ValidTokenIssuerPrefixes">https://sts.windows.net/00000000-0000-0000-0000-000000000000,https://sts.windows.net/11111111-1111-1111-1111-111111111111</Item>

        <!-- The commented key below specifies that users from any tenant can sign-in. Uncomment if you would like anyone with an Azure AD account to be able to sign in. -->
        <!-- <Item Key="ValidTokenIssuerPrefixes">https://sts.windows.net/</Item> -->

      </Metadata>
      <CryptographicKeys>
      <!-- Make sure to update the reference ID of the client secret below you just created (B2C_1A_AADAppSecret) -->
        <Key Id="client_secret" StorageReferenceId="B2C_1A_AADAppSecret" />
      </CryptographicKeys>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
        <OutputClaim ClaimTypeReferenceId="identityProvider" PartnerClaimType="iss" />
        <OutputClaim ClaimTypeReferenceId="socialIdpUserId" PartnerClaimType="sub" />
        <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
        <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name" />
        <OutputClaim ClaimTypeReferenceId="surName" PartnerClaimType="family_name" />
        <OutputClaim ClaimTypeReferenceId="email" />
      </OutputClaims>
      <OutputClaimsTransformations>
        <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName" />
        <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName" />
        <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId" />
        <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromAlternativeSecurityId" />
      </OutputClaimsTransformations>
      <UseTechnicalProfileForSessionManagement ReferenceId="SM-SocialLogin" />
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

1. V části `<ClaimsProvider>` uzlu, aktualizujte hodnotu pro `<Domain>` k jedinečná hodnota, které je možné ho odlišuje od jiných zprostředkovatelů identity.
1. V části `<TechnicalProfile>` uzlu, aktualizujte hodnotu pro `<DisplayName>`. Tato hodnota se zobrazí na tlačítko přihlásit na obrazovce přihlášení.
1. Aktualizujte hodnotu pro `<Description>`.
1. Nastavit `<Item Key="client_id">` ID aplikace z registrace mulity klienta aplikace Azure AD.

### <a name="step-31-restrict-access-to-a-specific-list-of-azure-ad-tenants"></a>Krok 3.1 omezení přístupu ke konkrétní seznam klientů Azure AD

> [!NOTE]
> Pomocí `https://sts.windows.net` hodnotu **ValidTokenIssuerPrefixes** bude povolit všechny uživatele Azure AD pro přihlášení do aplikace.

Budete muset aktualizovat seznam vystavitelů platný token a omezení přístupu ke konkrétní seznam klientů Azure AD uživatelům můžete přihlásit. Pokud chcete získat hodnoty, budete muset podívejte se na metadata pro každou z konkrétní klienty Azure AD, které chcete mít uživatelé přihlásit z. Formát dat vypadat takhle: `https://login.windows.net/yourAzureADtenant/.well-known/openid-configuration`, kde `yourAzureADtenant` je název klienta Azure AD (contoso.com nebo žádným jiným klientem Azure AD).
1. Otevřete prohlížeč a přejděte na adresu URL metadat.
1. V prohlížeči vyhledejte objekt 'vystavitele' a zkopírujte jeho hodnotu. Měl by vypadat třeba takto: `https://sts.windows.net/{tenantId}/`.
1. Vložte hodnotu `ValidTokenIssuerPrefixes` klíč. Můžete přidat více, oddělte je čárkou. Příkladem je označeno jako komentář v ukázce výše XML.

## <a name="step-4-register-the-azure-ad-account-claims-provider"></a>Krok 4. Zaregistrujte poskytovatele deklarací identity účtu služby Azure AD

### <a name="step-41-make-a-copy-of-the-user-journey"></a>Krok 4.1 vytvořit kopii cesty uživatele

Teď je potřeba přidat poskytovatele identit Azure AD na jednu z vaší uživatelské cesty. V tomto okamžiku byla nastavena zprostředkovatele identity, ale není k dispozici v žádném z obrazovky registrace-množství nebo přihlášení.

Chcete-li k dispozici, jsme se duplicitní existující uživatele cesty šablony vytvořit a upravit ho tak, aby je také poskytovatele identit Azure AD:

1. Otevřete soubor základní zásad (například TrustFrameworkBase.xml).
1. Najít `<UserJourneys>` elementu a zkopírujte celou `<UserJourney>` uzlu, který zahrnuje `Id="SignUpOrSignIn"`.
1. Otevřete soubor rozšíření (například TrustFrameworkExtensions.xml) a najděte `<UserJourneys>` elementu. Pokud element neexistuje, přidejte jedno.
1. Vložte celý `<UserJourney>` uzlu, který jste zkopírovali jako podřízenou `<UserJourneys>` elementu.
1. Přejmenujte ID nové cesty uživatele (například přejmenování jako `SignUpOrSignUsingAzureAD`). 

### <a name="step-42-display-the-button"></a>Zobrazení krok 4.2 "button"

`<ClaimsProviderSelection>` Element je obdobou tlačítko zprostředkovatele identity na obrazovce registrace-množství nebo přihlášení. Pokud přidáte `<ClaimsProviderSelection>` element pro Azure AD, nové tlačítko se zobrazí při pojmenováváme uživatele na stránce. Chcete-li přidat tento element:

1. Najít `<OrchestrationStep>` uzlu, který zahrnuje `Order="1"` v cesty uživatele, který jste vytvořili.
1. Přidejte následující:

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="AzureADExchange" />
    ```

1. Nastavit `TargetClaimsExchangeId` na odpovídající hodnotu. Doporučujeme následující stejné konvence jako ostatní:  *\[ClaimProviderName\]Exchange*.

### <a name="step-43-link-the-button-to-an-action"></a>Krok 4.3 odkaz na tlačítko akce

Nyní když máte tlačítka na místě, budete muset propojit akce. Akce, v takovém případě je pro Azure AD B2C ke komunikaci s Azure AD přijmout token. Odkaz na tlačítko akce pomocí propojení technické profil pro poskytovatele deklarací identity služby Azure AD:

1. Najít `<OrchestrationStep>` , který obsahuje `Order="2"` v `<UserJourney>` uzlu.
1. Přidejte následující:

    ```XML
    <ClaimsExchange Id="AzureADExchange" TechnicalProfileReferenceId="Common-AAD" />
    ```

1. Aktualizace `Id` na stejnou hodnotu jako `TargetClaimsExchangeId` v předchozí části.
1. Aktualizace `TechnicalProfileReferenceId` na ID technické profil vytvoříte starší (běžné AAD).

## <a name="step-5-create-a-new-rp-policy"></a>Krok 5: Vytvořte novou zásadu RP

Teď je potřeba aktualizovat soubor předávající stranu, který zahájí cesty uživatele, který jste právě vytvořili:
 
1. Vytvořte kopii SignUpOrSignIn.xml v pracovní adresář a přejmenujte ho (například přejmenujte jej na SignUpOrSignInWithAAD.xml).  
1. Otevřete nový soubor a aktualizace `PolicyId` atribut pro `<TrustFrameworkPolicy>` s jedinečnou hodnotu (například SignUpOrSignInWithAAD). To bude název vaší zásady (například B2C\_1A\_SignUpOrSignInWithAAD). 
1. Změnit `ReferenceId` atribut `<DefaultUserJourney>` tak, aby odpovídaly ID nové cesty uživatele, který jste vytvořili (SignUpOrSignUsingAzureAD). 
1. Uložte změny a soubor odešlete. 

## <a name="step-6-upload-the-policy-to-your-tenant"></a>Krok 6: Nahrajte zásady klienta

1. V [portál Azure](https://portal.azure.com), přepnout [kontextu klienta služby Azure AD B2C](active-directory-b2c-navigate-to-b2c-context.md)a potom vyberte **Azure AD B2C**.
1. Vyberte **Identity rozhraní Framework**.
1. Vyberte **všechny zásady**.
1. Vyberte **nahrát zásady**.
1. Vyberte **přepsat zásady, pokud existuje** zaškrtávací políčko.
1. Nahrát `TrustFrameworkExtensions.xml` soubor a soubor RP (například `SignUpOrSignInWithAAD.xml`) a ujistěte se, že projít ověřením.

## <a name="step-7-test-the-custom-policy-by-using-run-now"></a>Krok 7: Testování spustit pomocí vlastních zásad

1. Vyberte **nastavení Azure AD B2C**a potom vyberte **Identity rozhraní Framework**.
    > [!NOTE]
    > Spustit nyní vyžaduje alespoň jedné aplikace do být preregistered u klienta. Další postup registrace aplikace najdete v tématu Azure AD B2C [Začínáme](active-directory-b2c-get-started.md) článek nebo [registrace aplikace](active-directory-b2c-app-registration.md) článku.

1. Otevřete předávající stranu vlastních zásad, které jste odeslali (*B2C\_1A\_SignUpOrSignInWithAAD*) a potom vyberte **spustit nyní**.
1. Nyní byste měli být moct přihlásit pomocí účtu Azure AD.

## <a name="optional-step-8-register-the-azure-ad-account-claims-provider-to-the-profile-edit-user-journey"></a>(Volitelné) Krok 8: Registrace zprostředkovatele deklarací identity účtu služby Azure AD k cesty úpravy profilu uživatele

Můžete také přidat zprostředkovatele identity účtu služby Azure AD, který se vaše `ProfileEdit` cesty uživatele. Chcete-li uživatel cesty k dispozici, opakujte kroky 4 až 6. Tentokrát vyberte `<UserJourney>` uzlu, který obsahuje `Id="ProfileEdit"`. Uložit, odeslání a testování vaší zásady.

## <a name="troubleshooting"></a>Řešení potíží

K diagnostikování problémů, přečtěte si informace o [řešení potíží s](active-directory-b2c-troubleshoot-custom.md).

## <a name="next-steps"></a>Další postup

Poskytnutí zpětné vazby k [ AADB2CPreview@microsoft.com ](mailto:AADB2CPreview@microsoft.com).
