---
title: Získání přístupových tokenů služeb
description: Popisuje, jak vytvořit tokeny pro přístup k rozhraním REST API ARR.
author: florianborn71
ms.author: flborn
ms.date: 02/11/2020
ms.topic: how-to
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 9721685fc3ccd2c1c80b55e9118d6d347cc97a9c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "97830696"
---
# <a name="get-service-access-tokens"></a>Získání přístupových tokenů služeb

Přístup k rozhraním REST API ARR se uděluje jenom autorizovaným uživatelům. Chcete-li prokázat autorizaci, je nutné odeslat *přístupový token* společně s požadavky REST. Tyto tokeny se vydávají pomocí *služby tokenů zabezpečení* (STS) v Exchangi pro klíč účtu. Tokeny mají **dobu životnosti 24 hodin** , takže je lze vydat uživatelům, aniž by jim poskytli úplný přístup ke službě.

Tento článek popisuje, jak tento přístupový token vytvořit.

## <a name="prerequisites"></a>Předpoklady

[Vytvořte si účet ARR](create-an-account.md), pokud ho ještě nemáte.

## <a name="token-service-rest-api"></a>REST API služby tokenu

Pro vytvoření přístupových tokenů poskytuje *služba tokenů zabezpečení* jednu REST API. Adresa URL služby STS závisí na doméně účtu vzdáleného vykreslování. Je ve formátu https://sts . [ Doména účtu], např. `https://sts.southcentralus.mixedreality.azure.com`

### <a name="get-token-request"></a>Žádost o získání tokenu

| Identifikátor URI | Metoda |
|-----------|:-----------|
| /Accounts/**accountId**/token | GET |

| Hlavička | Hodnota |
|--------|:------|
| Autorizace | "Bearer **accountId**:**accountKey**" |

Položku *accountId* a *accountKey* nahraďte odpovídajícími daty.

### <a name="get-token-response"></a>Odpověď Get token

| Stavový kód | Datová část JSON | Komentáře |
|-----------|:-----------|:-----------|
| 200 | AccessToken: řetězec | Success |

| Hlavička | Účel |
|--------|:------|
| MS-CV | Tato hodnota se dá použít k trasování volání v rámci služby. |

## <a name="getting-a-token-using-powershell"></a>Získání tokenu pomocí PowerShellu

Následující kód PowerShellu ukazuje, jak odeslat nezbytnou žádost REST do služby STS. Pak vypíše token do příkazového řádku PowerShellu.

```PowerShell
$accountId = "<account_id_from_portal>"
$accountKey = "<account_key_from_portal>"
$accountDomain = "<account_domain_from_portal>

[System.Net.ServicePointManager]::SecurityProtocol = [System.Net.SecurityProtocolType]::Tls12;
$webResponse = Invoke-WebRequest -Uri "https://sts.$accountDomain/accounts/$accountId/token" -Method Get -Headers @{ Authorization = "Bearer ${accountId}:$accountKey" }
$response = ConvertFrom-Json -InputObject $webResponse.Content

Write-Output "Token: $($response.AccessToken)"
```

Skript pouze vytiskne token do výstupu, ze kterého můžete zkopírovat & jej vložit. Pro skutečný projekt byste měli tento proces automatizovat.

## <a name="next-steps"></a>Další kroky

* [Ukázkové skripty PowerShellu](../samples/powershell-example-scripts.md)
* [Rozhraní API front-endu Azure](../how-tos/frontend-apis.md)
* [REST API správy relací](../how-tos/session-rest-api.md)
