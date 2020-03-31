---
title: Poradce při potížích s odesíláním dat pomocí protokolů
titleSuffix: Azure Data Box Disk
description: Popisuje, jak používat protokoly a řešení problémů při nahrávání dat na disk datové schránky Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: article
ms.date: 06/17/2019
ms.author: alkohli
ms.openlocfilehash: 7c14988706ef193ef5da868c55f6c4f55e7d98f9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79260134"
---
# <a name="understand-logs-to-troubleshoot-data-upload-issues-in-azure-data-box-disk"></a>Principy protokolů pro řešení problémů s nahráváním dat na disku Azure Data Box

Tento článek se vztahuje na disk datové schránky Microsoft Azure a popisuje problémy, které se zobrazí při nahrávání dat do Azure.

## <a name="about-upload-logs"></a>Protokoly pro nahrávání

Když se data nahrají do Azure `_error.xml` v `_verbose.xml` datovém centru a soubory se generují pro každý účet úložiště. Tyto protokoly se nahrají do stejného účtu úložiště, který byl použit k nahrání dat. 

Oba protokoly jsou ve stejném formátu a obsahují xml popisy událostí, ke kterým došlo při kopírování dat z disku do účtu Azure Storage.

Podrobný protokol obsahuje úplné informace o stavu operace kopírování pro každý objekt blob nebo soubor, zatímco protokol chyb obsahuje pouze informace pro objekty BLOB nebo soubory, které došlo k chybám během nahrávání.

Protokol chyb má stejnou strukturu jako podrobný protokol, ale filtruje úspěšné operace.

## <a name="download-logs"></a>Stažení protokolů

Chcete-li vyhledat protokoly pro nahrávání, postupujte takto.

1. Pokud se při nahrávání dat do Azure zobrazí nějaké chyby, portál zobrazí cestu ke složce, kde jsou umístěny protokoly diagnostiky.

    ![Odkaz na protokoly na portálu](./media/data-box-disk-troubleshoot-upload/data-box-disk-portal-logs.png)

2. Přejít na **waies**.

    ![chybové a podrobné protokoly](./media/data-box-disk-troubleshoot-upload/data-box-disk-portal-logs-1.png)

V každém případě se zobrazí protokoly chyb a podrobné protokoly. Vyberte každý protokol a stáhněte si místní kopii.

## <a name="sample-upload-logs"></a>Ukázkové protokoly nahrávání

Ukázka `_verbose.xml` je uvedena níže. V tomto případě byla objednávka úspěšně dokončena bez chyb.

```xml

<?xml version="1.0" encoding="utf-8"?>
<DriveLog Version="2018-10-01">
  <DriveId>184020D95632</DriveId>
  <Blob Status="Completed">
    <BlobPath>$root/botetapageblob.vhd</BlobPath>
    <FilePath>\PageBlob\botetapageblob.vhd</FilePath>
    <Length>1073742336</Length>
    <ImportDisposition Status="Created">rename</ImportDisposition>
    <PageRangeList>
      <PageRange Offset="0" Length="4194304" Status="Completed" />
      <PageRange Offset="4194304" Length="4194304" Status="Completed" />
      <PageRange Offset="8388608" Length="4194304" Status="Completed" />
      --------CUT-------------------------------------------------------
      <PageRange Offset="1061158912" Length="4194304" Status="Completed" />
      <PageRange Offset="1065353216" Length="4194304" Status="Completed" />
      <PageRange Offset="1069547520" Length="4194304" Status="Completed" />
      <PageRange Offset="1073741824" Length="512" Status="Completed" />
    </PageRangeList>
  </Blob>
  <Blob Status="Completed">
    <BlobPath>$root/botetablockblob.txt</BlobPath>
    <FilePath>\BlockBlob\botetablockblob.txt</FilePath>
    <Length>19</Length>
    <ImportDisposition Status="Created">rename</ImportDisposition>
    <BlockList>
      <Block Offset="0" Length="19" Status="Completed" />
    </BlockList>
  </Blob>
  <File Status="Completed">
    <FileStoragePath>botetaazurefilesfolder/botetaazurefiles.txt</FileStoragePath>
    <FilePath>\AzureFile\botetaazurefilesfolder\botetaazurefiles.txt</FilePath>
    <Length>20</Length>
    <ImportDisposition Status="Created">rename</ImportDisposition>
    <FileRangeList>
      <FileRange Offset="0" Length="20" Status="Completed" />
    </FileRangeList>
  </File>
  <Status>Completed</Status>
</DriveLog>
```

