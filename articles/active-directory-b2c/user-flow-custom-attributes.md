---
title: Definování vlastních atributů v Azure Active Directory B2C | Microsoft Docs
description: Definujte vlastní atributy vaší aplikace v Azure Active Directory B2C, abyste mohli shromažďovat informace o vašich zákaznících.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/10/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 17c73257db371bbec0c72a23b1303847a8d14102
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/10/2021
ms.locfileid: "102607913"
---
# <a name="define-custom-attributes-in-azure-active-directory-b2c"></a>Definování vlastních atributů v Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

V článku [Přidání deklarací identity a přizpůsobení uživatelského vstupu pomocí vlastních zásad](configure-user-input.md) se dozvíte, jak používat předdefinované [atributy profilů uživatelů](user-profile-attributes.md). V tomto článku povolíte vlastní atribut v adresáři Azure Active Directory B2C (Azure AD B2C). Později můžete použít nový atribut jako vlastní deklaraci identity v [uživatelských tocích](user-flow-overview.md) nebo ve [vlastních zásadách](custom-policy-get-started.md) současně.

Součástí vašeho adresáře Azure AD B2C je [předdefinovaná sada atributů](user-profile-attributes.md). Často je však potřeba vytvořit vlastní atributy pro správu konkrétního scénáře, například když:

* Zákaznická aplikace musí zachovat atribut **LoyaltyId** .
* Zprostředkovatel identity má jedinečný identifikátor uživatele **uniqueUserGUID**, který musí být trvale uložený.
* Vlastní cesta uživatele musí zachovat stav uživatele ( **migrationStatus**) pro další logiku, na které se má pracovat.

*Vlastnost rozšíření* podmínek, *vlastní atribut* a *vlastní deklarace identity* odkazují na stejnou věc v kontextu tohoto článku. Název se liší v závislosti na kontextu, jako je například aplikace, objekt nebo zásada.

Azure AD B2C umožňuje zvětšit sadu atributů uložených v každém uživatelském účtu. Tyto atributy můžete také číst a zapisovat pomocí [rozhraní Microsoft Graph API](microsoft-graph-operations.md).

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="create-a-custom-attribute"></a>Vytvoření vlastního atributu

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/) jako globální správce vašeho tenanta Azure AD B2C.
1. Přepněte v pravém horním rohu portálu Azure Portal na adresář, který obsahuje tenanta Azure AD B2C, a ujistěte se tak, že používáte správný adresář. Vyberte informace o předplatném a pak **Přepnout adresář**.
1. Zvolte **Všechny služby** v levém horním rohu portálu Azure Portal a vyhledejte a vyberte **Azure AD B2C**.
1. Vyberte **atributy uživatele** a pak vyberte **Přidat**.
1. Zadejte **název** vlastního atributu (například "ShoeSize").
1. Vyberte **datový typ**. K dispozici je pouze **řetězec**, **logická hodnota** a **int** .
1. Volitelně můžete zadat **Popis** pro informativní účely.
1. Klikněte na **Vytvořit**.

Vlastní atribut je teď k dispozici v seznamu **atributů uživatele** a pro použití ve vašich uživatelských tocích. Vlastní atribut je vytvořen pouze při prvním použití v uživatelském toku, a ne při jeho přidání do seznamu **atributů uživatele**.

::: zone pivot="b2c-user-flow"

## <a name="use-a-custom-attribute-in-your-user-flow"></a>Použití vlastního atributu v toku uživatele

1. Ve vašem tenantovi Azure AD B2C vyberte **toky uživatelů**.
1. Vyberte zásadu (například "B2C_1_SignupSignin") a otevřete ji.
1. Vyberte **atributy uživatele** a pak vyberte vlastní atribut (například "ShoeSize"). Klikněte na **Uložit**.
1. Vyberte **deklarace identity aplikace** a pak vyberte vlastní atribut.
1. Klikněte na **Uložit**.

