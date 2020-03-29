---
title: Serializace vlastní mezipaměti tokenů (MSAL Python) | Azure
titleSuffix: Microsoft identity platform
description: Naučte se serializovat mezipaměť tokenů pro MSAL pro Python
services: active-directory
author: rayluo
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/13/2019
ms.author: rayluo
ms.reviewer: nacanuma
ms.custom: aaddev
ms.openlocfilehash: 2593cc856afb98cf5186c4e33032c5e9151614f0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76704387"
---
# <a name="custom-token-cache-serialization-in-msal-for-python"></a>Serializace vlastní mezipaměti tokenů v jazyce MSAL pro Python

V MSAL Python, mezipaměti tokenu v paměti, která přetrvává po dobu trvání relace aplikace, je k dispozici ve výchozím nastavení při vytváření instance [ClientApplication](https://msal-python.readthedocs.io/en/latest/#confidentialclientapplication).

Serializace mezipaměti tokenů, takže různé relace vaší aplikace k ní mají přístup, není k dispozici "po vybalení z krabice". Je to proto, že MSAL Python lze použít v typech aplikací, které nemají přístup k systému souborů – jako jsou webové aplikace. Chcete-li mít trvalé mezipaměti tokenů v aplikaci MSAL Python, musíte zadat vlastní serializaci mezipaměti tokenů.

Strategie serializace mezipaměti tokenů se liší v závislosti na tom, zda píšete veřejnou klientskou aplikaci (Desktop) nebo důvěrnou klientskou aplikaci (Web App, Webové rozhraní API nebo aplikace Daemon).

## <a name="token-cache-for-a-public-client-application"></a>Mezipaměť tokenů pro veřejnou klientskou aplikaci

Veřejné klientské aplikace běží na zařízení uživatele a spravovat tokeny pro jednoho uživatele. V takovém případě můžete serializovat celou mezipaměť do souboru. Nezapomeňte zajistit zamykání souborů, pokud vaše aplikace nebo jiná aplikace, přístup ke mezipaměti současně. Jednoduchý příklad serializace mezipaměti tokenů do souboru bez uzamčení naleznete v příkladu v referenční dokumentaci třídy [SerializableTokenCache.](https://msal-python.readthedocs.io/en/latest/#msal.SerializableTokenCache)

## <a name="token-cache-for-a-web-app-confidential-client-application"></a>Mezipaměť tokenů pro webovou aplikaci (důvěrná klientská aplikace)

Pro webové aplikace nebo webová api můžete použít relaci nebo mezipaměť Redis nebo databázi k uložení mezipaměti tokenů. Měla by existovat jedna mezipaměť tokenů na uživatele (na účet), takže se ujistěte, že serializujete mezipaměť tokenů na účet.

## <a name="next-steps"></a>Další kroky

Příklad použití mezipaměti tokenů pro webovou aplikaci pro Windows nebo Linux nebo webové rozhraní API najdete v tématu [ms-identity-python-webapp.](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/master/app.py#L64-L72) Příklad je pro webovou aplikaci, která volá rozhraní Microsoft Graph API.
