---
title: Vytvoření vstupu úlohy Mediálních služeb Azure z adresy URL HTTPS | Dokumenty společnosti Microsoft
description: Toto téma ukazuje, jak vytvořit vstup úlohy Azure Media Services z adresy URL HTTPS.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 02/13/2019
ms.author: juliako
ms.openlocfilehash: a3d4568dd237491f28ae2880bdd78dd236870c3f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74899826"
---
# <a name="create-a-job-input-from-an-https-url"></a>Vytvoření vstupu úlohy z adresy URL HTTPS

Pokud ve službě Media Services v3 odešlete úlohy pro zpracování videí, musíte službu Media Services informovat, kde najde vstupní video. Jednou z možností je zadat adresu URL HTTPS jako vstup úlohy (jak je znázorněno v tomto příkladu). Upozorňujeme, že AMS v3 v současné době nepodporuje blokového kódování přenosu prostřednictvím adresy URL HTTPS. Úplný příklad najdete v této [ukázce GitHubu](https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts/blob/master/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs).

> [!TIP]
> Než začnete vyvíjet, [přečtěte si vývoj s media services v3 API](media-services-apis-overview.md) (obsahuje informace o přístupu k api, konvence pojmenování, atd.)

## <a name="net-sample"></a>Ukázka rozhraní .NET

Následující kód ukazuje, jak vytvořit úlohu se vstupem adresy URL HTTPS.

[!code-csharp[Main](../../../media-services-v3-dotnet-quickstarts/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs#SubmitJob)]

## <a name="job-error-codes"></a>Kódy chyb úlohy

Viz [Chybové kódy](https://docs.microsoft.com/rest/api/media/jobs/get#joberrorcode).

## <a name="next-steps"></a>Další kroky

[Vytvořte vstup úlohy z místního souboru](job-input-from-local-file-how-to.md).
