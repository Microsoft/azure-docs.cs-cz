---
title: Protokol SAML pro jednotné přihlašování Azure
titleSuffix: Microsoft identity platform
description: Tento článek popisuje protokol SAML jednotného Sign-On (SSO) v Azure Active Directory
services: active-directory
documentationcenter: .net
author: kenwith
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 05/18/2020
ms.author: kenwith
ms.custom: aaddev
ms.reviewer: paulgarn
ms.openlocfilehash: 40bf202e0f14f18d817e4e918f8372ba3c0a4ad8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "91950665"
---
# <a name="single-sign-on-saml-protocol"></a>Protokol SAML pro jednotné přihlašování

Tento článek popisuje žádosti o ověření SAML 2,0 a odpovědi, které Azure Active Directory (Azure AD) podporuje pro jednotné Sign-On (SSO).

Následující diagram protokolu popisuje posloupnost jednotného přihlašování. Cloudová služba (poskytovatel služeb) používá vazbu přesměrování HTTP k předání `AuthnRequest` elementu (žádosti o ověření) do služby Azure AD (zprostředkovatele identity). Azure AD potom používá vazbu HTTP POST k odeslání `Response` elementu do cloudové služby.

![Pracovní postup jednotného Sign-On (SSO)](./media/single-sign-on-saml-protocol/active-directory-saml-single-sign-on-workflow.png)

