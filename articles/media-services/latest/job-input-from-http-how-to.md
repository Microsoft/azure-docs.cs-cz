---
title: Vytvořit úlohu služby Azure média vstup z adresy URL http (s) | Microsoft Docs
description: Toto téma ukazuje, jak vytvořit úlohu vstup z adresy URL http (s).
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 03/19/2018
ms.author: juliako
ms.openlocfilehash: d429665de64dacc5818d1d26c2a9029531cd39b3
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/14/2018
---
# <a name="create-a-job-input-from-an-https-url"></a>Vytvoření úlohy vstup z adresy URL http (s)

Ve službě Media Services v3 při odesílání úloh zpracujte videa, budete muset říct Media Services, kde najít vstupní video. Jednu z možností je zadat adresu URL http (s) jako úlohu vstup (jak je uvedeno v tomto příkladu). Úplný příklad najdete [githubu ukázka](https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts/blob/master/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs).

## <a name="net-sample"></a>Ukázka rozhraní .net

Následující kód ukazuje, jak vytvořit úlohu s adresu URL HTTPS vstup.

[!code-csharp[Main](../../../media-services-v3-dotnet-quickstarts/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs#SubmitJob)]

## <a name="next-steps"></a>Další postup

[Vytvořit úlohu vstupní z místního souboru](job-input-from-local-file-how-to.md).
