---
title: Ukázkový pracovní postup pro přípravu pevných disků pro úlohu importu Azure Import/Export | Dokumentace Microsoftu
description: Zobrazit návod pro kompletní proces přípravy disků pro úlohu importu ve službě Azure Import/Export.
author: muralikk
services: storage
ms.service: storage
ms.topic: article
ms.date: 04/07/2017
ms.author: muralikk
ms.subservice: common
ms.openlocfilehash: de2cfbf2b4119daf965c95d8973b8528799c9502
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2019
ms.locfileid: "55454621"
---
# <a name="sample-workflow-to-prepare-hard-drives-for-an-import-job"></a>Ukázkový pracovní postup pro přípravu pevných disků pro úlohu importu

Tento článek vás provede kompletní proces přípravy disků pro úlohu importu.

## <a name="sample-data"></a>Ukázková data

Tento příklad importuje následující data do účtu služby Azure storage s názvem `mystorageaccount`:

|Umístění|Popis|Velikost dat|
|--------------|-----------------|-----|
|H:\Video\ |Kolekce videa|12 TB|
|H:\Photo\ |Kolekce fotografií|30 GB|
|K:\Temp\FavoriteMovie.ISO|Image disku A Blu-ray™|25 GB|
|\\\bigshare\john\music\ |Kolekce hudebních souborů do sdílené síťové složky|10 GB|

## <a name="storage-account-destinations"></a>Cíle účtu úložiště

Úlohy importu se import dat do následujících cílů v rámci účtu úložiště:

|Zdroj|Cílový virtuální adresář nebo objekt blob|
|------------|-------------------------------------------|
|H:\Video\ |Video /|
|H:\Photo\ |fotografie /|
|K:\Temp\FavoriteMovie.ISO|favorite/FavoriteMovies.ISO|
|\\\bigshare\john\music\ |Hudba|

Pomocí této mapování, soubor `H:\Video\Drama\GreatMovie.mov` se naimportují do objektu blob `https://mystorageaccount.blob.core.windows.net/video/Drama/GreatMovie.mov`.

## <a name="determine-hard-drive-requirements"></a>Určení požadavků na pevném disku

V dalším kroku výpočetní můžete určit, kolik pevné disky jsou potřeba, velikost dat:

`12TB + 30GB + 25GB + 10GB = 12TB + 65GB`

V tomto příkladu by měla stačit dva 8TB pevné disky. Nicméně, protože zdrojový adresář `H:\Video` má 12TB dat a kapacita jednu pevném disku je pouze 8TB, budou moct tuto verzi uveďte stejně, jako v následujícím **driveset.csv** souboru:

```
DriveLetter,FormatOption,SilentOrPromptOnFormat,Encryption,ExistingBitLockerKey
X,Format,SilentMode,Encrypt,
Y,Format,SilentMode,Encrypt,
```
Nástroj bude distribuovat data mezi dva pevné disky optimalizovaná způsobem.

## <a name="attach-drives-and-configure-the-job"></a>Připojte jednotky a konfigurace úlohy
Se oba disky připojit k počítači a vytvořte svazky. Potom vytvářejte **dataset.csv** souboru:
```
BasePath,DstBlobPathOrPrefix,BlobType,Disposition,MetadataFile,PropertiesFile
H:\Video\,video/,BlockBlob,rename,None,H:\mydirectory\properties.xml
H:\Photo\,photo/,BlockBlob,rename,None,H:\mydirectory\properties.xml
K:\Temp\FavoriteVideo.ISO,favorite/FavoriteVideo.ISO,BlockBlob,rename,None,H:\mydirectory\properties.xml
\\myshare\john\music\,music/,BlockBlob,rename,None,H:\mydirectory\properties.xml
```

Kromě toho můžete nastavit následující metadata pro všechny soubory:

* **UploadMethod:** Služba Windows Azure Import/Export
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

Můžete také nastavit některé vlastnosti `FavoriteMovie.ISO` objektů blob:

* **Content-Type:** application/octet-stream
* **Content-MD5:** Q2hlY2sgSW50ZWdyaXR5IQ==
* **Cache-Control:** no-cache

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

Nyní jste připraveni ke spuštění nástroje Import/Export Azure Příprava dva pevné disky.

**Pro první relace:**

```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#1  /sk:************* /InitialDriveSet:driveset-1.csv /DataSet:dataset-1.csv /logdir:F:\logs
```

Pokud žádná další data musí být přidán, vytvořte jiný soubor datové sady (stejný formát jako Initialdataset).

**Pro druhý relace:**

```
WAImportExport.exe PrepImport /j:JournalTest.jrn /id:session#2  /DataSet:dataset-2.csv
```

Po dokončení kopírování relací, můžete odpojit dvě jednotky z počítače, kopírování a dodat odpovídající datovému centru Azure. Budete nahrát soubory deníku s dvěma `<FirstDriveSerialNumber>.xml` a `<SecondDriveSerialNumber>.xml`, když vytvoříte úlohu importu na webu Azure Portal.

## <a name="next-steps"></a>Další postup

* [Příprava pevných disků pro úlohu importu](../storage-import-export-tool-preparing-hard-drives-import.md)
* [Stručná referenční příručka pro často používané příkazy](../storage-import-export-tool-quick-reference.md)
