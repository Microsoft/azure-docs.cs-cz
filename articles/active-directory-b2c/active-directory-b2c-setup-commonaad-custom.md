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
ms.openlocfilehash: e751272222c493f3061eb15b8106f1c13eec960d
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="azure-active-directory-b2c-allow-users-to-sign-into-a-multi-tenant-azure-ad-identity-provider-using-custom-policies"></a>Azure Active Directory B2C: Umožňují uživatelům přihlásit do zprostředkovatelů identity více klientů Azure AD pomocí vlastních zásad

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Tento článek ukazuje, jak povolit přihlášení pro uživatele, kteří používají společný koncový bod pro Azure Active Directory (Azure AD) prostřednictvím [vlastní zásady](active-directory-b2c-overview-custom.md).

>[!NOTE]
> Používáme "contoso.com" pro organizační klienta Azure AD a "fabrikamb2c.onmicrosoft.com" jako klienta Azure AD B2C v následujících pokynech.

## <a name="prerequisites"></a>Požadavky

Proveďte kroky v [Začínáme s vlastními zásadami](active-directory-b2c-get-started-custom.md) článku.

K těmto krokům patří:

1. Vytvoření Azure Active Directory B2C klienta (Azure AD B2C).
2. Vytvoření aplikace Azure AD B2C.
3. Probíhá registrace dvě aplikace modul zásad.
4. Nastavení klíče.
5. Nastavení úvodní pack.

## <a name="create-a-multi-tenant-azure-ad-app"></a>Vytvoření aplikace pro více klientů Azure AD

Pokud chcete povolit přihlášení pro uživatele, kteří používají víceklientské koncového bodu Azure AD, je potřeba mít víceklientské aplikace, zaregistrované v žádném z vašich klientů

V tomto článku jsme vám ukáže, jak vytvořit aplikaci víceklientské služby Azure AD ve vašem klientovi Azure AD B2C.

