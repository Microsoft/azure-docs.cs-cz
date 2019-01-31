---
title: Nastavení vlastností a metadat pomocí Azure Import/Export | Dokumentace Microsoftu
description: Zjistěte, jak k určení vlastností a metadat nastavit cílové objektů BLOB při spuštění nástroje Import/Export Azure Příprava jednotky.
author: muralikk
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
ms.subservice: common
ms.openlocfilehash: 3e2796d943fbc4a4ce99576de74b2fc5c77bcca9
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2019
ms.locfileid: "55459447"
---
# <a name="setting-properties-and-metadata-during-the-import-process"></a>Nastavení vlastností a metadat během procesu importu

Při spuštění nástroje Microsoft Azure Import/Export Příprava jednotky můžete určit vlastnosti a metadata k nastavení cílové objektů BLOB. Postupujte následovně:

1.  Pokud chcete nastavit vlastnosti objektu blob, vytvořte textový soubor v místním počítači, který určuje názvy a hodnoty vlastností.
2.  Pokud chcete nastavit metadata objektu blob, vytvořte textový soubor v místním počítači, který určuje metadat názvy a hodnoty.
3.  Předat úplnou cestu na jeden nebo oba z těchto souborů do nástroje Import/Export Azure jako součást `PrepImport` operace.

> [!NOTE]
>  Při zadání vlastnosti nebo metadata souboru jako součást kopírování relace tyto vlastnosti nebo metadata jsou nastavené pro každý objekt blob, který je naimportováno v rámci této relace kopírování. Pokud chcete zadat jinou sadu vlastností nebo metadat pro některé objekty BLOB importování, potřeba vytvořit relaci samostatná kopie s různými vlastnostmi nebo soubory metadat.

## <a name="specify-blob-properties-in-a-text-file"></a>Zadejte vlastnosti objektu blob do textového souboru

K určení vlastností objektu blob, vytvořte místní textový soubor a patří kód XML, který určuje názvy vlastností, jako elementy a hodnoty vlastností jako hodnoty. Tady je příklad, který určuje hodnoty některých vlastností:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<Properties>
    <Content-Type>application/octet-stream</Content-Type>
    <Content-MD5>Q2hlY2sgSW50ZWdyaXR5IQ==</Content-MD5>
    <Cache-Control>no-cache</Cache-Control>
</Properties>
```

Uložte soubor do místního umístění, jako je `C:\WAImportExport\ImportProperties.txt`.

## <a name="specify-blob-metadata-in-a-text-file"></a>Zadat metadata objektu blob do textového souboru

Podobně zadat metadata objektu blob, vytvořte místní textový soubor, který určuje názvy metadat jako elementy a hodnoty metadat jako hodnoty. Tady je příklad, který určuje hodnoty některých metadat:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<Metadata>
    <UploadMethod>Windows Azure Import/Export service</UploadMethod>
    <DataSetName>SampleData</DataSetName>
    <CreationDate>10/1/2013</CreationDate>
</Metadata>
```

Uložte soubor do místního umístění, jako je `C:\WAImportExport\ImportMetadata.txt`.

## <a name="add-the-path-to-properties-and-metadata-files-in-datasetcsv"></a>Přidat cestu do vlastností a metadat souborů v dataset.csv

```
BasePath,DstBlobPathOrPrefix,BlobType,Disposition,MetadataFile,PropertiesFile
H:\Video\,https://mystorageaccount.blob.core.windows.net/video/,BlockBlob,rename,None,H:\mydirectory\properties.xml
H:\Photo\,https://mystorageaccount.blob.core.windows.net/photo/,BlockBlob,rename,None,H:\mydirectory\properties.xml
K:\Temp\FavoriteVideo.ISO,https://mystorageaccount.blob.core.windows.net/favorite/FavoriteVideo.ISO,BlockBlob,rename,None,H:\mydirectory\properties.xml
\\myshare\john\music\,https://mystorageaccount.blob.core.windows.net/music/,BlockBlob,rename,None,H:\mydirectory\properties.xml
```

## <a name="next-steps"></a>Další postup

* [Formát souborů metadat a vlastností služby Import/export](../storage-import-export-file-format-metadata-and-properties.md)