Pro stejné pořadí `_error.xml` je uveden vzorek níže.

```xml

<?xml version="1.0" encoding="utf-8"?>
<DriveLog Version="2018-10-01">
  <DriveId>184020D95632</DriveId>
  <Summary>
    <ValidationErrors>
      <None Count="3" />
    </ValidationErrors>
    <CopyErrors>
      <None Count="3" Description="No errors encountered" />
    </CopyErrors>
  </Summary>
  <Status>Completed</Status>
</DriveLog>
```

Ukázka je `_error.xml` uvedena níže, kde objednávka dokončena s chybami. 

Soubor chyb y v `Summary` tomto případě obsahuje oddíl a další oddíl, který obsahuje všechny chyby na úrovni souboru. 

Obsahuje `Summary` `ValidationErrors` a `CopyErrors`. V tomto případě bylo do Azure odesláno 8 souborů nebo složek a nebyly zjištěny žádné chyby ověření. Když byla data zkopírována do účtu Azure Storage, 5 souborů nebo složek se úspěšně nahrálo. Zbývající 3 soubory nebo složky byly přejmenovány podle konvencí pojmenování kontejnerů Azure a pak úspěšně odeslány do Azure.

Stav na úrovni `BlobStatus` souboru je v tom, že popisuje všechny akce provedené k nahrání objektů BLOB. V tomto případě jsou přejmenovány tři kontejnery, protože složky, do kterých byla data zkopírována, neodpovídaly konvencím pojmenování Azure pro kontejnery. Pro objekty BLOB nahrané v těchto kontejnerech, nový název kontejneru, cesta k objektu blob v Azure, původní neplatná cesta k souboru a velikost objektu blob jsou zahrnuty.
    
```xml
 <?xml version="1.0" encoding="utf-8"?>
    <DriveLog Version="2018-10-01">
      <DriveId>18041C582D7E</DriveId>
      <Summary>
     <!--Summary for validation and data copy to Azure -->
        <ValidationErrors>
          <None Count="8" />
        </ValidationErrors>
        <CopyErrors>
          <Completed Count="5" Description="No errors encountered" />
          <ContainerRenamed Count="3" Description="Renamed the container as the original container name does not follow Azure conventions." />
        </CopyErrors>
      </Summary>
    <!--List of renamed containers with the new names, new file path in Azure, original invalid file path, and size -->
      <Blob Status="ContainerRenamed">
        <BlobPath>databox-c2073fd1cc379d83e03d6b7acce23a6cf29d1eef/private.vhd</BlobPath>
        <OriginalFilePath>\PageBlob\pageblob test\private.vhd</OriginalFilePath>
        <SizeInBytes>10490880</SizeInBytes>
      </Blob>
      <Blob Status="ContainerRenamed">
        <BlobPath>databox-c2073fd1cc379d83e03d6b7acce23a6cf29d1eef/resource.vhd</BlobPath>
        <OriginalFilePath>\PageBlob\pageblob test\resource.vhd</OriginalFilePath>
        <SizeInBytes>71528448</SizeInBytes>
      </Blob>
      <Blob Status="ContainerRenamed">
        <BlobPath>databox-c2073fd1cc379d83e03d6b7acce23a6cf29d1eef/role.vhd</BlobPath>
        <OriginalFilePath>\PageBlob\pageblob test\role.vhd</OriginalFilePath>
        <SizeInBytes>10490880</SizeInBytes>
      </Blob>
      <Status>CompletedWithErrors</Status>
    </DriveLog>
```

