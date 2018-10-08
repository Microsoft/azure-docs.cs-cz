---
title: Sady SDK a ukázky – Content Moderator, Python, Java, Node.js a .NET
titlesuffix: Azure Cognitive Services
description: Získejte sady SDK a ukázky pro Content Moderatora
services: cognitive-services
author: sanjeev3
manager: cgronlun
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: sample
ms.date: 02/27/2018
ms.author: sajagtap
ms.openlocfilehash: a57f6a312b00d7ec3d927c6fda319f1de8663c9c
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/26/2018
ms.locfileid: "47220444"
---
# <a name="content-moderator-sdks-and-samples"></a>Sady Content Moderator SDK a ukázky

## <a name="sdks-for-python-java-nodejs-and-net"></a>Sady SDK pro Python, Java, Node.js a .NET

- [Sada Content Moderator SDK pro Python](https://pypi.python.org/pypi/azure-cognitiveservices-vision-contentmoderator)
- [Sada Content Moderator SDK pro Javu](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-cognitiveservices-contentmoderator%22)
- [Sada Content Moderator SDK pro Node.js](https://www.npmjs.com/package/azure-cognitiveservices-contentmoderator)
- [Sada Content Moderator SDK pro .NET](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/)

## <a name="net-sdk-samples"></a>Ukázky .NET SDK

Následující seznam obsahuje odkazy na ukázky kódu, které jsou vytvořené pomocí sady Azure Content Moderator SDK pro .NET.

- **Knihovny pomocných rutin**: [Vytvoření klienta Content Moderator pro použití v jiných ukázkách](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ModeratorHelper/Clients.cs). Viz [Rychlý start](content-moderator-helper-quickstart-dotnet.md).

### <a name="moderation"></a>Moderování 
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

### <a name="review"></a>Revize
- **Úlohy s obrázky**: [Spuštění úlohy moderování, která vyhledá a vytvoří revize](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ImageJobs/Program.cs). Viz [Rychlý start](moderation-jobs-quickstart-dotnet.md).
- **Revize obrázků**: [Vytvoření revizí pro lidský faktor](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ImageReviews/Program.cs). Viz [Rychlý start](moderation-reviews-quickstart-dotnet.md).
- **Revize videí**: [Vytvoření videorevizí pro lidský faktor](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/VideoReviews/Program.cs). Viz [Rychlý start](video-reviews-quickstart-dotnet.md).
- **Revize přepisu videa**: [Vytvoření revizí přepisu videa pro lidský faktor](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/VideoTranscriptReviews/Program.cs). Viz [Rychlý start](video-reviews-quickstart-dotnet.md).

Všechny ukázky pro .NET najdete na stránce [Ukázky Content Moderator pro .NET na GitHubu](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/ContentModerator).

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
