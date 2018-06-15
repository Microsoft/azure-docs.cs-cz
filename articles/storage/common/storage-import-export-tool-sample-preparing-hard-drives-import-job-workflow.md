---
title: Ukázkový pracovní postup připravená data pevné disky pro úlohy importu Azure Import/Export | Microsoft Docs
description: Návod pro dokončení procesu přípravy jednotky pro úlohy importu v rámci služby Azure Import/Export najdete.
author: muralikk
manager: syadav
editor: tysonn
services: storage
documentationcenter: ''
ms.assetid: ''
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/07/2017
ms.author: muralikk
ms.openlocfilehash: 60139ff36b66432620591ceaf201e046ad30217f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2017
ms.locfileid: "23873790"
---
# <a name="sample-workflow-to-prepare-hard-drives-for-an-import-job"></a>Ukázkový pracovní postup pro přípravu pevných disků pro úlohu importu

Tento článek vás provede procesem dokončení úlohy importu Příprava jednotky.

## <a name="sample-data"></a>Ukázková data

Tento příklad importuje následující data do účtu úložiště Azure s názvem `mystorageaccount`:

|Umístění|Popis|Velikost dat|
|--------------|-----------------|-----|
|H:\Video\ |Kolekce videa|12 TB|
|H:\Photo\ |Kolekce fotografií|30 GB|
|K:\Temp\FavoriteMovie.ISO|Bitová kopie disku A Blu-ray™|25 GB|
|\\\bigshare\john\music\ |Kolekce hudebních souborů ve sdílené síťové složce|10 GB|

## <a name="storage-account-destinations"></a>Cíle účtu úložiště

Úloha importu bude import dat do následující cíle v účtu úložiště:

|Zdroj|Cílový virtuální adresář nebo objekt blob|
|------------|-------------------------------------------|
|H:\Video\ |video nebo|
|H:\Photo\ |fotografie nebo|
|K:\Temp\FavoriteMovie.ISO|favorite/FavoriteMovies.ISO|
|\\\bigshare\john\music\ |Hudba|

Pomocí této mapování, souboru `H:\Video\Drama\GreatMovie.mov` budou importovány do objektu blob `https://mystorageaccount.blob.core.windows.net/video/Drama/GreatMovie.mov`.

## <a name="determine-hard-drive-requirements"></a>Stanovení požadavků na pevném disku

Dále určit, kolik pevné disky jsou potřeba, Vypočítat velikost dat:

`12TB + 30GB + 25GB + 10GB = 12TB + 65GB`

V tomto příkladu by mělo být dostatečné dva 8TB pevné disky. Nicméně, protože zdrojový adresář `H:\Video` má 12TB dat a jeden pevný disk je kapacita je pouze 8TB, bude moci určit to v následujícím způsobem v **driveset.csv** souboru:

```
DriveLetter,FormatOption,SilentOrPromptOnFormat,Encryption,ExistingBitLockerKey
X,Format,SilentMode,Encrypt,
Y,Format,SilentMode,Encrypt,
```
Nástroj provede distribuci dat mezi dva pevné disky optimálního.

## <a name="attach-drives-and-configure-the-job"></a>Připojte jednotky a konfiguraci úlohy
Můžete se připojit k počítači, oba disky a vytvářet svazky. Pak vytvořte **dataset.csv** souboru:
```
BasePath,DstBlobPathOrPrefix,BlobType,Disposition,MetadataFile,PropertiesFile
H:\Video\,video/,BlockBlob,rename,None,H:\mydirectory\properties.xml
H:\Photo\,photo/,BlockBlob,rename,None,H:\mydirectory\properties.xml
K:\Temp\FavoriteVideo.ISO,favorite/FavoriteVideo.ISO,BlockBlob,rename,None,H:\mydirectory\properties.xml
\\myshare\john\music\,music/,BlockBlob,rename,None,H:\mydirectory\properties.xml
```

Kromě toho můžete nastavit následující metadata pro všechny soubory:

* **UploadMethod:** služby Windows Azure Import/Export
* **DataSetName:** SampleData
* **Datum vytvoření:** 10/1/2013

Pokud chcete nastavit metadata pro importované soubory, vytvořte textový soubor, `c:\WAImportExport\SampleMetadata.txt`, s následujícím obsahem:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<Metadata>
    <UploadMethod>Windows Azure Import/Export service</UploadMethod>
    <DataSetName>SampleData</DataSetName>
    <CreationDate>10/1/2013</CreationDate>
</Metadata>
```

Můžete vytvořit také některé vlastnosti `FavoriteMovie.ISO` objektů blob:

* **Content-Type:** application/octet-stream
* **Obsah MD5:** Q2hlY2sgSW50ZWdyaXR5IQ ==
* **Cache-Control:** no cache

Chcete-li nastavit tyto vlastnosti, vytvořte textový soubor, `c:\WAImportExport\SampleProperties.txt`:

```xml
<?xml version="1.0" encoding="UTF-8"?>
<Properties>
    <Content-Type>application/octet-stream</Content-Type>
    <Content-MD5>Q2hlY2sgSW50ZWdyaXR5IQ==</Content-MD5>
    <Cache-Control>no-cache</Cache-Control>
</Properties>
```

## <a name="run-the-azure-importexport-tool-waimportexportexe"></a>Spusťte nástroj Azure Import/Export (WAImportExport.exe)

Nyní jste připraveni ke spuštění nástroje Azure Import/Export Příprava dva pevné disky.

**Pro první relaci:**

```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#1  /sk:************* /InitialDriveSet:driveset-1.csv /DataSet:dataset-1.csv /logdir:F:\logs
```

Pokud žádná další data musí být přidán, vytvořte jiný soubor datovou sadu (stejný formát jako Initialdataset).

**Pro druhou relaci:**

```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#2  /DataSet:dataset-2.csv
```

Po dokončení kopírování relací mají můžete odpojit dvě jednotky z počítače, kopírování a dodávat je k příslušné datového centra Azure. Budete odesílat soubory, dvě deníku, `<FirstDriveSerialNumber>.xml` a `<SecondDriveSerialNumber>.xml`, při vytváření úlohy importu v portálu Azure.

## <a name="next-steps"></a>Další kroky

* [Příprava pevných disků pro úlohu importu](../storage-import-export-tool-preparing-hard-drives-import.md)
* [Stručná referenční příručka pro často používané příkazy](../storage-import-export-tool-quick-reference.md)
