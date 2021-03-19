---
title: Příručka k migraci Python ADAL do MSAL | Azure
titleSuffix: Microsoft identity platform
description: Naučte se migrovat aplikaci v Pythonu Azure Active Directory Authentication Library (ADAL) do knihovny Microsoft Authentication Library (MSAL) pro Python.
services: active-directory
author: rayluo
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.tgt_pltfrm: Python
ms.workload: identity
ms.date: 11/11/2019
ms.author: rayluo
ms.reviewer: marsma, rayluo, nacanuma
ms.custom: aaddev, devx-track-python
ms.openlocfilehash: 60d6e40b568c1189cdc01ef4239612d3717063b7
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "104578834"
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

Další podrobnosti najdete v tématu [co se liší od platformy Microsoft Identity?](../azuread-dev/azure-ad-endpoint-comparison.md) .

### <a name="scopes-not-resources"></a>Obory nejsou prostředky

ADAL Python získá tokeny pro prostředky, ale MSAL Python získá tokeny pro obory. Plocha rozhraní API v MSAL Pythonu už nemá parametr prostředků. Je nutné zadat obory jako seznam řetězců, které deklarují požadovaná oprávnění a požadované prostředky. Pokud chcete zobrazit nějaký příklad oborů, přečtěte si téma [Microsoft Graph obory](/graph/permissions-reference).

Do prostředku můžete přidat `/.default` příponu oboru, která vám umožní migrovat aplikace z koncového bodu v 1.0 (ADAL) na Microsoft Identity Platform (MSAL). Například pro hodnotu prostředku `https://graph.microsoft.com` je ekvivalentní hodnota oboru `https://graph.microsoft.com/.default` .  Pokud prostředek není ve formátu adresy URL, ale ID prostředku ve formuláři `XXXXXXXX-XXXX-XXXX-XXXXXXXXXXXX` , můžete i nadále používat hodnotu oboru jako `XXXXXXXX-XXXX-XXXX-XXXXXXXXXXXX/.default` .

Další podrobnosti o různých typech oborů najdete v článcích věnovaném [oprávněním a souhlasům na platformě identity Microsoftu](./v2-permissions-and-consent.md) a v [oborech pro webové rozhraní API Přijímám v 1.0 tokeny](./msal-v1-app-scopes.md) .

### <a name="error-handling"></a>Zpracování chyb

Knihovna Azure Active Directory Authentication Library (ADAL) pro Python používá výjimku `AdalError` k označení toho, že došlo k potížím. MSAL pro Python obvykle používá chybové kódy. Další informace najdete v tématu [MSAL pro zpracování chyb Pythonu](msal-error-handling-python.md).

### <a name="api-changes"></a>Změny rozhraní API

Následující tabulka uvádí rozhraní API v ADAL pro Python a ten, který se má použít pro místo v MSAL pro Python:

