---
title: Definování technického profilu pro vystavitele SAML ve vlastních zásadách
titleSuffix: Azure AD B2C
description: Definujte technický profil vystavitele Security Assertion Markup Language tokenu (SAML) ve vlastních zásadách v Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 03/10/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: c35f85b9ec5d86d1cd61f165b891c576c06a03db
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "78967267"
---
# <a name="define-a-technical-profile-for-a-saml-token-issuer-in-an-azure-active-directory-b2c-custom-policy"></a>Definování technického profilu pro vystavitele tokenů SAML v Azure Active Directory B2C vlastní zásady

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) emituje několik typů tokenů zabezpečení při zpracovávání každého toku ověřování. Technický profil vystavitele tokenu SAML generuje token SAML, který se vrátí zpátky do aplikace předávající strany (poskytovatel služeb). Obvykle je tento technický profil posledním krokem orchestrace v cestě uživatele.

## <a name="protocol"></a>Protocol (Protokol)

Atribut **Name** elementu **Protocol** musí být nastaven na `None`hodnotu. Nastavte element **OutputTokenFormat** na `SAML2`.

Následující příklad ukazuje technický profil pro `Saml2AssertionIssuer`:

```XML
<TechnicalProfile Id="Saml2AssertionIssuer">
  <DisplayName>Token Issuer</DisplayName>
  <Protocol Name="None"/>
  <OutputTokenFormat>SAML2</OutputTokenFormat>
  <Metadata>
    <Item Key="IssuerUri">https://tenant-name.b2clogin.com/tenant-name.onmicrosoft.com/B2C_1A_signup_signin_SAML</Item>
  </Metadata>
  <CryptographicKeys>
    <Key Id="MetadataSigning" StorageReferenceId="B2C_1A_SamlIdpCert"/>
    <Key Id="SamlMessageSigning" StorageReferenceId="B2C_1A_SamlIdpCert"/>
  </CryptographicKeys>
  <InputClaims/>
  <OutputClaims/>
  <UseTechnicalProfileForSessionManagement ReferenceId="SM-Saml-sp"/>
</TechnicalProfile>
```

## <a name="input-output-and-persist-claims"></a>Vstupní, výstupní a trvalé deklarace identity

Prvky **InputClaims**, **OutputClaims**a **PersistClaims** jsou prázdné nebo chybí. Chybí také elementy **InutputClaimsTransformations** a **OutputClaimsTransformations** .

## <a name="metadata"></a>Metadata

| Atribut | Požaduje se | Popis |
| --------- | -------- | ----------- |
| IssuerUri | Ne | Název vystavitele, který se zobrazí v odpovědi SAML. Hodnota by měla být stejný název jako nakonfigurovaný v aplikaci předávající strany. |

## <a name="cryptographic-keys"></a>Kryptografické klíče

Element CryptographicKeys obsahuje následující atributy:

| Atribut | Požaduje se | Popis |
| --------- | -------- | ----------- |
| MetadataSigning | Ano | Certifikát x509 (sada klíčů RSA), který se použije k podepisování metadat SAML. Azure AD B2C používá tento klíč k podepsání metadat. |
| SamlMessageSigning| Ano| Zadejte certifikát x509 (sadu klíčů RSA), který se použije k podepisování zpráv SAML. Azure AD B2C používá tento klíč k podepsání odeslání `<samlp:Response>` odpovědi předávající straně.|

## <a name="session-management"></a>Správa relací

Chcete-li nakonfigurovat Azure AD B2C relace SAML mezi aplikací předávající strany, atributem `UseTechnicalProfileForSessionManagement` elementu, odkazem na relaci jednotného přihlašování ( [SamlSSOSessionProvider](custom-policy-reference-sso.md#samlssosessionprovider) SSO).

## <a name="next-steps"></a>Další kroky

Příklad použití technického profilu vystavitele SAML najdete v následujícím článku:

- [Registrace aplikace SAML v Azure AD B2C](connect-with-saml-service-providers.md)












