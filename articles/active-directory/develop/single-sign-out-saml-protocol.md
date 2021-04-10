---
title: Protokol SAML pro jednotné odhlašování v Azure
description: Tento článek popisuje jeden Sign-Out protokol SAML v Azure Active Directory
services: active-directory
author: kenwith
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 03/22/2021
ms.author: kenwith
ms.custom: aaddev
ms.reviewer: paulgarn
ms.openlocfilehash: 040e49ee870704107e1d4118f1484119d98a9ebf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "104781207"
---
# <a name="single-sign-out-saml-protocol"></a>Jeden Sign-Out protokol SAML

Azure Active Directory (Azure AD) podporuje profil jednotného přihlašování webového prohlížeče SAML 2,0. Aby jednotné přihlašování fungovalo správně, musí se **LogoutURL** pro aplikaci během registrace aplikace explicitně zaregistrovat ve službě Azure AD. Pokud se aplikace [přidá do Galerie aplikací Azure](v2-howto-app-gallery-listing.md) , tato hodnota se ve výchozím nastavení dá nastavit. V opačném případě musí být hodnota určena a uživatelem přidaná aplikace do svého tenanta služby Azure AD. Azure AD používá LogoutURL k přesměrování uživatelů poté, co se odhlásí. 

Azure AD podporuje vazbu přesměrování (HTTP GET), nikoli vazbu POST HTTP.

Následující diagram znázorňuje pracovní postup procesu jednotného odhlašování služby Azure AD.

![Pracovní postup jednotného odhlašování Azure AD](./media/single-sign-out-saml-protocol/active-directory-saml-single-sign-out-workflow.png)

## <a name="logoutrequest"></a>LogoutRequest
Cloudová služba pošle `LogoutRequest` zprávu službě Azure AD, která indikuje, že relace byla ukončena. Následující úryvek ukazuje vzorový `LogoutRequest` prvek.

```
<samlp:LogoutRequest xmlns="urn:oasis:names:tc:SAML:2.0:metadata" ID="idaa6ebe6839094fe4abc4ebd5281ec780" Version="2.0" IssueInstant="2013-03-28T07:10:49.6004822Z" xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
  <Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">https://www.workaad.com</Issuer>
  <NameID xmlns="urn:oasis:names:tc:SAML:2.0:assertion"> Uz2Pqz1X7pxe4XLWxV9KJQ+n59d573SepSAkuYKSde8=</NameID>
</samlp:LogoutRequest>
```

### <a name="logoutrequest"></a>LogoutRequest
`LogoutRequest`Element odeslaný do Azure AD vyžaduje následující atributy:

* `ID` – Identifikuje žádost o odhlášení. Hodnota `ID` by neměla začínat číslicí. Typický postup je připojit **ID** k řetězcové REprezentaci identifikátoru GUID.
* `Version` -Nastavte hodnotu tohoto prvku na **2,0**. Tato hodnota se vyžaduje.
* `IssueInstant` – Jedná se o `DateTime` řetězec s hodnotou souřadnic Universal Time (UTC) a [formátu Round-Trip ("o")](/dotnet/standard/base-types/standard-date-and-time-format-strings). Azure AD očekává hodnotu tohoto typu, ale neuplatní ji.

### <a name="issuer"></a>Vystavitel
`Issuer`Element v elementu `LogoutRequest` musí přesně odpovídat jednomu z **ServicePrincipalNames** v cloudové službě v Azure AD. Obvykle je tento parametr nastavený na **identifikátor URI ID aplikace** , který je zadaný při registraci aplikace.

### <a name="nameid"></a>NameID
Hodnota `NameID` elementu musí přesně odpovídat `NameID` uživateli, který je právě podepsán.

## <a name="logoutresponse"></a>LogoutResponse
Azure AD pošle `LogoutResponse` odpověď na `LogoutRequest` element. Následující úryvek ukazuje ukázku `LogoutResponse` .

```
<samlp:LogoutResponse ID="_f0961a83-d071-4be5-a18c-9ae7b22987a4" Version="2.0" IssueInstant="2013-03-18T08:49:24.405Z" InResponseTo="iddce91f96e56747b5ace6d2e2aa9d4f8c" xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
  <Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">https://sts.windows.net/82869000-6ad1-48f0-8171-272ed18796e9/</Issuer>
  <samlp:Status>
    <samlp:StatusCode Value="urn:oasis:names:tc:SAML:2.0:status:Success" />
  </samlp:Status>
</samlp:LogoutResponse>
```

### <a name="logoutresponse"></a>LogoutResponse
Azure AD nastaví `ID` `Version` `IssueInstant` hodnoty a v `LogoutResponse` elementu. Také nastaví `InResponseTo` element na hodnotu `ID` atributu `LogoutRequest` , který vyvolá odpověď.

### <a name="issuer"></a>Vystavitel
Azure AD Nastaví tuto hodnotu na `https://login.microsoftonline.com/<TenantIdGUID>/` , kde \<TenantIdGUID> je ID tenanta TENANTA Azure AD.

K vyhodnocení hodnoty `Issuer` elementu použijte hodnotu **identifikátoru URI ID aplikace** poskytnutou během registrace aplikace.

### <a name="status"></a>Status
Azure AD pomocí `StatusCode` elementu v `Status` elementu indikuje úspěch nebo neúspěch odhlášení. Pokud se pokus o odhlášení nezdaří, `StatusCode` element může obsahovat také vlastní chybové zprávy.
