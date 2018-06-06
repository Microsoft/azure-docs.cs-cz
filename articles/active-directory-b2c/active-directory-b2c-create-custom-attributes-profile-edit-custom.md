---
title: Přidat vlastní atributy do vlastní zásady v Azure Active Directory B2C | Microsoft Docs
description: Návod na pomocí vlastnosti rozšíření, vlastní atributy a jejich včetně v uživatelském rozhraní.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 08/04/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: e4dfb92257dca4069905f17e1c3ccd43d87cd45c
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/04/2018
ms.locfileid: "34710154"
---
# <a name="azure-active-directory-b2c-creating-and-using-custom-attributes-in-a-custom-profile-edit-policy"></a>Azure Active Directory B2C: Vytváření a používání vlastních atributů v vlastního profilu upravit zásady

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

V tomto článku můžete vytvořit vlastní atribut v adresáři Azure AD B2C a používat tento nový atribut jako vlastních deklarací identity v cesty uživatelské úpravy profilu.

## <a name="prerequisites"></a>Požadavky

Proveďte kroky v následujícím článku [Začínáme se zásadami vlastní](active-directory-b2c-get-started-custom.md).

## <a name="use-custom-attributes-to-collect-information-about-your-customers-in-azure-active-directory-b2c-using-custom-policies"></a>Použití vlastních atributů ke shromažďování informací o zákazníky v Azure Active Directory B2C pomocí vlastních zásad
Adresáře Azure Active Directory (Azure AD) B2C se dodává s integrovanou sadu atributů: zadané jméno, příjmení, Město, PSČ, userPrincipalName, atd.  Často je potřeba vytvořit vlastní atributy.  Příklad:
* Zákazník směřujících aplikací musí zachovat atribut jako je například "LoyaltyNumber."
* Zprostředkovatele identity má jedinečný identifikátor uživatele, musí být uložena jako je například "uniqueUserGUID"."
* Vlastní uživatelské cesty musí zachovat stav uživatele, jako je například "migrationStatus."

S Azure AD B2C můžete rozšířit sadu atributů uložené u každého uživatelského účtu. Můžete také číst a zapsat pomocí těchto atributů [Azure AD Graph API](active-directory-b2c-devquickstarts-graph-dotnet.md).

Vlastnosti rozšíření rozšířit schéma uživatelské objekty v adresáři.  Vlastnost rozšíření podmínky, vlastních atributů a vlastních deklarací identity označují totéž v kontextu tohoto článku a název se liší v závislosti na kontextu (aplikace, objekt, zásady).

Vlastnosti rozšíření se dají registrovat jenom na objekt aplikace i v případě, že mohou obsahovat data pro uživatele. Vlastnost je připojený k aplikaci. Objekt aplikace musí mít udělen přístup k zápisu do zaregistrovat ve vlastnosti rozšíření. K žádnému jednotlivému objektu může být napsán 100 – vlastnosti rozšíření (v rámci všech typů a všechny aplikace). Vlastnosti rozšíření jsou přidány do adresáře typ cíle a že bude okamžitě dostupný v adresáři klienta Azure AD B2C.
Pokud se aplikaci odstranit, budou odebrány také tyto vlastnosti rozšíření spolu s daty v nich obsažené pro všechny uživatele. Pokud ve vlastnosti rozšíření je odstraní aplikaci, odeberou se v cílové directory objekty a hodnoty odstranit.

Vlastnosti rozšíření existují pouze v kontextu registrovaný aplikace v klientovi. Id objektu této aplikace musí být součástí TechnicalProfile, který ho použít.

>[!NOTE]
>Adresář Azure AD B2C obvykle zahrnuje webovou aplikaci s názvem `b2c-extensions-app`.  Tato aplikace je používána především pomocí předdefinovaných zásad b2c pro vlastní deklarace vytvořené prostřednictvím portálu Azure.  Pomocí této aplikace k registraci rozšíření pro vlastní zásady b2c se doporučuje jenom pro zkušené uživatele.  Pokyny pro tento jsou uvedené v části Další kroky v tomto článku.


