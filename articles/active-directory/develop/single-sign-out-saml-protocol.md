---
title: Azure jednotné přihlašování SAML protokol | Dokumentace Microsoftu
description: Tento článek popisuje, protokol jednotné odhlašování SAML v Azure Active Directory
services: active-directory
documentationcenter: .net
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 0e4aa75d-d1ad-4bde-a94c-d8a41fb0abe6
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
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3a286b3578cc159b6771df3f91bcd3f2fd35a161
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56201668"
---
# <a name="single-sign-out-saml-protocol"></a>Protokol SAML pro jednotné odhlašování

Azure Active Directory (Azure AD) podporuje SAML 2.0 webové jednotné odhlašování profil prohlížeče. Pro zadání jedné odhlašování fungovala správně **LogoutURL** pro aplikace, musí explicitně zaregistrovat během registrace aplikace Azure AD. Azure AD používá LogoutURL přesměrovat uživatele, poté, co jste se přihlásili navýšení kapacity.

Následující diagram znázorňuje pracovní postup procesu jednotné odhlašování Azure AD.

![Azure AD jednoho odhlášení pracovního postupu](./media/single-sign-out-saml-protocol/active-directory-saml-single-sign-out-workflow.png)

## <a name="logoutrequest"></a>LogoutRequest
Odešle cloudové služby `LogoutRequest` zprávy do služby Azure AD k označení, že relace byla ukončena. Následující úryvek znázorňuje ukázku `LogoutRequest` elementu.

```
<samlp:LogoutRequest xmlns="urn:oasis:names:tc:SAML:2.0:metadata" ID="idaa6ebe6839094fe4abc4ebd5281ec780" Version="2.0" IssueInstant="2013-03-28T07:10:49.6004822Z" xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
  <Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">https://www.workaad.com</Issuer>
  <NameID xmlns="urn:oasis:names:tc:SAML:2.0:assertion"> Uz2Pqz1X7pxe4XLWxV9KJQ+n59d573SepSAkuYKSde8=</NameID>
</samlp:LogoutRequest>
```

### <a name="logoutrequest"></a>LogoutRequest
`LogoutRequest` Element posílá službě Azure AD vyžaduje následující atributy:

* `ID` -Určuje odhlašování přes protokol žádosti. Hodnota `ID` nesmí začínat číslicí. Typický postup je připojit **id** na řetězcové vyjádření identifikátoru GUID.
* `Version` – Nastavte hodnotu vlastnosti tohoto elementu **2.0**. Tato hodnota se vyžaduje.
* `IssueInstant` – Jedná se `DateTime` řetězec s hodnotou koordinovat světový čas (UTC) a [("o") formátu round-trip](https://msdn.microsoft.com/library/az4se3k1.aspx). Azure AD očekává, že hodnota tohoto typu, ale nebude vynucovat.

### <a name="issuer"></a>Vystavitel
`Issuer` Prvek `LogoutRequest` musí přesně odpovídat jedné ze **ServicePrincipalNames** v cloudové službě ve službě Azure AD. Obvykle je nastavené na **identifikátor ID URI aplikace** , který je uveden při registraci aplikace.

### <a name="nameid"></a>NameID
Hodnota `NameID` elementu musí přesně odpovídat `NameID` uživatele, který se právě odhlásí.

## <a name="logoutresponse"></a>LogoutResponse
Azure AD posílá `LogoutResponse` v reakci na `LogoutRequest` elementu. Následující úryvek znázorňuje ukázku `LogoutResponse`.

```
<samlp:LogoutResponse ID="_f0961a83-d071-4be5-a18c-9ae7b22987a4" Version="2.0" IssueInstant="2013-03-18T08:49:24.405Z" InResponseTo="iddce91f96e56747b5ace6d2e2aa9d4f8c" xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
  <Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">https://sts.windows.net/82869000-6ad1-48f0-8171-272ed18796e9/</Issuer>
  <samlp:Status>
    <samlp:StatusCode Value="urn:oasis:names:tc:SAML:2.0:status:Success" />
  </samlp:Status>
</samlp:LogoutResponse>
```

### <a name="logoutresponse"></a>LogoutResponse
Sady Azure AD `ID`, `Version` a `IssueInstant` hodnoty v `LogoutResponse` elementu. Také nastaví `InResponseTo` prvku na hodnotu `ID` atribut `LogoutRequest` , který vyvolaná odpovědi.

### <a name="issuer"></a>Vystavitel
Azure AD Nastaví tuto hodnotu na `https://login.microsoftonline.com/<TenantIdGUID>/` kde <TenantIdGUID> je tenant ID tenanta Azure AD.

Vyhodnotit hodnotu `Issuer` elementu, použijte hodnotu **identifikátor ID URI aplikace** zadali při registraci aplikace.

### <a name="status"></a>Status
Azure AD používá `StatusCode` prvek `Status` indikuje úspěch nebo neúspěch odhlašování element. Pokud se odhlášení nezdaří, `StatusCode` element může také obsahovat vlastní chybové zprávy.
