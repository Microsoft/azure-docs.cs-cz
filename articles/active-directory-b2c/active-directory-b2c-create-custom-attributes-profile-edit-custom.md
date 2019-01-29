---
title: Přidat vlastní atributy pro vlastní zásady v Azure Active Directory B2C | Dokumentace Microsoftu
description: Návod k použití vlastnosti rozšíření a vlastní atributy a jejich zahrnování v uživatelském rozhraní.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/04/2017
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 1f79330f12117c6ade8884165d1538623e19c7ea
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55175260"
---
# <a name="azure-active-directory-b2c-use-custom-attributes-in-a-custom-profile-edit-policy"></a>Azure Active Directory B2C: Použití vlastních atributů ve vlastním profilu upravit zásadu

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

V tomto článku vytvoříte vlastní atribut v adresáři Azure Active Directory (Azure AD) B2C. Tento nový atribut budete používat jako vlastní deklarace identity v cestě uživatele úpravy profilu.

## <a name="prerequisites"></a>Požadavky

Postupujte podle kroků v článku [Azure Active Directory B2C: Začínáme s vlastními zásadami](active-directory-b2c-get-started-custom.md).

## <a name="use-custom-attributes-to-collect-information-about-your-customers-in-azure-ad-b2c-by-using-custom-policies"></a>Použití vlastních atributů ke shromažďování informací o zákaznících v Azure AD B2C s využitím vlastních zásad
Služby Azure AD B2C se dodává s integrovanou sadu atributů. Mezi příklady patří **křestní jméno**, **příjmení**, **Město**, **PSČ**, a **userPrincipalName**. Často je potřeba vytvořit vlastní atributy jako v těchto příkladech:
* Aplikaci určenou pro zákazníky musí zachovat v atributu jako **LoyaltyNumber.**
* Zprostředkovatel identity nemá jedinečný identifikátor uživatele jako **uniqueUserGUID** , který musí být uložen.
* Cesta vlastní uživatele musí zachovat stav uživatele, jako je **migrationStatus**.

Azure AD B2C rozšiřuje sadu atributů uložené u každého uživatelského účtu. Může číst a zapisovat pomocí těchto atributů [Azure AD Graph API](active-directory-b2c-devquickstarts-graph-dotnet.md).

Vlastnosti rozšíření rozšíření schématu objektů uživatelů v adresáři. Podmínky *vlastnost rozšíření*, *vlastního atributu*, a *vlastních deklarací identity* odkazovat na stejnou věc v rámci tohoto článku. Název se liší v závislosti na kontextu, například aplikace, objekt nebo zásad.

Vlastnosti rozšíření lze registrovat pouze pro objekt aplikace i v případě, že může obsahovat data pro uživatele. Vlastnost je připojen k aplikaci. Objekt aplikace musí mít oprávnění k zápisu do registrace vlastnosti rozšíření. Set vlastnosti rozšíření, všechny typy a všechny aplikace, je možné zapisovat na libovolný objekt. Vlastnosti rozšíření jsou přidány do typu cílového adresáře a stanou okamžitě dostupné v tenanta adresáře Azure AD B2C.
Pokud je aplikace odstraněna, se odeberou také tyto vlastnosti rozšíření spolu se všechna data v nich obsažené pro všechny uživatele. Pokud vlastnost rozšíření je aplikace odstraní, odebere se současně na cílové objektů adresáře a hodnoty budou odstraněny.

Vlastnosti rozšíření existují pouze v kontextu zaregistrovanou aplikaci v tenantovi. Objekt musí být součástí ID této aplikace **technický profil** , která používá.

>[!NOTE]
>Adresář Azure AD B2C obvykle zahrnuje webovou aplikaci s názvem `b2c-extensions-app`. Tato aplikace se používá především B2C integrované zásady pro vlastní deklarace identity vytvořené prostřednictvím webu Azure portal. Doporučujeme vám, že jenom Pokročilí uživatelé zaregistrovat rozšíření pro vlastní zásady B2C pomocí této aplikace.  
Pokyny jsou součástí **další kroky** části v tomto článku.

## <a name="create-a-new-application-to-store-the-extension-properties"></a>Vytvoření nové aplikace a uložit vlastnosti rozšíření

