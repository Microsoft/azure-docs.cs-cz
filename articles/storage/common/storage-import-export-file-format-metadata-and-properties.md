---
title: Azure Import/Export metadat a vlastností formát souboru | Dokumentace Microsoftu
description: Zjistěte, jak určit metadat a vlastností pro minimálně jeden objekt BLOB, které jsou součástí import nebo export úloh.
author: muralikk
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
ms.component: common
ms.openlocfilehash: 5a886244b43ad006a95e9be0350d9c69fd987ad9
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/06/2018
ms.locfileid: "39526228"
---
# <a name="azure-importexport-service-metadata-and-properties-file-format"></a>Azure Import/Export metadat a vlastností formát souborů služby
Jako součást úlohy importu nebo úlohy exportu můžete zadat vlastnosti pro minimálně jeden objekt BLOB a metadat. K nastavení metadat nebo vlastností pro objekty BLOB jsou vytvořené jako součást úlohy importu, zadat soubor metadat nebo vlastností na pevný disk obsahující data, která mají být importována. Pro úlohu exportu metadat a vlastností se zapisují do souboru metadat nebo vlastností, který je zahrnut na pevném disku vrátil vám.  
  
## <a name="metadata-file-format"></a>Formát souboru metadat  
Formát souboru metadat vypadá takto:  
  
```xml
<?xml version="1.0" encoding="UTF-8"?>  
<Metadata>  
[<metadata-name-1>metadata-value-1</metadata-name-1>]  
[<metadata-name-2>metadata-value-2</metadata-name-2>]  
. . .  
</Metadata>  
```
  
|– Element XML|Typ|Popis|  
|-----------------|----------|-----------------|  
|`Metadata`|Kořenový element|Kořenový prvek souboru metadat.|  
|`metadata-name`|Řetězec|Volitelné. XML element určuje název metadata pro objekt blob a jeho hodnota určuje hodnotu nastavení metadat.|  
  
## <a name="properties-file-format"></a>Formát vlastnosti souboru  
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
  
|– Element XML|Typ|Popis|  
|-----------------|----------|-----------------|  
|`Properties`|Kořenový element|Kořenový element vlastnosti souboru.|  
|`Last-Modified`|Řetězec|Volitelné. Čas poslední změny pro objekt blob. Pro export pouze úlohy.|  
|`Etag`|Řetězec|Volitelné. Hodnota ETag objektu blob. Pro export pouze úlohy.|  
|`Content-Length`|Řetězec|Volitelné. Velikost objektu blob v bajtech. Pro export pouze úlohy.|  
|`Content-Type`|Řetězec|Volitelné. Typ obsahu objektu blob.|  
|`Content-MD5`|Řetězec|Volitelné. Hodnota hash MD5 objektu blob.|  
|`Content-Encoding`|Řetězec|Volitelné. Obsah objektu blob kódování.|  
|`Content-Language`|Řetězec|Volitelné. Jazyk obsahu objektu blob.|  
|`Cache-Control`|Řetězec|Volitelné. Řetězec řízení mezipaměti pro objekt blob.|  

## <a name="next-steps"></a>Další postup

Naleznete v tématu [nastavit vlastnosti objektu blob](/rest/api/storageservices/set-blob-properties), [Metadata objektu Blob nastavit](/rest/api/storageservices/set-blob-metadata), a [nastavení a načtení vlastností a metadat pro objekt blob prostředky](/rest/api/storageservices/setting-and-retrieving-properties-and-metadata-for-blob-resources) pro pravidla o metadata objektu blob nastavení a vlastnosti.
