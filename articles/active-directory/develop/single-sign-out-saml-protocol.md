---
title: Protokol SAML pro jednotné odhlašování v Azure | Microsoft Docs
description: Tento článek popisuje protokol SAML jednotného odhlašování v Azure Active Directory
services: active-directory
documentationcenter: .net
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: 0e4aa75d-d1ad-4bde-a94c-d8a41fb0abe6
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
ms.openlocfilehash: 95d3deff73ce357f012b15a7fc1cfa3decdb4bda
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2020
ms.locfileid: "76701361"
---
# <a name="single-sign-out-saml-protocol"></a>Protokol SAML pro jednotné odhlašování

Azure Active Directory (Azure AD) podporuje profil jednotného přihlašování webového prohlížeče SAML 2,0. Aby jednotné přihlašování fungovalo správně, musí se **LogoutURL** pro aplikaci během registrace aplikace explicitně zaregistrovat ve službě Azure AD. Azure AD používá LogoutURL k přesměrování uživatelů poté, co se odhlásí.

Následující diagram znázorňuje pracovní postup procesu jednotného odhlašování služby Azure AD.

![Pracovní postup jednotného odhlašování Azure AD](./media/single-sign-out-saml-protocol/active-directory-saml-single-sign-out-workflow.png)

## <a name="logoutrequest"></a>LogoutRequest
Cloudová služba pošle službě Azure AD zprávu `LogoutRequest`, která indikuje, že relace byla ukončena. Následující úryvek ukazuje vzorový `LogoutRequest` element.

```
<samlp:LogoutRequest xmlns="urn:oasis:names:tc:SAML:2.0:metadata" ID="idaa6ebe6839094fe4abc4ebd5281ec780" Version="2.0" IssueInstant="2013-03-28T07:10:49.6004822Z" xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
  <Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">https://www.workaad.com</Issuer>
  <NameID xmlns="urn:oasis:names:tc:SAML:2.0:assertion"> Uz2Pqz1X7pxe4XLWxV9KJQ+n59d573SepSAkuYKSde8=</NameID>
</samlp:LogoutRequest>
```

### <a name="logoutrequest"></a>LogoutRequest
Element `LogoutRequest` odeslaný do Azure AD vyžaduje následující atributy:

* `ID` – identifikuje žádost o odhlášení. Hodnota `ID` nesmí začínat číslicí. Typický postup je připojit **ID** k řetězcové REprezentaci identifikátoru GUID.
* `Version` – nastavte hodnotu tohoto prvku na **2,0**. Tato hodnota se vyžaduje.
* `IssueInstant` – jedná se o `DateTime` řetězec s hodnotou souřadnic Universal Time (UTC) a [formátem Round-Trip ("o")](https://msdn.microsoft.com/library/az4se3k1.aspx). Azure AD očekává hodnotu tohoto typu, ale neuplatní ji.

### <a name="issuer"></a>Vystavitel
Element `Issuer` v `LogoutRequest` musí přesně odpovídat jednomu z **ServicePrincipalNames** v cloudové službě v Azure AD. Obvykle je tento parametr nastavený na **identifikátor URI ID aplikace** , který je zadaný při registraci aplikace.

### <a name="nameid"></a>NameID
Hodnota prvku `NameID` musí přesně odpovídat `NameID` uživatele, který je právě přihlášen.

## <a name="logoutresponse"></a>LogoutResponse
Azure AD pošle `LogoutResponse` jako odpověď na `LogoutRequest` prvek. Následující úryvek ukazuje vzorový `LogoutResponse`.

```
<samlp:LogoutResponse ID="_f0961a83-d071-4be5-a18c-9ae7b22987a4" Version="2.0" IssueInstant="2013-03-18T08:49:24.405Z" InResponseTo="iddce91f96e56747b5ace6d2e2aa9d4f8c" xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
  <Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">https://sts.windows.net/82869000-6ad1-48f0-8171-272ed18796e9/</Issuer>
  <samlp:Status>
    <samlp:StatusCode Value="urn:oasis:names:tc:SAML:2.0:status:Success" />
  </samlp:Status>
</samlp:LogoutResponse>
```

### <a name="logoutresponse"></a>LogoutResponse
Azure AD Nastaví `ID`, `Version` a `IssueInstant` hodnoty v elementu `LogoutResponse`. Také nastaví `InResponseTo` element na hodnotu atributu `ID` `LogoutRequest`, který vyvolá odpověď.

### <a name="issuer"></a>Vystavitel
Azure AD Nastaví tuto hodnotu na `https://login.microsoftonline.com/<TenantIdGUID>/`, kde \<TenantIdGUID > je ID tenanta tenanta Azure AD.

Pro vyhodnocení hodnoty prvku `Issuer` použijte hodnotu **identifikátoru URI ID aplikace** poskytnutou během registrace aplikace.

### <a name="status"></a>Stav
Azure AD pomocí elementu `StatusCode` v elementu `Status` označuje úspěch nebo neúspěch odhlášení. Pokud se pokus o odhlášení nezdaří, `StatusCode` element může také obsahovat vlastní chybové zprávy.
