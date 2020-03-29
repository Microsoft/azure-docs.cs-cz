---
title: Definování technického profilu pro vystavittele SAML ve vlastní chod
titleSuffix: Azure AD B2C
description: Ve vlastní chodnících ve službě Azure Active Directory B2C definujte technický profil pro token jazyka značek kontrolního výrazu zabezpečení (SAML).
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78967267"
---
# <a name="define-a-technical-profile-for-a-saml-token-issuer-in-an-azure-active-directory-b2c-custom-policy"></a>Definování technického profilu pro vystavitela tokenu SAML ve vlastních zásadách služby Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) vydává několik typů tokenů zabezpečení při procesu každého toku ověřování. Technický profil pro vystavitele tokenu SAML vydává token SAML, který je vrácen zpět do aplikace předávající strany (zprostředkovatele služeb). Obvykle tento technický profil je poslední krok orchestrace v cestě uživatele.

## <a name="protocol"></a>Protocol (Protokol)

**Atribut Name** elementu **Protocol** musí být `None`nastaven na . Nastavte element **OutputTokenFormat** na `SAML2`.

Následující příklad ukazuje technický `Saml2AssertionIssuer`profil pro :

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

**InputClaims**, **OutputClaims**a **PersistClaims** prvky jsou prázdné nebo chybí. **InutputClaimsTransformations** a **OutputClaimsTransformations prvky** jsou také chybí.

## <a name="metadata"></a>Metadata

| Atribut | Požaduje se | Popis |
| --------- | -------- | ----------- |
| IssuerUri | Ne | Název vystavithonek, který se zobrazí v odpovědi SAML. Hodnota by měla být stejný název jako nakonfigurován v aplikaci předávající strany. |

## <a name="cryptographic-keys"></a>Kryptografické klíče

Prvek CryptographicKeys obsahuje následující atributy:

| Atribut | Požaduje se | Popis |
| --------- | -------- | ----------- |
| Podepisování metadat | Ano | Certifikát X509 (sada klíčů RSA), který se má použít k podepisování metadat SAML. Azure AD B2C používá tento klíč k podepsání metadat. |
| SamlMessagePodpis| Ano| Zadejte certifikát X509 (sada klíčů RSA), který se má použít k podepisování zpráv SAML. Azure AD B2C používá tento `<samlp:Response>` klíč k podepsání odpovědi odeslat předávající straně.|

## <a name="session-management"></a>Správa relací

Chcete-li nakonfigurovat relace Azure AD B2C SAML mezi `UseTechnicalProfileForSessionManagement` aplikací předávající strany, atribut prvku, odkaz na relaci [samlSSOSessionProvider](custom-policy-reference-sso.md#samlssosessionprovider) sso.

## <a name="next-steps"></a>Další kroky

V následujícím článku najdete například použití technického profilu vystavittele SAML:

- [Registrace aplikace SAML v Azure AD B2C](connect-with-saml-service-providers.md)