1. Otevřete relaci prohlížeče a přejděte [webu Azure portal](https://portal.azure.com). Přihlaste se pomocí přihlašovacích údajů správce adresář B2C, které chcete provést konfiguraci.
2. Vyberte **Azure Active Directory** v levé navigační nabídce. Možná budete muset najít tak, že vyberete **další služby**.
3. Vyberte **Registrace aplikací**. Vyberte **Registrace nové aplikace**.
4. Zadejte následující položky:
    * Název webové aplikace: **WebApp-GraphAPI-DirectoryExtensions**.
    * Typ aplikace: **Webové aplikace nebo rozhraní API**.
    * Adresa URL přihlašování: **https://{tenantName}.onmicrosoft.com/WebApp-GraphAPI-DirectoryExtensions**.
5. Vyberte **Vytvořit**.
6. Vyberte nově vytvořenou webovou aplikaci.
7. Vyberte **nastavení** > **požadovaná oprávnění**.
8. Vyberte rozhraní API **Windows Azure Active Directory**.
9. Zaškrtněte oprávnění aplikací: **Čtení a zápis dat adresáře**. Potom vyberte **Uložit**.
10. Zvolte **udělit oprávnění** a potvrďte **Ano**.
11. Zkopírujte následující identifikátory do schránky a uloží:
    * **ID aplikace**. Příklad: `103ee0e6-f92d-4183-b576-8c3739027780`.
    * **ID objektu**. Příklad: `80d8296a-da0a-49ee-b6ab-fd232aa45201`.

## <a name="modify-your-custom-policy-to-add-the-applicationobjectid"></a>Upravit vlastní zásady pro přidání **ApplicationObjectId**

Pokud jste postupovali podle kroků v [Azure Active Directory B2C: Začínáme s vlastními zásadami](active-directory-b2c-get-started-custom.md), stáhli a upravili v [ukázkové soubory](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/archive/master.zip) s názvem **TrustFrameworkBase.xml**, **TrustFrameworkExtensions.xml**, **SignUpOrSignin.xml**, **ProfileEdit.xml**, a **PasswordReset.xml**. V tomto kroku provedete víc úpravy na tyto soubory.

* Otevřít **TrustFrameworkBase.xml** a přidejte `Metadata` části, jak je znázorněno v následujícím příkladu. ID objektu, který jste si dříve poznamenali pro vložení `ApplicationObjectId` hodnotu a ID aplikace, které jste si poznamenali pro `ClientId` hodnotu: 

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

> [!NOTE]
> Když **technický profil** zapíše poprvé k vlastnosti nově vytvořeného rozšíření, můžete zaznamenat chybu jednorázové. Vlastnosti rozšíření se vytvoří při prvním se používá.

## <a name="use-the-new-extension-property-or-custom-attribute-in-a-user-journey"></a>Použití nové vlastnosti rozšíření nebo vlastního atributu v cestě uživatele

1. Otevřít **ProfileEdit.xml** souboru.
2. Přidat vlastní deklarace identity `loyaltyId`. Zahrnutím vlastní deklarace identity v `<RelyingParty>` elementu, je zahrnutý v tokenu pro aplikaci.
    
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

3. Otevřít **TrustFrameworkExtensions.xml** a přidejte`<ClaimsSchema>` elementu a jeho podřízených prvků, které `BuildingBlocks` element:

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

4. Přidání stejné `ClaimType` definici **TrustFrameworkBase.xml**. Není nutné přidat `ClaimType` definice v základní třídě a přípony souborů. Však přidat další kroky `extension_loyaltyId` k **TechnicalProfiles** v základního souboru. Tak zásady program pro ověření odmítne nahrávání základního souboru bez něho. Může být užitečné pro sledování spuštění cestu uživatele s názvem **ProfileEdit** v **TrustFrameworkBase.xml** souboru. Hledání pro cestu uživatele se stejným názvem v editoru. Podívejte se, že krok Orchestrace 5 vyvolá **TechnicalProfileReferenceID = "SelfAsserted ProfileUpdate**. Hledání a kontrolovat toto **technický profil** Seznamte se s postupem.

5. Otevřít **TrustFrameworkBase.xml** a přidejte `loyaltyId` jako vstup a výstup služby deklarací identity v **technický profil SelfAsserted-ProfileUpdate**:

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

6. V **TrustFrameworkBase.xml** přidejte `loyaltyId` deklaraci identity pro **technický profil AAD-UserWriteProfileUsingObjectId**. Toto přidání nevyřeší hodnotu deklarace identity ve vlastnosti rozšíření pro aktuálního uživatele v adresáři:

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

7. V **TrustFrameworkBase.xml** přidejte `loyaltyId` deklaraci identity pro **technický profil AAD-UserReadUsingObjectId** načíst hodnotu atributu rozšíření pokaždé, když se uživatel přihlásí. Zatím **TechnicalProfiles** byly změněny v toku pouze místní účty. Pokud chcete nový atribut v toku účtu sociálních sítí nebo federované, jinou sadu **TechnicalProfiles** potřeba změnit. Zobrazit **další kroky** oddílu.

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

1. Otevře se okno Azure AD B2C a přejděte do **architekturu rozhraní identit** > **vlastní zásady**.
1. Vyberte vlastní zásadu, kterou jste nahráli. Vyberte **spustit nyní**.
1. Přihlásit se pomocí e-mailovou adresu.

ID token odeslaných zpět do vaší aplikace obsahuje nové vlastnosti rozšíření jako vlastní deklarace identity předchází **extension_loyaltyId**. Prohlédněte si následující příklad:

```json
{
  "exp": 1493585187,
  "nbf": 1493581587,
  "ver": "1.0",
  "iss": "https://contoso.b2clogin.com/f06c2fe8-709f-4030-85dc-38a4bfd9e82d/v2.0/",
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

1. Přidá novou deklaraci na toky pro přihlášení k účtů na sociálních sítích tak, že změníte následující **TechnicalProfiles**. Účty sociálních sítí a federované pomocí těchto dvou **TechnicalProfiles** k přihlášení. Zapisují a čtou data uživatele pomocí **alternativeSecurityId** jako Lokátor objektu user.

  ```xml
    <TechnicalProfile Id="AAD-UserWriteUsingAlternativeSecurityId">

    <TechnicalProfile Id="AAD-UserReadUsingAlternativeSecurityId">
  ```

2. Použijte stejné atributy rozšíření mezi předdefinované a vlastní zásady. Když přidáte rozšíření nebo vlastní atributy přes portál, tyto atributy jsou registrované pomocí **b2c-extensions-app** , která existuje v každé tenanta B2C. Proveďte následující kroky a použít atributy rozšíření ve vlastních zásadách:

  a. V rámci vašeho tenanta B2C na stránce portal.azure.com, přejděte na **Azure Active Directory** a vyberte **registrace aplikací**.  
  b. Najít vaše **b2c-extensions-app** a vyberte ji.  
  c. V části **Essentials**, zadejte **ID aplikace** a **ID objektu**.  
  d. Zahrnutí do vaší **AAD běžné** metadat technický profil:  

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

3. Zůstane konzistentní s prostředím portálu. Vytvoření těchto atributů pomocí uživatelské rozhraní portálu předtím, než je použijete v vlastních zásad. Při vytváření atributu **ActivationStatus** na portálu, musíte na ni můžete odkazovat následujícím způsobem:

  ```
  extension_ActivationStatus in the custom policy.
  extension_<app-guid>_ActivationStatus via Graph API.
  ```

## <a name="reference"></a>Referenční informace

Další informace o vlastnostech rozšíření najdete v článku [rozšíření schématu adresáře | Koncepty rozhraní Graph API](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-directory-schema-extensions).

> [!NOTE]
> * A **technický profil** je typ prvku, nebo funkce, který definuje název koncového bodu, metadata a protokolu. **Technický profil** podrobnosti výměny deklarací identity, které provádí architekturu rozhraní identit. Když tato funkce je volána v jednom z kroků Orchestrace nebo z jiného **technický profil**, **InputClaims** a **OutputClaims** jsou poskytovány jako parametry volající .  
> * Atributy rozšíření v rozhraní Graph API jsou pojmenovány používá konvence `extension_ApplicationObjectID_attributename`.  
> * Vlastní zásady odkazovat na rozšíření atributy jako **extension_attributename**. Vynechává se tento odkaz **ApplicationObjectId** ve formátu XML.
