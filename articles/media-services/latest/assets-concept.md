---
title: Prostředky
titleSuffix: Azure Media Services
description: Přečtěte si, co jsou assety a jak se používají v Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 03/09/2020
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 9b04941a5799955097fbd54ad9bdf50eccb87541
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "79087906"
---
# <a name="assets-in-azure-media-services-v3"></a>Prostředky v Azure Media Services V3

V Azure Media Services [Asset](https://docs.microsoft.com/rest/api/media/assets) je základní koncept. Je místo, kde zadáváte média (například prostřednictvím upload nebo Live ingestování), výstupní média (z výstupu úlohy) a publikování médií z (pro streamování). 

Asset se namapuje na kontejner objektů BLOB v [účtu Azure Storage](storage-account-concept.md) a soubory v prostředku se ukládají jako objekty blob bloku v tomto kontejneru. Prostředky obsahují informace o digitálních souborech uložených v Azure Storage (včetně videa, zvuku, obrázků, kolekcí miniatur, textových stop a skrytých souborů titulků).

Media Services podporuje úrovně objektů blob, pokud účet používá úložiště pro obecné účely v2 (GPv2). Pomocí GPv2 můžete přesunout soubory do [studeného nebo archivního úložiště](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers). **Archivní** úložiště je vhodné pro archivaci zdrojových souborů, když už nepotřebujete (například po kódování).

**Archivní** úroveň úložiště je doporučena pouze pro velmi velké zdrojové soubory, které již byly zakódovány a výstup úlohy kódování byl vložen do výstupního kontejneru objektů BLOB. Objekty blob ve výstupním kontejneru, které chcete přidružit k Assetu a které se mají použít ke streamování nebo analýze obsahu, musí existovat v **horké** nebo **studené** úrovni úložiště.

## <a name="naming"></a>Pojmenování 

### <a name="assets"></a>Prostředky

Názvy assetů musí být jedinečné. Pro názvy prostředků Media Services V3 (například Assety, úlohy, transformace) se vztahují omezení pro pojmenovávání Azure Resource Manager. Další informace najdete v tématu [konvence pojmenování](media-services-apis-overview.md#naming-conventions).

### <a name="blobs"></a>Objekty blob

Názvy souborů nebo objektů BLOB v rámci assetu musí splňovat požadavky na [název objektu BLOB](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata) a [požadavky na název systému souborů NTFS](https://docs.microsoft.com/windows/win32/fileio/naming-a-file). Důvodem těchto požadavků jsou soubory, které lze kopírovat z úložiště objektů blob na místní disk NTFS ke zpracování.

## <a name="next-steps"></a>Další kroky

[Správa prostředků v Media Services](manage-asset-concept.md)

## <a name="see-also"></a>Viz také

[Rozdíly mezi Media Services V2 a V3](migrate-from-v2-to-v3.md)
