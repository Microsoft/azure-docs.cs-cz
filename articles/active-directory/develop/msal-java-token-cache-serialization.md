---
title: Serializace mezipaměti vlastního tokenu v MSAL pro Java
titleSuffix: Microsoft identity platform
description: Zjistěte, jak serializovat mezipaměť tokenů pro MSAL pro jazyk Java.
services: active-directory
documentationcenter: dev-center-name
author: sangonzal
manager: henrikm
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/07/2019
ms.author: sagonzal
ms.reviewer: navyasri.canumalla
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7323a47095c58958503cecf16189ed875fac3b4d
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/24/2019
ms.locfileid: "74452598"
---
# <a name="custom-token-cache-serialization-in-msal-for-java"></a>Serializace mezipaměti vlastního tokenu v MSAL pro Java

Chcete-li uchovat mezipaměť tokenů mezi instancemi aplikace, bude nutné tuto serializaci přizpůsobit. Třídy a rozhraní jazyka Java zapojené do serializace mezipaměti tokenu jsou následující:

- [ITokenCache](https://static.javadoc.io/com.microsoft.azure/msal4j/0.5.0-preview/com/microsoft/aad/msal4j/ITokenCache.html): rozhraní představuje mezipaměť tokenů zabezpečení.
- [ITokenCacheAccessAspect](https://static.javadoc.io/com.microsoft.azure/msal4j/0.5.0-preview/com/microsoft/aad/msal4j/ITokenCacheAccessAspect.html): rozhraní představující operaci spuštění kódu před a po přístupu. Měli byste @Override *beforeCacheAccess* a *afterCacheAccess* s logikou odpovědnou za serializaci a deserializaci mezipaměti.
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
