---
title: Konfigurace tokenů – Azure Active Directory B2C | Microsoft Docs
description: Naučte se konfigurovat životnost tokenu a nastavení kompatibility v Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 12/14/2020
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: f22a55a65b7dc4fd4f714d880804e6fd65dbbe46
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "101654370"
---
# <a name="configure-tokens-in-azure-active-directory-b2c"></a>Konfigurace tokenů ve službě Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

V tomto článku se dozvíte, jak nakonfigurovat [životnost a kompatibilitu tokenu](tokens-overview.md) v Azure Active Directory B2C (Azure AD B2C).

## <a name="prerequisites"></a>Předpoklady

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="token-lifetime-behavior"></a>Chování při životnosti tokenů

Můžete nakonfigurovat životnost tokenu, včetně:

- **Doba životnosti tokenu a tokenu (minuty) protokolu** OAuth 2,0 a tokeny ID. Výchozí hodnota je 60 minut (1 hodina). Minimální (včetně) je 5 minut. Maximální (včetně) je 1 440 minut (24 hodin).
- **Doba platnosti tokenu aktualizace (dny)** – maximální doba, po jejímž uplynutí může být obnovovací token použit k získání nového přístupového tokenu, pokud je vaší aplikaci udělen `offline_access` obor. Výchozí hodnota je 14 dní. Minimální (včetně) je jeden den. Maximální (včetně) 90 dnů.
- **Doba trvání posuvných oken obnovovacího tokenu** – typ posuvných oken aktualizačního tokenu `Bounded` indikuje, že obnovovací token se dá rozšířit tak, jak zadat v poli **délka životnosti (dny)**. `No expiry` indikuje, že doba vypršení platnosti posuvných oken obnovovacího tokenu vypršela.
- **Doba života (dny)** – po uplynutí tohoto časového období se uživatel nuceně znovu neověřuje bez ohledu na dobu platnosti nejnovějšího obnovovacího tokenu, který aplikace získala. Hodnota musí být větší než nebo rovna hodnotě **životnosti obnovovacího tokenu** .

Následující diagram znázorňuje chování při dobu životnosti posuvných oken obnovovacího tokenu.

![Doba platnosti tokenu aktualizace](./media/configure-tokens/refresh-token-lifetime.png)

