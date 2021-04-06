---
title: Předání přístupového tokenu zprostředkovatele identity do vaší aplikace
titleSuffix: Azure AD B2C
description: Naučte se předat přístupový token pro zprostředkovatele identity OAuth 2,0 jako deklaraci identity v toku uživatele v Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 12/15/2020
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: a99d41f5f9fc9538aaf563bd3ae56075d269c94a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "97584642"
---
# <a name="pass-an-identity-provider-access-token-to-your-application-in-azure-active-directory-b2c"></a>Předání tokenu přístupu poskytovatele identity do aplikace v Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

[Tok uživatele](user-flow-overview.md) v Azure Active Directory B2C (Azure AD B2C) poskytuje uživatelům vaší aplikace možnost registrace nebo přihlášení pomocí poskytovatele identity. Po spuštění cesty Azure AD B2C obdrží od poskytovatele identity [přístupový token](tokens-overview.md) . Azure AD B2C používá tento token k načtení informací o uživateli. V toku uživatele povolíte deklaraci identity, abyste mohli token předat do aplikací, které zaregistrujete v Azure AD B2C.

::: zone pivot="b2c-user-flow"

Azure AD B2C podporuje předávání přístupového tokenu zprostředkovatelů identity [OAuth 2,0](add-identity-provider.md) , které zahrnují [Facebook](identity-provider-facebook.md) a [Google](identity-provider-google.md). U všech ostatních zprostředkovatelů identity se deklarace identity vrátí jako prázdná.

::: zone-end

::: zone pivot="b2c-custom-policy"

Azure AD B2C podporuje předávání přístupového tokenu [OAuth 2,0](authorization-code-flow.md) a zprostředkovatele identity [OpenID Connect](openid-connect.md) . U všech ostatních zprostředkovatelů identity se deklarace identity vrátí jako prázdná.

::: zone-end

Následující diagram znázorňuje, jak se token zprostředkovatele identity vrátí do vaší aplikace: 

![Tok zprostředkovatele identity průchozího toku](./media/idp-pass-through-user-flow/identity-provider-pass-through-flow.png)

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

::: zone pivot="b2c-user-flow"

## <a name="enable-the-claim"></a>Povolit deklaraci identity

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/) jako globální správce vašeho tenanta Azure AD B2C.
2. Ujistěte se, že používáte adresář, který obsahuje vašeho tenanta Azure AD B2C. V horní nabídce vyberte filtr **adresář + odběr** a zvolte adresář, který obsahuje vašeho tenanta.
3. Zvolte **Všechny služby** v levém horním rohu portálu Azure Portal a vyhledejte a vyberte **Azure AD B2C**.
4. Vyberte **toky uživatelů (zásady)** a pak vyberte tok uživatele. Například **B2C_1_signupsignin1**.
5. Vyberte **Deklarace identit aplikace**.
6. Povolte deklaraci identity **přístupového tokenu zprostředkovatele identity** .

    ![Povolit deklaraci identity přístupového tokenu zprostředkovatele identity](./media/idp-pass-through-user-flow/identity-provider-pass-through-app-claim.png)

7. Kliknutím na **Uložit** uložte tok uživatele.

## <a name="test-the-user-flow"></a>Testování toku uživatele

Při testování aplikací v Azure AD B2C může být užitečné, aby se token Azure AD B2C vrátil na `https://jwt.ms` kontrolu deklarací identity v něm.

1. Na stránce Přehled toku uživatele vyberte **Spustit tok uživatele**.
2. V případě **aplikace** vyberte svou aplikaci, kterou jste předtím zaregistrovali. Pokud chcete token zobrazit v následujícím příkladu, měla by se zobrazit **Adresa URL odpovědi** `https://jwt.ms` .
3. Klikněte na **Spustit tok uživatele** a pak se přihlaste pomocí přihlašovacích údajů k účtu. V deklaraci **idp_access_token** by měl být uveden přístupový token poskytovatele identity.

    Měl by se zobrazit něco podobného jako v následujícím příkladu:

    ![Dekódování tokenu v jwt.ms s zvýrazněným blokem idp_access_token](./media/idp-pass-through-user-flow/identity-provider-pass-through-token.png)

