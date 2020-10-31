---
title: Ukázky sady SDK pro Vyhledávání na webu Bingu
titleSuffix: Azure Cognitive Services
description: Použijte sadu SDK pro Vyhledávání na webu Bingu k přidání vyhledávacích funkcí k vaší aplikaci v Pythonu, Node.js, C# nebo Javě.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-web-search
ms.topic: sample
ms.date: 05/15/2019
ms.author: aahi
ms.openlocfilehash: b31335df7c1dc48f82699aa3676561721474071d
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2020
ms.locfileid: "93076210"
---
# <a name="bing-web-search-sdk-samples"></a>Ukázky sady SDK pro Vyhledávání na webu Bingu

> [!WARNING]
> Rozhraní API pro vyhledávání Bingu přesouváte z Cognitive Services na Vyhledávání Bingu služby. Od **30. října 2020** musí být všechny nové instance vyhledávání Bingu zřízené [podle popsaného procesu.](https://aka.ms/cogsvcs/bingmove)
> Rozhraní API pro vyhledávání Bingu zřízené pomocí Cognitive Services budou podporované v následujících třech letech nebo na konci smlouva Enterprise, podle toho, co nastane dřív.
> Pokyny k migraci najdete v tématu [vyhledávání Bingu Services](https://aka.ms/cogsvcs/bingmigration).

Sada SDK pro Vyhledávání na webu Bingu je dostupná v Pythonu, Node.js, C# a Javě. Na GitHubu jsou k dispozici ukázky kódu, požadavky a pokyny k sestavení. Pokryté jsou následující scénáře:

* Dotaz na jedno slovo a tisk názvu a adresy URL prvního výsledku z nalezených webových stránek, obrázků, článků zpráv a videí
* Dotaz na frázi, ověření počtu výsledků a tisk názvu a adresy URL prvního výsledku
* Dotaz na hledaný termín s filtrem odpovědí nastaveným na `news` a tisk podrobností výsledků zpráv
* Dotaz na hledaný termín s parametry `answerCount` a `promote` a následný tisk podrobností výsledků.

## <a name="sdks-and-libraries"></a>Sady SDK a knihovny

Tyto odkazy použijte pro přístup k sadě SDK pro váš preferovaný jazyk.

* Začínáme s [ukázkami Pythonu](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples)
  * Definice a závislosti najdete v [knihovnách Pythonu](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cognitiveservices/azure-cognitiveservices-search-websearch).
* Začínáme s [ ukázkamiNode.js](https://github.com/Azure-Samples/cognitive-services-node-sdk-samples)
  * Viz také [Node.js vyhledávání na webu](https://github.com/Azure/azure-sdk-for-node/tree/master/lib/services/cognitiveServicesWebSearch).
* Začínáme s [ukázkami .NET](https://github.com/Azure-Samples/cognitive-services-dotnet-sdk-samples/tree/master/BingSearchv7)
  * [Balíček NuGet](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.Search.WebSearch/1.2.0)
  * Definice a závislosti najdete v [knihovnách .NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/cognitiveservices/Search.BingWebSearch).
* Začínáme s [ukázkami Javy](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples)
  * Definice a závislosti najdete v [knihovnách Javy](https://github.com/Azure-Samples/cognitive-services-java-sdk-samples/tree/master/Search/BingWebSearch).
