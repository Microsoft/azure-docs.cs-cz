---
title: Jednotné přihlášení k Azure v protokolu SAML | Dokumenty společnosti Microsoft
description: Tento článek popisuje protokol SAML jednotného přihlášení ve službě Azure Active Directory.
services: active-directory
documentationcenter: .net
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: ad8437f5-b887-41ff-bd77-779ddafc33fb
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/19/2017
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: hirsin
ms.openlocfilehash: cecb78a82eb2925813bdc7f6df2503fae94b6437
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79262422"
---
# <a name="single-sign-on-saml-protocol"></a>Protokol SAML jednotného přihlášení

Tento článek popisuje požadavky na ověření SAML 2.0 a odpovědi, které azure active directory (Azure AD) podporuje pro jednotné přihlašování.

Diagram protokolu níže popisuje pořadí jednotného přihlašování. Cloudová služba (poskytovatel služeb) používá vazbu přesměrování `AuthnRequest` HTTP k předání elementu (požadavku na ověření) do Azure AD (zprostředkovatele identity). Azure AD pak používá vazbu `Response` post HTTP k zaúčtování prvku do cloudové služby.

![Pracovní postup jednotného přihlášení](./media/single-sign-on-saml-protocol/active-directory-saml-single-sign-on-workflow.png)

## <a name="authnrequest"></a>Požadavek authn

Chcete-li požádat o ověření `AuthnRequest` uživatele, cloudové služby odeslat prvek do Azure AD. Ukázka SAML 2.0 `AuthnRequest` může vypadat jako následující příklad:

```
<samlp:AuthnRequest
xmlns="urn:oasis:names:tc:SAML:2.0:metadata"
ID="id6c1c178c166d486687be4aaf5e482730"
Version="2.0" IssueInstant="2013-03-18T03:28:54.1839884Z"
xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
<Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">https://www.contoso.com</Issuer>
</samlp:AuthnRequest>
```

