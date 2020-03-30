---
title: Přidání vlastních atributů do vlastních zásad
titleSuffix: Azure AD B2C
description: Návod k použití vlastností rozšíření a vlastní atributy a jejich zahrnutí v uživatelském rozhraní.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/17/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: bc5204518cb6e801ba661aecd5498a501122225f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79473655"
---
# <a name="azure-active-directory-b2c-enable-custom-attributes-in-a-custom-profile-policy"></a>Azure Active Directory B2C: Povolení vlastních atributů ve vlastních zásadách profilu

V článku [Přidat deklarace identity a přizpůsobit vstup uživatele pomocí vlastních zásad](custom-policy-configure-user-input.md) se dozvíte, jak používat vestavěné [atributy profilu uživatele](user-profile-attributes.md). V tomto článku povolíte vlastní atribut v adresáři Azure Active Directory B2C (Azure AD B2C). Později můžete použít nový atribut jako vlastní deklaraci v [uživatelských tocích](user-flow-overview.md) nebo [vlastních zásadách](custom-policy-get-started.md) současně.

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

## <a name="prerequisites"></a>Požadavky

Postupujte podle kroků v článku [Azure Active Directory B2C: Začínáme s vlastními zásadami](custom-policy-get-started.md).

## <a name="use-custom-attributes-to-collect-information-about-your-customers"></a>Shromažďování informací o zákaznících pomocí vlastních atributů 

Váš adresář Azure AD B2C je dodáván s [integrovanou sadou atributů](user-profile-attributes.md). Často však potřebujete vytvořit vlastní atributy pro správu konkrétního scénáře, například když:

* Aplikace pro zákazníka musí zachovat atribut **LoyaltyId.**
* Zprostředkovatel identity má jedinečný identifikátor uživatele, **uniqueUserGUID**, které musí být trvalé.
* Vlastní cesta uživatele musí zachovat stav uživatele **migrationStatus**, pro jiné logiky pracovat.

Azure AD B2C umožňuje rozšířit sadu atributů uložených v každém uživatelském účtu. Tyto atributy můžete také číst a zapisovat pomocí [rozhraní Microsoft Graph API](manage-user-accounts-graph-api.md).

## <a name="azure-ad-b2c-extensions-app"></a>Aplikace rozšíření Azure AD B2C

Atributy rozšíření lze zaregistrovat pouze na objekt aplikace, i když mohou obsahovat data pro uživatele. Atribut rozšíření je připojen k aplikaci s názvem b2c-extensions-app. Tuto aplikaci neupravujte, protože ji používá Azure AD B2C pro ukládání uživatelských dat. Tuto aplikaci najdete v části Azure AD B2C, registrace aplikací.

Vlastnost *rozšíření*termínů , *vlastní atribut*a *vlastní deklarace odkazují* na totéž v kontextu tohoto článku. Název se liší v závislosti na kontextu, jako je například aplikace, objekt nebo zásady.

## <a name="get-the-application-properties"></a>Získání vlastností aplikace

