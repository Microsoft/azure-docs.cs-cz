---
title: Nastavení registrace a přihlášení pomocí Apple ID
titleSuffix: Azure AD B2C
description: Poskytněte zákazníkům registraci a přihlášení pomocí Apple ID v aplikacích pomocí Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/09/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: cc385c3a7ceb0245e3a4acbedb037b1b28bde7b3
ms.sourcegitcommit: 956dec4650e551bdede45d96507c95ecd7a01ec9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/09/2021
ms.locfileid: "102518105"
---
# <a name="set-up-sign-up-and-sign-in-with-an-apple-id--using-azure-active-directory-b2c-preview"></a>Nastavení registrace a přihlášení pomocí Apple ID pomocí Azure Active Directory B2C (Preview)

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

::: zone pivot="b2c-custom-policy"

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

::: zone-end

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="create-an-apple-id-application"></a>Vytvoření aplikace Apple ID

Pokud chcete povolit přihlášení pro uživatele s Apple ID v Azure Active Directory B2C (Azure AD B2C), musíte vytvořit aplikaci v [https://developer.apple.com](https://developer.apple.com) . Další informace najdete v tématu [přihlášení pomocí Apple](https://developer.apple.com/sign-in-with-apple/get-started/). Pokud ještě nemáte účet Apple Developer, můžete se zaregistrovat v [programu Apple Developer](https://developer.apple.com/programs/enroll/).

1. Přihlaste se k [portálu pro vývojáře Apple](https://developer.apple.com/account) pomocí svých přihlašovacích údajů k účtu.
1. V nabídce vyberte **certifikáty, id & profily** a pak vyberte **(+)**.
1. Pro **registraci nového identifikátoru** vyberte **ID aplikace** a pak vyberte **pokračovat**.
1. V případě **Vyberte typ** vyberte možnost **aplikace** a pak vyberte **pokračovat**.
1. Pro **registraci ID aplikace**:
    1. Zadejte **Popis** . 
    1. Zadejte **ID sady prostředků**, například `com.contoso.azure-ad-b2c` . 
    1. V části **Možnosti** vyberte možnost **Přihlásit se pomocí Apple** ze seznamu možností. 
    1. Poznamenejte si předponu ID aplikace (ID týmu) z tohoto kroku. Budete ho potřebovat později.
    1. Vyberte **pokračovat** a potom se **Zaregistrujte**.
1. V nabídce vyberte **certifikáty, id & profily** a pak vyberte **(+)**.
1. Pro **registraci nového identifikátoru** vyberte **ID služeb** a pak vyberte **pokračovat**.
1. Pro **registraci ID služeb**:
    1. Zadejte **Popis**. Popis se zobrazí uživateli na obrazovce pro vyjádření souhlasu.
    1. Zadejte **identifikátor**, například `com.consoto.azure-ad-b2c-service` . Identifikátor je vaše ID klienta pro tok OpenID Connect.
    1. Vyberte **pokračovat** a pak vyberte **zaregistrovat**.
1. Z **identifikátorů** vyberte identifikátor, který jste vytvořili.
1. Vyberte možnost **Přihlásit se pomocí Apple** a pak vyberte **Konfigurovat**.
    1. Vyberte **ID primární aplikace** , pro kterou chcete nakonfigurovat přihlášení pomocí Apple s.
    1. V oblasti **domény a subdomény** zadejte `your-tenant-name.b2clogin.com` . Nahraďte název-tenanta názvem vašeho tenanta.
    1. Do **návratových adres URL** zadejte `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` . Nahraďte název-tenanta názvem vašeho tenanta.
    1. Vyberte možnost **Další** a potom vyberte **Hotovo**.
    1. Po zavření automaticky otevíraného okna vyberte **pokračovat** a pak vyberte **Uložit**.

## <a name="creating-an-apple-client-secret"></a>Vytvoření tajného kódu klienta Apple

1. V nabídce portálu pro vývojáře Apple vyberte **klíče** a pak vyberte **(+)**.
1. Pro **registraci nového klíče**:
    1. Zadejte **název klíče**.
    1. Vyberte možnost **Přihlásit se pomocí Apple** a pak vyberte **Konfigurovat**.
    1. V poli **ID primární aplikace** vyberte aplikaci, kterou jste vytvořili dříve, a pak vyberte **Uložit**.
    1. Vyberte **Konfigurovat** a potom vyberte **Registrovat** a dokončete proces registrace klíče.
1. Pokud **si chcete stáhnout svůj klíč**, vyberte **Stáhnout** a Stáhněte si soubor. P8, který obsahuje váš klíč.


::: zone pivot="b2c-user-flow"

## <a name="configure-apple-as-an-identity-provider"></a>Konfigurace Apple jako zprostředkovatele identity

1. Přihlaste se k [Azure Portal](https://portal.azure.com/) jako globální správce vašeho tenanta Azure AD B2C.
1. V horní nabídce vyberte filtr **adresář + odběr** a zvolte adresář, který obsahuje vašeho tenanta Azure AD B2C.
1. V části **služby Azure** vyberte **Azure AD B2C**. Nebo pomocí vyhledávacího pole vyhledejte a vyberte **Azure AD B2C**.
1. Vyberte **Zprostředkovatelé identity** a pak vyberte **Apple (Preview)**.
1. Zadejte **název**. Například *Apple*.
1. Zadejte **Apple Developer ID (ID týmu)**.
1. Zadejte **ID služby Apple (ID klienta)**.
1. Zadejte **ID klávesy Apple**.
1. Vyberte a nahrajte **data certifikátu Apple**.
1. Vyberte **Uložit**.


> [!IMPORTANT] 
> - Přihlášení pomocí Apple vyžaduje, aby správce obnovil svůj tajný klíč klienta každých 6 měsíců. 
> - V rámci veřejné verze Preview této funkce budete muset při vypršení platnosti ručně obnovit tajný klíč klienta Apple. Na webu Apple identity Providers se zobrazí upozornění s konfigurací sociálních IDP stránky, doporučujeme ale nastavit vlastní připomenutí. 
> - Pokud potřebujete obnovit tajný klíč, otevřete Azure AD B2C v Azure Portal, přejdete na **Zprostředkovatelé identity**  >  **Apple** a vyberte **obnovit tajný kód**.

## <a name="add-the-apple-identity-provider-to-a-user-flow"></a>Přidání poskytovatele identity Apple do toku uživatele

Pokud chcete uživatelům povolit, aby se přihlásili pomocí Apple ID, musíte přidat poskytovatele identity Apple do toku uživatele. Přihlášení pomocí Apple se dá nakonfigurovat jenom na **doporučenou** verzi uživatelských toků. Přidání poskytovatele identity Apple do toku uživatele:

1. Ve vašem tenantovi Azure AD B2C vyberte **toky uživatelů**.
1. Vyberte tok uživatele, pro který chcete přidat poskytovatele identity Apple. 
1. V části **Zprostředkovatelé sociální identity** vyberte **Apple (Preview)**.
1. Vyberte **Uložit**.
1. Pokud chcete zásady testovat, vyberte **Spustit tok uživatele**.
1. V poli **aplikace** vyberte webovou aplikaci s názvem *testapp1* , kterou jste předtím zaregistrovali. Měla by se zobrazit **Adresa URL odpovědi** `https://jwt.ms` .
1. Vyberte tlačítko **tok spuštění uživatele** .
1. Na stránce registrace nebo přihlášení vyberte **Apple** a přihlaste se pomocí Apple ID.

Pokud je proces přihlášení úspěšný, je váš prohlížeč přesměrován na `https://jwt.ms` , který zobrazuje obsah tokenu vrácený Azure AD B2C.

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="signing-the-client-secret"></a>Podepisování tajného klíče klienta

Pomocí souboru. P8, který jste dříve stáhli, podepište tajný klíč klienta do tokenu JWT. Existuje mnoho knihoven, které mohou vytvořit a podepsat token JWT za vás. Použijte [funkci Azure, která pro vás vytvoří token](https://github.com/azure-ad-b2c/samples/tree/master/policies/sign-in-with-apple/azure-function) . 

1. Vytvoření [funkce Azure Functions](../azure-functions/functions-create-function-app-portal.md)
1. V části **vývojář** vyberte **kód + test**. 
1. Zkopírujte obsah souboru [Run. csx](https://github.com/azure-ad-b2c/samples/blob/master/policies/sign-in-with-apple/azure-function/run.csx) a vložte ho do editoru.
1. Vyberte **Uložit**.
1. Proveďte požadavek HTTP `POST` a zadejte následující informace:

    - **appleTeamId**: vaše ID týmu Apple Developer
    - **appleServiceId**: ID služby Apple (také ID klienta)
    - **p8key**: PEM formátovací klíč. To lze získat otevřením souboru. P8 v textovém editoru a zkopírováním všeho mezi `-----BEGIN PRIVATE KEY-----` i `-----END PRIVATE KEY-----` bez konců řádků.
 
Následující JSON je příkladem volání funkce Azure Function:

```json
{
    "appleTeamId": "ABC123DEFG",
    "appleKeyId": "URKEYID001",
    "appleServiceId": "com.yourcompany.app1",
    "p8key": "MIGTAgEAMBMGByqGSM49AgEGCCqGSM49AwEHBHkwdwIBAQQg+s07NiAcuGEu8rxsJBG7ttupF6FRe3bXdHxEipuyK82gCgYIKoZIzj0DAQehRANCAAQnR1W/KbbaihTQayXH3tuAXA8Aei7u7Ij5OdRy6clOgBeRBPy1miObKYVx3ki1msjjG2uGqRbrc1LvjLHINWRD"
}
```

Služba Azure Functions odpoví pomocí správně formátovaného a podepsaného tajného klíče klienta JWT v odpovědi, například:

```json
{
    "token": "eyJhbGciOiJFUzI1NiIsInR5cCI6IkpXVCJ9.eyJzdWIiOiJjb20ueW91cmNvbXBhbnkuYXBwMSIsIm5iZiI6MTU2MDI2OTY3NSwiZXhwIjoxNTYwMzU2MDc1LCJpc3MiOiJBQkMxMjNERUZHIiwiYXVkIjoiaHR0cHM6Ly9hcHBsZWlkLmFwcGxlLmNvbSJ9.Dt9qA9NmJ_mk6tOqbsuTmfBrQLFqc9BnSVKR6A-bf9TcTft2XmhWaVODr7Q9w1PP3QOYShFXAnNql5OdNebB4g"
}
```

## <a name="create-a-policy-key"></a>Vytvoření klíče zásad

Je potřeba uložit tajný klíč klienta, který jste předtím nahráli ve svém tenantovi Azure AD B2C.

1. Přihlaste se na [Azure Portal](https://portal.azure.com/).
1. V horní nabídce vyberte filtr **adresář + odběr** a zvolte adresář, který obsahuje vašeho tenanta Azure AD B2C.
2. V části **služby Azure** vyberte **Azure AD B2C**. Nebo pomocí vyhledávacího pole vyhledejte a vyberte **Azure AD B2C**.
1. Na stránce **Přehled** vyberte možnost **Architektura prostředí identity**.
1. Vyberte **klíče zásad** a pak vyberte **Přidat**.
1. V případě **možností** vyberte možnost **ručně**.
1. Zadejte **název** klíče zásad. Například "AppleSecret". Předpona "B2C_1A_" je automaticky přidána do názvu vašeho klíče.
1. V **tajnosti** zadejte hodnotu tokenu vráceného funkcí Azure Function (token JWT).
1. V případě **použití klíče** vyberte možnost **podpis**.
1. Vyberte **Vytvořit**.

> [!IMPORTANT] 
> - Přihlášení pomocí Apple vyžaduje, aby správce obnovil svůj tajný klíč klienta každých 6 měsíců.
> - Pokud vyprší platnost, budete muset ručně obnovit tajný klíč klienta Apple a uložit novou hodnotu do klíče zásad.
> - Doporučujeme nastavit vlastní připomenutí do 6 měsíců, aby se vygeneroval nový tajný klíč klienta. 

## <a name="configure-apple-as-an-identity-provider"></a>Konfigurace Apple jako zprostředkovatele identity

Pokud chcete uživatelům povolit, aby se přihlásili pomocí Apple ID, musíte účet definovat jako zprostředkovatele deklarací identity, se kterým Azure AD B2C můžou komunikovat prostřednictvím koncového bodu. Koncový bod poskytuje sadu deklarací, které používá Azure AD B2C k ověření, jestli je konkrétní uživatel ověřený.

Apple ID můžete definovat jako zprostředkovatele deklarací, a to tak, že ho přidáte do prvku **ClaimsProviders** v souboru rozšíření zásady.

1. Otevřete *TrustFrameworkExtensions.xml*.
2. Vyhledejte element **ClaimsProviders** . Pokud neexistuje, přidejte jej pod kořenový element.
3. Přidejte nový **ClaimsProvider** následujícím způsobem:

    ```xml
    <ClaimsProvider>
      <Domain>apple.com</Domain>
      <DisplayName>Apple</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Apple-OIDC">
          <DisplayName>Apple</DisplayName>
          <Protocol Name="OpenIdConnect" />
          <Metadata>
            <Item Key="ProviderName">apple</Item>
            <Item Key="authorization_endpoint">https://appleid.apple.com/auth/authorize</Item>
            <Item Key="AccessTokenEndpoint">https://appleid.apple.com/auth/token</Item>
            <Item Key="JWKS">https://appleid.apple.com/auth/keys</Item>
            <Item Key="issuer">https://appleid.apple.com</Item>
            <Item Key="scope">name email openid</Item>
            <Item Key="HttpBinding">POST</Item>
            <Item Key="response_types">code</Item>
            <Item Key="external_user_identity_claim_id">sub</Item>
            <Item Key="response_mode">form_post</Item>
            <Item Key="ReadBodyClaimsOnIdpRedirect">user.firstName user.lastName user.email</Item>
            <Item Key="client_id">You Apple ID</Item>
            <Item Key="UsePolicyInRedirectUri">false</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_AppleSecret"/>
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="sub" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="https://appleid.apple.com" AlwaysUseDefaultValue="true" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" AlwaysUseDefaultValue="true" />
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="user.firstName"/>
            <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="user.lastName"/>
            <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="user.email"/>
          </OutputClaims>
          <OutputClaimsTransformations>
            <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName"/>
            <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName"/>
            <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId"/>
            <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromAlternativeSecurityId"/>
          </OutputClaimsTransformations>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-SocialLogin" />
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

4. Nastavte **client_id** identifikátoru služby. Například, `com.consoto.azure-ad-b2c-service`.
5. Soubor uložte.

[!INCLUDE [active-directory-b2c-add-identity-provider-to-user-journey](../../includes/active-directory-b2c-add-identity-provider-to-user-journey.md)]


```xml
<OrchestrationStep Order="1" Type="CombinedSignInAndSignUp" ContentDefinitionReferenceId="api.signuporsignin">
  <ClaimsProviderSelections>
    ...
    <ClaimsProviderSelection TargetClaimsExchangeId="AppleExchange" />
  </ClaimsProviderSelections>
  ...
</OrchestrationStep>

<OrchestrationStep Order="2" Type="ClaimsExchange">
  ...
  <ClaimsExchanges>
    <ClaimsExchange Id="AppleExchange" TechnicalProfileReferenceId="Apple-OIDC" />
  </ClaimsExchanges>
</OrchestrationStep>
```

[!INCLUDE [active-directory-b2c-configure-relying-party-policy](../../includes/active-directory-b2c-configure-relying-party-policy-user-journey.md)]

## <a name="test-your-custom-policy"></a>Testování vlastních zásad

1. Vyberte třeba zásady předávající strany `B2C_1A_signup_signin` .
1. V případě **aplikace** vyberte webovou aplikaci, kterou jste [předtím zaregistrovali](troubleshoot-custom-policies.md#troubleshoot-the-runtime). Měla by se zobrazit **Adresa URL odpovědi** `https://jwt.ms` .
1. Vyberte tlačítko **Spustit** .
1. Na stránce registrace nebo přihlášení vyberte **Apple** a přihlaste se pomocí Apple ID.

Pokud je proces přihlášení úspěšný, je váš prohlížeč přesměrován na `https://jwt.ms` , který zobrazuje obsah tokenu vrácený Azure AD B2C.

::: zone-end
