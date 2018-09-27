---
title: Vytvoření vstupní úlohy Azure Media Services z adresy URL HTTPS | Dokumentace Microsoftu
description: Toto téma ukazuje, jak vytvořit vstup úlohy z adresy URL http (s).
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 09/24/2018
ms.author: juliako
ms.openlocfilehash: 8b8bfb578b9a7190e93da721531041bb93a9a03c
ms.sourcegitcommit: ad08b2db50d63c8f550575d2e7bb9a0852efb12f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/26/2018
ms.locfileid: "47224728"
---
# <a name="create-a-job-input-from-an-https-url"></a>Vytvoření vstup úlohy z adresy URL HTTPS

V Media Services v3 při odesílání úloh zpracování videa, budete muset zjistit, kde najít vstupního videa Media Services. Jednu z možností je zadat adresu URL http (s) jako úloha vstup (jak je znázorněno v tomto příkladu). Všimněte si, že v současné době AMS v3 nepodporuje blokového kódování prostřednictvím adresy URL HTTPS přenosu. Úplný příklad najdete v tomto [githubu ukázky](https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts/blob/master/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs).

## <a name="net-sample"></a>Ukázky .NET

Následující kód ukazuje, jak vytvořit úlohu s adresu URL HTTPS vstup.

[!code-csharp[Main](../../../media-services-v3-dotnet-quickstarts/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs#SubmitJob)]

## <a name="next-steps"></a>Další postup

[Vytvoření vstupní úlohy z místního souboru](job-input-from-local-file-how-to.md).
