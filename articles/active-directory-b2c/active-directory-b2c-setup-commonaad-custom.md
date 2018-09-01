---
title: Přidat zprostředkovatele identity více tenanty Azure AD pomocí vlastních zásad v Azure Active Directory B2C | Dokumentace Microsoftu
description: Přidáte zprostředkovatele identity více tenanty Azure AD pomocí vlastních zásad – Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/14/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 68eab85c7f67ad3af18c6066c29e1250e1be3d23
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/31/2018
ms.locfileid: "43344402"
---
# <a name="azure-active-directory-b2c-allow-users-to-sign-in-to-a-multi-tenant-azure-ad-identity-provider-using-custom-policies"></a>Azure Active Directory B2C: Povolit uživatelům umožní přihlásit k poskytovateli identity více tenantů Azure AD pomocí vlastních zásad

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

V tomto článku se dozvíte, jak povolit přihlášení pro uživatele, kteří používají více tenantů koncový bod pro službu Azure Active Directory (Azure AD) prostřednictvím [vlastní zásady](active-directory-b2c-overview-custom.md). To umožňuje uživatelům z více tenantů Azure AD pro přihlášení do Azure AD B2C bez konfigurace technické zprostředkovatele pro jednotlivé tenanty. Ale hosta členy v některém z těchto klientů **nebudou** moct přihlásit. K tomu budete muset [konfigurovat jednotlivě každý tenant](active-directory-b2c-setup-aad-custom.md).

>[!NOTE]
> Používáme "contoso.com" pro organizační tenanta Azure AD a "fabrikamb2c.onmicrosoft.com" jako tenant Azure AD B2C v následujících pokynech.

## <a name="prerequisites"></a>Požadavky

Proveďte kroky v [Začínáme s vlastními zásadami](active-directory-b2c-get-started-custom.md) článku.

Tyto kroky zahrnují:
     
1. Vytváří se službou Azure Active Directory B2C (Azure AD B2C) tenanta.
1. Vytvoření aplikace Azure AD B2C.    
1. Registruje se dvěma aplikacemi modul zásad.  
1. Nastavení klíče. 
1. Nastavení úvodní balíček.

## <a name="step-1-create-a-multi-tenant-azure-ad-app"></a>Krok 1. Vytvoření aplikace s více tenanty Azure AD

Pokud chcete povolit přihlášení pro uživatele, kteří používají více tenantů koncového bodu Azure AD, musíte mít aplikaci s více tenanty zaregistrovaný v jednom z vašich tenantů Azure AD. V tomto článku vám ukážeme, jak vytvořit aplikaci s více tenanty Azure AD ve vašem tenantovi Azure AD B2C. Povolte přihlášení pro uživatele pomocí tohoto víceklientské aplikace Azure AD.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
1. Na horním panelu vyberte svůj účet. Z **Directory** , zvolte tenanta Azure AD B2C k registraci aplikace Azure AD (fabrikamb2c.onmicrosoft.com).
1. Vyberte **další služby** v levém podokně a vyhledejte "Registrace aplikací."
1. Vyberte **Registrace nové aplikace**.
1. Zadejte název pro vaši aplikaci (například `Azure AD B2C App`).
1. Jako typ aplikace vyberte položku **Webová aplikace / webové rozhraní API**.
1. Pro **přihlašovací adresa URL**, zadejte následující adresu URL, kde `yourtenant` se nahradí názvem vašeho tenanta Azure AD B2C (`fabrikamb2c.onmicrosoft.com`):

    >[!NOTE]
    >Hodnota pro "yourtenant" musí být malými písmeny v **přihlašovací adresa URL**.

    ```
    https://yourtenant.b2clogin.com/te/yourtenant.onmicrosoft.com/oauth2/authresp
    ```

1. Uložení ID aplikace.
1. Vyberte nově vytvořenou aplikaci.
1. V části **nastavení** okně vyberte **vlastnosti**.
1. Nastavte **víceklientských** k **Ano**.
1. V části **nastavení** okně vyberte **klíče**.
1. Vytvořte nový klíč a uložte ho. Použijete ho v krocích v další části.

## <a name="step-2-add-the-azure-ad-key-to-azure-ad-b2c"></a>Krok 2. Přidat klíč služby Azure AD do Azure AD B2C

Budete muset zaregistrovat klíč aplikace v nastavení Azure AD B2C. Použijte následující postup:

1. Přejděte do nabídky nastavení pro Azure AD B2C
1. Klikněte na **architekturu rozhraní identit** > **klíče zásad**.
1. Vyberte **+ přidat**.
1. Vyberte nebo zadejte tyto možnosti:
   * Vyberte **ruční**.
   * Pro **název**, zvolte název, který odpovídá název tenanta Azure AD (například `AADAppSecret`).  Předpona, která `B2C_1A_` je automaticky přidán do názvu klíče.
   * Vložte klíč aplikace v **tajný kód** pole.
   * Vyberte **podpis**.
