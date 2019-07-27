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
ms.date: 06/26/2019
ms.author: dapine
ms.openlocfilehash: 1b46c58d3f3c804052e637f7bde2e1a456764dba
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/26/2019
ms.locfileid: "67717226"
---
### <a name="speech-to-text-sub-chart-chartsspeechtotext"></a>Převod řeči na text (dílčí graf: grafy/speechToText)

Chcete-li potlačit graf "zastřešující", přidejte `speechToText.` předponu pro libovolný parametr, aby byl konkrétnější. Například přepíše odpovídající parametr, např. `speechToText.numberOfConcurrentRequest` Overrides. `numberOfConcurrentRequest`

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
| `service.autoScaler.enabled` | Určuje, zda je [Automatické škálování vodorovně pod](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/) povoleno. Pokud `true`sebudenasaditdoclusteru Kubernetes.`speech-to-text-autoscaler` | `true` |
| `service.podDisruption.enabled` | Zda je povoleno [rozpočet přerušení pod](https://kubernetes.io/docs/concepts/workloads/pods/disruptions/) . Pokud `true`sebudenasaditdoclusteru Kubernetes.`speech-to-text-poddisruptionbudget` | `true` |