> [!NOTE]
> Tento článek popisuje použití SAML pro jednotné přihlašování. Další informace o dalších způsobech zpracování jednotného přihlašování (například pomocí OpenID Connect nebo integrovaného ověřování systému Windows) najdete v tématu [jednotné přihlašování k aplikacím v Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="authnrequest"></a>AuthnRequest

Aby bylo možné požádat o ověření uživatele, služba Cloud Services odešle `AuthnRequest` do služby Azure AD element. Ukázka SAML 2,0 `AuthnRequest` může vypadat jako v následujícím příkladu:

```
<samlp:AuthnRequest
xmlns="urn:oasis:names:tc:SAML:2.0:metadata"
ID="id6c1c178c166d486687be4aaf5e482730"
Version="2.0" IssueInstant="2013-03-18T03:28:54.1839884Z"
xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
<Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">https://www.contoso.com</Issuer>
</samlp:AuthnRequest>
```

| Parametr | Typ | Popis |
| --- | --- | --- |
| ID | Vyžadováno | Azure AD používá tento atribut k naplnění `InResponseTo` atributu vrácené odpovědi. ID nesmí začínat číslicí, takže běžnou strategií je předřadit řetězec jako "ID" do řetězcové reprezentace identifikátoru GUID. Například `id6c1c178c166d486687be4aaf5e482730` je platný identifikátor. |
| Verze | Vyžadováno | Tento parametr by měl být nastaven na **2,0**. |
| IssueInstant | Vyžadováno | Toto je řetězec DateTime s hodnotou UTC a [formátem Round-Trip ("o")](/dotnet/standard/base-types/standard-date-and-time-format-strings). Azure AD očekává hodnotu DateTime tohoto typu, ale nevyhodnotí ani nepoužije hodnotu. |
| AssertionConsumerServiceURL | Volitelné | Je-li tento parametr zadán, musí odpovídat `RedirectUri` cloudové službě ve službě Azure AD. |
| ForceAuthn | Volitelné | Jedná se o logickou hodnotu. Pokud má hodnotu true, znamená to, že se uživatel bude nuceně znovu ověřovat, i když má platnou relaci se službou Azure AD. |
| Podpasse | Volitelné | Jedná se o logickou hodnotu, která určuje, jestli má služba Azure AD bez zásahu uživatele ověřit uživatele bez ohledu na to, jestli existuje. Pokud je to pravda, Azure AD se pokusí ověřit uživatele pomocí souboru cookie relace. |

Všechny ostatní `AuthnRequest` atributy, jako je například souhlas, cíl, AssertionConsumerServiceIndex, AttributeConsumerServiceIndex a ProviderName, budou **ignorovány**.

Azure AD také ignoruje `Conditions` prvek v `AuthnRequest` .

### <a name="issuer"></a>Vystavitel

`Issuer`Element v elementu `AuthnRequest` musí přesně odpovídat jednomu z **ServicePrincipalNames** v cloudové službě v Azure AD. Obvykle je tento parametr nastavený na **identifikátor URI ID aplikace** , který je zadaný při registraci aplikace.

Úryvek SAML obsahující `Issuer` element vypadá jako v následujícím příkladu:

```
<Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">https://www.contoso.com</Issuer>
```

### <a name="nameidpolicy"></a>NameIDPolicy

Tento element vyžaduje v odpovědi konkrétní formát ID názvu a je volitelný v `AuthnRequest` prvcích odeslaných do služby Azure AD.

`NameIdPolicy`Element vypadá jako v následujícím příkladu:

```
<NameIDPolicy Format="urn:oasis:names:tc:SAML:2.0:nameid-format:persistent"/>
```

Pokud `NameIDPolicy` je k dispozici, můžete zahrnout její nepovinný `Format` atribut. `Format`Atribut může mít pouze jednu z následujících hodnot. Výsledkem jakékoli jiné hodnoty je chyba.

* `urn:oasis:names:tc:SAML:2.0:nameid-format:persistent`: Azure Active Directory vydá deklaraci NameID jako párový identifikátor.
* `urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress`: Azure Active Directory vydá deklaraci identity NameID ve formátu e-mailové adresy.
* `urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified`: Tato hodnota povoluje Azure Active Directory pro výběr formátu deklarace identity. Azure Active Directory vydá NameID jako párový identifikátor.
* `urn:oasis:names:tc:SAML:2.0:nameid-format:transient`: Azure Active Directory vydá deklaraci identity NameID jako náhodně generovanou hodnotu, která je jedinečná pro aktuální operaci jednotného přihlašování. To znamená, že hodnota je dočasná a nelze ji použít k identifikaci ověřovacího uživatele.

`SPNameQualifier`Je-li parametr zadán, bude služba Azure AD `SPNameQualifier` v odpovědi obsahovat stejnou hodnotu.

Služba Azure AD ignoruje `AllowCreate` atribut.

### <a name="requestauthncontext"></a>RequestAuthnContext
`RequestedAuthnContext`Prvek určuje požadované metody ověřování. `AuthnRequest`Prvky odesílané do služby Azure AD jsou volitelné. Azure AD podporuje `AuthnContextClassRef` hodnoty, jako je například `urn:oasis:names:tc:SAML:2.0:ac:classes:Password` .

### <a name="scoping"></a>Rozsah
`Scoping`Element, který obsahuje seznam zprostředkovatelů identity, je volitelný v `AuthnRequest` prvcích odeslaných do služby Azure AD.

Pokud je tato vlastnost k dispozici, nezahrnujte `ProxyCount` atribut `IDPListOption` ani `RequesterID` element, protože nejsou podporovány.

### <a name="signature"></a>Podpis
`Signature`Element v `AuthnRequest` elementech je nepovinný. Služba Azure AD neověřuje podepsané žádosti o ověření, pokud je k dispozici podpis. Ověření žadatele je poskytováno pouze v reakci na registrované adresy URL služby kontrolního výrazu.

### <a name="subject"></a>Předmět
Nezahrnujte `Subject` element. Azure AD nepodporuje zadání předmětu pro požadavek a vrátí chybu, pokud je k dispozici.

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

`Response`Element zahrnuje výsledek autorizační žádosti. Azure AD nastaví `ID` `Version` `IssueInstant` hodnoty a v `Response` elementu. Také nastavuje následující atributy:

* `Destination`: Po úspěšném dokončení přihlašování se nastaví jako `RedirectUri` poskytovatel služeb (cloudová služba).
* `InResponseTo`: Toto je nastaveno na `ID` atribut `AuthnRequest` prvku, který inicioval odpověď.

### <a name="issuer"></a>Vystavitel

Azure AD nastaví `Issuer` element na `https://sts.windows.net/<TenantIDGUID>/` , kde \<TenantIDGUID> je ID TENANTA tenanta Azure AD.

Například odpověď s prvkem vystavitele může vypadat jako v následujícím příkladu:

```
<Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion"> https://sts.windows.net/82869000-6ad1-48f0-8171-272ed18796e9/</Issuer>
```

### <a name="status"></a>Status

Prvek předává `Status` úspěch nebo neúspěch přihlášení. Obsahuje `StatusCode` element, který obsahuje kód nebo sadu vnořených kódů, které představují stav žádosti. Zahrnuje také `StatusMessage` element, který obsahuje vlastní chybové zprávy, které jsou generovány během procesu přihlašování.

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

Kromě `ID` `IssueInstant` a `Version` Služba Azure AD nastavuje následující prvky v `Assertion` elementu odpovědi.

#### <a name="issuer"></a>Vystavitel

To je nastavené na `https://sts.windows.net/<TenantIDGUID>/` místo, kde \<TenantIDGUID> je ID tenanta TENANTA Azure AD.

```
<Issuer>https://sts.windows.net/82869000-6ad1-48f0-8171-272ed18796e9/</Issuer>
```

#### <a name="signature"></a>Podpis

Služba Azure AD podepíše kontrolní výraz jako odpověď na úspěšné přihlášení. `Signature`Element obsahuje digitální podpis, který může cloudová služba použít k ověření zdroje k ověření integrity kontrolního výrazu.

K vygenerování tohoto digitálního podpisu Azure AD používá podpisový klíč v `IDPSSODescriptor` elementu svého dokumentu metadat.

```
<ds:Signature xmlns:ds="https://www.w3.org/2000/09/xmldsig#">
      digital_signature_here
    </ds:Signature>
```

#### <a name="subject"></a>Předmět

Určuje objekt zabezpečení, který je předmětem příkazů v kontrolním výrazu. Obsahuje `NameID` element, který představuje ověřeného uživatele. `NameID`Hodnota je cílový identifikátor, který je směrován pouze k poskytovateli služeb, který je cílovou skupinou pro daný token. Je trvalý – dá se odvolat, ale nikdy se znovu nepřiřazuje. Je také neprůhledný, v tom případě neodhalí žádné informace o uživateli a nelze jej použít jako identifikátor pro dotazy na atributy.

`Method`Atribut `SubjectConfirmation` elementu je vždy nastaven na `urn:oasis:names:tc:SAML:2.0:cm:bearer` .

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

`NotBefore`Atributy a `NotOnOrAfter` určují interval, během kterého je kontrolní výraz platný.

* Hodnota `NotBefore` atributu je rovna nebo mírně (méně než sekunda) později než hodnota `IssueInstant` atributu `Assertion` elementu. Azure AD nezohledňuje žádný časový rozdíl mezi sebou samými a cloudovou službou (poskytovatelem služeb) a do této doby nepřidává žádnou vyrovnávací paměť.
* Hodnota `NotOnOrAfter` atributu je 70 minut později než hodnota `NotBefore` atributu.

#### <a name="audience"></a>Cílová skupina

Obsahuje identifikátor URI, který identifikuje zamýšlenou cílovou skupinu. Azure AD nastaví hodnotu tohoto prvku na hodnotu `Issuer` elementu `AuthnRequest` , která iniciovala přihlašování. Pro vyhodnocení `Audience` hodnoty použijte hodnotu `App ID URI` , která byla zadána při registraci aplikace.

```
<AudienceRestriction>
        <Audience>https://www.contoso.com</Audience>
</AudienceRestriction>
```

Podobně jako `Issuer` hodnota se `Audience` hodnota musí přesně shodovat s jedním z hlavních názvů služby, které představují cloudovou službu ve službě Azure AD. Nicméně pokud hodnota `Issuer` elementu není hodnota identifikátoru URI, `Audience` hodnota v odpovědi je `Issuer` hodnota s předponou `spn:` .

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

* **Deklarace identity Name** – hodnota `Name` atributu ( `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` ) je hlavní název uživatele ověřeného uživatele, například `testuser@managedtenant.com` .
* **Které ObjectIdentifier deklarace identity** – hodnota `ObjectIdentifier` atributu ( `http://schemas.microsoft.com/identity/claims/objectidentifier` ) je `ObjectId` objekt adresáře, který představuje ověřeného uživatele ve službě Azure AD. `ObjectId` je neměnný, globálně jedinečný a znovu používá bezpečný identifikátor ověřeného uživatele.

#### <a name="authnstatement"></a>AuthnStatement

Tento prvek vyhodnotí, že předmět kontrolního výrazu byl v určitou dobu ověřen určitým prostředkem.

* `AuthnInstant`Atribut určuje čas, kdy se uživatel ověřil ve službě Azure AD.
* `AuthnContext`Element určuje kontext ověřování použitý k ověření uživatele.

```
<AuthnStatement AuthnInstant="2013-03-18T07:33:56.000Z" SessionIndex="_bf9c623d-cc20-407a-9a59-c2d0aee84d12">
      <AuthnContext>
        <AuthnContextClassRef> urn:oasis:names:tc:SAML:2.0:ac:classes:Password</AuthnContextClassRef>
      </AuthnContext>
</AuthnStatement>
```
