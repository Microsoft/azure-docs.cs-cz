---
title: Přidání vlastních atributů do vlastních zásad
titleSuffix: Azure AD B2C
description: Návod k používání vlastností rozšíření a vlastních atributů a jejich zahrnutí v uživatelském rozhraní.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/17/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 9ec99558f3a168b770ad19fb4f6c811a31c44f08
ms.sourcegitcommit: 6172a6ae13d7062a0a5e00ff411fd363b5c38597
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/11/2020
ms.locfileid: "97108872"
---
# <a name="azure-active-directory-b2c-enable-custom-attributes-in-a-custom-profile-policy"></a>Azure Active Directory B2C: povolení vlastních atributů v zásadách vlastního profilu

V článku [Přidání deklarací identity a přizpůsobení uživatelského vstupu pomocí vlastních zásad](configure-user-input.md) se dozvíte, jak používat předdefinované [atributy profilů uživatelů](user-profile-attributes.md). V tomto článku povolíte vlastní atribut v adresáři Azure Active Directory B2C (Azure AD B2C). Později můžete použít nový atribut jako vlastní deklaraci identity v [uživatelských tocích](user-flow-overview.md) nebo ve [vlastních zásadách](custom-policy-get-started.md) současně.

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

## <a name="prerequisites"></a>Předpoklady

Postupujte podle kroků v článku [Azure Active Directory B2C: Začínáme s vlastními zásadami](custom-policy-get-started.md).

## <a name="use-custom-attributes-to-collect-information-about-your-customers"></a>Použití vlastních atributů ke shromažďování informací o zákaznících 

Adresář Azure AD B2C obsahuje [integrovanou sadu atributů](user-profile-attributes.md). Často je však potřeba vytvořit vlastní atributy pro správu konkrétního scénáře, například když:

* Zákaznická aplikace musí zachovat atribut **LoyaltyId** .
* Zprostředkovatel identity má jedinečný identifikátor uživatele **uniqueUserGUID**, který musí být trvale uložený.
* Vlastní cesta uživatele musí zachovat stav uživatele ( **migrationStatus**) pro další logiku, na které se má pracovat.

Azure AD B2C umožňuje zvětšit sadu atributů uložených v každém uživatelském účtu. Tyto atributy můžete také číst a zapisovat pomocí [rozhraní Microsoft Graph API](manage-user-accounts-graph-api.md).

## <a name="azure-ad-b2c-extensions-app"></a>Aplikace rozšíření Azure AD B2C

Atributy rozšíření se dají registrovat jenom u objektu aplikace, i když můžou obsahovat data pro uživatele. Atribut Extension je připojen k aplikaci s názvem B2C-Extensions-App. Neupravujte tuto aplikaci, protože ji používá Azure AD B2C k ukládání uživatelských dat. Tuto aplikaci můžete najít v části Azure AD B2C, registrace aplikací.

*Vlastnost rozšíření* podmínek, *vlastní atribut* a *vlastní deklarace identity* odkazují na stejnou věc v kontextu tohoto článku. Název se liší v závislosti na kontextu, jako je například aplikace, objekt nebo zásada.

## <a name="get-the-application-properties"></a>Získat vlastnosti aplikace

