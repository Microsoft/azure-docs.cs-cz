---
title: Azure Data Box Disk – řešení potíží | Microsoft Docs
description: Popisuje, jak řešit potíže s Azure Data Box Diskem.
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: article
ms.date: 06/14/2019
ms.author: alkohli
ms.openlocfilehash: f8116ec0836623adf803991017950ddc7f960923
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2019
ms.locfileid: "67805711"
---
# <a name="use-logs-to-troubleshoot-validation-issues-in-azure-data-box-disk"></a>Řešení potíží s problémy s ověřováním v Azure Data Box Disk pomocí protokolů

Tento článek se týká Microsoft Azure Data Box Disk. Tento článek popisuje, jak protokoly můžete použít k řešení potíží s problémy s ověřením, které je možné, uvidíte při nasazení tohoto řešení.

## <a name="validation-tool-log-files"></a>Soubory protokolu nástroje ověření

Při ověřování dat na disky pomocí [nástroje pro ověření](data-box-disk-deploy-copy-data.md#validate-data), *error.xml* generováno do protokolu všechny chyby. Soubor protokolu je umístěný v `Drive:\DataBoxDiskImport\logs` složky jednotky. Při spuštění ověřování je k dispozici odkaz v protokolu chyb.

<!--![Validation tool with link to error log](media/data-box-disk-troubleshoot/validation-tool-link-error-log.png)-->

Při spuštění více relací pro ověření, je generování jednoho protokolu chyb na relaci.

- Tady je ukázka v protokolu chyb při načtení dat do `PageBlob` složka není 512 – bajtů zarovnána. Žádná data nahrát do PageBlob musí být 512 – bajtů zarovnána, například VHD nebo VHDX. Chyby v tomto souboru jsou v `<Errors>` a upozornění v `<Warnings>`.

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
        <ErrorLog Version="2018-10-01">
            <SessionId>session#1</SessionId>
            <ItemType>PageBlob</ItemType>
            <SourceDirectory>D:\Dataset\TestDirectory</SourceDirectory>
            <Errors>
                <Error Code="Not512Aligned">
                    <Description>The file is not 512 bytes aligned.</Description>
                    <List>
                        <File Path="\Practice\myScript.ps1" />
                    </List>
                    <Count>1</Count>
                </Error>
            </Errors>
            <Warnings />
        </ErrorLog>
    ```

- Tady je ukázka v protokolu chyb, kdy název kontejneru není platný. Složky, která vytvoříte v rámci `BlockBlob`, `PageBlob`, nebo `AzureFile` složky na disku bude kontejner ve vašem účtu Azure Storage. Název kontejneru musí následovat [zásady vytváření názvů Azure](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions).

    ```xml
        <?xml version="1.0" encoding="utf-8"?>
        <ErrorLog Version="2018-10-01">
          <SessionId>bbsession</SessionId>
          <ItemType>BlockBlob</ItemType>
          <SourceDirectory>E:\BlockBlob</SourceDirectory>
          <Errors>
            <Error Code="InvalidShareContainerFormat">
              <List>
                <Container Name="Azu-reFile" />
                <Container Name="bbcont ainer1" />
              </List>
              <Count>2</Count>
            </Error>
          </Errors>
          <Warnings />
    </ErrorLog>
    ```

## <a name="validation-tool-errors"></a>Nástroj – chyby ověřování

Chyby obsažené v *error.xml* s odpovídajícím doporučené akce, které jsou shrnuty v následující tabulce.

| Kód chyby| Popis                       | Doporučené akce               |
|------------|--------------------------|-----------------------------------|
| `None` | Data se úspěšně ověřila. | Nevyžaduje se žádná akce. |
| `InvalidXmlCharsInPath` |Nelze vytvořit soubor manifestu, protože cesta k souboru obsahuje znaky, které nejsou platné. | Odeberte tyto znaky, aby bylo možné pokračovat.  |
| `OpenFileForReadFailed`| Nepodařilo se zpracovat soubor. To může být způsobeno systému poškození přístup, problém nebo soubor.|Nelze přečíst soubor z důvodu chyby. Podrobnosti o chybě jsou ve výjimce. |
| `Not512Aligned` | Tento soubor není v platném formátu PageBlob složky.| Pouze nahrávaných dat, která je 512 bajtů zarovnána `PageBlob` složky. Odebrat soubor ze složky PageBlob nebo přesuňte ho do složky, BlockBlob. Opakujte ověření.|
| `InvalidBlobPath` | Cesta k souboru není namapován na platný objekt blob cestu v cloudu podle konvence pojmenování objektu Blob Azure.|Postupujte podle pokynů na Azure pojmenování přejmenovat cesta k souboru. |
| `EnumerationError` | Nepodařilo se vytvořit výčet souborů pro ověření. |Může existovat několik důvodů, proč k této chybě. Nejpravděpodobnějším důvodem je přístup k souboru. |
| `ShareSizeExceeded` | Tento soubor způsobila velikost sdílené složky Azure file překročí Azure limit 5 TB.|Omezit velikost dat ve sdílené složce tak, že vyhovuje [omezení velikosti objektu Azure](data-box-disk-limits.md#azure-object-size-limits). Opakujte ověření. |
| `AzureFileSizeExceeded` | Velikost souboru přesahuje omezení velikosti souborů Azure.| Zmenšit velikost souboru nebo data tak, že vyhovuje [omezení velikosti objektu Azure](data-box-disk-limits.md#azure-object-size-limits). Opakujte ověření.|
| `BlockBlobSizeExceeded` | Velikost souboru přesahuje omezení velikosti objektů Blob bloku Azure. | Zmenšit velikost souboru nebo data tak, že vyhovuje [omezení velikosti objektu Azure](data-box-disk-limits.md#azure-object-size-limits). Opakujte ověření. |
| `ManagedDiskSizeExceeded` | Velikost souboru přesahuje omezení velikosti Azure Managed Disks. | Zmenšit velikost souboru nebo data tak, že vyhovuje [omezení velikosti objektu Azure](data-box-disk-limits.md#azure-object-size-limits). Opakujte ověření. |
| `PageBlobSizeExceeded` | Velikost souboru přesahuje omezení velikosti Azure Managed Disks. | Zmenšit velikost souboru nebo data tak, že vyhovuje [omezení velikosti objektu Azure](data-box-disk-limits.md#azure-object-size-limits). Opakujte ověření. |
| `InvalidShareContainerFormat`  |Názvy adresářů není v souladu s Azure zásady vytváření názvů pro kontejnery nebo sdílené složky.         |První složky vytvořené v rámci existující složky na disku bude kontejner ve vašem účtu úložiště. Tento název sdílené složky nebo kontejneru není v souladu s Azure zásady vytváření názvů. Přejmenujte soubor tak, že vyhovuje [zásady vytváření názvů Azure](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions). Opakujte ověření.   |
| `InvalidBlobNameFormat` | Cesta k souboru není namapován na platný objekt blob cestu v cloudu podle konvence pojmenování objektu Blob Azure.|Přejmenujte soubor tak, že vyhovuje [zásady vytváření názvů Azure](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions). Opakujte ověření. |
| `InvalidFileNameFormat` | Cesta k souboru není namapován na platnou cestu k souboru v cloudu podle konvence pojmenování souborů Azure. |Přejmenujte soubor tak, že vyhovuje [zásady vytváření názvů Azure](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions). Opakujte ověření. |
| `InvalidDiskNameFormat` | Cesta k souboru nelze mapovat na název platný disk v cloudu podle zásady vytváření názvů Azure Managed Disks. |Přejmenujte soubor tak, že vyhovuje [zásady vytváření názvů Azure](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions). Opakujte ověření.       |
| `NotPartOfFileShare` | Cesta pro uložení souborů není platný. Nahrajte soubory do složky ve službě soubory Azure.   | Odebrat soubory chybu a odeslání těchto souborů do vytvořených složky. Opakujte ověření. |
| `NonVhdFileNotSupportedForManagedDisk` | Virtuálního pevného disku soubor nelze odeslat jako spravovaný disk. |Odebrání souborů – virtuální pevný disk z `ManagedDisk` složky jako ty nejsou podporovány nebo přesunutí těchto souborů do `PageBlob` složky. Opakujte ověření. |


## <a name="next-steps"></a>Další kroky

- Řešení potíží s [chyby při odesílání dat](data-box-disk-troubleshoot-upload.md).
