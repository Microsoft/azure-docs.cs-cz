---
title: Rozhraní Azure API pro ověření přístupového tokenu FHIR
description: Provede ověření tokenu a nabídne tipy, jak řešit problémy s přístupem.
services: healthcare-apis
author: caitlinv39
ms.reviewer: matjazl
ms.service: healthcare-apis
ms.subservice: fhir
ms.topic: conceptual
ms.date: 02/19/2019
ms.author: cavoeg
ms.openlocfilehash: 426ec0c2d6cc274aa0b6829eb4a30fd29b9ba8e2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87844656"
---
# <a name="azure-api-for-fhir-access-token-validation"></a>Rozhraní Azure API pro ověření přístupového tokenu FHIR

Způsob, jakým Azure API pro FHIR ověřuje přístupový token, bude záviset na implementaci a konfiguraci. V tomto článku Vás provedeme kroky ověřování, což může být užitečné při řešení problémů s přístupem.

## <a name="validate-token-has-no-issues-with-identity-provider"></a>Ověření tokenu nemá žádné problémy se zprostředkovatelem identity.

Prvním krokem při ověřování tokenu je ověření, že token byl vydán správným zprostředkovatelem identity a že nebyl upraven. Server FHIR se nakonfiguruje tak, aby používal konkrétního poskytovatele identity známého jako autorita `Authority` . Server FHIR načte informace o zprostředkovateli identity z `/.well-known/openid-configuration` koncového bodu. Při použití Azure AD bude úplná adresa URL:

```
GET https://login.microsoftonline.com/<TENANT-ID>/.well-known/openid-configuration
```

kde `<TENANT-ID>` je konkrétní tenant služby Azure AD (buď ID tenanta, nebo název domény).

Azure AD vrátí do serveru FHIR dokument podobný tomuto:.

```json
{
    "authorization_endpoint": "https://login.microsoftonline.com/<TENANT-ID>/oauth2/authorize",
    "token_endpoint": "https://login.microsoftonline.com/<TENANT-ID>/oauth2/token",
    "token_endpoint_auth_methods_supported": [
        "client_secret_post",
        "private_key_jwt",
        "client_secret_basic"
    ],
    "jwks_uri": "https://login.microsoftonline.com/common/discovery/keys",
    "response_modes_supported": [
        "query",
        "fragment",
        "form_post"
    ],
    "subject_types_supported": [
        "pairwise"
    ],
    "id_token_signing_alg_values_supported": [
        "RS256"
    ],
    "http_logout_supported": true,
    "frontchannel_logout_supported": true,
    "end_session_endpoint": "https://login.microsoftonline.com/<TENANT-ID>/oauth2/logout",
    "response_types_supported": [
        "code",
        "id_token",
        "code id_token",
        "token id_token",
        "token"
    ],
    "scopes_supported": [
        "openid"
    ],
    "issuer": "https://sts.windows.net/<TENANT-ID>/",
    "claims_supported": [
        "sub",
        "iss",
        "cloud_instance_name",
        "cloud_instance_host_name",
        "cloud_graph_host_name",
        "msgraph_host",
        "aud",
        "exp",
        "iat",
        "auth_time",
        "acr",
        "amr",
        "nonce",
        "email",
        "given_name",
        "family_name",
        "nickname"
    ],
    "microsoft_multi_refresh_token": true,
    "check_session_iframe": "https://login.microsoftonline.com/<TENANT-ID>/oauth2/checksession",
    "userinfo_endpoint": "https://login.microsoftonline.com/<TENANT-ID>/openid/userinfo",
    "tenant_region_scope": "WW",
    "cloud_instance_name": "microsoftonline.com",
    "cloud_graph_host_name": "graph.windows.net",
    "msgraph_host": "graph.microsoft.com",
    "rbac_url": "https://pas.windows.net"
}
``` 
Důležité vlastnosti serveru FHIR jsou `jwks_uri` , které sdělují serveru, kde načte šifrovací klíče potřebné k ověření podpisu tokenu a `issuer` který oznamuje serveru, který bude v deklaraci identity vystavitele ( `iss` ) tokenů vydaných tímto serverem. Server FHIR to může použít k ověření, že přijímá autentický token.

## <a name="validate-claims-of-the-token"></a>Ověřit deklarace identity tokenu

Jakmile server ověří pravost tokenu, server FHIR pak bude pokračovat v ověřování, zda má klient požadované deklarace identity pro přístup k tokenu.

Při použití rozhraní Azure API pro FHIR bude server ověřovat:

1. Token má právo `Audience` ( `aud` deklarace).
1. Uživatel nebo objekt zabezpečení, pro který byl token vydán, má povolen přístup k rovině dat serveru FHIR. `oid`Deklarace tokenu obsahuje ID objektu identity, které jedinečně identifikuje uživatele nebo objekt zabezpečení.

Doporučujeme, aby služba FHIR byla [nakonfigurovaná tak, aby používala Azure RBAC](configure-azure-rbac.md) ke správě přiřazení rolí roviny dat. Můžete ale také [nakonfigurovat místní RBAC](configure-local-rbac.md) , pokud vaše služba FHIR používá externího nebo sekundárního tenanta Azure Active Directory. 

Při použití serveru OSS Microsoft FHIR pro Azure se ověří server:

1. Token má právo `Audience` ( `aud` deklarace).
1. Token má roli v `roles` deklaraci identity, která má povolený přístup k serveru FHIR.

Přečtěte si podrobné informace o tom, jak [definovat role na serveru FHIR](https://github.com/microsoft/fhir-server/blob/master/docs/Roles.md).

Server FHIR může také ověřit, že přístupový token má obory (v deklaraci tokenu `scp` ) pro přístup k části rozhraní FHIR API, ke které se klient pokouší získat přístup. V současné době rozhraní API Azure pro FHIR a server FHIR pro Azure neověřuje obory tokenů.

## <a name="next-steps"></a>Další kroky
Když teď víte, jak projít ověřováním tokenů, můžete dokončit kurz a vytvořit aplikaci JavaScriptu a číst FHIR data.

>[!div class="nextstepaction"]
>[Kurz webové aplikace](tutorial-web-app-fhir-server.md)