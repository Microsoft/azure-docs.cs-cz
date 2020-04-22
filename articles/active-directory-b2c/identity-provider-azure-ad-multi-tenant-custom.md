---
title: Nastavení přihlášení pro víceklientské Azure AD podle vlastních zásad
titleSuffix: Azure AD B2C
description: Přidejte víceklientského zprostředkovatele identity Azure AD pomocí vlastních zásad ve službě Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/10/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 320723744e1366fdc73cd0593fb0ebece03367f8
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2020
ms.locfileid: "81678106"
---
# <a name="set-up-sign-in-for-multi-tenant-azure-active-directory-using-custom-policies-in-azure-active-directory-b2c"></a>Nastavení přihlášení pro víceklientský Azure Active Directory pomocí vlastních zásad ve službě Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Tento článek ukazuje, jak povolit přihlášení pro uživatele, kteří používají koncový bod s více klienty pro Azure Active Directory (Azure AD) pomocí [vlastních zásad](custom-policy-overview.md) v Azure AD B2C. To umožňuje uživatelům z více klientů Azure AD přihlásit pomocí Azure AD B2C, aniž byste museli nakonfigurovat zprostředkovatele identity pro každého klienta. Členové hosta v některém z těchto klientů se však **nebudou** moci přihlásit. Za tímto účelem je třeba [individuálně nakonfigurovat každého klienta](identity-provider-azure-ad-single-tenant-custom.md).

## <a name="prerequisites"></a>Požadavky

Dokončete kroky v [části Začínáme s vlastními zásadami ve službě Azure Active Directory B2C](custom-policy-get-started.md).

## <a name="register-an-application"></a>Registrace aplikace

Chcete-li povolit přihlášení pro uživatele z konkrétní organizace Azure AD, musíte zaregistrovat aplikaci v rámci klienta Azure AD organizace.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
1. Ujistěte se, že používáte adresář, který obsahuje vašeho klienta Azure AD organizace (například contoso.com). V horní nabídce vyberte **filtr Directory + subscription** a pak zvolte adresář, který obsahuje vašeho tenanta.
1. V levém horním rohu portálu Azure zvolte **Všechny služby** a pak **vyhledejte**a vyberte registrace aplikací .
1. Vyberte **možnost Nová registrace**.
1. Zadejte **název** aplikace. Například, `Azure AD B2C App`.
1. Vyberte **účty v libovolném organizačním adresáři** pro tuto aplikaci.
1. Pro **identifikátor URI přesměrování**přijměte hodnotu **webu**a zadejte následující `your-B2C-tenant-name` adresu URL ve všech malých písmenech, kde je nahrazen názvem klienta Azure AD B2C.

    ```
    https://your-B2C-tenant-name.b2clogin.com/your-B2C-tenant-name.onmicrosoft.com/oauth2/authresp
    ```

    Například, `https://fabrikam.b2clogin.com/fabrikam.onmicrosoft.com/oauth2/authresp`.

1. Vyberte **Zaregistrovat**. Zaznamenejte **ID aplikace (klienta)** pro pozdější krok.
1. Vyberte **certifikáty & tajných kódů**a potom vyberte **Nový tajný klíč klienta**.
1. Zadejte **popis** tajného klíče, vyberte vypršení platnosti a pak vyberte **Přidat**. Zaznamenejte **hodnotu** tajného klíče pro pozdější krok.

## <a name="configuring-optional-claims"></a>Konfigurace volitelných deklarací identity

Pokud chcete získat `family_name` deklarace identity `given_name` z Azure AD, můžete nakonfigurovat volitelné deklarace identity pro vaši aplikaci v unovém rozhraní portálu Azure nebo manifestu aplikace. Další informace najdete v tématu [Jak poskytovat volitelné deklarace identity do aplikace Azure AD](../active-directory/develop/active-directory-optional-claims.md).

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com). Vyhledejte a vyberte **Azure Active Directory**.
1. V části **Spravovat** vyberte **Registrace aplikací**.
1. V seznamu vyberte aplikaci, pro kterou chcete konfigurovat volitelné deklarace identity.
1. V části **Spravovat** vyberte **konfigurace tokenu**.
1. Vyberte **Přidat volitelnou deklaraci .**
1. Pro **typ tokenu**vyberte **ID**.
1. Vyberte volitelné deklarace `family_name` `given_name`identity, které chcete přidat, a .
1. Klikněte na tlačítko **Add** (Přidat).

## <a name="create-a-policy-key"></a>Vytvoření klíče zásad

