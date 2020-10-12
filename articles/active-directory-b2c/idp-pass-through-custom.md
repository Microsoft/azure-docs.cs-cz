---
title: Předání přístupového tokenu pomocí vlastní zásady do vaší aplikace
titleSuffix: Azure AD B2C
description: Přečtěte si, jak můžete předat přístupový token poskytovatelům identity OAuth 2,0 jako deklaraci identity pomocí vlastní zásady pro aplikaci v Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 08/17/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: c434ad6a724ba513caf7923916997600097b43f6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "85387860"
---
# <a name="pass-an-access-token-through-a-custom-policy-to-your-application-in-azure-active-directory-b2c"></a>Předání přístupového tokenu pomocí vlastní zásady do aplikace v Azure Active Directory B2C

[Vlastní zásada](custom-policy-get-started.md) v Azure Active Directory B2C (Azure AD B2C) poskytuje uživatelům vaší aplikace možnost registrace nebo přihlášení pomocí poskytovatele identity. Pokud k tomu dojde, Azure AD B2C obdrží od poskytovatele identity [přístupový token](tokens-overview.md) . Azure AD B2C používá tento token k načtení informací o uživateli. Do vlastních zásad můžete přidat typ deklarace identity a výstupní deklaraci identity, abyste mohli token předat do aplikací, které zaregistrujete v Azure AD B2C.

Azure AD B2C podporuje předávání přístupového tokenu [OAuth 2,0](authorization-code-flow.md) a zprostředkovatele identity [OpenID Connect](openid-connect.md) . U všech ostatních zprostředkovatelů identity se deklarace identity vrátí jako prázdná.

## <a name="prerequisites"></a>Požadavky

* Vaše vlastní zásada je nakonfigurovaná s poskytovatelem identity OAuth 2,0 nebo OpenID Connect.

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
4. Otevřete soubor zásad předávající strany, například *SignUpOrSignIn.xml*, a přidejte do **TechnicalProfile**element **OutputClaim** :

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

1. Přihlaste se k [portálu Azure Portal](https://portal.azure.com/).
2. Ujistěte se, že používáte adresář, který obsahuje vašeho tenanta Azure AD B2C kliknutím na v horní nabídce na položku filtr **adresářů a předplatných** a vyberte adresář, který obsahuje vašeho tenanta.
3. V levém horním rohu Azure Portal vyberte **všechny služby** a pak vyhledejte a vyberte **Azure AD B2C**.
4. Vyberte **architekturu prostředí identity**.
5. Na stránce vlastní zásady klikněte na **nahrát zásadu**.
6. Vyberte **přepsat zásadu, pokud existuje**, a pak vyhledejte a vyberte soubor *TrustframeworkExtensions.xml* .
7. Vyberte **Nahrát**.
8. Opakujte kroky 5 až 7 pro soubor předávající strany, například *SignUpOrSignIn.xml*.

### <a name="run-the-policy"></a>Spustit zásadu

1. Otevřete zásadu, kterou jste změnili. Například *B2C_1A_signup_signin*.
2. V případě **aplikace**vyberte svou aplikaci, kterou jste předtím zaregistrovali. Pokud chcete token zobrazit v následujícím příkladu, měla by se zobrazit **Adresa URL odpovědi** `https://jwt.ms` .
3. Vyberte **Spustit nyní**.

    Měl by se zobrazit něco podobného jako v následujícím příkladu:

    ![Dekódování tokenu v jwt.ms s zvýrazněným blokem idp_access_token](./media/idp-pass-through-custom/idp-pass-through-custom-token.PNG)

## <a name="next-steps"></a>Další kroky

Další informace o tokenech najdete v referenčních informacích k [tokenu Azure Active Directory B2C](tokens-overview.md).
