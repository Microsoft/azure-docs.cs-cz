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
ms.date: 10/15/2018
ms.author: juliako
ms.openlocfilehash: 3b4c11c359c15f1275a16774b490c08b543572c3
ms.sourcegitcommit: 3a7c1688d1f64ff7f1e68ec4bb799ba8a29a04a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/17/2018
ms.locfileid: "49378701"
---
# <a name="create-a-job-input-from-a-local-file"></a>Vytvoření vstupní úlohy z místního souboru

V Media Services v3 při odesílání úloh zpracování videa, budete muset zjistit, kde najít vstupního videa Media Services. Vstupního videa mohou být uloženy jako majetek Media Service, v takovém případě třeba pouze vytvořit vstupní prostředek založený na souboru (uložená místně nebo ve službě Azure Blob storage). Toto téma ukazuje, jak vytvořit vstup úlohy z místního souboru. Úplný příklad najdete v tomto [githubu ukázky](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs).

## <a name="net-sample"></a>Ukázky .NET

Následující kód ukazuje, jak vytvořit vstupní asset a použít jako vstup pro úlohu. Funkce CreateInputAsset provede následující akce:

* Vytvoří prostředek 
* Získá [adresu SAS](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1) [kontejneru v úložišti](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-dotnet?tabs=windows#upload-blobs-to-the-container) prostředku s možností zápisu.
* Přes adresu SAS odešle soubor do kontejneru v úložišti.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateInputAsset)]

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#SubmitJob)]

## <a name="next-steps"></a>Další postup

[Vytvoření vstup úlohy z adresy URL HTTPS](job-input-from-http-how-to.md).
