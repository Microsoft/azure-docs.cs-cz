---
title: Protokol SAML pro jednotné přihlašování Azure | Microsoft Docs
description: Tento článek popisuje protokol SAML jednotného přihlašování v Azure Active Directory
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
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2020
ms.locfileid: "76701395"
---
# <a name="single-sign-on-saml-protocol"></a>Protokol SAML jednotného přihlašování

Tento článek popisuje žádosti o ověření SAML 2,0 a odpovědi, které Azure Active Directory (Azure AD) podporuje pro jednotné přihlašování.

Následující diagram protokolu popisuje posloupnost jednotného přihlašování. Cloudová služba (poskytovatel služeb) používá vazbu přesměrování HTTP k předání elementu `AuthnRequest` (žádost o ověření) do služby Azure AD (zprostředkovatele identity). Azure AD potom používá vazbu HTTP POST k odeslání `Response` elementu do cloudové služby.

![Pracovní postup jednotného přihlašování](./media/single-sign-on-saml-protocol/active-directory-saml-single-sign-on-workflow.png)

## <a name="authnrequest"></a>AuthnRequest

K vyžádání ověření uživatele služba Cloud Services odešle do služby Azure AD prvek `AuthnRequest`. Ukázka `AuthnRequest` SAML 2,0 může vypadat jako v následujícím příkladu:

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
| ID | Požaduje se | Azure AD používá tento atribut k naplnění atributu `InResponseTo` vrácené odpovědi. ID nesmí začínat číslicí, takže běžnou strategií je předřadit řetězec jako "ID" do řetězcové reprezentace identifikátoru GUID. Například `id6c1c178c166d486687be4aaf5e482730` je platný identifikátor. |
| Verze | Požaduje se | Tento parametr by měl být nastaven na **2,0**. |
| IssueInstant | Požaduje se | Toto je řetězec DateTime s hodnotou UTC a [formátem Round-Trip ("o")](https://msdn.microsoft.com/library/az4se3k1.aspx). Azure AD očekává hodnotu DateTime tohoto typu, ale nevyhodnotí ani nepoužije hodnotu. |
| AssertionConsumerServiceUrl | Volitelné | Je-li tento parametr zadán, musí odpovídat `RedirectUri` cloudové služby ve službě Azure AD. |
| ForceAuthn | Volitelné | Jedná se o logickou hodnotu. Pokud má hodnotu true, znamená to, že se uživatel bude nuceně znovu ověřovat, i když má platnou relaci se službou Azure AD. |
| Podpasse | Volitelné | Jedná se o logickou hodnotu, která určuje, jestli má služba Azure AD bez zásahu uživatele ověřit uživatele bez ohledu na to, jestli existuje. Pokud je to pravda, Azure AD se pokusí ověřit uživatele pomocí souboru cookie relace. |

Všechny ostatní atributy `AuthnRequest`, jako je například souhlas, cíl, AssertionConsumerServiceIndex, AttributeConsumerServiceIndex a ProviderName, budou **ignorovány**.

Azure AD také ignoruje `Conditions` element v `AuthnRequest`.

### <a name="issuer"></a>Vystavitel

Element `Issuer` v `AuthnRequest` musí přesně odpovídat jednomu z **ServicePrincipalNames** v cloudové službě v Azure AD. Obvykle je tento parametr nastavený na **identifikátor URI ID aplikace** , který je zadaný při registraci aplikace.

Úryvek SAML obsahující `Issuer` element vypadá jako v následujícím příkladu:

```
<Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">https://www.contoso.com</Issuer>
```

### <a name="nameidpolicy"></a>NameIDPolicy

Tento prvek vyžaduje v odpovědi konkrétní formát ID názvu a je volitelný v `AuthnRequest` prvků odeslaných do služby Azure AD.

`NameIdPolicy` element vypadá jako v následujícím příkladu:

```
<NameIDPolicy Format="urn:oasis:names:tc:SAML:2.0:nameid-format:persistent"/>
```

Pokud `NameIDPolicy` k dispozici, můžete zahrnout svůj volitelný `Format` atribut. Atribut `Format` může mít pouze jednu z následujících hodnot: jakákoli jiná hodnota má za následek chybu.

* `urn:oasis:names:tc:SAML:2.0:nameid-format:persistent`: Azure Active Directory vydá deklaraci NameID jako párový identifikátor.
* `urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress`: Azure Active Directory vydá deklaraci identity NameID ve formátu e-mailové adresy.
* `urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified`: Tato hodnota povoluje Azure Active Directory výběr formátu deklarace identity. Azure Active Directory vydá NameID jako párový identifikátor.
* `urn:oasis:names:tc:SAML:2.0:nameid-format:transient`: Azure Active Directory vystaví deklaraci identity NameID jako náhodně generovanou hodnotu, která je jedinečná pro aktuální operaci jednotného přihlašování. To znamená, že hodnota je dočasná a nelze ji použít k identifikaci ověřovacího uživatele.

Služba Azure AD ignoruje atribut `AllowCreate`.

### <a name="requestauthncontext"></a>RequestAuthnContext
Element `RequestedAuthnContext` určuje požadované metody ověřování. Je volitelný v `AuthnRequest` prvky odesílané do služby Azure AD. Azure AD podporuje `AuthnContextClassRef` hodnoty, jako je například `urn:oasis:names:tc:SAML:2.0:ac:classes:Password`.

### <a name="scoping"></a>Rozsah
Element `Scoping`, který obsahuje seznam zprostředkovatelů identity, je volitelný v `AuthnRequest` elementy odeslaných do služby Azure AD.

Pokud je tato vlastnost k dispozici, nezahrnujte atribut `ProxyCount`, `IDPListOption` nebo `RequesterID` elementu, protože nejsou podporovány.

### <a name="signature"></a>Podpis
Nezahrnovat `Signature` element do `AuthnRequest` prvků, protože služba Azure AD nepodporuje podepsané žádosti o ověření.

### <a name="subject"></a>Předmět
Služba Azure AD ignoruje `Subject` element `AuthnRequest` prvků.

## <a name="response"></a>Odpověď
Až se požadované přihlášení úspěšně dokončí, Azure AD odešle odpověď do cloudové služby. Odpověď na úspěšný pokus o přihlášení vypadá jako v následující ukázce:

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

Element `Response` zahrnuje výsledek žádosti o autorizaci. Azure AD Nastaví `ID`, `Version` a `IssueInstant` hodnoty v elementu `Response`. Také nastavuje následující atributy:

* `Destination`: po úspěšném přihlášení se tato nastavení nastaví na `RedirectUri` poskytovatele služeb (cloudová služba).
* `InResponseTo`: Tato vlastnost je nastavena na atribut `ID` prvku `AuthnRequest`, který inicioval odpověď.

### <a name="issuer"></a>Vystavitel

Azure AD Nastaví element `Issuer` na `https://login.microsoftonline.com/<TenantIDGUID>/`, kde \<TenantIDGUID > je ID tenanta tenanta Azure AD.

Například odpověď s prvkem vystavitele může vypadat jako v následujícím příkladu:

```
<Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion"> https://login.microsoftonline.com/82869000-6ad1-48f0-8171-272ed18796e9/</Issuer>
```

### <a name="status"></a>Stav

Prvek `Status` předává úspěch nebo neúspěch přihlášení. Obsahuje prvek `StatusCode`, který obsahuje kód nebo sadu vnořených kódů, které představují stav žádosti. Zahrnuje také prvek `StatusMessage`, který obsahuje vlastní chybové zprávy, které jsou generovány během procesu přihlašování.

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

Kromě `ID`, `IssueInstant` a `Version`, služba Azure AD nastaví následující prvky v `Assertion` prvku odpovědi.

#### <a name="issuer"></a>Vystavitel

To je nastavené na `https://sts.windows.net/<TenantIDGUID>/`, kde \<TenantIDGUID > je ID tenanta tenanta Azure AD.

```
<Issuer>https://login.microsoftonline.com/82869000-6ad1-48f0-8171-272ed18796e9/</Issuer>
```

#### <a name="signature"></a>Podpis

Služba Azure AD podepíše kontrolní výraz jako odpověď na úspěšné přihlášení. Element `Signature` obsahuje digitální podpis, který může cloudová služba použít k ověření zdroje k ověření integrity kontrolního výrazu.

K vygenerování tohoto digitálního podpisu Azure AD používá podpisový klíč v elementu `IDPSSODescriptor` svého dokumentu metadat.

```
<ds:Signature xmlns:ds="https://www.w3.org/2000/09/xmldsig#">
      digital_signature_here
    </ds:Signature>
```

#### <a name="subject"></a>Předmět

Určuje objekt zabezpečení, který je předmětem příkazů v kontrolním výrazu. Obsahuje `NameID` element, který představuje ověřeného uživatele. Hodnota `NameID` je cílový identifikátor, který je směrován pouze k poskytovateli služeb, který je cílovou skupinou pro daný token. Je trvalý – dá se odvolat, ale nikdy se znovu nepřiřazuje. Je také neprůhledný, v tom případě neodhalí žádné informace o uživateli a nelze jej použít jako identifikátor pro dotazy na atributy.

Atribut `Method` elementu `SubjectConfirmation` je vždy nastaven na `urn:oasis:names:tc:SAML:2.0:cm:bearer`.

```
<Subject>
      <NameID>Uz2Pqz1X7pxe4XLWxV9KJQ+n59d573SepSAkuYKSde8=</NameID>
      <SubjectConfirmation Method="urn:oasis:names:tc:SAML:2.0:cm:bearer">
        <SubjectConfirmationData InResponseTo="id758d0ef385634593a77bdf7e632984b6" NotOnOrAfter="2013-03-18T07:43:15.144Z" Recipient="https://contoso.com/identity/inboundsso.aspx" />
      </SubjectConfirmation>
</Subject>
```

#### <a name="conditions"></a>Podmínky

Tento prvek určuje podmínky, které definují přijatelné použití kontrolních výrazů SAML.

```
<Conditions NotBefore="2013-03-18T07:38:15.128Z" NotOnOrAfter="2013-03-18T08:48:15.128Z">
      <AudienceRestriction>
        <Audience>https://www.contoso.com</Audience>
      </AudienceRestriction>
</Conditions>
```

Atributy `NotBefore` a `NotOnOrAfter` určují interval, během kterého je kontrolní výraz platný.

* Hodnota atributu `NotBefore` je rovna nebo mírně (méně než druhá) později než hodnota atributu `IssueInstant` elementu `Assertion`. Azure AD nezohledňuje žádný časový rozdíl mezi sebou samými a cloudovou službou (poskytovatelem služeb) a do této doby nepřidává žádnou vyrovnávací paměť.
* Hodnota atributu `NotOnOrAfter` je 70 minut později než hodnota atributu `NotBefore`.

#### <a name="audience"></a>Cílová skupina

Obsahuje identifikátor URI, který identifikuje zamýšlenou cílovou skupinu. Azure AD nastaví hodnotu tohoto prvku na hodnotu `Issuer` elementu `AuthnRequest`, která iniciovala přihlašování. Chcete-li vyhodnotit `Audience` hodnotu, použijte hodnotu `App ID URI`, která byla zadána při registraci aplikace.

```
<AudienceRestriction>
        <Audience>https://www.contoso.com</Audience>
</AudienceRestriction>
```

Podobně jako u `Issuer` hodnoty musí `Audience` hodnota přesně odpovídat jednomu z hlavních názvů služby, které představují cloudovou službu ve službě Azure AD. Pokud však hodnota `Issuer`ho prvku není hodnotou identifikátoru URI, hodnota `Audience` v odpovědi je hodnota `Issuer` s předponou `spn:`.

#### <a name="attributestatement"></a>AttributeStatement

Obsahuje deklarace identity týkající se předmětu nebo uživatele. Následující úryvek obsahuje vzorový `AttributeStatement` element. Tři tečky označují, že element může zahrnovat více atributů a hodnot atributů.

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

* **Deklarace identity Name** – hodnota atributu `Name` (`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`) je hlavní název uživatele ověřeného uživatele, například `testuser@managedtenant.com`.
* **Které ObjectIdentifier deklarace identity** – hodnota atributu `ObjectIdentifier` (`http://schemas.microsoft.com/identity/claims/objectidentifier`) je `ObjectId` objekt adresáře, který představuje ověřeného uživatele v Azure AD. `ObjectId` je neměnný, globálně jedinečný a znovu používá bezpečný identifikátor ověřeného uživatele.

#### <a name="authnstatement"></a>AuthnStatement

Tento prvek vyhodnotí, že předmět kontrolního výrazu byl v určitou dobu ověřen určitým prostředkem.

* Atribut `AuthnInstant` určuje čas, kdy se uživatel ověřil ve službě Azure AD.
* Element `AuthnContext` určuje kontext ověřování, který se používá k ověření uživatele.

```
<AuthnStatement AuthnInstant="2013-03-18T07:33:56.000Z" SessionIndex="_bf9c623d-cc20-407a-9a59-c2d0aee84d12">
      <AuthnContext>
        <AuthnContextClassRef> urn:oasis:names:tc:SAML:2.0:ac:classes:Password</AuthnContextClassRef>
      </AuthnContext>
</AuthnStatement>
```
