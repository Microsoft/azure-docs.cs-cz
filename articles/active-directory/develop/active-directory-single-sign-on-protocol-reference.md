---
title: Azure jednotného přihlašování SAML protokol | Microsoft Docs
description: Tento článek popisuje protokol jeden znak na SAML v Azure Active Directory
services: active-directory
documentationcenter: .net
author: priyamohanram
manager: mtillman
editor: ''
ms.assetid: ad8437f5-b887-41ff-bd77-779ddafc33fb
ms.service: active-directory
ms.component: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/19/2017
ms.author: priyamo
ms.custom: aaddev
ms.openlocfilehash: ddd5fa6f2ed0878afd8bbd6399471e92dfa30385
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/14/2018
ms.locfileid: "34156700"
---
# <a name="single-sign-on-saml-protocol"></a>Jeden protokol přihlašování SAML
Tento článek popisuje požadavky na ověření SAML 2.0 a odpovědi, které podporuje Azure Active Directory (Azure AD) pro jednotné přihlašování.

Následující diagram protokolu popisuje pořadí přihlášení. Cloudové služby (service provider) používá k předání vazbu přesměrování protokolu HTTP `AuthnRequest` – element (žádosti o ověření) do služby Azure AD (zprostředkovatele identity). Azure AD poté používá HTTP post vytvoření vazby na vystavení `Response` element ke cloudové službě.

![Jednotné přihlašování pracovního postupu](media/active-directory-single-sign-on-protocol-reference/active-directory-saml-single-sign-on-workflow.png)

