---
title: Ukázky kódu – Content Moderator, .NET
description: Používejte Content Moderator ve svých aplikacích.
services: cognitive-services
author: sanjeev3
manager: cgronlun
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: sample
ms.date: 01/10/2018
ms.author: sajagtap
ms.openlocfilehash: c5465a2de0924037eb93b9de652e93e85f166737
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/26/2018
ms.locfileid: "47223147"
---
# <a name="net-sdk-samples"></a>Ukázky .NET SDK

Následující seznam obsahuje odkazy na ukázky kódu, které jsou vytvořené pomocí sady Azure Content Moderator SDK pro .NET.

- **Knihovny pomocných rutin**: [Vytvoření klienta Content Moderatoru pro použití v jiných ukázkách](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ModeratorHelper/Clients.cs). Viz [Rychlý start](content-moderator-helper-quickstart-dotnet.md).

## <a name="moderation"></a>Moderování

- **Moderování obrázků**: [Vyhodnocení obrázku z hlediska obsahu pro dospělé nebo neslušného obsahu, textu a tváří](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ImageModeration/Program.cs). Viz [Rychlý start](image-moderation-quickstart-dotnet.md).
- **Vlastní obrázky**: [Moderování s vlastními seznamy obrázků](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ImageListManagement/Program.cs). Viz [Rychlý start](image-lists-quickstart-dotnet.md).

> [!NOTE]
> Limit je maximálně **5 seznamů obrázků** a v každém seznamu může být **maximálně 10 000 obrázků**.
>

- **Moderování textu**: [Kontrola textu z hlediska vulgárních výrazů a identifikovatelných osobních údajů](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/TextModeration/Program.cs). Viz [Rychlý start](text-moderation-quickstart-dotnet.md).
- **Vlastní výrazy**: [Moderování se seznamy vlastních výrazů](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/TermListManagement/Program.cs). Viz [Rychlý start](term-lists-quickstart-dotnet.md).

> [!NOTE]
> Limit je maximálně **5 seznamů výrazů** a v každém seznamu může být **maximálně 10 000 výrazů**.
>

- **Moderování videa**: [Kontrola videa z hlediska obsahu pro dospělé nebo neslušného obsahu a získání výsledků](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/VideoModeration/Program.cs). Viz [Rychlý start](video-moderation-api.md).

## <a name="review"></a>Revize

- **Úlohy s obrázky**: [Spuštění úlohy moderování, která vyhledá a vytvoří revize](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ImageJobs/Program.cs). Viz [Rychlý start](moderation-jobs-quickstart-dotnet.md).
- **Revize obrázků**: [Vytvoření revizí pro lidský faktor](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ImageReviews/Program.cs). Viz [Rychlý start](moderation-reviews-quickstart-dotnet.md).
- **Revize videí**: [Vytvoření videorevizí pro lidský faktor](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/VideoReviews/Program.cs). Viz [Rychlý start](video-reviews-quickstart-dotnet.md).
- **Revize přepisu videa**: [Vytvoření revizí přepisu videa pro lidský faktor](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/VideoTranscriptReviews/Program.cs). Viz [Rychlý start](video-reviews-quickstart-dotnet.md).

Všechny ukázky pro .NET najdete na stránce [Ukázky Content Moderatoru pro .NET na GitHubu](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/ContentModerator).
