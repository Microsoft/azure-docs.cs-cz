---
title: Ukázky kódu – Content Moderator, .NET
titleSuffix: Azure Cognitive Services
description: Zjistěte, jak používat Moderátor obsahu služby Azure Cognitive Services ve vašich aplikacích .NET prostřednictvím sady SDK.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: sample
ms.date: 01/10/2019
ms.author: pafarley
ms.openlocfilehash: 930571c841146e0b12efbf7325915ba2b23a7efa
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73744344"
---
# <a name="content-moderator-net-sdk-samples"></a>Ukázky sady Content Moderator .NET SDK

Následující seznam obsahuje odkazy na ukázky kódu, které jsou vytvořené pomocí sady Azure Content Moderator SDK pro .NET.

## <a name="moderation"></a>Moderování

- **Moderování obrázků**: [Vyhodnocení obrázku z hlediska obsahu pro dospělé nebo neslušného obsahu, textu a tváří](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ImageModeration/Program.cs). Podívejte se na [rychlý start sady .NET SDK](dotnet-sdk-quickstart.md).
- **Vlastní obrázky**: [Moderování s vlastními seznamy obrázků](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ImageListManagement/Program.cs). Podívejte se na[rychlý start sady .NET SDK](dotnet-sdk-quickstart.md).

> [!NOTE]
> Limit je maximálně **pět seznamů obrázků** a v každém seznamu může být **maximálně 10 000 obrázků**.
>

- **Moderování textu**: [Text obrazovky pro vulgární výrazy a osobní údaje](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/TextModeration/Program.cs). Podívejte se na[rychlý start sady .NET SDK](dotnet-sdk-quickstart.md).
- **Vlastní výrazy**: [Moderování se seznamy vlastních výrazů](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/TermListManagement/Program.cs). Podívejte se na [rychlý start sady .NET SDK](dotnet-sdk-quickstart.md).

> [!NOTE]
> Limit je maximálně **5 seznamů výrazů** a v každém seznamu může být **maximálně 10 000 výrazů**.
>

- **Moderování videa**: [Kontrola videa z hlediska obsahu pro dospělé nebo neslušného obsahu a získání výsledků](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/VideoModeration/Program.cs). Viz [Rychlý start](video-moderation-api.md).

## <a name="review"></a>Revize

- **Úlohy s obrázky**: [Spuštění úlohy moderování, která vyhledá a vytvoří revize](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ImageJobs/Program.cs). Viz [rychlý start](moderation-jobs-quickstart-dotnet.md).
- **Revize obrázků**: [Vytvoření revizí pro lidský faktor](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ImageReviews/Program.cs). Viz [Rychlý start](dotnet-sdk-quickstart.md).
- **Revize videí**: [Vytvoření videorevizí pro lidský faktor](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/VideoReviews/Program.cs). Viz [Rychlý start](video-reviews-quickstart-dotnet.md).
- **Revize přepisu videa**: [Vytvoření revizí přepisu videa pro lidský faktor](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/VideoTranscriptReviews/Program.cs). Viz [Rychlý start](video-reviews-quickstart-dotnet.md).

Všechny ukázky pro .NET najdete na stránce [Ukázky Content Moderator pro .NET na GitHubu](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/ContentModerator).
