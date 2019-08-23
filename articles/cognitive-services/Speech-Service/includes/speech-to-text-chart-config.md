---
title: Instalace kontejnerů řeči
titleSuffix: Azure Cognitive Services
description: Podrobnější informace o možnostech konfigurace grafu Helm řeči na text
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 08/22/2019
ms.author: dapine
ms.openlocfilehash: 3f390affe7badb401277aa86d1867c763aa0ae3b
ms.sourcegitcommit: 47b00a15ef112c8b513046c668a33e20fd3b3119
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/22/2019
ms.locfileid: "69971314"
---
### <a name="speech-to-text-sub-chart-chartsspeechtotext"></a>Převod řeči na text (dílčí graf: grafy/speechToText)

Chcete-li potlačit graf "zastřešující", přidejte `speechToText.` předponu pro libovolný parametr, aby byl konkrétnější. Například přepíše odpovídající parametr, `speechToText.numberOfConcurrentRequest` například Overrides. `numberOfConcurrentRequest`

|Parametr|Popis|Výchozí|
| -- | -- | -- |
| `enabled` | Zda je povolena služba **Převod řeči na text** . | `false` |
| `numberOfConcurrentRequest` | Počet souběžných požadavků služby **Speech-to-text** . Tento graf automaticky vypočítá prostředky procesoru a paměti na základě této hodnoty. | `2` |
| `optimizeForAudioFile`| Zda služba potřebuje k optimalizaci zvukového vstupu přes zvukové soubory. Pokud `true`bude tento graf přidělovat službě více prostředků procesoru. | `false` |
| `image.registry`| Registr v programu **Speech to text** Docker image. | `containerpreview.azurecr.io` |
| `image.repository` | Úložiště obrázků Docker pro dopředné **zpracování textu** . | `microsoft/cognitive-services-speech-to-text` |
| `image.tag` | Značka obrázku Docker **pro domluvené slovo na text** | `latest` |
| `image.pullSecrets` | Obrazová tajemství pro vyžádání image Docker pro **text v textu** | |
| `image.pullByHash`| Zda je obrázek Docker načítán pomocí algoritmu hash. Pokud `true`jevyžadováno. `image.hash` | `false` |
| `image.hash`| Hodnota hash obrázku Docker **na text** Používá se pouze `image.pullByHash: true`v případě.  | |
| `image.args.eula`požadovanou | Označuje, že jste přijali licenci. Jediná platná hodnota je`accept` | |
| `image.args.billing`požadovanou | Hodnota identifikátoru URI fakturačního koncového bodu je k dispozici na stránce Přehled řeči Azure Portal. | |
| `image.args.apikey`požadovanou | Lze sledovat fakturační údaje. ||
| `service.type` | Typ služby Kubernetes služby **Speech-to-text** . Další podrobnosti najdete v pokynech k [typům služby Kubernetes](https://kubernetes.io/docs/concepts/services-networking/service/) a ověření podpory poskytovatele cloudu. | `LoadBalancer` |
| `service.port`|  Port služby převodu **řeči na text** . | `80` |
| `service.annotations` | Poznámky **k textovému** převodu pro metadata služby. Poznámky jsou páry klíč-hodnota. <br>`annotations:`<br>&nbsp;&nbsp;`some/annotation1: value1`<br>&nbsp;&nbsp;`some/annotation2: value2` | |
| `service.autoScaler.enabled` | Určuje, zda je [Automatické škálování vodorovně pod](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/) povoleno. Pokud `true`sebudenasaditdoclusteru Kubernetes.`speech-to-text-autoscaler` | `true` |
| `service.podDisruption.enabled` | Zda je povoleno [rozpočet přerušení pod](https://kubernetes.io/docs/concepts/workloads/pods/disruptions/) . Pokud `true`sebudenasaditdoclusteru Kubernetes.`speech-to-text-poddisruptionbudget` | `true` |