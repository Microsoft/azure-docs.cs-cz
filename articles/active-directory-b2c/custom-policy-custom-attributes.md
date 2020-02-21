---
title: Přidání vlastních atributů do vlastních zásad
titleSuffix: Azure AD B2C
description: Návod k používání vlastností rozšíření a vlastních atributů a jejich zahrnutí v uživatelském rozhraní.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/04/2017
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: c2361192a612cfd92003b1e3c36e85dbbd9090bb
ms.sourcegitcommit: 98a5a6765da081e7f294d3cb19c1357d10ca333f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/20/2020
ms.locfileid: "77482764"
---
# <a name="azure-active-directory-b2c-use-custom-attributes-in-a-custom-profile-edit-policy"></a>Azure Active Directory B2C: použití vlastních atributů v zásadách úprav vlastního profilu

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

V tomto článku vytvoříte vlastní atribut v adresáři Azure Active Directory B2C (Azure AD B2C). Tento nový atribut použijete jako vlastní deklaraci v profilu upravit cestu uživatele.

## <a name="prerequisites"></a>Požadavky

Postupujte podle kroků v článku [Azure Active Directory B2C: Začínáme s vlastními zásadami](custom-policy-get-started.md).

## <a name="use-custom-attributes-to-collect-information-about-your-customers-in-azure-ad-b2c-by-using-custom-policies"></a>Použití vlastních atributů ke shromáždění informací o zákaznících v Azure AD B2C pomocí vlastních zásad
Adresář Azure AD B2C obsahuje integrovanou sadu atributů. Příklady jsou **křestní jméno**, **příjmení**, **město**, **poštovní směrovací číslo**a **userPrincipalName**. Často je třeba vytvořit vlastní atributy, jako jsou tyto příklady:
* Zákaznická aplikace musí být zachována pro atribut, jako je **LoyaltyNumber.**
* Zprostředkovatel identity má jedinečný identifikátor uživatele, jako je **uniqueUserGUID** , který se musí uložit.
* Vlastní cesta uživatele musí uchovávat stav uživatele, jako je **migrationStatus**.

Azure AD B2C rozšiřuje sadu atributů uložených v každém uživatelském účtu. Tyto atributy můžete také číst a zapisovat pomocí [rozhraní Microsoft Graph API](manage-user-accounts-graph-api.md).

Vlastnosti rozšíření rozšíření schématu objektů uživatele v adresáři. *Vlastnost rozšíření*podmínek, *vlastní atribut*a *vlastní deklarace identity* odkazují na stejnou věc v kontextu tohoto článku. Název se liší v závislosti na kontextu, jako je například aplikace, objekt nebo zásada.

Vlastnosti rozšíření se dají registrovat jenom u objektu aplikace, i když můžou obsahovat data pro uživatele. Vlastnost je připojena k aplikaci. Aby bylo možné zaregistrovat vlastnost rozšíření, objekt aplikace musí mít oprávnění k zápisu. Stovky vlastností rozšíření napříč všemi typy a všemi aplikacemi lze zapsat do libovolného jednoho objektu. Vlastnosti rozšíření se přidají do cílového typu adresáře a hned se zpřístupní v tenantovi Azure AD B2C Directory.
Pokud se aplikace odstraní, odeberou se také tyto vlastnosti rozšíření spolu s daty, která jsou v nich obsažená pro všechny uživatele. Pokud je vlastnost rozšíření odstraněna aplikací, je odebrána v cílových objektech adresáře a hodnoty jsou odstraněny.

Vlastnosti rozšíření existují pouze v kontextu registrované aplikace v tenantovi. ID objektu aplikace musí být součástí **TechnicalProfile** , který ho používá.

>[!NOTE]
>Azure AD B2C adresář obvykle obsahuje webovou aplikaci s názvem `b2c-extensions-app`. Tato aplikace se primárně používá v předdefinovaných zásadách B2C pro vlastní deklarace vytvořené prostřednictvím Azure Portal. Pro vlastní zásady B2C doporučujeme, abyste pomocí této aplikace zaregistrovali jenom pokročilé uživatele.
Pokyny jsou uvedené v části **Další kroky** v tomto článku.

## <a name="create-a-new-application-to-store-the-extension-properties"></a>Vytvoření nové aplikace pro uložení vlastností rozšíření

