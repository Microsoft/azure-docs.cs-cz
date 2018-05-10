---
title: Azure schémata mřížky události pro události služby Media Services
description: Popisuje vlastnosti, které jsou k dispozici pro události služby Media Services s Azure událostí mřížky
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 03/19/2018
ms.author: juliako
ms.openlocfilehash: c443ea755c9e7e88b4685682d5e43f675d42efa4
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
---
# <a name="azure-event-grid-schemas-for-media-services-events"></a>Azure schémata mřížky události pro události služby Media Services

Tento článek obsahuje schémata a vlastnosti události Media Services.

## <a name="media-services-job-state-change-event"></a>Událost změny stavu úlohy Media Services

Tato část poskytuje že události změny vlastnosti a schéma pro stav úlohy Media Services.  

### <a name="available-event-types"></a>Typy událostí k dispozici

Služba Media Services **úlohy** vysílá následující typy událostí:

| Typ události | Popis |
| ---------- | ----------- |
| Microsoft.Media.JobStateChange| Vyvolá, když stav úlohy změny. |

### <a name="example-event"></a>Příklad událostí

Následující příklad ukazuje schéma dokončení událost stavu úlohy: 

```json
[
  {
    "topic": "/subscriptions/{subscription id}/resourceGroups/amsResourceGroup/providers/Microsoft.Media/mediaservices/amsaccount",
    "subject": "transforms/VideoAnalyzerTransform/jobs/{job id}",
    "eventType": "Microsoft.Media.JobStateChange",
    "eventTime": "2018-04-20T21:26:13.8978772",
    "id": "<id>",
    "data": {
      "previousState": "Processing",
      "state": "Finished"
    },
    "dataVersion": "1.0",
    "metadataVersion": "1"
  }
]
```

### <a name="event-properties"></a>Vlastnosti události

Událost má následující dat nejvyšší úrovně:

| Vlastnost | Typ | Popis |
| -------- | ---- | ----------- |
| Téma | řetězec | Událost mřížky poskytuje tuto hodnotu. |
| Předmět | řetězec | Cesta prostředku v rámci prostředků účtu Media Services. |
| Typ události | řetězec | Jeden z typů událostí registrovaných pro tento zdroj událostí. Například "Microsoft.Media.JobStateChange". |
| eventTime | řetězec | Čas, který se vygeneruje událost založené na čas UTC poskytovatele. |
| id | řetězec | Jedinečný identifikátor pro událost. |
| data | objekt | Data události Media Services. |
| dataVersion | řetězec | Verze schématu datového objektu. Vydavatel definuje verze schématu. |
| metadataVersion | řetězec | Verze schématu metadat událostí. Událost mřížky definuje schéma vlastnosti nejvyšší úrovně. Událost mřížky poskytuje tuto hodnotu. |

Datový objekt má následující vlastnosti:

| Vlastnost | Typ | Popis |
| -------- | ---- | ----------- |
| previousState | řetězec | Stav úlohy před událostí. |
| state | řetězec | Nový stav úlohy se v tomto případě oznámení. Například "zařazeno do fronty: Úloha čeká na prostředky" nebo "naplánovaná: Úloha je připraven pro spuštění".|

Stav úlohy kde může být jedna z hodnot: *zařazeno ve frontě*, *naplánovaná*, *zpracování*, *dokončeno*, *chyba*, *Zrušena*, *zrušení*

## <a name="next-steps"></a>Další postup

[Registrace pro události změny stavu úlohy](job-state-events-cli-how-to.md)
