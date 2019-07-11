---
title: Nainstalujte kontejnery řeči
titleSuffix: Azure Cognitive Services
description: Podrobnosti o možnostech konfigurace grafu helm speech to text.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 06/26/2019
ms.author: dapine
ms.openlocfilehash: 1b46c58d3f3c804052e637f7bde2e1a456764dba
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/10/2019
ms.locfileid: "67717226"
---
### <a name="speech-to-text-sub-chart-chartsspeechtotext"></a>Převod řeči na Text (dílčí graf: grafy/speechToText)

K přepsání graf "zastřešující", přidejte tuto předponu `speechToText.` na žádné parametry, aby konkrétnější. Například přepíše odpovídajícího parametru třeba `speechToText.numberOfConcurrentRequest` přepíše `numberOfConcurrentRequest`.

|Parametr|Popis|Výchozí|
| -- | -- | -- |
| `enabled` | Zda **speech to text** aktivace služby. | `false` |
| `numberOfConcurrentRequest` | Počet souběžných požadavků pro **speech to text** služby. Tento graf automaticky vypočítá prostředky procesoru a paměti, na základě této hodnoty. | `2` |
| `optimizeForAudioFile`| Určuje, zda služba potřebuje pro optimalizaci pro zvukového vstupu prostřednictvím zvukové soubory. Pokud `true`, tento graf se přidělit další prostředky procesoru do služby. | `false` |
| `image.registry`| **Speech to text** registrem imagí dockeru. | `containerpreview.azurecr.io` |
| `image.repository` | **Speech to text** úložiště image dockeru. | `microsoft/cognitive-services-speech-to-text` |
| `image.tag` | **Speech to text** značce image dockeru. | `latest` |
| `image.pullSecrets` | Obrázek tajné kódy pro stahování **speech to text** image dockeru. | |
| `image.pullByHash`| Určuje, zda image dockeru načítána algoritmem hash. Pokud `true`, `image.hash` je povinný. | `false` |
| `image.hash`| **Speech to text** hash image dockeru. Použije se pouze při `image.pullByHash: true`.  | |
| `image.args.eula` (povinné) | Označuje, že jste přijali licence. Jediná platná hodnota je `accept` | |
| `image.args.billing` (povinné) | Fakturační koncový bod hodnotu identifikátoru URI je k dispozici na stránce s přehledem řeči webu Azure portal. | |
| `image.args.apikey` (povinné) | Lze sledovat fakturační údaje. ||
| `service.type` | Typ služby Kubernetes **speech to text** služby. Najdete v článku [služby Kubernetes typy pokyny](https://kubernetes.io/docs/concepts/services-networking/service/) podrobné informace a ověřte podporu zprostředkovatele cloudu. | `LoadBalancer` |
| `service.port`|  Číslo portu **speech to text** služby. | `80` |
| `service.autoScaler.enabled` | Zda [vodorovné automatického škálování Podů](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/) je povolená. Pokud `true`, `speech-to-text-autoscaler` se nasadí Kubernetes cluster. | `true` |
| `service.podDisruption.enabled` | Zda [Pod narušení rozpočtu](https://kubernetes.io/docs/concepts/workloads/pods/disruptions/) je povolená. Pokud `true`, `speech-to-text-poddisruptionbudget` se nasadí Kubernetes cluster. | `true` |