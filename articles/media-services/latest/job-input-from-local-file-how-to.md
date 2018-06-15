---
title: Vytvořit úlohu služby Azure média vstup z místního souboru | Microsoft Docs
description: Toto téma ukazuje, jak vytvořit úlohu vstupní z místního souboru.
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
ms.openlocfilehash: 94e7192e13397ad8ec973d92f4c538f430c9cd60
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/14/2018
ms.locfileid: "34160001"
---
# <a name="create-a-job-input-from-a-local-file"></a>Vytvořit úlohu vstupní z místního souboru

Ve službě Media Services v3 při odesílání úloh zpracujte videa, budete muset říct Media Services, kde najít vstupní video. Vstupní video mohou být uloženy jako majetek služby média, v takovém případě můžete vytvořit prostředek vstupní založené na soubor (uložené místně nebo v Azure Blob storage). Toto téma ukazuje, jak vytvořit úlohu vstupní z místního souboru. Úplný příklad najdete [githubu ukázka](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs).

## <a name="net-sample"></a>Ukázka rozhraní .NET

Následující kód ukazuje, jak vytvořit prostředek vstupní a použít jej jako vstup pro úlohu. Funkce CreateInputAsset provede následující akce:

* Vytvoří prostředku 
* Získá [adresu SAS](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1) [kontejneru v úložišti](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-dotnet?tabs=windows#upload-blobs-to-the-container) prostředku s možností zápisu.
* Přes adresu SAS odešle soubor do kontejneru v úložišti.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateInputAsset)]

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#SubmitJob)]

## <a name="next-steps"></a>Další postup

[Vytvoření úlohy vstup z adresy URL http (s)](job-input-from-http-how-to.md).