Jakmile vytvoříte nového uživatele pomocí toku uživatele, který používá nově vytvořený vlastní atribut, může se objekt dotazovat v [Microsoft Graph Exploreru](https://developer.microsoft.com/graph/graph-explorer). Případně můžete pomocí funkce [tok](./tutorial-create-user-flows.md) uživatele v toku uživatelů ověřit činnost zákazníka. Teď byste měli vidět **ShoeSize** v seznamu atributů shromážděných během cesty pro registraci a zobrazit je v tokenu, který se pošle zpátky do vaší aplikace.

::: zone-end

## <a name="azure-ad-b2c-extensions-app"></a>Aplikace rozšíření Azure AD B2C

Atributy rozšíření se dají registrovat jenom u objektu aplikace, i když můžou obsahovat data pro uživatele. Atribut Extension je připojen k aplikaci s názvem `b2c-extensions-app` . Neupravujte tuto aplikaci, protože ji používá Azure AD B2C k ukládání uživatelských dat. Tuto aplikaci můžete najít v části Azure AD B2C, registrace aplikací. Získat vlastnosti aplikace:

1. Přihlaste se na [Azure Portal](https://portal.azure.com).
1. V horní nabídce vyberte filtr **adresář + odběr** a potom vyberte adresář, který obsahuje vašeho tenanta Azure AD B2C.
1. V nabídce vlevo vyberte **Azure AD B2C**. Případně vyberte **všechny služby** a vyhledejte a vyberte **Azure AD B2C**.
1. Vyberte **Registrace aplikací** a pak vyberte **všechny aplikace**.
1. Vyberte aplikaci `b2c-extensions-app. Do not modify. Used by AADB2C for storing user data.`.
1. Zkopírujte do schránky následující identifikátory a uložte je:
    * **ID aplikace:** Příklad: `11111111-1111-1111-1111-111111111111`.
    * **ID objektu** Příklad: `22222222-2222-2222-2222-222222222222`.

::: zone pivot="b2c-custom-policy"

## <a name="modify-your-custom-policy"></a>Úprava vlastních zásad

Pokud chcete v zásadách povolit vlastní atributy, zadejte **ID aplikace** a **ID objektu** aplikace v metadatech AAD-Common Technical Profile. Technický profil *AAD-Common* se nachází v základním profilu [Azure Active Directory](active-directory-technical-profile.md) Technical a poskytuje podporu pro správu uživatelů Azure AD. Další technické profily Azure AD zahrnují AAD-Common pro využití jeho konfigurace. Přepsat AAD-Common technický profil v souboru rozšíření.

1. Otevřete soubor rozšíření vaší zásady. Například <em>`SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`**</em> .
1. Vyhledejte element ClaimsProviders. Přidejte nový ClaimsProvider do elementu ClaimsProviders.
1. Vložte **ID aplikace** , které jste dříve nahráli, mezi otevírací `<Item Key="ClientId">` a ukončovací `</Item>` prvky.
1. Vložte **identifikátor objektu aplikace** , který jste nahráli dříve, mezi otevírací `<Item Key="ApplicationObjectId">` a ukončovací `</Item>` prvky.

    ```xml
    <!-- 
    <ClaimsProviders> -->
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
    <!-- 
    </ClaimsProviders> -->
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

Tyto atributy můžete vytvořit pomocí uživatelského rozhraní portálu před nebo po jejich použití ve vlastních zásadách. Když vytvoříte atribut **loyaltyId** na portálu, je nutné na něj odkazovat následujícím způsobem:

|Name     |Použito v |
|---------|---------|
|`extension_loyaltyId`  | Vlastní zásady|
|`extension_<b2c-extensions-app-guid>_loyaltyId`  | [Microsoft Graph API](microsoft-graph-operations.md)|

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

::: zone-end

## <a name="using-custom-attribute-with-ms-graph-api"></a>Použití vlastního atributu s MS Graph API

Rozhraní Microsoft Graph API podporuje vytváření a aktualizaci uživatele s atributy rozšíření. Atributy rozšíření v Graph API jsou pojmenovány pomocí konvence `extension_ApplicationClientID_attributename` , kde `ApplicationClientID` je **ID aplikace (klienta)** `b2c-extensions-app` aplikace. Všimněte si, že **ID aplikace (klienta)** , jak je znázorněno v názvu atributu rozšíření, neobsahuje spojovníky. Například:

```json
"extension_831374b3bd5041bfaa54263ec9e050fc_loyaltyId": "212342" 
``` 

## <a name="next-steps"></a>Další kroky

Postupujte podle pokynů pro [Přidání deklarací identity a přizpůsobení uživatelského vstupu pomocí vlastních zásad](configure-user-input.md). V této ukázce se používá integrovaná deklarace identity "City". Chcete-li použít vlastní atribut, nahraďte ' City ' vlastními atributy.
