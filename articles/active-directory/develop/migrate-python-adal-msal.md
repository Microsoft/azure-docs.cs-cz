---
title: Příručka k migraci Python ADAL do MSAL | Azure
description: Naučte se migrovat aplikaci v Pythonu Azure Active Directory Authentication Library (ADAL) do knihovny Microsoft Authentication Library (MSAL) pro Python.
services: active-directory
titleSuffix: Microsoft identity platform
author: rayluo
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.tgt_pltfrm: Python
ms.workload: identity
ms.date: 11/11/2019
ms.author: rayluo
ms.reviewer: rayluo, nacanuma, twhitney
ms.custom: aaddev
ms.openlocfilehash: fe9dc6c04fe033fd518218d1b5ea971e573405fc
ms.sourcegitcommit: af6847f555841e838f245ff92c38ae512261426a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2020
ms.locfileid: "76696551"
---
# <a name="adal-to-msal-migration-guide-for-python"></a>Příručka k migraci ADAL do MSAL pro Python

Tento článek popisuje změny, které je třeba provést při migraci aplikace, která používá knihovnu Azure Active Directory Authentication Library (ADAL) k použití knihovny Microsoft Authentication Library (MSAL).

## <a name="difference-highlights"></a>Rozdíly – světla

ADAL funguje s koncovým bodem Azure Active Directory (Azure AD) v 1.0. Knihovna Microsoft Authentication Library (MSAL) spolupracuje s platformou Microsoft identity, která se dřív jmenovala jako koncový bod Azure Active Directory v 2.0. Platforma Microsoft identity se liší od Azure AD v 1.0 v tom, že:

Podporovaných
  - Pracovní a školní účty (účty zřízené Azure AD)
  - Osobní účty (například Outlook.com nebo Hotmail.com)
  - Zákazníci, kteří přinášejí svoji vlastní e-mailovou nebo sociální identitu (například LinkedIn, Facebook, Google) prostřednictvím nabídky Azure AD B2C

- Jsou kompatibilní se standardy:
  - OAuth v 2.0
  - OpenID Connect (OIDC)

