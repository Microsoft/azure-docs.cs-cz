---
title: Vytvoření vstupu úlohy z místního souboru
description: Tento článek ukazuje, jak vytvořit vstup Azure Media Services úlohy z místního souboru.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: how-to
ms.date: 08/31/2020
ms.author: inhenkel
ms.openlocfilehash: 466c609b21d115340a919ab32bd9dcf9c8d3a7ca
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "101092283"
---
# <a name="create-a-job-input-from-a-local-file"></a>Vytvoření vstupu úlohy z místního souboru

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Pokud ve službě Media Services v3 odešlete úlohy pro zpracování videí, musíte službu Media Services informovat, kde najde vstupní video. Vstupní video se může ukládat jako Asset služby Media Service. v takovém případě vytvoříte vstupní Asset založený na souboru (uložený místně nebo v úložišti objektů BLOB v Azure). V tomto tématu se dozvíte, jak vytvořit vstup úlohy z místního souboru. Úplný příklad najdete v této [ukázce GitHubu](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs).

## <a name="prerequisites"></a>Předpoklady

* [Vytvořte účet Media Services](./create-account-howto.md).

## <a name="net-sample"></a>Ukázka .NET

Následující kód ukazuje, jak vytvořit vstupní Asset a použít ho jako vstup pro úlohu. Funkce CreateInputAsset provádí následující akce:

* Vytvoří Asset.
* Získá [adresu SAS](../../storage/common/storage-sas-overview.md)[kontejneru v úložišti](../../storage/blobs/storage-quickstart-blobs-dotnet.md#upload-blobs-to-a-container) prostředku s možností zápisu.
* Přes adresu SAS odešle soubor do kontejneru v úložišti.

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateInputAsset)]

Následující fragment kódu vytvoří výstupní Asset, pokud ještě neexistuje:

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#CreateOutputAsset)]

Následující fragment kódu odesílá úlohu kódování:

[!code-csharp[Main](../../../media-services-v3-dotnet-tutorials/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#SubmitJob)]

## <a name="job-error-codes"></a>Kódy chyb úlohy

Viz [kódy chyb](/rest/api/media/jobs/get#joberrorcode).

## <a name="next-steps"></a>Další kroky

[Vytvoří vstup úlohy z adresy URL protokolu HTTPS](job-input-from-http-how-to.md).
