---
title: Omezení kontejneru – LUIS
titleSuffix: Azure Cognitive Services
description: Podporované jazyky kontejneru LUIS.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 5c76e45f09a0f432d2775eb19d3dcaa668294e6f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "96002327"
---
# <a name="language-understanding-luis-container-limitations"></a>Omezení kontejneru Language Understanding (LUIS)

Kontejnery LUIS mají několik důležitých omezení. Tento článek podrobně popisuje tato omezení z nepodporovaných závislostí až po podmnožinu podporovaných jazyků.

## <a name="supported-dependencies-for-latest-container"></a>Podporované závislosti pro `latest` kontejner

Nejnovější kontejner LUIS podporuje:

* [Nové předem připravené domény](luis-reference-prebuilt-domains.md): tyto domény zaměřené na podnikové sítě zahrnují entity, příklady projevy a vzory. Rozšíří tyto domény pro vlastní použití.

## <a name="unsupported-dependencies-for-latest-container"></a>Nepodporované závislosti pro `latest` kontejner

Pokud chcete [exportovat kontejner](luis-container-howto.md#export-packaged-app-from-luis), musíte z aplikace Luis odebrat nepodporované závislosti. Při pokusu o export do kontejneru nahlásí portál LUIS tyto nepodporované funkce, které je třeba odebrat.

Aplikaci LUIS můžete **použít, pokud neobsahuje žádnou z** následujících závislostí:

Nepodporované konfigurace aplikací|Podrobnosti|
|--|--|
|Nepodporované jazykové verze kontejneru| Jazyky holandštiny ( `nl-NL` ), japonština ( `ja-JP` ) a němčina ( `de-DE` ) jsou podporovány pouze s [1.0.2 provádějících tokenizaci](luis-language-support.md#custom-tokenizer-versions).|
|Nepodporované entity pro všechny jazykové verze|[KeyPhrase](luis-reference-prebuilt-keyphrase.md) předem vytvořenou entitu pro všechny jazykové verze|
|Nepodporované entity pro `en-US` jazykovou verzi Angličtina ()|[GeographyV2](luis-reference-prebuilt-geographyV2.md) předem připravené entity|
|Neprojevení řeči|Externí závislosti nejsou v kontejneru podporovány.|
|Analýza mínění|Externí závislosti nejsou v kontejneru podporovány.|
|Kontrola pravopisu Bingu|Externí závislosti nejsou v kontejneru podporovány.|

## <a name="languages-supported"></a>Podporované jazyky

Kontejnery LUIS podporují podmnožinu jazyků, které [podporuje](luis-language-support.md#languages-supported) Luis správně. Kontejnery LUIS jsou schopné pochopit projevy v následujících jazycích:

| Jazyk | Národní prostředí | Předem sestavená doména | Předem vytvořená entita | Doporučení pro seznam frází | **[Analýza textu](../text-analytics/language-support.md)<br>(Mínění a<br>Klíčov|
|--|--|:--:|:--:|:--:|:--:|
| Angličtina (Spojené státy) | `en-US` | ✔️ | ✔️ | ✔️ | ✔️ |
| Arabština (Preview – moderní standardní arabština) |`ar-AR`|❌|❌|❌|❌|
| *[Čínština](#chinese-support-notes) |`zh-CN` | ✔️ | ✔️ | ✔️ | ❌ |
| francouzština (Francie) |`fr-FR` | ✔️ | ✔️ | ✔️ | ✔️ |
| Francouzština (Kanada) |`fr-CA` | ❌ | ❌ | ❌ | ✔️ |
| Němčina |`de-DE` | ✔️ | ✔️ | ✔️ | ✔️ |
| Hindština | `hi-IN`| ❌ | ❌ | ❌ | ❌ |
| Italština |`it-IT` | ✔️ | ✔️ | ✔️ | ✔️ |
| Korejština |`ko-KR` | ✔️ | ❌ | ❌ | Jenom *klíčová fráze* |
| Maráthština | `mr-IN`|❌|❌|❌|❌|
| Portugalština (Brazílie) |`pt-BR` | ✔️ | ✔️ | ✔️ | Ne všechny dílčí kultury |
| Španělština (Španělsko) |`es-ES` | ✔️ | ✔️ |✔️|✔️|
| Španělština (Mexiko)|`es-MX` | ❌ | ❌ |✔️|✔️|
| Tamilština | `ta-IN`|❌|❌|❌|❌|
| Telugština | `te-IN`|❌|❌|❌|❌|
| Turečtina | `tr-TR` |✔️| ❌ | ❌ | Jenom *mínění* |

[!INCLUDE [Chinese language support notes](includes/chinese-language-support-notes.md)]

[!INCLUDE [Text Analytics support notes](includes/text-analytics-support-notes.md)]