1. Vyberte **Vytvořit**.
1. Potvrďte, že jste vytvořili klíč `B2C_1A_AADAppSecret`.

## <a name="step-3-add-a-claims-provider-in-your-base-policy"></a>Krok 3. Přidat zprostředkovatele deklarací identity v základních zásadách

Pokud chcete uživatelům umožní přihlásit pomocí Azure AD, musíte definovat Azure AD jako poskytovatele deklarací identity. Jinými slovy budete muset zadat koncový bod, který Azure AD B2C bude komunikovat s. Koncový bod poskytne sadu deklarací identity, které používají Azure AD B2C k ověření, že se ověřil konkrétního uživatele. 

Azure AD jako poskytovatele deklarací identity můžete definovat tak, že přidáte Azure AD, aby `<ClaimsProvider>` uzel v souboru rozšíření zásady:

1. Otevřete soubor rozšíření (TrustFrameworkExtensions.xml) z pracovního adresáře.
1. Najít `<ClaimsProviders>` oddílu. Pokud neexistuje, přidejte ho do kořenového uzlu.
1. Přidat nový `<ClaimsProvider>` uzel následujícím způsobem:

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

1. V části `<ClaimsProvider>` uzlu, aktualizujte hodnotu `<Domain>` na jedinečnou hodnotu, která je možné, aby se odlišil od jiných zprostředkovatelů identity.
1. V části `<TechnicalProfile>` uzlu, aktualizujte hodnotu pro `<DisplayName>`. Tato hodnota se zobrazí na tlačítko přihlásit na obrazovce přihlášení.
1. Aktualizujte hodnotu pro `<Description>`.
1. Nastavte `<Item Key="client_id">` do ID aplikace z registrace aplikace mulity tenanta služby Azure AD.

### <a name="step-31-restrict-access-to-a-specific-list-of-azure-ad-tenants"></a>Krok 3.1 omezení přístupu ke konkrétní seznam tenantů Azure AD

> [!NOTE]
> Pomocí `https://sts.windows.net` hodnotu **ValidTokenIssuerPrefixes** všechny služby Azure AD uživatelům umožní přihlášení do vaší aplikace.

Budete muset aktualizovat seznam platní Vystavitelé tokenů a omezit přístup na konkrétní seznam tenantů Azure AD, uživatelé můžou přihlásit. K získání hodnot, budete muset podívejte se na metadata pro každou z konkrétních klientů Azure AD, která byste chtěli nechat uživatele přihlásit z. Formát dat vypadá takto: `https://login.windows.net/yourAzureADtenant/.well-known/openid-configuration`, kde `yourAzureADtenant` je název tenanta Azure AD (contoso.com nebo žádným jiným tenantem Azure AD).
1. Otevřete prohlížeč a přejděte na adresu URL metadat.
1. V prohlížeči vyhledejte "vystavitele" objekt a zkopírujte jeho hodnotu. Měl by vypadat nějak takto: `https://sts.windows.net/{tenantId}/`.
1. Vložte hodnotu `ValidTokenIssuerPrefixes` klíč. Můžete přidat více, oddělte je čárkou. Příkladem je zakomentovaný v ukázce výše XML.

## <a name="step-4-register-the-azure-ad-account-claims-provider"></a>Krok 4. Registrace poskytovatele deklarací identity účtu služby Azure AD

### <a name="step-41-make-a-copy-of-the-user-journey"></a>Krok 4.1 vytvořte kopii cesty uživatele

Teď musíte přidat zprostředkovatele identity Azure AD do jednoho z vaší cesty uživatele. V tomto okamžiku je nastavený zprostředkovatele identity, ale není k dispozici v některém z obrazovky přihlášení-registrace/přihlášení.

Chcete-li k dispozici, jsme se vytvořit duplikát cesty existující uživatele šablony a upravte ho tak, aby má také zprostředkovatele identity Azure AD:

1. Otevřete soubor základní zásady (například TrustFrameworkBase.xml).
1. Najít `<UserJourneys>` elementu a zkopírujte celý `<UserJourney>` uzel, který zahrnuje `Id="SignUpOrSignIn"`.
1. Otevřete soubor rozšíření (například TrustFrameworkExtensions.xml) a vyhledejte `<UserJourneys>` elementu. Pokud element neexistuje, přidejte jeden.
1. Vložit celé `<UserJourney>` uzel, který jste zkopírovali jako podřízený objekt `<UserJourneys>` elementu.
1. Přejmenovat ID nové cesty uživatele (například přejmenovat jako `SignUpOrSignUsingAzureAD`). 

### <a name="step-42-display-the-button"></a>Krok 4.2 zobrazení "button"

`<ClaimsProviderSelection>` Element je obdobou k tlačítku na obrazovce přihlášení-registrace/přihlášení zprostředkovatele identity. Pokud chcete přidat `<ClaimsProviderSelection>` – element pro Azure AD, nové tlačítko se zobrazí při uživatel umístil na stránce. Chcete-li přidat tento element:

