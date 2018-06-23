---
title: Ukázky kódu Azure obsahu moderátora | Microsoft Docs
description: Použití obsahu moderátora ve svých aplikacích
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 01/10/2018
ms.author: sajagtap
ms.openlocfilehash: a7f5b0a7433631e303de47667871cc1354053c08
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35342760"
---
# <a name="net-sdk-samples"></a>Ukázky sady .NET SDK

Následující seznam obsahuje odkazy na ukázky kódu, které jsou vytvořené pomocí sady Azure obsahu moderátora SDK pro .NET.

- **Pomocné knihovny**: [vytvořte obsahu moderátora klienta pro použití v jiných ukázky](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ModeratorHelper/Clients.cs). V tématu [rychlý Start](content-moderator-helper-quickstart-dotnet.md).

## <a name="moderation"></a>Přerušování

- **Obrázek přerušování**: [vyhodnotit bitovou kopii pro obsah pro dospělé a zájem, text a řezy](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ImageModeration/Program.cs). V tématu [rychlý Start](image-moderation-quickstart-dotnet.md).
- **Vlastní image**: [střední pomocí vlastní image seznamů](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ImageListManagement/Program.cs). V tématu [rychlý Start](image-lists-quickstart-dotnet.md).

> [!NOTE]
> Maximální limit je **5 obrázku seznamy** s každou seznamu **není delší než 10 000 image**.
>

- **Text přerušování**: [obrazovky text pro vulgárnost a identifikovatelné osobní údaje (PII)](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/TextModeration/Program.cs). V tématu [rychlý Start](text-moderation-quickstart-dotnet.md).
- **Vlastní podmínky**: [střední s vlastní termín seznamy](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/TermListManagement/Program.cs). V tématu [rychlý Start](term-lists-quickstart-dotnet.md).

> [!NOTE]
> Maximální limit je **5 termín uvádí** s každou seznamu **není delší než 10 000 podmínky**.
>

- **Video přerušování**: [prohledávání video na obsah pro dospělé a zájem a získat výsledky](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/VideoModeration/Program.cs). V tématu [rychlý Start](video-moderation-api.md).

## <a name="review"></a>Zkontrolovat

- **Obrázek úlohy**: [spustit úlohu přerušování, který vyhledá a vytvoří recenze](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ImageJobs/Program.cs). V tématu [rychlý Start](moderation-jobs-quickstart-dotnet.md).
- **Obrázek recenze**: [vytvořit recenze pro lidské v the smyčky](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ImageReviews/Program.cs). V tématu [rychlý Start](moderation-reviews-quickstart-dotnet.md).
- **Zkontroluje video**: [vytvořit video zkontroluje pro lidské v the smyčky](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/VideoReviews/Program.cs). V tématu [rychlý start](video-reviews-quickstart-dotnet.md)
- **Zkontroluje video přepis**: [vytvořit video přepis zkontroluje pro lidské v the smyčky](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/VideoTranscriptReviews/Program.cs) najdete v části [rychlý start](video-reviews-quickstart-dotnet.md)

Zobrazit všechny ukázky rozhraní .NET v [obsahu .NET moderátora ukázky z webu GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/ContentModerator).
