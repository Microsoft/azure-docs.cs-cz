---
title: Řešení potíží s nahráváním dat pomocí protokolů
titleSuffix: Azure Data Box Disk
description: Popisuje, jak používat protokoly a řešit problémy, které se zobrazují při nahrávání dat do Azure Data Box Disk.
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: troubleshooting
ms.date: 06/17/2019
ms.author: alkohli
ms.openlocfilehash: 7225b04908753bb7c07ac89510859bac9db5b89c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "85565013"
---
# <a name="understand-logs-to-troubleshoot-data-upload-issues-in-azure-data-box-disk"></a>Principy protokolů pro řešení potíží s nahráváním dat v Azure Data Box Disk

Tento článek se týká Microsoft Azure Data Box Disk a popisuje problémy, které vidíte při nahrávání dat do Azure.

## <a name="about-upload-logs"></a>O nahrání protokolů

Když se data nahrají do Azure v datacentru, `_error.xml` `_verbose.xml` vygenerují se soubory pro každý účet úložiště. Tyto protokoly se nahrají do stejného účtu úložiště, který jste použili k nahrání dat. 

Oba protokoly jsou ve stejném formátu a obsahují popisy XML událostí, ke kterým došlo při kopírování dat z disku do účtu Azure Storage.

Podrobný protokol obsahuje kompletní informace o stavu operace kopírování pro každý objekt BLOB nebo soubor, zatímco protokol chyb obsahuje jenom informace o objektech blob nebo souborech, které během nahrávání zjistily chyby.

Protokol chyb má stejnou strukturu jako podrobný protokol, ale filtruje úspěšné operace.

## <a name="download-logs"></a>Stažení protokolů

Pro vyhledání protokolů odeslání proveďte následující kroky.

1. Pokud dojde k chybám při odesílání dat do Azure, portál zobrazí cestu ke složce, ve které jsou umístěné diagnostické protokoly.

    ![Odkaz na protokoly na portálu](./media/data-box-disk-troubleshoot-upload/data-box-disk-portal-logs.png)

2. Přejít na **waies**.

    ![chybové a podrobné protokoly](./media/data-box-disk-troubleshoot-upload/data-box-disk-portal-logs-1.png)

V každém případě se zobrazí protokoly chyb a podrobné protokoly. Vyberte jednotlivé protokoly a stáhněte místní kopii.

## <a name="sample-upload-logs"></a>Ukázka nahrávání protokolů

Ukázka `_verbose.xml` je uvedena níže. V tomto případě se objednávka úspěšně dokončila bez chyb.

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

Pro stejné pořadí je ukázka uvedená `_error.xml` níže.

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

Ukázka `_error.xml` je zobrazena níže, kde se objednávka dokončila s chybami. 

Soubor s chybou v tomto případě obsahuje `Summary` oddíl a další oddíl, který obsahuje všechny chyby na úrovni souborů. 

`Summary`Obsahuje `ValidationErrors` a `CopyErrors` . V tomto případě se 8 souborů nebo složek nahrály do Azure a nedošlo k chybám ověření. Po zkopírování dat do Azure Storage účtu se úspěšně nahrály 5 souborů nebo složek. Zbývající 3 soubory nebo složky se přejmenovaly podle konvencí vytváření názvů kontejnerů Azure a pak se úspěšně nahrály do Azure.

Stav na úrovni souboru je v `BlobStatus` této části popisuje všechny akce prováděné při nahrávání objektů BLOB. V takovém případě se tři kontejnery přejmenují, protože složky, do kterých se data zkopírovala, nesplňovaly zásady vytváření názvů Azure pro kontejnery. Pro objekty blob nahrané v těchto kontejnerech se jedná o nový název kontejneru, cestu k objektu BLOB v Azure, původní neplatnou cestu k souboru a velikost objektu BLOB je obsažená.
    
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

## <a name="data-upload-errors"></a>Chyby nahrávání dat

