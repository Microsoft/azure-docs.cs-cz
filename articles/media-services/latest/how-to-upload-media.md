---
title: Nahrát multimédia
titleSuffix: Azure Media Services
description: Naučte se nahrávat multimédia pro streamování nebo kódování.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: tutorial
ms.date: 08/11/2020
ms.author: inhenkel
ms.openlocfilehash: 3da313803aa546c2399e3c8c18858bee5cfa2ea1
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/20/2020
ms.locfileid: "88684367"
---
# <a name="upload-media-for-streaming-or-encoding"></a>Nahrání média pro streamování nebo kódování
V Media Services nahrajete digitální soubory (Media) do kontejneru objektů BLOB přidruženého k assetu. Entita [assetu](/rest/api/media/operations/asset) může obsahovat video, zvuk, obrázky, kolekce miniatur, textové stopy a soubory titulků (a metadata o těchto souborech). Po nahrání souborů do kontejneru assetu se váš obsah bezpečně uloží do cloudu pro další zpracování a streamování.

Než začnete, budete muset shromáždit několik hodnot nebo si je představit.

1. Místní cesta k souboru, který chcete nahrát.
1. ID assetu (kontejneru)
1. Název, který chcete použít pro nahraný soubor, včetně rozšíření
1. Název účtu úložiště, který používáte
1. Přístupový klíč pro účet úložiště

## <a name="portal"></a>[Azure Portal](#tab/portal/)

[!INCLUDE [Upload files with the portal](./includes/task-upload-file-to-asset-portal.md)]

## <a name="cli"></a>[Rozhraní příkazového řádku](#tab/cli/)

[!INCLUDE [Upload files with the portal](./includes/task-upload-file-to-asset-cli.md)]

## <a name="cli-shell"></a>[Prostředí CLI](#tab/clishell/)

[!INCLUDE [Upload files with the portal](./includes/task-upload-file-to-asset-cli-shell.md)]

## <a name="rest"></a>[REST](#tab/rest/)

Po [Vytvoření assetu pomocí metody post nebo jiné metody Rest a obdržení adresy URL SAS pro daný Asset](how-to-create-asset.md?tabs=rest)použijte Azure Storage rozhraní API nebo sady SDK (například [úložiště REST API](../../storage/common/storage-rest-api-auth.md) nebo [sada .NET SDK](../../storage/blobs/storage-quickstart-blobs-dotnet.md).

---
<!-- add these to the tabs when available -->
Další metody naleznete v [dokumentaci Azure Storage](https://docs.microsoft.com/azure/storage/blobs/) pro práci s objekty BLOB v [jazycích .NET](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-dotnet), [Java](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-java), [Python](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-python)a [JavaScript (Node.js)](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-nodejs).

## <a name="next-steps"></a>Další kroky

> [Přehled Media Services](media-services-overview.md)
