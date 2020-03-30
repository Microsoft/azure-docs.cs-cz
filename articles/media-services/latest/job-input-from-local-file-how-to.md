---
title: Vytvoření vstupu úlohy Mediálníslužby Azure z místního souboru | Dokumenty společnosti Microsoft
description: Tento článek ukazuje, jak vytvořit vstup úlohy Azure Media Services z místního souboru.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 03/26/2020
ms.author: juliako
ms.openlocfilehash: aba987ba232a29ffc240f72039b1e24bb87a2ed4
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "80345907"
---
# <a name="create-a-job-input-from-a-local-file"></a>Vytvoření vstupu úlohy z místního souboru

Pokud ve službě Media Services v3 odešlete úlohy pro zpracování videí, musíte službu Media Services informovat, kde najde vstupní video. Vstupní video lze uložit jako prostředek mediální služby, v takovém případě vytvoříte vstupní prostředek založený na souboru (uloženémístně nebo v úložišti objektů Blob Azure). Toto téma ukazuje, jak vytvořit vstup úlohy z místního souboru. Úplný příklad najdete v této [ukázce GitHubu](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs).

## <a name="prerequisites"></a>Požadavky 

* [Vytvořte účet mediálních služeb](create-account-cli-how-to.md).
* Zkontrolujte [správu majetku](manage-asset-concept.md).

## <a name="net-sample"></a>Ukázka rozhraní .NET

Následující kód ukazuje, jak vytvořit vstupní datový zdroj a použít jej jako vstup pro úlohu. Funkce CreateInputAsset provádí následující akce:

* Vytvoří datový zdroj
* Získá [adresu SAS](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1)[kontejneru v úložišti](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-dotnet#upload-blobs-to-a-container) prostředku s možností zápisu.
* Přes adresu SAS odešle soubor do kontejneru v úložišti.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateInputAsset)]

Následující fragment kódu vytvoří výstupní datový zdroj, pokud ještě neexistuje:

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateOutputAsset)]

Následující fragment kódu odešle úlohu kódování:

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#SubmitJob)]

## <a name="job-error-codes"></a>Kódy chyb úlohy

Viz [Chybové kódy](https://docs.microsoft.com/rest/api/media/jobs/get#joberrorcode).

## <a name="next-steps"></a>Další kroky

[Vytvořte vstup úlohy z adresy URL https](job-input-from-http-how-to.md).
