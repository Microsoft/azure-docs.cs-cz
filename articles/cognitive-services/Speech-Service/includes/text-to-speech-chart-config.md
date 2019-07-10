---
title: Nainstalujte kontejnery řeči
titleSuffix: Azure Cognitive Services
description: Podrobně popisuje možnosti konfigurace grafu helm pro převod textu na řeč.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 06/26/2019
ms.author: dapine
ms.openlocfilehash: e6c7dcd3015b0b8ab5b3c719ebd2397bc814b81a
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/09/2019
ms.locfileid: "67711491"
---
### <a name="text-to-speech-sub-chart-chartstexttospeech"></a>Převod textu na řeč (dílčí graf: grafy/textToSpeech)

K přepsání graf "zastřešující", přidejte tuto předponu `textToSpeech.` na žádné parametry, aby konkrétnější. Například přepíše odpovídajícího parametru třeba `textToSpeech.numberOfConcurrentRequest` přepíše `numberOfConcurrentRequest`.

|Parametr|Popis|Výchozí|
| -- | -- | -- |
| `enabled` | Zda **převod textu na řeč** aktivace služby. | `false` |
| `numberOfConcurrentRequest` | Počet souběžných požadavků pro **převod textu na řeč** služby. Tento graf automaticky vypočítá prostředky procesoru a paměti, na základě této hodnoty. | `2` |
| `optimizeForTurboMode`| Určuje, zda služba potřebuje pro optimalizaci pro textový vstup prostřednictvím textových souborů. Pokud `true`, tento graf se přidělit další prostředky procesoru do služby. | `false` |
| `image.registry`| **Převod textu na řeč** registrem imagí dockeru. | `containerpreview.azurecr.io` |
| `image.repository` | **Převod textu na řeč** úložiště image dockeru. | `microsoft/cognitive-services-text-to-speech` |
| `image.tag` | **Převod textu na řeč** značce image dockeru. | `latest` |
| `image.pullSecrets` | Obrázek tajné kódy pro stahování **převod textu na řeč** image dockeru. | |
| `image.pullByHash`| Určuje, zda image dockeru načítána algoritmem hash. Pokud `true`, `image.hash` je povinný. | `false` |
| `image.hash`| **Převod textu na řeč** hash image dockeru. Použije se pouze při `image.pullByHash: true`.  | |
| `image.args.eula` (povinné) | Označuje, že jste přijali licence. Jediná platná hodnota je `accept` | |
| `image.args.billing` (povinné) | Fakturační koncový bod hodnotu identifikátoru URI je k dispozici na stránce s přehledem řeči webu Azure portal. | |
| `image.args.apikey` (povinné) | Lze sledovat fakturační údaje. ||
| `service.type` | Typ služby Kubernetes **převod textu na řeč** služby. Najdete v článku [služby Kubernetes typy pokyny](https://kubernetes.io/docs/concepts/services-networking/service/) podrobné informace a ověřte podporu zprostředkovatele cloudu. | `LoadBalancer` |
| `service.port`|  Číslo portu **převod textu na řeč** služby. | `80` |
| `service.autoScaler.enabled` | Zda [vodorovné automatického škálování Podů](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/) je povolená. Pokud `true`, `text-to-speech-autoscaler` se nasadí Kubernetes cluster. | `true` |
| `service.podDisruption.enabled` | Zda [Pod narušení rozpočtu](https://kubernetes.io/docs/concepts/workloads/pods/disruptions/) je povolená. Pokud `true`, `text-to-speech-poddisruptionbudget` se nasadí Kubernetes cluster. | `true` |