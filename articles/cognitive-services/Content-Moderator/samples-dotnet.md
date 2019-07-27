---
title: Ukázky kódu – Content Moderator, .NET
titleSuffix: Azure Cognitive Services
description: Použijte Content Moderator v aplikacích .NET prostřednictvím sady SDK.
services: cognitive-services
author: sanjeev3
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: sample
ms.date: 01/10/2019
ms.author: sajagtap
ms.openlocfilehash: 0c7db3c48f1ff7a141fda84caaad84ac8ff7f85d
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/26/2019
ms.locfileid: "68564421"
---
# <a name="content-moderator-net-sdk-samples"></a>Ukázky Content Moderator .NET SDK

Následující seznam obsahuje odkazy na ukázky kódu, které jsou vytvořené pomocí sady Azure Content Moderator SDK pro .NET.

## <a name="moderation"></a>Moderování

- **Moderování obrázku**: [Vyhodnoťte obrázek pro dospělé a pikantní obsah, text a obličeje](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ImageModeration/Program.cs). Viz [Rychlý start](image-moderation-quickstart-dotnet.md).
- **Vlastní image**: [Střední s vlastními seznamy obrázků](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ImageListManagement/Program.cs). Viz [Rychlý start](image-lists-quickstart-dotnet.md).

> [!NOTE]
> Limit je maximálně **5 seznamů obrázků** a v každém seznamu může být **maximálně 10 000 obrázků**.
>

- **Moderování textu**: [Text obrazovky pro vulgární výrazy a osobní údaje](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/TextModeration/Program.cs) Viz [Rychlý start](text-moderation-quickstart-dotnet.md).
- **Vlastní výrazy**: [Střední s vlastními seznamy termínů](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/TermListManagement/Program.cs). Viz [Rychlý start](term-lists-quickstart-dotnet.md).

> [!NOTE]
> Limit je maximálně **5 seznamů výrazů** a v každém seznamu může být **maximálně 10 000 výrazů**.
>

- **Moderování videa**: [Naskenovat video pro dospělé a pikantní obsah a získat výsledky](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/VideoModeration/Program.cs). Viz [Rychlý start](video-moderation-api.md).

## <a name="review"></a>Zkontrolovat

- **Úlohy imagí**: [Spusťte úlohu moderování, která vyhledá a vytvoří recenze](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ImageJobs/Program.cs). Viz [Rychlý start](moderation-jobs-quickstart-dotnet.md).
- **Recenze obrázků**: [Vytvářejte recenze pro lidskou smyčku](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ImageReviews/Program.cs). Viz [Rychlý start](moderation-reviews-quickstart-dotnet.md).
- **Recenze videí**: [Vytvářejte recenze videí pro lidskou smyčku](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/VideoReviews/Program.cs). Viz [Rychlý start](video-reviews-quickstart-dotnet.md).
- **Recenze přepisu videa**: [Vytváření recenzí přepisů videa pro lidskou smyčku](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/VideoTranscriptReviews/Program.cs) Viz [rychlý Start](video-reviews-quickstart-dotnet.md)

Všechny ukázky pro .NET najdete na stránce [Ukázky Content Moderatoru pro .NET na GitHubu](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/ContentModerator).
