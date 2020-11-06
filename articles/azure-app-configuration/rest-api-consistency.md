---
title: REST API konfigurace aplikace Azure – konzistence
description: Referenční stránky pro zajištění konzistence v reálném čase pomocí konfigurace aplikace Azure REST API
author: lisaguthrie
ms.author: lcozzens
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: 4f11e6edcd4bc128f815db7e93b00b72bf990ea8
ms.sourcegitcommit: 7cc10b9c3c12c97a2903d01293e42e442f8ac751
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/06/2020
ms.locfileid: "93424103"
---
# <a name="real-time-consistency"></a>Konzistence v reálném čase

Vzhledem k povaze některých distribuovaných systémů je konzistence v reálném čase mezi požadavky obtížné vymáhat implicitně. Řešením je povolení podpory protokolu ve formě více **synchronizačních tokenů**. Tokeny synchronizace jsou volitelné.

## <a name="initial-request"></a>Počáteční požadavek

Aby se zajistila konzistence v reálném čase mezi různými instancemi klienta a požadavky, použijte hlavičky volitelného `Sync-Token` požadavku a odpovědi.

Syntaxe:

```http
Sync-Token: <id>=<value>;sn=<sn>
```

|Parametr|Popis|
|--|--|
| `<id>` | ID tokenu (neprůhledné) |
| `<value>` | Hodnota tokenu (neprůhledná). Povoluje řetězec kódovaný v kódování Base64. |
| `<sn>` | Pořadové číslo tokenu (verze) Vyšší znamená novější verzi stejného tokenu. Umožňuje lepší souběžnost a ukládání do mezipaměti klienta. Klient se může rozhodnout použít jenom poslední verzi tokenu, protože verze tokenů jsou včetně. Nevyžaduje se pro žádosti. |

## <a name="response"></a>Odpověď

Služba poskytuje `Sync-Token` hlavičku s každou odpovědí.

```http
Sync-Token: jtqGc1I4=MDoyOA==;sn=28
```

## <a name="subsequent-requests"></a>Následné žádosti

Každá následná žádost zaručuje konzistentní odpověď v **reálném čase** ve vztahu k poskytovanému `Sync-Token` .

```http
Sync-Token: <id>=<value>
```

Pokud `Sync-Token` je hlavička z požadavku vynechána, je možné, že služba reaguje s daty uloženými v mezipaměti během krátké doby (až do několika sekund), než se interně vyrovná. Toto chování může způsobit nekonzistentní čtení, pokud došlo ke změnám hned před čtením.

## <a name="multiple-sync-tokens"></a>Vícenásobná synchronizace – tokeny

Server může reagovat s více synchronizačními tokeny pro jeden požadavek. Aby se zajistila konzistence v **reálném čase** pro další požadavek, klient musí odpovědět se všemi obdrženými tokeny Sync. V závislosti na specifikaci RFC musí být více hodnot hlaviček oddělených čárkami.

```http
Sync-Token: <token1-id>=<value>,<token2-id>=<value>
```
