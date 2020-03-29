---
title: Serializace vlastní mezipaměti tokenů (MSAL4j)
titleSuffix: Microsoft identity platform
description: Přečtěte si, jak serializovat mezipaměť tokenů pro MSAL pro Javu
services: active-directory
author: sangonzal
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/07/2019
ms.author: sagonzal
ms.reviewer: nacanuma
ms.custom: aaddev
ms.openlocfilehash: bcb34d83365112b97769186ad74dfd762b05c2e8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76696159"
---
# <a name="custom-token-cache-serialization-in-msal-for-java"></a>Serializace vlastní mezipaměti tokenů v jazyce MSAL pro jazyk Java

Chcete-li zachovat mezipaměť tokenů mezi instancemi aplikace, budete muset přizpůsobit serializaci. Třídy Java a rozhraní, které se účastní serializace mezipaměti tokenů, jsou následující:

- [ITokenCache](https://static.javadoc.io/com.microsoft.azure/msal4j/0.5.0-preview/com/microsoft/aad/msal4j/ITokenCache.html): Rozhraní představující mezipaměť tokenů zabezpečení.
- [ITokenCacheAccessAspect](https://static.javadoc.io/com.microsoft.azure/msal4j/0.5.0-preview/com/microsoft/aad/msal4j/ITokenCacheAccessAspect.html): Rozhraní představující operaci provádění kódu před a po přístupu. Byste @Override *předCacheAccess* a *afterCacheAccess* s logikou zodpovědní za serializaci a deserializaci mezipaměti.
- [ITokenCacheContext](https://static.javadoc.io/com.microsoft.azure/msal4j/0.5.0-preview/com/microsoft/aad/msal4j/ITokenCacheAccessContext.html): Rozhraní představující kontext, ve kterém je přístupná mezipaměti tokenů. 

Níže je naivní implementace vlastní serializace serializace mezipaměti tokenů nebo deserializace. Nekopírujte a nevkládejte jej do produkčního prostředí.

```Java
static class TokenPersistence implements ITokenCacheAccessAspect {
String data;

TokenPersistence(String data) {
        this.data = data;
}

@Override
public void beforeCacheAccess(ITokenCacheAccessContext iTokenCacheAccessContext) {
        iTokenCacheAccessContext.tokenCache().deserialize(data);
}

@Override
public void afterCacheAccess(ITokenCacheAccessContext iTokenCacheAccessContext) {
        data = iTokenCacheAccessContext.tokenCache().serialize();
}
```

```Java
// Loads cache from file
String dataToInitCache = readResource(this.getClass(), "/cache_data/serialized_cache.json");

ITokenCacheAccessAspect persistenceAspect = new TokenPersistence(dataToInitCache);

// By setting *TokenPersistence* on the PublicClientApplication, MSAL will call *beforeCacheAccess()* before accessing the cache and *afterCacheAccess()* after accessing the cache. 
PublicClientApplication app = 
PublicClientApplication.builder("my_client_id").setTokenCacheAccessAspect(persistenceAspect).build();
```

## <a name="learn-more"></a>Další informace

Další informace o [získání a odebrání účtů z mezipaměti tokenů pomocí msal pro Javu](msal-java-get-remove-accounts-token-cache.md).