| ADAL pro Python API  | MSAL pro Python API |
| ------------------- | ---------------------------------- |
| [AuthenticationContext](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext)  | [PublicClientApplication nebo ConfidentialClientApplication](https://msal-python.readthedocs.io/en/latest/#msal.ClientApplication.__init__)  |
| –  | [get_authorization_request_url ()](https://msal-python.readthedocs.io/en/latest/#msal.ClientApplication.get_authorization_request_url)  |
| [acquire_token_with_authorization_code ()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token_with_authorization_code) | [acquire_token_by_authorization_code ()](https://msal-python.readthedocs.io/en/latest/#msal.ClientApplication.acquire_token_by_authorization_code) |
| [acquire_token ()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token) | [acquire_token_silent ()](https://msal-python.readthedocs.io/en/latest/#msal.ClientApplication.acquire_token_silent) |
| [acquire_token_with_refresh_token ()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token_with_refresh_token) | – |
| [acquire_user_code ()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_user_code) | [initiate_device_flow ()](https://msal-python.readthedocs.io/en/latest/#msal.PublicClientApplication.initiate_device_flow) |
| [acquire_token_with_device_code ()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token_with_device_code) a [cancel_request_to_get_token_with_device_code ()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.cancel_request_to_get_token_with_device_code) | [acquire_token_by_device_flow ()](https://msal-python.readthedocs.io/en/latest/#msal.PublicClientApplication.acquire_token_by_device_flow) |
| [acquire_token_with_username_password ()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token_with_username_password) | [acquire_token_by_username_password ()](https://msal-python.readthedocs.io/en/latest/#msal.PublicClientApplication.acquire_token_by_username_password) |
| [acquire_token_with_client_credentials ()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token_with_client_credentials) a [acquire_token_with_client_certificate ()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token_with_client_certificate) | [acquire_token_for_client ()](https://msal-python.readthedocs.io/en/latest/#msal.ConfidentialClientApplication.acquire_token_for_client) |
| – | [acquire_token_on_behalf_of ()](https://msal-python.readthedocs.io/en/latest/#msal.ConfidentialClientApplication.acquire_token_on_behalf_of) |
| [TokenCache ()](https://adal-python.readthedocs.io/en/latest/#adal.TokenCache) | [SerializableTokenCache()](https://msal-python.readthedocs.io/en/latest/#msal.SerializableTokenCache) |
| – | Mezipaměť s persistencí, která je k dispozici z [rozšíření MSAL](https://github.com/marstr/original-microsoft-authentication-extensions-for-python) |

## <a name="migrate-existing-refresh-tokens-for-msal-python"></a>Migrace existujících obnovovacích tokenů pro MSAL Python

Knihovna Microsoft Authentication Library (MSAL) vyabstrakce koncept aktualizačních tokenů. MSAL Python poskytuje ve výchozím nastavení mezipaměť tokenů v paměti, takže nemusíte ukládat, vyhledávat ani aktualizovat obnovovací tokeny. Uživatelům se zobrazí také méně výzev k přihlášení, protože aktualizační tokeny lze obvykle aktualizovat bez zásahu uživatele. Další informace o mezipaměti tokenů najdete v tématu věnovaném [serializaci mezipaměti vlastního tokenu v MSAL pro Python](msal-python-token-cache-serialization.md).

Následující kód vám pomůže s migrací aktualizačních tokenů spravovaných jinou knihovnou OAuth2 (včetně, ale ne jenom pro ADAL Python), která se má spravovat pomocí MSAL pro Python. Jedním z důvodů migrace těchto aktualizačních tokenů je zabránit tomu, aby se stávající uživatelé mohli znovu přihlásit, když aplikaci migrujete do MSAL pro Python.

Metodou migrace obnovovacího tokenu je použití MSAL pro Python k získání nového přístupového tokenu pomocí předchozího obnovovacího tokenu. Po vrácení nového obnovovacího tokenu ho MSAL for Python uloží do mezipaměti.
Vzhledem k tomu, že MSAL Python 1.3.0, poskytujeme rozhraní API v MSAL pro tento účel.
Přečtěte si následující fragment kódu, který je uvedený v [kompletní ukázce migrace obnovovacích tokenů pomocí MSAL Pythonu](https://github.com/AzureAD/microsoft-authentication-library-for-python/blob/1.3.0/sample/migrate_rt.py#L28-L67) .

```python
import msal
def get_preexisting_rt_and_their_scopes_from_elsewhere():
    # Maybe you have an ADAL-powered app like this
    #   https://github.com/AzureAD/azure-activedirectory-library-for-python/blob/1.2.3/sample/device_code_sample.py#L72
    # which uses a resource rather than a scope,
    # you need to convert your v1 resource into v2 scopes
    # See https://docs.microsoft.com/azure/active-directory/azuread-dev/azure-ad-endpoint-comparison#scopes-not-resources
    # You may be able to append "/.default" to your v1 resource to form a scope
    # See https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent#the-default-scope

    # Or maybe you have an app already talking to the Microsoft identity platform,
    # powered by some 3rd-party auth library, and persist its tokens somehow.

    # Either way, you need to extract RTs from there, and return them like this.
    return [
        ("old_rt_1", ["scope1", "scope2"]),
        ("old_rt_2", ["scope3", "scope4"]),
        ]


# We will migrate all the old RTs into a new app powered by MSAL
app = msal.PublicClientApplication(
    "client_id", authority="...",
    # token_cache=...  # Default cache is in memory only.
                       # You can learn how to use SerializableTokenCache from
                       # https://msal-python.readthedocs.io/en/latest/#msal.SerializableTokenCache
    )

# We choose a migration strategy of migrating all RTs in one loop
for old_rt, scopes in get_preexisting_rt_and_their_scopes_from_elsewhere():
    result = app.acquire_token_by_refresh_token(old_rt, scopes)
    if "error" in result:
        print("Discarding unsuccessful RT. Error: ", json.dumps(result, indent=2))

print("Migration completed")
```


## <a name="next-steps"></a>Další kroky

Další informace najdete [v tématu porovnání v 1.0 a v 2.0](../azuread-dev/azure-ad-endpoint-comparison.md).
