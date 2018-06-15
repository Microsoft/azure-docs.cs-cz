---
title: Ukázkový pracovní postup připravená data pevné disky pro úlohy importu Azure Import/Export - v1 | Microsoft Docs
description: Návod pro dokončení procesu přípravy jednotky pro úlohy importu v rámci služby Azure Import/Export najdete.
author: muralikk
manager: syadav
editor: tysonn
services: storage
documentationcenter: ''
ms.assetid: 6eb1b1b7-c69f-4365-b5ef-3cd5e05eb72a
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
ms.openlocfilehash: 66e85bd3e9e43ae360d0507f5bdf3596abbeb7d1
ms.sourcegitcommit: fa28ca091317eba4e55cef17766e72475bdd4c96
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/14/2017
ms.locfileid: "26692020"
---
# <a name="sample-workflow-to-prepare-hard-drives-for-an-import-job"></a>Ukázkový pracovní postup pro přípravu pevných disků pro úlohu importu
Toto téma vás provede kompletní proces přípravy jednotky pro úlohy importu.  
  
Tento příklad importuje následující data do účtu úložiště Azure okno s názvem `mystorageaccount`:  
  
|Umístění|Popis|  
|--------------|-----------------|  
|H:\Video|Kolekce videa, 5 TB celkem.|  
|H:\Photo|Kolekce fotografií, celkem 30 GB.|  
|K:\Temp\FavoriteMovie.ISO|Image disku A Blu-ray™, 25 GB.|  
|\\\bigshare\john\music|Kolekce hudebních souborů ve sdílené síťové složce, celkem 10 GB.|  
  
Úloha importu importuje tato data do následující cíle v účtu úložiště:  
  
|Zdroj|Cílový virtuální adresář nebo objekt blob|  
|------------|-------------------------------------------|  
|H:\Video|https://mystorageaccount.BLOB.Core.Windows.NET/video|  
|H:\Photo|https://mystorageaccount.BLOB.Core.Windows.NET/Photo|  
|K:\Temp\FavoriteMovie.ISO|https://mystorageaccount.BLOB.Core.Windows.NET/Favorite/FavoriteMovies.ISO|  
|\\\bigshare\john\music|https://mystorageaccount.BLOB.Core.Windows.NET/Music|  
  
Pomocí této mapování, souboru `H:\Video\Drama\GreatMovie.mov` je importovat do objektu blob `https://mystorageaccount.blob.core.windows.net/video/Drama/GreatMovie.mov`.  
  
Dále určit, kolik pevné disky jsou potřeba, Vypočítat velikost dat:  
  
`5TB + 30GB + 25GB + 10GB = 5TB + 65GB`  
  
V tomto příkladu by mělo být dostatečné dva 3 TB pevné disky. Nicméně, protože zdrojový adresář `H:\Video` má 5 TB dat a jeden pevný disk je kapacita je jenom 3 TB, je nutné rozdělit `H:\Video` do dvou menší adresářů: `H:\Video1` a `H:\Video2`, před spuštěním Microsoft Azure Nástroj pro import nebo Export. Tento krok vypočítá následující zdrojového adresáře:  
  
|Umístění|Velikost|Cílový virtuální adresář nebo objekt blob|  
|--------------|----------|-------------------------------------------|  
|H:\Video1|2,5 TB|https://mystorageaccount.BLOB.Core.Windows.NET/video|  
|H:\Video2|2,5 TB|https://mystorageaccount.BLOB.Core.Windows.NET/video|  
|H:\Photo|30 GB|https://mystorageaccount.BLOB.Core.Windows.NET/Photo|  
|K:\Temp\FavoriteMovies.ISO|25 GB|https://mystorageaccount.BLOB.Core.Windows.NET/Favorite/FavoriteMovies.ISO|  
|\\\bigshare\john\music|10 GB|https://mystorageaccount.BLOB.Core.Windows.NET/Music|  
  
 I když `H:\Video`directory rozdělení na dva adresáře ukazovaly na stejný cílový virtuální adresář v účtu úložiště. Tímto způsobem, všechny soubory videa se udržují v rámci jedné `video` kontejneru v účtu úložiště.  
  
 V dalším kroku předchozí zdrojového adresáře jsou rovnoměrně rozloženy dva pevné disky:  
  
||||  
|-|-|-|  
|Pevný disk|Zdrojové adresáře|Celková velikost|  
|První jednotky|H:\Video1|2,5 TB + 30 GB|  
||H:\Photo||  
|Sekundy jednotky|H:\Video2|2,5 TB + 35 GB|  
||K:\Temp\BlueRay.ISO||  
||\\\bigshare\john\music||  
  
Kromě toho můžete nastavit následující metadata pro všechny soubory:  
  
-   **UploadMethod:** služby Windows Azure Import/Export  
  
-   **DataSetName:** SampleData  
  
-   **Datum vytvoření:** 10/1/2013  
  
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
  
-   **Content-Type:** application/octet-stream  
  
-   **Obsah MD5:** Q2hlY2sgSW50ZWdyaXR5IQ ==  
  
-   **Cache-Control:** no cache  
  
Chcete-li nastavit tyto vlastnosti, vytvořte textový soubor, `c:\WAImportExport\SampleProperties.txt`:  
  