1. Přihlaste se k [portálu Azure](https://portal.azure.com).
1. V horní nabídce vyberte filtr **Directory + subscription** a pak vyberte adresář, který obsahuje vašeho klienta Azure AD B2C.
1. V levé nabídce vyberte **Azure AD B2C**. Nebo vyberte **Všechny služby** a vyhledejte a vyberte **Azure AD B2C**.
1. Vyberte **Registrace aplikací (preview)** a pak vyberte **Všechny aplikace**.
1. Vyberte `b2c-extensions-app. Do not modify. Used by AADB2C for storing user data.` aplikaci.
1. Zkopírujte do schránky následující identifikátory a uložte je:
    * **ID aplikace**. Příklad: `11111111-1111-1111-1111-111111111111`.
    * **ID objektu**. Příklad: `22222222-2222-2222-2222-222222222222`.

## <a name="modify-your-custom-policy"></a>Úprava vlastních zásad

Chcete-li povolit vlastní atributy v zásadách, zadejte **ID aplikace** a **ID aplikačního objektu** v metadatech technického profilu AAD-Common. Technický profil *AAD Common* se nachází v základním technickém profilu [služby Azure Active Directory](active-directory-technical-profile.md) a poskytuje podporu pro správu uživatelů Azure AD. Další technické profily Azure AD patří AAD-Společné využít jeho konfiguraci. Přepište technický profil AAD-Common v souboru rozšíření.

1. Otevřete soubor přípon zásad. Například <em> `SocialAndLocalAccounts/` </em>.
1. Najít ClaimsProviders element. Přidejte nového zprostředkovatele deklarací identity do prvku ClaimsProviders.
1. Nahraďte `ApplicationObjectId` id objektu, který jste dříve zaznamenali. Poté `ClientId` je nahraďte ID aplikace, které jste dříve zaznamenali v níže uvedeném fragmentu.

    ```xml
    <ClaimsProvider>
      <DisplayName>Azure Active Directory</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="AAD-Common">
          <Metadata>
            <!--Insert b2c-extensions-app application ID here, for example: 11111111-1111-1111-1111-111111111111-->  
            <Item Key="ClientId"></Item>
            <!--Insert b2c-extensions-app application ObjectId here, for example: 22222222-2222-2222-2222-222222222222-->
            <Item Key="ApplicationObjectId"></Item>
          </Metadata>
        </TechnicalProfile>
      <TechnicalProfiles> 
    </ClaimsProvider>
    ```

## <a name="upload-your-custom-policy"></a>Nahrání vlastních zásad

1. Přihlaste se k [portálu Azure](https://portal.azure.com).
2. Ujistěte se, že používáte adresář, který obsahuje vašeho klienta Azure AD výběrem directory **+ předplatné** filtr v horní nabídce a výběrem adresáře, který obsahuje vašeho klienta Azure AD B2C.
3. V levém horním rohu portálu Azure zvolte **Všechny služby** a pak **vyhledejte**a vyberte registrace aplikací .
4. Vyberte **rozhraní Identity Experience Framework**.
5. Vyberte **Nahrát vlastní zásady**a potom nahrajte soubory zásad TrustFrameworkExtensions.xml, které jste změnili.

> [!NOTE]
> Při prvním technickém profilu Azure AD zachová deklarace deklarace adresáře, zkontroluje, zda vlastní atribut existuje. Pokud ne, vytvoří vlastní atribut.  

## <a name="create-a-custom-attribute-through-azure-portal"></a>Vytvoření vlastního atributu prostřednictvím portálu Azure

Stejné atributy rozšíření jsou sdíleny mezi předdefinované a vlastní zásady. Při přidání vlastní atributy prostřednictvím prostředí portálu, tyto atributy jsou registrovány pomocí **b2c rozšíření aplikace,** která existuje v každém tenantovi B2C.

Tyto atributy můžete vytvořit pomocí portálu ui před nebo po jejich použití ve vlastních zásadách. Postupujte podle pokynů, jak [definovat vlastní atributy ve službě Azure Active Directory B2C](user-flow-custom-attributes.md). Při vytváření **atributu loyaltyId** na portálu, musíte odkazovat na něj takto:

|Name (Název)     |Používá se v |
|---------|---------|
|`extension_loyaltyId`  | Vlastní zásady|
|`extension_<b2c-extensions-app-guid>_loyaltyId`  | [Microsoft Graph API](manage-user-accounts-graph-api.md)|

Následující příklad ukazuje použití vlastních atributů v definici deklarace vlastní zásady Azure AD B2C.

```xml
<BuildingBlocks>
  <ClaimsSchema>
    <ClaimType Id="extension_loyaltyId">
      <DisplayName>Loyalty Identification</DisplayName>
      <DataType>string</DataType>
      <UserHelpText>Your loyalty number from your membership card</UserHelpText>
      <UserInputType>TextBox</UserInputType>
    </ClaimType>
  </ClaimsSchema>
</BuildingBlocks>
```

Následující příklad ukazuje použití vlastního atributu ve vlastní chodazure AD B2C v technickém profilu, vstupu, výstupu a trvalých deklaracích identity.

```xml
<InputClaims>
  <InputClaim ClaimTypeReferenceId="extension_loyaltyId"  />
</InputClaims>
<PersistedClaims>
  <PersistedClaim ClaimTypeReferenceId="extension_loyaltyId" />
</PersistedClaims>
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="extension_loyaltyId" />
</OutputClaims>
```

## <a name="use-a-custom-attribute-in-a-policy"></a>Použití vlastního atributu v zásadách

Postupujte podle pokynů pro [přidávání deklarací identity a přizpůsobení vstupu uživatele pomocí vlastních zásad](custom-policy-configure-user-input.md). Tato ukázka používá vestavěnou deklaraci "město". Chcete-li použít vlastní atribut, nahraďte "město" vlastními atributy.


## <a name="next-steps"></a>Další kroky

Další informace:

- [Atributy profilu uživatele Azure AD B2C](user-profile-attributes.md)
- [Definice atributů rozšíření](user-profile-attributes.md#extension-attributes)
- [Správa uživatelských účtů Azure AD B2C pomocí Microsoft Graphu](manage-user-accounts-graph-api.md)
