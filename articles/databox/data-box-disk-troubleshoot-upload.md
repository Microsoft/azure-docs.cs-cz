---
title: Řešení potíží dat Azure Data Box Disk nahrát problémů pomocí protokolů | Dokumentace Microsoftu
description: Popisuje, jak pomocí protokolů a řešení problémů, kterým dochází při nahrávání dat do disku Azure Data Box.
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: article
ms.date: 06/17/2019
ms.author: alkohli
ms.openlocfilehash: deaa9a220ee4d765650779b40742225e300ffdb7
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2019
ms.locfileid: "67807488"
---
# <a name="understand-logs-to-troubleshoot-data-upload-issues-in-azure-data-box-disk"></a>Vysvětlení protokolů řešit problémy data v disku Azure Data Box

V tomto článku se vztahuje na Microsoft Azure Data Box Disk a popisuje problémy, které se zobrazí při odesílání dat do Azure.

## <a name="about-upload-logs"></a>O nahrát protokoly

Při nahrání dat do Azure v datacentru, `_error.xml` a `_verbose.xml` soubory jsou vygenerovány pro každý účet úložiště. Tyto protokoly se nahrají do stejného účtu úložiště, který byl použit jak nahrát data. 

Oba protokoly jsou ve stejném formátu a obsahují popisy XML událostí, ke kterým došlo při kopírování dat z disku na účet služby Azure Storage.

Podrobný protokol obsahuje úplné informace o stavu operace kopírování pro každý objekt blob nebo soubor, že v protokolu chyb obsahuje pouze informace pro objekty BLOB nebo soubory, které se vyskytly chyby při nahrávání.

V protokolu chyb má stejnou strukturu jako podrobný protokol, ale filtry úspěšné operace.

## <a name="download-logs"></a>Stáhnout protokoly

Proveďte následující kroky k vyhledání nahrát protokoly.

1. Pokud existují nějaké chyby při odesílání dat do Azure, na portálu zobrazuje cestu ke složce, ve kterém jsou umístěné protokoly diagnostiky.

    ![Odkaz na protokoly na portálu](./media/data-box-disk-troubleshoot-upload/data-box-disk-portal-logs.png)

2. Přejděte na **waies**.

    ![Chyba a podrobné protokoly](./media/data-box-disk-troubleshoot-upload/data-box-disk-portal-logs-1.png)

V obou případech se zobrazí podrobné protokoly a protokoly chyb. Vyberte všechny protokoly a stáhněte si místní kopii.

## <a name="sample-upload-logs"></a>Ukázky nahrávání protokolů

Vzorek `_verbose.xml` je uveden níže. Pořadí v tomto případě byla úspěšně dokončena bez chyb.

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

Pro stejnou objednávku vzorek `_error.xml` je uveden níže.

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

Vzorek `_error.xml` je uveden níže where příkazu bylo dokončeno s chybami. 

Chybový soubor v tomto případě má `Summary` a další části, která obsahuje všechny soubor úrovně chyby. 

`Summary` Obsahuje `ValidationErrors` a `CopyErrors`. V tomto případě 8 soubory nebo složky nahraný do Azure a nebyly zjištěny žádné chyby ověření. Když data byla zkopírována do účtu služby Azure Storage, 5 soubory nebo složky se úspěšně nahrál. Zbývající 3 soubory nebo složky byla přejmenování podle konvence pojmenování kontejnerů Azure a úspěšně se nahrál do Azure.

Úroveň stavu souboru jsou v `BlobStatus` , který popisuje všechny akce prováděné na nahrání objektů BLOB. V takovém případě tři kontejnery jsou přejmenovat, protože složky, do které se kopírují data neodpovídala s Azure zásady vytváření názvů pro kontejnery. Pro objekty BLOB nahrané v těchto kontejnerech jsou zahrnuty nový název kontejneru, cesta objektu blob v Azure, původní neplatná cesta k souboru a velikost objektu blob.
    
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

