---
title: Nastavení toku resetování hesla v Azure AD B2C
titleSuffix: Azure AD B2C
description: Přečtěte si, jak nastavit tok resetování hesla v Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/08/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 8b3224333a3915b7827242004d1dec0f4695f479
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "104581911"
---
# <a name="set-up-a-password-reset-flow-in-azure-active-directory-b2c"></a>Nastavení toku resetování hesla v Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

## <a name="password-reset-flow"></a>Tok resetování hesla

[Cesta pro registraci a přihlašování](add-sign-up-and-sign-in-policy.md) umožňuje uživatelům resetovat vlastní heslo pomocí odkazu **zapomenuté heslo?** . Tok resetování hesla zahrnuje následující kroky:

1. Na stránce registrace a přihlášení uživatel klikne na odkaz **zapomenuté heslo?** . Azure AD B2C iniciuje tok resetování hesla. 
2. Uživatel poskytne a ověří svou e-mailovou adresu s časovým limitem jednorázového hesla.
3. Uživatel pak může zadat nové heslo.

![Tok resetování hesla](./media/add-password-reset-policy/password-reset-flow.png)

Tok resetování hesla se vztahuje na místní účty v Azure AD B2C, které používají [e-mailovou adresu](identity-provider-local.md#email-sign-in) nebo [uživatelské jméno](identity-provider-local.md#username-sign-in) s heslem pro přihlášení.

Běžný postup po migraci uživatelů na Azure AD B2C s náhodnými hesly spočívá v tom, že uživatelé ověřují své e-mailové adresy a resetují hesla během jejich prvního přihlášení. Je také běžné vynutit, aby uživatel resetoval heslo poté, co správce změní heslo; Pokud chcete tuto funkci povolit, přečtěte si téma [vynucení resetování hesla](force-password-reset.md) .

## <a name="prerequisites"></a>Předpoklady

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="self-service-password-reset-recommended"></a>Samoobslužné resetování hesla (doporučeno)

Nové prostředí pro resetování hesla je teď součástí zásady registrace nebo přihlašování. Když uživatel vybere odkaz **zapomenuté heslo?** , okamžitě se pošle k zapomenutému heslu. Vaše aplikace už nepotřebuje zpracovávat [kód chyby AADB2C90118](#password-reset-policy-legacy)a nepotřebujete samostatnou zásadu pro resetování hesla.

::: zone pivot="b2c-user-flow"

Prostředí samoobslužného resetování hesla je možné nakonfigurovat pro **přihlašování (doporučeno)** nebo si **zaregistrovat a přihlásit se (doporučeno)** toky uživatelů. Pokud takový tok uživatele nemáte, vytvořte [přihlášení a zaregistrujte](add-sign-up-and-sign-in-policy.md) si uživatelský tok. 

Povolení samoobslužného resetování hesla pro uživatelský tok registrace nebo přihlašování:

1. Přihlaste se na [Azure Portal](https://portal.azure.com).
1. Na panelu nástrojů na portálu vyberte ikonu **adresář + předplatné** a pak vyberte adresář, který obsahuje vašeho tenanta Azure AD B2C.
1. V Azure Portal vyhledejte a vyberte **Azure AD B2C**.
1. Vyberte **toky uživatelů**.
1. Vyberte uživatelský tok pro registraci nebo přihlašování ( **doporučený** typ), který chcete přizpůsobit.
1. V části **Nastavení** v levé nabídce vyberte **vlastnosti**.
1. V části **složitost hesla** vyberte **Samoobslužné resetování hesla**.
1. Vyberte **Uložit**.
1. V části **přizpůsobit** v nabídce vlevo vyberte **rozložení stránky**.
1. Ve **verzi rozložení stránky** vyberte **2.1.2 – aktuální** nebo vyšší.
1. Vyberte **Uložit**.

::: zone-end

::: zone pivot="b2c-custom-policy"

V následujících částech najdete popis postupu přidání samoobslužného hesla pro vlastní zásadu. Ukázka je založena na souborech zásad obsažených v [úvodním balíčku Custom Policy](./custom-policy-get-started.md). 

> [!TIP]
> Úplnou ukázku zásad registrace nebo přihlášení pomocí resetování hesla najdete v tématu o službě [GitHub](https://github.com/azure-ad-b2c/samples/tree/master/policies/embedded-password-reset).

### <a name="indicate-a-user-selected-the-forgot-your-password-link"></a>Označte uživateli možnost zapomenuté heslo? propojit

Chcete-li určit zásadu, kterou uživatel vybral jako odkaz **zapomenuté heslo?** , definujte logickou deklaraci identity. Tato deklarace se použije k nasměrování cesty uživatele na technický profil zapomenutého hesla. Tato deklarace může být také vystavena tokenu, takže aplikace je vědoma, že uživatel byl přihlášený prostřednictvím toku zapomenutého hesla.

Deklarace identity deklarujete ve [schématu deklarací identity](claimsschema.md). Otevřete soubor rozšíření vaší zásady. Například <em>`SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`**</em> .

1. Vyhledejte element [BuildingBlocks](buildingblocks.md) . Pokud element neexistuje, přidejte jej.
1. Vyhledejte element [ClaimsSchema](claimsschema.md) . Pokud element neexistuje, přidejte jej.
1. Do prvku **ClaimsSchema** přidejte následující deklaraci identity. 

```XML
<!-- 
<BuildingBlocks>
  <ClaimsSchema> -->
    <ClaimType Id="isForgotPassword">
      <DisplayName>isForgotPassword</DisplayName>
      <DataType>boolean</DataType>
      <AdminHelpText>Whether the user has selected Forgot your Password</AdminHelpText>
    </ClaimType>
  <!--
  </ClaimsSchema>
</BuildingBlocks> -->
```

### <a name="upgrade-the-page-layout-version"></a>Upgrade verze rozložení stránky

[Verze](contentdefinitions.md#migrating-to-page-layout) `2.1.2` rozložení stránky vyžaduje se k povolení toku samoobslužného resetování hesla v rámci registrace nebo přihlašovací cesty.

1. Vyhledejte element [BuildingBlocks](buildingblocks.md) . Pokud element neexistuje, přidejte jej.
1. Vyhledejte element [ContentDefinitions](contentdefinitions.md) . Pokud element neexistuje, přidejte jej.
1. Upravte element **DataURI** v rámci elementu **ContentDefinition** s ID **API. signuporsignin** , jak je znázorněno níže.

```xml
<!-- 
<BuildingBlocks>
  <ContentDefinitions> -->
    <ContentDefinition Id="api.signuporsignin">
      <DataUri>urn:com:microsoft:aad:b2c:elements:contract:unifiedssp:2.1.2</DataUri>
    </ContentDefinition>
  <!-- 
  </ContentDefinitions>
</BuildingBlocks> -->
```

K zahájení `isForgotPassword` deklarace identity se použije technický profil transformace deklarací identity. Na tento technický profil se bude odkazovat později. Při vyvolání se nastaví hodnota `isForgotPassword` deklarace na `true` . Vyhledejte `ClaimsProviders` element. Pokud element neexistuje, přidejte jej. Pak přidejte následujícího zprostředkovatele deklarací identity:  

```xml
<!-- 
<ClaimsProviders> -->
  <ClaimsProvider>
    <DisplayName>Local Account</DisplayName>
    <TechnicalProfiles>
      <TechnicalProfile Id="ForgotPassword">
        <DisplayName>Forgot your password?</DisplayName>
        <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.ClaimsTransformationProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null"/>
        <OutputClaims>
          <OutputClaim ClaimTypeReferenceId="isForgotPassword" DefaultValue="true" AlwaysUseDefaultValue="true"/>
        </OutputClaims>
      </TechnicalProfile>
      <TechnicalProfile Id="SelfAsserted-LocalAccountSignin-Email">
        <Metadata>
          <Item Key="setting.forgotPasswordLinkOverride">ForgotPasswordExchange</Item>
        </Metadata>
      </TechnicalProfile>
    </TechnicalProfiles>
  </ClaimsProvider>
<!-- 
</ClaimsProviders> -->
```

`SelfAsserted-LocalAccountSignin-Email`Technický profil `setting.forgotPasswordLinkOverride` dejemnější výměnu deklarací resetování hesla, které se mají provést na cestě uživatele. 

### <a name="add-the-password-reset-sub-journey"></a>Přidejte podřízenou cestu resetování hesla.

Vaše cesta teď bude obsahovat možnosti, jak se uživatel přihlásí, zaregistrovat se a provede resetování hesla. Aby bylo možné lépe uspořádat cestu uživatele, lze použít [dílčí cestu](subjourneys.md) pro zpracování toku resetování hesla.

Podřízená cesta bude volána z cesty uživatele a provede konkrétní postup, který uživateli doručí prostředí pro resetování hesla. Použijte `Call` dílčí cestu typu tak, aby po dokončení dílčí cesty byl ovládací prvek vrácen do kroku orchestrace, který inicioval dílčí cestu.

Vyhledejte `SubJourneys` element. Pokud element neexistuje, přidejte jej za `User Journeys` element. Pak přidejte následující podřízenou cestu:

```xml
<!--
<SubJourneys>-->
  <SubJourney Id="PasswordReset" Type="Call">
    <OrchestrationSteps>
      <!-- Validate user's email address. -->
      <OrchestrationStep Order="1" Type="ClaimsExchange">
        <ClaimsExchanges>
          <ClaimsExchange Id="PasswordResetUsingEmailAddressExchange" TechnicalProfileReferenceId="LocalAccountDiscoveryUsingEmailAddress" />
        </ClaimsExchanges>
      </OrchestrationStep>

      <!-- Collect and persist a new password. -->
      <OrchestrationStep Order="2" Type="ClaimsExchange">
        <ClaimsExchanges>
          <ClaimsExchange Id="NewCredentials" TechnicalProfileReferenceId="LocalAccountWritePasswordUsingObjectId" />
        </ClaimsExchanges>
      </OrchestrationStep>
    </OrchestrationSteps>
  </SubJourney>
<!--
</SubJourneys>-->
```

### <a name="prepare-your-user-journey"></a>Příprava cesty uživatele

Budete muset připojit **zapomenuté heslo?** odkaz na podřízenou cestu k zapomenutému heslu. Provedete to tak, že zadáte ID dílčí cesty k zapomenutému heslu v rámci elementu **claimsproviderselection.** kroku **CombinedSignInAndSignUp** .

Pokud nemáte vlastní cestu uživatele k **CombinedSignInAndSignUp** kroku, pomocí následujícího postupu duplikujte stávající cestu uživatele pro registraci nebo přihlášení. V opačném případě pokračujte k další části.

1. Otevřete soubor *TrustFrameworkBase.xml* z úvodní sady.
2. Vyhledejte a zkopírujte celý obsah prvku **UserJourney** , který obsahuje `Id="SignUpOrSignIn"` .
3. Otevřete *TrustFrameworkExtensions.xml* a vyhledejte element **userjourney** . Pokud element neexistuje, přidejte jej.
4. Vytvořte podřízený element elementu **userjourney** vložením celého obsahu elementu **UserJourney** , který jste zkopírovali v kroku 2.
5. Přejmenujte ID cesty pro uživatele. Například, `Id="CustomSignUpSignIn"`.

### <a name="connect-the-forgot-password-link-to-the-forgot-password-sub-journey"></a>Připojte odkaz zapomenutého hesla k dílčí cestě zapomenutého hesla. 

Na cestě uživatele můžete vyjádřit cestu k zapomenutému heslu jako **claimsproviderselection.**. Přidání tohoto elementu připojí **zapomenuté heslo?** odkaz na podcestu zapomenutého hesla.

1. Na cestě uživatele Najděte element kroku orchestrace, který obsahuje `Type="CombinedSignInAndSignUp"` nebo `Type="ClaimsProviderSelection"` . Obvykle je to první krok orchestrace. Element **ClaimsProviderSelections** obsahuje seznam zprostředkovatelů identity, které může uživatel použít k přihlášení. Přidejte následující řádek:
    
    ```xml
    <ClaimsProviderSelection TargetClaimsExchangeId="ForgotPasswordExchange" />
    ```

1. V dalším kroku orchestrace přidejte element **ClaimsExchange** . Přidejte následující řádek:

    ```xml
    <ClaimsExchange Id="ForgotPasswordExchange" TechnicalProfileReferenceId="ForgotPassword" />
    ```
    
1. Přidejte následující krok orchestrace mezi aktuální krok a další krok. Nový krok orchestrace, který přidáte, zkontroluje, jestli `isForgotPassword` existuje deklarace identity. Pokud tato deklarace identity existuje, vyvolá [podřízenou cestu resetování hesla](#add-the-password-reset-sub-journey). 

    ```xml
    <OrchestrationStep Order="3" Type="InvokeSubJourney">
      <Preconditions>
        <Precondition Type="ClaimsExist" ExecuteActionsIf="false">
          <Value>isForgotPassword</Value>
          <Action>SkipThisOrchestrationStep</Action>
        </Precondition>
      </Preconditions>
      <JourneyList>
        <Candidate SubJourneyReferenceId="PasswordReset" />
      </JourneyList>
    </OrchestrationStep>
    ```
    
1. Po přidání nového kroku orchestrace znovu načíslování kroků bez přeskakování všech celých čísel od 1 do N.

### <a name="set-the-user-journey-to-be-executed"></a>Nastavte cestu uživatele, která se má spustit.

Teď, když jste změnili nebo vytvořili cestu k uživateli, v části **předávající strana** Určete cestu, kterou Azure AD B2C spustí pro tuto vlastní zásadu. V rámci elementu [RelyingParty](relyingparty.md) Najděte element **DefaultUserJourney** . Aktualizujte  **DefaultUserJourney ReferenceId** tak, aby odpovídala ID cesty uživatele, do které jste přidali **ClaimsProviderSelections**.

```xml
<RelyingParty>
  <DefaultUserJourney ReferenceId="CustomSignUpSignIn" />
  ...
</RelyingParty>
```

### <a name="indicate-the-forgot-password-flow-to-your-app"></a>Označuje tok zapomenutého hesla k vaší aplikaci.

Vaše aplikace může potřebovat zjistit, jestli se uživatel přihlásil prostřednictvím toku uživatelů zapomenutého hesla. Deklarace **isForgotPassword** obsahuje logickou hodnotu, která označuje, že se dá vydat v tokenu odeslaném do aplikace. V případě potřeby přidejte `isForgotPassword` do výstupních deklarací v části **předávající strany** . Vaše aplikace může ověřit `isForgotPassword` deklaraci identity a zjistit, jestli uživatel resetuje heslo.

```xml
<RelyingParty>
  <OutputClaims>
    ...
    <OutputClaim ClaimTypeReferenceId="isForgotPassword" DefaultValue="false" />
  </OutputClaims>
</RelyingParty>
```


### <a name="upload-the-custom-policy"></a>Nahrajte vlastní zásadu.

1. Přihlaste se na [Azure Portal](https://portal.azure.com).
1. Na panelu nástrojů na portálu vyberte ikonu **adresář + předplatné** a pak vyberte adresář, který obsahuje vašeho tenanta Azure AD B2C.
1. V Azure Portal vyhledejte a vyberte **Azure AD B2C**.
1. V části **zásady** vyberte **Architektura prostředí identity**.
1. Vyberte **nahrát vlastní zásadu** a pak nahrajte dva soubory zásad, které jste změnili v následujícím pořadí:
   1. Zásada rozšíření, například `TrustFrameworkExtensions.xml` .
   2. Například zásady předávající strany `SignUpSignIn.xml` .

::: zone-end

### <a name="test-the-password-reset-flow"></a>Testování toku resetování hesla

1. Vyberte uživatelský tok pro registraci nebo přihlašování (doporučený typ), který chcete otestovat.
1. Vyberte **Spustit tok uživatele**.
1. V poli **aplikace** vyberte webovou aplikaci s názvem *WebApp1* , kterou jste předtím zaregistrovali. Měla by se zobrazit **Adresa URL odpovědi** `https://jwt.ms` .
1. Vyberte **Spustit tok uživatele**.
1. Na stránce registrace nebo přihlášení vyberte **zapomenuté heslo?**.
1. Ověřte e-mailovou adresu účtu, který jste vytvořili dříve, a pak vyberte **pokračovat**.
1. Teď máte možnost změnit heslo pro uživatele. Změňte heslo a vyberte **pokračovat**. Token se vrátí do `https://jwt.ms` a měl by se vám zobrazovat.
1. Ověřte hodnotu deklarace vráceného tokenu `isForgotPassword` . Pokud existuje a je nastavené na hodnotu true, znamená to, že uživatel obnovil heslo.

## <a name="password-reset-policy-legacy"></a>Zásada pro resetování hesla (starší verze)

Pokud není prostředí [samoobslužného resetování hesla](#self-service-password-reset-recommended) povolené, kliknutím na tento odkaz se automaticky nespustí tok uživatele resetování hesla. Místo toho se kód chyby `AADB2C90118` vrátí do vaší aplikace. Vaše aplikace potřebuje zpracovat tento kód chyby tím, že znovu inicializuje knihovnu ověřování a ověří Azure AD B2C tok uživatele resetování hesla.

V následujícím diagramu:

1. Z aplikace uživatel klikne na přihlášení. Aplikace zahájí žádost o autorizaci a převezme uživatele, aby Azure AD B2C dokončení přihlášení. Žádost o autorizaci Určuje název zásady registrace nebo přihlašování, například **B2C_1_signup_signin**.
1. Uživatel vybere odkaz **zapomenuté heslo?** . Azure AD B2C vrátí kód chyby AADB2C90118 do aplikace.
1. Aplikace zpracovává kód chyby a Inicializuje novou žádost o autorizaci. Žádost o autorizaci Určuje název zásady resetování hesla, například **B2C_1_pwd_reset**.

![Starší tok uživatele pro resetování hesla](./media/add-password-reset-policy/password-reset-flow-legacy.png)

Příklad zobrazíte tak, že se podíváte na [jednoduchou ukázku ASP.NET](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIDConnect-DotNet-SUSI), která ukazuje propojení toků uživatelů.

::: zone pivot="b2c-user-flow"

### <a name="create-a-password-reset-user-flow"></a>Vytvoření toku uživatele pro resetování hesla

Chcete-li umožnit uživatelům vaší aplikace resetovat heslo, vytvořte tok uživatele pro resetování hesla.

1. V nabídce přehled Azure AD B2C tenanta vyberte **toky uživatelů** a pak vyberte **Nový tok uživatele**.
1. Na stránce **vytvořit tok uživatele** vyberte tok uživatele **resetování hesla** . 
1. V části **vybrat verzi** vyberte **doporučeno** a pak vyberte **vytvořit**.
1. Zadejte **název** toku uživatele. Například *passwordreset1*.
1. U **zprostředkovatelů identity** povolte možnost **resetovat heslo pomocí e-mailové adresy**.
1. V části **deklarace identity aplikace** vyberte **Zobrazit více** a zvolte deklarace identity, které chcete vrátit do vaší aplikace. Vyberte například **ID objektu uživatele**.
1. Vyberte **OK**.
1. Vyberte **vytvořit** a přidejte tok uživatele. K názvu se automaticky připojí předpona *B2C_1* .

### <a name="test-the-user-flow"></a>Testování toku uživatele

1. Vyberte tok uživatele, který jste vytvořili, a otevřete jeho stránku Přehled a pak vyberte **Spustit tok uživatele**.
1. V poli **aplikace** vyberte webovou aplikaci s názvem *WebApp1* , kterou jste předtím zaregistrovali. Měla by se zobrazit **Adresa URL odpovědi** `https://jwt.ms` .
1. Klikněte na **Spustit tok uživatele**, ověřte e-mailovou adresu účtu, který jste vytvořili dříve, a pak vyberte **pokračovat**.
1. Nyní můžete změnit heslo pro uživatele. Změňte heslo a vyberte **pokračovat**. Token se vrátí do `https://jwt.ms` a měl by se vám zobrazovat.

::: zone-end

::: zone pivot="b2c-custom-policy"

### <a name="create-a-password-reset-policy"></a>Vytvoření zásady resetování hesla

Vlastní zásady jsou sada souborů XML, které nahrajete do svého tenanta Azure AD B2C, abyste mohli definovat cesty uživatelů. Nabízíme počáteční balíčky s několika předem sestavenými zásadami, včetně registrace a přihlášení, resetování hesla a zásad úprav profilů. Další informace najdete v tématu [Začínáme s vlastními zásadami v Azure AD B2C](custom-policy-get-started.md).

::: zone-end

## <a name="next-steps"></a>Další kroky

Nastavte [vynucené resetování hesla](force-password-reset.md).
