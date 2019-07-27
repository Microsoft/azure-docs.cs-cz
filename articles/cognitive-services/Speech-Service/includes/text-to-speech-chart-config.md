---
title: Instalace kontejnerů řeči
titleSuffix: Azure Cognitive Services
description: Podrobnější informace o možnostech konfigurace grafu Helm textu na řeč.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 06/26/2019
ms.author: dapine
ms.openlocfilehash: e6c7dcd3015b0b8ab5b3c719ebd2397bc814b81a
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/26/2019
ms.locfileid: "67717246"
---
### <a name="text-to-speech-sub-chart-chartstexttospeech"></a>Převod textu na řeč (dílčí graf: grafy/textToSpeech)

Chcete-li potlačit graf "zastřešující", přidejte `textToSpeech.` předponu pro libovolný parametr, aby byl konkrétnější. Například přepíše odpovídající parametr, např. `textToSpeech.numberOfConcurrentRequest` Overrides. `numberOfConcurrentRequest`

|Parametr|Popis|Výchozí|
| -- | -- | -- |
| `enabled` | Určuje, zda je povolena služba **Převod textu na mluvené slovo** . | `false` |
| `numberOfConcurrentRequest` | Počet souběžných požadavků na službu převodu **textu na řeč** . Tento graf automaticky vypočítá prostředky procesoru a paměti na základě této hodnoty. | `2` |
| `optimizeForTurboMode`| Zda služba potřebuje optimalizovat pro zadávání textu prostřednictvím textových souborů. Pokud `true`bude tento graf přidělovat službě více prostředků procesoru. | `false` |
| `image.registry`| Registr **textu na řeč** Docker image. | `containerpreview.azurecr.io` |
| `image.repository` | Úložiště obrázků Docker **pro převod textu na mluvené slovo** . | `microsoft/cognitive-services-text-to-speech` |
| `image.tag` | Značka obrázku Docker **pro převod textu na řeč** | `latest` |
| `image.pullSecrets` | Obrazová tajemství pro vyžádání image Docker **textu na řeč** | |
| `image.pullByHash`| Zda je obrázek Docker načítán pomocí algoritmu hash. Pokud `true`jevyžadováno. `image.hash` | `false` |
| `image.hash`| Hodnota hash obrázku Docker **pro text na řeč** Používá se pouze `image.pullByHash: true`v případě.  | |
| `image.args.eula`požadovanou | Označuje, že jste přijali licenci. Jediná platná hodnota je`accept` | |
| `image.args.billing`požadovanou | Hodnota identifikátoru URI fakturačního koncového bodu je k dispozici na stránce Přehled řeči Azure Portal. | |
| `image.args.apikey`požadovanou | Lze sledovat fakturační údaje. ||
| `service.type` | Typ služby Kubernetes služby převodu **textu na řeč** . Další podrobnosti najdete v pokynech k [typům služby Kubernetes](https://kubernetes.io/docs/concepts/services-networking/service/) a ověření podpory poskytovatele cloudu. | `LoadBalancer` |
| `service.port`|  Port služby převodu **textu na mluvené slovo** . | `80` |
| `service.autoScaler.enabled` | Určuje, zda je [Automatické škálování vodorovně pod](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/) povoleno. Pokud `true`sebudenasaditdoclusteru Kubernetes.`text-to-speech-autoscaler` | `true` |
| `service.podDisruption.enabled` | Zda je povoleno [rozpočet přerušení pod](https://kubernetes.io/docs/concepts/workloads/pods/disruptions/) . Pokud `true`sebudenasaditdoclusteru Kubernetes.`text-to-speech-poddisruptionbudget` | `true` |