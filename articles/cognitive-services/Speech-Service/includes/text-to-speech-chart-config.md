---
title: Instalace kontejnerů řeči
titleSuffix: Azure Cognitive Services
description: Podrobnosti o možnostech konfigurace grafu kormidelníka pro převod textu na řeč.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 08/22/2019
ms.author: dapine
ms.openlocfilehash: c87132c3ae41dec82e3493f9a0ee2397455ff881
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "69971350"
---
### <a name="text-to-speech-sub-chart-chartstexttospeech"></a>Převod textu na řeč (podgraf: grafy/textToSpeech)

Chcete-li přepsat "deštník" graf, `textToSpeech.` přidejte předponu na libovolný parametr, aby byl konkrétnější. Například přepíše odpovídající parametr, `textToSpeech.numberOfConcurrentRequest` například přepsání `numberOfConcurrentRequest`.

|Parametr|Popis|Výchozí|
| -- | -- | -- |
| `enabled` | Určuje, zda je povolena služba **převodu textu na řeč.** | `false` |
| `numberOfConcurrentRequest` | Počet souběžných požadavků na službu **převodu textu na řeč.** Tento graf automaticky vypočítá prostředky procesoru a paměti na základě této hodnoty. | `2` |
| `optimizeForTurboMode`| Určuje, zda služba potřebuje optimalizovat pro zadávání textu prostřednictvím textových souborů. Pokud `true`bude tento graf přidělovat službu více prostředků procesoru. | `false` |
| `image.registry`| Registr bitových obrázků dockeru **pro převod textu na řeč.** | `containerpreview.azurecr.io` |
| `image.repository` | Úložiště obrázků dockeru **pro převod textu na řeč.** | `microsoft/cognitive-services-text-to-speech` |
| `image.tag` | Značka **obrázku dockeru pro převod textu na řeč.** | `latest` |
| `image.pullSecrets` | Tajemství obrázku pro vytažení obrázku **dockeru pro převod textu na řeč.** | |
| `image.pullByHash`| Určuje, zda je bitová kopie dockeru vytahována pomocí hash. Pokud `true` `image.hash` je požadováno . | `false` |
| `image.hash`| Text **pro řeč** docker uhas. Používá se `image.pullByHash: true`pouze v případě, že .  | |
| `image.args.eula`(povinné) | Označuje, že jste licenci přijali. Jedinou platnou hodnotou je`accept` | |
| `image.args.billing`(povinné) | Hodnota URI koncového bodu fakturace je dostupná na stránce Přehled řeči na webu Azure Portal. | |
| `image.args.apikey`(povinné) | Slouží ke sledování fakturačních údajů. ||
| `service.type` | Typ služby Kubernetes služby **pro převod textu na řeč.** Další podrobnosti najdete v [pokynech k typům služeb Kubernetes](https://kubernetes.io/docs/concepts/services-networking/service/) a ověřte podporu poskytovatele cloudu. | `LoadBalancer` |
| `service.port`|  Port služby **převodu textu na řeč.** | `80` |
| `service.annotations` | Poznámky **převodu textu na řeč** pro metadata služby. Poznámky jsou dvojice hodnot klíče. <br>`annotations:`<br>&nbsp;&nbsp;`some/annotation1: value1`<br>&nbsp;&nbsp;`some/annotation2: value2` | |
| `service.autoScaler.enabled` | Určuje, zda je povolen [horizontální pod autoscaler.](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/) Pokud `true` `text-to-speech-autoscaler` bude nasazena v clusteru Kubernetes. | `true` |
| `service.podDisruption.enabled` | Určuje, zda je povolen [rozpočet přerušení podu.](https://kubernetes.io/docs/concepts/workloads/pods/disruptions/) Pokud `true` `text-to-speech-poddisruptionbudget` bude nasazena v clusteru Kubernetes. | `true` |