---
title: Azure jednotného přihlašování SAML protokol | Dokumentace Microsoftu
description: Tento článek popisuje, protokol jednotné přihlašování na SAML v Azure Active Directory
services: active-directory
documentationcenter: .net
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: ad8437f5-b887-41ff-bd77-779ddafc33fb
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/19/2017
ms.author: celested
ms.custom: aaddev
ms.reviewer: hirsin
ms.openlocfilehash: dd9bdc4638d1c055706026798acba08d6add08c7
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/28/2019
ms.locfileid: "55098744"
---
# <a name="single-sign-on-saml-protocol"></a>Protokol pro jednotné přihlašování – SAML

Tento článek se týká požadavků na ověření SAML 2.0 a odpovědi, které podporuje Azure Active Directory (Azure AD) pro jednotné přihlašování.

Následující diagram protokolu popisuje pořadí jednotné přihlašování. Cloudové služby (service provider) používá k předání vazbu přesměrování protokolu HTTP `AuthnRequest` – element (žádost o ověření) do služby Azure AD (zprostředkovatele identity). Azure AD pak používá HTTP post vazby k publikování `Response` element ke cloudové službě.

![Jednotné přihlašování pro pracovní postup](./media/single-sign-on-saml-protocol/active-directory-saml-single-sign-on-workflow.png)

## <a name="authnrequest"></a>AuthnRequest

