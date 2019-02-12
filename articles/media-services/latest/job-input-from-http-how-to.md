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
ms.date: 02/10/2019
ms.author: juliako
ms.openlocfilehash: 6919bf49e46a65a02d2444277c8005fbb6f4ca1f
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/11/2019
ms.locfileid: "55991646"
---
# <a name="create-a-job-input-from-an-https-url"></a>Vytvoření vstup úlohy z adresy URL HTTPS

V Media Services v3 při odesílání úloh zpracování videa, budete muset zjistit, kde najít vstupního videa Media Services. Jednu z možností je zadat adresu URL http (s) jako úloha vstup (jak je znázorněno v tomto příkladu). Upozorňujeme, že AMS v3 v současné době nepodporuje blokového kódování přenosu prostřednictvím adresy URL HTTPS. Úplný příklad najdete v tomto [Githubu ukázky](https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts/blob/master/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs).

## <a name="net-sample"></a>Ukázky .NET

Následující kód ukazuje, jak vytvořit úlohu s adresu URL HTTPS vstup.

[!code-csharp[Main](../../../media-services-v3-dotnet-quickstarts/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs#SubmitJob)]

## <a name="next-steps"></a>Další postup

[Vytvoření vstupní úlohy z místního souboru](job-input-from-local-file-how-to.md).
