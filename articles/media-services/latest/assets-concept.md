---
title: Prostředky
titleSuffix: Azure Media Services
description: Přečtěte si, co jsou assety a jak se používají v Azure Media Services.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: conceptual
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: seodec18
ms.openlocfilehash: 99101f31ec955c822c784180dd8b04bfb8e5f73c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89291598"
---
# <a name="assets-in-azure-media-services-v3"></a>Prostředky v Azure Media Services V3

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

V Azure Media Services [Asset](/rest/api/media/assets) je základní koncept. Je místo, kde zadáváte média (například prostřednictvím upload nebo Live ingestování), výstupní média (z výstupu úlohy) a publikování médií z (pro streamování). 

Asset se namapuje na kontejner objektů BLOB v [účtu Azure Storage](storage-account-concept.md) a soubory v prostředku se ukládají jako objekty blob bloku v tomto kontejneru. Prostředky obsahují informace o digitálních souborech uložených v Azure Storage (včetně videa, zvuku, obrázků, kolekcí miniatur, textových stop a skrytých souborů titulků).

Media Services podporuje úrovně objektů blob, pokud účet používá úložiště pro obecné účely v2 (GPv2). Pomocí GPv2 můžete přesunout soubory do [studeného nebo archivního úložiště](../../storage/blobs/storage-blob-storage-tiers.md). **Archivní** úložiště je vhodné pro archivaci zdrojových souborů, když už nepotřebujete (například po kódování).

**Archivní** úroveň úložiště je doporučena pouze pro velmi velké zdrojové soubory, které již byly zakódovány a výstup úlohy kódování byl vložen do výstupního kontejneru objektů BLOB. Objekty blob ve výstupním kontejneru, které chcete přidružit k Assetu a které se mají použít ke streamování nebo analýze obsahu, musí existovat v **horké** nebo **studené** úrovni úložiště.

## <a name="naming"></a>Pojmenování 

### <a name="assets"></a>Prostředky

Názvy assetů musí být jedinečné. Pro názvy prostředků Media Services V3 (například Assety, úlohy, transformace) se vztahují omezení pro pojmenovávání Azure Resource Manager. Další informace najdete v tématu [konvence pojmenování](media-services-apis-overview.md#naming-conventions).

### <a name="blobs"></a>Objekty blob

Názvy souborů nebo objektů BLOB v rámci assetu musí splňovat požadavky na [název objektu BLOB](/rest/api/storageservices/naming-and-referencing-containers--blobs--and-metadata) a [požadavky na název systému souborů NTFS](/windows/win32/fileio/naming-a-file). Důvodem těchto požadavků jsou soubory, které lze kopírovat z úložiště objektů blob na místní disk NTFS ke zpracování.

## <a name="next-steps"></a>Další kroky

[Přehled Media Services](media-services-overview.md)

## <a name="see-also"></a>Viz také

[Rozdíly mezi Media Services V2 a V3](migrate-from-v2-to-v3.md)