## <a name="data-upload-errors"></a>Chyby při odesílání dat

Chyby generované při nahrávání dat do Azure jsou shrnuty v následující tabulce.

| Kód chyby | Popis                   |
|-------------|------------------------------|
|`None` |  Bylo úspěšně dokončeno.           |
|`Renamed` | Objekt blob byl úspěšně přejmenován.   |
|`CompletedWithErrors` | Nahrávání bylo dokončeno s chybami. Podrobnosti o souborech v chybě jsou zahrnuty v souboru protokolu.  |
|`Corrupted`|CRC vypočítané během ingestování dat neodpovídá CRC vypočítané během nahrávání.  |  
|`StorageRequestFailed` | Požadavek na úložiště Azure se nezdařil.   |     
|`LeasePresent` | Tato položka je pronajata a je používána jiným uživatelem. |
|`StorageRequestForbidden` |Nelze odeslat z důvodu problémů s ověřováním. |
|`ManagedDiskCreationTerminalFailure` | Nelze odeslat jako spravované disky. Soubory jsou k dispozici v účtu pracovní úložiště jako objekty BLOB stránky. Objekty BLOB stránky můžete ručně převést na spravované disky.  |
|`DiskConversionNotStartedTierInfoMissing` | Vzhledem k tomu, že soubor VHD byl zkopírován mimo předem vytvořené složky vrstvy, nebyl vytvořen spravovaný disk. Soubor se nahraje jako objekt blob stránky do účtu pracovního úložiště, jak je určeno při vytváření objednávky. Můžete jej převést ručně na spravovaný disk.|
|`InvalidWorkitem` | Nelze odeslat data, protože neodpovídá pojmenování Azure a omezuje konvence.|
|`InvalidPageBlobUploadAsBlockBlob` | Nahráno jako objekty BLOB bloku `databoxdisk-invalid-pb-`v kontejneru s předponou .|
|`InvalidAzureFileUploadAsBlockBlob` | Nahráno jako objekty BLOB bloku `databoxdisk-invalid-af`v kontejneru s předponou -.|
|`InvalidManagedDiskUploadAsBlockBlob` | Nahráno jako objekty BLOB bloku `databoxdisk-invalid-md`v kontejneru s předponou -.|
|`InvalidManagedDiskUploadAsPageBlob` |Nahráno jako objekty BLOB stránky `databoxdisk-invalid-md-`v kontejneru s předponou . |
|`MovedToOverflowShare` |Nahrané soubory do nové sdílené složky, protože původní velikost sdílené složky překročila maximální limit velikosti Azure. Nový název sdílené složky má původní název `-2`suffixed s .   |
|`MovedToDefaultAzureShare` |Nahrané soubory, které nebyly součástí žádné složky, do výchozí sdílené složky. Název sdílené položky začíná písmenem `databox-`. |
|`ContainerRenamed` |Kontejner pro tyto soubory neodpovídá konvencím pojmenování Azure a je přejmenován. Nový název začíná `databox-` a je přiložit s SHA1 hash původnínázev |
|`ShareRenamed` |Sdílená složka pro tyto soubory neodpovídala konvencím pojmenování Azure a je přejmenována. Nový název začíná `databox-` a je přiložit s SHA1 hash původní název. |
|`BlobRenamed` |Tyto soubory neodpovídaly konvencím pojmenování Azure a byly přejmenovány. Zkontrolujte, zda `BlobPath` pole neumítá nový název. |
|`FileRenamed` |Tyto soubory neodpovídaly konvencím pojmenování Azure a byly přejmenovány. Zkontrolujte, zda `FileStoragePath` pole neumítá nový název. |
|`DiskRenamed` |Tyto soubory neodpovídaly konvencím pojmenování Azure a byly přejmenovány. Zkontrolujte, zda `BlobPath` pole neumítá nový název. |


## <a name="next-steps"></a>Další kroky

- [Otevřete lístek podpory pro problémy s diskem datové schránky](data-box-disk-contact-microsoft-support.md).
