---
title: Vytvoření vstupu Azure Media Services úlohy z místního souboru | Microsoft Docs
description: Tento článek ukazuje, jak vytvořit vstup Azure Media Services úlohy z místního souboru.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 02/18/2019
ms.author: juliako
ms.openlocfilehash: a91305f1c5bfb50e0354dc0054d1a149182d921f
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/06/2019
ms.locfileid: "74888440"
---
# <a name="create-a-job-input-from-a-local-file"></a>Vytvoření vstupu úlohy z místního souboru

Pokud ve službě Media Services v3 odešlete úlohy pro zpracování videí, musíte službu Media Services informovat, kde najde vstupní video. Vstupní video se může ukládat jako Asset služby Media Service. v takovém případě vytvoříte vstupní Asset založený na souboru (uložený místně nebo v úložišti objektů BLOB v Azure). V tomto tématu se dozvíte, jak vytvořit vstup úlohy z místního souboru. Úplný příklad najdete v této [ukázce GitHubu](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs).

## <a name="net-sample"></a>Ukázka .NET

Následující kód ukazuje, jak vytvořit vstupní Asset a použít ho jako vstup pro úlohu. Funkce CreateInputAsset provádí následující akce:

* Vytvoří Asset.
* Získá [adresu SAS](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1) [kontejneru v úložišti](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-dotnet#upload-blobs-to-a-container) prostředku s možností zápisu.
* Přes adresu SAS odešle soubor do kontejneru v úložišti.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateInputAsset)]

Následující fragment kódu vytvoří výstupní Asset, pokud ještě neexistuje:

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateOutputAssetAsync)]

Následující fragment kódu odesílá úlohu kódování:

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#SubmitJob)]

## <a name="job-error-codes"></a>Kódy chyb úlohy

Viz [kódy chyb](https://docs.microsoft.com/rest/api/media/jobs/get#joberrorcode).

## <a name="next-steps"></a>Další kroky

[Vytvoří vstup úlohy z adresy URL protokolu HTTPS](job-input-from-http-how-to.md).