Další podrobnosti najdete v tématu [co se liší od koncového bodu Microsoft Identity Platform (v 2.0)](https://docs.microsoft.com/azure/active-directory/develop/azure-ad-endpoint-comparison) .

### <a name="scopes-not-resources"></a>Obory nejsou prostředky

ADAL Python získá tokeny pro prostředky, ale MSAL Python získá tokeny pro obory. Plocha rozhraní API v MSAL Pythonu už nemá parametr prostředků. Je nutné zadat obory jako seznam řetězců, které deklarují požadovaná oprávnění a požadované prostředky. Pokud chcete zobrazit nějaký příklad oborů, přečtěte si téma [Microsoft Graph obory](https://docs.microsoft.com/graph/permissions-reference).

### <a name="error-handling"></a>Zpracování chyb

Knihovna Azure Active Directory Authentication Library (ADAL) pro Python používá `AdalError` výjimky k indikaci, že došlo k problému. MSAL pro Python obvykle používá chybové kódy. Další informace najdete v tématu [MSAL pro zpracování chyb Pythonu](https://docs.microsoft.com/azure/active-directory/develop/msal-handling-exceptions?tabs=python).

### <a name="api-changes"></a>Změny rozhraní API

Následující tabulka uvádí rozhraní API v ADAL pro Python a ten, který se má použít pro místo v MSAL pro Python:

| ADAL pro Python API  | MSAL pro Python API |
| ------------------- | ---------------------------------- |
| [AuthenticationContext](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext)  | [PublicClientApplication nebo ConfidentialClientApplication](https://msal-python.readthedocs.io/en/latest/#msal.ClientApplication.__init__)  |
| Nevztahuje se  | [get_authorization_request_url ()](https://msal-python.readthedocs.io/en/latest/#msal.ClientApplication.get_authorization_request_url)  |
| [acquire_token_with_authorization_code ()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token_with_authorization_code) | [acquire_token_by_authorization_code ()](https://msal-python.readthedocs.io/en/latest/#msal.ClientApplication.acquire_token_by_authorization_code) |
| [acquire_token ()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token) | [acquire_token_silent ()](https://msal-python.readthedocs.io/en/latest/#msal.ClientApplication.acquire_token_silent) |
| [acquire_token_with_refresh_token ()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token_with_refresh_token) | Nevztahuje se |
| [acquire_user_code ()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_user_code) | [initiate_device_flow ()](https://msal-python.readthedocs.io/en/latest/#msal.PublicClientApplication.initiate_device_flow) |
| [acquire_token_with_device_code ()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token_with_device_code) a [cancel_request_to_get_token_with_device_code ()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.cancel_request_to_get_token_with_device_code) | [acquire_token_by_device_flow ()](https://msal-python.readthedocs.io/en/latest/#msal.PublicClientApplication.acquire_token_by_device_flow) |
| [acquire_token_with_username_password ()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token_with_username_password) | [acquire_token_by_username_password ()](https://msal-python.readthedocs.io/en/latest/#msal.PublicClientApplication.acquire_token_by_username_password) |
| [acquire_token_with_client_credentials ()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token_with_client_credentials) a [acquire_token_with_client_certificate ()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token_with_client_certificate) | [acquire_token_for_client ()](https://msal-python.readthedocs.io/en/latest/#msal.ConfidentialClientApplication.acquire_token_for_client) |
| Nevztahuje se | [acquire_token_on_behalf_of ()](https://msal-python.readthedocs.io/en/latest/#msal.ConfidentialClientApplication.acquire_token_on_behalf_of) |
| [TokenCache ()](https://adal-python.readthedocs.io/en/latest/#adal.TokenCache) | [SerializableTokenCache()](https://msal-python.readthedocs.io/en/latest/#msal.SerializableTokenCache) |
| Nevztahuje se | Mezipaměť s persistencí, která je k dispozici z [rozšíření MSAL](https://github.com/marstr/original-microsoft-authentication-extensions-for-python) |

## <a name="migrate-existing-refresh-tokens-for-msal-python"></a>Migrace existujících obnovovacích tokenů pro MSAL Python

Knihovna Microsoft Authentication Library (MSAL) vyabstrakce koncept aktualizačních tokenů. MSAL Python poskytuje ve výchozím nastavení mezipaměť tokenů v paměti, takže nemusíte ukládat, vyhledávat ani aktualizovat obnovovací tokeny. Uživatelům se zobrazí také méně výzev k přihlášení, protože aktualizační tokeny lze obvykle aktualizovat bez zásahu uživatele. Další informace o mezipaměti tokenů najdete v tématu věnovaném [serializaci mezipaměti vlastního tokenu v MSAL pro Python](msal-python-token-cache-serialization.md).

Následující kód vám pomůže s migrací aktualizačních tokenů spravovaných jinou knihovnou OAuth2 (včetně, ale ne jenom pro ADAL Python), která se má spravovat pomocí MSAL pro Python. Jedním z důvodů migrace těchto aktualizačních tokenů je zabránit tomu, aby se stávající uživatelé mohli znovu přihlásit, když aplikaci migrujete do MSAL pro Python.

Metodou migrace obnovovacího tokenu je použití MSAL pro Python k získání nového přístupového tokenu pomocí předchozího obnovovacího tokenu. Po vrácení nového obnovovacího tokenu ho MSAL for Python uloží do mezipaměti. Tady je příklad, jak to provést:

```python
from msal import PublicClientApplication

def get_preexisting_rt_and_their_scopes_from_elsewhere(...):
    raise NotImplementedError("You will need to implement this by yourself")

app = PublicClientApplication(..., token_cache=...)

for old_rt, old_scope in get_preexisting_rt_and_their_scopes_from_elsewhere(...):
    # Assuming the old scope could be a space-delimited string.
    # MSAL expects a list, like ["scope1", "scope2"].
    scopes = old_scope.split()
        # If your old refresh token came from ADAL for Python, which uses a resource rather than a scope,
        # you need to convert your v1 resource into v2 scopes
        # See https://docs.microsoft.com/azure/active-directory/develop/azure-ad-endpoint-comparison#scopes-not-resources
        # You may be able to append "/.default" to your v1 resource to form a scope
        # See https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent#the-default-scope

    result = app.client.obtain_token_by_refresh_token(old_rt, scope=scopes)
    # When this call returns the new token(s), a new refresh token is issued by the Microsoft identity platform and MSAL for Python
    # stores it in the token cache.
```

## <a name="next-steps"></a>Další kroky

Další informace najdete [v tématu porovnání v 1.0 a v 2.0](active-directory-v2-compare.md).
