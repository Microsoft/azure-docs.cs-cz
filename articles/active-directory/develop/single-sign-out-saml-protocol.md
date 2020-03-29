---
title: Protokol SAML jednotného odhlášení Azure | Dokumenty společnosti Microsoft
description: Tento článek popisuje protokol SAML jednotného odhlášení ve službě Azure Active Directory.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76701361"
---
# <a name="single-sign-out-saml-protocol"></a>Protokol SAML pro jednotné odhlášení

Azure Active Directory (Azure AD) podporuje saml 2.0 profil jednotného odhlašování webového prohlížeče. Aby jednotné odhlášení fungovalo správně, musí být **adresa Url logouturl** pro aplikaci explicitně zaregistrována ve službě Azure AD během registrace aplikace. Azure AD používá LogoutURL k přesměrování uživatelů po jejich odhlášení.

Následující diagram znázorňuje pracovní postup procesu jednotného odhlašování Azure AD.

![Pracovní postup jednotného odhlašování Azure AD](./media/single-sign-out-saml-protocol/active-directory-saml-single-sign-out-workflow.png)

## <a name="logoutrequest"></a>Požadavek na odhlášení
Cloudová služba `LogoutRequest` odešle zprávu do služby Azure AD označující, že relace byla ukončena. Následující výňatek ukazuje `LogoutRequest` ukázkový prvek.

```
<samlp:LogoutRequest xmlns="urn:oasis:names:tc:SAML:2.0:metadata" ID="idaa6ebe6839094fe4abc4ebd5281ec780" Version="2.0" IssueInstant="2013-03-28T07:10:49.6004822Z" xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
  <Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">https://www.workaad.com</Issuer>
  <NameID xmlns="urn:oasis:names:tc:SAML:2.0:assertion"> Uz2Pqz1X7pxe4XLWxV9KJQ+n59d573SepSAkuYKSde8=</NameID>
</samlp:LogoutRequest>
```

### <a name="logoutrequest"></a>Požadavek na odhlášení
Prvek `LogoutRequest` odeslaný do služby Azure AD vyžaduje následující atributy:

* `ID`- To identifikuje žádost o odhlášení. Hodnota by `ID` neměla začínat číslem. Typickým postupem je připojit **id** k řetězcové reprezentaci identifikátoru GUID.
* `Version`- Nastavte hodnotu tohoto prvku na **2,0**. Tato hodnota se vyžaduje.
* `IssueInstant`- Jedná `DateTime` se o řetězec s hodnotou Coordinate Universal Time (UTC) a [formátu round-trip ("o")](https://msdn.microsoft.com/library/az4se3k1.aspx). Azure AD očekává hodnotu tohoto typu, ale nevynucuje ji.

### <a name="issuer"></a>Vystavitel
Prvek `Issuer` v `LogoutRequest` musí přesně odpovídat jednomu z **ServicePrincipalNames** v cloudové službě ve službě Azure AD. Obvykle je to nastaveno na **identifikátor URI ID aplikace,** který je zadán při registraci aplikace.

### <a name="nameid"></a>NameID
Hodnota `NameID` prvku musí přesně odpovídat `NameID` uživateli, který je odhlášen.

## <a name="logoutresponse"></a>Odhlášení
Azure AD `LogoutResponse` odešle v `LogoutRequest` reakci na prvek. Následující výňatek ukazuje `LogoutResponse`ukázku .

```
<samlp:LogoutResponse ID="_f0961a83-d071-4be5-a18c-9ae7b22987a4" Version="2.0" IssueInstant="2013-03-18T08:49:24.405Z" InResponseTo="iddce91f96e56747b5ace6d2e2aa9d4f8c" xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
  <Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">https://sts.windows.net/82869000-6ad1-48f0-8171-272ed18796e9/</Issuer>
  <samlp:Status>
    <samlp:StatusCode Value="urn:oasis:names:tc:SAML:2.0:status:Success" />
  </samlp:Status>
</samlp:LogoutResponse>
```

### <a name="logoutresponse"></a>Odhlášení
Azure AD `ID`nastaví a `Version` `LogoutResponse` `IssueInstant` hodnoty v prvku. Také nastaví `InResponseTo` prvek na hodnotu `ID` atributu, `LogoutRequest` který vyvolal odpověď.

### <a name="issuer"></a>Vystavitel
Azure AD nastaví `https://login.microsoftonline.com/<TenantIdGUID>/` \<tuto hodnotu, kde TenantIdGUID> je ID klienta klienta klienta Azure AD klienta.

Chcete-li vyhodnotit `Issuer` hodnotu prvku, použijte hodnotu **identifikátoru URI aplikace** poskytnuté během registrace aplikace.

### <a name="status"></a>Status
Azure AD `StatusCode` používá prvek `Status` v elementu k označení úspěchu nebo selhání odhlášení. Pokud se pokus o odhlášení nezdaří, `StatusCode` prvek může také obsahovat vlastní chybové zprávy.
