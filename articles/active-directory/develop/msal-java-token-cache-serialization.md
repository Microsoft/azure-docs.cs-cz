---
title: Serializace mezipaměti vlastního tokenu (MSAL4j)
titleSuffix: Microsoft identity platform
description: Zjistěte, jak serializovat mezipaměť tokenů pro MSAL pro jazyk Java.
services: active-directory
author: sangonzal
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 11/07/2019
ms.author: sagonzal
ms.reviewer: nacanuma
ms.custom: aaddev, devx-track-java
ms.openlocfilehash: e85cfb8c03111b889a9f95509f337d415c517163
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "87312363"
---
# <a name="custom-token-cache-serialization-in-msal-for-java"></a>Serializace mezipaměti vlastního tokenu v MSAL pro Java

Chcete-li uchovat mezipaměť tokenů mezi instancemi aplikace, bude nutné tuto serializaci přizpůsobit. Třídy a rozhraní jazyka Java zapojené do serializace mezipaměti tokenu jsou následující:

- [ITokenCache](https://static.javadoc.io/com.microsoft.azure/msal4j/0.5.0-preview/com/microsoft/aad/msal4j/ITokenCache.html): rozhraní představuje mezipaměť tokenů zabezpečení.
- [ITokenCacheAccessAspect](https://static.javadoc.io/com.microsoft.azure/msal4j/0.5.0-preview/com/microsoft/aad/msal4j/ITokenCacheAccessAspect.html): rozhraní představující operaci spuštění kódu před a po přístupu. Měli byste @Override *BeforeCacheAccess* a *afterCacheAccess* s logikou odpovědnou za serializaci a deserializaci mezipaměti.
- [ITokenCacheContext](https://static.javadoc.io/com.microsoft.azure/msal4j/0.5.0-preview/com/microsoft/aad/msal4j/ITokenCacheAccessContext.html): rozhraní představující kontext, ve kterém je dostupná mezipaměť tokenu. 

Níže je Naive implementace vlastního serializace serializace mezipaměti tokenů nebo deserializace. Tuto hodnotu Nekopírujte a nevkládejte do produkčního prostředí.

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

Přečtěte si [, jak získat a odebrat účty z mezipaměti tokenů pomocí MSAL pro Java](msal-java-get-remove-accounts-token-cache.md).
