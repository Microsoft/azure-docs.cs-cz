---
title: Zůstat přihlášeni v Azure Active Directory B2C | Dokumentace Microsoftu
description: Téma ukazuje, jak nastavit "zachovat zůstat přihlášeni".
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/05/2016
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 6bad6e1f2b204f76b075652a9d3f27367a8de49f
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/03/2018
ms.locfileid: "37441313"
---
# <a name="azure-active-directory-b2c-enable-keep-me-signed-in-kmsi"></a>Azure Active Directory B2C: Povolte "neodhlašovat (políčko zůstat Přihlášeni).  
[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure AD B2C teď umožňuje vašich webových a nativních aplikací k povolení funkcí "Neodhlašovat (políčko zůstat Přihlášeni)". Tato funkce uděluje přístup k vrácení uživatelé aplikaci bez zobrazení výzvy znovu zadat uživatelské jméno a heslo. Když se uživatel odhlásí, je tento přístup odvolat. 

Nedoporučujeme tuto možnost na veřejné počítače kontroly uživatelů. 

![obrázek](images/kmsi.PNG)


## <a name="prerequisites"></a>Požadavky

Tenanta služby Azure AD B2C nakonfigurované tak, aby místní účty přihlášení-registrace/přihlášení, jak je popsáno v [Začínáme](active-directory-b2c-get-started-custom.md).

## <a name="how-to-enable-kmsi"></a>Jak povolit políčko zůstat Přihlášeni

Proveďte následující změny v zásadách zabezpečení framework rozšíření.

## <a name="adding-a-content-definition-element"></a>Přidání elementu definice obsahu 

`BuildingBlocks` Uzlu souboru rozšíření musí obsahovat `ContentDefinitions` elementu. 

1. V `ContentDefinitions` části, definovat novou `ContentDefinition` s ID `api.signuporsigninwithkmsi`.
2. Vaše nová `ContentDefinition` musí obsahovat `LoadUri`, `RecoveryUri` a `DataUri` následujícím způsobem.
3. Parametr`urn:com:microsoft:aad:b2c:elements:unifiedssp:1.1.0` srozumitelné identifikátor počítače, který se zobrazí zaškrtávací políčko políčko zůstat Přihlášeni v přihlašovací stránky. Ujistěte se prosím, že nemusíte tuto hodnotu změnit. 

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



## <a name="add-a--local-account-sign-in-claims-provider"></a>Přidat zprostředkovatele deklarací identity přihlášení místním účtem 

Můžete definovat přihlášení místním účtem jako zprostředkovatele deklarací identity, aby `<ClaimsProvider>` uzel v souboru rozšíření zásady:

1. Otevřete soubor rozšíření (TrustFrameworkExtensions.xml) z pracovního adresáře. 
2. Najít `<ClaimsProviders>` oddílu. Pokud neexistuje, přidejte ho do kořenového uzlu.
3. Úvodní balíček z [Začínáme](active-directory-b2c-get-started-custom.md) součástí zprostředkovatele deklarací identity přihlášení místním účtem. 
4. Pokud ne, přidejte novou `<ClaimsProvider>` uzel následujícím způsobem:

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

### <a name="add-the-application-ids-to-your-custom-policy"></a>ID aplikace přidat do vlastní zásady

Přidejte do souboru rozšíření ID aplikace (`TrustFrameworkExtensions.xml`):

1. V souboru rozšíření (TrustFrameworkExtensions.xml) najděte prvek `<TechnicalProfile Id="login-NonInteractive">` a `<TechnicalProfile Id="login-NonInteractive-PasswordChange">`

2. Nahraďte všechny výskyty `IdentityExperienceFrameworkAppId` s ID aplikace, architekturu rozhraní identit aplikace, jak je popsáno v [Začínáme](active-directory-b2c-get-started-custom.md). Zde naleznete příklad:

   ```
   <Item Key="client_id">8322dedc-cbf4-43bc-8bb6-141d16f0f489</Item>
   ```

3. Nahraďte všechny výskyty `ProxyIdentityExperienceFrameworkAppId` s ID aplikace, architekturu rozhraní identit Proxy aplikace, jak je popsáno v [Začínáme](active-directory-b2c-get-started-custom.md).

4. Uložte soubor rozšíření.

## <a name="create-a-kmsi-in-enabled-user-journey"></a>Vytvoření políčko zůstat Přihlášeni na cestě povoleného uživatele

Teď musíte přidat poskytovatele deklarací identity přihlášení místním účtem vaší cesty uživatele. 

1. Otevřete soubor základní zásady (například TrustFrameworkBase.xml).
2. Najít `<UserJourneys>` elementu a zkopírujte celý `<UserJourney>` uzel, který zahrnuje `Id="SignUpOrSignIn"`.
3. Otevřete soubor rozšíření (například TrustFrameworkExtensions.xml) a vyhledejte `<UserJourneys>` elementu. Pokud element neexistuje, přidejte jeden.
4. Vložit celé `<UserJourney>` uzel, který jste zkopírovali jako podřízený objekt `<UserJourneys>` elementu.
5. Přejmenovat ID nové cesty uživatele (například přejmenovat jako `SignUpOrSignInWithKmsi`).
6. Nakonec v `OrchestrationStep 1` změnit `ContentDefinitionReferenceId` k `api.signuporsigninwithkmsi` , definované v předchozích krocích. To umožňuje zaškrtávací políčko v cestě uživatele. 
7. Dokončení úpravy souboru rozšíření. Uložte a odešlete tento soubor. Ujistěte se, že všechny ověření úspěšné.

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

## <a name="create-a-relying-party-rp-file"></a>Vytvořit soubor předávající stranu

V dalším kroku aktualizujte předávající stranu soubor, který iniciuje cesty uživatele, který jste vytvořili:

1. Vytvořte kopii SignUpOrSignIn.xml ve svém pracovním adresáři. Přejmenujte (například SignUpOrSignInWithKmsi.xml).

2. Otevřete nový soubor a aktualizace `PolicyId` atributu `<TrustFrameworkPolicy>` s jedinečnou hodnotu. Toto je název zásady (například SignUpOrSignInWithKmsi).

3. Upravit `ReferenceId` atribut `<DefaultUserJourney>` tak, aby odpovídaly `Id` nové cesty uživatele, který jste vytvořili (například SignUpOrSignInWithKmsi).

4. Políčko zůstat Přihlášeni je nakonfigurovaný v `UserJourneyBehaviors`. 

5. **`KeepAliveInDays`** Určuje, jak dlouho zůstane přihlášený uživatel. V následujícím příkladu políčko zůstat Přihlášeni relace automaticky vyprší 14 dnů bez ohledu na to, kolikrát uživatel provádí bezobslužné ověření.

   Nastavení `KeepAliveInDays` hodnota 0 vypne funkce políčko zůstat Přihlášeni. Ve výchozím nastavení tato hodnota je 0

6. Pokud **`SessionExpiryType`** je *Hromadná*, pak políčko zůstat Přihlášeni relace je rozšířená 14 dny pokaždé, když uživatel provádí bezobslužné ověření.  Pokud *Hromadná* je vybraný, doporučujeme udržovat počet dní, minimální. 

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

7. Uložte změny a pak nahrajte soubor.

8. Vlastní zásady, které jste nahráli, otestovat na webu Azure Portal, přejděte na okno zásad a pak klikněte na tlačítko **spustit nyní**.


## <a name="link-to-sample-policy"></a>Odkaz na ukázkové zásady

Můžete najít ukázkové zásady [tady](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/keep%20me%20signed%20in).