Musíte uložit klíč aplikace, který jste vytvořili v tenantovi Azure AD B2C.

1. Ujistěte se, že používáte adresář, který obsahuje vašeho klienta Azure AD B2C. V horní nabídce vyberte **filtr Directory + subscription** a pak zvolte adresář, který obsahuje vašeho klienta Azure AD B2C.
1. V levém horním rohu portálu Azure zvolte **Všechny služby** a pak vyhledejte a vyberte **Azure AD B2C**.
1. V části **Zásady**vyberte **rozhraní Identity Experience Framework**.
1. Vyberte **klávesy zásad a** pak vyberte **Přidat**.
1. V části `Manual` **Možnosti**zvolte .
1. Zadejte **název** klíče zásady. Například, `AADAppSecret`.  Předpona `B2C_1A_` se při vytvoření automaticky přidá k názvu klíče, takže její odkaz ve formátu XML v následující části je *B2C_1A_AADAppSecret*.
1. V **poli Tajné**zadejte tajný klíč klienta, který jste nahráli dříve.
1. V případě použití `Signature` **klíče**vyberte .
1. Vyberte **Vytvořit**.

## <a name="add-a-claims-provider"></a>Přidání poskytovatele deklarací identity

Pokud chcete, aby se uživatelé přihlašovali pomocí Azure AD, musíte definovat Azure AD jako poskytovatele deklarací identity, se kterým může Azure AD B2C komunikovat prostřednictvím koncového bodu. Koncový bod poskytuje sadu deklarací, které používá Azure AD B2C k ověření, že konkrétní uživatel byl ověřen.

Azure AD můžete definovat jako poskytovatele deklarací identity přidáním Azure AD do prvku **ClaimsProvider** v souboru rozšíření zásad.

1. Otevřete soubor *TrustFrameworkExtensions.xml.*
1. Najít **ClaimsProviders** element. Pokud neexistuje, přidejte jej pod kořenový prvek.
1. Přidejte nového **Zprostředkovatele deklarací** identity takto:

    ```XML
    <ClaimsProvider>
      <Domain>commonaad</Domain>
      <DisplayName>Common AAD</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Common-AAD">
          <DisplayName>Multi-Tenant AAD</DisplayName>
          <Description>Login with your Contoso account</Description>
          <Protocol Name="OpenIdConnect"/>
          <Metadata>
            <Item Key="METADATA">https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration</Item>
            <!-- Update the Client ID below to the Application ID -->
            <Item Key="client_id">00000000-0000-0000-0000-000000000000</Item>
            <Item Key="response_types">code</Item>
            <Item Key="scope">openid profile</Item>
            <Item Key="response_mode">form_post</Item>
            <Item Key="HttpBinding">POST</Item>
            <Item Key="UsePolicyInRedirectUri">false</Item>
            <Item Key="DiscoverMetadataByTokenIssuer">true</Item>
            <!-- The key below allows you to specify each of the Azure AD tenants that can be used to sign in. Update the GUIDs below for each tenant. -->
            <Item Key="ValidTokenIssuerPrefixes">https://login.microsoftonline.com/00000000-0000-0000-0000-000000000000,https://login.microsoftonline.com/11111111-1111-1111-1111-111111111111</Item>
            <!-- The commented key below specifies that users from any tenant can sign-in. Uncomment if you would like anyone with an Azure AD account to be able to sign in. -->
            <!-- <Item Key="ValidTokenIssuerPrefixes">https://login.microsoftonline.com/</Item> -->
          </Metadata>
          <CryptographicKeys>
            <Key Id="client_secret" StorageReferenceId="B2C_1A_AADAppSecret"/>
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="issuerUserId" PartnerClaimType="oid"/>
            <OutputClaim ClaimTypeReferenceId="tenantId" PartnerClaimType="tid"/>
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name" />
            <OutputClaim ClaimTypeReferenceId="surName" PartnerClaimType="family_name" />
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication" AlwaysUseDefaultValue="true" />
            <OutputClaim ClaimTypeReferenceId="identityProvider" PartnerClaimType="iss" />
          </OutputClaims>
          <OutputClaimsTransformations>
            <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName"/>
            <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName"/>
            <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId"/>
            <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromAlternativeSecurityId"/>
          </OutputClaimsTransformations>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-SocialLogin"/>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

1. V rámci **ClaimsProvider** element, aktualizovat hodnotu **domény** na jedinečnou hodnotu, která lze odlišit od jiných poskytovatelů identity.
1. V části **Prvek TechnicalProfile** aktualizujte hodnotu pro `Contoso Employee` **DisplayName**, například . Tato hodnota se zobrazí na přihlašovacím tlačítku na přihlašovací stránce.
1. Nastavte **client_id** na ID aplikace víceklientské aplikace Azure AD, kterou jste zaregistrovali dříve.
1. V části **CryptographicKeys**aktualizujte hodnotu **StorageReferenceId** na název klíče zásady, který byl vytvořen dříve. Například, `B2C_1A_AADAppSecret`.

### <a name="restrict-access"></a>Omezení přístupu

> [!NOTE]
> Použití `https://login.microsoftonline.com/` jako hodnota pro **ValidTokenIssuerPrefixes** umožňuje všem uživatelům Azure AD přihlásit se k vaší aplikaci.

