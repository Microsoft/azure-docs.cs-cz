---
title: Událost automatického škálování fondu Azure Batch
description: Odkaz na událost automatického škálování fondu dávek, která je generována po provedení automatického škálování fondu. Obsah protokolu zveřejní vzorec automatického škálování a výsledky vyhodnocení pro fond.
ms.topic: reference
ms.date: 10/08/2020
ms.openlocfilehash: e548ed484e5f683a8f79434ce4095ac66900f01a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "91852132"
---
# <a name="pool-autoscale-event"></a>Událost automatického škálování fondu

 Tato událost je generována po provedení automatického škálování fondu. Obsah protokolu zveřejní vzorec automatického škálování a výsledky vyhodnocení pro fond.

 Následující příklad ukazuje tělo události automatického škálování fondu pro automatické škálování fondu, které selhalo kvůli nedostatku ukázkových dat.

```
{
    "id": "myPool1",
    "timestamp": "2020-09-21T18:59:56.204Z",
    "formula": "...",
    "results": "...",
    "error": {
        "code": "InsufficientSampleData",
        "message": "Autoscale evaluation failed due to insufficient sample data",
        "values": [{
                "name": "Message",
                "value": "Line 15, Col 44: Insufficient data from data set: $RunningTasks wanted 70%, received 50%"
            }
        ]
    }
}
```

|Prvek|Typ|Poznámky|
|-------------|----------|-----------|
|`id`|Řetězec|ID fondu|
|`timestamp`|DateTime|Časové razítko při spuštění automatického škálování|
|`formula`|Řetězec|Vzorec definovaný pro automatické škálování.|
|`results`|Řetězec|Výsledky vyhodnocení pro všechny proměnné použité ve vzorci.|
|[`error`](#error)|Komplexní typ|Podrobná chyba pro automatické škálování.|

###  <a name="error"></a><a name="error"></a> Chyba

|Název elementu|Typ|Poznámky|
|------------------|----------|-----------|
|`code`|Řetězec|Identifikátor pro chybu automatického škálování. Kódy jsou invariantní a mají být zpracovány programově.|
|`message`|Řetězec|Zpráva popisující chybu automatického škálování, která je vhodná pro zobrazení v uživatelském rozhraní.|
|`values`|Pole|Seznam párů název-hodnota, které popisují další podrobnosti chyby automatického škálování|