```xml
<?xml version="1.0" encoding="UTF-8"?>  
<Properties>  
    <Content-Type>application/octet-stream</Content-Type>  
    <Content-MD5>Q2hlY2sgSW50ZWdyaXR5IQ==</Content-MD5>  
    <Cache-Control>no-cache</Cache-Control>  
</Properties>  
```
  
Nyní jste připraveni ke spuštění nástroje Azure Import/Export Příprava dva pevné disky. Poznámky:  
  
-   První disk je připojit jako jednotka X.  
  
-   Druhý disk je připojit jako disk Y.  
  
-   Klíč pro účet úložiště `mystorageaccount` je `8ImTigJhIwvL9VEIQKB/zbqcXbxrIHbBjLIfOt0tyR98TxtFvUM/7T0KVNR6KRkJrh26u5I8hTxTLM2O1aDVqg==`.  

## <a name="preparing-disk-for-import-when-data-is-pre-loaded"></a>Příprava disku pro import, když je předem načtená data
 
 Pokud data, která mají být importována již existuje na disku, použijte příznak /skipwrite. Hodnota /t a /srcdir má obě, přejděte na disku připraveném pro import. Pokud všechna data, která bude importována není má stejné cílový virtuální adresář nebo kořenového účtu úložiště, spusťte stejný příkaz pro každý cílový adresář samostatně, udržování hodnota /id stejné napříč všechny spustí.

>[!NOTE] 
>Nezadávejte/Format, jak se bude vymazání dat na disku. Můžete zadat / šifrování nebo /bk v závislosti na tom, jestli je disk už je šifrovaný nebo ne. 
>

```
    When data is already present on the disk for each drive run the following command.
    WAImportExport.exe PrepImport /j:FirstDrive.jrn /id:Video1 /logdir:c:\logs /sk:8ImTigJhIwvL9VEIQKB/zbqcXbxrIHbBjLIfOt0tyR98TxtFvUM/7T0KVNR6KRkJrh26u5I8hTxTLM2O1aDVqg== /t:x /format /encrypt /srcdir:x:\Video1 /dstdir:video/ /MetadataFile:c:\WAImportExport\SampleMetadata.txt /skipwrite
```

## <a name="copy-sessions---first-drive"></a>Zkopírujte relací - nejprve jednotky

Pro první disk spusťte nástroj Azure Import/Export dvakrát pro kopírování adresářů dva zdroje:  

**Nejdříve zkopírovat relace**
  
```
WAImportExport.exe PrepImport /j:FirstDrive.jrn /id:Video1 /logdir:c:\logs /sk:8ImTigJhIwvL9VEIQKB/zbqcXbxrIHbBjLIfOt0tyR98TxtFvUM/7T0KVNR6KRkJrh26u5I8hTxTLM2O1aDVqg== /t:x /format /encrypt /srcdir:H:\Video1 /dstdir:video/ /MetadataFile:c:\WAImportExport\SampleMetadata.txt  
```

**Druhá kopie relace**

```  
WAImportExport.exe PrepImport /j:FirstDrive.jrn /id:Photo /srcdir:H:\Photo /dstdir:photo/ /MetadataFile:c:\WAImportExport\SampleMetadata.txt
```

## <a name="copy-sessions---second-drive"></a>Zkopírujte relací - druhé jednotky
 
Pro druhý disk, spusťte nástroj Azure Import/Export trojnásobek, jednou každý pro zdrojového adresáře a jednou pro soubor bitové kopie samostatné Blu-Ray™):  
  
**Nejdříve zkopírovat relace** 

```
WAImportExport.exe PrepImport /j:SecondDrive.jrn /id:Video2 /logdir:c:\logs /sk:8ImTigJhIwvL9VEIQKB/zbqcXbxrIHbBjLIfOt0tyR98TxtFvUM/7T0KVNR6KRkJrh26u5I8hTxTLM2O1aDVqg== /t:y /format /encrypt /srcdir:H:\Video2 /dstdir:video/ /MetadataFile:c:\WAImportExport\SampleMetadata.txt  
```
  
**Druhá kopie relace**

```
WAImportExport.exe PrepImport /j:SecondDrive.jrn /id:Music /srcdir:\\bigshare\john\music /dstdir:music/ /MetadataFile:c:\WAImportExport\SampleMetadata.txt  
```  
  
**Třetí kopii relace**  

```
WAImportExport.exe PrepImport /j:SecondDrive.jrn /id:BlueRayIso /srcfile:K:\Temp\BlueRay.ISO /dstblob:favorite/BlueRay.ISO /MetadataFile:c:\WAImportExport\SampleMetadata.txt /PropertyFile:c:\WAImportExport\SampleProperties.txt  
```

## <a name="copy-session-completion"></a>Zkopírujte dokončení relace

Po dokončení kopírování relací mají můžete odpojit dvě jednotky z počítače, kopírování a dodávat je k příslušné datovému centru systému Windows Azure. Odesílat soubory dvě deníku `FirstDrive.jrn` a `SecondDrive.jrn`, při vytváření úlohy importu v [portál Azure](https://portal.azure.com).  
  
## <a name="next-steps"></a>Další kroky

* [Příprava pevných disků pro úlohu importu](../storage-import-export-tool-preparing-hard-drives-import-v1.md)   
* [Stručná referenční příručka pro často používané příkazy](../storage-import-export-tool-quick-reference-v1.md) 