| Parametr |  | Popis |
| --- | --- | --- |
| ID | Požaduje se | Azure AD používá tento `InResponseTo` atribut k naplnění atributu vrácené odpovědi. ID nesmí začínat číslem, takže běžnou strategií je předřadit řetězec jako "id" na řetězcovou reprezentaci identifikátoru GUID. Například `id6c1c178c166d486687be4aaf5e482730` je platné ID. |
| Version | Požaduje se | Tento parametr by měl být nastaven na **hodnotu 2.0**. |
| IssueInstant | Požaduje se | Jedná se o řetězec DateTime s hodnotou UTC a [formátem round-trip ("o")](https://msdn.microsoft.com/library/az4se3k1.aspx). Azure AD očekává DateTime hodnotu tohoto typu, ale nevyhodnocuje ani nepoužije hodnotu. |
| AssertionConsumerServiceUrl | Nepovinné | Pokud je k dispozici, `RedirectUri` tento parametr musí odpovídat cloudové služby ve službě Azure AD. |
| ForceAuthn | Nepovinné | Toto je logická hodnota. Pokud true, znamená to, že uživatel bude nucen znovu ověřit, i v případě, že mají platnou relaci s Azure AD. |
| IsPassive | Nepovinné | Toto je logická hodnota, která určuje, zda Azure AD by měl ověřit uživatele tiše, bez interakce s uživatelem, pomocí souboru cookie relace, pokud existuje. Pokud je to pravda, Azure AD se pokusí ověřit uživatele pomocí souboru cookie relace. |

Všechny `AuthnRequest` ostatní atributy, například Consent, Destination, AssertionConsumerServiceIndex, AttributeConsumerServiceIndex a ProviderName, jsou **ignorovány**.

Azure AD také `Conditions` ignoruje `AuthnRequest`prvek v .

### <a name="issuer"></a>Vystavitel

Prvek `Issuer` v `AuthnRequest` musí přesně odpovídat jednomu z **ServicePrincipalNames** v cloudové službě ve službě Azure AD. Obvykle je to nastaveno na **identifikátor URI ID aplikace,** který je zadán při registraci aplikace.

Výňatek SAML obsahující `Issuer` prvek vypadá jako následující ukázka:

```
<Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">https://www.contoso.com</Issuer>
```

### <a name="nameidpolicy"></a>Zásady iD názvu

Tento prvek požaduje konkrétní formát ID názvu v `AuthnRequest` odpovědi a je volitelné v prvky odeslané do Služby Azure AD.

Prvek `NameIdPolicy` vypadá jako následující ukázka:

```
<NameIDPolicy Format="urn:oasis:names:tc:SAML:2.0:nameid-format:persistent"/>
```

Pokud `NameIDPolicy` je k dispozici, můžete `Format` zahrnout jeho volitelný atribut. Atribut `Format` může mít pouze jednu z následujících hodnot; jakákoli jiná hodnota má za následek chybu.

* `urn:oasis:names:tc:SAML:2.0:nameid-format:persistent`: Služba Azure Active Directory vydává deklaraci NameID jako identifikátor párového.
* `urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress`: Služba Azure Active Directory vydává deklaraci NameID ve formátu e-mailové adresy.
* `urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified`: Tato hodnota umožňuje službě Azure Active Directory vybrat formát deklarace. Služba Azure Active Directory vydává název NameID jako identifikátor párové.
* `urn:oasis:names:tc:SAML:2.0:nameid-format:transient`: Služba Azure Active Directory vydává deklaraci NameID jako náhodně generovanou hodnotu, která je jedinečná pro aktuální operaci přihlašování. To znamená, že hodnota je dočasná a nelze ji použít k identifikaci ověřovacího uživatele.

Azure AD ignoruje `AllowCreate` atribut.

### <a name="requestauthncontext"></a>RequestAuthnKontext
Prvek `RequestedAuthnContext` určuje požadované metody ověřování. Je volitelné `AuthnRequest` v prvky odeslané do Služby Azure AD. Azure AD `AuthnContextClassRef` podporuje `urn:oasis:names:tc:SAML:2.0:ac:classes:Password`hodnoty, jako je například .

### <a name="scoping"></a>Oboru
Prvek, `Scoping` který obsahuje seznam zprostředkovatelů identity, `AuthnRequest` je volitelné v prvky odeslané do služby Azure AD.

Pokud je k dispozici, `ProxyCount` nezahrnejte atribut `IDPListOption` nebo `RequesterID` prvek, protože nejsou podporovány.

### <a name="signature"></a>Podpis
Nezahrnejte `Signature` prvek `AuthnRequest` do prvků, protože Azure AD nepodporuje podepsané požadavky na ověření.

### <a name="subject"></a>Subjekt
Azure AD ignoruje `Subject` prvek `AuthnRequest` prvků.

## <a name="response"></a>Odpověď
Po úspěšném dokončení požadovaného přihlášení azure ad příspěvky odpověď na cloudové služby. Odpověď na úspěšný pokus o přihlášení vypadá jako následující ukázka:

```
<samlp:Response ID="_a4958bfd-e107-4e67-b06d-0d85ade2e76a" Version="2.0" IssueInstant="2013-03-18T07:38:15.144Z" Destination="https://contoso.com/identity/inboundsso.aspx" InResponseTo="id758d0ef385634593a77bdf7e632984b6" xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
  <Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion"> https://login.microsoftonline.com/82869000-6ad1-48f0-8171-272ed18796e9/</Issuer>
  <ds:Signature xmlns:ds="https://www.w3.org/2000/09/xmldsig#">
    ...
  </ds:Signature>
  <samlp:Status>
    <samlp:StatusCode Value="urn:oasis:names:tc:SAML:2.0:status:Success" />
  </samlp:Status>
  <Assertion ID="_bf9c623d-cc20-407a-9a59-c2d0aee84d12" IssueInstant="2013-03-18T07:38:15.144Z" Version="2.0" xmlns="urn:oasis:names:tc:SAML:2.0:assertion">
    <Issuer>https://login.microsoftonline.com/82869000-6ad1-48f0-8171-272ed18796e9/</Issuer>
    <ds:Signature xmlns:ds="https://www.w3.org/2000/09/xmldsig#">
      ...
    </ds:Signature>
    <Subject>
      <NameID>Uz2Pqz1X7pxe4XLWxV9KJQ+n59d573SepSAkuYKSde8=</NameID>
      <SubjectConfirmation Method="urn:oasis:names:tc:SAML:2.0:cm:bearer">
        <SubjectConfirmationData InResponseTo="id758d0ef385634593a77bdf7e632984b6" NotOnOrAfter="2013-03-18T07:43:15.144Z" Recipient="https://contoso.com/identity/inboundsso.aspx" />
      </SubjectConfirmation>
    </Subject>
    <Conditions NotBefore="2013-03-18T07:38:15.128Z" NotOnOrAfter="2013-03-18T08:48:15.128Z">
      <AudienceRestriction>
        <Audience>https://www.contoso.com</Audience>
      </AudienceRestriction>
    </Conditions>
    <AttributeStatement>
      <Attribute Name="http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name">
        <AttributeValue>testuser@contoso.com</AttributeValue>
      </Attribute>
      <Attribute Name="http://schemas.microsoft.com/identity/claims/objectidentifier">
        <AttributeValue>3F2504E0-4F89-11D3-9A0C-0305E82C3301</AttributeValue>
      </Attribute>
      ...
    </AttributeStatement>
    <AuthnStatement AuthnInstant="2013-03-18T07:33:56.000Z" SessionIndex="_bf9c623d-cc20-407a-9a59-c2d0aee84d12">
      <AuthnContext>
        <AuthnContextClassRef> urn:oasis:names:tc:SAML:2.0:ac:classes:Password</AuthnContextClassRef>
      </AuthnContext>
    </AuthnStatement>
  </Assertion>
</samlp:Response>
```

### <a name="response"></a>Odpověď

Prvek `Response` obsahuje výsledek žádosti o autorizaci. Azure AD `ID`nastaví a `Version` `Response` `IssueInstant` hodnoty v prvku. Nastaví také následující atributy:

* `Destination`: Po úspěšném dokončení přihlášení je tonastaveno na `RedirectUri` poskytovatele služeb (cloudovou službu).
* `InResponseTo`: Toto je `ID` nastaveno `AuthnRequest` na atribut prvku, který inicioval odpověď.

### <a name="issuer"></a>Vystavitel

Azure AD `Issuer` nastaví `https://login.microsoftonline.com/<TenantIDGUID>/` \<prvek, kde TenantIDGUID> je ID klienta klienta klienta azure ad tenanta.

Odpověď s elementem Issuer může například vypadat jako následující ukázka:

```
<Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion"> https://login.microsoftonline.com/82869000-6ad1-48f0-8171-272ed18796e9/</Issuer>
```

### <a name="status"></a>Status

Prvek `Status` vyjadřuje úspěch nebo neúspěch přihlášení. Obsahuje `StatusCode` prvek, který obsahuje kód nebo sadu vnořených kódů, které představují stav požadavku. Obsahuje také `StatusMessage` prvek, který obsahuje vlastní chybové zprávy, které jsou generovány během procesu přihlášení.

<!-- TODO: Add an authentication protocol error reference -->

Následující ukázka je odpověď SAML na neúspěšný pokus o přihlášení.

```
<samlp:Response ID="_f0961a83-d071-4be5-a18c-9ae7b22987a4" Version="2.0" IssueInstant="2013-03-18T08:49:24.405Z" InResponseTo="iddce91f96e56747b5ace6d2e2aa9d4f8c" xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
  <Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">https://sts.windows.net/82869000-6ad1-48f0-8171-272ed18796e9/</Issuer>
  <samlp:Status>
    <samlp:StatusCode Value="urn:oasis:names:tc:SAML:2.0:status:Requester">
      <samlp:StatusCode Value="urn:oasis:names:tc:SAML:2.0:status:RequestUnsupported" />
    </samlp:StatusCode>
    <samlp:StatusMessage>AADSTS75006: An error occurred while processing a SAML2 Authentication request. AADSTS90011: The SAML authentication request property 'NameIdentifierPolicy/SPNameQualifier' is not supported.
Trace ID: 66febed4-e737-49ff-ac23-464ba090d57c
Timestamp: 2013-03-18 08:49:24Z</samlp:StatusMessage>
  </samlp:Status>
```

### <a name="assertion"></a>Kontrolní výraz

Kromě `ID`, `IssueInstant` a `Version`Azure AD nastaví následující `Assertion` prvky v prvku odpovědi.

#### <a name="issuer"></a>Vystavitel

To je `https://sts.windows.net/<TenantIDGUID>/`nastavena na kde \<TenantIDGUID> je ID klienta klienta klienta klienta Azure AD klienta.

```
<Issuer>https://login.microsoftonline.com/82869000-6ad1-48f0-8171-272ed18796e9/</Issuer>
```

#### <a name="signature"></a>Podpis

Azure AD podepíše kontrolní výraz v reakci na úspěšné přihlášení. Prvek `Signature` obsahuje digitální podpis, který cloudová služba může použít k ověření zdroje k ověření integrity kontrolního výrazu.

Chcete-li generovat tento digitální podpis, Azure `IDPSSODescriptor` AD používá podpisový klíč v prvku dokumentu metadat.

```
<ds:Signature xmlns:ds="https://www.w3.org/2000/09/xmldsig#">
      digital_signature_here
    </ds:Signature>
```

#### <a name="subject"></a>Subjekt

To určuje objekt zabezpečení, který je předmětem příkazy v tvrzení. Obsahuje `NameID` prvek, který představuje ověřeného uživatele. Hodnota `NameID` je cílový identifikátor, který je určen pouze na poskytovatele služeb, který je cílovou skupinou pro token. Je trvalý - může být odvolán, ale nikdy není znovu přiřazen. Je také neprůhledné, v tom, že neodhaluje nic o uživateli a nelze použít jako identifikátor pro dotazy atributů.

Atribut `Method` `SubjectConfirmation` prvku je vždy nastaven `urn:oasis:names:tc:SAML:2.0:cm:bearer`na .

```
<Subject>
      <NameID>Uz2Pqz1X7pxe4XLWxV9KJQ+n59d573SepSAkuYKSde8=</NameID>
      <SubjectConfirmation Method="urn:oasis:names:tc:SAML:2.0:cm:bearer">
        <SubjectConfirmationData InResponseTo="id758d0ef385634593a77bdf7e632984b6" NotOnOrAfter="2013-03-18T07:43:15.144Z" Recipient="https://contoso.com/identity/inboundsso.aspx" />
      </SubjectConfirmation>
</Subject>
```

#### <a name="conditions"></a>Podmínky

Tento prvek určuje podmínky, které definují přijatelné použití kontrolnívýrazy SAML.

```
<Conditions NotBefore="2013-03-18T07:38:15.128Z" NotOnOrAfter="2013-03-18T08:48:15.128Z">
      <AudienceRestriction>
        <Audience>https://www.contoso.com</Audience>
      </AudienceRestriction>
</Conditions>
```

`NotBefore` Atributy `NotOnOrAfter` a určují interval, během kterého je kontrolní výraz platný.

* Hodnota atributu `NotBefore` je rovna nebo mírně (menší než sekunda) `IssueInstant` později `Assertion` než hodnota atributu prvku. Azure AD nezohledňuje žádný časový rozdíl mezi sebou a cloudovou službou (poskytovatelem služeb) a nepřidává žádnou vyrovnávací paměť do této doby.
* Hodnota atributu `NotOnOrAfter` je o 70 minut později než hodnota atributu. `NotBefore`

#### <a name="audience"></a>Cílová skupina

Obsahuje identifikátor URI, který identifikuje zamýšlenou cílovou skupinu. Azure AD nastaví hodnotu tohoto `Issuer` prvku na `AuthnRequest` hodnotu prvku, který inicioval přihlášení. Chcete-li `Audience` vyhodnotit hodnotu, `App ID URI` použijte hodnotu, která byla zadána při registraci aplikace.

```
<AudienceRestriction>
        <Audience>https://www.contoso.com</Audience>
</AudienceRestriction>
```

Stejně `Issuer` jako `Audience` hodnota, hodnota musí přesně odpovídat jeden z hlavních názvů služby, která představuje cloudovou službu ve službě Azure AD. Pokud však hodnota `Issuer` prvku není hodnotou IDENTIFIKÁTORURI, `Audience` hodnota v `Issuer` odpovědi je `spn:`hodnota s předponou .

#### <a name="attributestatement"></a>Atribut

Obsahuje deklarace identity týkající se subjektu nebo uživatele. Následující výňatek obsahuje `AttributeStatement` ukázkový prvek. Tři tečky označují, že prvek může obsahovat více atributů a hodnot atributů.

```
<AttributeStatement>
      <Attribute Name="http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name">
        <AttributeValue>testuser@contoso.com</AttributeValue>
      </Attribute>
      <Attribute Name="http://schemas.microsoft.com/identity/claims/objectidentifier">
        <AttributeValue>3F2504E0-4F89-11D3-9A0C-0305E82C3301</AttributeValue>
      </Attribute>
      ...
</AttributeStatement>
```        

* **Deklarace názvu** - `Name` Hodnota`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`atributu ( ) je hlavní jméno uživatele `testuser@managedtenant.com`ověřeného uživatele, například .
* **ObjectIdentifier Claim** – hodnota `ObjectIdentifier` atributu`http://schemas.microsoft.com/identity/claims/objectidentifier`( `ObjectId` ) je objekt adresáře, který představuje ověřeného uživatele ve službě Azure AD. `ObjectId`je neměnný, globálně jedinečný a znovu použít bezpečný identifikátor ověřeného uživatele.

#### <a name="authnstatement"></a>AuthnStatement

Tento prvek tvrdí, že předmět kontrolního výrazu byl ověřen určitým způsobem v určitém čase.

* Atribut `AuthnInstant` určuje čas, kdy se uživatel ověřoval pomocí služby Azure AD.
* Prvek `AuthnContext` určuje kontext ověřování použitý k ověření uživatele.

```
<AuthnStatement AuthnInstant="2013-03-18T07:33:56.000Z" SessionIndex="_bf9c623d-cc20-407a-9a59-c2d0aee84d12">
      <AuthnContext>
        <AuthnContextClassRef> urn:oasis:names:tc:SAML:2.0:ac:classes:Password</AuthnContextClassRef>
      </AuthnContext>
</AuthnStatement>
```
