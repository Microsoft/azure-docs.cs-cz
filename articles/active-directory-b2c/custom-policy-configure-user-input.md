---
title: Přidání deklarací identity a přizpůsobení uživatelského vstupu ve vlastních zásadách
titleSuffix: Azure AD B2C
description: Přečtěte si, jak přizpůsobit uživatelský vstup a přidat deklarace do cesty pro registraci nebo přihlašování v Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/17/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 47fdf445fa11693dd3a998b8c73ac0c3ed8452a8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "85389356"
---
#  <a name="add-claims-and-customize-user-input-using-custom-policies-in-azure-active-directory-b2c"></a>Přidání deklarací identity a přizpůsobení uživatelského vstupu pomocí vlastních zásad v Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

V tomto článku shromáždíte nový atribut během cesty pro registraci v Azure Active Directory B2C (Azure AD B2C). Získáte město uživatele, nakonfigurujete ho jako rozevírací seznam a definujete, jestli je potřeba poskytnout.

> [!NOTE]
> V této ukázce se používá integrovaná deklarace identity "City". Místo toho můžete zvolit jeden z podporovaných [předdefinovaných atributů Azure AD B2C](user-profile-attributes.md) nebo vlastní atribut. Pokud chcete použít vlastní atribut, [Povolte v zásadách vlastní atributy](custom-policy-custom-attributes.md). Chcete-li použít jiný vestavěný nebo vlastní atribut, nahraďte ' City ' atributem dle vašeho výběru, například vestavěný atribut *jobtitle* nebo vlastní atribut jako *extension_loyaltyId*.  

Počáteční data můžete shromažďovat od uživatelů pomocí cesty uživatelů při registraci nebo přihlašování. Další deklarace identity je možné shromažďovat později pomocí profilu upravit cestu uživatele. Kdykoli Azure AD B2C shromažďuje informace přímo od uživatele interaktivně, rozhraní identity Experience Framework používá svůj [technický profil s vlastním uplatněním](self-asserted-technical-profile.md). V této ukázce:

1. Definujte deklaraci identity City. 
1. Požádejte uživatele o své město.
1. Pochovejte město od města do profilu uživatele v adresáři Azure AD B2C.
1. Při každém přihlášení si přečtěte deklaraci města z Azure AD B2C adresáře.
1. Až se přihlásíte nebo se přihlásíte, vraťte město do aplikace předávající strany.  

## <a name="prerequisites"></a>Požadavky

Proveďte kroky v části Začínáme [s vlastními zásadami](custom-policy-get-started.md). Měli byste mít pracovní vlastní zásady pro registraci a přihlášení pomocí sociálních a místních účtů.

## <a name="define-a-claim"></a>Definování deklarace identity

Deklarace identity poskytuje dočasné úložiště dat během provádění zásad Azure AD B2C. [Schéma deklarací identity](claimsschema.md) je místo, kde deklarujete deklarace identity. K definování deklarace identity se používají tyto prvky:

