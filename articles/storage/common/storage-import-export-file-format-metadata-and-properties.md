---
title: Azure Import/Export metadat a vlastnosti formát souboru | Microsoft Docs
description: Zjistěte, jak určit metadata a vlastnosti pro jeden nebo více objektů BLOB, které jsou součástí importu nebo exportu úlohy.
author: muralikk
manager: syadav
editor: tysonn
services: storage
documentationcenter: ''
ms.assetid: 840364c6-d9a8-4b43-a9f3-f7441c625069
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
ms.openlocfilehash: 3f728ad94cdcbd32092b677f11a737ae91376720
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2017
ms.locfileid: "23873650"
---
# <a name="azure-importexport-service-metadata-and-properties-file-format"></a>Azure Import/Export metadat a vlastnosti souboru formát služby
Metadata a vlastnosti pro jeden nebo více objektů BLOB můžete zadat jako součást úlohy importu nebo úlohy exportu. Pokud chcete nastavit vlastnosti pro objekty BLOB vytváří jako součást úlohy importu nebo metadata, poskytnete metadata nebo vlastnosti souboru na pevný disk obsahující data, která bude importována. Pro úlohy exportu metadat a vlastnosti se zapisují do metadata nebo vlastnosti souboru, který je zahrnut na pevný disk, který vrátil pro vás.  
  
## <a name="metadata-file-format"></a>Formát souboru metadat  
Formát souboru metadat je následující:  
  
```xml
<?xml version="1.0" encoding="UTF-8"?>  
<Metadata>  
[<metadata-name-1>metadata-value-1</metadata-name-1>]  
[<metadata-name-2>metadata-value-2</metadata-name-2>]  
. . .  
</Metadata>  
```
  
|XML Element|Typ|Popis|  
|-----------------|----------|-----------------|  
|`Metadata`|Kořenový element|Kořenový element soubor metadat.|  
|`metadata-name`|Řetězec|Volitelné. XML element určuje název metadata pro objekt blob a jeho hodnota určuje hodnota nastavení metadat.|  
  
## <a name="properties-file-format"></a>Formát souboru vlastnosti  
Formát souboru vlastnosti vypadá takto:  
  
```xml
<?xml version="1.0" encoding="UTF-8"?>  
<Properties>  
[<Last-Modified>date-time-value</Last-Modified>]  
[<Etag>etag</Etag>]  
[<Content-Length>size-in-bytes<Content-Length>]  
[<Content-Type>content-type</Content-Type>]  
[<Content-MD5>content-md5</Content-MD5>]  
[<Content-Encoding>content-encoding</Content-Encoding>]  
[<Content-Language>content-language</Content-Language>]  
[<Cache-Control>cache-control</Cache-Control>]  
</Properties>  
```
  
|XML Element|Typ|Popis|  
|-----------------|----------|-----------------|  
|`Properties`|Kořenový element|Kořenový element souboru vlastnosti.|  
|`Last-Modified`|Řetězec|Volitelné. Čas poslední úpravy pro tento objekt blob. Pro export pouze úlohy.|  
|`Etag`|Řetězec|Volitelné. Hodnota objektu blob značka ETag. Pro export pouze úlohy.|  
|`Content-Length`|Řetězec|Volitelné. Velikost objektu blob v bajtech. Pro export pouze úlohy.|  
|`Content-Type`|Řetězec|Volitelné. Typ obsahu objektu blob.|  
|`Content-MD5`|Řetězec|Volitelné. Hodnota hash MD5 objektu blob.|  
|`Content-Encoding`|Řetězec|Volitelné. Obsah objektu blob kódování.|  
|`Content-Language`|Řetězec|Volitelné. Jazyk obsahu objektu blob.|  
|`Cache-Control`|Řetězec|Volitelné. Řetězec ovládací prvek mezipaměti pro tento objekt blob.|  

## <a name="next-steps"></a>Další kroky

V tématu [umožňuje nastavit vlastnosti objektu blob](/rest/api/storageservices/set-blob-properties), [nastavit Metadata objektu Blob](/rest/api/storageservices/set-blob-metadata), a [vlastnosti a metadata pro nastavení a načítání objektů blob prostředky](/rest/api/storageservices/setting-and-retrieving-properties-and-metadata-for-blob-resources) pro podrobná pravidla o nastavení vlastnosti a metadata objektu blob.
