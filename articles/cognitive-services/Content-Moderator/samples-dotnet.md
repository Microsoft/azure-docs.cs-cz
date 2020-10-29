---
title: Ukázky kódu – Content Moderator, .NET
titleSuffix: Azure Cognitive Services
description: Naučte se používat Azure Cognitive Services Content Moderator v aplikacích .NET prostřednictvím sady SDK.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: sample
ms.date: 01/10/2019
ms.author: pafarley
ms.openlocfilehash: a746a4baf172bcb6728c06f2a31b460139377e46
ms.sourcegitcommit: d76108b476259fe3f5f20a91ed2c237c1577df14
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/29/2020
ms.locfileid: "92912918"
---
# <a name="content-moderator-net-sdk-samples"></a>Ukázky Content Moderator .NET SDK

Následující seznam obsahuje odkazy na ukázky kódu, které jsou vytvořené pomocí sady Azure Content Moderator SDK pro .NET.

## <a name="moderation"></a>Moderování

- **Moderování obrázků** : [Vyhodnocení obrázku z hlediska obsahu pro dospělé nebo neslušného obsahu, textu a tváří](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ImageModeration/Program.cs). Projděte si [rychlý Start sady .NET SDK](./client-libraries.md?pivots=programming-language-csharp%253fpivots%253dprogramming-language-csharp).
- **Vlastní obrázky** : [Moderování s vlastními seznamy obrázků](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ImageListManagement/Program.cs). Projděte si[rychlý Start sady .NET SDK](./client-libraries.md?pivots=programming-language-csharp%253fpivots%253dprogramming-language-csharp).

> [!NOTE]
> Limit je maximálně **pět seznamů obrázků** a v každém seznamu může být **maximálně 10 000 obrázků** .
>

- **Moderování textu** : [text obrazovky pro vulgární výrazy a osobní údaje](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/TextModeration/Program.cs). Projděte si[rychlý Start sady .NET SDK](./client-libraries.md?pivots=programming-language-csharp%253fpivots%253dprogramming-language-csharp).
- **Vlastní výrazy** : [Moderování se seznamy vlastních výrazů](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/TermListManagement/Program.cs). Projděte si [rychlý Start sady .NET SDK](./client-libraries.md?pivots=programming-language-csharp%253fpivots%253dprogramming-language-csharp).

> [!NOTE]
> Limit je maximálně **5 seznamů výrazů** a v každém seznamu může být **maximálně 10 000 výrazů** .
>

- **Moderování videa** : [Kontrola videa z hlediska obsahu pro dospělé nebo neslušného obsahu a získání výsledků](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/VideoModeration/Program.cs). Viz [Rychlý start](video-moderation-api.md).

## <a name="review"></a>Opakování

- **Úlohy s obrázky** : [Spuštění úlohy moderování, která vyhledá a vytvoří revize](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ImageJobs/Program.cs). Viz [rychlý Start](moderation-jobs-quickstart-dotnet.md).
- **Revize obrázků** : [Vytvoření revizí pro lidský faktor](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ImageReviews/Program.cs). Viz [rychlý Start](./client-libraries.md?pivots=programming-language-csharp%253fpivots%253dprogramming-language-csharp).
- **Revize videí** : [Vytvoření videorevizí pro lidský faktor](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/VideoReviews/Program.cs). Viz [Rychlý start](video-reviews-quickstart-dotnet.md).
- **Revize přepisu videa** : [Vytvoření revizí přepisu videa pro lidský faktor](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/VideoTranscriptReviews/Program.cs). Viz [Rychlý start](video-reviews-quickstart-dotnet.md).

Všechny ukázky pro .NET najdete na stránce [Ukázky Content Moderator pro .NET na GitHubu](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/ContentModerator).