>[!NOTE]
> Pokud chcete uživatelům Azure AD **a uživatelé s účty Microsoft** se přihlásit, přeskočte tuto část a místo toho zaregistrovat aplikaci v [portál pro vývojáře společnosti Microsoft](https://apps.dev.microsoft.com).

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
1. Na horním panelu vyberte svůj účet. Z **Directory** vyberte klienta Azure AD B2C, ve které chcete zaregistrovat aplikaci (fabrikamb2c.onmicrosoft.com).
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

## <a name="add-the-azure-ad-key-to-azure-ad-b2c"></a>Přidat klíč Azure AD do Azure AD B2C

Je třeba zaregistrovat klíč aplikace v Azure AD B2C nastavení. Použijte následující postup:

1. Přejděte do nabídky nastavení pro Azure AD B2C
1. Klikněte na **Identity rozhraní Framework** > **zásad klíče**.
1. Vyberte **+ přidat**.
1. Vyberte nebo zadejte tyto možnosti:
   * Vyberte **ruční**.
   * Pro **název**, zvolte název, který odpovídá název vašeho klienta Azure AD (například `AADAppSecret`).  Předpona `B2C_1A_` se automaticky přidá k názvu klíče.
   * Vložte klíč vaší aplikace v **tajný klíč** pole.
   * Vyberte **podpis**.
5. Vyberte **Vytvořit**.
6. Potvrďte, že jste vytvořili klíč `B2C_1A_ContosoAppSecret`.

## <a name="add-a-claims-provider-in-your-base-policy"></a>Přidání poskytovatele deklarací identity v základní zásady

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
            <!-- Update the client ID below to Application ID -->
            <Item Key="client_id">00000000-0000-0000-0000-000000000000</Item>
            <Item Key="UsePolicyInRedirectUri">0</Item>
            <Item Key="METADATA">https://login.microsoftonline.com/common/.well-known/openid-configuration</Item>
            <Item Key="response_types">code</Item>
            <Item Key="scope">openid</Item>
            <Item Key="response_mode">form_post</Item>
            <Item Key="HttpBinding">POST</Item>
            <Item Key="DiscoverMetadataByTokenIssuer">true</Item>
            <!-- The key below allows you to specify each of the Azure AD tenants that can be used to sign in -->
            <Item Key="ValidTokenIssuerPrefixes">https://sts.windows.net/00000000-0000-0000-0000-000000000000,https://sts.windows.net/11111111-1111-1111-1111-111111111111</Item>
          </Metadata>
          <CryptographicKeys>
            <!-- Make sure to update the reference ID of the client secret below -->
            <Key Id="client_secret" StorageReferenceId="Reference to Client Secret" />
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
1. V části `<ClaimsProvider>` uzlu, aktualizujte hodnotu pro `<DisplayName>` pro popisný název zprostředkovatele deklarací identity. Tato hodnota není právě používána.

### <a name="update-the-technical-profile"></a>Technické profil aktualizovat.

K získání tokenu z koncového bodu Azure AD, budete muset definovat protokolů, které by měl použít Azure AD B2C ke komunikaci s Azure AD. To se provádí uvnitř `<TechnicalProfile>` element `<ClaimsProvider>`.
1. Aktualizovat ID `<TechnicalProfile>` uzlu. Toto ID použité k odkazování na tento profil technické z dalších částí zásad.
1. Aktualizujte hodnotu pro `<DisplayName>`. Tato hodnota se zobrazí na tlačítko přihlásit na obrazovce přihlášení.
1. Aktualizujte hodnotu pro `<Description>`.
1. Azure AD používá protokol OpenID Connect, tak zajistěte, aby hodnota `<Protocol>` je `"OpenIdConnect"`.
1. Nastavit `<Item Key="client_id">` ID aplikace z aplikace registrace.
1. Ujistěte se, že `<Item Key="UsePolicyInRedirectUri">` je nastaven na `false`.

Je potřeba aktualizovat seznam platný token vystavitelů. Pokud chcete získat hodnoty, budete muset podívejte se na metadata pro každou z konkrétní klienty Azure AD, které chcete mít uživatelé přihlásit z. Formát dat vypadat takhle: `https://login.windows.net/yourAzureADtenant/.well-known/openid-configuration`, kde `yourAzureADtenant` je název klienta Azure AD (contoso.com).
1. Otevřete prohlížeč a přejděte na adresu URL metadat.
1. V prohlížeči vyhledejte objekt 'vystavitele' a zkopírujte jeho hodnotu. Měl by vypadat třeba takto: `https://sts.windows.net/{tenantId}/`.
1. Vložte hodnotu `ValidTokenIssuerPrefixes` klíč. Přidáním více podle oddělit čárkou. 

> [!NOTE]
> Pomocí `https://sts.windows.net` jako hodnotu předpona bude povolit všechny uživatele Azure AD pro přihlášení do aplikace.

Musíte také propojit tajný klíč aplikace Azure AD, který je zaregistrovaný v klientovi služby Azure AD B2C ke službě Azure AD `<ClaimsProvider>` informace:

* V `<CryptographicKeys>` tématu v předchozím soubor XML, aktualizujte hodnotu pro `StorageReferenceId` na odkaz na ID tajný klíč, který jste definovali (například `ContosoAppSecret`).

### <a name="upload-the-extension-file-for-verification"></a>Nahrát soubor rozšíření pro ověření

Nyní by jste nakonfigurovali zásady tak, aby Azure AD B2C umí ke komunikaci s koncovým bodem běžné pro Azure AD. Zkuste odeslat soubor rozšíření jenom k potvrzení, že nemá žádné problémy, pokud vaše zásady. Postupujte následovně:

1. Otevřete **všechny zásady** okno v klientovi služby Azure AD B2C.
1. Zkontrolujte **přepsat zásady, pokud existuje** pole.
1. Nahrát soubor rozšíření (TrustFrameworkExtensions.xml) a ujistěte se, že ověření neselže.

## <a name="register-the-azure-ad-claims-provider-to-a-user-journey"></a>Zaregistrujte poskytovatele deklarací identity Azure AD pro uživatele cesty

Teď je potřeba přidat poskytovatele identit Azure AD na jednu z vaší uživatelské cesty. V tomto okamžiku byla nastavena zprostředkovatele identity, ale není k dispozici v žádném z obrazovky registrace-množství nebo přihlášení. Chcete-li k dispozici, jsme se duplicitní existující uživatele cesty šablony vytvořit a upravit ho tak, aby je také poskytovatele identit Azure AD:

1. Otevřete soubor základní zásad (například TrustFrameworkBase.xml).
1. Najít `<UserJourneys>` elementu a zkopírujte celou `<UserJourney>` uzlu, který zahrnuje `Id="SignUpOrSignIn"`.
1. Otevřete soubor rozšíření (například TrustFrameworkExtensions.xml) a najděte `<UserJourneys>` elementu. Pokud element neexistuje, přidejte jedno.
1. Vložte celý `<UserJourney>` uzlu, který jste zkopírovali jako podřízenou `<UserJourneys>` elementu.
1. Přejmenujte ID nové cesty uživatele (například přejmenování jako `SignUpOrSignUsingAzureAD`).

### <a name="display-the-button"></a>Zobrazit "button"

`<ClaimsProviderSelection>` Element je obdobou tlačítko zprostředkovatele identity na obrazovce registrace-množství nebo přihlášení. Pokud přidáte `<ClaimsProviderSelection>` element pro Azure AD, nové tlačítko se zobrazí při pojmenováváme uživatele na stránce. Chcete-li přidat tento element:

1. Najít `<OrchestrationStep>` uzlu, který zahrnuje `Order="1"` v cesty uživatele, který jste právě vytvořili.
1. Přidejte následující:

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="AzureADExchange" />
    ```

1. Nastavit `TargetClaimsExchangeId` na odpovídající hodnotu. Doporučujeme následující stejné konvence jako ostatní:  *\[ClaimProviderName\]Exchange*.

### <a name="link-the-button-to-an-action"></a>Tlačítko propojit akce

Nyní když máte tlačítka na místě, budete muset propojit akce. Akce, v takovém případě je pro Azure AD B2C ke komunikaci s Azure AD přijmout token. Odkaz na tlačítko akce pomocí propojení technické profil pro poskytovatele deklarací identity služby Azure AD:

1. Najít `<OrchestrationStep>` , který obsahuje `Order="2"` v `<UserJourney>` uzlu.
1. Přidejte následující:

    ```XML
    <ClaimsExchange Id="AzureADExchange" TechnicalProfileReferenceId="Common-AAD" />
    ```

1. Aktualizace `Id` na stejnou hodnotu jako `TargetClaimsExchangeId` v předchozí části.
1. Aktualizace `TechnicalProfileReferenceId` na ID technické profil vytvoříte starší (běžné AAD).

### <a name="upload-the-updated-extension-file"></a>Nahrát soubor aktualizované rozšíření

Dokončení úpravy souboru rozšíření. Uložte jej. Pak nahrajte soubor a ujistěte se, že všechny ověření úspěšné.

### <a name="update-the-rp-file"></a>Aktualizovat soubor RP

Teď je potřeba aktualizovat soubor předávající stranu, který zahájí cesty uživatele, který jste právě vytvořili:

1. Vytvořte kopii SignUpOrSignIn.xml v pracovní adresář a přejmenujte ho (například přejmenujte jej na SignUpOrSignInWithAAD.xml).
1. Otevřete nový soubor a aktualizace `PolicyId` atribut pro `<TrustFrameworkPolicy>` s jedinečnou hodnotu (například SignUpOrSignInWithAAD). <br> To bude název vaší zásady (například B2C\_1A\_SignUpOrSignInWithAAD).
1. Změnit `ReferenceId` atribut `<DefaultUserJourney>` tak, aby odpovídaly ID nové cesty uživatele, který jste vytvořili (SignUpOrSignUsingAzureAD).
1. Uložte změny a soubor odešlete.

## <a name="troubleshooting"></a>Řešení potíží

Testování vlastních zásad, které jste právě nahráli otevřením její okno a kliknutím na **spustit nyní**. K diagnostikování problémů, přečtěte si informace o [řešení potíží s](active-directory-b2c-troubleshoot-custom.md).

## <a name="next-steps"></a>Další postup

Poskytnutí zpětné vazby k [ AADB2CPreview@microsoft.com ](mailto:AADB2CPreview@microsoft.com).