::: zone-end

::: zone pivot="b2c-custom-policy"

## <a name="add-the-claim-elements"></a>Přidání prvků deklarace identity

1. Otevřete soubor *TrustframeworkExtensions.xml* a přidejte následující element **ClaimType** s identifikátorem `identityProviderAccessToken` do prvku **ClaimsSchema** :

    ```xml
    <BuildingBlocks>
      <ClaimsSchema>
        <ClaimType Id="identityProviderAccessToken">
          <DisplayName>Identity Provider Access Token</DisplayName>
          <DataType>string</DataType>
          <AdminHelpText>Stores the access token of the identity provider.</AdminHelpText>
        </ClaimType>
        ...
      </ClaimsSchema>
    </BuildingBlocks>
    ```

2. Přidejte element **OutputClaim** do elementu **TechnicalProfile** pro každého poskytovatele identity OAuth 2,0, pro který chcete získat přístupový token. Následující příklad ukazuje prvek přidaný do technického profilu Facebooku:

    ```xml
    <ClaimsProvider>
      <DisplayName>Facebook</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Facebook-OAUTH">
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="identityProviderAccessToken" PartnerClaimType="{oauth2:access_token}" />
          </OutputClaims>
          ...
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

3. Uložte soubor *TrustframeworkExtensions.xml* .
4. Otevřete soubor zásad předávající strany, například *SignUpOrSignIn.xml*, a přidejte do **TechnicalProfile** element **OutputClaim** :

    ```xml
    <RelyingParty>
      <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
      <TechnicalProfile Id="PolicyProfile">
        <OutputClaims>
          <OutputClaim ClaimTypeReferenceId="identityProviderAccessToken" PartnerClaimType="idp_access_token"/>
        </OutputClaims>
        ...
      </TechnicalProfile>
    </RelyingParty>
    ```

5. Uložte soubor zásad.

## <a name="test-your-policy"></a>Testování zásad

Při testování aplikací v Azure AD B2C může být užitečné, aby byl token Azure AD B2C vrácen, aby bylo možné `https://jwt.ms` zkontrolovat deklarace identity v ní.

### <a name="upload-the-files"></a>Nahrání souborů

1. Přihlaste se na [Azure Portal](https://portal.azure.com/).
2. Ujistěte se, že používáte adresář, který obsahuje vašeho tenanta Azure AD B2C kliknutím na v horní nabídce na položku filtr **adresářů a předplatných** a vyberte adresář, který obsahuje vašeho tenanta.
3. V levém horním rohu Azure Portal vyberte **všechny služby** a pak vyhledejte a vyberte **Azure AD B2C**.
4. Vyberte **architekturu prostředí identity**.
5. Na stránce vlastní zásady klikněte na **nahrát zásadu**.
6. Vyberte **přepsat zásadu, pokud existuje**, a pak vyhledejte a vyberte soubor *TrustframeworkExtensions.xml* .
7. Vyberte **Nahrát**.
8. Opakujte kroky 5 až 7 pro soubor předávající strany, například *SignUpOrSignIn.xml*.

### <a name="run-the-policy"></a>Spustit zásadu

1. Otevřete zásadu, kterou jste změnili. Například *B2C_1A_signup_signin*.
2. V případě **aplikace** vyberte svou aplikaci, kterou jste předtím zaregistrovali. Pokud chcete token zobrazit v následujícím příkladu, měla by se zobrazit **Adresa URL odpovědi** `https://jwt.ms` .
3. Vyberte **Spustit nyní**.

    Měl by se zobrazit něco podobného jako v následujícím příkladu:

    ![Dekódování tokenu v jwt.ms s zvýrazněným blokem idp_access_token](./media/idp-pass-through-user-flow/identity-provider-pass-through-token-custom.png)

::: zone-end

## <a name="next-steps"></a>Další kroky

Další informace najdete v [přehledu Azure AD B2Cch tokenů](tokens-overview.md).
