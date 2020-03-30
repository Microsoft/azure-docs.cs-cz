---
title: Přidání deklarací identity a přizpůsobení vstupu uživatele ve vlastních zásadách
titleSuffix: Azure AD B2C
description: Zjistěte, jak přizpůsobit vstup uživatele a přidat deklarace identity na cestu registrace nebo přihlášení ve službě Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/17/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 85f2ab6f8c3e5edda027e44eeda13a3279a88321
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79473672"
---
#  <a name="add-claims-and-customize-user-input-using-custom-policies-in-azure-active-directory-b2c"></a>Přidání deklarací identity a přizpůsobení vstupu uživatele pomocí vlastních zásad ve službě Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

V tomto článku shromažďovat nový atribut během cesty registrace v Azure Active Directory B2C (Azure AD B2C). Získáte město uživatelů, nakonfigurujete ho jako rozevírací soubor a určíte, zda je nutné ho poskytnout.

> [!NOTE]
> Tato ukázka používá vestavěnou deklaraci "město". Místo toho můžete zvolit jeden z podporovaných [předdefinovaných atributů Azure AD B2C](user-profile-attributes.md) nebo vlastní atribut. Chcete-li použít vlastní atribut, [povolte vlastní atributy v zásadách](custom-policy-custom-attributes.md). Chcete-li použít jiný předdefinovaný nebo vlastní atribut, nahraďte "město" atributem, který si vyberete, například předdefinovaný atribut *jobTitle* nebo vlastní atribut, jako *je extension_loyaltyId*.  

Počáteční data od uživatelů můžete shromažďovat pomocí cesty uživatele registrace nebo přihlášení. Další deklarace identity lze shromáždit později pomocí cesty uživatele pro úpravu profilu. Kdykoli v azure ad b2c shromažďuje informace přímo od uživatele interaktivně, rozhraní Identity Experience Framework používá jeho [vlastní uplatněný technický profil](self-asserted-technical-profile.md). V této ukázce:

1. Definujte deklaraci "město". 
1. Zeptejte se uživatele na jejich město.
1. Zachovat město na profil uživatele v adresáři Azure AD B2C.
1. Přečtěte si deklarace města z adresáře Azure AD B2C při každém přihlášení.
1. Po přihlášení nebo registraci vraťte město do aplikace předávající strany.  

## <a name="prerequisites"></a>Požadavky

Proveďte kroky v [části Začínáme s vlastními zásadami](custom-policy-get-started.md). Měli byste mít funkční vlastní zásady pro registraci a přihlášení pomocí sociálních a místních účtů.

## <a name="define-a-claim"></a>Definování deklarace

Deklarace poskytuje dočasné úložiště dat během provádění zásad Azure AD B2C. [Schéma nároků](claimsschema.md) je místo, kde deklarujete své nároky. K definování deklarace se používají následující prvky:

- **DisplayName** - řetězec, který definuje popisek směřující uživatelem.
- [DataType](claimsschema.md#datatype) - Typ deklarace.
- **UserHelpText** - Pomáhá uživateli pochopit, co je požadováno.
- [UserInputType](claimsschema.md#userinputtype) - Typ vstupního ovládacího prvku, například textové pole, výběr rádia, rozevírací seznam nebo více výběrů.

Otevřete soubor přípon zásad. Například <em> `SocialAndLocalAccounts/` </em>.

1. Vyhledejte element [BuildingBlocks.](buildingblocks.md) Pokud prvek neexistuje, přidejte jej.
1. Vyhledejte [ClaimsSchema](claimsschema.md) element. Pokud prvek neexistuje, přidejte jej.
1. Přidejte deklaraci města do prvku **ClaimsSchema.**  

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

## <a name="add-a-claim-to-the-user-interface"></a>Přidání deklarace deklarace do uživatelského rozhraní

Následující technické profily jsou [vlastní-tvrdil , vyvolána,](self-asserted-technical-profile.md)když se očekává, že uživatel poskytnout vstup:

- **LocalAccountSignUpWithWithLogonEmail** - Tok registrace místního účtu.
- **SelfAsserted-Social** – přihlašování k prvnímu uživateli federovaného účtu.
- **SelfAsserted-ProfileUpdate** - Upravit tok profilu.

Pro vyzvednutí nároku města během registrace musí být přidán jako `LocalAccountSignUpWithLogonEmail` výstupní nárok na technický profil. Přepište tento technický profil v souboru rozšíření. Zadejte celý seznam výstupních deklarací pro řízení pořadí, ve které jsou deklarace uvedeny na obrazovce. Najít **ClaimsProviders** element. Přidejte nové ClaimsProviders takto:

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

Chcete-li získat nárok na město po počátečním přihlášení pomocí federovaného účtu, musí být přidán jako výstupní nárok do technického `SelfAsserted-Social` profilu. Aby mohli uživatelé místního a federovaného účtu později upravovat `SelfAsserted-ProfileUpdate` data svého profilu, přidejte výstupní deklaraci do technického profilu. Přepište tyto technické profily v souboru rozšíření. Zadejte celý seznam výstupních deklarací pro řízení pořadí deklarací identity, které jsou uvedeny na obrazovce. Najít **ClaimsProviders** element. Přidejte nové ClaimsProviders takto:

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

## <a name="read-and-write-a-claim"></a>Čtení a napsání nároku

Následující technické profily jsou [technické profily služby Active Directory](active-directory-technical-profile.md), které přijímají a zapisují data do služby Azure Active Directory.  
Slouží `PersistedClaims` k zápisu dat `OutputClaims` do uživatelského profilu a ke čtení dat z profilu uživatele v rámci příslušných technických profilů služby Active Directory.

Přepište tyto technické profily v souboru rozšíření. Najít **ClaimsProviders** element.  Přidejte nové ClaimsProviders takto:

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

## <a name="include-a-claim-in-the-token"></a>Zahrnout deklaraci do tokenu 

Chcete-li vrátit nárok města zpět do aplikace předávající <em> `SocialAndLocalAccounts/` </em> strany, přidejte výstupní deklaraci do souboru. Výstupní deklarace bude přidána do tokenu po úspěšné cestě uživatele a bude odeslána do aplikace. Upravte prvek technického profilu v části předávající strany a přidejte město jako výstupní deklaraci.
 
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

1. Přihlaste se k [portálu Azure](https://portal.azure.com).
2. Ujistěte se, že používáte adresář, který obsahuje vašeho klienta Azure AD výběrem filtru **directory + předplatné** v horní nabídce a výběrem adresáře, který obsahuje vašeho klienta Azure AD.
3. V levém horním rohu portálu Azure zvolte **Všechny služby** a pak **vyhledejte**a vyberte registrace aplikací .
4. Vyberte **rozhraní Identity Experience Framework**.
5. Vyberte **Nahrát vlastní zásady**a potom nahrajte dva soubory zásad, které jste změnili.
2. Vyberte zásadu registrace nebo přihlášení, kterou jste nahráli, a klikněte na tlačítko **Spustit.**
3. Měli byste být schopni se zaregistrovat pomocí e-mailové adresy.

Přihlašovací obrazovka by měla vypadat podobně jako na následujícím snímku obrazovky:

![Snímek obrazovky s možností upravené registrace](./media/custom-policy-configure-user-input/signup-with-city-claim-dropdown-example.png)

Token odeslaný zpět do `city` vaší aplikace obsahuje deklaraci.

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

- Další informace o [ClaimsSchema](claimsschema.md) prvek v odkazu IEF.
- Přečtěte si, jak [používat vlastní atributy ve vlastních zásadách pro úpravy profilu](custom-policy-custom-attributes.md).
