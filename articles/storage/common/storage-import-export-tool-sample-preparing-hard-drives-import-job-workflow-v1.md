---
title: Ukázkový pracovní postup pro přípravu pevných disků pro úlohu importu Azure Import/Export - v1 | Dokumentace Microsoftu
description: Zobrazit návod pro kompletní proces přípravy disků pro úlohu importu ve službě Azure Import/Export.
author: muralikk
services: storage
ms.service: storage
ms.topic: article
ms.date: 01/23/2017
ms.author: muralikk
ms.subservice: common
ms.openlocfilehash: e183ed5ecda3053ed052952f4db5adfb016bfa68
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2019
ms.locfileid: "55459041"
---
# <a name="sample-workflow-to-prepare-hard-drives-for-an-import-job"></a>Ukázkový pracovní postup pro přípravu pevných disků pro úlohu importu
Toto téma vás provede kompletní proces přípravy disků pro úlohu importu.  
  
Tento příklad importuje následující data do účtu úložiště Azure okno s názvem `mystorageaccount`:  
  
|Umístění|Popis|  
|--------------|-----------------|  
|H:\Video|Kolekce videa, 5 TB celkové.|  
|H:\Photo|Kolekce fotografií, celkem 30 GB.|  
|K:\Temp\FavoriteMovie.ISO|Image disku A Blu-ray™, 25 GB.|  
|\\\bigshare\john\music|Kolekce hudebních souborů do sdílené síťové složky, celkem 10 GB.|  
  
Úlohy importu importuje těchto dat do následujících cílů v rámci účtu úložiště:  
  
|Zdroj|Cílový virtuální adresář nebo objekt blob|  
|------------|-------------------------------------------|  
|H:\Video|https://mystorageaccount.blob.core.windows.net/video|  
|H:\Photo|https://mystorageaccount.blob.core.windows.net/photo|  
|K:\Temp\FavoriteMovie.ISO|https://mystorageaccount.blob.core.windows.net/favorite/FavoriteMovies.ISO|  
|\\\bigshare\john\music|https://mystorageaccount.blob.core.windows.net/music|  
  
Pomocí této mapování, soubor `H:\Video\Drama\GreatMovie.mov` , je naimportován do objektu blob `https://mystorageaccount.blob.core.windows.net/video/Drama/GreatMovie.mov`.  
  
V dalším kroku výpočetní můžete určit, kolik pevné disky jsou potřeba, velikost dat:  
  
`5TB + 30GB + 25GB + 10GB = 5TB + 65GB`  
  
V tomto příkladu by měla stačit dva 3 TB pevné disky. Nicméně, protože zdrojový adresář `H:\Video` má 5 TB dat a kapacita jednu pevném disku je pouze 3 TB, je nutné přerušit `H:\Video` do menších dva adresáře: `H:\Video1` a `H:\Video2`, před spuštěním Microsoft Azure Nástroje pro import/Export. Tento krok provede následující adresáře zdrojových souborů:  
  
|Umístění|Velikost|Cílový virtuální adresář nebo objekt blob|  
|--------------|----------|-------------------------------------------|  
|H:\Video1|2,5 TB|https://mystorageaccount.blob.core.windows.net/video|  
|H:\Video2|2,5 TB|https://mystorageaccount.blob.core.windows.net/video|  
|H:\Photo|30 GB|https://mystorageaccount.blob.core.windows.net/photo|  
|K:\Temp\FavoriteMovies.ISO|25 GB|https://mystorageaccount.blob.core.windows.net/favorite/FavoriteMovies.ISO|  
|\\\bigshare\john\music|10 GB|https://mystorageaccount.blob.core.windows.net/music|  
  
 I v případě, `H:\Video`adresáře byla rozdělena na dva adresáře, ukazují na stejný cílový virtuální adresář v účtu úložiště. Tímto způsobem se zachovají všechny soubory videa v rámci jedné `video` kontejneru v účtu úložiště.  
  
 V dalším kroku předchozí adresáře zdrojových souborů jsou rovnoměrně rozloženy na dva pevné disky:  
  
||||  
|-|-|-|  
|Pevný disk|Adresáře zdrojových souborů|Celková velikost|  
|První disk|H:\Video1|2,5 TB + 30 GB|  
||H:\Photo||  
|Sekundy jednotky|H:\Video2|2,5 TB + 35 GB|  
||K:\Temp\BlueRay.ISO||  
||\\\bigshare\john\music||  
  
Kromě toho můžete nastavit následující metadata pro všechny soubory:  
  
-   **UploadMethod:** Služba Windows Azure Import/Export  
  
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
  
Můžete také nastavit některé vlastnosti `FavoriteMovie.ISO` objektů blob:  
  
-   **Content-Type:** application/octet-stream  
  
-   **Content-MD5:** Q2hlY2sgSW50ZWdyaXR5IQ==  
  
-   **Cache-Control:** no-cache  
  
Chcete-li nastavit tyto vlastnosti, vytvořte textový soubor, `c:\WAImportExport\SampleProperties.txt`:  
  