- **DisplayName** – řetězec definující popisek pro uživatele.
- [DataType](claimsschema.md#datatype) – typ deklarace identity.
- **UserHelpText** – pomáhá uživateli pochopit, co je potřeba.
- [UserInputType](claimsschema.md#userinputtype) – typ ovládacího prvku vstupu, jako je textové pole, přepínač výběr, rozevírací seznam nebo vícenásobný výběr.

Otevřete soubor rozšíření vaší zásady. Například <em>`SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`**</em> .

1. Vyhledejte element [BuildingBlocks](buildingblocks.md) . Pokud element neexistuje, přidejte jej.
1. Vyhledejte element [ClaimsSchema](claimsschema.md) . Pokud element neexistuje, přidejte jej.
1. Přidejte deklaraci identity města do elementu **ClaimsSchema** .  

```xml
<ClaimType Id="city">
  <DisplayName>City where you work</DisplayName>
  <DataType>string</DataType>
  <UserInputType>DropdownSingleSelect</UserInputType>
  <Restriction>
    <Enumeration Text="Bellevue" Value="bellevue" SelectByDefault="false" />
    <Enumeration Text="Redmond" Value="redmond" SelectByDefault="false" />
    <Enumeration Text="Kirkland" Value="kirkland" SelectByDefault="false" />
  </Restriction>
</ClaimType>
```

## <a name="add-a-claim-to-the-user-interface"></a>Přidání deklarace identity do uživatelského rozhraní

Následující technické profily jsou vyvolány [vlastním](self-asserted-technical-profile.md)příznakem, vyvolány, když uživatel očekává zadání vstupu:

- **LocalAccountSignUpWithLogonEmail** – tok registrace místního účtu.
- **SelfAsserted –** účet federovaného uživatele při prvním přihlášení.
- **SelfAsserted-ProfileUpdate** – úprava toku profilu.

Chcete-li shromáždit deklaraci identity města během registrace, je nutné ji přidat jako výstupní deklaraci do `LocalAccountSignUpWithLogonEmail` technického profilu. Přepište tento technický profil v souboru rozšíření. Zadejte celý seznam výstupních deklarací identity, abyste mohli řídit pořadí, na kterém jsou deklarace identity zobrazené na obrazovce. Vyhledejte element **ClaimsProviders** . Přidejte nový ClaimsProviders následujícím způsobem:

```xml
<ClaimsProvider>
  <DisplayName>Local Account</DisplayName>
  <TechnicalProfiles>
    <!--Local account sign-up page-->
    <TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
      <OutputClaims>
       <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="Verified.Email" Required="true" />
       <OutputClaim ClaimTypeReferenceId="newPassword" Required="true" />
       <OutputClaim ClaimTypeReferenceId="reenterPassword" Required="true" />
       <OutputClaim ClaimTypeReferenceId="displayName" />
       <OutputClaim ClaimTypeReferenceId="givenName" />
       <OutputClaim ClaimTypeReferenceId="surName" />
       <OutputClaim ClaimTypeReferenceId="city"/>
     </OutputClaims>
   </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
<ClaimsProvider>
```

Pokud chcete po počátečním přihlášení ke federovanému účtu shromáždit deklaraci města, musíte ji přidat jako výstupní deklaraci do `SelfAsserted-Social` technického profilu. Aby mohli uživatelé s místními a federovaným účty později upravovat svoje data profilu, přidejte do technického profilu výstupní deklaraci identity `SelfAsserted-ProfileUpdate` . Tyto technické profily popište v souboru rozšíření. Zadejte celý seznam výstupních deklarací identity pro řízení pořadí deklarací identity na obrazovce. Vyhledejte element **ClaimsProviders** . Přidejte nový ClaimsProviders následujícím způsobem:

```xml
  <DisplayName>Self Asserted</DisplayName>
  <TechnicalProfiles>
    <!--Federated account first-time sign-in page-->
    <TechnicalProfile Id="SelfAsserted-Social">
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="displayName"/>
        <OutputClaim ClaimTypeReferenceId="givenName"/>
        <OutputClaim ClaimTypeReferenceId="surname"/>
        <OutputClaim ClaimTypeReferenceId="city"/>
      </OutputClaims>
    </TechnicalProfile>
    <!--Edit profile page-->
    <TechnicalProfile Id="SelfAsserted-ProfileUpdate">
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="displayName"/>
        <OutputClaim ClaimTypeReferenceId="givenName" />
        <OutputClaim ClaimTypeReferenceId="surname" />
        <OutputClaim ClaimTypeReferenceId="city"/>
      </OutputClaims>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="read-and-write-a-claim"></a>Čtení a zápis deklarace identity

Následující technické profily jsou [technické profily služby Active Directory](active-directory-technical-profile.md), které čtou a zapisují data do Azure Active Directory.  
Slouží `PersistedClaims` k zápisu dat do profilu uživatele a `OutputClaims` ke čtení dat z profilu uživatele v příslušných technických profilech služby Active Directory.

Tyto technické profily popište v souboru rozšíření. Vyhledejte element **ClaimsProviders** .  Přidejte nový ClaimsProviders následujícím způsobem:

```xml
<ClaimsProvider>
  <DisplayName>Azure Active Directory</DisplayName>
  <TechnicalProfiles>
    <!-- Write data during a local account sign-up flow. -->
    <TechnicalProfile Id="AAD-UserWriteUsingLogonEmail">
      <PersistedClaims>
        <PersistedClaim ClaimTypeReferenceId="city"/>
      </PersistedClaims>
    </TechnicalProfile>
    <!-- Write data during a federated account first-time sign-in flow. -->
    <TechnicalProfile Id="AAD-UserWriteUsingAlternativeSecurityId">
      <PersistedClaims>
        <PersistedClaim ClaimTypeReferenceId="city"/>
      </PersistedClaims>
    </TechnicalProfile>
    <!-- Write data during edit profile flow. -->
    <TechnicalProfile Id="AAD-UserWriteProfileUsingObjectId">
      <PersistedClaims>
        <PersistedClaim ClaimTypeReferenceId="city"/>
      </PersistedClaims>
    </TechnicalProfile>
    <!-- Read data after user authenticates with a local account. -->
    <TechnicalProfile Id="AAD-UserReadUsingEmailAddress">
      <OutputClaims>  
        <OutputClaim ClaimTypeReferenceId="city" />
      </OutputClaims>
    </TechnicalProfile>
    <!-- Read data after user authenticates with a federated account. -->
    <TechnicalProfile Id="AAD-UserReadUsingObjectId">
      <OutputClaims>  
        <OutputClaim ClaimTypeReferenceId="city" />
      </OutputClaims>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="include-a-claim-in-the-token"></a>Zahrnutí deklarace identity do tokenu 

Pokud chcete vrátit deklaraci města zpět do aplikace předávající strany, přidejte do souboru výstupní deklaraci identity <em>`SocialAndLocalAccounts/`**`SignUpOrSignIn.xml`**</em> . Po úspěšné cestě uživatele se do tokenu přidá výstupní deklarace identity a pošle se do aplikace. Upravte element Technical Profile v části předávající strany a přidejte město jako výstupní deklaraci identity.
 
```xml
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
  <TechnicalProfile Id="PolicyProfile">
    <DisplayName>PolicyProfile</DisplayName>
    <Protocol Name="OpenIdConnect" />
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="displayName" />
      <OutputClaim ClaimTypeReferenceId="givenName" />
      <OutputClaim ClaimTypeReferenceId="surname" />
      <OutputClaim ClaimTypeReferenceId="email" />
      <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
      <OutputClaim ClaimTypeReferenceId="identityProvider" />
      <OutputClaim ClaimTypeReferenceId="tenantId" AlwaysUseDefaultValue="true" DefaultValue="{Policy:TenantObjectId}" />
      <OutputClaim ClaimTypeReferenceId="city" DefaultValue="" />
    </OutputClaims>
    <SubjectNamingInfo ClaimType="sub" />
  </TechnicalProfile>
</RelyingParty>
```

## <a name="test-the-custom-policy"></a>Testování vlastních zásad

1. Přihlaste se k [portálu Azure Portal](https://portal.azure.com).
2. Ujistěte se, že používáte adresář, který obsahuje vašeho tenanta Azure AD, a to tak, že v horní nabídce vyberete adresář a filtr **předplatného** a zvolíte adresář, který obsahuje vašeho TENANTA Azure AD.
3. V levém horním rohu Azure Portal vyberte **všechny služby** a pak vyhledejte a vyberte **Registrace aplikací**.
4. Vyberte **architekturu prostředí identity**.
5. Vyberte **Odeslat vlastní zásadu**a pak nahrajte dva soubory zásad, které jste změnili.
2. Vyberte zásadu registrace nebo přihlašování, kterou jste nahráli, a klikněte na tlačítko **Spustit** .
3. Měli byste být schopni se zaregistrovat pomocí e-mailové adresy.

Přihlašovací obrazovka by měla vypadat podobně jako na následujícím snímku obrazovky:

![Snímek obrazovky s upravenou možností registrace](./media/custom-policy-configure-user-input/signup-with-city-claim-dropdown-example.png)

Token, který se odesílá zpátky do vaší aplikace, zahrnuje `city` deklaraci identity.

```json
{
  "typ": "JWT",
  "alg": "RS256",
  "kid": "X5eXk4xyojNFum1kl2Ytv8dlNP4-c57dO6QGTVBwaNk"
}.{
  "exp": 1583500140,
  "nbf": 1583496540,
  "ver": "1.0",
  "iss": "https://contoso.b2clogin.com/f06c2fe8-709f-4030-85dc-38a4bfd9e82d/v2.0/",
  "aud": "e1d2612f-c2bc-4599-8e7b-d874eaca1ee1",
  "acr": "b2c_1a_signup_signin",
  "nonce": "defaultNonce",
  "iat": 1583496540,
  "auth_time": 1583496540,
  "name": "Emily Smith",
  "email": "joe@outlook.com",
  "given_name": "Emily",
  "family_name": "Smith",
  "city": "Bellevue"
  ...
}
```

## <a name="next-steps"></a>Další kroky

- Další informace o elementu [ClaimsSchema](claimsschema.md) najdete v referenčních informacích k IEF.
- Naučte se [používat vlastní atributy v zásadách úprav vlastního profilu](custom-policy-custom-attributes.md).