## <a name="authnrequest"></a>AuthnRequest
Požádat o ověřování uživatelů, cloudových služeb odeslání `AuthnRequest` element do služby Azure AD. Ukázka SAML 2.0 `AuthnRequest` může vypadat například takto:

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
| ID |povinné |Azure AD používá tento atribut naplnit `InResponseTo` atribut vrácený odpovědi. ID nesmí začínat číslicí, tak, aby společné strategie pro předřazení řetězec jako "id" řetězcovou reprezentaci identifikátor GUID. Například `id6c1c178c166d486687be4aaf5e482730` je platné ID. |
| Verze |povinné |Měl by být **2.0**. |
| IssueInstant |povinné |Toto je datum a čas řetězec s hodnotou UTC a [odezvy formátu ("o")](https://msdn.microsoft.com/library/az4se3k1.aspx). Azure AD očekává hodnotu DateTime tohoto typu, ale nemá vyhodnocení nebo použijte hodnotu. |
| AssertionConsumerServiceUrl |nepovinné |Pokud je zadán, musí odpovídat `RedirectUri` cloudové služby ve službě Azure AD. |
| ForceAuthn |nepovinné | Je to logická hodnota. V případě hodnoty true, to znamená, že uživatel musí znovu provést ověření, i v případě, že mají platný relace s Azure AD. |
| IsPassive |nepovinné |To je logická hodnota, která určuje, zda Azure AD by měl ověřit uživatele tiše, bez zásahu uživatele pomocí souboru cookie relace, pokud nějaká existuje. Pokud je to pravda, Azure AD se pokusí ověřit uživatele pomocí souboru cookie relace. |

Všechny ostatní `AuthnRequest` atributy, jako jsou souhlasu, cílový, AssertionConsumerServiceIndex, AttributeConsumerServiceIndex a ProviderName se **Ignorovat**.

Azure AD také ignoruje `Conditions` element v `AuthnRequest`.

### <a name="issuer"></a>Vystavitel
`Issuer` Element v `AuthnRequest` musí přesně shodovat s jedním z **ServicePrincipalNames** v rámci cloudové služby ve službě Azure AD. Je standardně nastavena **identifikátor ID URI aplikace** , který je určen při registraci aplikace.

Ukázkový SAML výňatek ze, který obsahuje `Issuer` element vypadá takto:

```
<Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">https://www.contoso.com</Issuer>
```

### <a name="nameidpolicy"></a>NameIDPolicy
Tento element požadavky konkrétní název formátu ID v odpovědi a je v volitelné `AuthnRequest` elementy odeslaných do služby Azure AD.

Ukázka `NameIdPolicy` element vypadá takto:

```
<NameIDPolicy Format="urn:oasis:names:tc:SAML:2.0:nameid-format:persistent"/>
```

Pokud `NameIDPolicy` je zadáno, můžete zahrnout jeho volitelné `Format` atribut. `Format` Atribut může mít jen jeden z následujících hodnot; žádné jiné hodnoty dojde k chybě.

* `urn:oasis:names:tc:SAML:2.0:nameid-format:persistent`: Azure Active Directory vystaví deklarace NameID jako pairwise identifikátor.
* `urn:oasis:names:tc:SAML:1.1:nameid-format:emailAddress`: Azure Active Directory vystaví deklarace NameID ve formátu e-mailovou adresu.
* `urn:oasis:names:tc:SAML:1.1:nameid-format:unspecified`: Tato hodnota umožňuje Azure Active Directory a vyberte formát deklarace identity. Azure Active Directory vystaví NameID jako pairwise identifikátor.
* `urn:oasis:names:tc:SAML:2.0:nameid-format:transient`: Azure Active Directory vystaví deklarace NameID jako hodnotu náhodně generované, které jsou jedinečné pro aktuální operaci jednotné přihlašování. To znamená, že hodnota je dočasný a nelze použít k identifikaci ověřování uživatele.

Azure AD ignoruje `AllowCreate` atribut.

### <a name="requestauthncontext"></a>RequestAuthnContext
`RequestedAuthnContext` Element určuje metody požadované ověřování. Zadání je volitelné v `AuthnRequest` elementy odeslaných do služby Azure AD. Azure AD podporuje pouze jeden `AuthnContextClassRef` hodnota: `urn:oasis:names:tc:SAML:2.0:ac:classes:Password`.

### <a name="scoping"></a>Obor
`Scoping` Element, který obsahuje seznam zprostředkovatelů identity, je v volitelné `AuthnRequest` elementy odeslaných do služby Azure AD.

Pokud je k dispozici, nezahrnujte `ProxyCount` atribut `IDPListOption` nebo `RequesterID` elementu, jako nejsou podporovány.

### <a name="signature"></a>Podpis
Nezahrnovat `Signature` element v `AuthnRequest` elementy, protože Azure AD nepodporují podepsané žádosti o ověření.

### <a name="subject"></a>Předmět
Azure AD ignoruje `Subject` element `AuthnRequest` elementy.

## <a name="response"></a>Odpověď
Pokud požadovaný přihlašování dokončí úspěšně, Azure AD odešle odpověď do cloudové služby. Ukázková odpověď pro úspěšný pokus přihlašování vypadá takto:

```
<samlp:Response ID="_a4958bfd-e107-4e67-b06d-0d85ade2e76a" Version="2.0" IssueInstant="2013-03-18T07:38:15.144Z" Destination="https://contoso.com/identity/inboundsso.aspx" InResponseTo="id758d0ef385634593a77bdf7e632984b6" xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
  <Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion"> https://login.microsoftonline.com/82869000-6ad1-48f0-8171-272ed18796e9/</Issuer>
  <ds:Signature xmlns:ds="http://www.w3.org/2000/09/xmldsig#">
    ...
  </ds:Signature>
  <samlp:Status>
    <samlp:StatusCode Value="urn:oasis:names:tc:SAML:2.0:status:Success" />
  </samlp:Status>
  <Assertion ID="_bf9c623d-cc20-407a-9a59-c2d0aee84d12" IssueInstant="2013-03-18T07:38:15.144Z" Version="2.0" xmlns="urn:oasis:names:tc:SAML:2.0:assertion">
    <Issuer>https://login.microsoftonline.com/82869000-6ad1-48f0-8171-272ed18796e9/</Issuer>
    <ds:Signature xmlns:ds="http://www.w3.org/2000/09/xmldsig#">
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
`Response` Element zahrnuje výsledek požadavek ověřování. Azure AD Nastaví `ID`, `Version` a `IssueInstant` hodnoty ve `Response` elementu. Nastaví také následující atributy:

* `Destination`: Při přihlašování dokončí úspěšně, je nastavena `RedirectUri` poskytovatele služeb (Cloudová služba).
* `InResponseTo`: Je nastavena `ID` atribut `AuthnRequest` element, který iniciuje odpovědi.

### <a name="issuer"></a>Vystavitel
Azure AD Nastaví `Issuer` element `https://login.microsoftonline.com/<TenantIDGUID>/` kde <TenantIDGUID> je klientské ID klienta Azure AD.

Ukázková odpověď s elementem vystavitele může například vypadat například takto:

```
<Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion"> https://login.microsoftonline.com/82869000-6ad1-48f0-8171-272ed18796e9/</Issuer>
```

### <a name="status"></a>Status
`Status` Vyjadřuje prvek úspěšná nebo neúspěšná přihlášení. Obsahuje `StatusCode` element, který obsahuje kód nebo sadu vnořené kódy, které představují stav žádosti. Zahrnuje také `StatusMessage` element, který obsahuje vlastní chybové zprávy, které jsou generovány během procesu přihlášení.

<!-- TODO: Add a authentication protocol error reference -->

Toto je odpověď SAML neúspěšný pokus o přihlášení.

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
Kromě `ID`, `IssueInstant` a `Version`, Azure AD Nastaví následující prvky v `Assertion` element odpovědi.

#### <a name="issuer"></a>Vystavitel
To je nastaven na `https://sts.windows.net/<TenantIDGUID>/`kde <TenantIDGUID> je klientské ID klienta Azure AD.

```
<Issuer>https://login.microsoftonline.com/82869000-6ad1-48f0-8171-272ed18796e9/</Issuer>
```

#### <a name="signature"></a>Podpis
Azure AD podepisuje assertion v reakci na úspěšné přihlašování. `Signature` Element obsahuje digitální podpis, cloudové služby můžete použít k ověření zdroji Ověřte integritu kontrolní výraz.

Pokud chcete vygenerovat tento digitální podpis, Azure AD používá podpisový klíč v `IDPSSODescriptor` element jeho dokument metadat.

```
<ds:Signature xmlns:ds="http://www.w3.org/2000/09/xmldsig#">
      digital_signature_here
    </ds:Signature>
```

#### <a name="subject"></a>Předmět
Toto nastavení určuje objekt, který je předmětem příkazy v kontrolní výraz. Obsahuje `NameID` element, který představuje ověřeného uživatele. `NameID` Hodnota je cílový identifikátor, který je směrované jenom na poskytovatele služeb, která je cílová skupina pro daný token. Je trvalé – se dají odvolávat, ale nikdy opětovně přiřazován. Je také neprůhledné, v, aby neodhalí nic o uživateli a nelze použít jako identifikátor pro dotazy atributů.

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
Tento element určuje podmínky, které definují podmínky použití kontrolních výrazů SAML.

```
<Conditions NotBefore="2013-03-18T07:38:15.128Z" NotOnOrAfter="2013-03-18T08:48:15.128Z">
      <AudienceRestriction>
        <Audience>https://www.contoso.com</Audience>
      </AudienceRestriction>
</Conditions>
```

`NotBefore` a `NotOnOrAfter` atributy zadejte interval, během které kontrolní výraz je neplatný.

* Hodnota `NotBefore` atribut je stejná na nebo mírně (méně než druhý) později než hodnota `IssueInstant` atribut `Assertion` elementu. Azure AD neanalyzuje žádné časový rozdíl mezi samostatně a cloudové služby (service provider) a tentokrát nepřidá všechny vyrovnávací paměti.
* Hodnota `NotOnOrAfter` atribut je 70 minut později než hodnota `NotBefore` atribut.

#### <a name="audience"></a>Cílová skupina
Tato položka obsahuje identifikátor URI identifikující cílová skupina. Azure AD Nastaví hodnotu tohoto elementu na hodnotu `Issuer` element `AuthnRequest` která inicializována přihlášení. K vyhodnocení `Audience` hodnoty, použijte hodnotu `App ID URI` zadaný během registrace aplikace.

```
<AudienceRestriction>
        <Audience>https://www.contoso.com</Audience>
</AudienceRestriction>
```

Podobně jako `Issuer` hodnota, `Audience` hodnota musí odpovídat přesně jeden z hlavních názvů služby, které představuje cloudové služby ve službě Azure AD. Ale pokud hodnotu `Issuer` element není hodnota identifikátoru URI, `Audience` je hodnota v odpovědi `Issuer` hodnotu s předponou `spn:`.

#### <a name="attributestatement"></a>AttributeStatement
Tato položka obsahuje deklarace identity o předmět nebo uživatele. Následující výpis obsahuje ukázku `AttributeStatement` elementu. Se třemi tečkami označuje, že element může obsahovat více atributy a hodnoty atributů.

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

* **Název deklarace identity** : hodnota `Name` atribut (`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`), jako je hlavní název uživatele ověřeného uživatele, `testuser@managedtenant.com`.
* **Deklarace identity které** : hodnota `ObjectIdentifier` atribut (`http://schemas.microsoft.com/identity/claims/objectidentifier`) je `ObjectId` objektu adresáře, který představuje ověřeného uživatele ve službě Azure AD. `ObjectId` je nezměnitelná, globálně jedinečný a znovu používat bezpečné identifikátor ověřeného uživatele.

#### <a name="authnstatement"></a>AuthnStatement
Tento element vyhodnotí, že subjektu assertion byla ověřena konkrétní prostředky v určitou dobu.

* `AuthnInstant` Atribut určuje dobu, kdy uživatel ověřený službou Azure AD.
* `AuthnContext` Element určuje kontext ověřování používá k ověření uživatele.

```
<AuthnStatement AuthnInstant="2013-03-18T07:33:56.000Z" SessionIndex="_bf9c623d-cc20-407a-9a59-c2d0aee84d12">
      <AuthnContext>
        <AuthnContextClassRef> urn:oasis:names:tc:SAML:2.0:ac:classes:Password</AuthnContextClassRef>
      </AuthnContext>
</AuthnStatement>
```