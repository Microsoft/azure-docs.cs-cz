---
title: Obsah sady SDK přerušování a ukázky pro moderátora obsahu Azure | Microsoft Docs
description: Získání sady SDK a ukázky pro obsahu moderátora
services: cognitive-services
author: sanjeev3
manager: mikemcca
ms.service: cognitive-services
ms.component: content-moderator
ms.topic: article
ms.date: 02/27/2018
ms.author: sajagtap
ms.openlocfilehash: 40b8fc0f63383e837f0813f876f20806e6e8c6eb
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "35342483"
---
# <a name="content-moderator-sdks-and-samples"></a>Obsah sady SDK moderátora a ukázky

## <a name="sdks-for-python-java-nodejs-and-net"></a>Sady SDK pro Python, Java, Node.js a rozhraní .NET

- [Obsahu moderátora SDK pro jazyk Python](https://pypi.python.org/pypi/azure-cognitiveservices-vision-contentmoderator)
- [Obsahu moderátora SDK pro jazyk Java](https://search.maven.org/#search%7Cga%7C1%7Ca%3A%22azure-cognitiveservices-contentmoderator%22)
- [Obsahu moderátora SDK pro Node.js](https://www.npmjs.com/package/azure-cognitiveservices-contentmoderator)
- [Obsahu moderátora sady SDK pro .NET](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/)

## <a name="net-sdk-samples"></a>Ukázky sady .NET SDK

Následující seznam obsahuje odkazy na ukázky kódu, které jsou vytvořené pomocí sady Azure obsahu moderátora SDK pro .NET.

- **Pomocné knihovny**: [vytvořte obsahu moderátora klienta pro použití v jiných ukázky](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ModeratorHelper/Clients.cs). V tématu [rychlý Start](content-moderator-helper-quickstart-dotnet.md).

### <a name="moderation"></a>Přerušování 
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

### <a name="review"></a>Zkontrolovat
- **Obrázek úlohy**: [spustit úlohu přerušování, který vyhledá a vytvoří recenze](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ImageJobs/Program.cs). V tématu [rychlý Start](moderation-jobs-quickstart-dotnet.md).
- **Obrázek recenze**: [vytvořit recenze pro lidské v the smyčky](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/ImageReviews/Program.cs). V tématu [rychlý Start](moderation-reviews-quickstart-dotnet.md).
- **Zkontroluje video**: [vytvořit video zkontroluje pro lidské v the smyčky](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/VideoReviews/Program.cs). V tématu [rychlý start](video-reviews-quickstart-dotnet.md)
- **Zkontroluje video přepis**: [vytvořit video přepis zkontroluje pro lidské v the smyčky](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/blob/master/ContentModerator/VideoTranscriptReviews/Program.cs) najdete v části [rychlý start](video-reviews-quickstart-dotnet.md)

Zobrazit všechny ukázky rozhraní .NET v [obsahu .NET moderátora ukázky z webu GitHub](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/ContentModerator).

## <a name="rest-api-samples-in-c"></a>Ukázky rozhraní API REST v jazyce C#

- [Moderování obrázků](https://github.com/MicrosoftContentModerator/ContentModerator-API-Samples/tree/master/ImageModeration)
- [Moderování textu](https://github.com/MicrosoftContentModerator/ContentModerator-API-Samples/tree/master/TextModeration)
- [Moderování videa](https://github.com/MicrosoftContentModerator/ContentModerator-API-Samples/tree/master/VideoModeration)
- [Recenze obrázků](https://github.com/MicrosoftContentModerator/ContentModerator-API-Samples/tree/master/ImageReviews)
- [Obrázek úlohy](https://github.com/MicrosoftContentModerator/ContentModerator-API-Samples/tree/master/ImageJob)

Návody pro ukázek najdete na [na vyžádání webinář](https://info.microsoft.com/cognitive-services-content-moderator-ondemand.html).

## <a name="tutorials"></a>Kurzy
- [přerušování katalogu elektronické obchodování](https://github.com/MicrosoftContentModerator/samples-eCommerceCatalogModeration). V tématu [kurzu](ecommerce-retail-catalog-moderation.md).
- [Přerušování obsahu Facebook](https://github.com/MicrosoftContentModerator/samples-fbPageModeration). V tématu [kurzu](facebook-post-moderation.md).
- [Video a přepis přerušování a zkontrolujte řešení](https://github.com/MicrosoftContentModerator/VideoReviewConsoleApp) najdete v části [kurzu](video-transcript-moderation-review-tutorial-dotnet.md)

## <a name="on-demand-webinars"></a>Webináře na vyžádání
- [Počítače s asistencí obsahu přerušování ve velkém měřítku s moderátora obsahu](https://info.microsoft.com/cognitive-services-content-moderator-ondemand.html)
