---
title: Prostředky
titleSuffix: Azure Media Services
description: Přečtěte si, co jsou prostředky a jak je používají Azure Media Services.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79087906"
---
# <a name="assets-in-azure-media-services-v3"></a>Datové zdroje ve službě Azure Media Services v3

Ve službě Azure Media Services je základní koncept [assetu.](https://docs.microsoft.com/rest/api/media/assets) Je to místo, kde zadáváte média (například prostřednictvím nahrávání nebo živého ingestování), výstupní médium (z výstupu úlohy) a publikujete média z (pro streamování). 

Prostředek je mapován na kontejner objektů blob v [účtu Azure Storage](storage-account-concept.md) a soubory v Asset jsou uloženy jako objekty BLOB bloku v tomto kontejneru. Datové zdroje obsahují informace o digitálních souborech uložených ve službě Azure Storage (včetně videa, zvuku, obrázků, kolekcí miniatur, textových stop a souborů skrytých titulků).

Media Services podporuje vrstvy objektů Blob, když účet používá úložiště GPv2 (General-purpose v2). S GPv2 můžete přesunout soubory do [úložiště Cool nebo Archive](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers). **Úložiště archivu** je vhodné pro archivaci zdrojových souborů, když už nejsou potřeba (například po jejich zakódování).

Úroveň úložiště **Archivu** se doporučuje pouze pro velmi velké zdrojové soubory, které již byly zakódovány a výstup úlohy kódování byl vložen do výstupního kontejneru objektů blob. Objekty BLOB ve výstupním kontejneru, které chcete přidružit k prostředku a použít k streamování nebo analýze obsahu, musí existovat ve vrstvě úložiště **hot** nebo **cool.**

## <a name="naming"></a>Pojmenování 

### <a name="assets"></a>Prostředky

Názvy majetku musí být jedinečné. Názvy prostředků Media Services v3 (například Prostředky, Úlohy, Transformace) podléhají omezením názvů Azure Resource Manager. Další informace naleznete v [tématu Zásady pojmenování](media-services-apis-overview.md#naming-conventions).

### <a name="blobs"></a>Objekty blob

Názvy souborů/objektů BLOB v rámci datového zdroje musí dodržovat [požadavky na název objektu blob](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata) i [požadavky na název systému souborů](https://docs.microsoft.com/windows/win32/fileio/naming-a-file). Důvodem pro tyto požadavky je, že soubory mohou být zkopírovány z úložiště objektů blob na místní disk NTFS pro zpracování.

## <a name="next-steps"></a>Další kroky

[Správa datových zdrojů v mediálních službách](manage-asset-concept.md)

## <a name="see-also"></a>Viz také

[Rozdíly mezi mediálními službami v2 a v3](migrate-from-v2-to-v3.md)