Chyby generované při nahrávání dat do Azure jsou shrnuté v následující tabulce.

| Kód chyby | Description                   |
|-------------|------------------------------|
|`None` |  Úspěšně dokončeno.           |
|`Renamed` | Objekt BLOB se úspěšně přejmenoval.   |
|`CompletedWithErrors` | Nahrávání se dokončilo s chybami. Podrobnosti o souborech v chybě jsou obsaženy v souboru protokolu.  |
|`Corrupted`|CRC vypočtený během příjmu dat neodpovídá kontrolnímu součtu CRC vypočítanému během nahrávání.  |  
|`StorageRequestFailed` | Požadavek na úložiště Azure se nezdařil.   |     
|`LeasePresent` | Tato položka je zapůjčena a používá ji jiný uživatel. |
|`StorageRequestForbidden` |Nelze nahrávat z důvodu problémů s ověřováním. |
|`ManagedDiskCreationTerminalFailure` | Nebylo možné nahrávat jako spravované disky. Soubory jsou k dispozici v pracovním účtu úložiště jako objekty blob stránky. Objekty blob stránky můžete převést ručně na spravované disky.  |
|`DiskConversionNotStartedTierInfoMissing` | Vzhledem k tomu, že se soubor VHD zkopíroval mimo složky předvytvořené vrstvy, nevytvořil se spravovaný disk. Soubor se nahraje jako objekt blob stránky do přípravného účtu úložiště, jak je zadaný během vytváření objednávky. Můžete ho převést ručně na spravovaný disk.|
|`InvalidWorkitem` | Data nešlo nahrát, protože neodpovídají konvencím názvů a omezení pro Azure.|
|`InvalidPageBlobUploadAsBlockBlob` | Nahráno jako objekty blob bloku v kontejneru s předponou `databoxdisk-invalid-pb-` .|
|`InvalidAzureFileUploadAsBlockBlob` | Nahráno jako objekty blob bloku v kontejneru s předponou `databoxdisk-invalid-af` -.|
|`InvalidManagedDiskUploadAsBlockBlob` | Nahráno jako objekty blob bloku v kontejneru s předponou `databoxdisk-invalid-md` -.|
|`InvalidManagedDiskUploadAsPageBlob` |Nahráno jako objekty blob stránky v kontejneru s předponou `databoxdisk-invalid-md-` . |
|`MovedToOverflowShare` |Nahrálo se soubory do nové sdílené složky, protože původní velikost sdílené složky překročila maximální limit velikosti Azure. Název nového sdílení souborů má příponu s názvem `-2` .   |
|`MovedToDefaultAzureShare` |Nahrálo se soubory, které nebyly součástí žádné složky, do výchozí sdílené složky. Název sdílené složky začíná na `databox-` . |
|`ContainerRenamed` |Kontejner pro tyto soubory neodpovídal zásadám vytváření názvů Azure a je přejmenován. Nový název začíná `databox-` příponou a má příponu s hodnotou hash SHA1 původního názvu. |
|`ShareRenamed` |Sdílená složka pro tyto soubory nesplňovala zásady vytváření názvů Azure a je přejmenovaná. Nový název začíná `databox-` příponou a má příponu s hodnotou hash SHA1 původního názvu. |
|`BlobRenamed` |Tyto soubory nesplňovaly zásady vytváření názvů Azure a přejmenovaly se. V `BlobPath` poli pro nový název ověřte. |
|`FileRenamed` |Tyto soubory nesplňovaly zásady vytváření názvů Azure a přejmenovaly se. V `FileStoragePath` poli pro nový název ověřte. |
|`DiskRenamed` |Tyto soubory nesplňovaly zásady vytváření názvů Azure a přejmenovaly se. V `BlobPath` poli pro nový název ověřte. |


## <a name="next-steps"></a>Další kroky

- [Otevřete lístek podpory pro problémy s data box disk](data-box-disk-contact-microsoft-support.md).
