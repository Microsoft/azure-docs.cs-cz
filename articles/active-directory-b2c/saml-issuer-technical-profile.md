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
ms.date: 08/17/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: bb5383ee7930cb3d54593f71a709c033d3850889
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88521208"
---
# <a name="define-a-technical-profile-for-a-saml-token-issuer-in-an-azure-active-directory-b2c-custom-policy"></a>Definování technického profilu pro vystavitele tokenů SAML v Azure Active Directory B2C vlastní zásady

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) emituje několik typů tokenů zabezpečení při zpracovávání každého toku ověřování. Technický profil vystavitele tokenu SAML generuje token SAML, který se vrátí zpátky do aplikace předávající strany (poskytovatel služeb). Obvykle je tento technický profil posledním krokem orchestrace v cestě uživatele.

## <a name="protocol"></a>Protokol

Atribut **Name** elementu **Protocol** musí být nastaven na hodnotu `None` . Nastavte element **OutputTokenFormat** na `SAML2` .

Následující příklad ukazuje technický profil pro `Saml2AssertionIssuer` :

```xml
<TechnicalProfile Id="Saml2AssertionIssuer">
  <DisplayName>Token Issuer</DisplayName>
  <Protocol Name="SAML2"/>
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
  <UseTechnicalProfileForSessionManagement ReferenceId="SM-Saml-issuer"/>
</TechnicalProfile>
```

## <a name="input-output-and-persist-claims"></a>Vstupní, výstupní a trvalé deklarace identity

Prvky **InputClaims**, **OutputClaims**a **PersistClaims** jsou prázdné nebo chybí. Chybí také elementy **InutputClaimsTransformations** a **OutputClaimsTransformations** .

## <a name="metadata"></a>Metadata

| Atribut | Povinné | Popis |
| --------- | -------- | ----------- |
| IssuerUri | No | Název vystavitele, který se zobrazí v odpovědi SAML. Hodnota by měla být stejný název jako nakonfigurovaný v aplikaci předávající strany. |
| XmlSignatureAlgorithm | No | Metoda, kterou Azure AD B2C používá k podepsání kontrolního výrazu SAML. Možné hodnoty: `Sha256` , `Sha384` , `Sha512` , nebo `Sha1` . Nezapomeňte nakonfigurovat algoritmus podpisu na obou stranách se stejnou hodnotou. Používejte jenom algoritmus, který podporuje váš certifikát. Postup konfigurace odpovědi SAML najdete v tématu [metadata SAML předávající strany](relyingparty.md#metadata) .|

## <a name="cryptographic-keys"></a>Kryptografické klíče

Element CryptographicKeys obsahuje následující atributy:

| Atribut | Povinné | Popis |
| --------- | -------- | ----------- |
| MetadataSigning | Yes | Certifikát x509 (sada klíčů RSA), který se použije k podepisování metadat SAML. Azure AD B2C používá tento klíč k podepsání metadat. |
| SamlMessageSigning| Yes| Zadejte certifikát x509 (sadu klíčů RSA), který se použije k podepisování zpráv SAML. Azure AD B2C používá tento klíč k podepsání `<samlp:Response>` odeslání odpovědi předávající straně.|

## <a name="session-management"></a>Správa relací

Chcete-li nakonfigurovat Azure AD B2C relace SAML mezi aplikací předávající strany, atributem `UseTechnicalProfileForSessionManagement` elementu, odkazem na relaci jednotného přihlašování ( [SamlSSOSessionProvider](custom-policy-reference-sso.md#samlssosessionprovider) SSO).

## <a name="next-steps"></a>Další kroky

Příklad použití technického profilu vystavitele SAML najdete v následujícím článku:

- [Registrace aplikace SAML v Azure AD B2C](connect-with-saml-service-providers.md)

