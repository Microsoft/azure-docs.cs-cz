---
title: Vytvoření vstupu Azure Media Services úlohy z adresy URL protokolu HTTPS | Microsoft Docs
description: Toto téma ukazuje, jak vytvořit vstup Azure Media Services úlohy z adresy URL HTTPS.
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
ms.openlocfilehash: 1ff2bc6d4bb4f7b983787b287fbb27e1c33cc179
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/23/2020
ms.locfileid: "87022986"
---
# <a name="create-a-job-input-from-an-https-url"></a>Vytvoření vstupu úlohy z adresy URL HTTPS

Pokud ve službě Media Services v3 odešlete úlohy pro zpracování videí, musíte službu Media Services informovat, kde najde vstupní video. Jednou z možností je určit adresu URL HTTPS jako vstup úlohy (jak je znázorněno v tomto příkladu). Upozorňujeme, že AMS v3 v současné době nepodporuje blokového kódování přenosu prostřednictvím adresy URL HTTPS. Úplný příklad najdete v této [ukázce GitHubu](https://github.com/Azure-Samples/media-services-v3-dotnet-quickstarts/blob/master/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs).

> [!TIP]
> Než začnete s vývojem, přečtěte si téma [vývoj s Media Services V3 API](media-services-apis-overview.md) (obsahuje informace o přístupu k rozhraním API, konvencím pojmenování atd.

## <a name="net-sample"></a>Ukázka .NET

Následující kód ukazuje, jak vytvořit úlohu s zadáním adresy URL protokolu HTTPS.

[!code-csharp[Main](../../../media-services-v3-dotnet-quickstarts/AMSV3Quickstarts/EncodeAndStreamFiles/Program.cs#SubmitJob)]

## <a name="job-error-codes"></a>Kódy chyb úlohy

Viz [kódy chyb](/rest/api/media/jobs/get#joberrorcode).

## <a name="next-steps"></a>Další kroky

[Vytvoří vstup úlohy z místního souboru](job-input-from-local-file-how-to.md).
