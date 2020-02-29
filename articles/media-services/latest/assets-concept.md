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
ms.date: 08/29/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 68bb1fea88ab7ba30e0ba07839f2d962840b7818
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/28/2020
ms.locfileid: "77921177"
---
# <a name="assets-in-azure-media-services"></a>Prostředky v Azure Media Services

V Azure Media Services [Asset](https://docs.microsoft.com/rest/api/media/assets) je základní koncept. Je místo, kde zadáváte média (například prostřednictvím upload nebo Live ingestování), výstupní média (z výstupu úlohy) a publikování médií z (pro streamování). 

Asset se namapuje na kontejner objektů BLOB v [účtu Azure Storage](storage-account-concept.md) a soubory v prostředku se ukládají jako objekty blob bloku v tomto kontejneru. Prostředky obsahují informace o digitálních souborech uložených v Azure Storage (včetně videa, zvuku, obrázků, kolekcí miniatur, textových stop a skrytých souborů titulků).

Media Services podporuje úrovně objektů blob, pokud účet používá úložiště pro obecné účely v2 (GPv2). Pomocí GPv2 můžete přesunout soubory do [studeného nebo archivního úložiště](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers). **Archivní** úložiště je vhodné pro archivaci zdrojových souborů, když už nepotřebujete (například po kódování).

**Archivní** úroveň úložiště je doporučena pouze pro velmi velké zdrojové soubory, které již byly zakódovány a výstup úlohy kódování byl vložen do výstupního kontejneru objektů BLOB. Objekty blob ve výstupním kontejneru, které chcete přidružit k Assetu a které se mají použít ke streamování nebo analýze obsahu, musí existovat v **horké** nebo **studené** úrovni úložiště.

## <a name="naming"></a>Pojmenování 

### <a name="assets"></a>Prostředky

Názvy assetů musí být jedinečné. Pro názvy prostředků Media Services V3 (například Assety, úlohy, transformace) se vztahují omezení pro pojmenovávání Azure Resource Manager. Další informace najdete v tématu [konvence pojmenování](media-services-apis-overview.md#naming-conventions).

### <a name="blobs"></a>Objekty blob

Názvy souborů nebo objektů BLOB v rámci assetu musí splňovat požadavky na [název objektu BLOB](https://docs.microsoft.com/rest/api/storageservices/Naming-and-Referencing-Containers--Blobs--and-Metadata) a [požadavky na název systému souborů NTFS](https://docs.microsoft.com/windows/win32/fileio/naming-a-file). Důvodem těchto požadavků jsou soubory, které lze kopírovat z úložiště objektů blob na místní disk NTFS ke zpracování.

## <a name="map-v3-asset-properties-to-v2"></a>Mapování vlastností assetu V3 na v2

Následující tabulka ukazuje, jak vlastnosti [assetu](https://docs.microsoft.com/rest/api/media/assets/createorupdate#asset)v v3 jsou mapovány na vlastnosti assetu ve verzi v2.

|vlastnosti V3|vlastnosti v2|
|---|---|
|`id`-(jedinečné) úplná Azure Resource Manager cesta najdete v tématu Příklady v [assetu](https://docs.microsoft.com/rest/api/media/assets/createorupdate) .||
|`name` – (jedinečné) viz zásady [vytváření názvů](media-services-apis-overview.md#naming-conventions) ||
|`alternateId`|`AlternateId`|
|`assetId`|`Id` – (jedinečná) hodnota začíná předponou `nb:cid:UUID:`.|
|`created`|`Created`|
|`description`|`Name`|
|`lastModified`|`LastModified`|
|`storageAccountName`|`StorageAccountName`|
|`storageEncryptionFormat`| `Options` (možnosti vytváření)|
|`type`||

## <a name="storage-side-encryption"></a>Šifrování na straně úložiště

K ochraně vašich prostředků v klidovém stavu, prostředky by se měla šifrovat pomocí šifrování na straně úložiště. Následující tabulka ukazuje, jak funguje šifrování na straně úložiště ve službě Media Services:

|Možnost šifrování|Popis|Media Services v2|Media Services v3|
|---|---|---|---|
|Šifrování úložiště služby Media Services|Šifrování AES-256, klíč spravovaný pomocí Media Services.|Podporováno<sup>(1)</sup>|Nepodporováno<sup>(2)</sup>|
|[Šifrování služby Storage pro neaktivní neaktivní data](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)|Šifrování na straně serveru, které nabízí Azure Storage, klíč spravuje Azure nebo zákazník.|Podporuje se|Podporuje se|
|[Šifrování na straně klienta úložiště](https://docs.microsoft.com/azure/storage/common/storage-client-side-encryption)|Šifrování na straně klienta, které nabízí služba Azure Storage, klíč spravovaný zákazníkem v Key Vault.|Nepodporuje se|Nepodporuje se|

<sup>1</sup> když Media Services podporuje zpracování obsahu v nešifrované/bez jakékoli formy šifrování, nedoporučuje se to.

<sup>2</sup> v Media Services V3 se šifrování úložiště (šifrování AES-256) podporuje jenom pro zpětnou kompatibilitu, když se vaše prostředky vytvořily pomocí Media Services V2. To znamená, že V3 funguje se stávajícími šifrovanými prostředky úložiště, ale neumožňuje vytváření nových.

## <a name="next-steps"></a>Další kroky

[Správa prostředků v Media Services](manage-asset-concept.md)

## <a name="also-see"></a>Viz také

[Rozdíly mezi Media Services V2 a V3](migrate-from-v2-to-v3.md)
