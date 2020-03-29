---
title: Nastavení přihlášení pomocí účtu účtu Microsoft pomocí vlastních zásad
titleSuffix: Azure AD B2C
description: Jak pomocí vlastních zásad povolit účet Microsoft (MSA) jako zprostředkovatele identity pomocí protokolu OpenID Connect (OIDC).
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/19/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: b7d8fbddc86c0d05d7b0d4ce46cb06c5fc92a2cf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78188113"
---
# <a name="set-up-sign-in-with-a-microsoft-account-using-custom-policies-in-azure-active-directory-b2c"></a>Nastavení přihlášení pomocí účtu Microsoft pomocí vlastních zásad ve službě Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Tento článek ukazuje, jak povolit přihlášení pro uživatele z účtu Microsoft pomocí [vlastních zásad](custom-policy-overview.md) v Azure Active Directory B2C (Azure AD B2C).

## <a name="prerequisites"></a>Požadavky

- Dokončete kroky v [části Začínáme s vlastními zásadami ve službě Azure Active Directory B2C](custom-policy-get-started.md).
- Pokud ještě nemáte účet Microsoft, vytvořte [https://www.live.com/](https://www.live.com/)si ho na adrese .

## <a name="register-an-application"></a>Registrace aplikace

Pokud chcete povolit přihlášení pro uživatele s účtem Microsoft, musíte zaregistrovat aplikaci v rámci klienta Azure AD. Tenant Azure AD není stejný jako váš klient Azure AD B2C.

1. Přihlaste se k [portálu Azure](https://portal.azure.com).
1. Ujistěte se, že používáte adresář, který obsahuje vašeho klienta Azure AD výběrem filtru **directory + předplatné** v horní nabídce a výběrem adresáře, který obsahuje vašeho klienta Azure AD.
1. V levém horním rohu portálu Azure zvolte **Všechny služby** a pak **vyhledejte**a vyberte registrace aplikací .
1. Vyberte **možnost Nová registrace**.
1. Zadejte **název** aplikace. Například *MSAapp1*.
1. V části **Podporované typy účtů**vyberte Účty ve všech **organizačních adresářích a osobních účtech Microsoft (např. skype, xbox, Outlook.com).**
1. V části **Přesměrování identifikátoru URI (volitelné)** vyberte **Web** a zadejte `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` do textového pole. Nahraďte `your-tenant-name` název klienta Azure AD B2C.
1. Vybrat **registr**
1. Zaznamenejte **ID aplikace (klienta)** zobrazené na stránce Přehled aplikace. Potřebujete to při konfiguraci zprostředkovatele deklarací identity v pozdější části.
1. Vybrat **certifikáty & tajných kódů**
1. Klikněte na **Nový tajný klíč klienta.**
1. Zadejte **popis** tajného klíče, například *tajný klíč klienta aplikace MSA*, a klepněte na tlačítko **Přidat**.
1. Zaznamenejte heslo aplikace zobrazené ve sloupci **Hodnota.** Tuto hodnotu použijete v další části.

## <a name="configuring-optional-claims"></a>Konfigurace volitelných deklarací identity

Pokud chcete získat `family_name` deklarace identity `given_name` z Azure AD, můžete nakonfigurovat volitelné deklarace identity pro vaši aplikaci v unovém rozhraní portálu Azure nebo manifestu aplikace. Další informace najdete v tématu [Jak poskytovat volitelné deklarace identity do aplikace Azure AD](../active-directory/develop/active-directory-optional-claims.md).

1. Přihlaste se k [portálu Azure](https://portal.azure.com). Vyhledejte a vyberte **Azure Active Directory**.
1. V části **Spravovat** vyberte **Registrace aplikací**.
1. V seznamu vyberte aplikaci, pro kterou chcete konfigurovat volitelné deklarace identity.
1. V části **Spravovat** vyberte **konfigurace tokenu (náhled).**
1. Vyberte **Přidat volitelnou deklaraci .**
1. Vyberte typ tokenu, který chcete konfigurovat.
1. Vyberte volitelné deklarace identity, které chcete přidat.
1. Klikněte na **Přidat**.

## <a name="create-a-policy-key"></a>Vytvoření klíče zásad

Teď, když jste vytvořili aplikaci ve vašem tenantovi Azure AD, musíte uložit tajný klíč klienta této aplikace v tenantovi Azure AD B2C.

1. Přihlaste se k [portálu Azure](https://portal.azure.com/).
1. Ujistěte se, že používáte adresář, který obsahuje vašeho klienta Azure AD B2C. V horní nabídce vyberte filtr **Directory + odběr** a zvolte adresář, který obsahuje vašeho klienta.
1. V levém horním rohu portálu Azure zvolte **Všechny služby** a pak vyhledejte a vyberte **Azure AD B2C**.
1. Na stránce Přehled vyberte **rozhraní Identity Experience Framework**.
1. Vyberte **klávesy zásad** a pak vyberte **Přidat**.
1. V části `Manual` **Možnosti**zvolte .
1. Zadejte **název** klíče zásady. Například, `MSASecret`. Předpona `B2C_1A_` se automaticky přidá k názvu klíče.
1. V **poli Tajné**zadejte tajný klíč klienta, který jste zaznamenali v předchozí části.
1. V případě použití `Signature` **klíče**vyberte .
1. Klikněte na **Vytvořit**.

## <a name="add-a-claims-provider"></a>Přidání poskytovatele deklarací identity

Chcete-li uživatelům povolit přihlášení pomocí účtu Microsoft, musíte definovat účet jako poskytovatele deklarací identity, se kterým může Azure AD B2C komunikovat prostřednictvím koncového bodu. Koncový bod poskytuje sadu deklarací, které používá Azure AD B2C k ověření, že konkrétní uživatel byl ověřen.

Azure AD můžete definovat jako zprostředkovatele deklarací přidáním **claimsprovider** prvek v rozšiřující masce zásady.

1. Otevřete soubor zásad *TrustFrameworkExtensions.xml.*
1. Najít **ClaimsProviders** element. Pokud neexistuje, přidejte jej pod kořenový prvek.
1. Přidejte nového **Zprostředkovatele deklarací** identity takto:

    ```xml
    <ClaimsProvider>
      <Domain>live.com</Domain>
      <DisplayName>Microsoft Account</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="MSA-OIDC">
          <DisplayName>Microsoft Account</DisplayName>
          <Protocol Name="OpenIdConnect" />
          <Metadata>
            <Item Key="ProviderName">https://login.live.com</Item>
            <Item Key="METADATA">https://login.live.com/.well-known/openid-configuration</Item>
            <Item Key="response_types">code</Item>
            <Item Key="response_mode">form_post</Item>
            <Item Key="scope">openid profile email</Item>
            <Item Key="HttpBinding">POST</Item>
            <Item Key="UsePolicyInRedirectUri">0</Item>
            <Item Key="client_id">Your Microsoft application client ID</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_MSASecret" />
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="oid" />
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name" />
            <OutputClaim ClaimTypeReferenceId="surName" PartnerClaimType="family_name" />
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" PartnerClaimType="iss" />
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

1. Nahraďte hodnotu **client_id** *ID aplikace (klienta)* aplikace Azure AD, které jste zaznamenali dříve.
1. Uložte soubor.

Teď jste nakonfigurovali zásady tak, aby Azure AD B2C věděl, jak komunikovat s aplikací účtu Microsoft ve službě Azure AD.

### <a name="upload-the-extension-file-for-verification"></a>Nahrát soubor rozšíření pro ověření

Než budete pokračovat, nahrajte upravené zásady a ověřte, že zatím nemá žádné problémy.

1. Přejděte do svého tenanta Azure AD B2C na webu Azure Portal a vyberte **rozhraní Identity Experience Framework**.
1. Na stránce **Vlastní zásady** vyberte **Nahrát vlastní zásady**.
1. Povolte **Přepište zásadu, pokud existuje**, a potom vyhledejte soubor *TrustFrameworkExtensions.xml* a vyberte jej.
1. Klikněte na **Odeslat**.

Pokud se na portálu nezobrazí žádné chyby, pokračujte další částí.

## <a name="register-the-claims-provider"></a>Registrace zprostředkovatele deklarací identity

V tomto okamžiku jste nastavili zprostředkovatele identity, ale ještě není k dispozici v žádné z přihlašovacích nebo přihlašovacích obrazovek. Chcete-li ji zpřístupnit, vytvořte duplikát existující cesty uživatele šablony a upravte ji tak, aby měla také poskytovatele identity účtu Microsoft.

1. Otevřete soubor *TrustFrameworkBase.xml* ze startovního balíčku.
1. Vyhledejte a zkopírujte celý obsah prvku `Id="SignUpOrSignIn"` **UserJourney,** který obsahuje .
1. Otevřete soubor *TrustFrameworkExtensions.xml* a vyhledejte element **UserJourneys.** Pokud prvek neexistuje, přidejte jeden.
1. Vložte celý obsah **UserJourney** prvek, který jste zkopírovali jako podřízený **UserJourneys** element.
1. Přejmenujte ID cesty uživatele. Například, `SignUpSignInMSA`.

### <a name="display-the-button"></a>Zobrazení tlačítka

**ClaimsProviderSelection** prvek je obdobou tlačítko zprostředkovatele identity na přihlašovací nebo přihlašovací obrazovce. Pokud přidáte **ClaimsProviderSelection** prvek pro účet Microsoft, nové tlačítko se zobrazí, když uživatel přistane na stránce.

1. V souboru *TrustFrameworkExtensions.xml* vyhledejte element **OrchestrationStep,** který zahrnuje `Order="1"` cestu uživatele, kterou jste vytvořili.
1. V části **ClaimsProviderSelects**přidejte následující prvek. Nastavte hodnotu **TargetClaimsExchangeId** na příslušnou hodnotu, například `MicrosoftAccountExchange`:

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="MicrosoftAccountExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>Propojení tlačítka s akcí

Nyní, když máte tlačítko na místě, musíte jej propojit s akcí. Akce, v tomto případě je pro Azure AD B2C komunikovat s účtem Microsoft pro příjem tokenu.

1. Najít **OrchestrationStep,** `Order="2"` který zahrnuje v cestě uživatele.
1. Přidejte následující **claimsexchange** prvek ujistěte se, že používáte stejnou hodnotu pro ID, které jste použili pro **TargetClaimsExchangeId**:

    ```xml
    <ClaimsExchange Id="MicrosoftAccountExchange" TechnicalProfileReferenceId="MSA-OIDC" />
    ```

    Aktualizujte hodnotu **TechnicalProfileReferenceId** tak, `Id` aby odpovídala hodnotě v prvku **TechnicalProfile** poskytovatele deklarací identity, který jste přidali dříve. Například, `MSA-OIDC`.

1. Uložte soubor *TrustFrameworkExtensions.xml* a znovu jej nahrajte k ověření.

## <a name="create-an-azure-ad-b2c-application"></a>Vytvoření aplikace Azure AD B2C

Komunikace s Azure AD B2C probíhá prostřednictvím aplikace, kterou zaregistrujete ve vašem tenantovi B2C. V této části jsou uvedeny volitelné kroky, které můžete provést a vytvořit testovací aplikaci, pokud jste tak ještě neučinili.

[!INCLUDE [active-directory-b2c-appreg-idp](../../includes/active-directory-b2c-appreg-idp.md)]

## <a name="update-and-test-the-relying-party-file"></a>Aktualizace a testování souboru předávající strany

Aktualizujte soubor předávající strany (RP), který iniciuje cestu uživatele, kterou jste vytvořili.

1. Vytvořte kopii *souboru SignUpOrSignIn.xml* ve svém pracovním adresáři a přejmenujte ji. Můžete jej například přejmenovat na *SignUpSignInMSA.xml*.
1. Otevřete nový soubor a aktualizujte hodnotu atributu **PolicyId** pro **TrustFrameworkPolicy** s jedinečnou hodnotou. Například, `SignUpSignInMSA`.
1. Aktualizujte hodnotu **PublicPolicyUri** pomocí identifikátoru URI pro zásadu. Například`http://contoso.com/B2C_1A_signup_signin_msa`
1. Aktualizujte hodnotu atributu **ReferenceId** v **DefaultUserJourney** tak, aby odpovídalo ID cesty uživatele, kterou jste vytvořili dříve (SignUpSignInMSA).
1. Uložte změny, nahrajte soubor a vyberte novou zásadu v seznamu.
1. Ujistěte se, že aplikace Azure AD B2C, kterou jste vytvořili v předchozí části (nebo dokončením předpokladů, například *webapp1* nebo *testapp1*) je vybrána v poli **Vybrat aplikaci** a potom ji otestujte klepnutím na tlačítko **Spustit nyní**.
1. Vyberte tlačítko **Účet Microsoft** a přihlaste se.

    Pokud je operace přihlášení úspěšná, budete přesměrováni na `jwt.ms` který se zobrazí dekódovaný token, podobně jako:

    ```json
    {
      "typ": "JWT",
      "alg": "RS256",
      "kid": "<key-ID>"
    }.{
      "exp": 1562365200,
      "nbf": 1562361600,
      "ver": "1.0",
      "iss": "https://your-b2c-tenant.b2clogin.com/10000000-0000-0000-0000-000000000000/v2.0/",
      "sub": "20000000-0000-0000-0000-000000000000",
      "aud": "30000000-0000-0000-0000-000000000000",
      "acr": "b2c_1a_signupsigninmsa",
      "nonce": "defaultNonce",
      "iat": 1562361600,
      "auth_time": 1562361600,
      "idp": "live.com",
      "name": "Azure User",
      "email": "azureuser@contoso.com",
      "tid": "6fc3b573-7b38-4c0c-b627-2e8684f6c575"
    }.[Signature]
    ```
