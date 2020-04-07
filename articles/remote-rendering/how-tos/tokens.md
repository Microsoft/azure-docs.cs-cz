---
title: Získání přístupových tokenů služby
description: Popisuje, jak vytvořit tokeny pro přístup k arr REST API
author: florianborn71
ms.author: flborn
ms.date: 02/11/2020
ms.topic: how-to
ms.openlocfilehash: 001a77734a8cedf08c5523380c1cbd00dce89f40
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681191"
---
# <a name="get-service-access-tokens"></a>Získání přístupových tokenů služby

Přístup k apim ARR REST je udělen pouze oprávněným uživatelům. Chcete-li prokázat autorizaci, musíte odeslat *přístupový token* spolu s požadavky REST. Tyto tokeny jsou vydávány *službou secure token* (STS) výměnou za klíč účtu. Tokeny mají **životnost 24 hodin,** a proto mohou být vydány uživatelům, aniž by jim plný přístup ke službě.

Tento článek popisuje, jak vytvořit takový přístupový token.

## <a name="prerequisites"></a>Požadavky

[Vytvořte si účet ARR](create-an-account.md), pokud ho ještě nemáte.

## <a name="token-service-rest-api"></a>Rozhraní REST SLUŽBY TOKEN

Chcete-li vytvořit přístupové tokeny, *služba zabezpečeného tokenu* poskytuje jedno rozhraní REST API. Adresa URL služby ARR [https://sts.mixedreality.azure.com](https://sts.mixedreality.azure.com)STS je .

### <a name="get-token-request"></a>Požadavek na získání tokenu

| Identifikátor URI | Metoda |
|-----------|:-----------|
| /účty/**accountId**/token | GET |

| Hlavička | Hodnota |
|--------|:------|
| Autorizace | **"AccountId na doručitele**:**accountKey**" |

Nahraďte *accountId* a *accountKey* příslušnými daty.

### <a name="get-token-response"></a>Odpověď "Získat token"

| Kód stavu | Datová část JSON | Komentáře |
|-----------|:-----------|:-----------|
| 200 | AccessToken: řetězec | Úspěch |

| Hlavička | Účel |
|--------|:------|
| MS-CV | Tuto hodnotu lze použít ke sledování volání v rámci služby |

## <a name="getting-a-token-using-powershell"></a>Získání tokenu pomocí PowerShellu

Kód prostředí PowerShell níže ukazuje, jak odeslat požadavek REST do SLUŽBY STS. Potom vytiskne token na výzvu prostředí PowerShell.

```PowerShell
$accountId = "<account_id_from_portal>"
$accountKey = "<account_key_from_portal>"

[System.Net.ServicePointManager]::SecurityProtocol = [System.Net.SecurityProtocolType]::Tls12;
$webResponse = Invoke-WebRequest -Uri "https://sts.mixedreality.azure.com/accounts/$accountId/token" -Method Get -Headers @{ Authorization = "Bearer ${accountId}:$accountKey" }
$response = ConvertFrom-Json -InputObject $webResponse.Content

Write-Output "Token: $($response.AccessToken)"
```

Skript pouze vytiskne token na výstup, odkud můžete zkopírovat & vložit. Pro skutečný projekt byste měli tento proces automatizovat.

## <a name="next-steps"></a>Další kroky

* [Příklad skriptů prostředí PowerShell](../samples/powershell-example-scripts.md)
* [Azure Front-end API](../how-tos/frontend-apis.md)
* [Rozhraní REST API pro správu relací](../how-tos/session-rest-api.md)
