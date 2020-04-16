---
title: Instalace kontejnerů řeči
titleSuffix: Azure Cognitive Services
description: Podrobnosti o možnostech konfigurace grafu kormidelník řeči na text.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 08/22/2019
ms.author: trbye
ms.openlocfilehash: f7ca8fdaddab9757292939c4f7e658179d6e517c
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81421672"
---
### <a name="speech-to-text-sub-chart-chartsspeechtotext"></a>Převod řeči na text (podgraf: grafy/speechToText)

Chcete-li přepsat "deštník" graf, `speechToText.` přidejte předponu na libovolný parametr, aby byl konkrétnější. Například přepíše odpovídající parametr, `speechToText.numberOfConcurrentRequest` například přepsání `numberOfConcurrentRequest`.

|Parametr|Popis|Výchozí|
| -- | -- | -- |
| `enabled` | Určuje, zda je povolena služba **převodu řeči na text.** | `false` |
| `numberOfConcurrentRequest` | Počet souběžných požadavků na službu **převodu řeči na text.** Tento graf automaticky vypočítá prostředky procesoru a paměti na základě této hodnoty. | `2` |
| `optimizeForAudioFile`| Zda služba potřebuje optimalizovat pro zvukový vstup prostřednictvím zvukových souborů. Pokud `true`bude tento graf přidělovat službu více prostředků procesoru. | `false` |
| `image.registry`| Registr bitových obrázků **dockeru převodu řeči na text.** | `containerpreview.azurecr.io` |
| `image.repository` | Úložiště obrázků dockeru **pro převod řeči na text.** | `microsoft/cognitive-services-speech-to-text` |
| `image.tag` | Značka image dockeru **převodu řeči na text.** | `latest` |
| `image.pullSecrets` | Tajemství obrázku pro tahání image dockeru **převodu řeči na text.** | |
| `image.pullByHash`| Určuje, zda je bitová kopie dockeru vytahována pomocí hash. Pokud `true` `image.hash` je požadováno . | `false` |
| `image.hash`| Hash **obrázku dockeru převodu řeči na text.** Používá se `image.pullByHash: true`pouze v případě, že .  | |
| `image.args.eula`(povinné) | Označuje, že jste licenci přijali. Jedinou platnou hodnotou je`accept` | |
| `image.args.billing`(povinné) | Hodnota URI koncového bodu fakturace je dostupná na stránce Přehled řeči na webu Azure Portal. | |
| `image.args.apikey`(povinné) | Slouží ke sledování fakturačních údajů. ||
| `service.type` | Typ služby Kubernetes pro **převod řeči na text.** Další podrobnosti najdete v [pokynech k typům služeb Kubernetes](https://kubernetes.io/docs/concepts/services-networking/service/) a ověřte podporu poskytovatele cloudu. | `LoadBalancer` |
| `service.port`|  Port služby **převodřeči na text.** | `80` |
| `service.annotations` | Poznámky **převodu řeči na text** pro metadata služby. Poznámky jsou dvojice hodnot klíče. <br>`annotations:`<br>&nbsp;&nbsp;`some/annotation1: value1`<br>&nbsp;&nbsp;`some/annotation2: value2` | |
| `service.autoScaler.enabled` | Určuje, zda je povolen [horizontální pod autoscaler.](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/) Pokud `true` `speech-to-text-autoscaler` bude nasazena v clusteru Kubernetes. | `true` |
| `service.podDisruption.enabled` | Určuje, zda je povolen [rozpočet přerušení podu.](https://kubernetes.io/docs/concepts/workloads/pods/disruptions/) Pokud `true` `speech-to-text-poddisruptionbudget` bude nasazena v clusteru Kubernetes. | `true` |