## <a name="creating-a-new-application-to-store-the-extension-properties"></a>Vytvoření nové aplikace a uložit vlastnosti rozšíření

1. Spustí relaci prohlížeče a přejděte do [portál Azure](https://portal.azure.com) a přihlaste se pomocí pověření správce adresáře B2C, které chcete nakonfigurovat.
1. Klikněte na tlačítko **Azure Active Directory** v levé navigační nabídce. Budete muset najít tak, že vyberete další služby >.
1. Vyberte **registrace aplikace** a klikněte na tlačítko **novou registraci aplikace**
1. Zadejte následující doporučené položky:
  * Zadejte název webové aplikace: **WebApp. GraphAPI DirectoryExtensions**
  * Typ aplikace: webové aplikace nebo rozhraní API
  * URL:https://{tenantName}.onmicrosoft.com/WebApp-GraphAPI-DirectoryExtensions přihlašování
1. Vyberte ** vytvořit. Úspěšné dokončení se zobrazí v **oznámení**
1. Vyberte nově vytvořenou webovou aplikaci: **WebApp. GraphAPI DirectoryExtensions**
1. Vyberte nastavení: **požadovaná oprávnění**
1. Vybrat rozhraní API **Windows Azure Active Directory**
1. Oprávnění aplikací, zaškrtněte: **pro čtení a zápis dat adresáře**, a **uložit**
1. Zvolte **udělit oprávnění** a potvrďte **Ano**.
1. Zkopírujte do schránky a uložte následující identifikátory z webové aplikace. GraphAPI DirectoryExtensions > Nastavení > Vlastnosti >
*  **ID aplikace** . Příklad: `103ee0e6-f92d-4183-b576-8c3739027780`
* **ID objektu**. Příklad: `80d8296a-da0a-49ee-b6ab-fd232aa45201`



## <a name="modifying-your-custom-policy-to-add-the-applicationobjectid"></a>Úprava vaše vlastní zásady pro přidání ApplicationObjectId

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
><TechnicalProfile Id="AAD-Common"> Se označuje jako "běžné", protože jeho prvky jsou součástí a opakovaně používat ve všech Azure Active Directory TechnicalProfiles pomocí prvku: `<IncludeTechnicalProfile ReferenceId="AAD-Common" />`

>[!NOTE]
>Když TechnicalProfile zapíše první vlastnost nově vytvořený rozšíření, může docházet k chybě jednorázové.  Vlastnost rozšíření se vytvoří při prvním se používá.  

## <a name="using-the-new-extension-property--custom-attribute-in-a-user-journey"></a>Pomocí nové vlastnosti rozšíření / vlastní atribut cesty uživatele


1. Otevřete úprav předávající Party(RP) soubor, který popisuje vaše zásady uživatele cesty.  Pokud jsou začínáte, může být vhodné vaší už nakonfigurované verzi souboru RP PolicyEdit stáhnout přímo z části vlastní zásady pro Azure B2C na portálu Azure.  Alternativně můžete otevřete soubor XML ze složky úložiště.
2. Přidat vlastní deklarace identity `loyaltyId`.  Zahrnutím vlastní deklarace identity v `<RelyingParty>` elementu, je jako parametr předaný UserJourney TechnicalProfiles a součástí token pro danou aplikaci.
```xml
<RelyingParty>
   <DefaultUserJourney ReferenceId="ProfileEdit" />
   <TechnicalProfile Id="PolicyProfile">
     <DisplayName>PolicyProfile</DisplayName>
     <Protocol Name="OpenIdConnect" />
     <OutputClaims>
       <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
       <OutputClaim ClaimTypeReferenceId="city" />

       <OutputClaim ClaimTypeReferenceId="extension_loyaltyId" />

     </OutputClaims>
     <SubjectNamingInfo ClaimType="sub" />
   </TechnicalProfile>
 </RelyingParty>
 ```
3. Přidejte definici deklarací identity na soubor zásad, rozšíření `TrustFrameworkExtensions.xml` uvnitř `<ClaimsSchema>` element, jak je vidět.
```xml
<ClaimsSchema>
        <ClaimType Id="extension_loyaltyId">
            <DisplayName>Loyalty Identification Tag</DisplayName>
            <DataType>string</DataType>
            <UserHelpText>Your loyalty number from your membership card</UserHelpText>
            <UserInputType>TextBox</UserInputType>
        </ClaimType>
</ClaimsSchema>
```
4. Přidání stejné deklarace identity na soubor zásad základní definice `TrustFrameworkBase.xml`.  
>Přidání `ClaimType` definice v základní a soubor rozšíření se obvykle nemusí, ale vzhledem k tomu, že další kroky přidá do TechnicalProfiles v základního souboru extension_loyaltyId, validátor zásad odmítnou nahrávání základního souboru bez.
>Může být užitečná pro sledování spuštění cesty uživatele s názvem "ProfileEdit" v souboru TrustFrameworkBase.xml.  Vyhledejte uživatele cesty se stejným názvem ve svém editoru a pozorovat, že krok 5 Orchestration vyvolá TechnicalProfileReferenceID = "SelfAsserted-ProfileUpdate".  Hledání a zkontrolovat tento TechnicalProfile Seznamte se s toku.
5. Přidat loyaltyId jako vstupní a výstupní deklarací identity v TechnicalProfile "SelfAsserted-ProfileUpdate"
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

            <!-- Optional claims. These claims are collected from the user and can be modified. Any claim added here should be updated in the
                 ValidationTechnicalProfile referenced below so it can be written to directory after being updated by the user, i.e. AAD-UserWriteProfileUsingObjectId. -->
            <InputClaim ClaimTypeReferenceId="givenName" />
            <InputClaim ClaimTypeReferenceId="surname" />
            <InputClaim ClaimTypeReferenceId="extension_loyaltyId"/>
          </InputClaims>
          <OutputClaims>
            <!-- Required claims -->
            <OutputClaim ClaimTypeReferenceId="executed-SelfAsserted-Input" DefaultValue="true" />

            <!-- Optional claims. These claims are collected from the user and can be modified. Any claim added here should be updated in the
                 ValidationTechnicalProfile referenced below so it can be written to directory after being updated by the user, i.e. AAD-UserWriteProfileUsingObjectId. -->
            <OutputClaim ClaimTypeReferenceId="givenName" />
            <OutputClaim ClaimTypeReferenceId="surname" />
            <OutputClaim ClaimTypeReferenceId="extension_loyaltyId"/>
          </OutputClaims>
          <ValidationTechnicalProfiles>
            <ValidationTechnicalProfile ReferenceId="AAD-UserWriteProfileUsingObjectId" />
          </ValidationTechnicalProfiles>
        </TechnicalProfile>
```
6. Přidání deklarace identity v TechnicalProfile "AAD-UserWriteProfileUsingObjectId" udržení hodnoty deklarace identity ve vlastnosti rozšíření pro aktuálního uživatele v adresáři.
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
            <!-- Required claims -->
            <PersistedClaim ClaimTypeReferenceId="objectId" />

            <!-- Optional claims -->
            <PersistedClaim ClaimTypeReferenceId="givenName" />
            <PersistedClaim ClaimTypeReferenceId="surname" />
            <PersistedClaim ClaimTypeReferenceId="extension_loyaltyId" />

          </PersistedClaims>
          <IncludeTechnicalProfile ReferenceId="AAD-Common" />
        </TechnicalProfile>
```
7. Přidání deklarace identity v TechnicalProfile "AAD-UserReadUsingObjectId" načíst hodnotu atributu rozšíření pokaždé, když se uživatel přihlásí. Doposud byly změněny TechnicalProfiles toku pouze místní účty.  Pokud nový atribut se požaduje toku účet sociálního/federovaný, je potřeba změnit jinou sadu TechnicalProfiles. Najdete v části Další kroky.

```xml
<!-- The following technical profile is used to read data after user authenticates. -->
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
         <!-- Optional claims -->
         <OutputClaim ClaimTypeReferenceId="signInNames.emailAddress" />
         <OutputClaim ClaimTypeReferenceId="displayName" />
         <OutputClaim ClaimTypeReferenceId="otherMails" />
         <OutputClaim ClaimTypeReferenceId="givenName" />
         <OutputClaim ClaimTypeReferenceId="surname" />
         <OutputClaim ClaimTypeReferenceId="extension_loyaltyId" />
       </OutputClaims>
       <IncludeTechnicalProfile ReferenceId="AAD-Common" />
     </TechnicalProfile>
```


>[!IMPORTANT]
>IncludeTechnicalProfile element přidá všechny elementy AAD běžné tento TechnicalProfile.

## <a name="test-the-custom-policy-using-run-now"></a>Otestovat vlastní zásady pomocí "Spustit nyní"
1. Otevřete **okno Azure AD B2C** a přejděte do **Identity rozhraní Framework > vlastní zásady**.
1. Vyberte vlastní zásady, který jste nahráli a klikněte na **spustit nyní** tlačítko.
1. Nyní byste měli mít zaregistrovat pomocí e-mailovou adresu.

Id token odeslána zpět do vaší aplikace obsahuje nové vlastnosti rozšíření jako vlastních deklarací identity extension_loyaltyId před sebou. Podívejte se na příklad.

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

### <a name="add-the-new-claim-to-the-flows-for-social-account-logins-by-changing-the-technicalprofiles-listed-below-these-two-technicalprofiles-are-used-by-socialfederated-account-logins-to-write-and-read-the-user-data-using-the-alternativesecurityid-as-the-locator-of-the-user-object"></a>Přidejte novou deklaraci na toky pro sociálních účet přihlášení změnou TechnicalProfiles uvedené níže. Tyto dvě TechnicalProfiles jsou používány sociálního/federovaný účet přihlášení k zápisu a čtení dat uživatele pomocí alternativeSecurityId jako Lokátor objektu uživatele.
```xml
  <TechnicalProfile Id="AAD-UserWriteUsingAlternativeSecurityId">

  <TechnicalProfile Id="AAD-UserReadUsingAlternativeSecurityId">
```

Pomocí stejné atributy rozšíření mezi předdefinované a vlastní zásady.
Když přidáte rozšíření atributy (neboli vlastní atributy) prostřednictvím portálu prostředí, jsou tyto atributy registrovat pomocí ** b2c-rozšíření aplikaci, která existuje v každé klienta b2c.  Tyto atributy rozšíření použít vlastní zásady:
1. V rámci svého klienta b2c na stránce portal.azure.com, přejděte na **Azure Active Directory** a vyberte **registrace aplikace**
2. Najít váš **aplikace b2c rozšíření** a vyberte ho
3. V části "Essentials, záznam **ID aplikace** a **ID objektu**
4. Zahrnout do AAD běžné technické metadata profil jako následujícím způsobem:

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

Zachovat konzistenci s práce s portálem, vytvořte tyto atributy pomocí uživatelského rozhraní portálu *před* je použijete v vlastní zásady.  Při vytváření atributu "ActivationStatus" na portálu můžete musí odkazovat na ho následujícím způsobem:

```
extension_ActivationStatus in the custom policy
extension_<app-guid>_ActivationStatus via the Graph API.
```


## <a name="reference"></a>Referenční informace

* A **technické profilu (TP)** je typ elementu, který lze považovat za *funkce* který definuje název koncového bodu, jeho metadata, používá protokol a podrobnosti exchange deklarací identity, které by měl provést rozhraní prostředí Identity.  Pokud to *funkce* je volána v kroku orchestration nebo z jiného TechnicalProfile, InputClaims a OutputClaims jsou poskytovány jako parametry volající.


* Úplné zpracování u rozšíření vlastností, najdete v článku [rozšíření schématu adresáře | KONCEPTY GRAPH API](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-directory-schema-extensions)

>[!NOTE]
>Rozšíření atributů v rozhraní Graph API jsou pojmenované pomocí konvence `extension_ApplicationObjectID_attributename`. Vlastní zásady, které se odkazují na rozšíření atributy jako extension_attributename, proto vynechání ApplicationObjectId v souboru XML
