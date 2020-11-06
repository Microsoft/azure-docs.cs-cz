---
title: Azure Active Directory REST API – ověřování
description: Použití Azure Active Directory k ověření v konfiguraci aplikace Azure pomocí REST API
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: fb3d00fb79c55e29d578f5e068e4ae025414a935
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/06/2020
ms.locfileid: "93424016"
---
# <a name="azure-active-directory-authentication"></a>Ověřování služby Azure Active Directory

Požadavky HTTP se můžou ověřit pomocí schématu ověření **nosiče** s tokenem získaným z Azure Active Directory (Azure AD). Tyto požadavky musí být přenášeny přes protokol TLS.

## <a name="prerequisites"></a>Předpoklady

Objekt zabezpečení, který se použije k vyžádání tokenu Azure AD, musí být přiřazený k jedné z platných [rolí konfigurace aplikace](./rest-api-authorization-azure-ad.md) .

Zadejte všechny požadavky na všechny hlavičky HTTP vyžadované pro ověřování. Minimální požadovaná:

|  Hlavička požadavku | Popis  |
| --------------- | ------------ |
| **Autorizace** | Informace o ověřování vyžadované schématem **nosiče** . Formátování a podrobnosti jsou vysvětleny níže. |

**Příklad:**

```http
Host: {myconfig}.azconfig.io
Authorization: Bearer {{AadToken}}
```

## <a name="azure-active-directory-token-acquisition"></a>Získání tokenu Azure Active Directory

Než zjistíte, že je potřeba získat token Azure AD, musí zjistit, co uživatel chce ověřit, jakou cílovou skupinu požaduje token, a co by měl používat koncový bod Azure AD (autorita).

### <a name="audience"></a>Cílová skupina

Token Azure AD musí být požadován se správnou cílovou skupinou. Pro konfiguraci aplikací Azure by se při žádosti o token měla zadat jeden z následujících cílových skupin. Cílová skupina může být také označována jako "prostředek", pro který je požadován token.

- {configurationStoreName}. azconfig. IO
- *. azconfig.io

> [!IMPORTANT]
> Když je požadovaná cílová skupina {configurationStoreName}. azconfig. IO, musí se přesně shodovat s hlavičkou žádosti hostitel (rozlišuje velká a malá písmena), která se používá k odeslání požadavku.

### <a name="azure-ad-authority"></a>Autorita Azure AD

Autorita Azure AD je koncový bod, který se používá k získání tokenu Azure AD. Má formu `https://login.microsoftonline.com/{tenantId}` . `{tenantId}`Segment odkazuje na ID tenanta Azure Active Directory, do kterého patří uživatel nebo aplikace, které se pokouší ověřit.

### <a name="authentication-libraries"></a>Knihovny ověřování

Azure poskytuje sadu knihoven s názvem Azure Active Directory Authentication Library (ADAL), která zjednodušuje proces získání tokenu Azure AD. Tyto knihovny jsou sestavené pro různé jazyky. Dokumentaci najdete [tady](https://docs.microsoft.com/azure/active-directory/develop/active-directory-authentication-libraries).

## <a name="errors"></a>**Chyby**

```http
HTTP/1.1 401 Unauthorized
WWW-Authenticate: HMAC-SHA256, Bearer
```

**Důvod:** Není Zadaná hlavička žádosti o autorizaci se schématem nosného schématu.
**Řešení:** Zadejte platnou ```Authorization``` hlavičku požadavku HTTP.

```http
HTTP/1.1 401 Unauthorized
WWW-Authenticate: HMAC-SHA256, Bearer error="invalid_token", error_description="Authorization token failed validation"
```

**Důvod:** Token Azure AD není platný.
**Řešení:** Získejte token Azure AD od autority Azure AD a zajistěte, aby se použila správná cílová skupina.

```http
HTTP/1.1 401 Unauthorized
WWW-Authenticate: HMAC-SHA256, Bearer error="invalid_token", error_description="The access token is from the wrong issuer. It must match the AD tenant associated with the subscription to which the configuration store belongs. If you just transferred your subscription and see this error message, please try back later."
```

**Důvod:** Token Azure AD není platný.
**Řešení:** Získejte token Azure AD od autority Azure AD a ujistěte se, že je tenant Azure AD ten přidružený k předplatnému, ke kterému patří úložiště konfigurace. Tato chyba se může zobrazit, pokud objekt zabezpečení patří do více než jednoho tenanta služby Azure AD.