1. Přihlaste se na [Azure Portal](https://portal.azure.com).
1. V horní nabídce vyberte filtr **adresář + odběr** a potom vyberte adresář, který obsahuje vašeho tenanta Azure AD B2C.
1. V nabídce vlevo vyberte **Azure AD B2C**. Případně vyberte **všechny služby** a vyhledejte a vyberte **Azure AD B2C**.
1. Vyberte **Registrace aplikací** a pak vyberte **všechny aplikace**.
1. Vyberte aplikaci `b2c-extensions-app. Do not modify. Used by AADB2C for storing user data.`.
1. Zkopírujte do schránky následující identifikátory a uložte je:
    * **ID aplikace:** Příklad: `11111111-1111-1111-1111-111111111111`.
    * **ID objektu** Příklad: `22222222-2222-2222-2222-222222222222`.

## <a name="modify-your-custom-policy"></a>Úprava vlastních zásad

Pokud chcete v zásadách povolit vlastní atributy, zadejte **ID aplikace** a **ID objektu** aplikace v metadatech AAD-Common Technical Profile. Technický profil *AAD-Common* se nachází v základním profilu [Azure Active Directory](active-directory-technical-profile.md) Technical a poskytuje podporu pro správu uživatelů Azure AD. Další technické profily Azure AD zahrnují AAD-Common pro využití jeho konfigurace. Přepsat AAD-Common technický profil v souboru rozšíření.

1. Otevřete soubor rozšíření vaší zásady. Například <em>`SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`**</em> .
1. Vyhledejte element ClaimsProviders. Přidejte nový ClaimsProvider do elementu ClaimsProviders.
1. Nahraďte `ApplicationObjectId` ID objektu, který jste předtím nahráli. Potom nahraďte `ClientId` ID aplikace, které jste předtím nahráli v níže uvedeném fragmentu kódu.

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
      </TechnicalProfiles> 
    </ClaimsProvider>
    ```

## <a name="upload-your-custom-policy"></a>Nahrání vlastních zásad

1. Přihlaste se na [Azure Portal](https://portal.azure.com).
2. Ujistěte se, že používáte adresář, který obsahuje vašeho tenanta Azure AD, a to tak, že v horní nabídce vyberete adresář a filtr **předplatného** a zvolíte adresář, který obsahuje vašeho tenanta Azure AD B2C.
3. V levém horním rohu Azure Portal vyberte **všechny služby** a pak vyhledejte a vyberte **Registrace aplikací**.
4. Vyberte **architekturu prostředí identity**.
5. Vyberte **Odeslat vlastní zásadu** a pak nahrajte soubory zásad TrustFrameworkExtensions.xml, které jste změnili.

> [!NOTE]
> Když technický profil služby Azure AD uchovává deklaraci identity do adresáře poprvé, zkontroluje, jestli existuje vlastní atribut. Pokud ne, vytvoří vlastní atribut.  

## <a name="create-a-custom-attribute-through-azure-portal"></a>Vytvoření vlastního atributu pomocí Azure Portal

Mezi integrovanými a vlastními zásadami se sdílí stejné atributy rozšíření. Když přidáte vlastní atributy prostřednictvím prostředí portálu, tyto atributy se zaregistrují pomocí **aplikace B2C-Extensions-App** , která existuje v každém tenantovi B2C.

Tyto atributy můžete vytvořit pomocí uživatelského rozhraní portálu před nebo po jejich použití ve vlastních zásadách. Postupujte podle pokynů pro [definování vlastních atributů v Azure Active Directory B2C](user-flow-custom-attributes.md). Když vytvoříte atribut **loyaltyId** na portálu, je nutné na něj odkazovat následujícím způsobem:

|Název     |Použito v |
|---------|---------|
|`extension_loyaltyId`  | Vlastní zásady|
|`extension_<b2c-extensions-app-guid>_loyaltyId`  | [Microsoft Graph API](manage-user-accounts-graph-api.md)|

Následující příklad demonstruje použití vlastních atributů v definici deklarace identity vlastní zásady Azure AD B2C.

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

Následující příklad ukazuje použití vlastního atributu v Azure AD B2C vlastní zásady v technickém profilu, vstupních, výstupech a trvalých deklaracích.

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

Postupujte podle pokynů pro [Přidání deklarací identity a přizpůsobení uživatelského vstupu pomocí vlastních zásad](configure-user-input.md). V této ukázce se používá integrovaná deklarace identity "City". Chcete-li použít vlastní atribut, nahraďte ' City ' vlastními atributy.


## <a name="next-steps"></a>Další kroky

Přečtěte si další informace:

- [Azure AD B2C atributů profilu uživatele](user-profile-attributes.md)
- [Definice atributů rozšíření](user-profile-attributes.md#extension-attributes)
- [Správa Azure AD B2C uživatelských účtů pomocí Microsoft Graph](manage-user-accounts-graph-api.md)