V následující tabulce jsou shrnuté chyb generovaných při nahrávání dat do Azure.

| Kód chyby | Popis                   |
|-------------|------------------------------|
|`None` |  Byla úspěšně dokončena.           |
|`Renamed` | Úspěšně přejmenovalo objekt blob.   |
|`CompletedWithErrors` | Nahrávání dokončeno s chybami. Podrobnosti o souborech v chybě jsou zahrnuty v souboru protokolu.  |
|`Corrupted`|CRC vypočítanou při ingestování neodpovídá kontrolní součet vypočítat při nahrávání.  |  
|`StorageRequestFailed` | Azure storage požadavek se nezdařil.   |     
|`LeasePresent` | Tato položka je pronajatý a používá jiný uživatel. |
|`StorageRequestForbidden` |Nešlo nahrát kvůli problémům s ověřování. |
|`ManagedDiskCreationTerminalFailure` | Nelze uložit jako spravované disky. Soubory jsou k dispozici v přípravného účtu úložiště jako objekty BLOB stránky. Objekty BLOB stránky můžete ručně převést na spravované disky.  |
|`DiskConversionNotStartedTierInfoMissing` | Protože mimo vytvořených úroveň složek se zkopíroval soubor virtuálního pevného disku, nebyl vytvořen spravovaného disku. Jako objekty blob stránky do přípravného účtu úložiště, jak je uvedeno nahrání souboru při vytváření pořadí. Můžete ji převést ručně na spravovaný disk.|
|`InvalidWorkitem` | Data nelze uložit, protože není v souladu s Azure pojmenování a omezení vytváření názvů.|
|`InvalidPageBlobUploadAsBlockBlob` | Uložit jako objekty BLOB bloku v kontejneru s předponou `databoxdisk-invalid-pb-`.|
|`InvalidAzureFileUploadAsBlockBlob` | Uložit jako objekty BLOB bloku v kontejneru s předponou `databoxdisk-invalid-af`-.|
|`InvalidManagedDiskUploadAsBlockBlob` | Uložit jako objekty BLOB bloku v kontejneru s předponou `databoxdisk-invalid-md`-.|
|`InvalidManagedDiskUploadAsPageBlob` |Uložit jako objekty BLOB stránky v kontejneru s předponou `databoxdisk-invalid-md-`. |
|`MovedToOverflowShare` |Nahrané soubory do nové sdílené složky jako původní velikost sdílené složky překročil limit maximální velikosti Azure. Nový název sdílené složky souboru má příponu původní název `-2`.   |
|`MovedToDefaultAzureShare` |Nahrané soubory, které nejsou součástí žádné složku pro výchozí sdílenou složku. Název sdílené složky začíná `databox-`. |
|`ContainerRenamed` |Kontejner pro tyto soubory nebyly v souladu s Azure konvence pojmenování a přejmenování. Nový název začíná řetězcem `databox-` a je doplněny algoritmus hash SHA1 původní název |
|`ShareRenamed` |Sdílené složky pro tyto soubory nebyly v souladu s Azure konvence pojmenování a přejmenování. Nový název začíná řetězcem `databox-` a je přidán s původní název algoritmus hash SHA1. |
|`BlobRenamed` |Tyto soubory nebyly v souladu s Azure konvence pojmenování a byly přejmenovány. Zkontrolujte, `BlobPath` pole pro nový název. |
|`FileRenamed` |Tyto soubory nebyly v souladu s Azure konvence pojmenování a byly přejmenovány. Zkontrolujte, `FileStoragePath` pole pro nový název. |
|`DiskRenamed` |Tyto soubory nebyly v souladu s Azure konvence pojmenování a byly přejmenovány. Zkontrolujte, `BlobPath` pole pro nový název. |


## <a name="next-steps"></a>Další postup

- [Vytvořit lístek podpory pro problémy disku Data Box](data-box-disk-contact-microsoft-support.md).
