---
title: Získání a odebrání účtů z mezipaměti tokenů pomocí MSAL pro Java (MSAL4j)
titleSuffix: Microsoft identity platform
description: Přečtěte si, jak zobrazit a odebrat účty z mezipaměti tokenů pomocí knihovny Microsoft Authentication Library pro Java.
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
ms.openlocfilehash: 343abd87d3b5e8b82989b8b370cef61ec6d051df
ms.sourcegitcommit: bc193bc4df4b85d3f05538b5e7274df2138a4574
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/10/2019
ms.locfileid: "73905508"
---
# <a name="get-and-remove-accounts-from-the-token-cache-using-msal-for-java-msal4j"></a>Získání a odebrání účtů z mezipaměti tokenů pomocí MSAL pro Java (MSAL4j)

MSAL4J poskytuje ve výchozím nastavení mezipaměť tokenů v paměti. Mezipaměť tokenů v paměti vydrží dobu trvání instance aplikace.

## <a name="see-which-accounts-are-in-the-cache"></a>Podívejte se, které účty jsou v mezipaměti.

Můžete zjistit, které účty jsou v mezipaměti, voláním `PublicClientApplication.getAccounts()`, jak je znázorněno v následujícím příkladu:

```java
PublicClientApplication pca = new PublicClientApplication.Builder(
                labResponse.getAppId()).
                authority(TestConstants.ORGANIZATIONS_AUTHORITY).
                build();

Set<IAccount> accounts = pca.getAccounts().join();
```

## <a name="remove-accounts-from-the-cache"></a>Odebrání účtů z mezipaměti

Chcete-li odebrat účet z mezipaměti, vyhledejte účet, který je třeba odebrat, a poté zavolejte `PublicClientApplicatoin.removeAccount()`, jak je znázorněno v následujícím příkladu:

```java
Set<IAccount> accounts = pca.getAccounts().join();

IAccount accountToBeRemoved = accounts.stream().filter(
                x -> x.username().equalsIgnoreCase(
                        UPN_OF_USER_TO_BE_REMOVED)).findFirst().orElse(null);

pca.removeAccount(accountToBeRemoved).join();
```

## <a name="learn-more"></a>Další informace

Pokud používáte MSAL for Java, přečtěte si informace o [serializaci mezipaměti vlastního tokenu v MSAL pro Java](msal-java-token-cache-serialization.md).
