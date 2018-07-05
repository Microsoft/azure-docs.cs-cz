---
title: Přidat vlastní atributy pro vlastní zásady v Azure Active Directory B2C | Dokumentace Microsoftu
description: Návod k použití vlastnosti rozšíření, vlastní atributy a jejich zahrnování v uživatelském rozhraní.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/04/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: ecde4d8cd8ee454290b16b640ba05d310cf348fe
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/03/2018
ms.locfileid: "37449875"
---
# <a name="azure-active-directory-b2c-creating-and-using-custom-attributes-in-a-custom-profile-edit-policy"></a>Azure Active Directory B2C: Vytváření a používání vlastních atributů ve vlastním profilu upravit zásadu

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

V tomto článku vytvořit vlastní atribut v adresáři Azure AD B2C a použít tento nový atribut jako vlastní deklarace identity v cestě uživatele úpravy profilu.

## <a name="prerequisites"></a>Požadavky

Proveďte kroky v následujícím článku [Začínáme se zásadami vlastní](active-directory-b2c-get-started-custom.md).

## <a name="use-custom-attributes-to-collect-information-about-your-customers-in-azure-active-directory-b2c-using-custom-policies"></a>Použití vlastních atributů ke shromažďování informací o zákaznících v Azure Active Directory B2C pomocí vlastních zásad
Služby Azure Active Directory (Azure AD) B2C se dodává s integrovanou sadu atributů: zadané jméno, příjmení, města, PSČ, userPrincipalName, atd.  Často je potřeba vytvořit vlastní atributy.  Příklad:
* Aplikaci určenou pro zákazníky musí zachovat atribut jako je například "LoyaltyNumber."
* Zprostředkovatel identity má jedinečný identifikátor uživatele, který musí být uložen jako je například "uniqueUserGUID"."
* Cesta vlastní uživatele musí zachovat stav uživatele, jako je například "migrationStatus."

S Azure AD B2C můžete rozšířit sadu atributů uložené u každého uživatelského účtu. Může číst a zapisovat pomocí těchto atributů [Azure AD Graph API](active-directory-b2c-devquickstarts-graph-dotnet.md).

Vlastnosti rozšíření rozšíření schématu objektů uživatelů v adresáři.  Vlastnost rozšíření podmínky, vlastní atribut a vlastních deklarací identity odkazuje na stejnou věc v rámci tohoto článku a název se liší v závislosti na kontextu (aplikace, objekt, zásady).

Vlastnosti rozšíření lze registrovat pouze pro objekt aplikace i když mohou obsahovat data pro uživatele. Vlastnost je připojen k aplikaci. Objekt aplikace musí být přístup k zápisu registrace vlastnosti rozšíření. K libovolnému objektu lze zapsat 100 vlastnosti rozšíření (v rámci všech typů a všech aplikací). Vlastnosti rozšíření jsou přidány do typu cílového adresáře a bude okamžitě dostupná v tenanta adresáře Azure AD B2C.
Pokud je aplikace odstraněna, se odeberou také tyto vlastnosti rozšíření spolu se všechna data v nich obsažené pro všechny uživatele. Pokud vlastnost rozšíření je aplikace odstraní, odebere se současně na cílové objektů adresáře a hodnoty odstraněn.

Vlastnosti rozšíření existují pouze v kontextu zaregistrovanou aplikaci v tenantovi. Id objektu této aplikace musí být součástí technický profil, který se použije.

>[!NOTE]
>Adresář Azure AD B2C obvykle zahrnuje webovou aplikaci s názvem `b2c-extensions-app`.  Tato aplikace se používá především b2c integrované zásady pro vlastní deklarace identity vytvořené prostřednictvím webu Azure portal.  Pomocí této aplikace k registraci rozšíření pro vlastní zásady b2c se doporučuje jenom pro pokročilé uživatele.  Pokyny k tomu jsou uvedené v části Další kroky v tomto článku.