```xml
<?xml version="1.0" encoding="UTF-8"?>  
<Properties>  
    <Content-Type>application/octet-stream</Content-Type>  
    <Content-MD5>Q2hlY2sgSW50ZWdyaXR5IQ==</Content-MD5>  
    <Cache-Control>no-cache</Cache-Control>  
</Properties>  
```
  
Nyní jste připraveni ke spuštění nástroje Import/Export Azure Příprava dva pevné disky. Poznámky:  
  
-   První disk připojený jako jednotky X.  
  
-   Druhý disk připojený jako jednotka Y.  
  
-   Klíč pro účet úložiště `mystorageaccount` je `8ImTigJhIwvL9VEIQKB/zbqcXbxrIHbBjLIfOt0tyR98TxtFvUM/7T0KVNR6KRkJrh26u5I8hTxTLM2O1aDVqg==`.  

## <a name="preparing-disk-for-import-when-data-is-pre-loaded"></a>Příprava disku pro import dat je předem načtena.
 
 Pokud data, která mají být importována již existuje na disku, použijte příznak /skipwrite. Hodnotu /t a /srcdir má obě, přejděte na disku je připravený pro import. Pokud všechna data pro import není stejný cílový virtuální adresář nebo kořenového účtu úložiště, spusťte stejný příkaz pro každý cílový adresář samostatně, zachovat hodnotu /id stejná napříč všechna spuštění.

>[!NOTE] 
>Nezadávejte všechny jako vymažou data na disku. Můžete určit / šifrovat nebo /bk v závislosti na tom, zda disk už zašifrovali nebo ne. 
>

```
    When data is already present on the disk for each drive run the following command.
    WAImportExport.exe PrepImport /j:FirstDrive.jrn /id:Video1 /logdir:c:\logs /sk:8ImTigJhIwvL9VEIQKB/zbqcXbxrIHbBjLIfOt0tyR98TxtFvUM/7T0KVNR6KRkJrh26u5I8hTxTLM2O1aDVqg== /t:x /format /encrypt /srcdir:x:\Video1 /dstdir:video/ /MetadataFile:c:\WAImportExport\SampleMetadata.txt /skipwrite
```

## <a name="copy-sessions---first-drive"></a>Zkopírujte relace - nejprve jednotka

Pro první disk spusťte nástroj Azure Import/Export dvakrát pro kopírování adresářů dva zdroje:  

**Nejprve zkopírovat relace**
  
```
WAImportExport.exe PrepImport /j:FirstDrive.jrn /id:Video1 /logdir:c:\logs /sk:8ImTigJhIwvL9VEIQKB/zbqcXbxrIHbBjLIfOt0tyR98TxtFvUM/7T0KVNR6KRkJrh26u5I8hTxTLM2O1aDVqg== /t:x /format /encrypt /srcdir:H:\Video1 /dstdir:video/ /MetadataFile:c:\WAImportExport\SampleMetadata.txt  
```

**Druhé kopie relace**

```  
WAImportExport.exe PrepImport /j:FirstDrive.jrn /id:Photo /srcdir:H:\Photo /dstdir:photo/ /MetadataFile:c:\WAImportExport\SampleMetadata.txt
```

## <a name="copy-sessions---second-drive"></a>Zkopírujte relací – druhý disk
 
Druhou jednotku, spusťte nástroj Azure Import/Export trojnásobek, jednou pro adresáře zdrojových souborů a jednou pro samostatný Blu-Ray™ soubor image):  
  
**Nejprve zkopírovat relace** 

```
WAImportExport.exe PrepImport /j:SecondDrive.jrn /id:Video2 /logdir:c:\logs /sk:8ImTigJhIwvL9VEIQKB/zbqcXbxrIHbBjLIfOt0tyR98TxtFvUM/7T0KVNR6KRkJrh26u5I8hTxTLM2O1aDVqg== /t:y /format /encrypt /srcdir:H:\Video2 /dstdir:video/ /MetadataFile:c:\WAImportExport\SampleMetadata.txt  
```
  
**Druhé kopie relace**

```
WAImportExport.exe PrepImport /j:SecondDrive.jrn /id:Music /srcdir:\\bigshare\john\music /dstdir:music/ /MetadataFile:c:\WAImportExport\SampleMetadata.txt  
```  
  
**Třetí kopii relace**  

```
WAImportExport.exe PrepImport /j:SecondDrive.jrn /id:BlueRayIso /srcfile:K:\Temp\BlueRay.ISO /dstblob:favorite/BlueRay.ISO /MetadataFile:c:\WAImportExport\SampleMetadata.txt /PropertyFile:c:\WAImportExport\SampleProperties.txt  
```

## <a name="copy-session-completion"></a>Relace dokončení kopírování

Po dokončení kopírování relací, můžete odpojit dvě jednotky z počítače, kopírování a dodávat na příslušné datové centrum Windows Azure. Nahrát soubory deníku s dvěma `FirstDrive.jrn` a `SecondDrive.jrn`, když vytvoříte úlohu importu v [webu Azure portal](https://portal.azure.com).  
  
## <a name="next-steps"></a>Další postup

* [Příprava pevných disků pro úlohu importu](../storage-import-export-tool-preparing-hard-drives-import-v1.md)   
* [Stručná referenční příručka pro často používané příkazy](../storage-import-export-tool-quick-reference-v1.md) 