Je třeba aktualizovat seznam platných vystavitelů tokenů a omezit přístup k určitému seznamu uživatelů klienta Azure AD, kteří se můžou přihlásit.

Chcete-li získat hodnoty, podívejte se na metadata zjišťování OpenID Connect pro každého z klientů Azure AD, ze kterých chcete mít uživatele, kteří se přihlašují. Formát adresy URL metadat je `https://login.microsoftonline.com/your-tenant/v2.0/.well-known/openid-configuration`podobný `your-tenant` , kde je název klienta Azure AD. Příklad:

`https://login.microsoftonline.com/fabrikam.onmicrosoft.com/v2.0/.well-known/openid-configuration`

Proveďte tyto kroky pro každý klient Azure AD, který by se měl použít k přihlášení:

1. Otevřete prohlížeč a přejděte na adresu URL metadat OpenID Connect pro klienta. Najděte objekt **vystavittele** a zaznamenejte jeho hodnotu. Mělo by vypadat `https://login.microsoftonline.com/00000000-0000-0000-0000-000000000000/`podobně jako .
1. Zkopírujte a vložte hodnotu do klíče **ValidTokenIssuerPrefixes.** Oddělte více vystavitelů čárkou. Příklad se dvěma vydavateli `ClaimsProvider` se zobrazí v předchozí ukázce XML.

### <a name="upload-the-extension-file-for-verification"></a>Nahrát soubor rozšíření pro ověření

Teď jste nakonfigurovali zásady tak, aby Azure AD B2C ví, jak komunikovat s adresáři Azure AD. Zkuste nahrát soubor rozšíření zásad, abyste potvrdili, že zatím nemá žádné problémy.

1. Na stránce **Vlastní zásady** v tenantovi Azure AD B2C vyberte **Zásady nahrávání**.
2. Povolte **Přepište zásadu, pokud existuje**, a potom vyhledejte soubor *TrustFrameworkExtensions.xml* a vyberte jej.
3. Vyberte **Nahrát**.

## <a name="register-the-claims-provider"></a>Registrace zprostředkovatele deklarací identity

V tomto okamžiku byl nastaven zprostředkovatel identity, ale není k dispozici v žádné z přihlašovacích a přihlašovacích obrazovek. Chcete-li zpřístupnit, vytvořte duplikát existující cesty uživatele šablony a pak ji upravte tak, aby měla také zprostředkovatele identity Azure AD.

1. Otevřete soubor *TrustFrameworkBase.xml* ze startovního balíčku.
2. Vyhledejte a zkopírujte celý obsah prvku `Id="SignUpOrSignIn"` **UserJourney,** který obsahuje .
3. Otevřete soubor *TrustFrameworkExtensions.xml* a vyhledejte element **UserJourneys.** Pokud prvek neexistuje, přidejte jeden.
4. Vložte celý obsah **UserJourney** prvek, který jste zkopírovali jako podřízený **UserJourneys** element.
5. Přejmenujte ID cesty uživatele. Například, `SignUpSignInContoso`.

### <a name="display-the-button"></a>Zobrazení tlačítka

**ClaimsProviderSelection** prvek je obdobou tlačítko zprostředkovatele identity na přihlašovací nebo přihlašovací obrazovce. Pokud přidáte **ClaimsProviderSelection** prvek pro Azure AD, nové tlačítko se zobrazí, když uživatel přistane na stránce.

