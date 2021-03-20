---
title: Serializace mezipaměti vlastního tokenu (MSAL Python) | Azure
titleSuffix: Microsoft identity platform
description: Naučte se serializovat mezipaměť tokenů pro MSAL pro Python.
services: active-directory
author: rayluo
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 11/13/2019
ms.author: rayluo
ms.reviewer: nacanuma
ms.custom: aaddev, devx-track-python
ms.openlocfilehash: 7aaf59377949101ba8dd5c9454b89229e925e859
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "87846186"
---
# <a name="custom-token-cache-serialization-in-msal-for-python"></a>Serializace mezipaměti vlastního tokenu v MSAL pro Python

V MSAL Pythonu je mezipaměť tokenů v paměti, která trvá po dobu trvání relace aplikace, ve výchozím nastavení k dispozici při vytváření instance [ClientApplication](https://msal-python.readthedocs.io/en/latest/#confidentialclientapplication).

Serializace mezipaměti tokenů, takže k nim mají přístup různé relace aplikace, není k dispozici. To je proto, že MSAL Python se dá použít v typech aplikací, které nemají přístup k systému souborů, jako jsou například webové aplikace. Pokud chcete mít mezipaměť trvalého tokenu v aplikaci MSAL Pythonu, musíte zadat vlastní serializaci mezipaměti tokenů.

Strategie serializace mezipaměti tokenů se liší v závislosti na tom, jestli píšete aplikaci veřejného klienta (Desktop) nebo důvěrnou klientskou aplikaci (webovou aplikaci, webové rozhraní API nebo aplikaci démon).

## <a name="token-cache-for-a-public-client-application"></a>Mezipaměť tokenů pro veřejnou klientskou aplikaci

Veřejné klientské aplikace běží na zařízení uživatele a spravují tokeny pro jednoho uživatele. V tomto případě můžete serializovat celou mezipaměť do souboru. Nezapomeňte zadat uzamykání souborů, pokud vaše aplikace nebo jiná aplikace může přistupovat k mezipaměti souběžně. Jednoduchý příklad, jak serializovat mezipaměť tokenů do souboru bez zamykání, naleznete v příkladu v referenční dokumentaci třídy [SerializableTokenCache](https://msal-python.readthedocs.io/en/latest/#msal.SerializableTokenCache) .

## <a name="token-cache-for-a-web-app-confidential-client-application"></a>Mezipaměť tokenů pro webovou aplikaci (důvěrná klientská aplikace)

U webových aplikací nebo webových rozhraní API můžete použít relaci nebo Redis Cache nebo databázi pro uložení tokenu mezipaměti. Měla by existovat jedna mezipaměť tokenů na uživatele (za každý účet), takže byste měli serializovat mezipaměť tokenů na účet.

## <a name="next-steps"></a>Další kroky

Příklad použití mezipaměti tokenu pro webovou aplikaci systému Windows nebo Linux nebo webové rozhraní API najdete v části [MS-identity-Python-WebApp](https://github.com/Azure-Samples/ms-identity-python-webapp/blob/master/app.py#L64-L72) . Příklad je pro webovou aplikaci, která volá rozhraní Microsoft Graph API.
