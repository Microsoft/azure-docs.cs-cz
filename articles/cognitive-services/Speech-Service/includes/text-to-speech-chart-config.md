---
title: Instalace kontejnerů řeči
titleSuffix: Azure Cognitive Services
description: Podrobnější informace o možnostech konfigurace grafu Helm textu na řeč.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 22168974ab8b285413b4fa6e947c05f65a73ae12
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "96002262"
---
### <a name="text-to-speech-sub-chart-chartstexttospeech"></a>Převod textu na řeč (dílčí graf: grafy/textToSpeech)

Chcete-li potlačit graf "zastřešující", přidejte předponu pro `textToSpeech.` libovolný parametr, aby byl konkrétnější. Například přepíše odpovídající parametr, například `textToSpeech.numberOfConcurrentRequest` Overrides `numberOfConcurrentRequest` .

|Parametr|Popis|Výchozí|
| -- | -- | -- |
| `enabled` | Určuje, zda je povolena služba **Převod textu na mluvené slovo** . | `false` |
| `numberOfConcurrentRequest` | Počet souběžných požadavků na službu převodu **textu na řeč** . Tento graf automaticky vypočítá prostředky procesoru a paměti na základě této hodnoty. | `2` |
| `optimizeForTurboMode`| Zda služba potřebuje optimalizovat pro zadávání textu prostřednictvím textových souborů. Pokud `true` bude tento graf přidělovat službě více prostředků procesoru. | `false` |
| `image.registry`| Registr **textu na řeč** Docker image. | `containerpreview.azurecr.io` |
| `image.repository` | Úložiště obrázků Docker **pro převod textu na mluvené slovo** . | `microsoft/cognitive-services-text-to-speech` |
| `image.tag` | Značka obrázku Docker **pro převod textu na řeč** | `latest` |
| `image.pullSecrets` | Obrazová tajemství pro vyžádání image Docker **textu na řeč** | |
| `image.pullByHash`| Zda je obrázek Docker načítán pomocí algoritmu hash. Pokud `true` `image.hash` je vyžadováno. | `false` |
| `image.hash`| Hodnota hash obrázku Docker **pro text na řeč** Používá se pouze v případě `image.pullByHash: true` .  | |
| `image.args.eula` požadovanou | Označuje, že jste přijali licenci. Jediná platná hodnota je `accept` | |
| `image.args.billing` požadovanou | Hodnota identifikátoru URI fakturačního koncového bodu je k dispozici na stránce Přehled řeči Azure Portal. | |
| `image.args.apikey` požadovanou | Používá se ke sledování fakturačních informací. ||
| `service.type` | Typ služby Kubernetes služby převodu **textu na řeč** . Další podrobnosti najdete v [pokynech k typům služby Kubernetes](https://kubernetes.io/docs/concepts/services-networking/service/) a ověření podpory poskytovatele cloudu. | `LoadBalancer` |
| `service.port`|  Port služby převodu **textu na mluvené slovo** . | `80` |
| `service.annotations` | Poznámky pro **Převod textu na mluvené slovo** pro metadata služby Poznámky jsou páry klíč-hodnota. <br>`annotations:`<br>&nbsp;&nbsp;`some/annotation1: value1`<br>&nbsp;&nbsp;`some/annotation2: value2` | |
| `service.autoScaler.enabled` | Určuje, zda je [Automatické škálování vodorovně pod](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/) povoleno. Pokud se `true` `text-to-speech-autoscaler` bude nasadit do clusteru Kubernetes. | `true` |
| `service.podDisruption.enabled` | Zda je povoleno [rozpočet přerušení pod](https://kubernetes.io/docs/concepts/workloads/pods/disruptions/) . Pokud se `true` `text-to-speech-poddisruptionbudget` bude nasadit do clusteru Kubernetes. | `true` |