---
title: Předání přístupového tokenu prostřednictvím vlastních zásad do vaší aplikace
titleSuffix: Azure AD B2C
description: Zjistěte, jak můžete předat přístupový token pro poskytovatele identit OAuth 2.0 jako deklaraci prostřednictvím vlastní chod vaší aplikace ve službě Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/17/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: ff5ef8f742914129d868152814d84d2112267c09
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78187781"
---
# <a name="pass-an-access-token-through-a-custom-policy-to-your-application-in-azure-active-directory-b2c"></a>Předání přístupového tokenu prostřednictvím vlastních zásad vaší aplikaci ve službě Azure Active Directory B2C

[Vlastní zásady](custom-policy-get-started.md) ve službě Azure Active Directory B2C (Azure AD B2C) poskytuje uživatelům vaší aplikace příležitost k registraci nebo přihlášení u poskytovatele identity. V takovém případě Azure AD B2C obdrží [přístupový token](tokens-overview.md) od poskytovatele identity. Azure AD B2C používá tento token k načtení informací o uživateli. Přidáte typ deklarace a výstupní deklarace do vlastní zásady předat token prostřednictvím aplikací, které zaregistrujete v Azure AD B2C.

Azure AD B2C podporuje předávání přístupového [tokenu zprostředkovatelů identit OAuth 2.0](authorization-code-flow.md) a [OpenID Connect.](openid-connect.md) Pro všechny ostatní zprostředkovatele identity je deklarace identity vrácena prázdná.

## <a name="prerequisites"></a>Požadavky

* Vaše vlastní zásady jsou nakonfigurovány s poskytovatelem identity OAuth 2.0 nebo OpenID Connect.

## <a name="add-the-claim-elements"></a>Přidání prvků deklarací

1. Otevřete soubor *TrustframeworkExtensions.xml* a přidejte následující **claimtype** element s identifikátorem `identityProviderAccessToken` prvku **ClaimsSchema:**

    ```XML
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

2. Přidejte element **OutputClaim** do prvku **TechnicalProfile** pro každého zprostředkovatele identity OAuth 2.0, pro kterého chcete získat přístupový token. Následující příklad ukazuje prvek přidaný do technického profilu Facebooku:

    ```XML
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

3. Uložte soubor *TrustframeworkExtensions.xml.*
4. Otevřete soubor zásad předávající strany, například *SignUpOrSignIn.xml*, a přidejte element **OutputClaim** do **technicalprofile**:

    ```XML
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

## <a name="test-your-policy"></a>Otestujte své zásady

Při testování aplikací v Azure AD B2C, může být užitečné mít token `https://jwt.ms` Azure AD B2C vrácena, aby bylo možné zkontrolovat deklarace identity v něm.

### <a name="upload-the-files"></a>Nahrání souborů

1. Přihlaste se k [portálu Azure](https://portal.azure.com/).
2. Ujistěte se, že používáte adresář, který obsahuje vašeho klienta Azure AD B2C kliknutím na filtr **Directory + předplatné** v horní nabídce a výběrem adresáře, který obsahuje vašeho klienta.
3. V levém horním rohu portálu Azure zvolte **Všechny služby** a pak vyhledejte a vyberte **Azure AD B2C**.
4. Vyberte **rozhraní Identity Experience Framework**.
5. Na stránce Vlastní zásady klikněte na **Nahrát zásady**.
6. Vyberte **Přepsat zásadu, pokud existuje**, a pak vyhledejte a vyberte soubor *TrustframeworkExtensions.xml.*
7. Vyberte **Nahrát**.
8. Opakujte kroky 5 až 7 pro soubor předávající strany, například *SignUpOrSignIn.xml*.

### <a name="run-the-policy"></a>Spuštění zásady

1. Otevřete zásadu, kterou jste změnili. Například *B2C_1A_signup_signin*.
2. V **aplikaci**vyberte aplikaci, kterou jste dříve zaregistrovali. Chcete-li zobrazit token v **Reply URL** příkladu níže, měla by se zobrazit `https://jwt.ms`adresa URL odpovědi .
3. Vyberte **Spustit nyní**.

    Měli byste vidět něco podobného v následujícím příkladu:

    ![Dekódovaný token v jwt.ms se zvýrazněným blokem idp_access_token](./media/idp-pass-through-custom/idp-pass-through-custom-token.PNG)

## <a name="next-steps"></a>Další kroky

Další informace o tokenech najdete v [odkazu na tokeny Služby Azure Active Directory B2C](tokens-overview.md).