1. Najděte element **OrchestrationStep,** který zahrnuje `Order="1"` cestu uživatele, kterou jste vytvořili v *souboru TrustFrameworkExtensions.xml*.
1. V části **ClaimsProviderSelects**přidejte následující prvek. Nastavte hodnotu **TargetClaimsExchangeId** na příslušnou hodnotu, například `AzureADExchange`:

    ```XML
    <ClaimsProviderSelection TargetClaimsExchangeId="AzureADExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>Propojení tlačítka s akcí

Nyní, když máte tlačítko na místě, musíte jej propojit s akcí. Akce, v tomto případě je pro Azure AD B2C komunikovat s Azure AD přijímat token. Propojte tlačítko s akcí propojením technického profilu pro poskytovatele deklarací identity Azure AD.

1. Najít **OrchestrationStep,** `Order="2"` který zahrnuje v cestě uživatele.
2. Přidejte následující **claimsexchange** prvek ujistěte se, že používáte stejnou hodnotu pro **ID,** které jste použili pro **TargetClaimsExchangeId**:

    ```XML
    <ClaimsExchange Id="AzureADExchange" TechnicalProfileReferenceId="Common-AAD" />
    ```

    Aktualizujte hodnotu **TechnicalProfileReferenceId** na **ID** technického profilu, který jste vytvořili dříve. Například, `Common-AAD`.

3. Uložte soubor *TrustFrameworkExtensions.xml* a znovu jej nahrajte k ověření.

## <a name="create-an-azure-ad-b2c-application"></a>Vytvoření aplikace Azure AD B2C

Komunikace s Azure AD B2C probíhá prostřednictvím aplikace, kterou zaregistrujete ve vašem tenantovi B2C. V této části jsou uvedeny volitelné kroky, které můžete provést a vytvořit testovací aplikaci, pokud jste tak ještě neučinili.

[!INCLUDE [active-directory-b2c-appreg-idp](../../includes/active-directory-b2c-appreg-idp.md)]

## <a name="update-and-test-the-relying-party-file"></a>Aktualizace a testování souboru předávající strany

Aktualizujte soubor předávající strany (RP), který iniciuje cestu uživatele, kterou jste vytvořili:

1. Vytvořte kopii *souboru SignUpOrSignIn.xml* ve svém pracovním adresáři a přejmenujte ji. Přejmenujte jej například na *SignUpSignContoso.xml*.
1. Otevřete nový soubor a aktualizujte hodnotu atributu **PolicyId** pro **TrustFrameworkPolicy** s jedinečnou hodnotou. Například, `SignUpSignInContoso`.
1. Aktualizujte hodnotu **PublicPolicyUri** pomocí identifikátoru URI pro zásadu. Například, `http://contoso.com/B2C_1A_signup_signin_contoso`.
1. Aktualizujte hodnotu atributu **ReferenceId** v **DefaultUserJourney** tak, aby odpovídalo ID cesty uživatele, kterou jste vytvořili dříve. Například *SignUpSignInContoso*.
1. Uložte změny a nahrajte soubor.
1. V části **Vlastní zásady**vyberte novou zásadu v seznamu.
1. V rozevíracím souboru **Select aplikace** vyberte aplikaci Azure AD B2C, kterou jste vytvořili dříve. Například *testapp1*.
1. Zkopírujte **koncový bod Spustit nyní** a otevřete jej v okně soukromého prohlížeče, například v anonymním režimu v prohlížeči Google Chrome nebo v okně InPrivate v prohlížeči Microsoft Edge. Otevření v okně soukromého prohlížeče umožňuje otestovat celou cestu uživatele tím, že nepoužíváte žádné aktuálně uložené přihlašovací údaje Azure AD.
1. Vyberte přihlašovací tlačítko Azure AD, například *Contoso Employee*, a zadejte přihlašovací údaje pro uživatele v jednom z vašich klientů organizace Azure AD. Budete vyzváni k autorizaci přihlášky a zadání informací pro svůj profil.

Pokud je proces přihlášení úspěšný, váš prohlížeč `https://jwt.ms`je přesměrován na , který zobrazuje obsah tokenu vráceného Azure AD B2C.

Chcete-li otestovat možnost přihlášení s více klienty, proveďte poslední dva kroky pomocí přihlašovacích údajů pro uživatele, který existuje jiný klient Azure AD.

## <a name="next-steps"></a>Další kroky

Při práci s vlastními zásadami může někdy potřebovat další informace při řešení potíží se zásadami během jejího vývoje.

Chcete-li diagnostikovat problémy, můžete dočasně umístit zásady do "režimu pro vývojáře" a shromažďovat protokoly pomocí Azure Application Insights. Zjistěte, jak ve [službě Azure Active Directory B2C: Shromažďování protokolů](troubleshoot-with-application-insights.md).