Požádat o ověřování uživatelů, využití cloudových služeb odeslat `AuthnRequest` element do služby Azure AD. Ukázkový protokol SAML 2.0 `AuthnRequest` by měl vypadat jako v následujícím příkladu:

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
| ID | Požaduje se | Azure AD používá tento atribut naplnit `InResponseTo` atribut vrácené odpovědi. ID nesmí začínat číslicí, takže běžných strategií je předřaďte řetězec jako "id" na řetězcové vyjádření identifikátoru GUID. Například `id6c1c178c166d486687be4aaf5e482730` je platné ID. |
| Verze | Požaduje se | Tento parametr by měl být nastaven na **2.0**. |
| IssueInstant | Požaduje se | Toto je řetězec data a času s hodnotou UTC a [("o") formátu round-trip](https://msdn.microsoft.com/library/az4se3k1.aspx). Azure AD očekává, že hodnota data a času tohoto typu, ale nebude vyhodnocení nebo použijte hodnotu. |
| AssertionConsumerServiceUrl | Nepovinné | Pokud je zadán, musí odpovídat tento parametr `RedirectUri` cloudové služby ve službě Azure AD. |
| ForceAuthn | Nepovinné | To je logická hodnota. Pokud je hodnota true, znamená to, že uživatel bude muset znovu ověřit, i v případě, že mají platný relace s Azure AD. |
| IsPassive | Nepovinné | To je logická hodnota, která určuje, zda služby Azure AD by měl ověřit uživatele tiše, bez zásahu uživatele, pomocí souboru cookie relace, pokud existuje. Pokud je to pravda, Azure AD se pokusí ověřit uživatele pomocí souboru cookie relace. |

Všechny ostatní `AuthnRequest` atributy, jako jsou souhlasu, cíl, AssertionConsumerServiceIndex, AttributeConsumerServiceIndex a ProviderName **ignoruje**.

Azure AD také ignoruje `Conditions` prvek `AuthnRequest`.

### <a name="issuer"></a>Vystavitel

`Issuer` Prvek `AuthnRequest` musí přesně odpovídat jedné ze **ServicePrincipalNames** v cloudové službě ve službě Azure AD. Obvykle je nastavené na **identifikátor ID URI aplikace** , který je uveden při registraci aplikace.

Výňatek ze SAML obsahující `Issuer` element vypadá jako v následující ukázce:

```
<Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">https://www.contoso.com</Issuer>
```

### <a name="nameidpolicy"></a>NameIDPolicy

Tento prvek požaduje formátu konkrétní název ID v odpovědi a je volitelné v `AuthnRequest` prvky posílá službě Azure AD.

A `NameIdPolicy` element vypadá jako v následující ukázce:

```
<NameIDPolicy Format="urn:oasis:names:tc:SAML:2.0:nameid-format:persistent"/>
```

Pokud `NameIDPolicy` je k dispozici, můžete zahrnout jeho volitelné `Format` atribut. `Format` Atribut může mít pouze jednu z následujících hodnot; žádné další hodnota dojde k chybě.

* `urn:oasis:names:tc:SAML:2.0:nameid-format:persistent`: Azure Active Directory vystaví deklarace identity NameID jako pairwise identifikátor.
* `urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress`: Azure Active Directory vystaví deklarace identity NameID ve formátu e-mailové adresy.
* `urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified`: Tato hodnota umožňuje Azure Active Directory a vyberte formát deklarace identity. Azure Active Directory vystaví NameID jako pairwise identifikátor.
* `urn:oasis:names:tc:SAML:2.0:nameid-format:transient`: Azure Active Directory vystaví deklarace identity NameID jako náhodně generované hodnoty, které jsou jedinečné pro aktuální operaci správy jednotného přihlašování. To znamená, že hodnota je dočasný a nelze použít k identifikaci ověřování uživatele.

Azure AD, ignoruje `AllowCreate` atribut.

### <a name="requestauthncontext"></a>RequestAuthnContext
`RequestedAuthnContext` Prvek určuje požadované ověřovací metody. Zadání je volitelné v `AuthnRequest` prvky posílá službě Azure AD. Azure AD podporuje pouze jeden `AuthnContextClassRef` hodnota: `urn:oasis:names:tc:SAML:2.0:ac:classes:Password`.

### <a name="scoping"></a>Vytváření oborů
`Scoping` Element, který obsahuje seznam zprostředkovatelů identity, je v volitelné `AuthnRequest` prvky posílá službě Azure AD.

Jsou-li zadán, `ProxyCount` atribut, `IDPListOption` nebo `RequesterID` element, protože nejsou podporovány.

### <a name="signature"></a>Podpis
Nejsou zahrnuté `Signature` prvek `AuthnRequest` prvky, jako je Azure AD nepodporuje podepsané žádosti o ověření.

### <a name="subject"></a>Subjekt
Azure AD ignoruje `Subject` prvek `AuthnRequest` elementy.

## <a name="response"></a>Odpověď
Při požadovaný přihlašování dokončí úspěšně, Azure AD odešle odpověď do cloudové služby. Odpověď úspěšný pokus přihlašování bude vypadat jako v následující ukázce:

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

`Response` Element obsahuje výsledek žádost o autorizaci. Sady Azure AD `ID`, `Version` a `IssueInstant` hodnoty v `Response` elementu. Nastaví také následující atributy:

* `Destination`: Při přihlašování dokončí úspěšně, je nastavené na `RedirectUri` poskytovatele služeb (cloudové služby).
* `InResponseTo`: Je nastavené na `ID` atribut `AuthnRequest` element, který iniciuje odpovědi.

### <a name="issuer"></a>Vystavitel

Sady Azure AD `Issuer` elementu `https://login.microsoftonline.com/<TenantIDGUID>/` kde <TenantIDGUID> je tenant ID tenanta Azure AD.

Odpověď s elementem vystavitele může například vypadat jako v následující ukázce:

```
<Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion"> https://login.microsoftonline.com/82869000-6ad1-48f0-8171-272ed18796e9/</Issuer>
```

### <a name="status"></a>Status

`Status` Element přenáší úspěch nebo neúspěch přihlašování. Její součástí `StatusCode` element, který bude obsahovat kód nebo sadu vnořených kódů, který představuje stav žádosti. Zahrnuje také `StatusMessage` element, který obsahuje vlastní chybové zprávy, které jsou generovány během procesu přihlašování.

<!-- TODO: Add a authentication protocol error reference -->

Následující příklad je odpověď SAML neúspěšný pokus o přihlášení.

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

### <a name="assertion"></a>kontrolní výraz

Kromě `ID`, `IssueInstant` a `Version`, Azure AD Nastaví následující prvky v `Assertion` prvek odpovědi.

#### <a name="issuer"></a>Vystavitel

Je nastavené na `https://sts.windows.net/<TenantIDGUID>/`kde <TenantIDGUID> je Tenant ID tenanta Azure AD.

```
<Issuer>https://login.microsoftonline.com/82869000-6ad1-48f0-8171-272ed18796e9/</Issuer>
```

#### <a name="signature"></a>Podpis

Azure AD podepisuje kontrolního výrazu v reakci na úspěšném přihlášení. `Signature` Prvek obsahuje digitální podpis, který cloudovou službu můžete použít k ověření zdroje k ověření integrity kontrolního výrazu.

Ke generování digitálního podpisu, používá podpisový klíč v Azure AD `IDPSSODescriptor` element dokumentu jeho metadata.

```
<ds:Signature xmlns:ds="https://www.w3.org/2000/09/xmldsig#">
      digital_signature_here
    </ds:Signature>
```

#### <a name="subject"></a>Subjekt

Určuje objekt, který je předmětem příkazy v kontrolního výrazu. Obsahuje `NameID` element, který představuje ověřeného uživatele. `NameID` Hodnota je cílový identifikátor, který se přesměruje pouze k poskytovateli služby, která je cílová skupina pro daný token. Je trvalé – možné odvolat, ale nikdy je přiřazena. Je také neprůhledný, neodhalí nic o uživateli a nelze použít jako identifikátor pro dotazy atributů.

`Method` Atribut `SubjectConfirmation` element je vždycky nastavený na `urn:oasis:names:tc:SAML:2.0:cm:bearer`.

```
<Subject>
      <NameID>Uz2Pqz1X7pxe4XLWxV9KJQ+n59d573SepSAkuYKSde8=</NameID>
      <SubjectConfirmation Method="urn:oasis:names:tc:SAML:2.0:cm:bearer">
        <SubjectConfirmationData InResponseTo="id758d0ef385634593a77bdf7e632984b6" NotOnOrAfter="2013-03-18T07:43:15.144Z" Recipient="https://contoso.com/identity/inboundsso.aspx" />
      </SubjectConfirmation>
</Subject>
```

#### <a name="conditions"></a>Podmínky

Tento prvek určuje podmínky, které definují podmínky použití kontrolní výrazy SAML.

```
<Conditions NotBefore="2013-03-18T07:38:15.128Z" NotOnOrAfter="2013-03-18T08:48:15.128Z">
      <AudienceRestriction>
        <Audience>https://www.contoso.com</Audience>
      </AudienceRestriction>
</Conditions>
```

`NotBefore` a `NotOnOrAfter` atributy zadejte interval, během které je platný výraz.

* Hodnota `NotBefore` atributu rovná položky nebo mírně (menší než druhý) vyšší než hodnota `IssueInstant` atribut `Assertion` elementu. Azure AD nezodpovídá za jakékoli časový rozdíl mezi samostatně a cloudové služby (service provider) a tentokrát nepřidá všechny vyrovnávací paměti.
* Hodnota `NotOnOrAfter` atribut je vyšší než hodnota 70 minut `NotBefore` atribut.

#### <a name="audience"></a>Cílová skupina

Tato položka obsahuje identifikátor URI identifikující zamýšlenou cílovou skupinou. Azure AD Nastaví hodnota tohoto prvku na hodnotu `Issuer` elementu `AuthnRequest` , která iniciovala přihlašování. K vyhodnocení `Audience` hodnotu, použijte hodnotu `App ID URI` , který byl zadán během registrace aplikace.

```
<AudienceRestriction>
        <Audience>https://www.contoso.com</Audience>
</AudienceRestriction>
```

Podobně jako `Issuer` hodnota, `Audience` hodnota musí odpovídat přesně jeden z hlavních názvů služby, které představuje cloudovou službu ve službě Azure AD. Ale pokud hodnotu `Issuer` prvek není hodnota identifikátoru URI, `Audience` hodnotu v odpovědi `Issuer` předponu hodnotu `spn:`.

#### <a name="attributestatement"></a>AttributeStatement

Obsahuje deklarace identity o předmětu nebo uživatele. Následující úryvek obsahuje vzorek `AttributeStatement` elementu. Symbol tří teček označuje, že element může obsahovat více atributy a hodnoty atributů.

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

* **Název deklarace identity** -hodnotu `Name` atribut (`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`), jako je hlavní název uživatele ověřeného uživatele `testuser@managedtenant.com`.
* **Které deklarace identity** -hodnotu `ObjectIdentifier` atribut (`http://schemas.microsoft.com/identity/claims/objectidentifier`) je `ObjectId` objektu adresáře, který představuje ověřeného uživatele ve službě Azure AD. `ObjectId` je neměnný, globálně jedinečný a znovu použít bezpečné identifikátor ověřeného uživatele.

#### <a name="authnstatement"></a>AuthnStatement

Tento prvek vyhodnotí, že došlo k předmětu kontrolního výrazu ověření konkrétní prostředky v určitou dobu.

* `AuthnInstant` Atribut určuje čas, kdy uživatel ověřený službou Azure AD.
* `AuthnContext` Prvek určuje kontext ověřování používá k ověření uživatele.

```
<AuthnStatement AuthnInstant="2013-03-18T07:33:56.000Z" SessionIndex="_bf9c623d-cc20-407a-9a59-c2d0aee84d12">
      <AuthnContext>
        <AuthnContextClassRef> urn:oasis:names:tc:SAML:2.0:ac:classes:Password</AuthnContextClassRef>
      </AuthnContext>
</AuthnStatement>
```
