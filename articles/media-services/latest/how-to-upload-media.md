---
title: Nahrávání médií
titleSuffix: Azure Media Services
description: Naučte se nahrávat multimédia pro streamování nebo kódování.
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
ms.openlocfilehash: 3040369e655ab91f56f271313dc4d2613f02be06
ms.sourcegitcommit: 2c586a0fbec6968205f3dc2af20e89e01f1b74b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/14/2020
ms.locfileid: "92015840"
---
# <a name="upload-media-for-streaming-or-encoding"></a>Nahrání média pro streamování nebo kódování

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

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

## <a name="rest"></a>[REST](#tab/rest/)

Po [Vytvoření assetu pomocí metody post nebo jiné metody Rest a obdržení adresy URL SAS pro daný Asset](how-to-create-asset.md?tabs=rest)použijte Azure Storage rozhraní API nebo sady SDK (například [úložiště REST API](../../storage/common/storage-rest-api-auth.md) nebo [sada .NET SDK](../../storage/blobs/storage-quickstart-blobs-dotnet.md).

---
<!-- add these to the tabs when available -->
Další metody naleznete v [dokumentaci Azure Storage](../../storage/blobs/index.yml) pro práci s objekty BLOB v [jazycích .NET](../../storage/blobs/storage-quickstart-blobs-dotnet.md), [Java](../../storage/blobs/storage-quickstart-blobs-java.md), [Python](../../storage/blobs/storage-quickstart-blobs-python.md)a [JavaScript (Node.js)](../../storage/blobs/storage-quickstart-blobs-nodejs.md).

## <a name="next-steps"></a>Další kroky

> [Přehled Media Services](media-services-overview.md)