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
ms.openlocfilehash: fa6c4c53d04f227db7a9a83946182c109dc06d39
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/24/2019
ms.locfileid: "74452480"
---
# <a name="get-and-remove-accounts-from-the-token-cache-using-msal-for-java"></a>Získání a odebrání účtů z mezipaměti tokenů pomocí MSAL pro jazyk Java

MSAL for Java poskytuje standardně mezipaměť tokenů v paměti. Mezipaměť tokenů v paměti vydrží dobu trvání instance aplikace.

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