1. Najít `<OrchestrationStep>` uzel, který zahrnuje `Order="1"` v cestě uživatele, který jste vytvořili.
1. Přidejte následující:

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="AzureADExchange" />
    ```

1. Nastavte `TargetClaimsExchangeId` na odpovídající hodnotu. Doporučujeme stejnou konvencí jako ostatní:  *\[ClaimProviderName\]Exchange*.

### <a name="step-43-link-the-button-to-an-action"></a>Krok 4.3 odkaz na tlačítko na akci

Teď, když máte tlačítko na místě, budete potřebovat odkázat na akci. Akce v tomto případě je pro Azure AD B2C ke komunikaci s Azure AD za účelem získání tokenu. Tlačítko odkazu na akci propojením technický profil pro vašeho poskytovatele deklarací identity Azure AD:

1. Najít `<OrchestrationStep>` , který obsahuje `Order="2"` v `<UserJourney>` uzlu.
1. Přidejte následující:

    ```XML
    <ClaimsExchange Id="AzureADExchange" TechnicalProfileReferenceId="Common-AAD" />
    ```

1. Aktualizace `Id` na stejnou hodnotu jako `TargetClaimsExchangeId` v předchozí části.
1. Aktualizace `TechnicalProfileReferenceId` ID technického profilu vytvořeného starší (běžné AAD).

## <a name="step-5-create-a-new-rp-policy"></a>Krok 5: Vytvořte novou zásadu RP

Teď je potřeba aktualizovat předávající stranu soubor, který opraví, zahájí se cesty uživatele, který jste právě vytvořili:
 
1. Vytvořte kopii SignUpOrSignIn.xml ve svém pracovním adresáři a přejmenujte jej (například, přejmenujte ho na SignUpOrSignInWithAAD.xml).  
1. Otevřete nový soubor a aktualizace `PolicyId` atributu `<TrustFrameworkPolicy>` s jedinečnou hodnotu (například SignUpOrSignInWithAAD). To bude název zásady (například B2C\_1A\_SignUpOrSignInWithAAD). 
1. Upravit `ReferenceId` atribut `<DefaultUserJourney>` tak, aby odpovídaly ID nové cesty uživatele, který jste vytvořili (SignUpOrSignUsingAzureAD). 
1. Uložte změny a nahrajte soubor. 

## <a name="step-6-upload-the-policy-to-your-tenant"></a>Krok 6: Nahrajte zásady pro vašeho tenanta

1. V [webu Azure portal](https://portal.azure.com), přepněte [kontextu vašeho tenanta Azure AD B2C](active-directory-b2c-navigate-to-b2c-context.md)a pak vyberte **Azure AD B2C**.
1. Vyberte **architekturu rozhraní identit**.
1. Vyberte **všechny zásady**.
1. Vyberte **nahrát zásady**.
1. Vyberte **přepsat zásady, pokud existuje** zaškrtávací políčko.
1. Nahrát `TrustFrameworkExtensions.xml` soubor a soubor RP (třeba `SignUpOrSignInWithAAD.xml`) a ujistěte se bude úspěšné ověření.

## <a name="step-7-test-the-custom-policy-by-using-run-now"></a>Krok 7: Test spustit pomocí vlastních zásad

1. Vyberte **nastavení Azure AD B2C**a pak vyberte **architekturu rozhraní identit**.
    > [!NOTE]
    > Spustit nyní vyžaduje aspoň jednu aplikaci do být registrované u klienta. Informace o postupu registrace aplikací, najdete v tématu Azure AD B2C [Začínáme](active-directory-b2c-get-started.md) článku nebo [registrace aplikace](active-directory-b2c-app-registration.md) článku.

1. Otevřete předávající stranu vlastní zásady, které jste nahráli (*B2C\_1A\_SignUpOrSignInWithAAD*) a pak vyberte **spustit nyní**.
1. Teď by měl být schopni se přihlásit pomocí účtu Azure AD.

## <a name="optional-step-8-register-the-azure-ad-account-claims-provider-to-the-profile-edit-user-journey"></a>(Volitelné) Krok 8: Registrace zprostředkovatele deklarací identity účtu služby Azure AD k úpravě profilu cesty uživatele

Můžete také přidat zprostředkovatele identity účtu služby Azure AD pro vaši `ProfileEdit` cesty uživatele. Chcete-li k dispozici cesty uživatele, opakujte kroky 4 až 6. Tentokrát vyberte `<UserJourney>` uzel, který obsahuje `Id="ProfileEdit"`. Uložit nahrát a testování vaší zásady.

## <a name="troubleshooting"></a>Řešení potíží

K diagnostice problémů, přečtěte si informace o [řešení potíží s](active-directory-b2c-troubleshoot-custom.md).

## <a name="next-steps"></a>Další postup

Poskytnout zpětnou vazbu k [ AADB2CPreview@microsoft.com ](mailto:AADB2CPreview@microsoft.com).
