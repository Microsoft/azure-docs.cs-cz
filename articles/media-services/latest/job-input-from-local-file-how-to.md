---
title: Vytvoření úlohy Azure Media Services vstup z místního souboru | Dokumentace Microsoftu
description: Toto téma ukazuje, jak vytvořit vstup úlohy z místního souboru.
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
ms.openlocfilehash: 399f6724b8948c8e507bc50622a4fb65b2262491
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/08/2019
ms.locfileid: "67653941"
---
# <a name="create-a-job-input-from-a-local-file"></a>Vytvoření vstupní úlohy z místního souboru

Pokud ve službě Media Services v3 odešlete úlohy pro zpracování videí, musíte službu Media Services informovat, kde najde vstupní video. Vstupního videa mohou být uloženy jako majetek Media Service, v takovém případě třeba pouze vytvořit vstupní prostředek založený na souboru (uložená místně nebo ve službě Azure Blob storage). Toto téma ukazuje, jak vytvořit vstup úlohy z místního souboru. Úplný příklad najdete v tomto [Githubu ukázky](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs).

## <a name="net-sample"></a>Ukázky .NET

Následující kód ukazuje, jak vytvořit vstupní asset a použít jako vstup pro úlohu. Funkce CreateInputAsset provede následující akce:

* Vytvoří prostředek
* Získá [adresu SAS](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1) [kontejneru v úložišti](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-dotnet#upload-blobs-to-a-container) prostředku s možností zápisu.
* Přes adresu SAS odešle soubor do kontejneru v úložišti.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateInputAsset)]

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#SubmitJob)]

## <a name="job-error-codes"></a>Kódy chyb úlohy

Zobrazit [kódy chyb](https://docs.microsoft.com/rest/api/media/jobs/get#joberrorcode).

## <a name="next-steps"></a>Další postup

[Vytvoření vstup úlohy z adresy URL HTTPS](job-input-from-http-how-to.md).
