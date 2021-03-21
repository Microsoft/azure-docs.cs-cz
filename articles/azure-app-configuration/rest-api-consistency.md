---
title: REST API konfigurace aplikace Azure – konzistence
description: Referenční stránky pro zajištění konzistence v reálném čase pomocí konfigurace aplikace Azure REST API
author: AlexandraKemperMS
ms.author: alkemper
ms.service: azure-app-configuration
ms.topic: reference
ms.date: 08/17/2020
ms.openlocfilehash: b324d23ce9abc1eb3893f316365aff828de2063d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "96932621"
---
# <a name="real-time-consistency"></a>Konzistence v reálném čase

Vzhledem k povaze některých distribuovaných systémů je konzistence v reálném čase mezi požadavky obtížné vymáhat implicitně. Řešením je povolení podpory protokolu ve formě více synchronizačních tokenů. Tokeny synchronizace jsou volitelné.

## <a name="initial-request"></a>Počáteční požadavek

Chcete-li zaručit konzistenci v reálném čase mezi různými instancemi klienta a požadavky, použijte volitelné `Sync-Token` hlavičky žádosti a odpovědi.

Syntaxe:

```http
Sync-Token: <id>=<value>;sn=<sn>
```

|Parametr|Popis|
|--|--|
| `<id>` | ID tokenu (neprůhledné) |
| `<value>` | Hodnota tokenu (neprůhledná). Povoluje řetězec kódovaný v kódování Base64. |
| `<sn>` | Pořadové číslo tokenu (verze) Vyšší znamená novější verzi stejného tokenu. Umožňuje lepší souběžnost a ukládání do mezipaměti klienta. Klient se může rozhodnout použít jenom poslední verzi tokenu, protože verze tokenů jsou včetně. Tento parametr není pro požadavky požadován. |

## <a name="response"></a>Odpověď

Služba poskytuje `Sync-Token` hlavičku s každou odpovědí.

```http
Sync-Token: jtqGc1I4=MDoyOA==;sn=28
```

## <a name="subsequent-requests"></a>Následné žádosti

Každá následná žádost zaručuje konzistentní odpověď v reálném čase ve vztahu k poskytovanému `Sync-Token` .

```http
Sync-Token: <id>=<value>
```

Pokud vynecháte `Sync-Token` hlavičku z požadavku, může služba reagovat s daty uloženými v mezipaměti během krátké doby (až do několika sekund), než se interně vyrovná. Toto chování může způsobit nekonzistentní čtení, pokud došlo ke změnám hned před čtením.

## <a name="multiple-sync-tokens"></a>Vícenásobná synchronizace – tokeny

Server může reagovat s více synchronizačními tokeny pro jeden požadavek. Aby se zajistila konzistence v reálném čase pro další požadavek, klient musí odpovědět se všemi přijatými synchronizačními tokeny. Více hodnot záhlaví musí být oddělených čárkami.

```http
Sync-Token: <token1-id>=<value>,<token2-id>=<value>
```
