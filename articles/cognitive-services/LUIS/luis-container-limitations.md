---
title: Omezení kontejnerů – luis
titleSuffix: Azure Cognitive Services
description: Jazyky kontejneru LUIS, které jsou podporovány.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: dapine
ms.openlocfilehash: bd8a7a63113bcf4e972ab08655aa58b35ddff03d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "73507855"
---
# <a name="language-understanding-luis-container-limitations"></a>Omezení kontejnerů pro porozumění jazykům (LUIS)

Kontejnery LUIS mají několik významných omezení. Z nepodporovaných závislostí na podmnožinu podporovaných jazyků, tento článek podrobně popisuje tato omezení.

## <a name="supported-dependencies-for-latest-container"></a>Podporované závislosti `latest` pro kontejner

Nejnovější kontejner LUIS, vydaný na [//build/ 2019](https://news.microsoft.com/build2019/), bude podporovat:

* [Nové předem sestavené domény](luis-reference-prebuilt-domains.md): tyto domény zaměřené na rozlehlé sítě zahrnují entity, ukázkové projevy a vzory. Rozšiřte tyto domény pro vlastní potřebu.

## <a name="unsupported-dependencies-for-latest-container"></a>Nepodporované závislosti `latest` pro kontejner

Chcete-li [exportovat pro kontejner](luis-container-howto.md#export-packaged-app-from-luis), je nutné odebrat nepodporované závislosti z aplikace LUIS. Při pokusu o export pro kontejner, portál LUIS hlásí tyto nepodporované funkce, které je třeba odebrat.

Aplikaci LUIS můžete použít, pokud **neobsahuje** žádné z následujících závislostí:

Nepodporované konfigurace aplikací|Podrobnosti|
|--|--|
|Nepodporované jazykové verze kontejnerů| Holandština (`nl-NL`)<br>V`ja-JP`japonštině ( )<br>Němčina je podporována pouze [tokenizérem 1.0.2](luis-language-support.md#custom-tokenizer-versions).|
|Nepodporované entity pro všechny jazykové verze|Předdefinovaná entita [KeyPhrase](luis-reference-prebuilt-keyphrase.md) pro všechny jazykové verze|
|Nepodporované entity pro`en-US`jazykovou verzi angličtiny ( ).|[GeografieV2](luis-reference-prebuilt-geographyV2.md) předem sestavené entity|
|Řečové priming|Externí závislosti nejsou v kontejneru podporovány.|
|Analýza mínění|Externí závislosti nejsou v kontejneru podporovány.|
|Kontrola pravopisu bingu|Externí závislosti nejsou v kontejneru podporovány.|

## <a name="languages-supported"></a>Podporované jazyky

Kontejnery LUIS podporují podmnožinu [jazyků podporovaných](luis-language-support.md#languages-supported) vlastní LUIS. Kontejnery LUIS jsou schopné pochopit projevy v následujících jazycích:

| Jazyk | Národní prostředí | Předem vyestavěná doména | Předem sestavená entita | Doporučení pro seznam frází | **[Analýza textu](../text-analytics/language-support.md)<br>(Sentiment a<br>Klíčová slova)|
|--|--|:--:|:--:|:--:|:--:|
| Americká angličtina | `en-US` | ✔️ | ✔️ | ✔️ | ✔️ |
| *[Čínština](#chinese-support-notes) |`zh-CN` | ✔️ | ✔️ | ✔️ | ❌ |
| francouzština (Francie) |`fr-FR` | ✔️ | ✔️ | ✔️ | ✔️ |
| Francouzština (Kanada) |`fr-CA` | ❌ | ❌ | ❌ | ✔️ |
| Němčina |`de-DE` | ✔️ | ✔️ | ✔️ | ✔️ |
| Hindština | `hi-IN`| ❌ | ❌ | ❌ | ❌ |
| Italština |`it-IT` | ✔️ | ✔️ | ✔️ | ✔️ |
| Korejština |`ko-KR` | ✔️ | ❌ | ❌ | *Pouze klíčová fráze* |
| Portugalština (Brazílie) |`pt-BR` | ✔️ | ✔️ | ✔️ | ne všechny subkultury |
| Španělština (Španělsko) |`es-ES` | ✔️ | ✔️ |✔️|✔️|
| Španělština (Mexiko)|`es-MX` | ❌ | ❌ |✔️|✔️|
| Turečtina | `tr-TR` |✔️| ❌ | ❌ | *Pouze sentiment* |

[!INCLUDE [Chinese language support notes](includes/chinese-language-support-notes.md)]

[!INCLUDE [Text Analytics support notes](includes/text-analytics-support-notes.md)]