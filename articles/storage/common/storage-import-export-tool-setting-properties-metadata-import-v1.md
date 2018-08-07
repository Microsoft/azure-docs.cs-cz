---
title: Nastavení vlastností a metadat pomocí Azure Import/Export - v1 | Dokumentace Microsoftu
description: Zjistěte, jak k určení vlastností a metadat nastavit cílové objektů BLOB při spuštění nástroje Import/Export Azure Příprava jednotky. To se vztahuje na v1 nástroje Import/Export.
author: muralikk
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
ms.component: common
ms.openlocfilehash: 9a27bae49b9a626c46440392fee03f14c403fe76
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/06/2018
ms.locfileid: "39520696"
---
# <a name="setting-properties-and-metadata-during-the-import-process"></a>Nastavení vlastností a metadat během procesu importu
Při spuštění nástroje Microsoft Azure Import/Export Příprava jednotky můžete určit vlastnosti a metadata k nastavení cílové objektů BLOB. Postupujte následovně:  
  
1.  Pokud chcete nastavit vlastnosti objektu blob, vytvořte textový soubor v místním počítači, který určuje názvy a hodnoty vlastností.  
  
2.  Pokud chcete nastavit metadata objektu blob, vytvořte textový soubor v místním počítači, který určuje metadat názvy a hodnoty.  
  
3.  Předat úplnou cestu na jeden nebo oba z těchto souborů do nástroje Import/Export Azure jako součást `PrepImport` operace.  
  
> [!NOTE]
>  Při zadání vlastnosti nebo metadata souboru jako součást kopírování relace tyto vlastnosti nebo metadata jsou nastavené pro každý objekt blob, který je naimportováno v rámci této relace kopírování. Pokud chcete zadat jinou sadu vlastností nebo metadat pro některé objekty BLOB importování, potřeba vytvořit relaci samostatná kopie s různými vlastnostmi nebo soubory metadat.  
  
## <a name="specify-blob-properties-in-a-text-file"></a>Zadejte vlastnosti objektu Blob do textového souboru  
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
  
## <a name="specify-blob-metadata-in-a-text-file"></a>Zadat Metadata objektu Blob do textového souboru  
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
  
## <a name="create-a-copy-session-including-the-properties-or-metadata-files"></a>Vytvoření kopie relace včetně vlastností a metadat souborů  
Při spuštění nástroje Azure Import/Export připravit úlohu importu zadejte vlastnosti souboru o použití příkazového řádku `PropertyFile` parametru. Zadejte soubor metadat na pomocí příkazového řádku `/MetadataFile` parametru. Tady je příklad, který určuje oba soubory:  
  
```
WAImportExport.exe PrepImport /j:SecondDrive.jrn /id:BlueRayIso /srcfile:K:\Temp\BlueRay.ISO /dstblob:favorite/BlueRay.ISO /MetadataFile:c:\WAImportExport\SampleMetadata.txt /PropertyFile:c:\WAImportExport\SampleProperties.txt  
```
  
## <a name="next-steps"></a>Další postup

* [Formát souborů metadat a vlastností služby Import/export](../storage-import-export-file-format-metadata-and-properties.md)
