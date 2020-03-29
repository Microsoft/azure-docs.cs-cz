---
title: Získat & odebrat účty z mezipaměti tokenů (MSAL4j) | Azure
titleSuffix: Microsoft identity platform
description: Přečtěte si, jak zobrazit a odebrat účty z mezipaměti tokenů pomocí Knihovny microsoft authentication library pro Jazyk Java.
services: active-directory
author: sangonzal
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/07/2019
ms.author: sagonzal
ms.reviewer: navyasri.canumalla
ms.custom: aaddev
ms.openlocfilehash: 2b138678b186cc41b76254658ad604c2da2d76c1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76696193"
---
# <a name="get-and-remove-accounts-from-the-token-cache-using-msal-for-java"></a>Získání a odebrání účtů z mezipaměti tokenů pomocí msal pro Javu

MSAL pro Jazyk Java poskytuje ve výchozím nastavení mezipaměť tokenů v paměti. Mezipaměť tokenů v paměti trvá dobu trvání instance aplikace.

## <a name="see-which-accounts-are-in-the-cache"></a>Zobrazení účtů, které jsou v mezipaměti

Můžete zkontrolovat, jaké účty jsou `PublicClientApplication.getAccounts()` v mezipaměti voláním, jak je znázorněno v následujícím příkladu:

```java
PublicClientApplication pca = new PublicClientApplication.Builder(
                labResponse.getAppId()).
                authority(TestConstants.ORGANIZATIONS_AUTHORITY).
                build();

Set<IAccount> accounts = pca.getAccounts().join();
```

## <a name="remove-accounts-from-the-cache"></a>Odebrání účtů z mezipaměti

Chcete-li odebrat účet z mezipaměti, vyhledejte účet, který je třeba odebrat, a pak volejte, `PublicClientApplicatoin.removeAccount()` jak je znázorněno v následujícím příkladu:

```java
Set<IAccount> accounts = pca.getAccounts().join();

IAccount accountToBeRemoved = accounts.stream().filter(
                x -> x.username().equalsIgnoreCase(
                        UPN_OF_USER_TO_BE_REMOVED)).findFirst().orElse(null);

pca.removeAccount(accountToBeRemoved).join();
```

## <a name="learn-more"></a>Další informace

Pokud používáte MSAL pro Jazyk Java, přečtěte si informace o [vlastní serializaci mezipaměti tokenů v MSAL pro Javu](msal-java-token-cache-serialization.md).
