---
title: Nastavení vlastností a metadat pomocí Azure Import/Export - v1 | Microsoft Docs
description: Zjistěte, jak zadat vlastnosti a metadata nastavení na cílový objektů BLOB při spuštění nástroje Azure Import/Export Příprava jednotky. Vztahuje se k v1 nástroj importu a exportu.
author: muralikk
manager: syadav
editor: tysonn
services: storage
documentationcenter: ''
ms.assetid: e8541695-bcfb-4bf0-84d9-72c9de32a0a8
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
ms.openlocfilehash: 77bdaa5559de86cd1de9f30e70656e47fd5719e2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2017
ms.locfileid: "23873734"
---
# <a name="setting-properties-and-metadata-during-the-import-process"></a>Nastavení vlastností a metadat během procesu importu
Když spustíte nástroj Microsoft Azure Import/Export Příprava jednotky, můžete zadat vlastnosti a metadata nastavení na cílový objekty BLOB. Postupujte následovně:  
  
1.  Pokud chcete nastavit vlastnosti objektů blob, vytvořte textový soubor do místního počítače, který určuje názvy a hodnoty vlastností.  
  
2.  Pokud chcete nastavit metadata objektu blob, vytvořte textový soubor do místního počítače, který určuje metadata názvy a hodnoty.  
  
3.  Předat úplnou cestu do jedné nebo obou těchto souborů do nástroje Azure Import/Export jako součást `PrepImport` operaci.  
  
> [!NOTE]
>  Když zadáte vlastnosti nebo metadata souboru v rámci relace kopírování, jsou pro každý objekt blob, který je naimportováno v rámci této relace kopie nastavit tyto vlastnosti nebo metadata. Pokud chcete určit jinou sadu vlastnosti nebo metadata pro některé objekty BLOB importována, budete muset vytvořit relaci samostatná kopie různé vlastnosti nebo soubory metadat.  
  
## <a name="specify-blob-properties-in-a-text-file"></a>Zadejte vlastnosti objektů Blob v textovém souboru  
K určení vlastností objektu blob, vytvořte místní textový soubor a zahrnují kód XML, který určuje názvy vlastností jako elementy a hodnoty vlastností jako hodnoty. Tady je příklad, který určuje některé hodnoty vlastností:  
  
```xml
<?xml version="1.0" encoding="UTF-8"?>  
<Properties>  
    <Content-Type>application/octet-stream</Content-Type>  
    <Content-MD5>Q2hlY2sgSW50ZWdyaXR5IQ==</Content-MD5>  
    <Cache-Control>no-cache</Cache-Control>  
</Properties>  
```
  
Uložte soubor do místního umístění, jako je `C:\WAImportExport\ImportProperties.txt`.  
  
## <a name="specify-blob-metadata-in-a-text-file"></a>Zadejte Metadata objektu Blob do textového souboru  
Podobně zadejte metadata objektu blob, vytvořte místní textový soubor, který určuje názvy metadat jako elementy a metadata hodnoty jako hodnoty. Tady je příklad, který určuje některé hodnoty metadat:  
  
```xml
<?xml version="1.0" encoding="UTF-8"?>  
<Metadata>  
    <UploadMethod>Windows Azure Import/Export service</UploadMethod>  
    <DataSetName>SampleData</DataSetName>  
    <CreationDate>10/1/2013</CreationDate>  
</Metadata>  
```
  
Uložte soubor do místního umístění, jako je `C:\WAImportExport\ImportMetadata.txt`.  
  
## <a name="create-a-copy-session-including-the-properties-or-metadata-files"></a>Vytvořit relaci kopírování, včetně všech vlastností či soubory metadat  
Když spustíte nástroj Azure Import/Export připravit úlohu importu, zadejte vlastnosti souboru o použití příkazového řádku `PropertyFile` parametr. Zadejte soubor metadat na pomocí příkazového řádku `/MetadataFile` parametr. Tady je příklad, který určuje oba soubory:  
  
```
WAImportExport.exe PrepImport /j:SecondDrive.jrn /id:BlueRayIso /srcfile:K:\Temp\BlueRay.ISO /dstblob:favorite/BlueRay.ISO /MetadataFile:c:\WAImportExport\SampleMetadata.txt /PropertyFile:c:\WAImportExport\SampleProperties.txt  
```
  
## <a name="next-steps"></a>Další kroky

* [Formát souborů metadat a vlastností služby Import/export](../storage-import-export-file-format-metadata-and-properties.md)
