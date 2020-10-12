---
title: Získat & odebrat účty z mezipaměti tokenů (MSAL4j) | Azure
titleSuffix: Microsoft identity platform
description: Přečtěte si, jak zobrazit a odebrat účty z mezipaměti tokenů pomocí knihovny Microsoft Authentication Library pro Java.
services: active-directory
author: sangonzal
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 11/07/2019
ms.author: sagonzal
ms.reviewer: navyasri.canumalla
ms.custom: aaddev, devx-track-java
ms.openlocfilehash: 6267c2118b7bac9befb42102d1118faac639bf49
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87312550"
---
# <a name="get-and-remove-accounts-from-the-token-cache-using-msal-for-java"></a>Získání a odebrání účtů z mezipaměti tokenů pomocí MSAL pro jazyk Java

MSAL for Java poskytuje standardně mezipaměť tokenů v paměti. Mezipaměť tokenů v paměti vydrží dobu trvání instance aplikace.

## <a name="see-which-accounts-are-in-the-cache"></a>Podívejte se, které účty jsou v mezipaměti.

Můžete zjistit, které účty jsou v mezipaměti, voláním `PublicClientApplication.getAccounts()` , jak je znázorněno v následujícím příkladu:

```java
PublicClientApplication pca = new PublicClientApplication.Builder(
                labResponse.getAppId()).
                authority(TestConstants.ORGANIZATIONS_AUTHORITY).
                build();

Set<IAccount> accounts = pca.getAccounts().join();
```

## <a name="remove-accounts-from-the-cache"></a>Odebrání účtů z mezipaměti

Chcete-li odebrat účet z mezipaměti, vyhledejte účet, který je třeba odebrat, a potom zavolejte `PublicClientApplicatoin.removeAccount()` jako v následujícím příkladu:

```java
Set<IAccount> accounts = pca.getAccounts().join();

IAccount accountToBeRemoved = accounts.stream().filter(
                x -> x.username().equalsIgnoreCase(
                        UPN_OF_USER_TO_BE_REMOVED)).findFirst().orElse(null);

pca.removeAccount(accountToBeRemoved).join();
```

## <a name="learn-more"></a>Další informace

Pokud používáte MSAL for Java, přečtěte si informace o [serializaci mezipaměti vlastního tokenu v MSAL pro Java](msal-java-token-cache-serialization.md).
