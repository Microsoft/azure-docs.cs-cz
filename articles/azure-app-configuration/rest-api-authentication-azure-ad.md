---
title: Azure Active Directory REST API – ověřování
description: Použití Azure Active Directory k ověření v konfiguraci aplikace Azure pomocí REST API
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: cbf05245768a663e324e9bb6e1ad422eeee3ab1a
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/09/2020
ms.locfileid: "96930513"
---
# <a name="azure-active-directory-authentication"></a>Ověřování služby Azure Active Directory

Požadavky HTTP můžete ověřit pomocí `Bearer` schématu ověřování s tokenem získaným z Azure Active Directory (Azure AD). Tyto požadavky je nutné přenést přes protokol TLS (Transport Layer Security).

## <a name="prerequisites"></a>Předpoklady

Musíte přiřadit objekt zabezpečení, který se používá k vyžádání tokenu Azure AD pro jednu z platných [rolí konfigurace aplikace Azure](./rest-api-authorization-azure-ad.md).

Zadejte všechny požadavky na všechny hlavičky HTTP vyžadované pro ověřování. Toto je minimální požadavek:

|  Hlavička požadavku | Popis  |
| --------------- | ------------ |
| `Authorization` | Informace o ověřování požadované `Bearer` schématem. |

**Příklad:**

```http
Host: {myconfig}.azconfig.io
Authorization: Bearer {{AadToken}}
```

## <a name="azure-ad-token-acquisition"></a>Získání tokenu Azure AD

Před získáním tokenu Azure AD musíte zjistit, co se uživatel má ověřit, jakou cílovou skupinu požadujete pro a co koncový bod Azure AD (autorita) má použít.

### <a name="audience"></a>Cílová skupina

Vyžádejte si token Azure AD se správnou cílovou skupinou. Pro konfiguraci aplikací Azure použijte jednu z následujících skupin. Cílovou skupinu lze také označovat jako *prostředek* , pro který je požadován token.

- {configurationStoreName}. azconfig. IO
- *. azconfig.io

> [!IMPORTANT]
> Když je požadovaná cílová skupina `{configurationStoreName}.azconfig.io` , musí přesně odpovídat `Host` hlavičce požadavku (rozlišuje velká a malá písmena), která se používá k odeslání požadavku.

### <a name="azure-ad-authority"></a>Autorita Azure AD

Autorita Azure AD je koncový bod, který používáte pro získání tokenu Azure AD. Je ve formátu `https://login.microsoftonline.com/{tenantId}` . `{tenantId}`Segment odkazuje na ID tenanta Azure AD, ke kterému patří uživatel nebo aplikace, které se pokouší ověřit.

### <a name="authentication-libraries"></a>Knihovny ověřování

Azure poskytuje sadu knihoven s názvem Azure Active Directory knihovny ověřování, které zjednodušují proces získání tokenu Azure AD. Azure vytvoří tyto knihovny pro několik jazyků. Další informace najdete v [dokumentaci](../active-directory/azuread-dev/active-directory-authentication-libraries.md).

## <a name="errors"></a>Chyby

Můžete se setkat s následujícími chybami.

```http
HTTP/1.1 401 Unauthorized
WWW-Authenticate: HMAC-SHA256, Bearer
```

**Důvod:** Nezadali jste záhlaví žádosti o autorizaci se `Bearer` schématem.

**Řešení:** Zadejte platnou `Authorization` hlavičku požadavku HTTP.

```http
HTTP/1.1 401 Unauthorized
WWW-Authenticate: HMAC-SHA256, Bearer error="invalid_token", error_description="Authorization token failed validation"
```

**Důvod:** Token Azure AD není platný.

**Řešení:** Získejte token Azure AD od autority Azure AD a ujistěte se, že jste používali správnou cílovou skupinu.

```http
HTTP/1.1 401 Unauthorized
WWW-Authenticate: HMAC-SHA256, Bearer error="invalid_token", error_description="The access token is from the wrong issuer. It must match the AD tenant associated with the subscription to which the configuration store belongs. If you just transferred your subscription and see this error message, please try back later."
```

**Důvod:** Token Azure AD není platný.

**Řešení:** Získejte token Azure AD od autority Azure AD. Ujistěte se, že je tenant služby Azure AD ten přidružený k předplatnému, ke kterému patří úložiště konfigurace. Tato chyba se může zobrazit, pokud objekt zabezpečení patří do více než jednoho tenanta Azure AD.