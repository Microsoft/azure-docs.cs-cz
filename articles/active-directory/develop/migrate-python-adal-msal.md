---
title: Průvodce migrací pythona ADAL na msal | Azure
description: Zjistěte, jak migrovat aplikaci Pythonu (ADAL) (Azure Active Directory Authentication Library) do Knihovny ověřování Microsoft (MSAL) pro Python.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76696551"
---
# <a name="adal-to-msal-migration-guide-for-python"></a>Průvodce migrací ADAL na MSAL pro Python

Tento článek upozorňuje na změny, které je třeba provést k migraci aplikace, která používá Azure Active Directory Authentication Library (ADAL) k použití Knihovny ověřování Microsoft (MSAL).

## <a name="difference-highlights"></a>Zvýraznění rozdílů

ADAL funguje s koncovým bodem Azure Active Directory (Azure AD) v1.0. Knihovna ověřování Microsoft (MSAL) pracuje s platformou identit Microsoftu – dříve označovanou jako koncový bod Azure Active Directory v2.0. Platforma identit Microsoftu se liší od Azure AD v1.0 v tom, že:

Podporuje:
  - Pracovní a školní účty (zřízené účty Azure AD)
  - Osobní účty (například Outlook.com nebo Hotmail.com)
  - Vaši zákazníci, kteří si prostřednictvím nabídky Azure AD B2C přinesou vlastní e-mail nebo sociální identitu (například LinkedIn, Facebook, Google)

- Jsou standardy kompatibilní s:
  - OAuth v2.0
  - OpenID Connect (OIDC)

Další [podrobnosti najdete v tématu Co se liší od koncového bodu platformy identit microsoftu (v2.0)?](https://docs.microsoft.com/azure/active-directory/develop/azure-ad-endpoint-comparison)

### <a name="scopes-not-resources"></a>Obory nejsou prostředky

ADAL Python získává tokeny pro prostředky, ale MSAL Python získává tokeny pro obory. Povrch rozhraní API v MSAL Pythonjiž nemá parametr prostředku. Budete muset poskytnout obory jako seznam řetězců, které deklarují požadovaná oprávnění a prostředky, které jsou požadovány. Chcete-li zobrazit některé příklady oborů, naleznete [v microsoft graphu obory](https://docs.microsoft.com/graph/permissions-reference).

### <a name="error-handling"></a>Zpracování chyb

Azure Active Directory Authentication Library (ADAL) `AdalError` pro Python používá výjimku k označení, že došlo k potížím. MSAL pro Python obvykle používá chybové kódy, místo toho. Další informace naleznete v tématu [MSAL for Python error handling](https://docs.microsoft.com/azure/active-directory/develop/msal-handling-exceptions?tabs=python).

### <a name="api-changes"></a>Změny rozhraní API

V následující tabulce je uvedeno rozhraní API v ADAL pro Python a rozhraní, které se má použít na svém místě v Jazyce MSAL pro Python:

| ADAL pro rozhraní API Pythonu  | MSAL pro rozhraní API pythonu |
| ------------------- | ---------------------------------- |
| [Ověřeníkontext](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext)  | [PublicClientApplication nebo ConfidentialClientApplication](https://msal-python.readthedocs.io/en/latest/#msal.ClientApplication.__init__)  |
| Není dostupné.  | [get_authorization_request_url()](https://msal-python.readthedocs.io/en/latest/#msal.ClientApplication.get_authorization_request_url)  |
| [acquire_token_with_authorization_code()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token_with_authorization_code) | [acquire_token_by_authorization_code()](https://msal-python.readthedocs.io/en/latest/#msal.ClientApplication.acquire_token_by_authorization_code) |
| [acquire_token()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token) | [acquire_token_silent()](https://msal-python.readthedocs.io/en/latest/#msal.ClientApplication.acquire_token_silent) |
| [acquire_token_with_refresh_token()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token_with_refresh_token) | Není dostupné. |
| [acquire_user_code()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_user_code) | [initiate_device_flow()](https://msal-python.readthedocs.io/en/latest/#msal.PublicClientApplication.initiate_device_flow) |
| [acquire_token_with_device_code()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token_with_device_code) a [cancel_request_to_get_token_with_device_code()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.cancel_request_to_get_token_with_device_code) | [acquire_token_by_device_flow()](https://msal-python.readthedocs.io/en/latest/#msal.PublicClientApplication.acquire_token_by_device_flow) |
| [acquire_token_with_username_password()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token_with_username_password) | [acquire_token_by_username_password()](https://msal-python.readthedocs.io/en/latest/#msal.PublicClientApplication.acquire_token_by_username_password) |
| [acquire_token_with_client_credentials()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token_with_client_credentials) a [acquire_token_with_client_certificate()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token_with_client_certificate) | [acquire_token_for_client()](https://msal-python.readthedocs.io/en/latest/#msal.ConfidentialClientApplication.acquire_token_for_client) |
| Není dostupné. | [acquire_token_on_behalf_of()](https://msal-python.readthedocs.io/en/latest/#msal.ConfidentialClientApplication.acquire_token_on_behalf_of) |
| [TokenCache()](https://adal-python.readthedocs.io/en/latest/#adal.TokenCache) | [Serializovatelnýtokencache()](https://msal-python.readthedocs.io/en/latest/#msal.SerializableTokenCache) |
| Není dostupné. | Mezipaměť s trvalostí, dostupná z [rozšíření MSAL](https://github.com/marstr/original-microsoft-authentication-extensions-for-python) |

## <a name="migrate-existing-refresh-tokens-for-msal-python"></a>Migrace existujících obnovovacích tokenů pro MSAL Python

Knihovna ověřování společnosti Microsoft (MSAL) abstrahuje koncept obnovovacích tokenů. MSAL Python poskytuje ve výchozím nastavení mezipaměť tokenů v paměti, takže není nutné ukládat, hledat nebo aktualizovat obnovovací tokeny. Uživatelům se také zobrazí méně výzev k přihlášení, protože obnovovací tokeny lze obvykle aktualizovat bez zásahu uživatele. Další informace o mezipaměti tokenů naleznete [v tématu Vlastní serializace mezipaměti tokenů v jazyce MSAL pro Python](msal-python-token-cache-serialization.md).

Následující kód vám pomůže migrovat obnovovací tokeny spravované jinou knihovnou OAuth2 (včetně, ale bez omezení na ADAL Python), které mají být spravovány MSAL pro Python. Jedním z důvodů migrace těchto tokenů aktualizace je zabránit stávajícím uživatelům, aby se museli znovu přihlásit při migraci aplikace do MSAL pro Python.

Metoda migrace obnovovacího tokenu je použití MSAL pro Python k získání nového přístupového tokenu pomocí předchozího obnovovacího tokenu. Když je vrácen nový obnovovací token, MSAL pro Python jej uloží do mezipaměti. Zde je příklad, jak na to:

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

Další informace naleznete [v porovnání v1.0 a v2.0](active-directory-v2-compare.md).
