---
title: Ukázky kódu – Content Moderator, .NET
description: Content Moderator použijte v aplikacích .NET pomocí sady SDK.
services: cognitive-services
author: sanjeev3
manager: cgronlun
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: sample
ms.date: 01/10/2019
ms.author: sajagtap
ms.openlocfilehash: 43aa1ca624bce78fa113c34fa19da9ccfea69bbc
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55222095"
---
# <a name="content-moderator-net-sdk-samples"></a>Ukázky Content Moderator .NET SDK

Následující seznam obsahuje odkazy na ukázky kódu, které jsou vytvořené pomocí sady Azure Content Moderator SDK pro .NET.

- **Pomocné knihovny**: [Vytvoření klienta Content Moderator pro použití v jiných vzorcích](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ModeratorHelper/Clients.cs). Viz [Rychlý start](content-moderator-helper-quickstart-dotnet.md).

## <a name="moderation"></a>Moderování

- **Moderování obrázků**: [Vyhodnocení image pro obsahu pro dospělé nebo pikantního obsahu, text a tváří](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ImageModeration/Program.cs). Viz [Rychlý start](image-moderation-quickstart-dotnet.md).
- **Vlastní image**: [Střední s vlastním seznamem obrázků](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ImageListManagement/Program.cs). Viz [Rychlý start](image-lists-quickstart-dotnet.md).

> [!NOTE]
> Limit je maximálně **5 seznamů obrázků** a v každém seznamu může být **maximálně 10 000 obrázků**.
>

- **Moderování textu**: [Text pro vulgárních výrazů a identifikovatelné osobní údaje (PII) obrazovky](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/TextModeration/Program.cs). Viz [Rychlý start](text-moderation-quickstart-dotnet.md).
- **Vlastní podmínky**: [Střední se seznamy vlastní termín](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/TermListManagement/Program.cs). Viz [Rychlý start](term-lists-quickstart-dotnet.md).

> [!NOTE]
> Limit je maximálně **5 seznamů výrazů** a v každém seznamu může být **maximálně 10 000 výrazů**.
>

- **Moderování videa**: [Kontrola video obsahu pro dospělé nebo pikantního obsahu a získání výsledků](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/VideoModeration/Program.cs). Viz [Rychlý start](video-moderation-api.md).

## <a name="review"></a>Revize

- **Obrázek úlohy**: [Spustit úlohu přerušování, který vyhledá a vytvoří revize](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ImageJobs/Program.cs). Viz [Rychlý start](moderation-jobs-quickstart-dotnet.md).
- **Obrázek kontroly**: [Vytvoření kontroly pro lidské v the smyčky](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ImageReviews/Program.cs). Viz [Rychlý start](moderation-reviews-quickstart-dotnet.md).
- **Video kontroly**: [Vytvoření videa revize pro lidské v the smyčky](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/VideoReviews/Program.cs). Viz [Rychlý start](video-reviews-quickstart-dotnet.md).
- **Přepis videa kontroly**: [Vytvoření kontroly přepis videa pro lidské v the smyčky](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/VideoTranscriptReviews/Program.cs) naleznete v tématu [rychlý start](video-reviews-quickstart-dotnet.md)

Všechny ukázky pro .NET najdete na stránce [Ukázky Content Moderatoru pro .NET na GitHubu](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/ContentModerator).