> [!NOTE]
> >Jednostránkové aplikace, které používají tok autorizačního kódu s PKCE, mají vždycky dobu platnosti tokenu aktualizace na 24 hodin, zatímco mobilní aplikace, desktopové aplikace a webové aplikace toto omezení nefungují. [Přečtěte si další informace o dopadech aktualizace tokenů v prohlížeči na zabezpečení](../active-directory/develop/reference-third-party-cookies-spas.md#security-implications-of-refresh-tokens-in-the-browser).


## <a name="configure-token-lifetime"></a>Konfigurace životnosti tokenů

::: zone pivot="b2c-user-flow"

Konfigurace životnosti tokenu uživatelského toku:

1. Přihlaste se na [Azure Portal](https://portal.azure.com).
1. Ujistěte se, že používáte adresář, který obsahuje vašeho tenanta Azure AD B2C. V horní nabídce vyberte filtr **adresář + odběr** a zvolte adresář, který obsahuje vašeho tenanta Azure AD B2C.
1. V levém horním rohu Azure Portal vyberte **všechny služby** a pak vyhledejte a vyberte **Azure AD B2C**.
1. Vyberte **toky uživatelů (zásady)**.
1. Otevřete uživatelský tok, který jste vytvořili dříve.
1. Vyberte **Vlastnosti**.
1. V části **životnost tokenu** upravte vlastnosti tak, aby vyhovovaly potřebám vaší aplikace.
1. Klikněte na **Uložit**.

::: zone-end

::: zone pivot="b2c-custom-policy"

Pokud chcete změnit nastavení kompatibility tokenu, nastavte metadata technického profilu [vystavitele tokenu](jwt-issuer-technical-profile.md) v rozšíření nebo soubor předávající strany zásad, které chcete ovlivnit. Technický profil vystavitele tokenu vypadá jako v následujícím příkladu:

```xml
<ClaimsProviders>
  <ClaimsProvider>
    <DisplayName>Token Issuer</DisplayName>
    <TechnicalProfiles>
      <TechnicalProfile Id="JwtIssuer">
        <Metadata>
          <Item Key="token_lifetime_secs">3600</Item>
          <Item Key="id_token_lifetime_secs">3600</Item>
          <Item Key="refresh_token_lifetime_secs">1209600</Item>
          <Item Key="rolling_refresh_token_lifetime_secs">7776000</Item>
          <!--<Item Key="allow_infinite_rolling_refresh_token">true</Item>-->
          <Item Key="IssuanceClaimPattern">AuthorityAndTenantGuid</Item>
          <Item Key="AuthenticationContextReferenceClaimPattern">None</Item>
        </Metadata>
      </TechnicalProfile>
    </TechnicalProfiles>
  </ClaimsProvider>
</ClaimsProviders>
```

V předchozím příkladu jsou nastaveny následující hodnoty:

- **token_lifetime_secs** – doba života tokenu přístupu (sekundy). Výchozí hodnota je 3 600 (1 hodina). Minimum je 300 (5 minut). Maximální hodnota je 86 400 (24 hodin). 
- životnost tokenu **id_token_lifetime_secs** -ID (sekundy). Výchozí hodnota je 3 600 (1 hodina). Minimum je 300 (5 minut). Maximální hodnota je 86 400 (24 hodin). 
- **refresh_token_lifetime_secs** Aktualizujte životnosti tokenů (sekundy). Výchozí hodnota je 120, 9600 (14 dní). Minimum je 86 400 (24 hodin). Maximální hodnota je 7 776 000 (90 dní). 
- **rolling_refresh_token_lifetime_secs** – doba trvání posuvných oken (sekundy) aktualizačního tokenu Výchozí hodnota je 7 776 000 (90 dní). Minimum je 86 400 (24 hodin). Maximální hodnota je 31 536 000 (365 dní). Pokud nechcete vymáhat dobu trvání klouzavého okna, nastavte hodnotu `allow_infinite_rolling_refresh_token` na `true` . 
- Doba platnosti posuvu okna **allow_infinite_rolling_refresh_token** -Refresh vypršela. 

::: zone-end


## <a name="token-compatibility-settings"></a>Nastavení kompatibility tokenů

Můžete nakonfigurovat kompatibilitu tokenu, včetně:

- **Issuer (ISS)** – formát vystavitele tokenu pro přístup a ID.
- **Deklarace subjektu (sub)** – hlavní informace o tom, který token vyhodnotí informace, jako je například uživatel aplikace. Tato hodnota je neměnná a nelze ji znovu přiřadit ani použít znovu. Dá se použít k bezpečnému provádění kontrol autorizace, například když se token používá pro přístup k prostředku. Ve výchozím nastavení se deklarace identity subjektu naplní s ID objektu uživatele v adresáři.
- **Deklarace identity představující tok uživatele** – tato deklarace identity identifikuje tok uživatele, který byl proveden. Možné hodnoty: `tfp` (výchozí), nebo `acr` .

::: zone pivot="b2c-user-flow"

Konfigurace nastavení kompatibility toku uživatele:

1. Vyberte **toky uživatelů (zásady)**.
1. Otevřete uživatelský tok, který jste vytvořili dříve.
1. Vyberte **Vlastnosti**.
1. V části **nastavení kompatibility tokenů** upravte vlastnosti tak, aby vyhovovaly potřebám vaší aplikace.
1. Klikněte na **Uložit**.

::: zone-end

::: zone pivot="b2c-custom-policy"

Pokud chcete změnit nastavení kompatibility tokenu, nastavte metadata technického profilu [vystavitele tokenu](jwt-issuer-technical-profile.md) v rozšíření nebo soubor předávající strany zásad, které chcete ovlivnit. Technický profil vystavitele tokenu vypadá jako v následujícím příkladu:

```xml
<ClaimsProviders>
  <ClaimsProvider>
    <DisplayName>Token Issuer</DisplayName>
    <TechnicalProfiles>
      <TechnicalProfile Id="JwtIssuer">
        <Metadata>
          ...
          <Item Key="IssuanceClaimPattern">AuthorityAndTenantGuid</Item>
          <Item Key="AuthenticationContextReferenceClaimPattern">None</Item>
        </Metadata>
      </TechnicalProfile>
    </TechnicalProfiles>
  </ClaimsProvider>
</ClaimsProviders>
```

- **Deklarace vystavitele** – deklarace identity vystavitele (ISS) se nastaví pomocí položky metadat **IssuanceClaimPattern** . Platné hodnoty jsou `AuthorityAndTenantGuid` a `AuthorityWithTfp` .
- **Nastavení deklarace identity představující ID zásady** – možnosti pro nastavení této hodnoty jsou `TFP` (zásady pro vztah důvěryhodnosti) a `ACR` (odkaz na kontext ověřování). `TFP` je doporučená hodnota. Nastavte **AuthenticationContextReferenceClaimPattern** s hodnotou `None` .

    Do elementu **ClaimsSchema** přidejte tento element:

    ```xml
    <ClaimType Id="trustFrameworkPolicy">
      <DisplayName>Trust framework policy name</DisplayName>
      <DataType>string</DataType>
    </ClaimType>
    ```

    Do prvku **OutputClaims** přidejte tento element:

    ```xml
    <OutputClaim ClaimTypeReferenceId="trustFrameworkPolicy" Required="true" DefaultValue="{policy}" />
    ```

    Pro ACR odeberte položku **AuthenticationContextReferenceClaimPattern** .

- **Deklarace subjektu (sub)** – Tato možnost je ve výchozím nastavení nastavena na hodnotu ObjectId, pokud byste chtěli toto nastavení přepnout na `Not Supported` , nahraďte tento řádek:

    ```xml
    <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub" />
    ```

    s tímto řádkem:

    ```xml
    <OutputClaim ClaimTypeReferenceId="sub" />
    ```

::: zone-end

## <a name="provide-optional-claims-to-your-app"></a>Poskytněte do aplikace volitelné deklarace identity.

Deklarace identity aplikace jsou hodnoty, které se vrátí do aplikace. Aktualizujte tok uživatele tak, aby obsahoval požadované deklarace identity.

::: zone pivot="b2c-user-flow"

1. Vyberte **toky uživatelů (zásady)**.
1. Otevřete uživatelský tok, který jste vytvořili dříve.
1. Vyberte **Deklarace identit aplikace**.
1. Vyberte deklarace identity a atributy, které chcete poslat zpátky do své aplikace.
1. Klikněte na **Uložit**.

::: zone-end

::: zone pivot="b2c-custom-policy"

Deklarace identity výstupu [Technical profil zásad předávající strany](relyingparty.md#technicalprofile) jsou hodnoty, které se vrátí do aplikace. Přidáním deklarací výstup budou deklarace identity vydány do tokenu po úspěšné cestě uživatele a budou odeslány do aplikace. Upravte element Technical profil v části předávající strany a přidejte požadované deklarace identity jako výstupní deklaraci.

1. Otevřete vlastní soubor zásad. Například SignUpOrSignin.xml.
1. Vyhledejte element OutputClaims. Přidejte OutputClaim, který chcete zahrnout do tokenu. 
1. Nastavte výstupní atributy deklarace identity. 

Následující příklad přidá `accountBalance` deklaraci identity. Deklarace identity accountBalance se do aplikace pošle jako zůstatek. 

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
      <!--Add the optional claims here-->
      <OutputClaim ClaimTypeReferenceId="accountBalance" DefaultValue="" PartnerClaimType="balance" />
    </OutputClaims>
    <SubjectNamingInfo ClaimType="sub" />
  </TechnicalProfile>
</RelyingParty>
```

Element OutputClaim obsahuje následující atributy:

- **ClaimTypeReferenceId** – identifikátor typu deklarace identity, který už je definovaný v oddílu [ClaimsSchema](claimsschema.md) v souboru zásad nebo v nadřazeném souboru zásad.
- **PartnerClaimType** – umožňuje změnit název deklarace identity v tokenu. 
- **DefaultValue** – výchozí hodnota. Výchozí hodnotu můžete také nastavit na [překladač deklarací identity](claim-resolver-overview.md), jako je například ID tenanta.
- **AlwaysUseDefaultValue** – vynutí použití výchozí hodnoty.

::: zone-end

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o tom, jak [vyžádat přístupové tokeny](access-tokens.md).