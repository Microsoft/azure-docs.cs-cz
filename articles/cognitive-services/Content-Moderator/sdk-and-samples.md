---
title: Sady SDK a ukázky – Content Moderator, Python, Java, Node.js a .NET
titlesuffix: Azure Cognitive Services
description: Získejte sady SDK a ukázky pro Content Moderatora
services: cognitive-services
author: sanjeev3
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: sample
ms.date: 02/27/2018
ms.author: sajagtap
ms.openlocfilehash: 929655f257dced1e12645d4d751b1475e2497b49
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55868357"
---
# <a name="content-moderator-sdks-and-samples"></a>Sady Content Moderator SDK a ukázky

## <a name="sdks-for-python-java-nodejs-and-net"></a>Sady SDK pro Python, Java, Node.js a .NET

- [Sada Content Moderator SDK pro Python](https://pypi.python.org/pypi/azure-cognitiveservices-vision-contentmoderator)
- [Sada Content Moderator SDK pro Javu](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-cognitiveservices-contentmoderator%22)
- [Sada Content Moderator SDK pro Node.js](https://www.npmjs.com/package/azure-cognitiveservices-contentmoderator)
- [Sada Content Moderator SDK pro .NET](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/)

## <a name="net-sdk-samples"></a>Ukázky .NET SDK

Následující seznam obsahuje odkazy na ukázky kódu, které jsou vytvořené pomocí sady Azure Content Moderator SDK pro .NET.

- **Pomocné knihovny**: [Vytvoření klienta Content Moderator pro použití v jiných vzorcích](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ModeratorHelper/Clients.cs). Viz [Rychlý start](content-moderator-helper-quickstart-dotnet.md).

### <a name="moderation"></a>Moderování 
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

### <a name="review"></a>Revize
- **Obrázek úlohy**: [Spustit úlohu přerušování, který vyhledá a vytvoří revize](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ImageJobs/Program.cs). Viz [Rychlý start](moderation-jobs-quickstart-dotnet.md).
- **Obrázek kontroly**: [Vytvoření kontroly pro lidské v the smyčky](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ImageReviews/Program.cs). Viz [Rychlý start](moderation-reviews-quickstart-dotnet.md).
- **Video kontroly**: [Vytvoření videa revize pro lidské v the smyčky](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/VideoReviews/Program.cs). Viz [Rychlý start](video-reviews-quickstart-dotnet.md).
- **Přepis videa kontroly**: [Vytvoření kontroly přepis videa pro lidské v the smyčky](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/VideoTranscriptReviews/Program.cs) naleznete v tématu [rychlý start](video-reviews-quickstart-dotnet.md)

Všechny ukázky pro .NET najdete na stránce [Ukázky Content Moderatoru pro .NET na GitHubu](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/ContentModerator).

## <a name="rest-api-samples-in-c"></a>Ukázky REST API v C#

- [Moderování obrázků](https://github.com/MicrosoftContentModerator/ContentModerator-API-Samples/tree/master/ImageModeration)
- [Moderování textu](https://github.com/MicrosoftContentModerator/ContentModerator-API-Samples/tree/master/TextModeration)
- [Moderování videa](https://github.com/MicrosoftContentModerator/ContentModerator-API-Samples/tree/master/VideoModeration)
- [Recenze obrázků](https://github.com/MicrosoftContentModerator/ContentModerator-API-Samples/tree/master/ImageReviews)
- [Úlohy s obrázky](https://github.com/MicrosoftContentModerator/ContentModerator-API-Samples/tree/master/ImageJob)

Pokud chcete získat názorné postupy pro tyto ukázky, zaregistrujte si [webinář na vyžádání](https://info.microsoft.com/cognitive-services-content-moderator-ondemand.html).

## <a name="tutorials"></a>Kurzy
- [Moderování katalogů elektronického obchodování](https://github.com/MicrosoftContentModerator/samples-eCommerceCatalogModeration). Zobrazit [kurz](ecommerce-retail-catalog-moderation.md).
- [Moderování obsahu pro Facebook](https://github.com/MicrosoftContentModerator/samples-fbPageModeration). Zobrazit [kurz](facebook-post-moderation.md).
- [Moderování videa a přepis videa a kontrola řešení](https://github.com/MicrosoftContentModerator/VideoReviewConsoleApp). Zobrazit [kurz](video-transcript-moderation-review-tutorial-dotnet.md).

## <a name="on-demand-webinars"></a>Webináře na vyžádání
- [Moderování obsahu s podporou počítače ve velkém měřítku pomocí Content Moderatoru](https://info.microsoft.com/cognitive-services-content-moderator-ondemand.html)