## <a name="creating-a-new-application-to-store-the-extension-properties"></a>Vytvoření nové aplikace a uložit vlastnosti rozšíření

1. Otevřete relaci prohlížeče a přejděte [webu Azure portal](https://portal.azure.com) a přihlaste se pomocí přihlašovacích údajů správce adresář B2C, které chcete nakonfigurovat.
2. Klikněte na tlačítko **Azure Active Directory** v levé navigační nabídce. Možná budete muset najít tak, že vyberete další služby >.
3. Vyberte **registrace aplikací** a klikněte na tlačítko **registrace nové aplikace**
4. Zadejte následující doporučené položky:
    * Zadejte název webové aplikace: **webové aplikace. GraphAPI DirectoryExtensions**
    * Typ aplikace: webové aplikace nebo rozhraní API
    * URL:https://{tenantName}.onmicrosoft.com/WebApp-GraphAPI-DirectoryExtensions přihlašování
5. Vyberte **Vytvořit**.
6. Vyberte nově vytvořenou webovou aplikaci.
7. Vyberte **nastavení** > **požadovaná oprávnění**.
8. Vyberte rozhraní API **Windows Azure Active Directory**.
9. Zaškrtněte políčko v oprávnění aplikací: **pro čtení a zápis dat adresáře**a pak vyberte **Uložit**.
10. Zvolte **udělit oprávnění** a potvrďte **Ano**.
11. Zkopírujte do schránky a uložte následující identifikátory:
    * **ID aplikace** . Příklad: `103ee0e6-f92d-4183-b576-8c3739027780`
    * **ID objektu**. Příklad: `80d8296a-da0a-49ee-b6ab-fd232aa45201`



## <a name="modifying-your-custom-policy-to-add-the-applicationobjectid"></a>Úprava vaše vlastní zásady pro přidání ApplicationObjectId

Když jste dokončili kroky v [Začínáme se zásadami vlastní](active-directory-b2c-get-started-custom.md), stáhli a upravili v [soubory](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/archive/master.zip) s názvem *TrustFrameworkBase.xml*,  *TrustFrameworkExtensions.xml*, *SignUpOrSignin.xml*, *ProfileEdit.xml*, a *PasswordReset.xml*. V následujících krocích můžete pokračovat v provádění úprav těchto souborů.

1. Otevřít *TrustFrameworkBase.xml* a přidejte `Metadata` části, jak je znázorněno v následujícím příkladu. ID objektu, který jste si dříve poznamenali pro vložení `ApplicationObjectId` hodnotu a ID aplikace, které jste si poznamenali pro `ClientId` hodnotu: 

    ```xml
    <ClaimsProviders>
        <ClaimsProvider>
          <DisplayName>Azure Active Directory</DisplayName>
            <TechnicalProfile Id="AAD-Common">
          <DisplayName>Azure Active Directory</DisplayName>
          <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AzureActiveDirectoryProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
              
          <!-- Provide objectId and appId before using extension properties. -->
          <Metadata>
            <Item Key="ApplicationObjectId">insert objectId here</Item>
            <Item Key="ClientId">insert appId here</Item>
          </Metadata>
          <!-- End of changes -->
              
          <CryptographicKeys>
            <Key Id="issuer_secret" StorageReferenceId="TokenSigningKeyContainer" />
          </CryptographicKeys>
          <IncludeInSso>false</IncludeInSso>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
        </TechnicalProfile>
        </ClaimsProvider>
    </ClaimsProviders>
    ```

>[!NOTE]
>Když technický profil zapisuje do vlastnosti nově vytvořeného rozšíření poprvé, může docházet k jednorázové chybě. Vlastnosti rozšíření se vytvoří při prvním se používá.  

## <a name="using-the-new-extension-property--custom-attribute-in-a-user-journey"></a>Pomocí nové vlastnosti rozšíření / vlastní atribut v cestě uživatele

1. Otevřít *ProfileEdit.xml* souboru.
2. Přidat vlastní deklarace identity `loyaltyId`.  Zahrnutím vlastní deklarace identity v `<RelyingParty>` elementu, je zahrnutá v tokenu pro aplikaci.
    
    ```xml
    <RelyingParty>
      <DefaultUserJourney ReferenceId="ProfileEdit" />
      <TechnicalProfile Id="PolicyProfile">
        <DisplayName>PolicyProfile</DisplayName>
        <Protocol Name="OpenIdConnect" />
        <OutputClaims>
          <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
          <OutputClaim ClaimTypeReferenceId="city" />

          <!-- Provide the custom claim identifier -->
          <OutputClaim ClaimTypeReferenceId="extension_loyaltyId" />
          <!-- End of changes -->
        </OutputClaims>
        <SubjectNamingInfo ClaimType="sub" />
      </TechnicalProfile>
    </RelyingParty>
    ```

3. Otevřít *TrustFrameworkExtensions.xml* a přidejte`<ClaimsSchema>` elementu a jeho podřízených prvků, které `BuildingBlocks` element:

    ```xml
    <BuildingBlocks>
      <ClaimsSchema> 
        <ClaimType Id="extension_loyaltyId"> 
          <DisplayName>Loyalty Identification Tag</DisplayName> 
          <DataType>string</DataType> 
          <UserHelpText>Your loyalty number from your membership card</UserHelpText> 
          <UserInputType>TextBox</UserInputType> 
        </ClaimType> 
      </ClaimsSchema>
    </BuildingBlocks>
    ```

4. Přidání stejné `ClaimType` definici *TrustFrameworkBase.xml*. Přidávání `ClaimType` definice v základní třídě a přípony souboru není obvykle nutné, ale vzhledem k tomu, že přidáte další kroky `extension_loyaltyId` k TechnicalProfiles v souboru základní ověřovací modul zásad odmítnou nahrávání základního souboru bez ho. Může být užitečné pro trasování provádění cestu uživatele s názvem "ProfileEdit" v *TrustFrameworkBase.xml* souboru.  Hledání pro cestu uživatele se stejným názvem ve svém editoru a podívejte se, že Orchestrace kroku 5 vyvolá TechnicalProfileReferenceID = "SelfAsserted ProfileUpdate".  Vyhledávejte a kontrolovat toto technický profil, abyste se seznámili s postupem.

5. Otevřít *TrustFrameworkBase.xml* a přidejte `loyaltyId` jako vstup a výstup služby deklarací identity v TechnicalProfile "SelfAsserted ProfileUpdate":

    ```xml
    <TechnicalProfile Id="SelfAsserted-ProfileUpdate">
      <DisplayName>User ID signup</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="ContentDefinitionReferenceId">api.selfasserted.profileupdate</Item>
      </Metadata>
      <IncludeInSso>false</IncludeInSso>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="alternativeSecurityId" />
        <InputClaim ClaimTypeReferenceId="userPrincipalName" />
        <InputClaim ClaimTypeReferenceId="givenName" />
            <InputClaim ClaimTypeReferenceId="surname" />

        <!-- Add the loyalty identifier -->
        <InputClaim ClaimTypeReferenceId="extension_loyaltyId"/>
        <!-- End of changes -->
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="executed-SelfAsserted-Input" DefaultValue="true" />
        <OutputClaim ClaimTypeReferenceId="givenName" />
        <OutputClaim ClaimTypeReferenceId="surname" />
        
        <!-- Add the loyalty identifier -->
        <OutputClaim ClaimTypeReferenceId="extension_loyaltyId"/>
        <!-- End of changes -->

      </OutputClaims>
      <ValidationTechnicalProfiles>
        <ValidationTechnicalProfile ReferenceId="AAD-UserWriteProfileUsingObjectId" />
      </ValidationTechnicalProfiles>
    </TechnicalProfile>
    ```

6. V *TrustFrameworkBase.xml* přidejte `loyaltyId` deklaraci identity pro technický profil "AAD-UserWriteProfileUsingObjectId" zachovat hodnotu deklarace identity ve vlastnosti rozšíření pro aktuálního uživatele v adresáři:

    ```xml
    <TechnicalProfile Id="AAD-UserWriteProfileUsingObjectId">
      <Metadata>
        <Item Key="Operation">Write</Item>
        <Item Key="RaiseErrorIfClaimsPrincipalAlreadyExists">false</Item>
        <Item Key="RaiseErrorIfClaimsPrincipalDoesNotExist">true</Item>
      </Metadata>
      <IncludeInSso>false</IncludeInSso>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="objectId" Required="true" />
      </InputClaims>
      <PersistedClaims>
        <PersistedClaim ClaimTypeReferenceId="objectId" />
        <PersistedClaim ClaimTypeReferenceId="givenName" />
        <PersistedClaim ClaimTypeReferenceId="surname" />

        <!-- Add the loyalty identifier -->
        <PersistedClaim ClaimTypeReferenceId="extension_loyaltyId" />
        <!-- End of changes -->

      </PersistedClaims>
      <IncludeTechnicalProfile ReferenceId="AAD-Common" />
    </TechnicalProfile>
    ```

7. V *TrustFrameworkBase.xml* přidejte `loyaltyId` deklaraci identity pro technický profil "AAD-UserReadUsingObjectId" číst hodnotu atributu rozšíření pokaždé, když se uživatel přihlásí. Doposud TechnicalProfiles byly změněny v toku pouze místní účty.  Pokud nový atribut je požadován v toku/chyby federovaného sociálního účtu, je potřeba změnit jinou sadu TechnicalProfiles. Podívejte se na další kroky.

    ```xml
    <TechnicalProfile Id="AAD-UserReadUsingObjectId">
      <Metadata>
        <Item Key="Operation">Read</Item>
        <Item Key="RaiseErrorIfClaimsPrincipalDoesNotExist">true</Item>
      </Metadata>
      <IncludeInSso>false</IncludeInSso>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="objectId" Required="true" />
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="signInNames.emailAddress" />
        <OutputClaim ClaimTypeReferenceId="displayName" />
        <OutputClaim ClaimTypeReferenceId="otherMails" />
        <OutputClaim ClaimTypeReferenceId="givenName" />
        <OutputClaim ClaimTypeReferenceId="surname" />

        <!-- Add the loyalty identifier -->
        <OutputClaim ClaimTypeReferenceId="extension_loyaltyId" />
        <!-- End of changes -->

      </OutputClaims>
      <IncludeTechnicalProfile ReferenceId="AAD-Common" />
    </TechnicalProfile>
    ```

## <a name="test-the-custom-policy"></a>Testování vlastní zásady

1. Otevřít **okno Azure AD B2C** a přejděte do **architekturu rozhraní identit > vlastní zásady**.
1. Vyberte vlastní zásady, které jste nahráli a klikněte na tlačítko **spustit nyní** tlačítko.
1. Měli byste být schopni registrace pomocí e-mailovou adresu.

Id token odesílají zpět do vaší aplikace obsahuje nové vlastnosti rozšíření jako vlastní deklarace identity předchází extension_loyaltyId. Podívejte se na příklad.

```json
{
  "exp": 1493585187,
  "nbf": 1493581587,
  "ver": "1.0",
  "iss": "https://login.microsoftonline.com/f06c2fe8-709f-4030-85dc-38a4bfd9e82d/v2.0/",
  "sub": "a58e7c6c-7535-4074-93da-b0023fbaf3ac",
  "aud": "4e87c1dd-e5f5-4ac8-8368-bc6a98751b8b",
  "acr": "b2c_1a_trustframeworkprofileedit",
  "nonce": "defaultNonce",
  "iat": 1493581587,
  "auth_time": 1493581587,
  "extension_loyaltyId": "abc",
  "city": "Redmond"
}
```

## <a name="next-steps"></a>Další postup

### <a name="add-the-new-claim-to-the-flows-for-social-account-logins-by-changing-the-technicalprofiles-listed-below-these-two-technicalprofiles-are-used-by-socialfederated-account-logins-to-write-and-read-the-user-data-using-the-alternativesecurityid-as-the-locator-of-the-user-object"></a>Přidáte novou deklaraci do toků pro přihlášení účtu na sociální síti tak, že změníte TechnicalProfiles uvedených níže. Tyto dvě TechnicalProfiles používají sociálního/chyby federovaného účet přihlášení pro zápis a čtení dat uživatele pomocí alternativeSecurityId jako Lokátor objektu user.
```xml
  <TechnicalProfile Id="AAD-UserWriteUsingAlternativeSecurityId">

  <TechnicalProfile Id="AAD-UserReadUsingAlternativeSecurityId">
```

Pomocí stejné atributy rozšíření mezi předdefinované a vlastní zásady.
Když přidáte atributy rozšíření (označuje se také jako vlastní atributy) přes portál, tyto atributy jsou registrovány pomocí ** b2c-extensions-app, která existuje v každé tenanta b2c.  Tyto atributy rozšíření použití ve vlastních zásadách:
1. V rámci vašeho tenanta b2c na stránce portal.azure.com, přejděte na **Azure Active Directory** a vyberte **registrace aplikací**
2. Najít vaše **b2c-extensions-app** a vyberte ho
3. V části "Essentials" záznam **ID aplikace** a **ID objektu**
4. Je zahrnout v AAD běžné technické metadata profilu tak, jako je následujícím způsobem:

```xml
    <ClaimsProviders>
        <ClaimsProvider>
              <DisplayName>Azure Active Directory</DisplayName>
            <TechnicalProfile Id="AAD-Common">
              <DisplayName>Azure Active Directory</DisplayName>
              <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AzureActiveDirectoryProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
              <!-- Provide objectId and appId before using extension properties. -->
              <Metadata>
                <Item Key="ApplicationObjectId">insert objectId here</Item> <!-- This is the "Object ID" from the "b2c-extensions-app"-->
                <Item Key="ClientId">insert appId here</Item> <!--This is the "Application ID" from the "b2c-extensions-app"-->
              </Metadata>
```

Zajištění konzistence s prostředím portálu vytvořit tyto atributy, pomocí uživatelského rozhraní portálu *před* použít ve vlastních zásad.  Při vytváření atributu "ActivationStatus" na portálu, musíte na ni můžete odkazovat následujícím způsobem:

```
extension_ActivationStatus in the custom policy
extension_<app-guid>_ActivationStatus via the Graph API.
```


## <a name="reference"></a>Referenční informace

* A **technický profil (TP)** je typ elementu, který si lze představit jako *funkce* , který definuje název koncového bodu, jeho metadata, používá protokol a podrobnosti výměny deklarací identity, která identita Rozhraní prostředí by měl provádět.  Když to *funkce* je volána v jednom z kroků Orchestrace nebo od jiného TechnicalProfile InputClaims a OutputClaims jsou poskytovány jako parametry volajícím.


* Úplné zpracování ve vlastnostech rozšíření najdete v článku [rozšíření schématu adresáře | KONCEPTY ROZHRANÍ GRAPH API](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-directory-schema-extensions)

>[!NOTE]
>Atributy rozšíření v rozhraní Graph API jsou pojmenovány pomocí konvence `extension_ApplicationObjectID_attributename`. Vlastní zásady, které se odkazují na atributy rozšíření jako extension_attributename, tedy vynechání ApplicationObjectId v souboru XML
