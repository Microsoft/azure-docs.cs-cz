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
ms.collection: M365-identity-device-management
ms.openlocfilehash: 66c509b1b901889241d6837611a2c373750fdb3a
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/06/2019
ms.locfileid: "68834791"
---
# <a name="single-sign-out-saml-protocol"></a>Protokol SAML pro jednotné odhlašování

Azure Active Directory (Azure AD) podporuje profil jednotného přihlašování webového prohlížeče SAML 2,0. Aby jednotné přihlašování fungovalo správně, musí se **LogoutURL** pro aplikaci během registrace aplikace explicitně zaregistrovat ve službě Azure AD. Azure AD používá LogoutURL k přesměrování uživatelů poté, co se odhlásí.

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
`LogoutRequest` Element odeslaný do Azure AD vyžaduje následující atributy:

* `ID`– Identifikuje žádost o odhlášení. Hodnota `ID` by neměla začínat číslicí. Typický postup je připojit **ID** k řetězcové REprezentaci identifikátoru GUID.
* `Version`-Nastavte hodnotu tohoto prvku na **2,0**. Hodnota je povinná.
* `IssueInstant`– Jedná `DateTime` se o řetězec s hodnotou souřadnic Universal Time (UTC) a [formátu Round-Trip ("o")](https://msdn.microsoft.com/library/az4se3k1.aspx). Azure AD očekává hodnotu tohoto typu, ale neuplatní ji.

### <a name="issuer"></a>Vystavitel
Element v elementu musí přesně odpovídat jednomu z ServicePrincipalNames v cloudové službě v Azure AD. `Issuer` `LogoutRequest` Obvykle je tento parametr nastavený na **identifikátor URI ID aplikace** , který je zadaný při registraci aplikace.

### <a name="nameid"></a>NameID
Hodnota `NameID` elementu musí přesně `NameID` odpovídat uživateli, který je právě podepsán.

## <a name="logoutresponse"></a>LogoutResponse
Azure AD pošle `LogoutResponse` odpověď `LogoutRequest` na element. Následující úryvek ukazuje ukázku `LogoutResponse`.

```
<samlp:LogoutResponse ID="_f0961a83-d071-4be5-a18c-9ae7b22987a4" Version="2.0" IssueInstant="2013-03-18T08:49:24.405Z" InResponseTo="iddce91f96e56747b5ace6d2e2aa9d4f8c" xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
  <Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">https://sts.windows.net/82869000-6ad1-48f0-8171-272ed18796e9/</Issuer>
  <samlp:Status>
    <samlp:StatusCode Value="urn:oasis:names:tc:SAML:2.0:status:Success" />
  </samlp:Status>
</samlp:LogoutResponse>
```

### <a name="logoutresponse"></a>LogoutResponse
Azure `ID`AD nastaví `Version` hodnoty a `IssueInstant` v `LogoutResponse` elementu. Také nastaví `InResponseTo` element na hodnotu `ID` atributu `LogoutRequest` , který vyvolá odpověď.

### <a name="issuer"></a>Vystavitel
Azure AD Nastaví tuto hodnotu na `https://login.microsoftonline.com/<TenantIdGUID>/` , \<kde TenantIdGUID > je ID tenanta tenanta Azure AD.

K vyhodnocení hodnoty `Issuer` elementu použijte hodnotu **identifikátoru URI ID aplikace** poskytnutou během registrace aplikace.

### <a name="status"></a>Stav
Azure AD pomocí `StatusCode` elementu `Status` v elementu indikuje úspěch nebo neúspěch odhlášení. Pokud se pokus o odhlášení nezdaří, `StatusCode` element může obsahovat také vlastní chybové zprávy.
