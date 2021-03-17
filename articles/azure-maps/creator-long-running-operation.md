---
title: Rozhraní API pro operace Azure Maps Long-Running
description: Seznamte se s dlouhotrvajícím asynchronním zpracováním na pozadí v Azure Maps
author: anastasia-ms
ms.author: v-stharr
ms.date: 12/07/2020
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.custom: mvc
ms.openlocfilehash: f5fb7c8059c8b98e8ec514a4159e96f48db7b1ad
ms.sourcegitcommit: 80c1056113a9d65b6db69c06ca79fa531b9e3a00
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/09/2020
ms.locfileid: "96906195"
---
# <a name="creator-preview-long-running-operation-api"></a>Rozhraní API pro Long-Running operací autora (Preview)

> [!IMPORTANT]
> Služby Azure Maps Creator jsou momentálně ve verzi Public Preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Některá rozhraní API v Azure Maps používají [asynchronní vzor Request-Reply](/azure/architecture/patterns/async-request-reply). Tento model umožňuje Azure Maps poskytovat vysoce dostupné a reagující služby. Tento článek vysvětluje konkrétní implementaci dlouhotrvajícího asynchronního zpracování na pozadí v mapě Azure.

## <a name="submitting-a-request"></a>Odesílá se žádost.

Klientská aplikace spouští dlouhotrvající operaci prostřednictvím synchronního volání rozhraní HTTP API. Toto volání je obvykle ve formě požadavku HTTP POST. Po úspěšném vytvoření asynchronní úlohy vrátí rozhraní API `202` stavový kód HTTP, který indikuje, že žádost byla přijata. Tato odpověď obsahuje `Location` hlavičku ukazující na koncový bod, který může klient dotázat, aby zkontroloval stav dlouhotrvající operace.

### <a name="example-of-a-success-response"></a>Příklad odpovědi na úspěch

```HTTP
Status: 202 Accepted
Location: https://atlas.microsoft.com/service/operations/{operationId}

```

Pokud volání ověřování neprojde, rozhraní API místo toho vrátí `400` odpověď HTTP pro chybný požadavek. Tělo odpovědi poskytne klientovi více informací o tom, proč byl požadavek neplatný.

### <a name="monitoring-the-operation-status"></a>Monitorování stavu operace

Koncový bod umístění poskytnutý v hlavičkách přijaté odpovědi se dá dotazovat, aby zkontroloval stav dlouhotrvající operace. Tělo odpovědi z požadavku na stav operace bude vždy obsahovat `status` `createdDateTime` vlastnosti a. `status`Vlastnost zobrazuje aktuální stav dlouhotrvající operace. Mezi možné stavy patří `"NotStarted"` ,, `"Running"` `"Succeeded"` a `"Failed"` . `createdDateTime`Vlastnost zobrazuje čas počátečního požadavku na spuštění dlouhotrvající operace. Pokud je stav buď `"NotStarted"` nebo `"Running"` , bude k `Retry-After` dispozici i hlavička s odpovědí. `Retry-After`Záhlaví měřená v sekundách lze použít k určení, kdy má být provedeno další volání cyklického dotazování rozhraní API stavu operace.

### <a name="example-of-running-a-status-response"></a>Příklad spuštění odpovědi na stav

```HTTP
Status: 200 OK
Retry-After: 30
{
    "operationId": "c587574e-add9-4ef7-9788-1635bed9a87e",
    "createdDateTime": "3/11/2020 8:45:13 PM +00:00",
    "status": "Running"
}
```

## <a name="handling-operation-completion"></a>Zpracovává se dokončování operace.

Po dokončení dlouhotrvající operace bude stav odpovědi buď `"Succeeded"` nebo `"Failed"` . Při vytvoření nového prostředku z dlouhotrvající operace vrátí odpověď po úspěšném spuštění `201 Created` stavový kód HTTP. Odpověď bude obsahovat také `Location` záhlaví, které odkazuje na metadata týkající se prostředku. Pokud nebyl vytvořen žádný nový prostředek, odpověď na úspěch vrátí `200 OK` stavový kód HTTP. Po selhání bude kód stavu odpovědi také `200 OK` kódem. Nicméně odpověď bude obsahovat `error` vlastnost v těle. Data chyby vyhovují specifikaci chyb OData.

### <a name="example-of-success-response"></a>Příklad odpovědi na úspěch

```HTTP
Status: 201 Created
Location: "https://atlas.microsoft.com/tileset/{tileset-id}"
 {
    "operationId": "c587574e-add9-4ef7-9788-1635bed9a87e",
    "createdDateTime": "3/11/2020 8:45:13 PM +00:00",
    "status": "Succeeded",
    "resourceLocation": "https://atlas.microsoft.com/tileset/{tileset-id}"
}
```

### <a name="example-of-failure-response"></a>Příklad reakce na selhání

```HTTP
Status: 200 OK

{
    "operationId": "c587574e-add9-4ef7-9788-1635bed9a87e",
    "createdDateTime": "3/11/2020 8:45:13 PM +00:00",
    "status": "Failed",
    "error": {
        "code": "InvalidFeature",
        "message": "The provided feature is invalid.",
        "details": {
            "code": "NoGeometry",
            "message": "No geometry was provided with the feature."
        }
    }
}
```