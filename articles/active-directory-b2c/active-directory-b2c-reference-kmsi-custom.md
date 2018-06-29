---
title: Zůstat přihlášeni v Azure Active Directory B2C | Microsoft Docs
description: Téma, který ukazuje, jak nastavit "zajistit mi přihlášení".
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 09/05/2016
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: a32aaa5d91426199c29765d2d9645e8a4ddb03b4
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/27/2018
ms.locfileid: "34709151"
---
# <a name="azure-active-directory-b2c-enable-keep-me-signed-in-kmsi"></a>Azure Active Directory B2C: Povolte 'zůstat přihlášeni (KMSI).  
[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure AD B2C teď umožňuje web a nativních aplikací k povolení funkcí, zůstat přihlášeni (KMSI)'. Tato funkce uděluje přístup k vrácení uživatelé aplikaci bez zobrazení výzvy k znovu zadejte uživatelské jméno a heslo. Tento přístup odvolaný při odhlášení uživatele. 

Doporučujeme, abyste před uživatelé kontrola této možnosti na veřejné počítače. 

![obrázek](images/kmsi.PNG)


## <a name="prerequisites"></a>Požadavky

Klient služby Azure AD B2C nakonfigurovat tak, aby místní účet registrace-množství nebo přihlášení, jak je popsáno v [Začínáme](active-directory-b2c-get-started-custom.md).

## <a name="how-to-enable-kmsi"></a>Postup povolení KMSI

Proveďte následující změny v vaše zásady důvěryhodnosti framework rozšíření.

## <a name="adding-a-content-definition-element"></a>Přidání obsahu definice elementu 

`BuildingBlocks` Musí zahrnovat uzlu souboru rozšíření `ContentDefinitions` elementu. 

1. V `ContentDefinitions` části, zadejte nový `ContentDefinition` s ID `api.signuporsigninwithkmsi`.
2. Nové `ContentDefinition` musí obsahovat `LoadUri`, `RecoveryUri` a `DataUri` následujícím způsobem.
3. Datauri`urn:com:microsoft:aad:b2c:elements:unifiedssp:1.1.0` je identifikátor nerozumí počítač, který zobrazí zaškrtávací políčko KMSI v přihlašovací stránky. Zkontrolujte prosím, že nemáte tuto hodnotu změnit. 

```XML
  <BuildingBlocks>
    <ContentDefinitions>
      <ContentDefinition Id="api.signuporsigninwithkmsi">
        <LoadUri>~/tenant/default/unified.cshtml</LoadUri>
        <RecoveryUri>~/common/default_page_error.html</RecoveryUri>
        <DataUri>urn:com:microsoft:aad:b2c:elements:unifiedssp:1.1.0</DataUri>
        <Metadata>
          <Item Key="DisplayName">Signin and Signup</Item>
        </Metadata>
      </ContentDefinition>
    </ContentDefinitions>
  </BuildingBlocks>                       
```



## <a name="add-a--local-account-sign-in-claims-provider"></a>Přidání poskytovatele deklarací identity přihlášení místní účet 

Místní přihlášení účtu můžete definovat jako zprostředkovatele deklarací identity k `<ClaimsProvider>` uzlu v souboru rozšíření zásad:

1. Otevřete soubor rozšíření (TrustFrameworkExtensions.xml) z pracovního adresáře. 
2. Najít `<ClaimsProviders>` části. Pokud neexistuje, přidejte ji do kořenového uzlu.
3. Startovní sady z [Začínáme](active-directory-b2c-get-started-custom.md) se dodává s poskytovatele deklarací identity, místní účet přihlášení". 
4. Pokud ne, přidejte nový `<ClaimsProvider>` uzlu následujícím způsobem:

```XML
<ClaimsProviders>
    <ClaimsProvider>
      <DisplayName>Local Account SignIn</DisplayName>
      <TechnicalProfiles>
         <TechnicalProfile Id="login-NonInteractive">
           <Metadata>
            <Item Key="client_id">ProxyIdentityExperienceFrameworkAppId</Item>
            <Item Key="IdTokenAudience">IdentityExperienceFrameworkAppId</Item>
           </Metadata>
            <InputClaim ClaimTypeReferenceId="client_id" DefaultValue="ProxyIdentityExperienceFrameworkAppID" />
            <InputClaim ClaimTypeReferenceId="resource_id" PartnerClaimType="resource" DefaultValue="IdentityExperienceFrameworkAppID" />
           </InputClaims>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
 </ClaimsProviders>
```

### <a name="add-the-application-ids-to-your-custom-policy"></a>Přidat ID aplikace do vlastních zásad

Přidat ID aplikace souboru rozšíření (`TrustFrameworkExtensions.xml`):

1. V souboru rozšíření (TrustFrameworkExtensions.xml) nalezen element `<TechnicalProfile Id="login-NonInteractive">` a `<TechnicalProfile Id="login-NonInteractive-PasswordChange">`

2. Nahraďte všechny výskyty `IdentityExperienceFrameworkAppId` s ID aplikace Framework prostředí Identity aplikace, jak je popsáno v [Začínáme](active-directory-b2c-get-started-custom.md). Zde naleznete příklad:

   ```
   <Item Key="client_id">8322dedc-cbf4-43bc-8bb6-141d16f0f489</Item>
   ```

3. Nahraďte všechny výskyty `ProxyIdentityExperienceFrameworkAppId` s ID aplikace Framework prostředí Identity Proxy aplikace, jak je popsáno v [Začínáme](active-directory-b2c-get-started-custom.md).

4. Uložte soubor rozšíření.

## <a name="create-a-kmsi-in-enabled-user-journey"></a>Vytvoření KMSI v cesty povoleného uživatele

Teď je potřeba přidat poskytovatele deklarací identity místní účet přihlášení pro vaše uživatele cesty. 

1. Otevřete soubor základní zásad (například TrustFrameworkBase.xml).
2. Najít `<UserJourneys>` elementu a zkopírujte celou `<UserJourney>` uzlu, který zahrnuje `Id="SignUpOrSignIn"`.
3. Otevřete soubor rozšíření (například TrustFrameworkExtensions.xml) a najděte `<UserJourneys>` elementu. Pokud element neexistuje, přidejte jedno.
4. Vložte celý `<UserJourney>` uzlu, který jste zkopírovali jako podřízenou `<UserJourneys>` elementu.
5. Přejmenujte ID nové cesty uživatele (například přejmenování jako `SignUpOrSignInWithKmsi`).
6. Nakonec v `OrchestrationStep 1` změnit `ContentDefinitionReferenceId` k `api.signuporsigninwithkmsi` , definována v dřívějších krocích. To umožňuje políčka v cesty uživatele. 
7. Dokončení úpravy souboru rozšíření. Uložte a nahrajte tento soubor. Ujistěte se, že všechny ověření úspěšné.

```XML
<UserJourneys>
    <UserJourney Id="SignUpOrSignInWithKmsi">
      <OrchestrationSteps>
        <OrchestrationStep Order="1" Type="CombinedSignInAndSignUp" ContentDefinitionReferenceId="api.signuporsigninwithkmsi">
          <ClaimsProviderSelections>
            <ClaimsProviderSelection ValidationClaimsExchangeId="LocalAccountSigninEmailExchange" />
          </ClaimsProviderSelections>
          <ClaimsExchanges>
            <ClaimsExchange Id="LocalAccountSigninEmailExchange" TechnicalProfileReferenceId="SelfAsserted-LocalAccountSignin-Email" />
          </ClaimsExchanges>
        </OrchestrationStep>
        <OrchestrationStep Order="2" Type="ClaimsExchange">
          <Preconditions>
            <Precondition Type="ClaimsExist" ExecuteActionsIf="true">
              <Value>objectId</Value>
              <Action>SkipThisOrchestrationStep</Action>
            </Precondition>
          </Preconditions>
          <ClaimsExchanges>
            <ClaimsExchange Id="SignUpWithLogonEmailExchange" TechnicalProfileReferenceId="LocalAccountSignUpWithLogonEmail" />
          </ClaimsExchanges>
        </OrchestrationStep>
        <!-- This step reads any user attributes that we may not have received when in the token. -->
        <OrchestrationStep Order="3" Type="ClaimsExchange">
          <ClaimsExchanges>
            <ClaimsExchange Id="AADUserReadWithObjectId" TechnicalProfileReferenceId="AAD-UserReadUsingObjectId" />
          </ClaimsExchanges>
        </OrchestrationStep>
        <OrchestrationStep Order="4" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="JwtIssuer" />
      </OrchestrationSteps>
      <ClientDefinition ReferenceId="DefaultWeb" />
    </UserJourney>
  </UserJourneys>
```

## <a name="create-a-relying-party-rp-file"></a>Vytvořte soubor předávající stranu

Potom aktualizujte soubor předávající stranu, který iniciuje cesty uživatele, který jste vytvořili:

1. Vytvořte kopii SignUpOrSignIn.xml v pracovní adresář. Potom přejmenujte ji (například SignUpOrSignInWithKmsi.xml).

2. Otevřete nový soubor a aktualizace `PolicyId` atribut pro `<TrustFrameworkPolicy>` s jedinečnou hodnotu. Toto je název vaší zásady (například SignUpOrSignInWithKmsi).

3. Změnit `ReferenceId` atribut `<DefaultUserJourney>` tak, aby odpovídala `Id` nové cesty uživatele, který jste vytvořili (například SignUpOrSignInWithKmsi).

4. KMSI je nakonfigurovaný v `UserJourneyBehaviors`. 

5. **`KeepAliveInDays`** Určuje, jak dlouho zůstane přihlášeného uživatele. V následujícím příkladu KMSI relace automaticky vyprší 14 dnů bez ohledu na to, jak často uživatel provede bezobslužnou ověřování.

   Nastavení `KeepAliveInDays` hodnota 0 vypne KMSI funkce. Ve výchozím nastavení tato hodnota je 0

6. Pokud **`SessionExpiryType`** je *kolejová*, pak KMSI relace je rozšířeno 14 dnů pokaždé, když uživatel provede bezobslužnou ověřování.  Pokud *kolejová* je vybraný, doporučujeme zachovat s minimální počet dnů. 

       <RelyingParty>
       <DefaultUserJourney ReferenceId="SignUpOrSignInWithKmsi" />
       <UserJourneyBehaviors>
         <SingleSignOn Scope="Tenant" KeepAliveInDays="14" />
         <SessionExpiryType>Absolute</SessionExpiryType>
         <SessionExpiryInSeconds>1200</SessionExpiryInSeconds>
       </UserJourneyBehaviors>
       <TechnicalProfile Id="PolicyProfile">
         <DisplayName>PolicyProfile</DisplayName>
         <Protocol Name="OpenIdConnect" />
         <OutputClaims>
           <OutputClaim ClaimTypeReferenceId="displayName" />
           <OutputClaim ClaimTypeReferenceId="givenName" />
           <OutputClaim ClaimTypeReferenceId="surname" />
           <OutputClaim ClaimTypeReferenceId="email" />
           <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
         </OutputClaims>
         <SubjectNamingInfo ClaimType="sub" />
       </TechnicalProfile>
       </RelyingParty>

7. Uložte změny a potom soubor odešlete.

8. Pokud chcete otestovat vlastní zásady, který jste nahráli, na portálu Azure, přejděte do okna zásady a pak klikněte na tlačítko **spustit nyní**.


## <a name="link-to-sample-policy"></a>Propojit ukázka zásad

Můžete najít ukázkové zásady [zde](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/keep%20me%20signed%20in).