1. Otevřete relaci procházení a přejděte na [Azure Portal](https://portal.azure.com). Přihlaste se pomocí přihlašovacích údajů správce adresáře B2C, který chcete konfigurovat.
2. V levé navigační nabídce vyberte **Azure Active Directory** . Možná je budete muset najít výběrem možnosti **Další služby**.
3. Vyberte **Registrace aplikací**. Vyberte **Registrace nové aplikace**.
4. Zadejte následující položky:
    * Název webové aplikace: **WebApp-GraphAPI-DirectoryExtensions**.
    * Typ aplikace: **Webová aplikace/rozhraní API**.
    * Přihlašovací adresa URL: **https://{tenant}. Microsoft. com/webapp-GraphAPI-DirectoryExtensions**.
5. Vyberte **Create** (Vytvořit).
6. Vyberte nově vytvořenou webovou aplikaci.
7. Vyberte **nastavení** > **požadovaná oprávnění**.
8. Vyberte Azure Active Directory rozhraní API pro **Windows**.
9. Zaškrtněte políčko oprávnění aplikace: **číst a zapsat data adresáře**. Potom vyberte **Uložit**.
10. Klikněte na **udělit oprávnění** a potvrďte **Ano**.
11. Zkopírujte do schránky následující identifikátory a uložte je:
    * **ID aplikace** Příklad: `103ee0e6-f92d-4183-b576-8c3739027780`.
    * **ID objektu** Příklad: `80d8296a-da0a-49ee-b6ab-fd232aa45201`.

## <a name="modify-your-custom-policy-to-add-the-applicationobjectid"></a>Upravte vlastní zásadu a přidejte tak **ApplicationObjectId** .

Když jste postupovali podle kroků v části [Azure Active Directory B2C: Začínáme s vlastními zásadami](custom-policy-get-started.md), stáhli a upravili jste [ukázkové soubory](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/archive/master.zip) s názvem **TrustFrameworkBase. XML**, **TrustFrameworkExtensions. XML**, **SignUpOrSignin. XML**, **ProfileEdit. XML**a **PasswordReset. XML**. V tomto kroku provedete další úpravy těchto souborů.

* Otevřete soubor **TrustFrameworkBase. XML** a přidejte část `Metadata`, jak je znázorněno v následujícím příkladu. Vložte ID objektu, které jste dříve nahráli pro `ApplicationObjectId` hodnotu, a ID aplikace, které jste si poznamenali pro `ClientId` hodnotu:

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
> Když **TechnicalProfile** zapisuje poprvé do nově vytvořené vlastnosti rozšíření, může dojít k jednorázové chybě. Vlastnost extension je vytvořená při prvním použití.

## <a name="use-the-new-extension-property-or-custom-attribute-in-a-user-journey"></a>Použití nové vlastnosti rozšíření nebo vlastního atributu v cestě uživatele

1. Otevřete soubor **ProfileEdit. XML** .
2. Přidejte `loyaltyId`vlastní deklarace identity. Zahrnutím vlastní deklarace do prvku `<RelyingParty>` je zahrnuto do tokenu pro aplikaci.

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

3. Otevřete soubor **TrustFrameworkExtensions. XML** a přidejte`<ClaimsSchema>` element a jeho podřízené prvky do elementu `BuildingBlocks`:

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

4. Přidejte stejnou definici `ClaimType` do **souboru TrustFrameworkBase. XML**. Není nutné přidávat definici `ClaimType` v souborech Base i Extensions. Další kroky však přidají `extension_loyaltyId` do **TechnicalProfiles** v základním souboru. Proto validátor zásad odmítne nahrávání základního souboru bez něj. Může být užitečné sledovat spuštění cesty uživatele s názvem **ProfileEdit** v souboru **TrustFrameworkBase. XML** . V editoru vyhledejte cestu uživatele se stejným názvem. Všimněte si, že orchestrace krok 5 vyvolá **TechnicalProfileReferenceID = "SelfAsserted-ProfileUpdate**. Vyhledejte tento **TechnicalProfile** a prozkoumejte ho, abyste se seznámili s tokem.

5. Otevřete soubor **TrustFrameworkBase. XML** a přidejte `loyaltyId` jako vstupní a výstupní deklaraci do **TechnicalProfile SelfAsserted-ProfileUpdate**:

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

6. V souboru **TrustFrameworkBase. XML** přidejte deklaraci `loyaltyId` do **TechnicalProfile AAD-UserWriteProfileUsingObjectId**. Toto sčítání uchovává hodnotu deklarace identity ve vlastnosti Extension pro aktuálního uživatele v adresáři:

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

7. V souboru **TrustFrameworkBase. XML** přidejte `loyaltyId` DEKLARACÍ **TechnicalProfile AAD-UserReadUsingObjectId** , abyste si přečetli hodnotu atributu Extension při každém přihlášení uživatele. **TechnicalProfiles** se zatím změnila pouze v toku místních účtů. Pokud chcete nový atribut v toku pro sociální nebo federovaný účet, je nutné změnit jinou sadu **TechnicalProfiles** . Podívejte se na část **Další kroky** .

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

## <a name="test-the-custom-policy"></a>Testování vlastních zásad

1. Otevřete okno Azure AD B2C a přejděte na **rozhraní identity Experience Framework** > **vlastní zásady**.
1. Vyberte vlastní zásady, které jste nahráli. Vyberte **Spustit nyní**.
1. Zaregistrujte se pomocí e-mailové adresy.

Token ID odeslaný zpět do vaší aplikace zahrnuje novou vlastnost rozšíření jako vlastní deklaraci předchází **extension_loyaltyId**. Prohlédněte si následující příklad:

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

## <a name="next-steps"></a>Další kroky

1. Přidejte novou deklaraci identity do toků, abyste se mohli přihlásit k účtům sociálních sítí změnou následujících **TechnicalProfiles**. Sociální a federované účty používají tyto dvě **TechnicalProfiles** k přihlášení. Zapisují a čtou data uživatelů pomocí **alternativeSecurityId** jako lokátoru objektu uživatele.

   ```xml
    <TechnicalProfile Id="AAD-UserWriteUsingAlternativeSecurityId">

    <TechnicalProfile Id="AAD-UserReadUsingAlternativeSecurityId">
   ```

2. Používejte stejné atributy rozšíření mezi integrovanými a vlastními zásadami. Když přidáváte rozšíření nebo vlastní atributy prostřednictvím prostředí portálu, tyto atributy se zaregistrují pomocí **aplikace B2C-Extensions-App** , která existuje v každém tenantovi B2C. Pomocí následujících kroků můžete ve vlastních zásadách používat atributy rozšíření:

   a. V rámci tenanta B2C v portal.azure.com přejděte na **Azure Active Directory** a vyberte **Registrace aplikací**.
   b. Najděte **aplikaci B2C-Extensions-App** a vyberte ji.
   c. V části **základy**zadejte **ID aplikace** a **ID objektu**.
   d. Zahrňte je do metadat služby **AAD – Common** TechnicalProfile:

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

3. Zůstaňte v souladu s prostředím portálu. Tyto atributy vytvořte pomocí uživatelského rozhraní portálu, abyste je mohli použít ve vlastních zásadách. Když vytvoříte atribut **ActivationStatus** na portálu, je nutné na něj odkazovat následujícím způsobem:

   ```
   extension_ActivationStatus in the custom policy.
   extension_<app-guid>_ActivationStatus via Graph API.
   ```

## <a name="reference"></a>Referenční informace

Další informace o vlastnostech rozšíření najdete v článku [Přidání vlastních dat do prostředků pomocí rozšíření](https://docs.microsoft.com/graph/extensibility-overview).

> [!NOTE]
> * **TechnicalProfile** je typ prvku nebo funkce, který definuje název, metadata a protokol koncového bodu. **TechnicalProfile** podrobně popisuje výměnu deklarací identity, které architektura prostředí identit provádí. Pokud je tato funkce volána v kroku orchestrace nebo z jiného **TechnicalProfile**, jsou **InputClaims** a **OutputClaims** zadány jako parametry volajícího.
> * Atributy rozšíření v Graph API jsou pojmenovány pomocí `extension_ApplicationObjectID_attributename`konvence.
> * Vlastní zásady odkazují na atributy rozšíření jako **extension_attributename**. Tento odkaz vynechá **ApplicationObjectId** v XML.
> * Je nutné zadat ID atributu v následujícím formátu **extension_attributename** bez ohledu na to, kam je odkazováno.
