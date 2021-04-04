---
title: Instalace kontejnerů řeči
titleSuffix: Azure Cognitive Services
description: Podrobnější informace o možnostech konfigurace grafu Helm řeči na text
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 05/05/2020
ms.author: trbye
ms.openlocfilehash: 85c4e0641e1989ddea6c8aa8b8a8895a966a5ddb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "96002261"
---
### <a name="speech-to-text-sub-chart-chartsspeechtotext"></a>Převod řeči na text (dílčí graf: grafy/speechToText)

Chcete-li potlačit graf "zastřešující", přidejte předponu pro `speechToText.` libovolný parametr, aby byl konkrétnější. Například přepíše odpovídající parametr, například `speechToText.numberOfConcurrentRequest` Overrides `numberOfConcurrentRequest` .

|Parametr|Popis|Výchozí|
| -- | -- | -- |
| `enabled` | Zda je povolena služba **Převod řeči na text** . | `false` |
| `numberOfConcurrentRequest` | Počet souběžných požadavků služby **Speech-to-text** . Tento graf automaticky vypočítá prostředky procesoru a paměti na základě této hodnoty. | `2` |
| `optimizeForAudioFile`| Zda služba potřebuje k optimalizaci zvukového vstupu přes zvukové soubory. Pokud `true` bude tento graf přidělovat službě více prostředků procesoru. | `false` |
| `image.registry`| Registr v programu **Speech to text** Docker image. | `containerpreview.azurecr.io` |
| `image.repository` | Úložiště obrázků Docker pro dopředné **zpracování textu** . | `microsoft/cognitive-services-speech-to-text` |
| `image.tag` | Značka obrázku Docker **pro domluvené slovo na text** | `latest` |
| `image.pullSecrets` | Obrazová tajemství pro vyžádání image Docker pro **text v textu** | |
| `image.pullByHash`| Zda je obrázek Docker načítán pomocí algoritmu hash. Pokud `true` `image.hash` je vyžadováno. | `false` |
| `image.hash`| Hodnota hash obrázku Docker **na text** Používá se pouze v případě `image.pullByHash: true` .  | |
| `image.args.eula` požadovanou | Označuje, že jste přijali licenci. Jediná platná hodnota je `accept` | |
| `image.args.billing` požadovanou | Hodnota identifikátoru URI fakturačního koncového bodu je k dispozici na stránce Přehled řeči Azure Portal. | |
| `image.args.apikey` požadovanou | Používá se ke sledování fakturačních informací. ||
| `service.type` | Typ služby Kubernetes služby **Speech-to-text** . Další podrobnosti najdete v [pokynech k typům služby Kubernetes](https://kubernetes.io/docs/concepts/services-networking/service/) a ověření podpory poskytovatele cloudu. | `LoadBalancer` |
| `service.port`|  Port služby převodu **řeči na text** . | `80` |
| `service.annotations` | Poznámky **k textovému** převodu pro metadata služby. Poznámky jsou páry klíč-hodnota. <br>`annotations:`<br>&nbsp;&nbsp;`some/annotation1: value1`<br>&nbsp;&nbsp;`some/annotation2: value2` | |
| `service.autoScaler.enabled` | Určuje, zda je [Automatické škálování vodorovně pod](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/) povoleno. Pokud se `true` `speech-to-text-autoscaler` bude nasadit do clusteru Kubernetes. | `true` |
| `service.podDisruption.enabled` | Zda je povoleno [rozpočet přerušení pod](https://kubernetes.io/docs/concepts/workloads/pods/disruptions/) . Pokud se `true` `speech-to-text-poddisruptionbudget` bude nasadit do clusteru Kubernetes. | `true` |

#### <a name="sentiment-analysis-sub-chart-chartsspeechtotext"></a>Analýza mínění (dílčí graf: grafy/speechToText)

Počínaje verzí v 2.2.0 kontejneru převodů textu na text a v 0.2.0 grafu Helm se k analýze mínění používá následující parametry pomocí rozhraní API pro analýzu textu.

|Parametr|Popis|Hodnoty|Výchozí|
| --- | --- | --- | --- |
|`textanalytics.enabled`| Zda je povolena služba **Analýza textu**| true nebo false| `false`|
|`textanalytics.image.registry`| Registr **Text-Analytics** Docker image| platný registr imagí Docker| |
|`textanalytics.image.repository`| Úložiště obrázků Docker **pro analýzu textu**| platné úložiště imagí Docker| |
|`textanalytics.image.tag`| Značka obrázku Docker **pro analýzu textu**| platná značka image Docker| |
|`textanalytics.image.pullSecrets`| Obrazová tajná zpráva pro vyžádání image Docker pro **analýzu textu**| platný název tajných kódů| |
|`textanalytics.image.pullByHash`| Určuje, jestli si vybíráte image Docker pomocí algoritmu hash.  Pokud `yes` `image.hash` je třeba, musí mít také. Pokud `no` je nastaveno na hodnotu false (NEPRAVDA). Výchozí je `false`.| true nebo false| `false`|
|`textanalytics.image.hash`| Hodnota hash obrázku Docker **pro analýzu textu** . Používejte ho jenom s `image.pullByHash:true` .| platná hodnota hash obrázku Docker | |
|`textanalytics.image.args.eula`| Jeden z požadovaných argumentů kontejneru **Text-Analytics** , který indikuje, že jste přijali licenci. Hodnota této možnosti musí být: `accept` .| `accept`, pokud chcete použít kontejner | |
|`textanalytics.image.args.billing`| Jeden z požadovaných argumentů kontejneru **Text-Analytics** , který určuje identifikátor URI koncového bodu fakturace. Hodnota identifikátoru URI fakturačního koncového bodu je k dispozici na stránce Přehled řeči Azure Portal.|platný identifikátor URI fakturačního koncového bodu||
|`textanalytics.image.args.apikey`| Jeden z požadovaných argumentů kontejneru **Text-Analytics** , který se používá ke sledování fakturačních informací.| platný apikey||
|`textanalytics.cpuRequest`| Požadovaný procesor pro kontejner **Text-Analytics**| int| `3000m`|
|`textanalytics.cpuLimit`| Omezený procesor pro kontejner **Text-Analytics**| | `8000m`|
|`textanalytics.memoryRequest`| Požadovaná paměť pro kontejner **Text-Analytics**| | `3Gi`|
|`textanalytics.memoryLimit`| Omezená paměť pro kontejner **Text-Analytics**| | `8Gi`|
|`textanalytics.service.sentimentURISuffix`| Přípona identifikátoru URI analýzy mínění je celý identifikátor URI ve formátu "http:// `<service>` : `<port>` / `<sentimentURISuffix>` ". | | `text/analytics/v3.0-preview/sentiment`|
|`textanalytics.service.type`| Typ služby **Analýza textu** v Kubernetes. Viz [typy služeb Kubernetes](https://kubernetes.io/docs/concepts/services-networking/service/) | platný typ služby Kubernetes | `LoadBalancer` |
|`textanalytics.service.port`| Port služby **Analýza textu**| int| `50085`|
|`textanalytics.service.annotations`| Poznámky, které uživatelé mohou přidat do metadat služby **Text-Analytics** . Například:<br/> **anotac**<br/>`   `**některé/annotation1: hodnota1**<br/>`  `**některé/annotation2: hodnota2** | poznámky, jedna na každý řádek| |
|`textanalytics.serivce.autoScaler.enabled`| Určuje, zda je povoleno [Automatické škálování vodorovně pod](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/) . Pokud je povoleno, `text-analytics-autoscaler` bude nasazena v clusteru Kubernetes. | true nebo false| `true`|
|`textanalytics.service.podDisruption.enabled`| Určuje, zda je povoleno [rozpočet pod přerušením](https://kubernetes.io/docs/concepts/workloads/pods/disruptions/) . Pokud je povoleno, `text-analytics-poddisruptionbudget` bude nasazena v clusteru Kubernetes.| true nebo false| `true`|
