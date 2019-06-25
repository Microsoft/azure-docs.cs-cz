---
title: Řešení potíží dat Azure Data Box Disk nahrát problémů pomocí protokolů | Dokumentace Microsoftu
description: Popisuje, jak pomocí protokolů a řešení problémů, kterým dochází při nahrávání dat do disku Azure Data Box.
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: article
ms.date: 06/14/2019
ms.author: alkohli
ms.openlocfilehash: 6f3ac38c3eac968bd2f7ec2aada435466d3ff279
ms.sourcegitcommit: 72f1d1210980d2f75e490f879521bc73d76a17e1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/14/2019
ms.locfileid: "67148306"
---
# <a name="understand-logs-to-troubleshoot-data-upload-issues-in-azure-data-box-disk"></a>Vysvětlení protokolů řešit problémy data v disku Azure Data Box

V tomto článku se vztahuje na Microsoft Azure Data Box Disk a popisuje problémy, které se zobrazí při odesílání dat do Azure.

## <a name="data-upload-logs"></a>Nahrát protokoly dat

Při nahrání dat do Azure v datacentru, `_error.xml` a `_verbose.xml` soubory jsou vygenerovány. Tyto protokoly se nahrají do stejného účtu úložiště, který byl použit jak nahrát data. Vzorek `_error.xml` je uveden níže.
    
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

## <a name="download-logs"></a>Stáhnout protokoly

Existují dva způsoby, jak vyhledat a stáhnout protokoly diagnostiky.

- Pokud existují nějaké chyby při odesílání dat do Azure, na portálu zobrazuje cestu ke složce, ve kterém jsou umístěné protokoly diagnostiky.

    ![Odkaz na protokoly na portálu](./media/data-box-disk-troubleshoot-upload/data-box-disk-portal-logs.png)

- Přejděte na účet úložiště přidružený k vaší objednávky zařízení Data Box. Přejděte na **Blob service > Procházet objekty blob** a vyhledejte blob odpovídající účtu úložiště. Přejděte na **waies**.

    ![Kopírování protokolů 2](./media/data-box-disk-troubleshoot/data-box-disk-copy-logs2.png)

V obou případech se zobrazí podrobné protokoly a protokoly chyb. Vyberte všechny protokoly a stáhněte si místní kopii.


## <a name="data-upload-errors"></a>Chyby nahrávání dat

V následující tabulce jsou shrnuté chyb generovaných při nahrávání dat do Azure.

| Kód chyby | Popis                        |
|-------------|------------------------------|
|`None` |  Byla úspěšně dokončena.           |
|`Renamed` | Úspěšně přejmenovalo objekt blob.  |                                                            |
|`CompletedWithErrors` | Nahrávání dokončeno s chybami. Podrobnosti o souborech v chybě jsou zahrnuty v souboru protokolu.  |
|`Corrupted`|CRC vypočítanou při ingestování neodpovídá kontrolní součet vypočítat při nahrávání.  |  
|`StorageRequestFailed` | Azure storage požadavek se nezdařil.   |     |
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
